---
title: 在 Microsoft Azure 映射中为地图创建数据源
description: 了解如何为地图创建数据源。 了解 Azure Maps Web SDK 使用的数据源： GeoJSON 源和矢量图块。
author: rbrundritt
ms.author: richbrun
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 9964c99ddfb59811fc67df634b41cede5847ede0
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2020
ms.locfileid: "97678857"
---
# <a name="create-a-data-source"></a>创建数据源

Azure Maps Web SDK 将数据存储在数据源中。 使用数据源优化用于查询和呈现的数据操作。 目前有两种类型的数据源：

* **GeoJSON source**：在本地管理 GeoJSON 格式的原始位置数据。 适用于小型到中型数据集， (的) 的数量为几十万个形状。
* **矢量图块源**：基于地图平铺系统，为当前地图视图加载格式为矢量图块的数据。 适用于大到大数据集 (数百万或数十亿个形状) 。

## <a name="geojson-data-source"></a>GeoJSON 数据源

基于 GeoJSON 的数据源使用类在本地加载和存储数据 `DataSource` 。 可以使用 [GeoJSON 命名空间](/javascript/api/azure-maps-control/atlas.data) 中的帮助器类手动创建或创建数据。 `DataSource`类提供用于导入本地或远程 GeoJSON 文件的函数。 远程 GeoJSON 文件必须托管在已启用 CORs 的终结点上。 `DataSource`类为聚集点数据提供功能。 而且，可以通过类轻松地添加、删除和更新数据 `DataSource` 。 下面的代码演示如何在 Azure Maps 中创建 GeoJSON 数据。

```javascript
//Create raw GeoJSON object.
var rawGeoJson = {
     "type": "Feature",
     "geometry": {
         "type": "Point",
         "coordinates": [-100, 45]
     },
     "properties": {
         "custom-property": "value"
     }
};

//Create GeoJSON using helper classes (less error prone and less typing).
var geoJsonClass = new atlas.data.Feature(new atlas.data.Point([-100, 45]), {
    "custom-property": "value"
}); 
```

创建后，可以通过属性将数据源添加到地图 `map.sources` 中，这是一个 [SourceManager](/javascript/api/azure-maps-control/atlas.sourcemanager)。 下面的代码演示如何创建 `DataSource` 并将其添加到地图中。

```javascript
//Create a data source and add it to the map.
var source = new atlas.source.DataSource();
map.sources.add(source);
```

下面的代码演示 GeoJSON 数据可添加到的不同方式 `DataSource` 。

```javascript
//GeoJsonData in the following code can be a single or array of GeoJSON features or geometries, a GeoJSON feature colleciton, or a single or array of atlas.Shape objects.

//Add geoJSON object to data source. 
source.add(geoJsonData);

//Load geoJSON data from URL. URL should be on a CORs enabled endpoint.
source.importDataFromUrl(geoJsonUrl);

//Overwrite all data in data source.
source.setShapes(geoJsonData);
```

> [!TIP]
> 假设您希望覆盖中的所有数据 `DataSource` 。 如果对 then 函数进行调用 `clear` `add` ，则映射可能会重新呈现两次，这可能会导致一些延迟。 改为使用 `setShapes` 函数，该函数将移除并替换数据源中的所有数据，并且只触发地图的单次重新呈现。

## <a name="vector-tile-source"></a>矢量磁贴源

矢量图块源介绍如何访问矢量图块层。 使用 [VectorTileSource](/javascript/api/azure-maps-control/atlas.source.vectortilesource) 类实例化矢量图块源。 矢量图块层类似于图块层，但它们不同。 图块层是光栅图像。 矢量图块层是压缩文件，采用 **PBF** 格式。 此压缩文件包含向量映射数据以及一个或多个层。 基于每个层的样式，可以在客户端上呈现并设计文件样式。 矢量磁贴中的数据包含点、线条和多边形格式的地理功能。 使用矢量图块层（而不是光栅图块层）有多种优点：

