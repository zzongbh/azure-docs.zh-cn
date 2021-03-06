 ---
标题： 使用情况 API 相关常见问题 |Microsoft 文档说明： Azure 堆栈的列表米，与 Azure 使用情况 API、 使用时间和报告的时间，错误代码进行比较。
服务： azure 堆栈 documentationcenter: ' 作者： mattbriggs manager: femila 编辑器: '

ms.assetid: 847f18b2-49a9-4931-9c09-9374e932a071 ms.service: azure-stack ms.workload: na ms.tgt_pltfrm: na ms.devlang: na ms.topic: article ms.date: 03/09/2018 ms.author: mabrigg ms.reviewer: alfredop

---
# <a name="frequently-asked-questions-in-azure-stack-usage-api"></a>在 Azure 堆栈使用情况 API 的常见问题
本文回答了一些有关 Azure 堆栈使用情况 API 的常见问题。

## <a name="what-meter-ids-can-i-see"></a>可以看到哪些测定仪 Id？
以下资源提供程序的报告使用率：

| **资源提供程序** | **电表 ID** | **测定仪名称** | **单位** | 其他信息 |
| --- | --- | --- | --- | --- |
| **网络** |F271A8A388C44D93956A063E1D2FA80B |静态 IP 地址使用情况 |IP 地址| 使用计数的 IP 地址。 如果调用使用情况 API 每日粒度，计量器返回 IP 地址小时数的乘积。 |
| |9E2739BA86744796B465F64674B822BA |动态 IP 地址使用情况 |IP 地址| 使用计数的 IP 地址。 如果调用使用情况 API 每日粒度，计量器返回 IP 地址小时数的乘积。 |
| **存储** |B4438D5D-453B-4EE1-B42A-DC72E377F1E4 |TableCapacity |GB\*小时数 |表占用的总容量。 |
| |B5C15376-6C94-4FDD-B655-1A69D138ACA3 |PageBlobCapacity |GB\*小时数 |由页 blob 的总容量。 |
| |B03C6AE7-B080-4BFA-84A3-22C800F315C6 |QueueCapacity |GB\*小时数 |使用的队列的总容量。 |
| |09F8879E-87E9-4305-A572-4B7BE209F857 |BlockBlobCapacity |GB\*小时数 |使用块 blob 的总容量。 |
| |B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90 |TableTransactions |在 10 中，000's年请求计数 |表服务请求 （以 10,000 计）。 |
| |50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D |TableDataTransIn |以 gb 为单位的传入数据 |表服务数据流入以 gb 为单位。 |
| |1B8C1DEC-EE42-414B-AA36-6229CF199370 |TableDataTransOut |以 gb 为单位的出口 |以 gb 为单位的表服务数据流出量 |
| |43DAF82B-4618-444A-B994-40C23F7CD438 |BlobTransactions |在 10 中，000's年请求计数 |Blob 服务请求 （以 10,000 计）。 |
| |9764F92C-E44A-498E-8DC1-AAD66587A810 |BlobDataTransIn |以 gb 为单位的传入数据 |以 gb 为单位的 blob 服务数据流入。 |
| |3023FEF4-ECA5-4D7B-87B3-CFBC061931E8 |BlobDataTransOut |以 gb 为单位的出口 |Blob 服务以 gb 为单位的数据流出量。 |
| |EB43DD12-1AA6-4C4B-872C-FAF15A6785EA |QueueTransactions |在 10 中，000's年请求计数 |队列服务请求 （以 10,000 计）。 |
| |E518E809-E369-4A45-9274-2017B29FFF25 |QueueDataTransIn |以 gb 为单位的传入数据 |以 gb 为单位的队列服务数据流入。 |
| |DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2 |QueueDataTransOut |以 gb 为单位的出口 |以 gb 为单位的队列服务数据流出量 |
| **Sql RP**            | CBCFEF9A-B91F-4597-A4D3-01FE334BED82 | DatabaseSizeHourSqlMeter   | MB\*小时数   | 在创建的总 DB 容量。 如果调用使用情况 API 每日粒度，计量器返回 MB 的小时数的乘积。 |
| **MySql RP**          | E6D8CFCD-7734-495E-B1CC-5AB0B9C24BD3 | DatabaseSizeHourMySqlMeter | MB\*小时数    | 在创建的总 DB 容量。 如果调用使用情况 API 每日粒度，计量器返回 MB 的小时数的乘积。 |
| **计算** |FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5 |基本 VM 大小小时数 |虚拟核心小时数 | 虚拟内核数乘以 VM 运行的小时数。 |
| |9CD92D4C-BAFD-4492-B278-BEDC2DE8232A |Windows VM 大小小时数 |虚拟核心小时数 | 虚拟内核数乘以 VM 运行的小时数。 |
| |6DAB500F-A4FD-49C4-956D-229BB9C8C793 |VM 大小小时数 |VM 小时数 |捕获基类和 Windows 的 VM。 不进行调整核心数。 |
| **Key Vault** |EBF13B9F-B3EA-46FE-BF54-396E93D48AB4 |密钥保管库事务 | 在 10 中，000's年请求计数| 收到的密钥保管库数据平面的 REST API 请求数。 |
| |2C354225-B2FE-42E5-AD89-14F0EA302C87 |高级的密钥事务 | 10k 次交易|     RSA 3 K/4 K，ECC 密钥的事务。 （预览版）。 |
| **应用服务** | 190C935E-9ADA-48FF-9AB8-56EA1CF9ADAA | 应用服务 | 虚拟核心小时数 | 用于运行应用程序服务的虚拟内核数。 注意： Microsoft 使用此指示器要收取 Azure 堆栈上的应用程序服务。 云服务提供商可以使用其他 App Service 米 （见下面） 为其租户计算使用情况。 |
|  | 67CC4AFC-0691-48E1-A4B8-D744D1FEDBDE | 函数请求 | 10 个请求 | （每 10 执行） 的请求执行的总数。 执行将计算每次函数运行以响应某个事件，或由某个绑定触发。 |
|  | D1D04836-075C-4F27-BF65-0A1130EC60ED | 功能-计算 | GB-s | 资源消耗以秒为单位千兆字节 (GB/s)。 **观察到的资源消耗**计算通过将以 gb 为单位的平均内存大小乘以以毫秒为单位执行函数所花的时间。 函数使用的内存大小的单位由舍入到最接近的 128 MB，直到达到最大内存大小为 1536 MB，其中计算通过舍入的执行时间最接近的 1 ms。 最小执行时间和内存中的准备单个函数执行分别为 100 毫秒，128 mb。 |
|  | 957E9F36-2C14-45A1-B6A1-1723EF71A01D | 共享的 App Service 小时数 | 1 小时	 | 每小时使用情况的 App Service 计划的分片。 计划计量基于每个应用程序。 |
|  | 539CDEC7-B4F5-49F6-AAC4-1F15CFF0EDA9 | 免费 App Service 小时数 | 1 小时	 | 每小时使用情况的免费 App Service 计划。 计划计量基于每个应用程序。 |
|  | 88039D51-A206-3A89-E9DE-C5117E2D10A6 | 小型标准的 App Service 小时数 | 1 小时	 | 根据大小和实例数的计算。 |
|  | 83A2A13E-4788-78DD-5D55-2831B68ED825 | 标准的中等规模 App Service 小时数 | 1 小时	 | 根据大小和实例数的计算。 |
|  | 1083B9DB-E9BB-24BE-A5E9-D6FDD0DDEFE6 | 大型标准的 App Service 小时数 | 1 小时	 | 根据大小和实例数的计算。 |
|  | *自定义的辅助角色层* | 自定义的辅助角色层 | 小时 | 确定性测定仪 ID 创建取决于 SKU 和自定义的辅助角色层名称。 此测定仪 ID 是唯一的每个自定义的辅助角色层。 |
|  | 264ACB47-AD38-47F8-ADD3-47F01DC4F473 | SNI SSL | 每个 SNI SSL 绑定 | App Service 支持两种类型的 SSL 连接： 服务器名称指示 (SNI) SSL 和 IP 地址的 SSL 连接。 基于 SNI 的 SSL 适用于新式浏览器，而基于 IP 的 SSL 适用于所有浏览器。 |
|  | 60B42D72-DC1C-472C-9895-6C516277EDB4 | IP SSL | 每个 IP 基于的 SSL 绑定 | App Service 支持两种类型的 SSL 连接： 服务器名称指示 (SNI) SSL 和 IP 地址的 SSL 连接。 基于 SNI 的 SSL 适用于新式浏览器，而基于 IP 的 SSL 适用于所有浏览器。 |
|  | 73215A6C-FA54-4284-B9C1-7E8EC871CC5B | Web 进程 |  | 计算每个每小时的活动站点。 |
|  | 5887D39B-0253-4E12-83C7-03E1A93DFFD9 | 外部出口带宽 | GB | 总传入的请求响应字节数 + 总传出请求字节 + 总传入 FTP 请求响应字节 + 总传入的 web 部署请求响应字节数。 |

