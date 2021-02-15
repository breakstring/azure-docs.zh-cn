---
title: Azure VPN 网关：关于 P2S 路由
description: 了解不同操作系统、远程访问协议和虚拟网络配置的 Azure 点到站点 VPN 路由。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/07/2020
ms.author: cherylmc
ms.openlocfilehash: 0b9b8ba555cddd56c49c750709e69ec180291c95
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91827182"
---
# <a name="about-point-to-site-vpn-routing"></a>关于点到站点 VPN 路由

本文介绍 Azure 点到站点 VPN 路由的工作原理。 P2S VPN 路由行为依赖于客户端 OS、用于 VPN 连接的协议，以及虚拟网络 (VNet) 相互之间的连接方式。

Azure 当前支持两种远程访问协议：IKEv2 和 SSTP。 许多客户端操作系统（包括 Windows、Linux、macOS、Android 和 iOS）都支持 IKEv2。 SSTP 仅可用于 Windows。 如果更改网络拓扑并且具有 VPN 客户端，必须再次下载和安装 Windows 客户端的 VPN 客户端包，以使更改应用于客户端。

> [!NOTE]
> 本文仅适用于 IKEv2。
>

## <a name="about-the-diagrams"></a><a name="diagrams"></a>关于关系图

本文包含大量不同的关系图。 每节都介绍了不同的拓扑或配置。 考虑到本文的目的，站点到站点 (S2S) 和 VNet 到 VNet 连接的工作原理都相同，因为两者都是 IPsec 隧道。 本文中的所有 VPN 网关都基于路由。

## <a name="one-isolated-vnet"></a><a name="isolatedvnet"></a>独立 VNet

本示例中的点到站点 VPN 网关连接适用于未连接或未与其他任何虚拟网络 (VNet1) 对等互连的 VNet。 在此示例中，客户端可以访问 VNet1。

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/isolated.jpg" alt-text="独立 VNet 路由" lightbox="./media/vpn-gateway-about-point-to-site-routing/isolated.jpg":::

### <a name="address-space"></a>地址空间

* VNet1：10.1.0.0/16

### <a name="routes-added"></a>已添加的路由

* 已添加到 Windows 客户端的路由：10.1.0.0/16、192.168.0.0/24

* 已添加到非 Windows 客户端的路由：10.1.0.0/16、192.168.0.0/24

### <a name="access"></a>访问

* Windows 客户端可以访问 VNet1

* 非 Windows 客户端可以访问 VNet1

## <a name="multiple-peered-vnets"></a><a name="multipeered"></a>多个对等互连 VNet

在此示例中，点到站点 VPN 网关连接适用于 VNet1。 VNet1 与 VNet2 对等互连。 VNet2 与 VNet3 对等互连。 VNet1 与 VNet4 对等互连。 VNet1 不与 VNet3 直接对等互连。 VNet1 已启用 "允许网关传输"，VNet2 和 VNet4 已启用 "使用远程网关"。

使用 Windows 的客户端可以直接访问对等互连 VNet，但如果 VNet 对等互连或网络拓扑发生任何更改，必须重新下载 VPN 客户端。 非 Windows 客户端可直接访问对等互连 VNet。 访问不可传递，且仅限直接对等互连的 VNet。

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/multiple.jpg" alt-text="独立 VNet 路由" lightbox="./media/vpn-gateway-about-point-to-site-routing/multiple.jpg":::

### <a name="address-space"></a>地址空间：

* VNet1：10.1.0.0/16

* VNet2：10.2.0.0/16

* VNet3：10.3.0.0/16

* VNet4：10.4.0.0/16

### <a name="routes-added"></a>已添加的路由

* 已添加到 Windows 客户端的路由：10.1.0.0/16、10.2.0.0/16、10.4.0.0/16、192.168.0.0/24

* 已添加到非 Windows 客户端的路由：10.1.0.0/16、10.2.0.0/16、10.4.0.0/16、192.168.0.0/24

### <a name="access"></a>访问

* Windows 客户端可以访问 VNet1、VNet2 和 VNet4，但必须重新下载 VPN 客户端，以使拓扑更改生效。

* 非 Windows 客户端可以访问 VNet1、VNet2 和 VNet4

## <a name="multiple-vnets-connected-using-an-s2s-vpn"></a><a name="multis2s"></a>使用 S2S VPN 连接的多个 VNet

