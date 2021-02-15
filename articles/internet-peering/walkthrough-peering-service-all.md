---
title: 对等互连服务合作伙伴演练
titleSuffix: Azure
description: 对等互连服务合作伙伴演练
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: 292c850f5352c80112e3f0bcc5c868f8128819be
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592307"
---
# <a name="peering-service-partner-walkthrough"></a>对等互连服务合作伙伴演练

本部分介绍了提供程序为实现对等互连服务启用直接对等互连所需遵循的步骤。

## <a name="create-direct-peering-connection-for-peering-service"></a>为对等互连服务创建直接对等连接
服务提供商可以通过创建支持对等互连服务的新直接对等互连来扩大其地理覆盖范围。 若要实现此目的，
1. 成为对等服务伙伴（如果尚未这样做）
1. 按照说明 [使用门户创建或修改直接对等互连](howto-direct-portal.md)。 确保它满足高可用性要求。
1. 然后，按照步骤 [使用门户在直接对等互连上启用对等互连服务](howto-peering-service-portal.md)。

## <a name="use-legacy-direct-peering-connection-for-peering-service"></a>对对等互连服务使用旧版直接对等连接
如果要使用旧直接对等互连来支持对等互连服务，
1. 成为对等服务伙伴（如果尚未这样做）。
1. 按照说明 [使用门户将旧直接对等互连转换为 Azure 资源](howto-legacy-direct-portal.md)。 如果需要，请订购其他线路以满足高可用性要求。
1. 然后，按照步骤 [使用门户在直接对等互连上启用对等互连服务](howto-peering-service-portal.md)。

## <a name="next-steps"></a>后续步骤

* 了解对 [等互连策略](https://peering.azurewebsites.net/peering)。
* 若要了解有关设置与 Microsoft 的直接对等互连的步骤，请遵循 [直接对等演练](walkthrough-direct-all.md)。
* 若要了解有关与 Microsoft 建立 Exchange 对等互连的步骤，请遵循 [exchange 对等互连演练](walkthrough-exchange-all.md)。