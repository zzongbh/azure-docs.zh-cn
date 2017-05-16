---
title: "Azure 安全性简介 | Microsoft Docs"
description: "了解 Azure 安全性以及其服务和工作原理。"
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/27/2017
ms.author: TomSh
ms.translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: 01a9876f3a8500bc6c6bb6b3cbceeb8921147376
ms.contentlocale: zh-cn
ms.lasthandoff: 05/03/2017


---

# <a name="introduction-to-azure-security"></a>Azure 安全性简介
## <a name="11-overview"></a>1.1 概述
我们知道，安全是云中的首要任务，及时找到有关 Azure 安全性的准确信息极其重要。 将 Azure 用于应用程序和服务的最合理原因之一是可以利用其各种安全工具和功能。 这些工具和功能可帮助在安全的 Azure 平台上创建安全的解决方案。 Microsoft Azure 提供具备保密性、完整性和可用性的客户数据，同时还能实现透明的问责制。

为帮助客户从客户和 Microsoft 操作两个角度更好地了解在 Microsoft Azure 中实施的安全控件集合，特编写本白皮书《Azure 安全性简介》，以全面了解 Microsoft Azure 的安全性。

### <a name="12-azure-platform"></a>1.2 Azure 平台
Azure 是一个公有云服务平台，支持极为广泛的操作系统、编程语言、框架、工具、数据库和设备选择。 它可运行与 Docker 集成的 Linux 容器；使用 JavaScript、Python、.NET、PHP、Java 和 Node.js 生成应用；生成适用于 iOS、Android 和 Windows 设备的后端。

Azure 公有云服务支持数百万开发人员和 IT 专业人士已经有所依赖并信任的相同技术。 构建 IT 资产或将其迁移到公有云服务提供商处时，需要借助该组织的能力来保护应用程序和数据，并使用该组织提供的服务和控制机制来管理基于云的资产的安全性。

Azure 的基础结构（从设备到应用程序）经过设计，可同时托管数百万的客户，并为企业提供可靠的基础，使之能够满足其安全要求。

此外，Azure 还提供广泛的可配置安全选项以及对这些选项进行控制的功能，方便用户自定义安全措施来满足组织部署的独特要求。 本文档可帮助用户了解 Azure 安全功能如何帮助满足这些要求。

