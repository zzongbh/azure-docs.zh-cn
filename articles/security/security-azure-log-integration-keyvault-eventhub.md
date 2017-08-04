---
title: "使用事件中心集成来自 Azure Key Vault 的日志 | Microsoft Docs"
description: "本教程提供了使用 Azure 日志集成使 Key Vault 日志可供 SIEM 使用需要执行的步骤"
services: security
author: barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 
ms.service: security
ms.topic: article
ms.date: 06/29/2017
ms.author: Barclayn
ms.custom: AzLog
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: a648852fadfeb5c9a4ff61c85bbe0af856e445d4
ms.contentlocale: zh-cn
ms.lasthandoff: 07/08/2017



---

# <a name="azure-log-integration-tutorial-process-azure-key-vault-events-by-using-event-hubs"></a>Azure 日志集成教程：使用事件中心处理 Azure Key Vault 事件

可以使用 Azure 日志集成 (AzLog) 检索已记录的事件并使其可供安全信息和事件管理 (SIEM) 系统使用。 本教程将指导你完成将 Azure Key Vault 活动记录到事件中心内并使其作为 JSON 文件供 SIEM 系统使用的过程。 然后，可以配置 SIEM 系统来处理 JSON 文件。

>[!NOTE]
>本教程中的大多数步骤都涉及配置 key vault、 存储帐户和事件中心。 具体的 Azure 日志集成步骤位于本教程的末尾。

沿途提供的信息可帮助你了解每个步骤背后的原因。 其他文章的链接提供了有关特定主题的更多详细信息。

有关本教程提到的服务的详细信息，请参阅： 

- [Azure Key Vault](../key-vault/key-vault-whatis.md)
- [Azure 事件中心](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Azure 日志集成](security-azure-log-integration-overview.md)


## <a name="initial-setup"></a>初始设置

要完成本文中的步骤，需要以下各项：

1. 一个 Azure 订阅和一个在该订阅中具有管理员权限的帐户。 如果没有订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free/)。
 
2. 一个能够访问 Internet 且满足 Azure 日志集成安装要求的系统。 该系统可以位于云服务中，也可以托管在本地。