在此示例中，点到站点 VPN 网关连接适用于 VNet1。 VNet1 使用站点到站点 VPN 连接连接到 VNet2。 VNet2 使用站点到站点 VPN 连接连接到 VNet3。 VNet1 和 VNet3 之间没有直接的对等互连或站点到站点 VPN 连接。 所有站点到站点连接均未针对路由运行 BGP。

使用 Windows 或其他受支持 OS 的客户端只能访问 VNet1。 若要访问其他 VNet，必须使用 BGP。

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/multiple-s2s.jpg" alt-text="独立 VNet 路由" lightbox="./media/vpn-gateway-about-point-to-site-routing/multiple-s2s.jpg":::

### <a name="address-space"></a>地址空间

* VNet1：10.1.0.0/16

* VNet2：10.2.0.0/16

* VNet3：10.3.0.0/16

### <a name="routes-added"></a>已添加的路由

* 已添加到 Windows 客户端的路由：10.1.0.0/16、192.168.0.0/24

* 已添加到非 Windows 客户端的路由：10.1.0.0/16、10.2.0.0/16、192.168.0.0/24

### <a name="access"></a>访问

* Windows 客户端只能访问 VNet1

* 非 Windows 客户端只能访问 VNet1

## <a name="multiple-vnets-connected-using-an-s2s-vpn-bgp"></a><a name="multis2sbgp"></a>使用 S2S VPN 的多个 VNet (BGP)

在此示例中，点到站点 VPN 网关连接适用于 VNet1。 VNet1 使用站点到站点 VPN 连接连接到 VNet2。 VNet2 使用站点到站点 VPN 连接连接到 VNet3。 VNet1 和 VNet3 之间没有直接的对等互连或站点到站点 VPN 连接。 所有站点到站点连接均针对路由运行 BGP。

使用 Windows 或其他受支持的 OS 的客户端可以访问使用站点到站点 VPN 连接连接的所有 VNet，但必须将到已连接 VNet 的路由手动添加到 Windows 客户端。

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/multiple-bgp.jpg" alt-text="独立 VNet 路由" lightbox="./media/vpn-gateway-about-point-to-site-routing/multiple-bgp.jpg":::

### <a name="address-space"></a>地址空间

* VNet1：10.1.0.0/16

* VNet2：10.2.0.0/16

* VNet3：10.3.0.0/16

### <a name="routes-added"></a>已添加的路由

* 已添加到 Windows 客户端的路由：10.1.0.0/16

* 已添加到非 Windows 客户端的路由：10.1.0.0/16、10.2.0.0/16、10.3.0.0/16、192.168.0.0/24

### <a name="access"></a>访问

* Windows 客户端可以访问 VNet1、VNet2 和 VNet3，但必须手动添加 VNet2 和 VNet3。

* 非 Windows 客户端可以访问 VNet1、VNet2 和 VNet3

## <a name="one-vnet-and-a-branch-office"></a><a name="vnetbranch"></a>一个 VNet 和一个分支机构

在此示例中，点到站点 VPN 网关连接适用于 VNet1。 VNet1 不与其他任何虚拟网络连接/对等互连，但通过未运行 BGP 的站点到站点 VPN 连接连接到本地站点。

Windows 客户端和非 Windows 客户端只能访问 VNet1。

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/branch-office.jpg" alt-text="独立 VNet 路由" lightbox="./media/vpn-gateway-about-point-to-site-routing/branch-office.jpg":::

### <a name="address-space"></a>地址空间

* VNet1：10.1.0.0/16

* Site1：10.101.0.0/16

### <a name="routes-added"></a>已添加的路由

* 已添加到 Windows 客户端的路由：10.1.0.0/16、192.168.0.0/24

* 已添加到非 Windows 客户端的路由：10.1.0.0/16、192.168.0.0/24

### <a name="access"></a>访问

* Windows 客户端只能访问 VNet1

* 非 Windows 客户端只能访问 VNet1

## <a name="one-vnet-and-a-branch-office-bgp"></a><a name="vnetbranchbgp"></a>一个 VNet 和一个分支机构 (BGP)

在此示例中，点到站点 VPN 网关连接适用于 VNet1。 VNet1 不与其他任何虚拟网络连接/对等互连，但通过运行 BGP 的站点到站点 VPN 连接连接到本地站点 (Site1)。

Windows 客户端可以访问 VNet 和其他分支机构 (Site1)，但必须将到 Site1 的路由手动添加到客户端。 非 Windows 客户端可以访问 VNet 以及本地分支机构。

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/branch-bgp.jpg" alt-text="独立 VNet 路由" lightbox="./media/vpn-gateway-about-point-to-site-routing/branch-bgp.jpg":::