> [!Note]
> 本文档重点介绍面向客户的控件，客户可以使用这些控件自定义和提高应用程序和服务的安全性。
>
> 我们还提供一些概述信息，但是若要深入了解 Microsoft 如何保护 Azure 平台本身，请参阅 [Microsoft 信任中心](https://www.microsoft.com/TrustCenter/default.aspx)中提供的信息。

### <a name="13-abstract"></a>1.3 摘要
最初，成本节约和灵活创新是公有云迁移的驱动力。 一段时间以来，安全性是主要关注的问题，甚至可以说是影响公有云迁移的关键因素。 然而，公有云安全性已经从主要关注点转换为云迁移的驱动力之一。 促成这种转换的原因是大型公有云服务提供商保护应用程序和基于云的资产数据的卓越能力。

Azure 的基础结构（从设备到应用程序）经过设计，可同时托管数百万的客户，并为企业提供可靠的基础，使之能够满足其安全需求。 此外，Azure 还提供广泛的可配置安全选项以及对这些选项进行控制的功能，方便用户自定义安全措施来满足部署的独特要求，进而满足 IT 控制策略并遵守外部法规。

本文概述了 Microsoft 在 Microsoft Azure 云平台中确保安全性的方法：
* Microsoft 实施的安全功能可保护 Azure 基础结构、客户数据和应用程序的安全。
* Azure 服务和安全功能可用于管理 Azure 订阅中的服务安全和数据。

## <a name="20-summary-azure-security-capabilities"></a>2.0 Azure 安全功能汇总
下表简要描述了 Microsoft 为保护 Azure 基础结构、客户数据和应用程序的安全而实现的安全功能。
### <a name="21-security-features-implemented-to-secure-the-azure-platform"></a>2.1 为保护 Azure 平台而实现的安全功能：
以下列出的功能可以用于确保以安全的方式管理 Azure 平台。 提供了相应链接，方便用户进一步了解 Microsoft 如何从四个方面解决客户信任问题：安全平台、隐私和控制、符合性和透明度。


| [安全平台](https://www.microsoft.com/en-us/trustcenter/Security/default.aspx)  | [隐私和控制](https://www.microsoft.com/en-us/trustcenter/Privacy/default.aspx)  |[合规性](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx)   | [透明度](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx) |
| :-- | :-- | :-- | :-- | 
| [安全开发周期](https://www.microsoft.com/en-us/sdl/)，内部审核 | [随时进行数据管理](https://www.microsoft.com/en-us/trustcenter/Privacy/You-own-your-data) | [信任中心](https://www.microsoft.com/en-us/trustcenter/default.aspx) |[Microsoft 如何保护 Azure 服务中的客户数据](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx) |
| [强制性安全培训，背景检查](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=2&cad=rja&uact=8&ved=0ahUKEwiwsOCpganRAhWqxVQKHUdiDsMQFghAMAE&url=https%3A%2F%2Fdownloads.cloudsecurityalliance.org%2Fstar%2Fself-assessment%2FStandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx&usg=AFQjCNEYvBky4zNeDQPN6YJGPFRZA7eeZg&sig2=2kkw1lOCP_kzLzgE9RS2Tg&bvm=bv.142059868,d.amc) |  [控制数据位置](https://www.microsoft.com/en-us/trustcenter/Privacy/Where-your-data-is-located) |  [通用控制中心](https://www.microsoft.com/en-us/trustcenter/Common-Controls-Hub) |[Microsoft 如何管理 Azure 服务中的数据位置](http://azuredatacentermap.azurewebsites.net/)|
| [渗透测试](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=2&cad=rja&uact=8&ved=0ahUKEwiwsOCpganRAhWqxVQKHUdiDsMQFghAMAE&url=https%3A%2F%2Fdownloads.cloudsecurityalliance.org%2Fstar%2Fself-assessment%2FStandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx&usg=AFQjCNEYvBky4zNeDQPN6YJGPFRZA7eeZg&sig2=2kkw1lOCP_kzLzgE9RS2Tg&bvm=bv.142059868,d.amc)，[入侵检测，DDoS](https://www.microsoft.com/en-us/trustcenter/Security/ThreatManagemen)，[审核和日志记录](https://www.microsoft.com/en-us/trustcenter/Security/AuditingAndLogging) | [根据条件提供数据访问](https://www.microsoft.com/en-us/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms) |  [云服务审慎调查清单](https://www.microsoft.com/en-us/trustcenter/Compliance/Due-Diligence-Checklist) |[Microsoft 中的哪些人员可以根据哪些条款访问你的数据](https://www.microsoft.com/en-us/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms)|
| [数据中心状态](https://www.microsoft.com/en-us/cloud-platform/global-datacenters)，物理安全性，[安全网络](https://docs.microsoft.com/en-us/azure/security/security-network-overview) | [响应执法部门](https://www.microsoft.com/en-us/trustcenter/Privacy/Responding-to-govt-agency-requests-for-customer-data) |  [服务、位置和行业的符合性](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx) |[Microsoft 如何保护 Azure 服务中的客户数据](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx)|
|  [安全事件响应](http://aka.ms/SecurityResponsepaper)，[共担责任](http://aka.ms/sharedresponsibility) |[严格的隐私标准](https://www.microsoft.com/en-us/TrustCenter/Privacy/We-set-and-adhere-to-stringent-standards) |  | [查看 Azure 服务和透明度中心的认证](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx)|



### <a name="22-security-features-offered-by-azure-to-secure-data-and-application"></a>2.2 Azure 为保护数据和应用程序而提供的安全功能
根据云服务模型，负责管理应用程序或服务的安全的人员需承担各种不同的责任。 Azure 平台中提供的功能可帮助用户通过内置功能以及可部署到 Azure 订阅中的合作伙伴解决方案来履行这些职责。

可在以下六 (6) 个功能区域中组织这些内置功能：操作、应用程序、存储、网络、计算和标识。 摘要信息对这六 (6) 个区域内 Azure 平台提供的特性和功能进行了详细介绍。

## <a name="30-operations"></a>3.0 操作
本部分提供了关于安全操作中主要特性的其他信息以及有关这些功能的摘要信息。

### <a name="31-operations-management-suite-security-and-audit-dashboard"></a>3.1 Operations Management Suite 安全和审核仪表板
[OMS 安全和审核解决方案](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started)借助[内置搜索查询](https://blogs.technet.microsoft.com/msoms/2016/01/21/easy-microsoft-operations-management-suite-search-queries/)找到需要关注的重要问题，从而提供有关组织的 IT 安全态势的全面观点。 “安全和审核”[](https://technet.microsoft.com/library/mt484091.aspx)仪表板是主屏幕，提供 OMS 中安全的所有相关内容。 它提供计算机安全状态的高级洞见。 还允许查看过去 24 小时、7 天或任何自定义时间范围的所有事件。

此外，检测到特定事件时，可以将 OMS 安全性和符合性配置为[自动执行特定操作](https://blogs.technet.microsoft.com/robdavies/2016/04/20/simple-look-at-oms-alert-remediation-with-runbooks-part-1/)。

### <a name="32-azure-resource-manager"></a>3.2 Azure Resource Manager
可以使用 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) 以组的方式处理解决方案中的资源。 可以通过一个协调的操作为解决方案部署、更新或删除所有资源。 可以使用 [Azure Resource Manager 模板](https://blogs.technet.microsoft.com/canitpro/2015/06/29/devops-basics-infrastructure-as-code-arm-templates/)来完成部署，该模板适用于测试、过渡和生产等不同环境。 资源管理器提供安全、审核和标记功能，以帮助你在部署后管理资源。

基于 Azure Resource Manager 模板的部署因其标准的安全控制设置，有助于提高 Azure 中部署的解决方案的安全性，并且还可以集成到基于标准化模板的部署中。 这样可以降低手动部署期间可能发生的安全配置错误风险。

### <a name="33-application-insights"></a>3.3 Application Insights
[Application Insights](https://docs.microsoft.com/azure/application-insights/) 是面向 Web 开发人员的可扩展应用程序性能管理 (APM) 服务。 用户可以使用 Application Insights 监视实时 Web 应用程序并自动检测性能异常。 Application Insights 内含强大的分析工具，有助于诊断问题并了解用户在应用中实际执行的操作。 它在应用程序运行时全程进行监视，包括测试期间以及发布或部署之后。

Application Insights 可创建图表和表格来显示多种信息，例如，一天中的哪些时间用户最多、应用的响应能力如何，以及应用依赖的任何外部服务是否顺利地为其提供服务。

如果出现崩溃、故障或性能问题，可以搜索详细的遥测数据来诊断原因。 此外，如果应用的可用性和性能有任何变化，该服务还会向用户发送电子邮件。 Application Insight 就是这样因其有助于实现保密性、完整性和可用性安全三元素的“可用性”而成为有价值的安全工具。

### <a name="34-azure-monitor"></a>3.4 Azure Monitor
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) 对来自 Azure 基础结构（[活动日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)）和每个单独的 Azure 资源（[诊断日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)）的数据提供可视化效果、查询、路由、警报、自动缩放和自动化功能。 可以使用 Azure Monitor 对 Azure 日志中生成的与安全相关的事件发出警报。

### <a name="35-log-analytics"></a>3.5 Log Analytics
[Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite) 中的 [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) 组件 – 为本地基础结构和第三方基于云的基础结构（例如 AWS），以及 Azure 资源提供 IT 管理解决方案。 可以将来自 Azure Monitor 的数据直接路由到 Log Analytics，因此你可以在一个位置查看整个环境的指标和日志。

在取证和其他安全分析中，Log Analytics 是非常有用的工具，因为使用该工具能通过灵活的查询方法快速搜索大量与安全相关的条目。 此外，本地[防火墙和代理日志可以导出到 Azure 中，并可以使用 Log Analytics 进行分析](https://docs.microsoft.com/azure/log-analytics/log-analytics-proxy-firewall)。

### <a name="36-azure-advisor"></a>3.6 Azure 顾问
[Azure 顾问](https://docs.microsoft.com/azure/advisor/)是一种个性化的云顾问，可帮助优化 Azure 部署。 它分析资源配置和使用情况遥测数据。 然后，它推荐解决方案，帮助提高资源的[性能](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations)、[安全性](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations)和[高可用性](https://docs.microsoft.com/azure/advisor/advisor-high-availability-recommendations)，同时寻找机会[减少总体 Azure 支出](https://docs.microsoft.com/azure/advisor/advisor-cost-recommendations)。 Azure 顾问提供安全建议，可显著提高在 Azure 中部署的解决方案的总体安全状况。 这些建议来自于 [Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)执行的安全分析。

### <a name="37-azure-security-center"></a>3.7 Azure 安全中心
[Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)有助于预防、检测和响应威胁，同时增加 Azure 资源的可见性和安全可控性。 它提供 Azure 订阅之间的集成安全监视和策略管理，帮助检测可能被忽略的威胁，且适用于广泛的安全解决方案生态系统。

此外，Azure 安全中心通过提供单个仪表板实现可立即执行的警报和建议，从而帮助进行安全操作。 通常，只需在 Azure 安全中心控制台中单击一下就可修复问题。
## <a name="40-applications"></a>4.0 应用程序
本部分提供了关于应用程序安全中主要特性的其他信息以及有关这些功能的摘要信息。

### <a name="41-web-application-vulnerability-scanning"></a>4.1 Web 应用程序漏洞扫描
开始对[应用服务应用](https://docs.microsoft.com/azure/app-service/app-service-value-prop-what-is)进行漏洞测试最简单的一种方法是使用[与 Tinfoil Security 的集成](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)对应用执行一键式漏洞扫描。 你可以查看易于理解的报告中的测试结果，并了解如何按照分步说明修复每个安全漏洞。

### <a name="42-penetration-testing"></a>4.2 渗透测试
如果想要执行自己的渗透测试，或者想要使用其他扫描程序套件或提供程序，则必须按照 [Azure 渗透测试审批流程](https://security-forms.azure.com/penetration-testing/terms)来进行并获得事先批准才能执行所需的渗透测试。

### <a name="43-web-application-firewall"></a>4.3 Web 应用程序防火墙
[Azure 应用程序网关](https://azure.microsoft.com/services/application-gateway/)中的 Web 应用程序防火墙 (WAF) 可帮助保护 Web 应用程序，使其免受常见基于 Web 的攻击威胁，例如 SQL 注入、跨站点脚本攻击和会话劫持。 同时预先配置保护，免受 [Open Web Application Security Project (OWASP) 标识为前 10 种常见漏洞](https://msdn.microsoft.com/library/)的威胁攻击。

### <a name="44-authentication-and-authorization-in-azure-app-service"></a>4.4 Azure 应用服务中的身份验证和授权
[应用服务身份验证/授权](https://docs.microsoft.com/azure/app-service/app-service-authentication-overview)是一项功能，方便应用程序登录用户，避免在应用后端更改代码。 该功能可以方便地保护应用程序和处理每个用户的数据。

### <a name="45-layered-security-architecture"></a>4.5 分层安全体系结构
由于[应用服务环境](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-intro)提供部署到 [Azure 虚拟网络](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)的隔离运行时环境，因此开发人员能够创建分层安全体系结构，针对每个应用层提供不同级别的网络访问权限。 常见的需求之一是要隐藏对 API 后端的常规 Internet 访问，而只允许由上游 Web 应用调用 API。 可以在包含应用服务环境的 Azure 虚拟网络子网上使用[网络安全组 (NSG)](https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/)，限制对 API 应用程序的公共访问。

### <a name="46-web-server-diagnostics-and-application-diagnostics"></a>4.6 Web 服务器诊断和应用程序诊断
应用服务 Web 应用为 Web 服务器和 Web 应用程序中的日志记录信息提供诊断功能。 这些诊断功能按逻辑分为 [Web 服务器诊断](https://docs.microsoft.com/azure/app-service-web/web-sites-enable-diagnostic-log)和[应用程序诊断](https://technet.microsoft.com/library/hh530058(v=sc.12).aspx)。 Web 服务器包括诊断和排查站点和应用程序这两大改进方面。

第一个新特点是有关应用程序池、工作进程、站点、应用程序域和运行请求的实时状态信息。 第二个新特点是在整个请求和响应过程中跟踪请求的详细跟踪事件。

若要启用这些跟踪事件的集合，可以将 IIS 7 配置为根据运行时间或错误响应代码自动捕获任何特定请求的完整跟踪日志（采用 XML 格式）。

#### <a name="461-web-server-diagnostics"></a>4.6.1 Web 服务器诊断
你可以启用或禁用以下种类的日志：

-    详细错误日志记录 - 指示故障的 HTTP 状态代码（状态代码 400 或更大数字）的详细错误消息。 其中可能包含有助于确定服务器返回错误代码的原因的信息。

-    失败请求跟踪 - 有关失败请求的详细信息，包括对用于处理请求的 IIS 组件和每个组件所用的时间的跟踪。 在尝试提高站点性能或隔离导致要返回特定 HTTP 错误的内容时，此信息很有用。

-    Web 服务器日志记录 - 使用 W3C 扩展日志文件格式的 HTTP 事务信息。 这在确定整体站点度量值（如处理的请求数量或来自特定 IP 地址的请求数）时非常有用。

#### <a name="462-application-diagnostics"></a>4.6.2 应用程序诊断
[应用程序诊断](https://docs.microsoft.com/azure/app-service-web/web-sites-enable-diagnostic-log)可以捕获由 Web 应用程序生成的信息。 ASP.NET 应用程序可使用 [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace) 类将信息记录到应用程序诊断日志。 在应用程序诊断中，有两种主要类型的事件，即与应用程序性能相关的事件以及与应用程序故障和错误相关的事件。 故障和错误可以进一步分为连接性、安全性和故障问题。 故障问题通常与应用程序代码问题相关。

在应用程序诊断中，可以查看按以下方式分组的事件：

-    全部（显示所有事件）
-    应用程序错误（显示异常事件）
-    性能（显示性能事件）

## <a name="50-storage"></a>5.0 存储
本部分提供了关于 Azure 存储安全中主要特性的其他信息以及有关这些功能的摘要信息。

### <a name="51-role-based-access-control-rbac"></a>5.1 基于角色的访问控制 (RBAC)
可以使用基于角色的访问控制 (RBAC) 来保护存储帐户。 对于想要实施数据访问安全策略的组织而言，必须根据[需知原则](https://en.wikipedia.org/wiki/Need_to_know)和[最低权限](https://en.wikipedia.org/wiki/Principle_of_least_privilege)安全原则限制访问权限。 这些访问权限是通过将相应的 RBAC 角色分配给特定范围内的组和应用程序来授予的。 可以使用[内置 RBAC 角色](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles)（例如存储帐户参与者）将权限分配给用户。 可通过基于角色的访问控制 (RBAC)，控制借助 [Azure Resource Manager](https://docs.microsoft.com/azure/storage/storage-security-guide) 模型访问存储帐户的存储密钥的情况。

### <a name="52-shared-access-signature"></a>5.2 共享访问签名
[共享访问签名 (SAS)](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) 用于对存储帐户中的资源进行委托访问。 使用 SAS，意味着可以授权客户端在指定时间段内，以一组指定权限有限访问你的存储帐户中的对象。 可以授予这些有限的权限，而不必共享帐户访问密钥。

### <a name="53-encryption-in-transit"></a>5.3 传输中加密
传输中加密是通过网络传输数据时用于保护数据的一种机制。 在 Azure 存储中，可以使用以下加密方式来保护数据：
-    [传输级别加密](https://docs.microsoft.com/azure/storage/storage-security-guide#encryption-in-transit)，例如从 Azure 存储传入或传出数据时使用的 HTTPS。

-    [线路加密](https://docs.microsoft.com/azure/storage/storage-security-guide#using-encryption-during-transit-with-azure-file-shares)，例如 [Azure 文件共享](https://docs.microsoft.com/azure/storage/storage-dotnet-how-to-use-files)的 [SMB 3.0 加密](https://docs.microsoft.com/azure/storage/storage-security-guide)。

-    客户端加密，在将数据传输到存储之前加密数据，以及从存储传出数据后解密数据。

### <a name="54-encryption-at-rest"></a>5.4 静态加密
对许多组织而言，静态数据加密是实现数据隐私性、符合性和数据所有权的必要措施。 有三项 Azure 存储安全功能可提供“静态”数据加密：

-    [存储服务加密](https://docs.microsoft.com/azure/storage/storage-service-encryption)可以请求存储服务在将数据写入 Azure 存储时自动加密数据。

-    [客户端加密](https://docs.microsoft.com/azure/storage/storage-client-side-encryption)也提供静态加密功能。

-    [Azure 磁盘加密](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)允许加密 IaaS 虚拟机使用的 OS 磁盘和数据磁盘。

### <a name="55-storage-analytics"></a>5.5 存储分析
[Azure 存储分析](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)执行日志记录并为存储帐户提供指标数据。 可以使用此数据跟踪请求、分析使用情况趋势以及诊断存储帐户的问题。 存储分析记录成功和失败的存储服务请求的详细信息。 可以使用该信息监视各个请求和诊断存储服务问题。 将最大程度地记录请求。 将记录以下类型的已经过身份验证的请求：
-    成功的请求。

-    失败的请求，包括超时、限制、网络、授权和其他错误。

-    使用共享访问签名 (SAS) 的请求，包括失败和成功的请求。

-    分析数据请求。

### <a name="56-enabling-browser-based-clients-using-cors"></a>5.6 使用 CORS 启用基于浏览器的客户端
[跨源资源共享 (CORS)](https://docs.microsoft.com/rest/api/storageservices/fileservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) 是一种允许域授予彼此资源访问权限的机制。 用户代理发送额外的标头，以确保允许从特定域中加载的 JavaScript 代码访问位于另一个域的资源。 然后，后一个域使用额外标头进行回复，允许或拒绝原始域访问其资源。

Azure 存储服务现支持 CORS，因此，为服务设置 CORS 规则后，便会对从另一个域对服务发出的经过正确验证的请求进行评估，以根据指定的规则确定是否允许该请求。
## <a name="60-networking"></a>6.0 网络
本部分提供了关于 Azure 网络安全中主要特性的其他信息以及有关这些功能的摘要信息。

### <a name="61-network-layer-controls"></a>6.1 网络层控制
网络访问控制是限制特定设备或子网之间的连接的行为，代表了网络安全的核心。 网络访问控制旨在确保你的虚拟机和服务仅让你指定可访问的用户和设备进行访问。

#### <a name="611-network-security-groups"></a>6.1.1 网络安全组
[网络安全组 (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) 是基本的静态数据包筛选防火墙，使用户能够基于 [5 元组](https://www.techopedia.com/definition/28190/5-tuple)控制访问权限。 NSG 不提供应用程序层检查或经过身份验证的访问控制。 它们可用于控制在 Azure 虚拟网络中的子网之间移动的流量以及控制 Azure 虚拟网络和 Internet 之间的流量。

#### <a name="612-route-control-and-forced-tunneling"></a>6.1.2 路由控制和强制隧道
在 Azure 虚拟网络上控制路由行为的能力是关键的网络安全和访问控制功能。 例如，如果要确保与 Azure 虚拟网络之间的所有流量都通过该虚拟安全设备，则必须能够控制和自定义路由行为。 可以通过在 Azure 中配置用户定义的路由实现此操作。

[用户定义的路由](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)允许用户为进出单个虚拟机或子网的流量自定义入站和出站路径，以确保最安全的路由。 [强制隧道](https://www.petri.com/azure-forced-tunneling)是一种机制，可用于确保不允许你的服务启动到 Internet 上的设备的连接。

这不同于能够接受传入连接然后对其作出响应。 前端 Web 服务器需要响应来自 Internet 主机的请求，因此允许源自 Internet 的流量传入到这些 Web 服务器，而且这些 Web 服务器可以作出响应。

强制隧道通常用于强制到 Internet 的外部流量通过本地安全代理和防火墙。

#### <a name="613-virtual-network-security-appliances"></a>6.1.3 虚拟网络安全设备
虽然网络安全组、用户定义的路由和强制隧道在 [OSI 模型](https://en.wikipedia.org/wiki/OSI_model)的网络层和传输层为用户提供了一定程度的安全性，但有时可能想要启用堆栈的更高级别安全性。 可以使用 Azure 合作伙伴安全设备解决方案访问这些增强的网络安全功能。 通过访问 [Azure 应用商店](https://azure.microsoft.com/marketplace/)并搜索“安全”和“网络安全”，可以找到最新的 Azure 合作伙伴网络安全解决方案。

### <a name="62-azure-virtual-network"></a>6.2 Azure 虚拟网络

Azure 虚拟网络 (VNet) 是你自己的网络在云中的表示形式。 它是对专用于你的订阅的 Azure 网络结构进行的逻辑隔离。 你可以完全控制该网络中的 IP 地址块、DNS 设置、安全策略和路由表。 可以将 VNet 细分成各个子网，并在 Azure 虚拟网络上放置 Azure IaaS 虚拟机 (VM) 和/或[云服务（PaaS 角色实例）](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me)。

此外，也可以使用 Azure 中提供的[连接选项](https://docs.microsoft.com/azure/vpn-gateway/)之一，将虚拟网络连接到本地网络。 实际上，你可以将网络扩展到 Azure，对 IP 地址块进行完全的控制，并享受企业级 Azure 带来的好处。

Azure 网络支持各种安全远程访问方案。 其中包括：

-    [将单独的工作站连接到 Azure 虚拟网络](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

-    [通过 VPN 将本地网络连接到 Azure 虚拟网络](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)

-    [通过专用 WAN 链接将本地网络连接到 Azure 虚拟网络](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)

-    [相互连接 Azure 虚拟网络](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps)

### <a name="63-vpn-gateway"></a>6.3 VPN 网关
若要在 Azure 虚拟网络与本地站点之间发送网络流量，必须为 Azure 虚拟网络创建 VPN 网关。 [VPN 网关](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)是一种虚拟网络网关，可以通过公共连接发送加密流量。 也可以使用 VPN 网关在基于 Azure 网络结构的 Azure 虚拟网络之间发送流量。

### <a name="64-express-route"></a>6.4 快速路由
Microsoft Azure [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 是专用 WAN 链接，可让用户通过连接服务提供商所提供的专用连接，将本地网络扩展到 Microsoft 云。

![Express Route](./media/azure-security/azure-security-fig1.png)

使用 ExpressRoute 可与 Microsoft Azure、Office 365 和 CRM Online 等 Microsoft 云服务建立连接。 可以从任意位置之间的 (IP VPN) 网络、点到点以太网或在共置设施上通过连接服务提供商的虚拟交叉连接来建立这种连接。

ExpressRoute 连接不会通过公共 Internet，因此可以认为它比基于 VPN 的解决方案更安全。 与通过 Internet 的典型连接相比，ExpressRoute 连接提供更高的可靠性、更快的速度、更低的延迟和更高的安全性。


### <a name="65-application-gateway"></a>6.5 应用程序网关
Microsoft [Azure 应用程序网关](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction)以服务形式提供[应用程序传送控制器 (ADC)](https://en.wikipedia.org/wiki/Application_delivery_controller)，借此为应用程序提供第 7 层各种负载均衡功能。

![应用程序网关](./media/azure-security/azure-security-fig2.png)

它使用户能够通过将 CPU 密集型 SSL 终端的负载卸载到应用程序网关（也称为“SSL 卸载”或“SSL 桥接”）来优化 Web 场生产率。 它还提供第 7 层其他路由功能，包括传入流量的轮循机制分配、基于 Cookie 的会话相关性、基于 URL 路径的路由，以及在单个应用程序网关后面托管多个网站的能力。 Azure 应用程序网关是第 7 层负载均衡器。

它在不同服务器之间提供故障转移和性能路由 HTTP 请求，而不管它们是在云中还是本地。

应用程序网关提供多种应用程序传送控制器 (ADC) 功能，包括 HTTP 负载均衡、基于 cookie 的会话相关性、[安全套接字层 (SSL)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-powershell) 卸载、自定义运行状况探测、多站点支持，以及许多其他功能。

### <a name="66-web-application-firewall"></a>6.6 Web 应用程序防火墙
Web 应用程序防火墙是 [Azure 应用程序网关](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction)的一项功能，它为使用应用程序网关实现标准应用程序传递控制 (ADC) 功能的 Web 应用程序提供保护。 Web 应用程序防火墙通过保护这些应用程序，免受 OWASP 前 10 个常见的 Web 漏洞中的大多数漏洞的威胁，来实现此目的。

![Web 应用程序防火墙](./media/azure-security/azure-security-fig1.png)

-    SQL 注入保护

-    常见 Web 攻击保护，例如命令注入、HTTP 请求走私、HTTP 响应拆分和远程文件包含攻击

-    防止 HTTP 协议违反行为

-    防止 HTTP 协议异常行为，例如缺少主机用户代理和接受标头

-    防止自动程序、爬网程序和扫描程序

-    检测常见应用程序错误配置（即 Apache、IIS 等）


可防止 Web 攻击的集中式 Web 应用程序防火墙，可简化安全管理，并可针对入侵威胁为应用程序提供更好的保障。 相较保护每个单独的 Web 应用程序，WAF 解决方案还可通过在中央位置修补已知漏洞，更快地响应安全威胁。 现有应用程序网关可以轻松地转换为带有 Web 应用程序防火墙的应用程序网关。
### <a name="67-traffic-manager"></a>6.7 流量管理器
使用 Microsoft [Azure 流量管理器](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview)，可以控制用户流量在不同数据中心内的服务终结点上的分布。 流量管理器支持的服务终结点包括 Azure VM、Web 应用和云服务。 也可将流量管理器用于外部的非 Azure 终结点。 流量管理器根据[流量路由方法](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods)和终结点的运行状况，使用域名系统 (DNS) 将客户端请求定向到最合适的终结点。

流量管理器提供多种流量路由方法来满足不同的应用程序需求、终结点运行状况[监视](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring)和自动故障转移。 流量管理器能够灵活应对故障，包括整个 Azure 区域的故障。
### <a name="68-azure-load-balancer"></a>6.8 Azure 负载均衡器
[Azure 负载均衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) 可提高应用程序的可用性和网络性能。 它是第 4 层（TCP、UDP）类型的负载均衡器，可在负载平衡集中定义的运行状况良好的服务实例之间分配传入流量。 可以将 Azure 负载均衡器配置为：

-    对传入到虚拟机的 Internet 流量进行负载均衡。 此配置称为[面向 Internet 的负载均衡](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview)。

-    对虚拟网络中虚拟机之间的流量、云服务中虚拟机之间的流量或本地计算机和跨界虚拟网络中虚拟机之间的流量进行负载均衡。 此配置称为[内部负载均衡](https://docs.microsoft.com/azure/load-balancer/load-balancer-internal-overview)。 • 将外部流量转发到特定的虚拟机

### <a name="69-internal-dns"></a>6.9 内部 DNS
你可以在管理门户或网络配置文件中管理 VNet 中使用的 DNS 服务器列表。 客户最多可以为每个 VNet 添加 12 个 DNS 服务器。 指定 DNS 服务器时，请务必按照客户环境的正确顺序列出客户的 DNS 服务器。 DNS 服务器列表不采用循环机制。 将按指定服务器的顺序使用这些服务器。 如果可访问列表上的第一个 DNS 服务器，则无论该 DNS 服务器是否运行正常，客户端都将使用该服务器。 若要更改客户的虚拟网络的 DNS 服务器顺序，请从列表中删除 DNS 服务器，然后按客户希望的顺序重新添加这些服务器。 DNS 支持“CIA”安全三因素的可用性方面。

### <a name="610-azure-dns"></a>6.10 Azure DNS
[域名系统](https://technet.microsoft.com/library/bb629410.aspx)或 DNS 负责将网站或服务名称转换（或解析）为它的 IP 地址。 [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) 是 DNS 域的托管服务，它使用 Microsoft Azure 基础结构提供名称解析。 通过在 Azure 中托管你的域，你可以使用与其他 Azure 服务相同的凭据、API、工具和计费来管理你的 DNS 记录。 DNS 支持“CIA”安全三因素的可用性方面。
### <a name="611-log-analytics-nsgs"></a>6.11 Log Analytics NSG
可以为 NSG 启用以下诊断日志类别：
-    事件：包含根据 MAC 地址向 VM 和实例角色应用的 NSG 规则条目。 每隔 60 秒收集一次这些规则的状态。

-    规则计数器：包含应用每个 NSG 规则以拒绝或允许流量的次数的条目。

### <a name="612-azure-security-center"></a>6.12 Azure 安全中心
安全中心可帮助预防、检测和响应威胁，同时提高对 Azure 资源安全性的可见性和控制力度。 它提供对 Azure 订阅的集成安全监视和策略管理，帮助检测可能被忽略的威胁，且适用于广泛的安全解决方案生态系统。 网络建议围绕防火墙和网络安全组，配置入站流量规则等。

可用的网络建议如下：

-    [添加下一代防火墙](https://docs.microsoft.com/azure/security-center/security-center-add-next-generation-firewall)：建议从 Microsoft 合作伙伴添加下一代防火墙 (NGFW)，以增强安全保护

-    [仅通过 NGFW 路由流量](https://docs.microsoft.com/azure/security-center/security-center-add-next-generation-firewall#route-traffic-through-ngfw-only)：建议配置通过 NGFW 强制将流量入站到 VM 的网络安全组 (NSG) 规则。

-    [在子网或虚拟机上启用网络安全组](https://docs.microsoft.com/azure/security-center/security-center-enable-network-security-groups)：建议在子网或 VM 上启用 NSG。

-    [通过面向 Internet 的终结点限制访问](https://docs.microsoft.com/azure/security-center/security-center-restrict-access-through-internet-facing-endpoints)：建议为 NSG 配置入站流量规则。


## <a name="70-compute"></a>7.0 计算

本部分提供了关于此区域中主要特性的其他信息以及有关这些功能的摘要信息。

### <a name="71-antimalware--antivirus"></a>7.1 反恶意软件和防病毒软件
借助 Azure IaaS，可以使用来自 Microsoft、Symantec、Trend Micro、McAfee 和 Kaspersky 等安全性供应商的反恶意软件，以保护虚拟机免受恶意文件、广告软件和其他威胁的侵害。 适用于 Azure 云服务和虚拟机的 [Microsoft 反恶意软件](https://docs.microsoft.com/azure/security/azure-security-antimalware)是一种保护功能，可帮助识别并删除病毒、间谍软件和其他恶意软件。 Microsoft 反恶意软件提供了已知恶意或不需要的软件试图安装自身或在 Azure 系统上运行时的可配置警报。 此外可以使用 Azure 安全中心部署 Microsoft 反恶意软件

### <a name="72-hardware-security-module"></a>7.2 硬件安全模块
加密和身份验证不会提高安全性，除非密钥本身受到保护。 通过将关键密码和密钥存储在 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) 中，可以简化此类密码和密钥的管理和保护。 Key Vault 可将用户密钥存储在已通过 FIPS 140-2 Level 2 标准认证的硬件安全模块 (HSM) 中。 用于备份或[透明数据加密](https://msdn.microsoft.com/library/bb934049.aspx)的 SQL Server 加密密钥均可存储在密钥保管库中，此外还可存储应用程序中的任意密钥或密码。 对这些受保护项的权限和访问权限通过 [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/) 进行管理。

### <a name="73-virtual-machine-backup"></a>7.3 虚拟机备份
[Azure 备份](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup)是一种解决方案，无需资本投资便可保护应用程序数据，最大限度降低运营成本。 应用程序错误可能损坏数据，人为错误可能将 bug 引入应用程序，从而导致安全问题。 借助 Azure 备份，可以保护运行 Windows 和 Linux 的虚拟机。

### <a name="74-azure-site-recovery"></a>7.4 Azure Site Recovery
组织的[业务连续性/灾难恢复 (BCDR)](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) 策略的其中一个重要部分是，找出在发生计划内和计划外的中断时让企业工作负荷和应用保持启动并运行的方法。 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) 可帮助协调工作负荷和应用的复制、故障转移及恢复，因此能够在主要位置发生故障时通过辅助位置来提供工作负荷和应用。

### <a name="75-sql-vm-tde"></a>7.5 SQL VM TDE
SQL Server 加密功能包括[透明数据加密 (TDE)](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault)和列级加密 (CLE)。 这种加密形式要求客户管理和存储用于加密的加密密钥。

Azure 密钥保管库 (AKV) 服务专用于在一个高度可用的安全位置改进这些密钥的安全性和管理。 SQL Server 连接器使 SQL Server 能够使用 Azure Key Vault 中的这些密钥。

如果在本地计算机上运行 SQL Server，请按照此处提供的步骤通过本地 SQL Server 计算机访问 Azure Key Vault。 但对于 Azure VM 中的 SQL Server，可以使用 Azure Key Vault 集成功能节省时间。 通过使用几个 Azure PowerShell cmdlet 来启用此功能，可以自动为 SQL VM 进行必要的配置以便访问密钥保管库。

### <a name="76-vm-disk-encryption"></a>7.6 VM 磁盘加密
[Azure 磁盘加密](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)是用于加密 Windows 和 Linux IaaS 虚拟机磁盘的新功能。 它应用 Windows 的行业标准 BitLocker 功能和 Linux 的 DM-Crypt 功能，为 OS 和数据磁盘提供卷加密。 该解决方案与 Azure Key Vault 集成，帮助用户控制和管理 Key Vault 订阅中的磁盘加密密钥和机密。 此解决方案还可确保虚拟机磁盘上的所有数据在 Azure 存储空间中静态加密。

### <a name="77-virtual-networking"></a>7.7 虚拟网络
虚拟机需要网络连接。 若要支持该要求，Azure 要求将虚拟机连接到 Azure 虚拟网络。 Azure 虚拟网络是一个构建于物理 Azure 网络结构之上的逻辑构造。 每个逻辑[ Azure 虚拟网络](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)都独立于所有其他 Azure 虚拟网络。 这种隔离可帮助确保部署中的网络流量对于其他 Microsoft Azure 客户不可访问。

### <a name="78-patch-updates"></a>7.8 修补程序更新
修补程序更新可以减少必须在企业中部署的软件更新数目并提高监视符合性的能力，从而提供查找及修复潜在问题的基础并简化软件更新管理过程。

### <a name="79-security-policy-management-and-reporting"></a>7.9 安全策略管理和报告
[Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)帮助预防、检测和响应威胁，同时提高 Azure 资源安全性的可见性和控制力度。 它提供对 Azure 订阅的集成安全监视和策略管理，帮助检测可能被忽略的威胁，且适用于广泛的安全解决方案生态系统。

### <a name="710-azure-security-center"></a>7.10 Azure 安全中心
安全中心有助于预防、检测和响应威胁，同时增加 Azure 资源的可见性和安全可控性。 它提供 Azure 订阅之间的集成安全监视和策略管理，帮助检测可能被忽略的威胁，且适用于广泛的安全解决方案生态系统。

## <a name="80-identify-and-access-management"></a>8.0 标识和访问管理

保护系统、应用程序和以基于标识的访问控制开始的数据。 Microsoft 企业产品和服务内置的标识和访问管理功能有助于保护组织和个人信息免受未经授权的访问，同时向合法用户提供随时随地访问权限。

### <a name="81-secure-identity"></a>8.1 安全标识
Microsoft 在其产品和服务中使用多种安全实践和技术来管理标识和访问权限。
-    [多重身份验证](https://azure.microsoft.com/services/multi-factor-authentication/)要求用户在本地和云中使用多种方法进行访问。 它提供强大的身份验证和一系列简单的验证选项，同时满足用户对简单登录过程的需求。

-    [Microsoft Authenticator ](https://aka.ms/authenticator) 提供了一种用户友好型多重身份验证体验，它可与 Microsoft Azure Active Directory 和 Microsoft 帐户兼容，并支持可穿戴设备和基于指纹的批准。

-    [强制实施密码策略](https://azure.microsoft.com/documentation/articles/active-directory-passwords-policy/)通过强制执行长度和复杂性要求、强制定期轮换和身份验证尝试失败后的帐户锁定来提高传统密码的安全性。

-    [基于令牌的身份验证](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/)通过 Active Directory 联合身份验证服务 (AD FS) 或第三方安全令牌系统启用身份验证。

-    [基于角色的访问控制 (RBAC)](https://azure.microsoft.com/documentation/articles/role-based-access-built-in-roles/) 能够根据用户分配的角色来授予访问权限，从而轻松为用户仅提供执行作业所需的访问量。 可以根据组织的业务模型和风险允许范围自定义 RBAC。

-    [集成标识管理（混合标识）](https://azure.microsoft.com/documentation/articles/active-directory-hybrid-identity-design-considerations-overview/)能够保持对用户在内部数据中心和云平台中的访问控制，并为所有资源的身份验证和授权创建单个用户标识。

### <a name="82-secure-apps-and-data"></a>8.2 保护应用和资源
[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 是综合性的标识和访问管理云解决方案，可帮助确保安全访问站点和云中的应用程序数据，并简化对用户和组的管理。 它结合了核心目录服务、高级 Identity Governance、安全性以及应用程序访问管理，使开发人员可以轻松在其应用中构建基于策略的标识管理。 若要增强 Azure Active Directory，可以使用 Azure Active Directory Basic、Premium P1、和 Premium P2 版添加付费功能。

| 免费/常用功能     | 基本功能    |高级 P1 功能 |高级 P2 功能 | Azure Active Directory Join – 仅适用于 Windows 10 的相关功能|
| :------------- | :------------- |:------------- |:------------- |:------------- |
|     [Directory 对象](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-editions#directory-objects)，[用户/组管理（添加/更新/删除）/基于用户的预配，设备注册](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-editions#usergroup-management-addupdatedelete-user-based-provisioning-device-registration)，[单一登录 (SSO)](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-editions#single-sign-on-sso)，[云用户的自助密码更改](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-editions#self-service-password-change-for-cloud-users)，[连接（将本地目录扩展到 Azure Active Directory 的同步引擎）](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-editions#connect-sync-engine-that-extends-on-premises-directories-to-azure-active-directory)，[安全/使用情况报告](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-editions#securityusage-reports)       |     [基于组的访问管理/预配](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-editions#group-based-access-managementprovisioning)，[云用户的自助密码重置](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-editions#self-service-password-reset-for-cloud-users)，[公司品牌（登录页/访问面板自定义）](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-editions#company-branding-logon-pagesaccess-panel-customization)，[应用程序代理](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-editions#application-proxy)，[SLA 99.9%](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-editions#sla-999) |  [自助组和应用管理/自助应用程序添加件/动态组](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-editions#self-service-group)，[通过本地写回实现自助密码重置/更改/解锁](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-editions#self-service-password-resetchangeunlock-with-on-premises-write-back)，[多重身份验证（云和本地（MFA 服务器））](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-editions#multi-factor-authentication-cloud-and-on-premises-mfa-server)，[MIM CAL + MIM 服务器](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-editions#mim-cal-mim-server)，[Cloud App Discovery](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-editions#cloud-app-discovery)，[连接运行状况](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-editions#connect-health)，[组帐户的自动密码滚动更新](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-editions#automatic-password-rollover-for-group-accounts)|     [标识保护](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-identityprotection)，[Privileged Identity Management](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-privileged-identity-management-configure)|    [让设备加入 Azure AD、Desktop SSO、Microsoft Passport for Azure AD 和 Administrator Bitlocker 恢复](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-editions#join-a-device-to-azure-ad-desktop-sso-microsoft-passport-for-azure-ad-administrator-bitlocker-recovery)，[MDM 自动注册、自助 Bitlocker 恢复、通过 Azure AD Join 将其他本地管理员加入 Windows 10 设备](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-editions#mdm-auto-enrollment)|


- [Cloud App Discovery](https://docs.microsoft.com/azure/active-directory/active-directory-cloudappdiscovery-whatis) 是 Azure Active Directory 的一项高级功能，能够识别组织中的人员所使用的云应用程序。

- [Azure Active Directory Identity Protection](https://azure.microsoft.com/documentation/articles/active-directory-identityprotection/) 是一种安全服务，它使用 Azure Active Directory 异常检测功能对风险事件和可能影响组织标识的潜在漏洞提供综合视图。

- [Azure Active Directory 域服务](https://azure.microsoft.com/services/active-directory-ds/)让用户可以将 Active VM 加入一个域，且无需部署域控制器。 用户可使用他们的企业 Active Directory 凭证登录这些 VM，且可以无缝访问资源。

- [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) 是一个高度可用的全局性标识管理服务，该服务适用于面向用户且可通过伸缩来处理数以亿计标识的应用程序，并可跨移动平台和 Web 平台集成。 客户可以通过使用现有社交媒体帐户的自定义体验登录所有应用，也可以创建新的独立凭据。

- [Azure Active Directory B2B 协作](https://aka.ms/aad-b2b-collaboration)是一种安全的合作伙伴集成解决方案，可让合作伙伴使用其自行管理的标识有选择性地访问你的企业应用程序和数据，为跨公司合作关系提供支持。

- [Azure Active Directory Join](https://azure.microsoft.com/documentation/articles/active-directory-azureadjoin-overview/) 可以将云功能扩展到 Windows 10 设备进行集中管理。 它使用户可以通过 Azure Active Directory 连接到企业或组织云，并简化对应用和资源的访问。

- [Azure Active Directory 应用程序代理](https://azure.microsoft.com/documentation/articles/active-directory-application-proxy-get-started/)为本地托管的 Web 应用程序提供 SSO 和安全远程访问。

## <a name="next-steps"></a>后续步骤
- [Microsoft Azure 安全入门](https://docs.microsoft.com/azure/security/azure-security-getting-started)

可以用来确保 Azure 中服务和数据安全性的 Azure 服务和功能

- [Azure 安全中心](https://azure.microsoft.com/services/security-center/)

预防、检测和响应威胁，同时增加 Azure 资源安全性的可见性以及控制力度

- [在 Azure 安全中心进行安全运行状况监视](https://docs.microsoft.com/azure/security-center/security-center-monitoring)

Azure 安全中心的监视功能，用于监视策略符合性。


