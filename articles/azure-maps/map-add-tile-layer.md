---
title: 向地图添加图块层 |Microsoft Azure 映射
description: 了解如何在地图上叠加图像。 查看使用 Azure Maps Web SDK 向地图添加包含天气雷达图的图块层的示例。
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: b3619995739c51d68b00f37ebea3a38680a6b6e7
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2020
ms.locfileid: "92890971"
---
# <a name="add-a-tile-layer-to-a-map"></a>将图块层添加到地图

本文介绍如何覆盖地图上的图块层。 通过图块层可以在 Azure Maps 基本地图图块顶部附加图像。 有关 Azure Maps 平铺系统的详细信息，请参阅 [缩放级别和磁贴网格](zoom-levels-and-tile-grid.md)。

图块层从服务器的磁贴中加载。 这些映像可以是预呈现的或动态呈现的。 使用图块层识别的命名约定，预先呈现的图像与服务器上的任何其他图像存储在一起。 动态呈现的图像使用服务将图像加载到接近实时的时间。 Azure Maps [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer) 类支持三个不同的平铺服务命名约定： 

* X、Y、缩放表示法-X 是列、Y 是磁贴网格中磁贴的行位置，而缩放表示法基于缩放级别。
* Quadkey 表示法-将 x、y 和缩放信息合并为一个字符串值。 此字符串值将成为单个磁贴的唯一标识符。
* 边界框-指定边界方框坐标格式的图像： `{west},{south},{east},{north}` 。 此格式通常由 [Web 映射服务 (WMS) ](https://www.opengeospatial.org/standards/wms)使用。

> [!TIP]
> [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer) 是直观显示地图上的大型数据集的好办法。 矢量数据不仅可以从图像生成，还可以作为图块层呈现。 通过将矢量数据呈现为图块层，地图控件只需加载小于其所表示的矢量数据的文件大小的磁贴。 此方法通常用于在地图上呈现数百万行数据。

传递到磁贴层的磁贴 URL 必须是指向 TileJSON 资源的 http 或 https URL，或者使用以下参数的磁贴 URL 模板： 

* `{x}` - 图块的 X 位置。 还需要 `{y}` 和 `{z}`。
* `{y}` - 图块的 Y 位置。 还需要 `{x}` 和 `{z}`。
* `{z}` - 图块的缩放级别。 还需要 `{x}` 和 `{y}`。
* `{quadkey}` - 基于必应地图图块系统命名约定的图块 quadkey 标识符。
* `{bbox-epsg-3857}` - EPSG 3857 空间引用系统中格式为 `{west},{south},{east},{north}` 的边界框字符串。
* `{subdomain}` -将添加子域值的占位符（如果指定） `subdomain` 。
* `{azMapsDomain}` -一个占位符，用于对齐域并使用映射所用的相同值对磁贴请求进行身份验证。

## <a name="add-a-tile-layer"></a>添加图块层

 此示例演示如何创建指向一组图块的图块层。 此示例使用 x、y、缩放平铺系统。 此图块层源自[爱荷华州立大学的 Iowa Environmental Mesonet](https://mesonet.agron.iastate.edu/ogc/) 的气象雷达图覆盖。 查看雷达图数据时，理想情况下，用户在导航地图时可以清楚地看到城市的标签。 此行为可以通过在层下方插入图块层来实现 `labels` 。

```javascript
//Create a tile layer and add it to the map below the label layer.
//Weather radar tiles from Iowa Environmental Mesonet of Iowa State University.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

下面是上述功能的完整运行代码示例。

<br/>

<iframe height='500' scrolling='no' title='使用 X、Y 和 Z 的图块层' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>使用 X、Y 和 Z 的图块层</a>。
</iframe>

## <a name="customize-a-tile-layer"></a>自定义图块层

图块层类具有许多样式选项。 以下工具可用来试用这些选项。

<br/>

<iframe height='700' scrolling='no' title='图块层选项' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>图块层选项</a>。
</iframe>

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer)

> [!div class="nextstepaction"]
> [TileLayerOptions](/javascript/api/azure-maps-control/atlas.tilelayeroptions)

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [添加图像层](./map-add-image-layer.md)