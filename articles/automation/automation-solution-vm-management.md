---
title: 在空闲时间启动/停止 VM 解决方案（预览）
description: VM 管理解决方案可按计划启动和停止 Azure 资源管理器虚拟机，并通过 Log Analytics 进行主动监视。
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: ec15859a92527c4e084075b40d3439d7a19fea1a
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2018
---
# <a name="startstop-vms-during-off-hours-solution-preview-in-azure-automation"></a>Azure 自动化中的在空闲时间启动/停止 VM 解决方案（预览）

通过在空闲时间启动/停止 VM 解决方案，可以按照用户定义的计划来启动和停止 Azure 虚拟机，并通过 Azure Log Analytics 提供见解，以及使用 [SendGrid](https://azuremarketplace.microsoft.com/marketplace/apps/SendGrid.SendGrid?tab=Overview) 发送可选电子邮件。 它在大多数情况下都支持 Azure 资源管理器和经典 VM。 

对于希望使用无服务器、低成本资源降低成本的用户，此解决方案可以提供分散式的自动化选项。 使用此解决方案可以：

* 计划要启动/停止的 VM。
* 使用 Azure 标记按升序计划要启动和停止的 VM（不支持经典 VM）。
* 根据 CPU 低使用率自动停止 VM。

## <a name="prerequisites"></a>先决条件

- Runbook 使用 [Azure 运行方式帐户](automation-offering-get-started.md#authentication-methods)。  运行方式帐户是首选的身份验证方法，因为它使用证书身份验证，而不是可能会过期或经常更改的密码。  

- 此解决方案只能管理与 Azure 自动化帐户位于同一订阅中的 VM。  

- 此解决方案只能部署到以下 Azure 区域：澳大利亚东南部、加拿大中部、印度中部、美国东部、日本东部、东南亚、英国南部和西欧。  
    
    > [!NOTE]
    > 管理 VM 计划的 runbook 可以面向任何区域中的 VM。  

- 在从 Azure Marketplace 加入时，要在启动和停止 VM Runbook 完成时发送电子邮件通知，请选择“是”以部署 SendGrid。 

    > [!IMPORTANT]
    > SendGrid 是第三方服务。 如需支持，请与 [SendGrid](https://sendgrid.com/contact/) 联系。  
    >
   
    SendGrid 的限制如下：

    * 每用户每订阅最多一个 SendGrid 帐户。
    * 每订阅最多两个 SendGrid 帐户。

如果你部署了此解决方案的以前版本，则在部署此版本之前，首先必须从帐户中将其删除。  

## <a name="solution-components"></a>解决方案组件

此解决方案包括预配置的 Runbook、计划以及与 Log Analytics 的集成，用于定制虚拟机的启动和关闭以满足业务需求。 

### <a name="runbooks"></a>Runbook

下表列出了部署到自动化帐户的 Runbook。  不应该对 Runbook 代码进行更改。 应该对新功能编写自己的 Runbook。

> [!IMPORTANT]
> 不要直接运行在名称末尾追加了“child”的任何 Runbook。
>

所有父 Runbook 都包含 *WhatIf* 参数。 设置为 **True** 时，*WhatIf* 支持详细说明在无 *WhatIf* 参数的情况下运行时 Runbook 的确切行为，并验证是否以正确 VM 为目标。  仅当 *WhatIf* 参数设置为 **False** 时，Runbook 才执行其定义的操作。 

|**Runbook** | **参数** | **说明**|
| --- | --- | ---| 
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | 仅从父 Runbook 调用。 为 AutoStop 方案按每个资源创建警报。| 
|AutoStop_CreateAlert_Parent | WhatIf：True 或 False <br> VMList | 在目标订阅或资源组中的 VM 上创建或更新 Azure 警报规则。 <br> VMList：以逗号分隔的 VM 列表。  例如，*vm1,vm2,vm3*。| 
|AutoStop_Disable | 无 | 禁用 AutoStop 警报和默认计划。| 
|AutoStop_StopVM_Child | WebHookData | 仅从父 Runbook 调用。 警报规则调用此 Runbook 以停止 VM。|  
|Bootstrap_Main | 无 | 使用一次来设置启动配置，例如通常无法从 Azure 资源管理器访问的 webhookURI。 部署成功后，将自动删除此 Runbook。|  
|ScheduledStartStop_Child | VMName <br> 操作：停止或启动 <br> ResourceGroupName | 仅从父 Runbook 调用。 为计划的停止执行停止或启动。|  
|ScheduledStartStop_Parent | 操作：停止或启动 <br> WhatIf：True 或 False | 这会影响订阅中的所有 VM。 将 **External_Start_ResourceGroupNames** 和 **External_Stop_ResourceGroupNames** 编辑为仅在这些目标资源组上执行。 此外，还可以通过更新 External_ExcludeVMNames 变量排除特定 VM。 *WhatIf* 的行为与在其他 Runbook 中的行为相同。|  
|SequencedStartStop_Parent | 操作：停止或启动 <br> WhatIf：True 或 False | 在要确定启动/停止活动序列的每个 VM 上，创建名为 **SequenceStart** 和 **SequenceStop** 的标记。 标记的值应为一个正整数（1、2、3），对应于启动或停止的顺序。 *WhatIf* 的行为与在其他 Runbook 中的行为相同。 <br> **注意**：VM 必须位于 Azure 自动化变量中定义的 External_Start_ResourceGroupNames、External_Stop_ResourceGroupNames 和 External_ExcludeVMNames 资源组中。 它们必须具有相应的标记才能使操作生效。|

### <a name="variables"></a>变量

下表列出了在自动化帐户中创建的变量。  只应修改以 **External** 为前缀的变量。 修改以 **Internal** 为前缀的变量将导致不利影响。  

|**变量** | **说明**|
---------|------------|
|External_AutoStop_Condition | 在触发警报之前配置条件时所需的条件运算符。 可接受的值包括：**GreaterThan**、**GreaterThanOrEqual**、**LessThan** 和 **LessThanOrEqual**。|  
|External_AutoStop_Description | CPU 百分比超过阈值时停止 VM 的警报。|  
|External_AutoStop_MetricName | 为其配置 Azure 警报规则的性能指标的名称。| 
|External_AutoStop_Threshold | 在变量 *External_AutoStop_MetricName* 中指定的 Azure 警报规则的阈值。 百分比值的范围是从 1 到 100。|  
|External_AutoStop_TimeAggregationOperator | 将应用到所选窗口大小以计算条件的时间聚合运算符。 可接受的值包括：**Average**、**Minimum**、**Maximum**、**Total** 和 **Last**。|  
|External_AutoStop_TimeWindow | Azure 将分析用于触发警报的选定指标的窗口大小。 此参数接受 timespan 格式的输入。 可能的值为 5 分钟到 6 小时。|  
|External_EmailFromAddress | 指定电子邮件的发件人。|  
|External_EmailSubject | 指定电子邮件的主题行。|  
|External_EmailToAddress | 指定电子邮件的收件人。 使用逗号分隔名称。|  
|External_ExcludeVMNames | 输入要排除的 VM 名称，使用逗号分隔名称，不带空格。|  
|External_IsSendEmail | 指定完成后发送电子邮件通知的选项。  指定“是”发送电子邮件，或指定“否”不发送电子邮件。  如果在初始部署期间未启用电子邮件通知，则此选项应为“否”。|  
|External_Start_ResourceGroupNames | 指定针对启动操作的一个或多个资源组，使用逗号分隔值。|  
|External_Stop_ResourceGroupNames | 指定针对停止操作的一个或多个资源组，使用逗号分隔值。|  
|Internal_AutomationAccountName | 指定自动化帐户的名称。|  
|Internal_AutoSnooze_WebhookUri | 指定为 AutoStop 方案调用的 Webhook URI。|  
|Internal_AzureSubscriptionId | 指定 Azure 订阅 ID。|  
|Internal_ResourceGroupName | 指定自动化帐户资源组名称。|  
|Internal_SendGridAccountName | 指定 SendGrid 帐户名称。|  
|Internal_SendGridPassword | 指定 SendGrid 帐户密码。|  

<br>

在所有方案中，除了为 **AutoStop_CreateAlert_Parent** Runbook 提供以逗号分隔的 VM 列表之外，**External_Start_ResourceGroupNames**、**External_Stop_ResourceGroupNames** 和 **External_ExcludeVMNames** 变量对于定位 VM 都是必需的。 也就是说，你的 VM 必须驻留在目标资源组中才能进行启动和停止操作。 此逻辑的工作方式有点类似于 Azure 策略，因为可以在订阅或资源组中设定目标，并且具有新创建的 VM 继承的操作。 此方法避免了必须为每个 VM 维护一个单独计划和管理规模启动和停止操作。

### <a name="schedules"></a>计划

下表列出了在自动化帐户中创建的每个默认计划。  你可以修改默认计划或创建自己的自定义计划。  默认情况下，除了 **Scheduled_StartVM** 和 **Scheduled-StopVM**，这些计划都是禁用的。

不应启用所有计划，因为这可能会创建重叠的计划操作。 最好确定希望执行哪些优化，然后再进行相应的修改。  请参阅概述部分的示例方案以查看进一步解释。   

|**计划名称** | **频率** | **说明**|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | 每隔 8 小时 | 每隔 8 小时运行一次 AutoStop_CreateAlert_Parent Runbook，它将基于 Azure 自动化变量中的 External_Start_ResourceGroupNames、External_Stop_ResourceGroupNames 和 External_ExcludeVMNames 的值依次停止 VM。  或者，可以使用 VMList 参数指定用逗号分隔的 VM 列表。|  
|Scheduled_StopVM | 用户定义，每天 | 每天在指定的时间运行带有 *Stop* 参数的 Scheduled_Parent Runbook。  自动停止所有满足通过资产变量定义的规则 VM。 应启用相关计划 **Scheduled-StartVM**。|  
|Scheduled_StartVM | 用户定义，每天 | 每天在给定的时间运行带有 *Start* 参数的 Scheduled_Parent Runbook。  自动启动所有满足相应变量定义的规则的 VM。  应启用相关计划 **Scheduled-StopVM**。|
|Sequenced-StopVM | 凌晨 1:00 (UTC)，每星期五 | 每星期五在指定的时间运行带有 *Stop* 参数的 Sequenced_Parent Runbook。  将按顺序（升序）停止具有相应变量定义的 **SequenceStop** 标记的所有 VM。  有关标记值和资产变量的更多详细信息，请参阅“Runbook”部分。  应启用相关计划 **Sequenced-StartVM**。|
|Sequenced-StartVM | 下午 1:00 (UTC)，每星期一 | 每星期一在给定的时间运行带有 Start 参数的 Sequenced_Parent runbook。 按顺序（降序）启动具有相应变量定义的 **SequenceStart** 标记的所有 VM。  有关标记值和资产变量的更多详细信息，请参阅“Runbook”部分。  应启用相关计划 **Sequenced-StopVM**。|

<br>

## <a name="configuration"></a>配置

执行以下步骤可将非工作时间启动/停止 VM 解决方案添加到自动化帐户，然后配置变量来自定义该解决方案。

1. 在 Azure 门户中，单击“创建资源”。<br> ![Azure 门户](media/automation-solution-vm-management/azure-portal-01.png)<br>  
2. 在“Marketplace”窗格中，键入类似于“启动”或“启动/停止” 的关键字。 开始键入时，会根据输入筛选该列表。 或者，可以键入解决方案完整名称中的一个或多个关键，然后按 Enter。  从搜索结果中选择“在空闲时间启动/停止 VM [预览版]”。  
3. 在所选解决方案的“在空闲时间启动/停止 VM [预览版]”窗格中查看摘要信息，并单击“创建”。  
4. 此时会显示“添加解决方案”窗格。 系统会提示先要配置解决方案，然后才可以将它导入自动化订阅。<br><br> ![VM 管理中的“添加解决方案”边栏选项卡](media/automation-solution-vm-management/azure-portal-add-solution-01.png)<br><br>
5.  在“添加解决方案”边栏选项卡中，选择“工作区”。 选择链接到自动化帐户所在的同一个 Azure 订阅的 OMS 工作区。 如果没有工作区，请选择“新建工作区”。 在“OMS 工作区”窗格中执行以下操作： 
   - 指定新 **OMS 工作区**的名称。
   - 如果选择的默认值不合适，请从下拉列表中选择要链接到的**订阅**。
   - 对于“资源组”，可以创建新资源组，或选择现有的资源组。  
   - 选择“位置” 。  目前可用的位置仅为：澳大利亚东南部、加拿大中部、印度中部、美国东部、日本东部、东南亚、英国南部和西欧。
   - 选择“定价层”。  该解决方案提供两种定价层：免费和 OMS 付费层。  免费层的每日可收集数据量、保留期和 Runbook 作业运行时分钟数有限制。  OMS 付费级别对每日可收集的数据量没有限制。  

        > [!NOTE]
        > 虽然“按 GB (独立)”付费层显示为一个选项，但它不适用。  如果选择它并继续在订阅中创建此解决方案，它会失败。  正式发布此解决方案时，此问题将得到解决。<br>此解决方案仅使用自动化作业分钟数和日志引入。  它不会将其他 OMS 节点添加到环境中。  

6. 在“OMS 工作区”窗格上提供所需信息后，单击“创建”。  可以在菜单中的“通知”下面跟踪操作进度，完成后将返回到“添加解决方案”窗格。  
7. 在“添加解决方案”窗格中，选择“自动化帐户”。  如果要创建新的 OMS 工作区，则还需要创建与它关联的新自动化帐户。  选择“创建自动化帐户”，并在“添加自动化帐户”窗格中提供以下信息： 
  - 在“名称”字段中输入自动化帐户的名称。

    系统会根据所选的 OMS 工作区自动填充所有其他选项。 无法修改这些选项。  “Azure 运行方式帐户”是此解决方案为 Runbook 包含的默认身份验证方法。  单击“确定”后，系统会验证配置选项并创建自动化帐户。  可以在菜单中的“通知”下面跟踪操作进度。 

    也可以选择现有的自动化运行方式帐户。  请注意，选择的帐户不能已链接到另一个 OMS 工作区。 如果该帐户已链接，则你会收到一条消息，并且需要选择不同的自动化运行方式帐户或创建新帐户。<br><br> ![自动化帐户已链接到 OMS 工作区](media/automation-solution-vm-management/vm-management-solution-add-solution-blade-autoacct-warning.png)<br>

8. 最后，在“添加解决方案”窗格中选择“配置”。 此时会显示“参数”窗格。<br><br> ![解决方案的参数窗格](media/automation-solution-vm-management/azure-portal-add-solution-02.png)<br><br>  在此处，系统会提示：  
   - 指定“目标资源组名称”。 这些名称是包含此解决方案要管理的 VM 的资源组名称。  可以输入多个名称，使用逗号分隔即可（这些值不区分大小写）。  如果想要针对订阅中的所有资源组内的 VM，可以使用通配符。
   - 指定“VM 排除列表(字符串)”。 这是来自目标资源组的一个或多个虚拟机的名称。  可以输入多个名称，使用逗号分隔即可（这些值不区分大小写）。  支持使用通配符。
   - 选择“计划”。 这是启动和停止目标资源组中 VM 的周期性日期和时间。  默认情况下，该计划配置为 UTC 时区。 无法选择其他区域。  若要在配置解决方案后将计划配置为特定时区，请参阅[修改启动和关闭计划](#modifying-the-startup-and-shutdown-schedule)。
   - 要从 SendGrid 接收“电子邮件通知”，请接受默认值“是”，并提供有效的电子邮件地址。 如果选择了 **No**，但后来想要接收电子邮件通知，则可以使用有效的电子邮件地址（以逗号分隔）更新 **External_EmailToAddress**，然后使用值 **Yes** 修改变量 **External_IsSendEmail**。  

9. 配置解决方案所需的初始设置后，选择“创建”。  系统会验证所有设置，然后在订阅中部署该解决方案。  此过程需要几秒钟才能完成，可以在菜单中的“通知”下面跟踪进度。 

## <a name="collection-frequency"></a>收集频率

自动化作业日志和作业流数据将每隔 5 分钟引入 Log Analytics 存储库一次。  

## <a name="using-the-solution"></a>使用解决方案

从 Azure 门户在 Log Analytics 工作区中添加 VM 管理解决方案时，将 **StartStopVMView** 磁贴添加到仪表板。  此磁贴显示已成功启动和完成的解决方案的 Runbook 作业计数和图形表示。<br><br> ![VM 管理 StartStopVM 视图磁贴](media/automation-solution-vm-management/vm-management-solution-startstopvm-view-tile.png)  

在自动化帐户中，可以通过选择“工作区”选项访问和管理解决方案。 在 Log Analytics 页上，从左窗格中选择“解决方案”。  在“解决方案”页上，从列表中选择解决方案“Start-Stop-VM[workspace]”。<br><br> ![Log Analytics 中的解决方案列表](media/automation-solution-vm-management/azure-portal-select-solution-01.png)  

选择该解决方案会显示“Start-Stop-VM[workspace]”解决方案窗格。 在此处可以查看重要详细信息，例如 **StartStopVM** 磁贴。 如同在 Log Analytics 工作区中一样，此磁贴显示解决方案中已成功启动和完成的 Runbook 作业计数与图形表示。<br><br> ![自动化更新管理解决方案页](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)  

在此处，可以通过单击圆环磁贴对作业记录执行进一步的分析。 解决方案仪表板显示作业历史记录和预定义的日志搜索查询。 切换到 Log Analytics 高级门户可以根据搜索查询进行搜索。  

所有父 Runbook 都包含 *WhatIf* 参数。 设置为 **True** 时，此参数支持详细说明在无 *WhatIf* 参数的情况下运行时 Runbook 的确切行为，并验证是否以正确 VM 为目标。 仅当 *WhatIf* 参数设置为 **False** 时，Runbook 才执行其定义的操作。  


### <a name="scenario-1-daily-startstop-vms-across-a-subscription-or-target-resource-groups"></a>方案 1：订阅或目标资源组上的每日启动/停止 VM 

这是首次部署解决方案时的默认配置。  例如，你可以将其配置为在下班时晚上停止订阅中的所有 VM，并在早上回到办公室时启动它们。 在部署期间配置计划 **Scheduled-StartVM** 和 **Scheduled-StopVM** 时，它们启动和停止目标 VM。
>[!NOTE]
>时区是你配置计划时间参数时的当前时区。 但是它以 UTC 格式存储在 Azure 自动化中。  你不必执行任何时区转换，因为这是在部署过程中处理的。

可以通过配置以下变量控制哪些 VM 位于范围中：**External_Start_ResourceGroupNames**、**External_Stop_ResourceGroupNames** 和 **External_ExcludeVMNames**。  

>[!NOTE]
> **Target ResourceGroup Names** 的值存储为 **External_Start_ResourceGroupNames** 和 **External_Stop_ResourceGroupNames** 变量的值。 如需更细化的配置粒度，可以修改这些变量以面向不同的资源组。  对于启动操作，请使用 **External_Start_ResourceGroupNames**，对于停止操作，请使用 **External_Stop_ResourceGroupNames**。 VM 会自动添加到启动和停止计划中。

若要测试并验证配置，请手动启动 **ScheduledStartStop_Parent** Runbook，并将 ACTION 参数设置为 **start** 或 **stop**，然后将 WHATIF 参数设置为 **True**。<br><br> ![配置 runbook 的参数](media/automation-solution-vm-management/solution-startrunbook-parameters-01.png)


 预览计划的操作，并根据需要进行任何更改，然后对生产 VM 执行操作。  可以手动执行参数设置为 **false** 的 Runbook，或让自动化计划 **Schedule-StartVM** 和 **Schedule-StopVM** 在规定的计划之后自动运行。

### <a name="scenario-2-sequence-the-startstop-vms-across-a-subscription-by-using-tags"></a>方案 2：使用标记对订阅中的启动/停止 VM 执行序列化
在支持分布式工作负荷的多个 VM 上包含两个或更多组件的环境中，支持按顺序启动和停止组件的顺序非常重要。  为此，可以执行以下步骤：

1. 将具有正整数值的 **SequenceStart** 和 **SequenceStop** 标记添加到 **External_Start_ResourceGroupNames** 和 **External_Stop_ResourceGroupNames** 变量中的目标 VM。  按升序执行启动和停止操作。  若要了解如何标记 VM，请参阅[在 Azure 中标记 Windows 虚拟机](../virtual-machines/windows/tag.md)和[在 Azure 中标记 Linux 虚拟机](../virtual-machines/linux/tag.md)。
2. 修改计划 **Sequenced-StartVM** 和 **Sequenced-StopVM** 的日期和时间，以满足要求并启用计划。  

若要测试和验证配置，请手动启动 **SequencedStartStop_Parent** Runbook。 将 ACTION 参数设置为 **start** 或 **stop**，将 WHATIF 参数设置为 **True**。<br><br> ![配置 runbook 的参数](media/automation-solution-vm-management/solution-startrunbook-parameters-01.png)<br> 预览操作，并根据需要进行任何更改，然后对生产 VM 执行操作。  准备就绪后，可以手动执行参数设置为 **False** 的 Runbook，或让自动化计划 **Sequenced-StartVM** 和 **Sequenced-StopVM** 在规定的计划之后自动运行。  

### <a name="scenario-3-automate-startstop-vms-across-a-subscription-based-on-cpu-utilization"></a>方案 3：基于 CPU 使用率自动启动/停止订阅中的 VM
为了帮助管理订阅中运行虚拟机的成本，此解决方案可以通过评估在非高峰时段（如下班之后）未使用的 Azure VM 来提供帮助，并在处理器利用率小于 x% 时自动关闭它们。  

默认情况下，解决方案已预配置为评估百分比 CPU 指标，并查看平均利用率是否为 5% 或更低。  如果其默认值不符合你的要求，以这由下变量控制并可以修改：

* External_AutoStop_MetricName
* External_AutoStop_Threshold
* External_AutoStop_TimeAggregationOperator
* External_AutoStop_TimeWindow

可以启用针对订阅和资源组的操作，或者针对特定虚拟机的列表，但不能同时启用两者。  

#### <a name="target-the-stop-action-against-a-subscription-and-resource-group"></a>针对订阅和资源组定位停止操作

1. 配置 External_Stop_ResourceGroupNames 和 External_ExcludeVMNames 变量以指定目标 VM。  
2. 启用和更新 Schedule_AutoStop_CreateAlert_Parent 计划。
3. 运行 ACTION 参数设置为 **start** 且 WHATIF 参数设置为 **True** 的 **AutoStop_CreateAlert_Parent** Runbook，以预览所做的更改。

#### <a name="target-the-stop-action-by-vm-list"></a>通过 VM 列表定位停止操作

1. 运行 ACTION 参数设置为 **start** 的 **AutoStop_CreateAlert_Parent** Runbook，在 *VMList* 参数中添加以逗号分隔的 VM 列表，运行 WHATIF 参数设置为 **True** 的 Runbook，以预览所做的更改。 预览更改。  
2. 通过以逗号分隔的 VM 列表 (VM1,VM2,VM3) 配置 **External_ExcludeVMNames** 参数。
3. 此方案使用 **External_Start_ResourceGroupNames** 和 **External_Stop_ResourceGroupnames** 变量。  对于此方案，需要创建自己的自动化计划。 有关详细信息，请参阅[在 Azure 自动化中计划 Runbook](../automation/automation-schedules.md)。

创建根据 CPU 利用率停止 VM 的计划后，接下来需要启用以下计划之一来启动 VM。

* 按订阅和资源组定位启动操作。  要测试和启用 **Scheduled-StartVM** 计划，请参阅[方案 1](#scenario-1:-daily-stop/start-vms-across-a-subscription-or-target-resource-groups) 中的步骤。
* 按订阅、资源组和标记定位启动操作。  要测试和启用 **Sequenced-StartVM** 计划，请参阅[方案 2](#scenario-2:-sequence-the-stop/start-vms-across-a-subscription-using-tags) 中的步骤。


### <a name="configuring-email-notifications"></a>配置电子邮件通知

若要在部署解决方案后配置电子邮件通知，请修改以下三个变量：

* External_EmailFromAddress：指定发件人的电子邮件地址。
* External_EmailToAddress：指定以逗号分隔的电子邮件地址列表 (user@hotmail.com, user@outlook.com)，用于接收通知电子邮件。
* External_IsSendEmail：设置为 **Yes** 可接收电子邮件。 若要禁用电子邮件通知，请将值设置为 **No**。   


### <a name="modifying-the-startup-and-shutdown-schedules"></a>修改启动和关闭计划

按照[在 Azure 自动化中计划 runbook](automation-schedules.md) 中所述的相同步骤来管理此解决方案中的启动和关闭计划。     

## <a name="log-analytics-records"></a>Log Analytics 记录

自动化在 OMS 存储库中创建两种类型的记录：作业日志和作业流。

### <a name="job-logs"></a>作业日志

属性 | 说明|
----------|----------|
调用方 |  谁启动了该操作。  可能的值为电子邮件地址或计划作业的系统。|
类别 | 数据类型的分类。  对于自动化，该值为 JobLogs。|
CorrelationId | 用作 Runbook 作业相关性 ID 的 GUID。|
JobId | 用作 Runbook 作业 ID 的 GUID。|
operationName | 指定在 Azure 中执行的操作类型。  对于自动化，该值为 Job。|
resourceId | 指定 Azure 中的资源类型。  对于自动化，该值是与 Runbook 关联的自动化帐户。|
resourceGroup | 指定 Runbook 作业的资源组名称。|
ResourceProvider | 指定  Azure 服务，它提供可部署和管理的资源。  对于自动化，该值为 Azure Automation。|
ResourceType | 指定 Azure 中的资源类型。  对于自动化，该值是与 Runbook 关联的自动化帐户。|
resultType | Runbook 作业的状态。  可能的值包括：<br>- Started（已启动）<br>- Stopped（已停止）<br>- Suspended（已暂停）<br>- Failed（失败）<br>- Succeeded（成功）|
resultDescription | 描述 Runbook 作业结果状态。  可能的值包括：<br>- 作业已启动<br>- 作业失败<br>- 作业已完成|
RunbookName | 指定 Runbook 的名称。|
SourceSystem | 指定所提交数据的源系统。  对于自动化，值为 OpsManager|
StreamType | 指定事件的类型。 可能的值包括：<br>- Verbose（详细）<br>- Output（输出）<br>- Error（错误）<br>- Warning（警告）|
SubscriptionId | 指定作业的订阅 ID。
时间 | 执行 Runbook 作业的日期和时间。|


### <a name="job-streams"></a>作业流

属性 | 说明|
----------|----------|
调用方 |  谁启动了该操作。  可能的值为电子邮件地址或计划作业的系统。|
类别 | 数据类型的分类。  对于自动化，该值为 JobStreams。|
JobId | 用作 Runbook 作业 ID 的 GUID。|
operationName | 指定在 Azure 中执行的操作类型。  对于自动化，该值为 Job。|
resourceGroup | 指定 Runbook 作业的资源组名称。|
resourceId | 指定 Azure 中的资源 ID。  对于自动化，该值是与 Runbook 关联的自动化帐户。|
ResourceProvider | 指定  Azure 服务，它提供可部署和管理的资源。  对于自动化，该值为 Azure Automation。|
ResourceType | 指定 Azure 中的资源类型。  对于自动化，该值是与 Runbook 关联的自动化帐户。|
resultType | 生成事件时 Runbook 作业的结果。 可能的值为：<br>- InProgress|
resultDescription | 包括来自 Runbook 的输出流。|
RunbookName | Runbook 的名称。|
SourceSystem | 指定所提交数据的源系统。  对于自动化，值为 OpsManager。|
StreamType | 作业流的类型。 可能的值包括：<br>- Progress（进度）<br>- Output（输出）<br>- Warning（警告）<br>- Error（错误）<br>- Debug（调试）<br>- Verbose（详细）|
时间 | 执行 Runbook 作业的日期和时间。|

如果执行的日志搜索返回 **JobLogs** 或 **JobStreams** 类别的记录时，可以选择 **JobLogs** 或 **JobStreams** 视图，其中显示了一组汇总搜索所返回的更新的磁贴。

## <a name="sample-log-searches"></a>示例日志搜索

下表提供了此解决方案收集的作业记录的示例日志搜索。 

查询 | 说明|
----------|----------|
查找已成功完成 Runbook ScheduledStartStop_Parent 的作业 | search Category == "JobLogs" &#124; where ( RunbookName_s == "ScheduledStartStop_Parent" ) &#124; where ( ResultType == "Completed" )  &#124; summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) &#124; sort by TimeGenerated desc|
查找已成功完成 Runbook SequencedStartStop_Parent 的作业 | search Category == "JobLogs" &#124; where ( RunbookName_s == "SequencedStartStop_Parent" ) &#124; where ( ResultType == "Completed" )  &#124; summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) &#124; sort by TimeGenerated desc

## <a name="removing-the-solution"></a>删除解决方案

如果决定不再需要使用解决方案，可从自动化帐户中删除它。  删除解决方案的操作只会删除 Runbook， 而不会删除添加解决方案时创建的计划或变量。  如果不会将它们与其他 Runbook 配合使用，则需要手动删除这些资产。  

若要删除解决方案，请执行以下步骤：

1.  从自动化帐户的左窗格中选择“工作区”。  
2.  在“解决方案”页中，请选择解决方案 Start-Stop-VM[Workspace]。  在“VMManagementSolution[Workspace]”页上，从菜单中选择“删除”。<br><br> ![删除 VM 管理解决方案](media/automation-solution-vm-management/vm-management-solution-delete.png)
3.  在“删除解决方案”窗口中，确认要删除该解决方案。
4.  在验证信息和删除解决方案期间，可以在菜单中的“通知”下面跟踪操作进度。  删除解决方案的进程启动后，系统会返回“解决方案”页。  

此进程不会删除自动化帐户和 Log Analytics 工作区。  如果不想保留 Log Analytics 工作区，则需要手动删除。  可以通过 Azure 门户完成此操作。

1.    在 Azure 门户主屏幕中，选择“Log Analytics”。
2. 在“Log Analytics”窗格中选择工作区。
3. 在工作区设置窗格中选择“删除”。  
      
## <a name="next-steps"></a>后续步骤

- 若要详细了解如何使用 Log Analytics 构造不同的搜索查询和查看自动化作业日志，请参阅 [Log Analytics 中的日志搜索](../log-analytics/log-analytics-log-searches.md)。
- 若要详细了解 Runbook 执行方式、如何监视 Runbook 作业和其他技术详细信息，请参阅[跟踪 Runbook 作业](automation-runbook-execution.md)。
- 若要了解有关 Log Analytics 和数据收集源的详细信息，请参阅[在 Log Analytics 中收集 Azure 存储数据概述](../log-analytics/log-analytics-azure-storage.md)。






   

