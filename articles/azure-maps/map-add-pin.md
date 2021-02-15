---
title: 向地图添加符号层 |Microsoft Azure 映射
description: 了解如何向地图添加自定义符号，如文本或图标。 若要实现此目的，请参阅如何在 Azure Maps Web SDK 中使用数据源和符号层。
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: c5434406af1f912c1c89123baa344dd3f9c7ff91
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2020
ms.locfileid: "92891048"
---
# <a name="add-a-symbol-layer-to-a-map"></a>将符号层添加到地图

将符号连接到数据源，并使用它在给定点呈现图标或文本。 

符号层是使用 WebGL 呈现的。 使用符号层可在地图上呈现大型点集合。 与 HTML 标记相比，符号层在地图上呈现大量的点数据，从而提高性能。 但是，符号层不支持传统的 CSS 和 HTML 元素进行样式设置。  

> [!TIP]
> 默认情况下，符号层将呈现数据源中所有几何图形的坐标。 若要将层限制为仅呈现点几何特征 `filter` ，请将层的属性设置为 `['==', ['geometry-type'], 'Point']` `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` ，或者如果需要，还可以包含 MultiPoint 功能。

地图图像动画管理器加载符号层使用的自定义图像。 它支持以下图像格式：

- JPEG
- PNG
- SVG
- BMP
- GIF 不 (动画) 

## <a name="add-a-symbol-layer"></a>添加符号层

你需要先执行几个步骤，然后才能将符号层添加到地图中。 首先，创建一个数据源，并将其添加到地图中。 创建符号层。 然后，将数据源传递到符号层，以从数据源中检索数据。 最后，将数据添加到数据源，以便呈现一些内容。 

下面的代码演示了加载后应添加到映射的内容。 此示例使用符号层在地图上呈现一个点。 

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a symbol layer to render icons and/or text at points on the map.
var layer = new atlas.layer.SymbolLayer(dataSource);

//Add the layer to the map.
map.layers.add(layer);

//Create a point and add it to the data source.
dataSource.add(new atlas.data.Point([0, 0]));
```

可向地图添加四种不同类型的点数据：

- GeoJSON 点几何-此对象只包含一个点的坐标，而不包含其他任何内容。 `atlas.data.Point`Helper 类可用于轻松创建这些对象。
- GeoJSON MultiPoint geometry-此对象包含多个点的坐标，而不包含其他任何点。 `atlas.data.MultiPoint`Helper 类可用于轻松创建这些对象。
- GeoJSON 功能-此对象包含任何 GeoJSON 几何和一组属性，其中包含与几何图形关联的元数据。 `atlas.data.Feature`Helper 类可用于轻松创建这些对象。
- `atlas.Shape` 类类似于 GeoJSON 功能。 两者都包含一个 GeoJSON 几何和一组属性，这些属性包含与几何关联的元数据。 如果将 GeoJSON 对象添加到数据源，则可以轻松地在层中呈现它。 但是，如果更新了该 GeoJSON 对象的坐标属性，则数据源和映射不会更改。 这是因为 JSON 对象中没有用于触发更新的机制。 Shape 类提供用于更新其包含的数据的函数。 进行更改时，将自动通知和更新数据源和映射。 

下面的代码示例创建一个 GeoJSON 点几何，并将其传递给 `atlas.Shape` 类以使其易于更新。 地图的中心最初用于呈现符号。 将一个 click 事件添加到地图中，以便在其触发时，将鼠标坐标用于 shapes `setCoordinates` 函数。 鼠标坐标在单击事件时记录。 然后， `setCoordinates` 更新地图上符号的位置。

<br/>

<iframe height='500' scrolling='no' title='切换图钉位置' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上的通过 Azure Maps <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>切换图钉位置</a> (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 文章。
</iframe>

> [!TIP]
> 默认情况下，符号层通过隐藏重叠的符号来优化符号的呈现。 放大时，隐藏的符号将变为可见。 若要禁用此功能并始终呈现所有符号，请将 `allowOverlap` 选项的属性设置 `iconOptions` 为 `true` 。

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>将自定义图标添加到符号层

符号层是使用 WebGL 呈现的。 因此，所有资源（例如图标图像）必须载入 WebGL 上下文。 此示例演示如何将自定义图标添加到地图资源。 然后，此图标用于使用地图上的自定义符号呈现点数据。 符号层的 `textField` 属性要求指定一个表达式。 在这种情况下，我们想要呈现温度属性。 由于温度为数字，因此需要将其转换为字符串。 此外，我们还需要在其中追加 "° F"。 表达式可用于执行此串联; `['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='自定义符号图像图标' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上的 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>自定义符号图像图标</a>文章。
</iframe>

> [!TIP]
> Azure Maps Web SDK 提供了多个可与符号层一起使用的可自定义的图像模板。 有关详细信息，请参阅[如何使用图像模板](how-to-use-image-templates-web-sdk.md)文档。

## <a name="customize-a-symbol-layer"></a>自定义符号层 

符号层有许多样式选项。 以下工具可以测试各种样式选项。

<br/>

<iframe height='700' scrolling='no' title='符号层选项' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上的 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) <a href='https://codepen.io/azuremaps/pen/PxVXje/'>符号层选项</a>文章。
</iframe>

> [!TIP]
> 如果只希望使用符号层呈现文本，则可以通过将 `image` 图标选项的属性设置为来隐藏该图标 `'none'` 。

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [SymbolLayer](/javascript/api/azure-maps-control/atlas.layer.symbollayer)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions)

> [!div class="nextstepaction"]
> [IconOptions](/javascript/api/azure-maps-control/atlas.iconoptions)

> [!div class="nextstepaction"]
> [TextOptions](/javascript/api/azure-maps-control/atlas.textoptions)

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [创建数据源](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [添加弹出项](map-add-popup.md)

> [!div class="nextstepaction"]
> [使用数据驱动样式表达式](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [如何使用图像模板](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [添加线条层](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [添加多边形层](map-add-shape.md)

> [!div class="nextstepaction"]
> [添加气泡层](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [添加 HTML 标记](map-add-bubble-layer.md)