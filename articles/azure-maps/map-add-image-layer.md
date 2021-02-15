---
title: 向地图添加图像层 |Microsoft Azure 映射
description: 了解如何将图像添加到地图中。 请参阅如何使用 Azure Maps Web SDK 自定义图像层并覆盖固定坐标集上的图像。
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 43de832095e2c2dd674a156da914ed26f1e472d0
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2020
ms.locfileid: "92892938"
---
# <a name="add-an-image-layer-to-a-map"></a>将图像层添加到地图

本文介绍如何将图像叠加到一组固定的坐标。 下面是可以在地图上重叠的不同图像类型的几个示例：

* 从无人机捕获的映像
* 构建 floorplans
* 历史或其他专用地图图像
* 作业站点的蓝图
* 天气雷达图

> [!TIP]
> [ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer)是在地图上覆盖图像的一种简单方法。 请注意，浏览器在加载大图像时可能会遇到困难。 在这种情况下，请考虑将图像分解为磁贴，并将其作为 [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer)加载到地图中。

图像层支持以下图像格式：

- JPEG
- PNG
- BMP
- GIF 不 (动画) 

## <a name="add-an-image-layer"></a>添加图像层

下面的代码在地图上覆盖 [1922 的纽瓦克、New Jersey 的图](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) 的图像。 可以通过将 URL 传递到图像来创建 [ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer) ，并以格式表示四个角的坐标 `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]` 。

```javascript
//Create an image layer and add it to the map.
map.layers.add(new atlas.layer.ImageLayer({
    url: 'newark_nj_1922.jpg',
    coordinates: [
        [-74.22655, 40.773941], //Top Left Corner
        [-74.12544, 40.773941], //Top Right Corner
        [-74.12544, 40.712216], //Bottom Right Corner
        [-74.22655, 40.712216]  //Bottom Left Corner
    ]
}));
```

下面是上述代码的完整运行代码示例。

<br/>

<iframe height='500' scrolling='no' title='简单图像层' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/eQodRo/'>简单图像层</a>。
</iframe>

## <a name="import-a-kml-file-as-ground-overlay"></a>导入 KML 文件作为地面覆盖

此示例演示如何将 KML 地面叠加信息添加为地图上的图像层。 KML 地面叠加提供北方、南部、东和西坐标以及逆时针旋转。 但图像层需要图像每个角的坐标。 本示例中的 KML 地面覆盖适用于 Chartres cathedral，其来源为 [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml)。

该代码使用 `getCoordinatesFromEdges` [ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer) 类中的静态函数。 它使用 "KML" 地面叠加的 "北部"、"东南"、"东部"、"西部" 和 "旋转" 信息来计算图像的四个角。

<br/>

<iframe height='500' scrolling='no' title='KML 地面叠加作为图像层' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>KML 地面叠加作为图像层</a>。
</iframe>

## <a name="customize-an-image-layer"></a>自定义图像层

图像层具有很多样式选项。 下面是一个用于试用的工具。

<br/>

<iframe height='700' scrolling='no' title='图像层选项' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>图像层选项</a>。
</iframe>

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer)

> [!div class="nextstepaction"]
> [ImageLayerOptions](/javascript/api/azure-maps-control/atlas.imagelayeroptions)

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [添加图块层](./map-add-tile-layer.md)