### <a name="address-space"></a>地址空间

* VNet1：10.1.0.0/16

* Site1：10.101.0.0/16

### <a name="routes-added"></a>已添加的路由

* 已添加到 Windows 客户端的路由：10.1.0.0/16、192.168.0.0/24

* 已添加到非 Windows 客户端的路由：10.1.0.0/16、10.101.0.0/16、192.168.0.0/24

### <a name="access"></a>访问

* Windows 客户端可以访问 VNet1 和 Site1，但必须手动添加到 Site1 的路由。

* 非 Windows 客户端可以访问 VNet1 和 Site1。


## <a name="multiple-vnets-connected-using-s2s-and-a-branch-office"></a><a name="multivnets2sbranch"></a>使用 S2S 和分支机构连接的多个 VNet

在此示例中，点到站点 VPN 网关连接适用于 VNet1。 VNet1 使用站点到站点 VPN 连接连接到 VNet2。 VNet2 使用站点到站点 VPN 连接连接到 VNet3。 VNet1 和 VNet3 网络之间没有直接的对等互连或站点到站点 VPN 隧道。 VNet3 使用站点到站点 VPN 连接连接到分支机构 (Site1)。 所有 VPN 连接均未运行 BGP。

所有客户端都只能访问 VNet1。

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/multi-branch.jpg" alt-text="独立 VNet 路由" lightbox="./media/vpn-gateway-about-point-to-site-routing/multi-branch.jpg":::

### <a name="address-space"></a>地址空间

* VNet1：10.1.0.0/16

* VNet2：10.2.0.0/16

* VNet3：10.3.0.0/16

* Site1：10.101.0.0/16

### <a name="routes-added"></a>已添加的路由

* 已添加到 Windows 客户端的路由：10.1.0.0/16、192.168.0.0/24

* 已添加到非 Windows 客户端的路由：10.1.0.0/16、10.2.0.0/16、10.3.0.0/16、10.101.0.0/16、192.168.0.0/24

### <a name="access"></a>访问

* Windows 客户端只能访问 VNet1

* 非 Windows 客户端只能访问 VNet1

## <a name="multiple-vnets-connected-using-s2s-and-a-branch-office-bgp"></a><a name="multivnets2sbranchbgp"></a>使用 S2S 和分支机构连接的多个 VNet (BGP)

在此示例中，点到站点 VPN 网关连接适用于 VNet1。 VNet1 使用站点到站点 VPN 连接连接到 VNet2。 VNet2 使用站点到站点 VPN 连接连接到 VNet3。 VNet1 和 VNet3 网络之间没有直接的对等互连或站点到站点 VPN 隧道。 VNet3 使用站点到站点 VPN 连接连接到分支机构 (Site1)。 所有 VPN 连接均运行 BGP。

使用 Windows 的客户端可以访问使用站点到站点 VPN 连接连接的 VNet 和站点，但必须将到 VNet2、VNet3 和 Site1 的路由手动添加到该客户端。 非 Windows 客户端可以访问使用站点到站点 VPN 连接连接的 VNet 和站点，而无需任何手动干预。 访问权限是可传递的，并且客户端可访问所有已连接 VNet 和站点（本地）中的资源。

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/multi-branch-bgp.jpg" alt-text="独立 VNet 路由" lightbox="./media/vpn-gateway-about-point-to-site-routing/multi-branch-bgp.jpg":::

### <a name="address-space"></a>地址空间

* VNet1：10.1.0.0/16

* VNet2：10.2.0.0/16

* VNet3：10.3.0.0/16

* Site1：10.101.0.0/16

### <a name="routes-added"></a>已添加的路由

* 已添加到 Windows 客户端的路由：10.1.0.0/16、192.168.0.0/24

* 已添加到非 Windows 客户端的路由：10.1.0.0/16、10.2.0.0/16、10.3.0.0/16、10.101.0.0/16、192.168.0.0/24

### <a name="access"></a>访问

* Windows 客户端可以访问 VNet1、VNet2、VNet3 和 Site1，但必须将到 VNet2、VNet3 和 Site1 的路由手动添加到客户端。

* 非 Windows 客户端可以访问 VNet1、VNet2、VNet3 和 Site1。

## <a name="next-steps"></a>后续步骤

若要开始创建 P2S VPN，请参阅[使用 Azure 门户创建 P2S VPN](vpn-gateway-howto-point-to-site-resource-manager-portal.md)。
