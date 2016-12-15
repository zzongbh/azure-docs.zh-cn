---
title: "Azure 网络安全概述 | Microsoft 文档"
description: " 本文便于你了解 Microsoft Azure 在网络安全领域内必须要提供的内容。 我们提供了核心网络安全概念和要求的基本说明，以及 Azure 在每个领域内必须要提供的内容的相关信息。 "
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TomSh
ms.assetid: bedf411a-0781-47b9-9742-d524cf3dbfc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/18/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: f62d964dc5da9200a9f1944c00a4983e8d01b997


---
# <a name="azure-network-security-overview"></a>Azure 网络安全概述
Microsoft Azure 包括可靠的网络基础结构以支持你的应用程序和服务连接要求。 Azure 中的资源之间、本地资源与 Azure 托管的资源之间以及 Internet 与 Azure 之间都可能存在网络连接。

本文旨在让你更轻松地了解 Microsoft Azure 在网络安全领域内必须要提供的内容。 此处我们提供了核心网络安全概念和要求的基本说明。 我们还提供了 Azure 在每个领域内必须要提供的内容的相关信息。 另外，还有许多其他内容的链接，通过这些链接你将能够深入了解你所感兴趣的领域。

此 Azure 网络安全概述文章将重点介绍以下内容：

* Azure 网络
* 网络访问控制
* 安全远程访问和跨界连接
* 可用性
* 日志记录
* 名称解析
* DMZ 体系结构
* Azure 安全中心

## <a name="azure-networking"></a>Azure 网络
虚拟机需要网络连接。 若要支持该要求，Azure 要求将虚拟机连接到 Azure 虚拟网络。 Azure 虚拟网络是一个构建于物理 Azure 网络结构之上的逻辑构造。 每个逻辑 Azure 虚拟网络都独立于所有其他 Azure 虚拟网络。 这可帮助确保部署中的网络流量对于其他 Microsoft Azure 客户不可访问。

了解更多：

* [虚拟网络概述](../virtual-network/virtual-networks-overview.md)

## <a name="network-access-control"></a>网络访问控制
网络访问控制是限制 Azure 虚拟网络内特定设备或子网之间的连接的行为。 网络访问控制旨在确保你的虚拟机和服务仅对具有访问权限的用户和设备可以访问。 访问控制基于虚拟机或服务之间的连接的允许或拒绝决策。

Azure 支持多种类型的网络访问控制。 其中包括：

* 网络层控制
* 路由控制和强制隧道
* 虚拟网络安全设备

### <a name="network-layer-control"></a>网络层控制
任何安全部署都需要一定程度的网络访问控制。 网络访问控制旨在确保你的虚拟机以及在这些虚拟机上运行的网络服务可以仅与需要与其通信的其他联网设备进行通信，并且阻止所有其他连接尝试。

