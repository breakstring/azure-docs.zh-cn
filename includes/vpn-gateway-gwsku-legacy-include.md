---
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: cherylmc
ms.openlocfilehash: 622a2f69c2e7b82f2df10d6120ee2dd466961915
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "77211373"
---
旧版（老版）VPN 网关 SKU 为：

* 默认值（基本）
* Standard
* HighPerformance

VPN 网关不使用 UltraPerformance 网关 SKU。 有关 UltraPerformance SKU 的信息，请参阅 [ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md) 文档。

使用旧版 SKU 时，请考虑以下方面：

* 如果想要使用 PolicyBased VPN 类型，必须使用基本 SKU。 任何其他 SKU 均不支持 PolicyBased VPN（之前称为静态路由）。
* 基本 SKU 不支持 BGP。
* 基本 SKU 不支持 ExpressRoute-VPN 网关共存配置。
* 只能在高性能 SKU 上配置主动-主动 S2S VPN 网关连接。