## <a name="how-do-the-azure-stack-usage-apis-compare-to-the-azure-usage-apihttpsmsdnmicrosoftcomlibraryazure1ea5b323-54bb-423d-916f-190de96c6a3c-currently-in-public-preview"></a>如何实现 Api 与 Azure 堆栈使用[Azure 使用情况 API](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) （当前在公共预览版）？
* 租户使用情况 API 是与 Azure API，有一个例外一致： *showDetails*标志目前不支持 Azure 堆栈中。
* 提供程序使用情况 API 仅适用于 Azure 堆栈。
* 目前， [RateCard API](https://msdn.microsoft.com/en-us/library/azure/mt219004.aspx) ，它是可用在 Azure 中不可用 Azure 堆栈中。

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>使用时间和报告的时间之间的区别是什么？
使用情况数据报告具有两个主要的时间值：

* **报告的时间**。 使用事件时进入使用情况系统的时间
* **使用时间**。 当 Azure 堆栈资源已使用的时间

特定使用事件，可能会使用时间和报告时间看到值之间存在差异。 延迟可能长达在任何环境中的多个小时。

目前，可以仅可由查询*报告时间*。

## <a name="what-do-these-usage-api-error-codes-mean"></a>这些使用情况 API 错误代码是什么意思？
| **HTTP 状态代码** | **错误代码** | **说明** |
| --- | --- | --- |
| 400/错误的请求 |*NoApiVersion* |*Api 版本*缺少查询参数，则。 |
| 400/错误的请求 |*InvalidProperty* |属性缺失或具有无效值。 响应正文中的错误代码中的消息标识缺少的属性。 |
| 400/错误的请求 |*RequestEndTimeIsInFuture* |值*ReportedEndTime*在将来。 为此参数在将来不允许值。 |
| 400/错误的请求 |*SubscriberIdIsNotDirectTenant* |提供程序 API 调用已使用不是有效的租户的调用方的订阅 ID。 |
| 400/错误的请求 |*SubscriptionIdMissingInRequest* |找不到调用方的订阅 ID。 |
| 400/错误的请求 |*InvalidAggregationGranularity* |已请求无效的聚合粒度。 有效值为每日和每小时。 |
| 503 |*ServiceUnavailable* |由于服务正忙于或调用将被阻止出现可重试错误。 |

## <a name="next-steps"></a>后续步骤
[客户计费和 Azure 堆栈中的 chargeback](azure-stack-billing-and-chargeback.md)

[提供程序资源使用情况 API](azure-stack-provider-resource-api.md)

[租户资源使用情况 API](azure-stack-tenant-resource-usage-api.md)
