---
title: 读取和写入空间数据 |Microsoft Azure 映射
description: 了解如何使用 Azure Maps Web SDK 提供的空间 IO 模块来读取和写入数据。
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/01/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 133674e6764e12742f5b238946e943d9b5011cd2
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2020
ms.locfileid: "92891323"
---
# <a name="read-and-write-spatial-data"></a>读取和写入空间数据

下表列出了在空间 IO 模块中执行读取和写入操作时支持的空间文件格式。

| 数据格式       | 读取 | 写入 |
|-------------------|------|-------|
| GeoJSON           | ✓  |  ✓  |
| GeoRSS            | ✓  |  ✓  |
| GML               | ✓  |  ✓  |
| .GPX）               | ✓  |  ✓  |
| KML               | ✓  |  ✓  |
| KMZ               | ✓  |  ✓  |
| 空间 CSV       | ✓  |  ✓  |
| Well-Known 文本   | ✓  |  ✓  |

以下各节概述了使用空间 IO 模块读取和写入空间数据的所有不同工具。

## <a name="read-spatial-data"></a>读取空间数据

`atlas.io.read`函数是用于读取通用空间数据格式（例如 KML、.gpx) 、GeoRSS、GeoJSON 和具有空间数据的 CSV 文件）的主要函数。 此函数还可以读取这些格式的压缩版本，如 zip 文件或 KMZ 文件。 KMZ 文件格式是 KML 的压缩版本，还可以包含诸如图像之类的资产。 或者，read 函数可以采用指向任意格式的文件的 URL。 Url 应托管在已启用 CORS 的终结点上，或者应在读取选项中提供代理服务。 代理服务用于在未启用 CORS 的域上加载资源。 Read 函数返回向地图添加图像图标的承诺，并异步处理数据，以最大程度地减小对 UI 线程的影响。

读取压缩文件（作为 zip 或 KMZ）时，将对第一个有效文件进行解压缩和扫描。 例如，kml 或具有其他有效扩展名的文件，例如：. kml、.xml、geojson、json、.csv、tsv 或 .txt。 然后，预加载 KML 和 GeoRSS 文件中引用的图像，以确保它们可访问。 不可访问的图像数据可能会加载备用回退映像，或将从样式中删除。 从 KMZ 文件中提取的图像将转换为数据 Uri。

Read 函数的结果是一个 `SpatialDataSet` 对象。 此对象扩展 GeoJSON FeatureCollection 类。 它可以轻松地按原样传递到， `DataSource` 以在地图上呈现其功能。 `SpatialDataSet`不仅包含功能信息，还可能包括 KML 地面叠加、处理度量值和其他详细信息，如下表中所述。

| 属性名称 | 类型 | 说明 | 
|---------------|------|-------------|
| `bbox` | `BoundingBox` | 数据集中所有数据的边界框。 |
| `features` | `Feature[]` | GeoJSON 数据集内的功能。 |
| `groundOverlays` | `(atlas.layer.ImageLayer | atlas.layers.OgcMapLayer)[]` | KML GroundOverlays 的数组。 |
| `icons` | 记录 &lt; 字符串，字符串&gt; | 一组图标 Url。 Key = icon name，Value = URL。 |
| properties | any | 在空间数据集的文档级提供的属性信息。 |
| `stats` | `SpatialDataSetStats` | 有关空间数据集的内容和处理时间的统计信息。 |
| `type` | `'FeatureCollection'` | 只读 GeoJSON 类型值。 |

## <a name="examples-of-reading-spatial-data"></a>读取空间数据的示例

下面的代码演示如何读取空间数据集，并使用类在地图上呈现该数据集 `SimpleDataLayer` 。 该代码使用 URL 指向的 .GPX) 文件。

<br/>

<iframe height='500' scrolling='no' title='简单加载空间数据' src='//codepen.io/azuremaps/embed/yLNXrZx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 CodePen 上的 () ，以简明的 Azure Maps 方式<a href='https://codepen.io/azuremaps/pen/yLNXrZx/'>加载空间数据</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>。
</iframe>

下面的代码演示演示如何读取 KML 或 KMZ，并将其加载到映射。 KML 可以包含地面覆盖，其形式为 `ImageLyaer` 或 `OgcMapLayer` 。 这些叠加必须与功能分开添加到地图中。 此外，如果数据集包含自定义图标，则在加载功能之前需要将这些图标加载到映射资源。

