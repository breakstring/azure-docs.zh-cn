---
title: Azure ExpressRoute 概述：通过专用连接进行连接
description: 此 ExpressRoute 技术概述介绍了如何使用 ExpressRoute 连接，以便用户通过专用连接将本地网络扩展到 Azure。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: overview
ms.date: 10/05/2020
ms.author: duau
ms.openlocfilehash: 82562eae748753cd785851c5d91f5f152b4c9960
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/19/2020
ms.locfileid: "92206945"
---
# <a name="what-is-azure-expressroute"></a>什么是 Azure ExpressRoute？
使用 ExpressRoute 可通过连接服务提供商所提供的专用连接，将本地网络扩展到 Microsoft 云。 使用 ExpressRoute 可与 Microsoft Azure 和 Microsoft 365 等 Microsoft 云服务建立连接。

可以从任意位置之间的 (IP VPN) 网络、点到点以太网或在场地租用设施上通过连接服务提供商的虚拟交叉连接来建立这种连接。 ExpressRoute 连接不经过公共 Internet。 与通过 Internet 的典型连接相比，ExpressRoute 连接提供更高的可靠性、更快的速度、一致的延迟和更高的安全性。 要了解如何使用 ExpressRoute 将网络连接到 Microsoft，请参阅 [ExpressRoute 连接模型](expressroute-connectivity-models.md)。

![ExpressRoute 连接概述](./media/expressroute-introduction/expressroute-connection-overview.png)

## <a name="key-benefits"></a>主要优点

