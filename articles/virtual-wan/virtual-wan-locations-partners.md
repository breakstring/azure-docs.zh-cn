---
title: Azure 虚拟 WAN 合作伙伴和位置 |Microsoft Docs
description: 本文包含 Azure 虚拟 WAN 的合作伙伴和中心位置的列表。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to find a Virtual WAN partner
ms.openlocfilehash: 188bc14de502640ae05f1d1eb55330d86cee50b4
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621473"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>虚拟 WAN 合作伙伴和虚拟中心位置

本文提供有关虚拟 WAN 针对连接到虚拟中心所支持的区域以及合作伙伴的信息。

Azure Virtual WAN 是一种网络服务，它通过 Azure 提供经优化、自动的分支到分支连接。 使用虚拟 WAN 可以连接分支设备，并将其配置为与 Azure 通信。 可以手动完成此操作，也可以通过虚拟 WAN 合作伙伴使用提供商设备完成此操作。 使用合作伙伴设备可以降低操作难度、简化连接和进行配置管理。

本地设备到虚拟中心的连接将自动建立。 虚拟中心是 Microsoft 托管的虚拟网络。 中心包含不同的服务终结点，可从本地网络 (vpnsite) 建立连接。 每个区域只能有一个中心。

## <a name="branch-ipsec-connectivity-automation-from-partners"></a><a name="automation"></a>从合作伙伴自动进行分支 IPSec 连接

连接到 Azure 虚拟 WAN 的设备具有内置的自动化连接功能。 这通常在设备管理 UI（或同等位置）中设置，该 UI 设置 VPN 分支设备到 Azure 虚拟中心 VPN 终结点（VPN 网关）之间的连接和配置管理。

以下高级自动化在设备控制台/管理中心中设置：

* 设备访问 Azure 虚拟 WAN 资源组的相应权限
* 将分支设备上传到 Azure 虚拟 WAN
* 自动下载 Azure 连接信息
* 本地分支设备的配置 

某些连接合作伙伴可以扩展自动化，包括创建 Azure 虚拟中心 VNet 和 VPN 网关。 若要了解有关自动化的详细信息，请参阅[适用于虚拟 WAN 合作伙伴的自动化指南](virtual-wan-configure-automation-providers.md)。

## <a name="branch-ipsec-connectivity-partners"></a><a name="partners"></a>分支 IPSec 连接合作伙伴

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

以下合作伙伴将被列入我们的路线图，该路线图基于公司之间签署的条款清单，该条款清单指示了在合作伙伴设备和 Azure 虚拟 WAN VPN 网关之间实现自动 IPsec 连接的工作范围：128 Technologies、Arista、F5 Networks、Oracle SD-WAN (Talari) 和 SharpLink。

## <a name="partners-with-integrated-virtual-hub-offerings"></a>具有集成虚拟中心产品/服务的合作伙伴
除了提供自动分支机构 IPSec 连接外，某些合作伙伴还提供网络虚拟设备 (NVA)，这些设备可直接集成到 Azure 虚拟 WAN 中心。  这让客户可以选择终止其与虚拟中心内兼容的第三方设备的分支连接。  

在虚拟 WAN 中心提供 NVA 的合作伙伴必须：

* 已通过其分支设备实现了自动 IPSec 连接，并已将其 NVA 产品/服务加入了 Azure 虚拟 WAN 中心。
* 在 Azure 市场中提供现有网络虚拟设备产品/服务。

如果你是合作伙伴，对虚拟中心产品/服务中的托管 NVA 有疑问，请通过发送电子邮件至 vwannvaonboarding@microsoft.com 与我们联系

## <a name="integrated-virtual-hub-nva-partners"></a>集成虚拟中心 NVA 合作伙伴
这些合作伙伴提供了“托管应用程序”产品/服务，这些产品/服务现在可以部署到虚拟 WAN 中心。

|合作伙伴|配置/操作方法/部署指南|
|---|---|
|[Barracuda Networks](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/barracudanetworks.barracuda_cloudgenwan_gateway?tab=Overviewus/marketplace/apps/barracudanetworks.barracuda_cloudgenwan_gateway?tab=Overview)| [Barracuda CloudGen WAN 部署指南](https://campus.barracuda.com/product/cloudgenwan/doc/91980640/deployment/)|
|[Cisco 云服务路由器 (CSR) VWAN](https://aka.ms/ciscoMarketPlaceOffer)| 在将 Cisco 云服务 (CSR) WAN 公开预览版发布到 VWAN 中心期间，Cisco 要求最终客户向 vwan_public_preview@external.cisco.com 发送电子邮件并请求“vManage 部署指南”，通过这种方式注册为 Cisco EFT（早期现场试用）客户。 |

在不久的将来，我们会将以下合作伙伴预定给虚拟中心中的 NVA： Aviatrix、Citrix、VeloCloud 和反向网络。

## <a name="locations"></a><a name="locations"></a>位置

[!INCLUDE [regions](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>后续步骤

* 有关虚拟 WAN 的详细信息，请参阅[虚拟 WAN 常见问题解答](virtual-wan-faq.md)。

* 有关如何自动连接到 Azure 虚拟 WAN 的详细信息，请参阅[适用于虚拟 WAN 合作伙伴的自动化指南](virtual-wan-configure-automation-providers.md)。