<br/>

<iframe height='500' scrolling='no' title='将 KML 加载到地图上' src='//codepen.io/azuremaps/embed/XWbgwxX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 CodePen 上的 Azure Maps () ，将笔<a href='https://codepen.io/azuremaps/pen/XWbgwxX/'>加载 KML 到 Map</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> 。 <a href='https://codepen.io'>CodePen</a>
</iframe>

你可以选择提供代理服务，用于访问可能未启用 CORS 的跨域资产。 Read 函数将尝试先使用 CORS 访问另一个域中的文件。 第一次无法使用 CORS 访问另一个域中的任何资源时，如果已提供代理服务，它只会请求其他文件。 Read 函数将文件 URL 附加到所提供的代理 URL 的末尾。 此代码段演示如何将代理服务传递到 read 函数：

```javascript
//Read a file from a URL or pass in a raw data as a string.
atlas.io.read('https://nonCorsDomain.example.com/mySuperCoolData.xml', {
    //Provide a proxy service
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
}).then(async r => {
    if (r) {
        // Some code goes here . . .
    }
});

```

下面的演示演示如何读取带分隔符的文件并将其呈现在地图上。 在这种情况下，代码使用具有空间数据列的 CSV 文件。

<br/>

<iframe height='500' scrolling='no' title='添加分隔文件' src='//codepen.io/azuremaps/embed/ExjXBEb/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 CodePen 上的 "通过 Azure Maps () <a href='https://codepen.io/azuremaps/pen/ExjXBEb/'>添加带分隔符的文件</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="write-spatial-data"></a>写入空间数据

空间 IO 模块中有两个主要写入函数。 `atlas.io.write`函数会生成一个字符串，而 `atlas.io.writeCompressed` 函数会生成压缩的 zip 文件。 压缩的 zip 文件将包含一个基于文本的文件，其中包含空间数据。 这两个函数都返回一项承诺，以将数据添加到文件。 而且，它们都可以编写以下任何数据： `SpatialDataSet` 、、、 `DataSource` `ImageLayer` `OgcMapLayer` 、功能集合、功能、几何或这些数据类型的任意组合的数组。 使用任一函数编写时，可以指定所需的文件格式。 如果未指定文件格式，则数据将以 KML 的形式写入。

下图演示了可与函数一起使用的大多数写入选项 `atlas.io.write` 。

<br/>

<iframe height='700' scrolling='no' title='空间数据写入选项' src='//codepen.io/azuremaps/embed/YzXxXPG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 CodePen 上的 "笔<a href='https://codepen.io/azuremaps/pen/YzXxXPG/'>空间数据写入选项</a>" Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 。 <a href='https://codepen.io'>CodePen</a>
</iframe>

## <a name="example-of-writing-spatial-data"></a>写入空间数据的示例

下面的示例允许您在地图上拖放并加载空间文件。 可以从映射中导出 GeoJSON 数据，并将其作为字符串或压缩文件写入其中一种受支持的空间数据格式。

<br/>

<iframe height='700' scrolling='no' title='将空间文件拖放到地图上' src='//codepen.io/azuremaps/embed/zYGdGoO/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 CodePen 上的 "<a href='https://codepen.io/azuremaps/pen/zYGdGoO/'>将空间文件拖放到地图</a>上" Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 。 <a href='https://codepen.io'>CodePen</a>
</iframe>

你可以选择提供代理服务，用于访问可能未启用 CORS 的跨域资产。 此代码片段演示了如何合并代理服务：

```javascript
atlas.io.read(data, {
    //Provide a proxy service
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
}).then(
    //Success
    function(r) {
        //some code goes here ...
    }
);
```

## <a name="read-and-write-well-known-text-wkt"></a>读取和写入 Well-Known 文本 (WKT) 

