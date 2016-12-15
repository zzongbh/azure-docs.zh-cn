---
title: "Azure 中继混合连接协议 | Microsoft 文档"
description: "Azure 中继混合连接协议指南。"
services: service-bus-relay
documentationcenter: na
author: clemensv
manager: timlt
editor: 
ms.assetid: 149f980c-3702-4805-8069-5321275bc3e8
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 16071ba6c99e41af9fe7614fcc3254cd7e786e89
ms.openlocfilehash: 497f54903bef564bab687103a763c7a7b58da074


---
# <a name="azure-relay-hybrid-connections-protocol"></a>Azure 中继混合连接协议
Azure 中继是 Azure 服务总线平台最重要的功能支柱之一。 该中继的新“混合连接”功能是在 HTTP 和 WebSocket 基础上的一个安全开放协议的演化版。 它取代了之前基于专用协议构建的名为“BizTalk 服务”的功能。 将混合连接集成到 Azure App Service 并不影响原有的运行方式。

“混合连接”可在两个联网的应用程序之间启用双向二进制流通信，由此这两方或其中一方可驻留在 NAT 或防火墙之后。 本文介绍了如何与混合连接中继的客户端进行交互，以连接侦听器和发送方角色中的客户端，以及侦听器如何接受新的连接。

## <a name="interaction-model"></a>交互模型
混合连接中继将通过提供双方都可在自身网络发现并连接到的 Azure 云中的集合点来连接两方。 该集合点就是在本文和其他文档、API 及 Azure 门户中提及的“混合连接”。 混合连接服务终结点在本文其余部分将被称为“服务”。 该交互模型倾向于使用由其他网络 API 创建的术语：

首先一个是侦听器，指示处理传入连接的准备情况，然后在这些连接到达时进行接受。 在另一端，是一个连接客户端，连接至侦听器，希望该连接被接受以建立双向通信路径。
“连接”、“侦听”和“接受”与将要在大多数套接字 API 上看到的术语相同。

任何中继通信模型都会使其中一方生成针对服务终结点的出站连接，这使得“侦听器”也被常说成“客户端”，而导致其他术语名称相同；因此，我们用于混合连接的准确术语如下所述：

连接两端上的程序称为“客户端”，因为相对于服务它们是客户端。 等待和接受连接的客户端是“侦听器”，或者称其在“侦听器角色”中。 通过服务向侦听器启动新连接的客户端称为“发送方”，或者称其在“发送方角色”中。

### <a name="listener-interactions"></a>侦听器交互
侦听器与服务之间存在四种交互，所有通信详细信息都已在本文后续参考部分中予以说明。

#### <a name="listen"></a>侦听
侦听器会创建一个出站 Web 套接字连接，指示对服务的准备情况，即侦听器准备接受连接的情况。 连接握手包含在中继命名空间中配置的混合连接名称，以及授予对该名称“侦听”权限的安全令牌。
服务接受 Web 套接字后，注册完成并且建立的 Web 套接字作为“控制通道”保持活动状态，以支持所有后续交互。 在一个混合连接上服务最多可允许 25 个并行侦听器。 如果有两个或更多活动的侦听器，则会将传入连接以随机顺序均衡分布在这些侦听器上，但不保证会平均分配。

#### <a name="accept"></a>接受
发送方打开服务上的新连接时，服务会选择并通知混合连接上活动的侦听器之一。 该通知将作为 JSON 消息（其中包含侦听器必须连接到的 Web 套接字的终结点 URL，以接受连接），通过开放的控制通道发送至侦听器。

该 URL 可以且必须直接由侦听器使用，无需其他作业。编码信息仅在短时间内有效，基本上为发送方愿意等待的端到端创建连接的时间，但是最长为 30 秒。 该 URL 只能用于一次成功的连接尝试。 使用集合 URL 建立 Web 套接字连接后，该 Web 套接字上的所有后续活动都将从发送方中继并中继到该发送方，无需服务任何干预或转译。

#### <a name="renew"></a>续订
注册侦听器和维护控制通道必须使用的安全令牌可能会在侦听器活动期间到期。 令牌到期不会影响正在进行的连接，但会导致服务在到期时或到期不久后终止控制通道。 “续订”操作是一个 JSON 消息，侦听器可以发送它来替换与控制通道关联的令牌，这样即可延长控制通道的维护时间。