* 矢量磁贴的文件大小通常比等效的光栅磁贴小得多。 因此，使用的带宽更少。 这意味着延迟较低、更快的地图和更好的用户体验。
* 由于矢量磁贴是在客户端上呈现的，因此它们会适应其所显示的设备的分辨率。 因此，呈现的地图的定义更清晰，并显示 crystal 清晰标签。
* 更改向量图中的数据样式不需要再次下载数据，因为新样式可应用于客户端。 与此相反，更改光栅图块层的样式通常需要从服务器加载磁贴，然后应用新样式。
* 由于数据是以矢量形式传递的，因此，进行数据准备需要的服务器端处理就越少。 因此，可以更快地提供较新的数据。

Azure Maps 遵循 [Mapbox Vector 磁贴规范](https://github.com/mapbox/vector-tile-spec)，即开放标准。 Azure Maps 在平台中提供以下矢量图块服务：

- 路标磁贴[文档](/rest/api/maps/renderv2/getmaptilepreview)  |  [数据格式详细信息](https://developer.tomtom.com/maps-api/maps-api-documentation-vector/tile)
- 流量事件[文档](/rest/api/maps/traffic/gettrafficincidenttile)  |  [数据格式详细信息](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles)
- 流量流[文档](/rest/api/maps/traffic/gettrafficflowtile)  |  [数据格式详细信息](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-flow/vector-flow-tiles)
- Azure Maps Creator (预览版) 还允许通过[获取磁贴呈现 V2](/rest/api/maps/renderv2/getmaptilepreview)创建和访问自定义向量磁贴

> [!TIP]
> 使用 web SDK Azure Maps 呈现服务中的矢量或光栅图像磁贴时，可以 `atlas.microsoft.com` 将替换为占位符 `{azMapsDomain}` 。 此占位符将替换为映射所使用的相同域，并将自动附加相同的身份验证详细信息。 当使用 Azure Active Directory authentication 时，这大大简化了使用渲染服务进行的身份验证。

若要在地图上显示 vector 磁贴源中的数据，请将源连接到数据呈现层之一。 使用矢量源的所有层都必须 `sourceLayer` 在选项中指定一个值。 下面的代码将 Azure Maps 流量向量磁贴服务作为矢量图块源加载，然后使用线条层在地图上显示它。 此向量磁贴源在源层中具有一组称为 "流量流" 的数据。 此数据集中的行数据具有一个名为的属性，该属性 `traffic_level` 在此代码中用于选择颜色并缩放线条大小。

```javascript
//Create a vector tile source and add it to the map.
var source = new atlas.source.VectorTileSource(null, {
    tiles: ['https://{azMapsDomain}/traffic/flow/tile/pbf?api-version=1.0&style=relative&zoom={z}&x={x}&y={y}'],
    maxZoom: 22
});
map.sources.add(source);

//Create a layer for traffic flow lines.
var flowLayer = new atlas.layer.LineLayer(source, null, {
    //The name of the data layer within the data source to pass into this rendering layer.
    sourceLayer: 'Traffic flow',

    //Color the roads based on the traffic_level property. 
    strokeColor: [
        'interpolate',
        ['linear'],
        ['get', 'traffic_level'],
        0, 'red',
        0.33, 'orange',
        0.66, 'green'
    ],

    //Scale the width of roads based on the traffic_level property. 
    strokeWidth: [
        'interpolate',
        ['linear'],
        ['get', 'traffic_level'],
        0, 6,
        1, 1
    ]
});

//Add the traffic flow layer below the labels to make the map clearer.
map.layers.add(flowLayer, 'labels');
```

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="矢量磁贴线条层" src="https://codepen.io/azuremaps/embed/wvMXJYJ?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅 CodePen 上的 () Azure Maps "笔<a href='https://codepen.io/azuremaps/pen/wvMXJYJ'>矢量平铺线层</a>" <a href='https://codepen.io/azuremaps'>@azuremaps</a> 。 <a href='https://codepen.io'></a>
</iframe>

<br/>

## <a name="connecting-a-data-source-to-a-layer"></a>将数据源连接到层

使用呈现层在地图上呈现数据。 一个或多个呈现层可以引用单个数据源。 以下呈现层需要数据源：

* [气泡图层](map-add-bubble-layer.md) -将点数据呈现为地图上的缩放圆圈。
* [符号层](map-add-pin.md) -将点数据呈现为图标或文本。
* [热度地图层](map-add-heat-map-layer.md) -将点数据呈现为密度热度地图。
* [线条层](map-add-shape.md) -呈现线条并呈现多边形的轮廓。 
* [多边形层](map-add-shape.md) -使用纯色或图像图案填充多边形的面积。

下面的代码演示如何创建数据源，将其添加到地图中，然后将其连接到气泡图层。 然后，将 GeoJSON 点数据从远程位置导入到数据源中。 

```javascript
//Create a data source and add it to the map.
var source = new atlas.source.DataSource();
map.sources.add(source);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(source));

//Load the earthquake data.
source.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

还有其他呈现层不连接到这些数据源，但它们直接加载要呈现的数据。 

* [图像层](map-add-image-layer.md) -将单个图像叠加到地图的顶部，并将其角部绑定到一组指定坐标。
* 图[块层](map-add-tile-layer.md)-superimposes 在地图的顶部。

## <a name="one-data-source-with-multiple-layers"></a>一个具有多个层的数据源

多个层可以连接到单个数据源。 在许多不同方案中，此选项很有用。 例如，假设用户绘制多边形的情况。 当用户向地图添加点时，应呈现并填充多边形区域。 如果将样式的线条添加到边框，则可以在用户绘制时更轻松地看到多边形的边缘。 若要方便地编辑多边形中的单个位置，可以在每个位置的上方添加一个句柄，如 pin 或标记。

![显示单个数据源呈现数据的多个层的地图](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

在大多数映射平台中，需要一个多边形对象、一个线条对象和一个用于多边形中每个位置的 pin。 修改多边形时，您需要手动更新线条和 pin，这很快就会变得复杂。

对于 Azure Maps，只需在数据源中使用一个多边形，如以下代码所示。

```javascript
//Create a data source and add it to the map.
var source = new atlas.source.DataSource();
map.sources.add(source);

//Create a polygon and add it to the data source.
source.add(new atlas.data.Polygon([[[/* Coordinates for polygon */]]]));

//Create a polygon layer to render the filled in area of the polygon.
var polygonLayer = new atlas.layer.PolygonLayer(source, 'myPolygonLayer', {
     fillColor: 'rgba(255,165,0,0.2)'
});

//Create a line layer for greater control of rendering the outline of the polygon.
var lineLayer = new atlas.layer.LineLayer(source, 'myLineLayer', {
     color: 'orange',
     width: 2
});

//Create a bubble layer to render the vertices of the polygon as scaled circles.
var bubbleLayer = new atlas.layer.BubbleLayer(source, 'myBubbleLayer', {
     color: 'orange',
     radius: 5,
     strokeColor: 'white',
     strokeWidth: 2
});

//Add all layers to the map.
map.layers.add([polygonLayer, lineLayer, bubbleLayer]);
```

> [!TIP]
> 使用函数向地图添加层时 `map.layers.add` ，可以将现有层的 ID 或实例作为第二个参数传入。 这会告知映射将插入要添加到现有层下方的新层。 除了传递层 ID 外，此方法还支持以下值。
>
> * `"labels"` -将新层插入到地图标签层之下。
> * `"transit"` -将新层插入到地图道路和中转层下方。

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource)

> [!div class="nextstepaction"]
> [DataSourceOptions](/javascript/api/azure-maps-control/atlas.datasourceoptions)

> [!div class="nextstepaction"]
> [VectorTileSource](/javascript/api/azure-maps-control/atlas.source.vectortilesource)

> [!div class="nextstepaction"]
> [VectorTileSourceOptions](/javascript/api/azure-maps-control/atlas.vectortilesourceoptions)

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [添加弹出项](map-add-popup.md)

> [!div class="nextstepaction"]
> [使用数据驱动样式表达式](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [添加符号层](map-add-pin.md)

> [!div class="nextstepaction"]
> [添加气泡层](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [添加线条层](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [添加多边形层](map-add-shape.md)

> [!div class="nextstepaction"]
> [添加热度地图](map-add-heat-map-layer.md)

> [!div class="nextstepaction"]
> [代码示例](/samples/browse/?products=azure-maps)