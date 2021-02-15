---
title: 移动服务 (预览) Microsoft Azure 地图中的数据结构
description: 了解如何将数据组织到 Azure Maps 移动服务 (预览版) 的大都市区。 查看哪些字段存储有关公共传输停止和行的信息。
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8ffedc18ba331733723a6293756b60b733cc32cf
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96904714"
---
# <a name="data-structures-in-azure-maps-mobility-services-preview"></a>Azure Maps 移动服务 (预览版中的数据结构)  

> [!IMPORTANT]
> Azure Maps 移动服务目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。



本文介绍了 [Azure Maps 移动服务](/rest/api/maps/mobility)中的大都市区域的概念。 我们讨论了在为公共传输停止和行查询此服务时返回的一些公共字段。 建议在开发移动服务 Api 之前阅读本文。

## <a name="metro-area"></a>地铁区域

移动服务 (预览) 数据按受支持的地铁区域分组。 地铁区域不遵循城市界限。 地铁区域可包含多个城市、密集填充的城市和周围的城市。 事实上，国家/地区可以是一个大都市区。 

`metroID`是大都市区域的 ID，可用于调用[获取地铁区域信息 API](/rest/api/maps/mobility/getmetroareainfopreview)。 使用 Azure Maps "获取地铁" API 来请求传输类型、传输机构、活动警报，以及所选地铁的其他详细信息。 你还可以请求支持的地铁区域和 metroIDs。 地铁区域 Id 可能会更改。

**metroID：** 522   **Name：** Tacoma-Bellevue

![西雅图-地铁区域](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>停止 Id

传输停止可由两种 Id 类型引用： [常规传输源规范 (GFTS) ](http://gtfs.org/) id 和 AZURE MAPS 停止 id。 GFTS ID 称为 stopKey，而 Azure Maps stop ID 称为 stopID。 当经常提到传输停止时，建议使用 Azure Maps 停止 ID。 只要物理停止存在，stopID 就会更稳定，并且可能会保持不变。 GTFS stop ID 会更频繁地更新。 例如，可以根据 GTFS 提供程序请求或发布新的 GTFS 版本时，更新 GTFS stop ID。 尽管物理停止没有变化，但 GTFS 停止 ID 可能会更改。

若要开始，可以使用 " [获取附近的传输 API](/rest/api/maps/mobility/getnearbytransitpreview)" 请求附近的传输停止。

## <a name="line-groups-and-lines"></a>行组和线条

移动服务 (预览) 对线条和行组使用并行数据模型。 此模型用于更好地处理从 [GTFS](http://gtfs.org/) 路由和行程数据继承的更改。


### <a name="line-groups"></a>行组

"行组" 是一个实体，它将逻辑上属于同一组的所有行组合在一起。 通常，线条组包含两行，一个从点 A 到 B，另一个从点 B 返回到。这两行都属于同一公共传输机构，并且具有相同的行号。 但是，在某些情况下，线条组具有两行以上的行或其中只有一行。


### <a name="lines"></a>线条

如上所述，每个行组都由一组行组成。 每个行组都由两行组成，每行描述一个方向。  但是，在某些情况下，多个行组成了一个线条组。 例如，有一条线有时 detour 某个邻近，有时不会。 在这两种情况下，它在相同的行号下运行。 此外，线条组可以由单个行组成。 具有单个方向的圆线是一个具有一行的 ling 组。

首先，可以使用 [获取传输线路 API](/rest/api/maps/mobility/gettransitlineinfopreview)请求行组。


## <a name="next-steps"></a>后续步骤

了解如何使用移动服务 (预览) 请求传输数据：

> [!div class="nextstepaction"]
> [如何请求传输数据](how-to-request-transit-data.md)

了解如何使用移动服务 (预览) 请求实时数据：

> [!div class="nextstepaction"]
> [如何请求实时数据](how-to-request-real-time-data.md)

浏览 Azure Maps 移动服务 (预览版) API 文档

> [!div class="nextstepaction"]
> [移动服务 API 文档](/rest/api/maps/mobility)