#### <a name="ping"></a>Ping
如果控制通道长时间处于空闲，通道上的中介，例如负载平衡器或 NAT 可能会终止 TCP 连接。 “ping”操作可通过在该通道上发送少量数据，提醒网络路由上的所有人该连接处于活动状态，以此来避免上述终止情况。 如果 ping 失败，则控制通道应视为不可用，并且侦听器应重新进行连接。

### <a name="sender-interaction"></a>发送方交互
发送方与服务之间仅存在一种交互，即发送方进行连接。

#### <a name="connect"></a>连接
“连接”操作会打开服务上的 Web 套接字，提供混合连接的名称和（可选，但是默认为必须）授予查询字符串中“发送”权限的安全令牌。 然后，服务会按照之前所述的方式与侦听器进行交互，并使侦听器创建一个将由该 Web 套接字连接的集合连接。 Web 套接字被接受后，该 Web 套接字上的所有后续交互由此都将使用已连接的侦听器。

### <a name="interaction-summary"></a>交互摘要
此交互模型的结果是，发送方客户端使用“干净”的 Web 套接字（已连接至侦听器且无需进一步的“报头”或准备）离开握手。 这使得几乎任何现有 Web 套接字客户端实现，只需通过将正确构造的 URL 提供到其 Web 套接字客户端层，即可立即使用混合连接服务。

侦听器通过接受交互获取的集合连接 Web 套接字同样也是干净的，并且可被传递至任何现有 Web 套接字服务器实现，使用一些最小限度的额外抽象层，区分其框架的本地网络侦听器上的“接受”操作和混合连接远程“接受”操作。

## <a name="protocol-reference"></a>协议参考

该部分介绍上述协议交互的详细信息。

所有 Web 套接字连接都作为 HTTPS 1.1 的升级在端口 443 上生成，此操作通常被一些 Web 套接字框架或 API 抽象化。 此处的说明保持实现中立，不指示特定框架。

### <a name="listener-protocol"></a>侦听器协议
侦听器协议由两个连接动作和三个消息操作组成。

