---
title: 旧式 Azure 虚拟网络 VPN 网关 SKU
description: 如何使用旧式虚拟网络网关 SKU；“基本”、“标准”和“高性能”。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/15/2019
ms.author: cherylmc
ms.openlocfilehash: 9c5e6d5aca51bd560a46837ba47de86362665773
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84687780"
---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>使用虚拟网关 SKU（传统 SKU）

本文包含有关传统（旧）虚拟网关 SKU 的信息。 传统 SKU 仍可用于已创建的 VPN 网关的两种部署模型。 经典 VPN 网关继续使用传统 SKU，不管是对于现有网关还是对于新网关。 新建资源管理器 VPN 网关时，使用新的网关 SKU。 有关新 SKU 的信息，请参阅[关于 VPN 网关](vpn-gateway-about-vpngateways.md)。

## <a name="gateway-skus"></a><a name="gwsku"></a>网关 SKU

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

可以在“虚拟网关”部分（位于 [ExpressRoute 定价页](https://azure.microsoft.com/pricing/details/expressroute)上）查看旧版网关定价。

## <a name="estimated-aggregate-throughput-by-sku"></a><a name="agg"></a>按 SKU 列出的估计聚合吞吐量

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="supported-configurations-by-sku-and-vpn-type"></a><a name="config"></a>按 SKU 和 VPN 类型划分的受支持配置

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize-a-gateway"></a><a name="resize"></a>重设网关大小

可以在同一 SKU 系列内将网关大小重设到某个网关 SKU。 例如，如果具有标准 SKU，则可重设大小为高性能 SKU。 但不能在旧式 SKU 和新式 SKU 系列之间重设 VPN 网关大小。 例如，不能从标准 SKU 调整为 VpnGw2 SKU 或从标准 SKU 调整为 VpnGw1。

### <a name="resource-manager"></a>Resource Manager

若要使用 PowerShell 重设资源管理器部署模型的网关大小，请使用以下命令：

```powershell
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

还可以在 Azure 门户中重设网关大小。

### <a name="classic"></a><a name="classicresize"></a>经典

若要重设经典部署模型的网关大小，必须使用服务管理 PowerShell cmdlet。 请使用以下命令：

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="change-to-the-new-gateway-skus"></a><a name="change"></a>更改到新式网关 SKU

[!INCLUDE [Change to the new SKUs](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="next-steps"></a>后续步骤

有关新式网关 SKU 的详细信息，请参阅[网关 SKU](vpn-gateway-about-vpngateways.md#gwsku)。

有关配置设置的详细信息，请参阅[关于 VPN 网关配置设置](vpn-gateway-about-vpn-gateway-settings.md)。