* 通过连接服务提供商在本地网络与 Microsoft 云之间建立第 3 层连接。 可以从任意位置之间的 (IPVPN) 网络、点到点以太网，或通过以太网交换经由虚拟交叉连接来建立这种连接。
* 跨地缘政治区域中的所有区域连接到 Microsoft 云服务。
* 通过 ExpressRoute 高级版附加组件从全球连接到所有区域的 Microsoft 服务。
* 通过 BGP 在网络与 Microsoft 之间进行动态路由。
* 在每个对等位置提供内置冗余以提高可靠性。
* 连接运行时间 [SLA](https://azure.microsoft.com/support/legal/sla/)。
* Skype for Business 的 QoS 支持。

有关详细信息，请参阅 [ExpressRoute 常见问题](expressroute-faqs.md)。

## <a name="features"></a>功能

### <a name="layer-3-connectivity"></a>第 3 层连接
Microsoft 使用 BGP（一种行业标准动态路由协议），在本地网络、Azure 中的实例和 Microsoft 公共地址之间交换路由。 我们根据不同的流量配置文件来与网络建立多个 BGP 会话。 有关详细信息，请参阅 [ExpressRoute 线路和路由域](expressroute-circuit-peerings.md) 一文。

### <a name="redundancy"></a>冗余
每个 ExpressRoute 线路都有两个连接，用于从连接服务提供商/网络边缘的 [ExpressRoute 位置](./expressroute-locations.md#expressroute-locations)连接到两个 Microsoft 企业边缘路由器 (MSEE)。 Microsoft 要求通过连接服务提供商/网络边缘建立双重 BGP 连接 – 各自连接到每个 MSEE。 可以选择不要在一端部署冗余设备/以太网路线。 但是，连接服务提供商会使用冗余设备，确保以冗余方式将连接移交给 Microsoft。 冗余的第 3 层连接配置是 Microsoft [SLA](https://azure.microsoft.com/support/legal/sla/) 生效的条件。

### <a name="connectivity-to-microsoft-cloud-services"></a>与 Microsoft 云服务建立连接
通过 ExpressRoute 连接可访问以下服务：
* Microsoft Azure 服务
* Microsoft 365 服务

> [!NOTE]
> [!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]
> 

有关通过 ExpressRoute 支持的服务的详细列表，请访问 [ExpressRoute 常见问题解答](expressroute-faqs.md)页。

### <a name="connectivity-to-all-regions-within-a-geopolitical-region"></a>与地缘政治区域中的所有区域建立连接
可以在我们的某个[对等位置](expressroute-locations.md)连接到 Microsoft，并访问该地缘政治区域中的区域。

例如，如果在阿姆斯特丹通过 ExpressRoute 连接到 Microsoft， 则可以访问在北欧和西欧托管的所有 Microsoft 云服务。 有关地缘政治区域、关联的 Microsoft 云区域和对应的 ExpressRoute 对等位置的概述，请参阅 [ExpressRoute 合作伙伴和对等位置](expressroute-locations.md)一文。

### <a name="global-connectivity-with-expressroute-premium"></a>使用 ExpressRoute 高级版建立全球连接
你可以启用 [ExpressRoute 高级版](expressroute-faqs.md)，将连接扩展为跨越地缘政治边界。 例如，如果在阿姆斯特丹通过 ExpressRoute 连接到 Microsoft，则可以访问全球所有区域托管的所有 Microsoft 云服务。 你也可以像访问北欧和西欧地区一样，访问部署在南美或澳大利亚的服务。 国家云不包括在内。

### <a name="local-connectivity-with-expressroute-local"></a>使用 ExpressRoute Local 建立本地连接
通过启用[本地 SKU](expressroute-faqs.md)，可以经济高效地传输数据。 使用本地 SKU，可以将数据传输到所需 Azure 区域附近的 ExpressRoute 位置。 使用 ExpressRoute Local 时，ExpressRoute 端口费用中包括了数据传输。 

### <a name="across-on-premises-connectivity-with-expressroute-global-reach"></a>使用 ExpressRoute Global Reach 进行本地连接
可以启用 ExpressRoute Global Reach，通过连接 ExpressRoute 线路，经本地站点交换数据。 例如，如果你在加利福尼亚州的专用数据中心连接到硅谷的 ExpressRoute 线路，在德克萨斯州的另一个专用数据中心连接到达拉斯的 ExpressRoute 线路。 使用 ExpressRoute Global Reach，你可以通过这两条 ExpressRoute 线路将专用数据中心连接在一起。 跨数据中心的流量将通过 Microsoft 的网络进行遍历。

有关详细信息，请参阅 [ExpressRoute Global Reach](expressroute-global-reach.md)。
### <a name="rich-connectivity-partner-ecosystem"></a>丰富的连接合作伙伴生态系统
ExpressRoute 的连接服务提供商和系统集成商合作伙伴生态系统不断发展。 有关最新信息，请参阅 [ExpressRoute 合作伙伴和对等互连位置](expressroute-locations.md)。

### <a name="connectivity-to-national-clouds"></a>与国家/地区云建立连接
Microsoft 为特殊的地缘政治地区和客户群提供隔离的云环境。 有关国家/地区云和提供商的列表，请参阅 [ExpressRoute 合作伙伴和对等互连位置](expressroute-locations.md)页。

### <a name="expressroute-direct"></a>ExpressRoute Direct
有了 ExpressRoute Direct，客户就有机会直接连接到巧妙分布在全球对等互连位置的 Microsoft 的全球网络。 ExpressRoute Direct 提供双 100 Gbps 连接，支持大规模的主动/主动连接。

ExpressRoute Direct 提供的主要功能包括但不限于：

* 大规模数据引入到存储（如存储和 Cosmos DB）
* 针对监管和需要专用和独立连接的行业的物理隔离，例如：银行、政府和零售
* 根据业务部门，细化控制线路分布

有关详细信息，请参阅[关于 ExpressRoute Direct](./expressroute-erdirect-about.md)。

### <a name="bandwidth-options"></a>带宽选项
可以购买各种带宽的 ExpressRoute 线路。 下面列出了支持的带宽。 请务必咨询连接服务提供商，以确定他们支持的带宽。

* 50 Mbps
* 100 Mbps
* 200 Mbps
* 500 Mbps
* 1 Gbps
* 2 Gbps
* 5 Gbps
* 10 Gbps

### <a name="dynamic-scaling-of-bandwidth"></a>动态调整带宽
用户可以在不中断连接的情况下增大 ExpressRoute 线路带宽（尽最大努力）。 有关详细信息，请参阅[修改 ExpressRoute 线路](expressroute-howto-circuit-portal-resource-manager.md#modify)。

### <a name="flexible-billing-models"></a>弹性计费模式
可以选择最适合自己的计费模式。 请从下列计费模式中进行选择。 有关详细信息，请参阅 [ExpressRoute 常见问题解答](expressroute-faqs.md)。

* **无限制数据**。 计费按月，所有入站和出站数据传输不收取费用。
* **计量数据**。 计费按月，所有入站数据传输不收取费用。 出站数据传输按每 GB 数据传输计费。 数据传输费率根据区域不同而异。
* **ExpressRoute 高级版附加组件**。 ExpressRoute 高级版是 ExpressRoute 线路上的附加组件。 ExpressRoute 高级版附加组件提供以下功能： 
  * 提高 Azure 公共和 Azure 专用对等互连的路由限制，从4,000 路由提升至 10,000 路由。
  * 服务的全球连接。 在任何区域（国家云除外）创建的 ExpressRoute 线路都能够访问位于全球其他区域的资源。 例如，创建于欧洲西部的虚拟网络可以通过在硅谷设置的 ExpressRoute 线路进行访问。
  * 增加了每个 ExpressRoute 线路的 VNet 链接数量，从 10 增加至更大的限制，具体取决于线路的带宽。

## <a name="faq"></a>常见问题解答
有关 ExpressRoute 的常见问题，请参阅 [ExpressRoute 常见问题解答](expressroute-faqs.md)。

## <a name="whats-new"></a><a name="new"></a>新增功能

订阅 RSS 源，并在 [Azure 更新](https://azure.microsoft.com/updates/?category=networking&query=ExpressRoute)页面上查看最新的 ExpressRoute 功能更新。

## <a name="next-steps"></a>后续步骤
* 确保符合所有先决条件。 请参阅 [ExpressRoute 先决条件](expressroute-prerequisites.md)。
* 了解 [ExpressRoute 连接模型](expressroute-connectivity-models.md)。
* 查找服务提供商。 请参阅 [ExpressRoute 合作伙伴和对等位置](expressroute-locations.md)。