#### <a name="listener-control-channel-connection"></a>侦听器控制通道连接
控制通道通过创建针对以下内容的 Web 套接字连接打开：

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...[&sb-hc-id=...]&sb-hc-token=...
```

`namespace-address` 是托管混合连接的 Azure 中继命名空间的完全限定域名，通常格式为 `{myname}.servicebus.windows.net`。

查询字符串参数选项如下所示。

| 参数 | 必选 | 说明 |
| --- | --- | --- |
| sb-hc-action |是 |对于侦听器角色，该参数必须是 **sb-hc-action=listen** |
| {path} |是 |要注册该侦听器的预配置混合连接的 URL 编码命名空间路径。 此表达式追加至固定的 `$hc/` 路径部分。 |
| sb-hc-token |是\* |侦听器必须为命名空间或混合连接提供有效的 URL 编码的服务总线共享访问令牌，以授予“**侦听**”权限。 |
| sb-hc-id |否 |该客户端提供的可选 ID 可实现端到端诊断跟踪。 |

如果由于混合连接路径未注册、令牌无效或丢失或其他一些错误，导致 Web 套接字连接失败，则会使用常规的 HTTP 1.1 状态反馈模型提供错误反馈。 状态说明将包含可传达至 Azure 支持部分的错误跟踪 ID：

| 代码 | 错误 | 说明 |
| --- | --- | --- |
| 404 |未找到 |混合连接“**路径**”无效或基 URL 格式不正确。 |
| 401 |未授权 |安全令牌丢失或格式不正确或无效。 |
| 403 |禁止 |安全令牌对此操作的此路径无效。 |
| 500 |内部错误 |服务内部出错。 |

如果服务在初始设置 Web 套接字连接后有意将其关闭，则执行此操作的原因将会使用相应的 Web 套接字协议错误代码，连同也包含跟踪 ID 的描述性错误消息进行传达。 服务在未出现错误状况的情况下将不会关闭控制通道。 任何干净关闭都由客户端控制。

| WS 状态 | 说明 |
| --- | --- |
| 1001 |混合连接路径已删除或禁用。 |
| 1008 |安全令牌已到期，因此违背了授权策略。 |
| 1011 |服务内部出错。 |

### <a name="accept-handshake"></a>接受握手
接受通知作为 Web 套接字文本框中的 JSON 消息，由服务通过之前建立的控制通道发送至侦听器。 不会对此消息进行任何回复。

该消息包含名为“accept”的 JSON 对象，此时该对象定义以下属性：

* **address** – 用于创建服务的 Web 套接字 URL 字符串，以接受传入连接。
* **id** – 该连接的唯一标识符。 如果该 ID 由发送方客户端提供，则是发送方提供的值，否则为系统生成的值。
* **connectHeaders** – 发送方向中继终结点提供的所有 HTTP 头，其中也包括 Sec-WebSocket-Protocol 和 Sec-WebSocket-Extensions 头。

#### <a name="accept-message"></a>接受消息
``` JSON
{                                                           
    "accept" : {
        "address" : "wss://168.61.148.205:443/$hc/{path}?..."    
        "id" : "4cb542c3-047a-4d40-a19f-bdc66441e736",  
        "connectHeaders" : {                                         
            "Host" : "...",                                                
            "Sec-WebSocket-Protocol" : "...",                              
            "Sec-WebSocket-Extensions" : "..."                             
        }                                                            
     }                                                         
}
```

侦听器使用 JSON 消息中提供的地址 URL，创建接受或拒绝发送方套接字的 Web 套接字。

#### <a name="accepting-the-socket"></a>接受套接字
若接受，侦听器会创建对已提供地址的 Web 套接字连接。

注意，在预览版中，地址 URI 可能会使用裸 IP 地址，因此，服务器提供的 TLS 证书将无法对该地址进行验证。
此问题将在预览版中进行纠正。

如果“接受”消息包含“Sec-WebSocket-Protocol”头，则在侦听器支持该协议且在创建 Web 套接字时设置了该头的情况下，将只会接受 Web 套接字。

这同样适用于“Sec-WebSocket-Extensions”头。 如果框架支持扩展，则框架应针对扩展将头设置为：所需的“Sec-WebSocket-Extensions”握手的*服务器*端回复。

URL 必须原样使用，用于创建接受套接字，但是要包含以下参数：

| 参数 | 必选 | 说明 |
| --- | --- | --- |
| sb-hc-action |是 |若要接受套接字，该参数必须为 `sb-hc-action=accept` |
| {path} |是 |（请参阅下文） |
| sb-hc-id |否 |请参阅上述的 **id** 说明。 |

`{path}` 是要注册此侦听器的预配置混合连接的 URL 编码命名空间路径。 此表达式追加至固定的 `$hc/` 路径部分。 

路径表达式可以使用后缀和查询字符串表达式进行扩展，在分隔正斜杠之后跟随注册名称。 这样发送方客户端就可以在无法包含 HTTP 头时将调度参数传递至接受侦听器。 侦听器框架将可从路径中分析出固定路径部分和注册名称，并向应用程序提供其余部分（可能不含任何带“sb-”前缀的查询字符串参数），以决定是否接受连接。

有关详细信息，请参阅后面的“发送方协议”部分。

如果出现错误，服务可能会提供以下回复：

| 代码 | 错误 | 说明 |
| --- | --- | --- |
| 403 |禁止 |此 URL 无效。 |
| 500 |内部错误 |服务内部出错 |

建立连接后，服务器会在发送方 Web 套接字关闭后或在以下状态下关闭 Web 套接字。

| WS 状态 | 说明 |
| --- | --- |
| 1001 |发送方客户端关闭连接。 |
| 1001 |混合连接路径已删除或禁用。 |
| 1008 |安全令牌已到期，因此违背了授权策略。 |
| 1011 |服务内部出错。 |

#### <a name="rejecting-the-socket"></a>拒绝套接字
在检查“接受”消息后拒绝套接字，将需要一个相似的握手，以使传达拒绝原因的状态代码和状态说明可以返回至发送方。

此处的协议设置选项是使用 Web 套接字握手（用于在定义的错误状态下结束），以便侦听器客户端实现可以继续依赖 Web 套接字客户端，而无需使用其他的裸 HTTP 客户端。

若要拒绝套接字，客户端将使用“接受”消息中的地址 URI 并将两个查询字符串参数追加到其中：

| Param | 必选 | 说明 |
| --- | --- | --- |
| statusCode |是 |数值型 HTTP 状态代码。 |
| statusDescription |是 |可人工读取的拒绝原因。 |

生成的 URI 然后用于建立 WebSocket 连接；再强调一次，预览版的 TLS 证书可能不匹配地址，因此可能需要禁用验证。

正确完成后，该握手将有意失败，并出现 HTTP 错误代码 410，因为尚未创建任何 Web 套接字。 如果出现错误，可使用以下选项：

| 代码 | 错误 | 说明 |
| --- | --- | --- |
| 403 |禁止 |此 URL 无效。 |
| 500 |内部错误 |服务内部出错。 |

### <a name="listener-token-renewal"></a>侦听器令牌续订
侦听器令牌即将到期时，可以通过已创建的控制通道向服务发送文本框消息来替换令牌。 消息中包含名为“renewToken”的 JSON 对象，此时该对象定义以下属性：

* **token** – 命名空间或混合连接的有效 URL 编码的服务总线共享访问令牌，可授予“**侦听**”权限。

#### <a name="renewtoken-message"></a>renewToken 消息
``` JSON
{                                                                                                                                                                        
    "renewToken" : {                                                                                                                                                      
        "token" : "SharedAccessSignature sr=http%3a%2f%2fcontoso.servicebus.windows.net%2fhyco%2f&amp;sig=XXXXXXXXXX%3d&amp;se=1471633754&amp;skn=SasKeyName"  
    }                                                                                                                                                                     
}
```

如果令牌验证失败，访问被拒绝，则云服务将关闭控制通道 Web 套接头，并显示错误，否则将不会有任何回复。

| WS 状态 | 说明 |
| --- | --- |
| 1008 |安全令牌已到期，因此违背了授权策略。 |

## <a name="sender-protocol"></a>发送方协议
发送方协议实际上与创建侦听器的方式相同。
其目标在于对端到端 Web 套接字最大化的透明。 要连接到的地址与侦听器的相同，但是“操作”不同且令牌需要不同的权限：

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...&sb-hc-id=...&sbc-hc-token=...
```