熟知[文本](https://en.wikipedia.org/wiki/Well-known_text_representation_of_geometry) (WKT) 是开放地理空间信息联盟 (OGC) 标准，用于将空间几何作为文本表示。 许多地理空间系统使用 PostGIS 插件支持 WKT，例如 Azure SQL 和 Azure PostgreSQL。 与大多数 OGC 标准一样，坐标的格式设置为 "经度纬度"，以与 "x y" 约定保持一致。 例如，可以使用 WKT 格式编写介于110和纬度45的点 `POINT(-110 45)` 。

可以使用函数读取已知文本 `atlas.io.ogc.WKT.read` ，并使用函数编写该文本 `atlas.io.ogc.WKT.write` 。

## <a name="examples-of-reading-and-writing-well-known-text-wkt"></a>读取和写入 Well-Known 文本 (WKT) 的示例

下面的代码演示如何读取众所周知的文本字符串 `POINT(-122.34009 47.60995)` ，并使用气泡图层在地图上呈现该字符串。

<br/>

<iframe height='500' scrolling='no' title='读取 Well-Known 文本' src='//codepen.io/azuremaps/embed/XWbabLd/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅在 CodePen 上通过 Azure Maps () <a href='https://codepen.io/azuremaps/pen/XWbabLd/'>读取 Well-Known 文本</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>。
</iframe>

下面的代码演示如何读取和写入已知文本。

<br/>

<iframe height='700' scrolling='no' title='读取和写入 Well-Known 文本' src='//codepen.io/azuremaps/embed/JjdyYav/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 CodePen 上的 "<a href='https://codepen.io/azuremaps/pen/JjdyYav/'>读取和写入 Well-Known 文本</a>" Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a> ") "。 <a href='https://codepen.io'>CodePen</a>
</iframe>

## <a name="read-and-write-gml"></a>读取和写入 GML

GML 是一个空间 XML 文件规范，通常用作其他 XML 规范的扩展。 可以使用函数将 GeoJSON 数据编写为具有 GML 标记的 XML `atlas.io.core.GmlWriter.write` 。 可以使用函数读取包含 GML 的 XML `atlas.io.core.GmlReader.read` 。 Read 函数有两个选项：

- `isAxisOrderLonLat`选项-坐标 "纬度"、"经度" 或 "经度，纬度" 的轴顺序在数据集之间可能会有所不同，并且这些数据集并非始终定义良好。 默认情况下，GML 读取器将坐标数据作为 "纬度，经度" 读取，但将此选项设置为 true 会将其读取为 "经度，纬度"。
- `propertyTypes`选项-此选项是键值查找表，其中的键是数据集中属性的名称。 值是在分析时将值强制转换为的对象类型。 支持的类型值为： `string` 、 `number` 、 `boolean` 和  `date` 。 如果某个属性不在查找表中或未定义该类型，则该属性将作为字符串进行分析。

`atlas.io.read`函数在 `atlas.io.core.GmlReader.read` 检测到输入数据为 xml 时将默认为函数，但数据不是其他支持空间 XML 格式之一。

`GmlReader`将分析具有以下 srid 之一的坐标：

- EPSG： 4326 (首选) 
- EPSG：4269、EPSG：4283、EPSG：4258、EPSG：4308、EPSG：4230、EPSG：4272、EPSG：4271、EPSG：4267、EPSG：4608、EPSG：4674可能出现错误的小边距。
- EPSG：3857、EPSG：102100、EPSG：3785、EPSG：900913、EPSG：102113、EPSG：41001、EPSG：54004

## <a name="more-resources"></a>更多资源

详细了解本文中使用的类和方法：

[atlas.io 静态函数](/javascript/api/azure-maps-spatial-io/atlas.io)

[SpatialDataSet](/javascript/api/azure-maps-spatial-io/atlas.spatialdataset)

[SpatialDataSetStats](/javascript/api/azure-maps-spatial-io/atlas.spatialdatasetstats)

[GmlReader](/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlreader)

[GmlWriter](/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlwriter)

[ogc. WKT 函数](/javascript/api/azure-maps-spatial-io/atlas.io.ogc.wkt)

[连接到 WFS 服务](spatial-io-connect-wfs-service.md)

[利用核心操作](spatial-io-core-operations.md)

[支持的数据格式详细信息](spatial-io-supported-data-format-details.md)


## <a name="next-steps"></a>后续步骤

有关可向地图添加的更多代码示例，请参阅以下文章：

[添加 OGC 地图层](spatial-io-add-ogc-map-layer.md)