3. 已安装了 [Azure 日志集成](https://www.microsoft.com/download/details.aspx?id=53324)。 若要安装它，请执行以下操作：
   1. 使用远程桌面连接到步骤 2 中提到的系统。
   2. 将 Azure 日志集成安装程序复制到该系统。 可以[下载安装文件](https://www.microsoft.com/download/details.aspx?id=53324)。
   3. 启动安装程序，并接受 Microsoft 软件许可条款中的条款。
   4. 如果你将提供遥测信息，请使复选框保持选中状态。 如果不希望向 Microsoft 发送使用情况信息，请清除复选框。
   
   有关 Azure 日志集成以及如何安装它的详细信息，请参阅[包含 Azure 诊断日志记录和 Windows 事件转发功能的 Azure 日志集成](security-azure-log-integration-get-started.md)。

4. 最新 PowerShell 版本。
 
   如果已安装了 Windows Server 2016，则已至少具有 PowerShell 5.0。 如果使用的是任何其他版本的 Windows Server，则可能安装了较早版本的 PowerShell。 可以通过在 PowerShell 窗口中键入 ```get-host``` 来检查版本。 如果未安装 PowerShell 5.0，可以[下载它](https://www.microsoft.com/download/details.aspx?id=50395)。

   在至少具有 PowerShell 5.0 后，可以继续安装最新版本：
   1. 在 PowerShell 窗口中，键入 ```Install-Module Azure``` 并按 Enter。 完成安装步骤。 
   2. 键入 ```Install-Module AzureRM```，然后按 Enter。 完成安装步骤。

   有关详细信息，请参阅[安装 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.0.0)。


## <a name="create-supporting-infrastructure-elements"></a>创建支持基础结构元素

1. 打开一个提升权限的 PowerShell 窗口并转至 **C:\Program Files\Microsoft Azure Log Integration**。
2. 通过运行脚本 LoadAzLogModule.ps1 导入 AzLog cmdlet。 （请注意以下命令中的“.\”。）键入 `.\LoadAzLogModule.ps1`，然后按 Enter。
您应看到与下面类似的内容：</br>

   ![已加载的模块列表](./media/security-azure-log-integration-keyvault-eventhub/loaded-modules.png)

3. 键入 `Login-AzureRmAccount`，然后按 Enter。 在登录窗口中，输入将用于本教程的订阅的凭据信息。

   >[!NOTE]
   >如果这是你首次从此计算机登录到 Azure，将会看到一条有关允许 Microsoft 收集 PowerShell 使用情况数据的消息。 建议启用此数据收集，因为该数据将用来改进 Azure PowerShell。

4. 在身份验证成功后，你将完成登录并看到以下屏幕截图中的信息。 请记下订阅 ID 和订阅名称，因为将需要使用它们完成后面的步骤。

   ![PowerShell 窗口](./media/security-azure-log-integration-keyvault-eventhub/login-azurermaccount.png)
5. 创建变量来存储后面将使用的值。 键入下面的每个 PowerShell 行并在每行后按 Enter。 可能需要调整值来匹配你的环境。
    - ```$subscriptionName = ‘Visual Studio Ultimate with MSDN’```（你的订阅名称可能不同。 可以在前面命令的输出中看到该名称。）
    - ```$location = 'West US'```（此变量将用来传递应当在其中创建资源的位置。 可以将此变量更改为你选择的任何其他位置。）
    - ```$random = Get-Random```
    - ``` $name = 'azlogtest' + $random```（名称可以是任何内容，但应当仅包含 小写字母和数字。）
    - ``` $storageName = $name```（此变量将用于存储帐户名称。）
    - ```$rgname = $name ```（此变量将用于资源组名称。）
    - ``` $eventHubNameSpaceName = $name```（这是事件中心命名空间的名称。）
6. 指定要使用的订阅：
    
    ```Select-AzureRmSubscription -SubscriptionName $subscriptionName```
7. 创建资源组：
    
    ```$rg = New-AzureRmResourceGroup -Name $rgname -Location $location```
    
   如果此时键入 `$rg` 并按 Enter，则应当会看到类似于以下屏幕截图的输出：

   ![创建资源组后的输出](./media/security-azure-log-integration-keyvault-eventhub/create-rg.png)
8. 创建一个将用来跟踪状态信息的存储帐户：
    
    ```$storage = New-AzureRmStorageAccount -ResourceGroupName $rgname -Name $storagename -Location $location -SkuName Standard_LRS```
9. 创建事件中心命名空间。 这是创建事件中心所必需的。
    
    ```$eventHubNameSpace = New-AzureRmEventHubNamespace -ResourceGroupName $rgname -NamespaceName $eventHubnamespaceName -Location $location```
10. 获取将用于见解提供程序的规则 ID：
    
    ```$sbruleid = $eventHubNameSpace.Id +'/authorizationrules/RootManageSharedAccessKey' ```
11. 获取所有可能的 Azure 位置并将名称添加到可以在后面的步骤中使用的变量：
    
    1. ```$locationObjects = Get-AzureRMLocation```    
    2. ```$locations = @('global') + $locationobjects.location```
    
    如果此时键入 `$locations` 并按 Enter，则会看到位置名称，且不会看到 Get-AzureRmLocation 返回的其他信息。
12. 创建 Azure Resource Manager 日志配置文件： 
    
    ```Add-AzureRmLogProfile -Name $name -ServiceBusRuleId $sbruleid -Locations $locations```
    
    有关 Azure 日志配置文件的详细信息，请参阅 [Azure 活动日志概述](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)。

>[!NOTE]
>尝试创建日志配置文件时可能会收到错误消息。 然后，你可以在文档中检查 Get-AzureRmLogProfile 和 Remove-AzureRmLogProfile。 如果运行 Get-AzureRmLogProfile，则可以看到有关日志配置文件的信息。 可以通过键入 ```Remove-AzureRmLogProfile -name 'Log Profile Name' ``` 并按 Enter 来删除现有的日志配置文件。
>
>![Resource Manager 配置文件错误](./media/security-azure-log-integration-keyvault-eventhub/rm-profile-error.png)

## <a name="create-a-key-vault"></a>创建 key vault

1. 创建 key vault：

   ```$kv = New-AzureRmKeyVault -VaultName $name -ResourceGroupName $rgname -Location $location ```

2. 配置 key vault 的日志记录：

   ```Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -ServiceBusRuleId $sbruleid -Enabled $true ```

## <a name="generate-log-activity"></a>生成日志活动

需要向 Key Vault 发送请求来生成日志活动。 诸如密钥生成、存储机密或从 Key Vault 中读取机密之类的活动都将创建日志条目。

1. 显示当前的存储密钥：
    
   ```Get-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
2. 生成一个新的 **key2**：
    
   ```New-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname -KeyName key2```
3. 再次显示密钥，可以发现 **key2** 存储着一个不同的值：
    
   ```Get-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
4. 设置并读取机密来生成其他日志条目：
    
   1. ```Set-AzureKeyVaultSecret -VaultName $name -Name TestSecret -SecretValue (ConvertTo-SecureString -String 'Hi There!' -AsPlainText -Force)```    
   2. ```(Get-AzureKeyVaultSecret -VaultName $name -Name TestSecret).SecretValueText```

   ![返回的机密](./media/security-azure-log-integration-keyvault-eventhub/keyvaultsecret.png)


## <a name="configure-azure-log-integration"></a>配置 Azure 日志集成

现在，你已配置了将 Key Vault 活动记录到事件中心内所需的所有元素，接下来需要配置 Azure 日志集成：

1. ```$storage = Get-AzureRmStorageAccount -ResourceGroupName $rgname -Name $storagename```
2. ```$eventHubKey = Get-AzureRmEventHubNamespaceKey -ResourceGroupName $rgname -NamespaceName $eventHubNamespace.name -AuthorizationRuleName RootManageSharedAccessKey```
3. ```$storagekeys = Get-AzureRmStorageAccountKey -ResourceGroupName $rgname -Name $storagename```
4. ``` $storagekey = $storagekeys[0].Value```

针对每个事件中心运行 AzLog 命令：

1. ```$eventhubs = Get-AzureRmEventHub -ResourceGroupName $rgname -NamespaceName $eventHubNamespaceName```
2. ```$eventhubs.Name | %{Add-AzLogEventSource -Name $sub' - '$_ -StorageAccount $storage.StorageAccountName -StorageKey $storageKey -EventHubConnectionString $eventHubKey.PrimaryConnectionString -EventHubName $_}```

在运行最后两个命令大约一分钟后，应当会看到正在生成的 JSON 文件。 可以通过监视 **C:\users\AzLog\EventHubJson** 进行确认。

## <a name="next-steps"></a>后续步骤

- [Azure 日志集成常见问题解答](security-azure-log-integration-faq.md)
- [Azure 日志集成入门](security-azure-log-integration-get-started.md)
- [将来自 Azure 资源的日志集成到 SIEM 系统](security-azure-log-integration-overview.md)