*namespace-address* 是托管混合连接的 Azure 中继命名空间的完全限定域名，通常格式为 `{myname}.servicebus.windows.net`。

请求可以包含任意其他 HTTP 头，包括应用程序定义的头。 所有提供的头将流向侦听器并且可在“接受”控制消息的“connectHeader”对象上找到。

查询字符串参数选项如下所示

| Param | 必需？ | 说明 |
| --- | --- | --- |
| sb-hc-action |是 |对于侦听器角色，该参数必须是 `action=connect`。 |
| {path} |是 |（请参阅下文） |
| sb-hc-token |是\* |侦听器必须为命名空间或混合连接提供有效的 URL 编码的服务总线共享访问令牌，以授予“**发送**”权限。 |
| sb-hc-id |否 |启用端到端诊断跟踪的可选 ID，在接受握手期间会将其提供至侦听器。 |

{path} 是要注册该侦听器的预配置混合连接的 URL 编码命名空间路径。 路径表达式可以使用后缀和查询字符串进行扩展，以进行其他通信。 如果混合连接注册在路径“hyco”下，则该路径表达式可以是 `hyco/suffix?param=value&...`，后面是此处定义的查询字符串参数。 完整的表达式可以如下所示：

```
wss://{namespace-address}/$hc/hyco/suffix?param=value&sb-hc-action=...[&sb-hc-id=...&]sbc-hc-token=...
```

路径表达式传递至“接受”控制消息包含的地址 URI 中的侦听器。

如果由于混合连接路径未注册、令牌无效或丢失或其他一些错误，导致 Web 套接字连接失败，则会使用常规的 HTTP 1.1 状态反馈模型提供错误反馈。 状态说明将包含可传达至 Azure 支持部分的错误跟踪 ID：

| 代码 | 错误 | 说明 |
| --- | --- | --- |
| 404 |未找到 |混合连接 `path` 无效或基 URL 格式不正确。 |
| 401 |未授权 |安全令牌丢失或格式不正确或无效。 |
| 403 |禁止 |安全令牌对此操作的此路径无效。 |
| 500 |内部错误 |服务内部出错。 |

如果服务在初始设置 Web 套接字连接后有意将其关闭，则执行此操作的原因将会使用相应的 Web 套接字协议错误代码，连同包含跟踪 ID 的描述性错误消息进行传达。

| WS 状态 | 说明 |
| --- | --- |
| 1000 |侦听器关闭套接字。 |
| 1001 |混合连接路径已删除或禁用。 |
| 1008 |安全令牌已到期，因此违背了授权策略。 |
| 1011 |服务内部出错。 |

## <a name="next-steps"></a>后续步骤
* [中继常见问题](relay-faq.md)
* [创建命名空间](relay-create-namespace-portal.md)
* [.NET 入门](relay-hybrid-connections-dotnet-get-started.md)
* [节点入门](relay-hybrid-connections-node-get-started.md)




<!--HONumber=Nov16_HO3-->