如果你需要基本的网络级别访问控制（基于 IP 地址和 TCP 或 UDP 协议），则可以使用网络安全组。 网络安全组 (NSG) 是基本的静态数据包筛选防火墙，它使你能够基于 [5 元组](https://www.techopedia.com/definition/28190/5-tuple)控制访问。 NSG 不提供应用程序层检查或经过身份验证的访问控制。

了解更多：

* [网络安全组](../virtual-network/virtual-networks-nsg.md)

### <a name="route-control-and-forced-tunneling"></a>路由控制和强制隧道
在 Azure 虚拟网络上控制路由行为的能力是关键的网络安全和访问控制功能。 如果路由配置不正确，你的虚拟机上托管的应用程序和服务可能会连接到你不希望其连接到的设备，其中包括潜在攻击者所拥有或操作的设备。

Azure 网络支持在 Azure 虚拟网络上为网络流量自定义路由行为的功能。 这样你便可以更改 Azure 虚拟网络中的默认路由表条目。 通过控制路由行为，可帮助你确保来自特定设备或一组设备的所有流量通过特定位置进入或离开你的 Azure 虚拟网络。

例如，你的 Azure 虚拟网络上可能有虚拟网络安全设备。 你想要确保与你的 Azure 虚拟网络之间的所有流量都通过该虚拟安全设备。 你可以通过在 Azure 中配置[用户定义的路由](../virtual-network/virtual-networks-udr-overview.md)实现此操作。

[强制隧道](https://www.petri.com/azure-forced-tunneling)是一种机制，可用于确保不允许你的服务启动到 Internet 上的设备的连接。 请注意，这不同于接受传入连接然后对其作出响应。 前端 Web 服务器需要从 Internet 主机对请求作出响应，因此允许源自 Internet 的流量传入到这些 Web 服务器，而且允许这些 Web 服务器作出响应。

你不想允许前端 Web 服务器启动出站请求。 此类请求可能带来安全风险，因为这些连接可用于下载恶意软件。 即使你希望这些前端服务器启动对 Internet 的出站请求，你可能想要强制它们通过你的本地 Web 代理服务器，以便你可以利用 URL 筛选和日志记录。

相反，你想要使用强制隧道来防止这种情况。 启用强制隧道后，会强制所有与 Internet 的连接通过你的本地网关。 你可以通过利用用户定义的路由配置强制隧道。

了解更多：

* [什么是用户定义的路由和 IP 转发](../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>虚拟网络安全设备
当网络安全组、用户定义的路由和强制隧道在 [OSI 模型](https://en.wikipedia.org/wiki/OSI_model)的网络层和传输层为你提供安全级别时，有时你可能想要启用级别高于网络的安全性。

例如，你的安全要求可能包括：

* 必须经过身份验证和授权才允许访问你的应用程序
* 入侵检测和入侵响应
* 高级别协议的应用程序层检查
* URL 筛选
* 网络级别防病毒和反恶意软件
* 防自动程序保护
* 应用程序访问控制
* 其他 DDoS 保护（除了 Azure 结构自身提供的 DDoS 保护以外）

你可以使用 Azure 合作伙伴解决方案访问这些增强的网络安全功能。 通过访问 [Azure 应用商店](https://azure.microsoft.com/marketplace/)并搜索“安全”和“网络安全”，可以找到最新的 Azure 合作伙伴网络安全解决方案。

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>安全远程访问和跨界连接
安装、配置和管理 Azure 资源需要远程完成。 此外，你可能想要部署在本地和 Azure 公有云中具有组件的[混合 IT](http://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) 解决方案。 这些方案要求安全远程访问。

Azure 网络支持以下安全远程访问方案：

* 将单独的工作站连接到 Azure 虚拟网络
* 通过 VPN 将你的本地网络连接到 Azure 虚拟网络
* 通过专用的 WAN 链路将本地网络连接到 Azure 虚拟网络
* 将 Azure 虚拟网络相互连接

### <a name="connect-individual-workstations-to-an-azure-virtual-network"></a>将单独的工作站连接到 Azure 虚拟网络
可能你有时想要使各个开发人员或操作人员在 Azure 中管理虚拟机和服务。 例如，你需要访问 Azure 虚拟网络上的虚拟机，并且你的安全策略不允许对单个虚拟机的 RDP 或 SSH 远程访问。 在这种情况下，你可以使用点到站点 VPN 连接。

点到站点 VPN 连接使用 [SSTP VPN](https://technet.microsoft.com/library/cc731352.aspx) 协议，使你能够在用户和 Azure 虚拟网络之间设置专用的安全连接。 建立 VPN 连接后，用户将能够通过 VPN 上的 RDP 或 SSH 链接到 Azure 虚拟网络上的任何虚拟机（假定用户可以进行身份验证且已经过授权）。

了解更多：

* [使用 PowerShell 配置与虚拟网络的点到站点连接](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-an-azure-virtual-network-with-a-vpn"></a>通过 VPN 将你的本地网络连接到 Azure 虚拟网络
你可能想要将整个企业网络或其中的某些部分连接到 Azure 虚拟网络。 这是常见的混合 IT 方案，通过该方案公司可以[将其本地数据中心扩展到 Azure](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84)。 在许多情况下，公司会将服务的某些部分托管在 Azure 中而其他部分托管在本地，例如一个解决方案包括 Azure 中的前端 Web 服务器和本地的后端数据库。 这样的“跨界”连接还使得位于 Azure 的资源的管理更加安全，并且能够启用方案，如将 Active Directory 域控制器扩展到 Azure 中。

完成此操作的一种方法是使用 [site-to-site VPN](https://www.techopedia.com/definition/30747/site-to-site-vpn)（站点到站点 VPN）。 站点到站点 VPN 和点到站点 VPN 之间的区别是，点到站点 VPN 将一台设备连接到 Azure 虚拟网络，而站点到站点 VPN 将整个网络（如你的本地网络）连接到 Azure 虚拟网络。 到 Azure 虚拟网络的站点到站点 VPN 使用高度安全的 IPsec 隧道模式 VPN 协议。

了解更多：

* [使用 Azure 门户创建具有站点到站点 VPN 连接的资源管理器 VNet](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [规划和设计 VPN 网关](../vpn-gateway/vpn-gateway-plan-design.md)

### <a name="connect-your-on-premises-network-to-an-azure-virtual-network-with-a-dedicated-wan-link"></a>通过专用的 WAN 链路将本地网络连接到 Azure 虚拟网络
点到站点和站点到站点 VPN 连接对启用跨界连接有效。 但是，有些组织认为它们具有以下缺点：

* VPN 连接通过 Internet 移动数据，这会将这些连接暴露给通过公用网络移动数据所涉及的潜在安全问题。 此外，不能保证 Internet 连接的可靠性和可用性。
* 到 Azure 虚拟网络的 VPN 连接可能会针对某些应用程序和目的考虑受限的带宽，因为它们达到的最高极限约为 200Mbps。

对于其跨界连接需要最高级别的安全性和可用性的组织通常使用专用的 WAN 链路连接到远程站点。 Azure 为你提供了使用专用的 WAN 链路将本地网络连接到 Azure 虚拟网络的功能。 此功能通过 Azure ExpressRoute 启用。

了解更多：

* [ExpressRoute 技术概述](../expressroute/expressroute-introduction.md)

### <a name="connect-azure-virtual-networks-to-each-other"></a>将 Azure 虚拟网络相互连接
你可以将多个 Azure 虚拟网络用于部署。 你可能这样做的原因有很多种。 其中一个原因可能是为了简化管理；另一个可能是出于安全方面的原因。 无论将资源放在不同的 Azure 虚拟网络上的动机或理由是什么，可能有时你都会想要将一个网络上的资源与另一个网络相连接。

一个选择是通过 Internet 以“环回”方式将一个 Azure 虚拟网络上的服务连接到另一个 Azure 虚拟网络上的服务。 该连接将在一个 Azure 虚拟网络上启动，通过 Internet，然后再回到目标 Azure 虚拟网络。 此选项将连接暴露给任何基于 Internet 的通信所固有的安全问题。

更好的选择可能是创建 Azure 虚拟网络到 Azure 虚拟网络的站点到站点 VPN。 此 Azure 虚拟网络到 Azure 虚拟网络的站点到站点 VPN 与上面提到的跨界站点到站点 VPN 连接使用相同的 [IPsec 隧道模式](https://technet.microsoft.com/library/cc786385.aspx)协议。

使用 Azure 虚拟网络到 Azure 虚拟网络的站点到站点 VPN 的优点是，VPN 连接基于 Azure 网络结构建立，而不是通过 Internet 进行连接。 与通过 Internet 连接的站点到站点 VPN 相比，这为你提供了额外的安全层。

了解更多：

* [使用 Azure 资源管理器和 PowerShell 配置 VNet 到 VNet 连接](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

## <a name="availability"></a>可用性
可用性是任何安全程序的一个重要组成部分。 如果你的用户和系统无法访问他们需要通过网络访问的内容，可视为该服务已遭到入侵。 Azure 具有支持以下高可用性机制的网络技术：

* 基于 HTTP 的负载平衡
* 网络级别的负载平衡
* 全局负载平衡

负载平衡是专为在多个设备之间均匀分布连接而设计的机制。 负载平衡的目标是：

* 提高可用性 – 在跨多个设备对连接进行负载平衡时，一个或多个设备可能变得不可用，并且正在其余联机设备上运行的服务可以继续提供来自服务的内容。
* 提高性能 – 在跨多个设备对连接进行负载平衡时，单个设备占用处理器命中。 相反，提供内容的处理和内存需求遍布多个设备。

### <a name="http-based-load-balancing"></a>基于 HTTP 的负载平衡
运行基于 Web 的服务的组织通常希望在这些 Web 服务前面具有基于 HTTP 的负载平衡器，以帮助确保足够级别的性能和高可用性。 与传统的基于网络的负载平衡器相比，由基于 HTTP 的负载平衡器所做的负载平衡决策基于 HTTP 协议的特征，而不是基于网络和传输层协议的特征。

为了给你的基于 Web 的服务提供基于 HTTP 的负载平衡，Azure 为你提供了 Azure 应用程序网关。 Azure 应用程序网关支持：

* 基于 HTTP 的负载平衡 – 负载平衡决策都是基于特定于 HTTP 协议的特征做出的。
* 基于 cookie 的会话相关性 – 此功能可确保建立到负载平衡器后面的服务器之一的连接在客户端和服务器之间保持不变。 这确保了事务的稳定性。
* SSL 卸载 – 当使用负载平衡器建立客户端连接时，客户端与负载平衡器之间的会话使用 HTTPS (SSL/) 协议进行加密。 但是，为了提高性能，你可以选择让负载平衡器和该负载平衡器后面的 Web 服务器之间的连接使用 HTTP（未加密）协议。 这称为“SSL 卸载”，因为负载平衡器后面的 Web 服务器不会遇到涉及加密的处理器开销，因此应能更快地为请求提供服务。
* 基于 URL 的内容路由 – 此功能可使负载平衡器决定在哪里转发基于目标 URL 的连接。 与基于 IP 地址做出负载平衡决策的解决方案相比，这提供了更多的灵活性。

了解更多：

* [应用程序网关概述](../application-gateway/application-gateway-introduction.md)

### <a name="network-level-load-balancing"></a>网络级别的负载平衡
与基于 HTTP 的负载平衡相比，网络级别负载平衡基于 IP 地址和端口（TCP 或 UDP）号做出负载平衡决策。
通过使用 Azure 负载平衡器，你可以在 Azure 中获得网络级别负载平衡的优点。 Azure 负载平衡器的一些主要特征包括：

* 基于 IP 地址和端口号的网络级别负载平衡
* 支持任何应用程序层协议
* 对 Azure 虚拟机和云服务角色实例进行负载平衡
* 可用于面向 Internet（外部负载平衡）和面向非 Internet（内部负载平衡）的应用程序和虚拟机
* 终结点监视，用于确定负载平衡器后面的任何服务是否已变得不可用

了解更多：

* [多个虚拟机或服务之间的面向 Internet 的负载平衡器](../load-balancer/load-balancer-internet-overview.md)
* [内部负载平衡器概述](../load-balancer/load-balancer-internal-overview.md)

### <a name="global-load-balancing"></a>全局负载平衡
某些组织可能会需要最高级别的可用性。 实现此目标的一种方法是在全球分布的数据中心中托管应用程序。 在分布于世界各地的数据中心中托管应用程序时，整个地缘政治区域可能会变得不可用，并且仍然让应用程序启动和运行。

除了你通过在全球分布的数据中心中托管应用程序获得的可用性优势，还可以获得性能优势。 通过使用将服务的请求定向到距离发出该请求的设备最近的数据中心的机制可以获得这些性能优势。

全局负载平衡可以为你提供这两个优点。 在 Azure 中，你可以使用 Azure 流量管理器获得全局负载平衡的优点。

了解更多：

* [什么是流量管理器？](../traffic-manager/traffic-manager-overview.md)

## <a name="logging"></a>日志记录
网络级别的日志记录是任何网络安全方案的一项关键功能。 在 Azure 中，你可以记录针对网络安全组获得的信息，以获取网络级别的日志记录信息。 使用 NSG 日志记录，你可以从以下日志中获取信息：

* 审核日志 – 这些日志用于查看提交到你的 Azure 订阅的所有操作。 这些日志默认情况下启用，并且可以在 Azure 门户中使用。
* 事件日志 – 这些日志提供有关应用了哪些 NSG 规则的信息。
* 计数器日志 – 通过这些日志，你可以知道每个 NSG 规则拒绝或允许流量的次数。

还可以使用功能强大的数据可视化工具 [Microsoft Power BI](https://powerbi.microsoft.com/what-is-power-bi/)，以查看和分析这些日志。

了解更多：

* [网络安全组 (NSG) 的 Log Analytics](../virtual-network/virtual-network-nsg-manage-log.md)

## <a name="name-resolution"></a>名称解析
名称解析是在 Azure 中托管的所有服务的一个重要功能。 从安全角度看，名称解析功能的泄漏可能会导致攻击者将来自你的站点的请求重定向到攻击者的站点。 安全的名称解析是所有云托管的服务的要求。

需要解决以下两种类型的名称解析：

* 内部名称解析 – 内部名称解析可供 Azure 虚拟网络、本地网络或两者上的服务使用。 用于内部名称解析的名称通过 Internet 不可访问。 为获得最佳的安全性，重要的是你的内部名称解析方案对于外部用户不可访问。
* 外部名称解析 – 外部名称解析可供你的本地网络和 Azure 虚拟网络外部的用户和设备使用。 这些是对 Internet 可见且用于将连接定向到基于云的服务的名称。

对于内部名称解析，你有两个选项：

* Azure 虚拟网络 DNS 服务器 – 在创建新的 Azure 虚拟网络时，为你创建 DNS 服务器。 此 DNS 服务器可以解析位于 Azure 虚拟网络上的计算机的名称。 此 DNS 服务器是不可配置的，而且由 Azure 结构管理器进行管理，从而使其成为安全的名称解析解决方案。
* 自带 DNS 服务器 – 你可以选择将自己选择的 DNS 服务器放置在 Azure 虚拟网络上。 此 DNS 服务器可能是 Active Directory 集成的 DNS 服务器或由 Azure 合作伙伴提供的专用 DNS 服务器解决方案，两者均可从 Azure 应用商店中获得。

了解更多：

* [虚拟网络概述](../virtual-network/virtual-networks-overview.md)
* [管理虚拟网络 (VNet) 使用的 DNS 服务器](../virtual-network/virtual-networks-manage-dns-in-vnet.md)

对于外部 DNS 解析，你有两个选项：

* 在本地托管你自己外部 DNS 服务器
* 通过服务提供商托管你自己的外部 DNS 服务器

许多大型组织将在本地托管自己的 DNS 服务器。 之所以可以这样做，是因为他们具有可以这样做的网络专业知识和全球影响力。

在大多数情况下，最好通过服务提供商托管你的 DNS 名称解析服务。 这些服务提供商具有网络专业知识和全球影响力，以确保名称解析服务的超高可用性。 可用性是 DNS 服务所必需的，因为如果你的名称解析服务失败，则任何人都将不能访问面向 Internet 的服务。

Azure 以 Azure DNS 的形式为你提供一个高可用性且高性能的外部 DNS 解决方案。 此外部名称解析解决方案利用全球 Azure DNS 基础结构。 它允许你使用与其他 Azure 服务相同的凭据、API、工具和计费在 Azure 中托管你的域。 作为 Azure 的一部分，它还继承了内置在平台中的强大的安全控件。

了解更多：

* [Azure DNS 概述](../dns/dns-overview.md)

## <a name="dmz-architecture"></a>DMZ 体系结构
许多企业组织使用 DMZ 对其网络进行分段，以创建 Internet 及其服务之间的缓冲区域。 网络的 DMZ 部分被视为一个低安全区域，而且不会将重要的资产放在该网段中。 通常，你将看到一些网络安全设备，它们具有 DMZ 网段上的网络接口，以及连接到具有虚拟机和服务的另一个网络接口，用于接受来自 Internet 的传入连接。

有许多 DMZ 设计的变体，如果决定部署 DMZ，那么将根据你的网络安全要求决定使用哪种类型的 DMZ。

了解更多：

* [Microsoft 云服务和网络安全](../best-practices-network-security.md)

## <a name="azure-security-center"></a>Azure 安全中心
安全中心可帮助你预防、检测和响应威胁，同时提高对 Azure 资源的安全性的可见性和控制力度。 它提供 Azure 订阅之间的集成安全监视和策略管理，帮助检测可能被忽略的威胁，且适用于广泛的安全解决方案生态系统。

Azure 安全中心通过以下方式来帮助优化和监视网络安全：

* 提供网络安全建议
* 监视网络安全配置的状态
* 在终结点和网络级别发出基于网络的威胁警报。

了解更多：

* [Azure 安全中心简介](../security-center/security-center-intro.md)



<!--HONumber=Nov16_HO3-->

