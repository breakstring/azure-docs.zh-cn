---
title: 支持的数据格式详细信息 |Microsoft Azure 映射
description: 了解如何在空间 IO 模块中分析分隔空间数据。
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 7227813f607ca18ee50f503a30b290414f333e21
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91310163"
---
# <a name="supported-data-format-details"></a>支持的数据格式详细信息

本文提供对所有 XML 标记和 Well-Known 文本几何类型的读取和写入支持的详细信息。 它还详细说明了如何在空间 IO 模块中分析分隔的空间数据。

## <a name="supported-xml-namespaces"></a>支持的 XML 命名空间

空间 IO 模块支持以下命名空间中的 XML 标记。

| 命名空间前缀 | 命名空间 URI   | 注意                                                                    |
|:------------------|:-----------------|:----------------------------------------|
| `atom`           | `http://www.w3.org/2005/Atom`   |                                         |
| `geo`            | `http://www.w3.org/2003/01/geo/wgs84_pos#`  | GeoRSS 文件中的只读支持。           |
| `georss`         | `http://www.georss.org/georss`  |                                                |
| `geourl`         | `http://geourl.org/rss/module/` | GeoRSS 文件中的只读支持。                       |
| `gml`            | `http://www.opengis.net/gml`    |                                                        |
| `gpx`            | `http://www.topografix.com/GPX/1/1` |                                                   |
| `gpxx`           | `http://www.garmin.com/xmlschemas/GpxExtensions/v3` | .GPX) 文件中的只读支持。 分析并使用 DisplayColor。 添加到形状元数据的所有其他属性。 |
| `gpx_style`      | `http://www.topografix.com/GPX/gpx_style/0/2`      | 在 .GPX) 文件中受支持。 使用线条颜色。 |
| `gx`             | `http://www.google.com/kml/ext/2.2` |                                                      |
| `kml`            | `http://www.opengis.net/kml/2.2`    |                                                      |
| `rss`            |                                 | 只读。 GeoRSS 使用 Atom 格式写入。              |

## <a name="supported-xml-elements"></a>支持的 XML 元素

空间 IO 模块支持以下 XML 元素。 不受支持的任何 XML 标记将转换为 JSON 对象。 然后，每个标记将作为属性添加到 `properties` 父形状或层的字段中。

### <a name="kml-elements"></a>KML 元素

空间 IO 模块支持以下 KML 元素。

| 元素名称         | 读取    | 写入   | 注意                                                                                                                      |
|----------------------|---------|---------|----------------------------------------------------------------------------------------------------------------------------|
| `address`            | 部分 | 是     | 对象经过分析，但不用于定位形状。                                                                    |
| `AddressDetails`     | 部分 | 否      | 对象经过分析，但不用于定位形状。                                                                    |
| `atom:author`        | 是     | 是     |                                                                                                                            |
| `atom:link`          | 是     | 是     |                                                                                                                            |
| `atom:name`          | 是     | 是     |                                                                                                                            |
| `BalloonStyle`       | 部分 | 部分 | 不支持 `displayMode`。 转换为 `PopupTemplate` 。 若要编写，请添加一个 `popupTemplate` 属性作为要为其编写的功能的属性。 |
| `begin`              | 是     | 是     |                                                                                                                            |
| `color`              | 是     | 是     | 包括 `#AABBGGRR` 和 `#BBGGRR` 。 分析为 CSS 颜色字符串                                                           |
| `colorMode`          | 是     | 否      |                                                                                                                            |
| `coordinates`        | 是     | 是     |                                                                                                                            |
| `Data`               | 是     | 是     |                                                                                                                            |
| `description`        | 是     | 是     |                                                                                                                            |
| `displayName`        | 是     | 是     |                                                                                                                            |
| `Document`           | 是     | 是     |                                                                                                                            |
| `drawOrder`          | 部分 | 否      | 阅读地面覆盖，并使用对其进行排序。 
| `east`               | 是     | 是     |                                                                                                                            |
| `end`                | 是     | 是     |                                                                                                                            |
| `ExtendedData`       | 是     | 是     | 支持窗体的非类型化的 `Data` 、 `SimpleData` 或 `Schema` 和实体替换 `$[dataName]` 。                      |
| `extrude`            | 部分 | 部分 | 仅多边形支持。 具有不同高度的多边形的 MultiGeometry 将被分解为单独的功能。 不支持线条样式。 海拔高度为0的多边形将呈现为平面多边形。 在读取时，外部环中第一个坐标的海拔将作为多边形的 height 属性添加。 然后，第一个坐标的海拔将用于呈现地图上的多边形。 |
| `fill`               | 是     | 是     |                                                                                                                            |
| `Folder`             | 是     | 是     |                                                                                                                            |
| `GroundOverlay`      | 是     | 是     | `color` 不受支持                                                                                                   |
| `heading`            | 部分 | 否      | 分析但不呈现 `SimpleDataLayer` 。 仅当数据存储在形状的属性中时才写入。                 |
| `hotSpot`            | 是     | 部分 | 仅当数据存储在形状的属性中时才写入。 仅将单元输出为 "像素"。                         |
| `href`               | 是     | 是     |                                                                                                                            |
| `Icon`               | 部分 | 部分 | 分析但不呈现 `SimpleDataLayer` 。 如果形状包含 URI 数据，则仅写入该形状的图标属性。 仅支持 `href`。 |
| `IconStyle`          | 部分 | 部分 | `icon``heading` `colorMode` 会分析、、和 `hotspots` 值，但不会对其进行呈现`SimpleDataLayer`         |
| `innerBoundaryIs`    | 是     | 是     |                                                                                                                            |
| `kml`                | 是     | 是     |                                                                                                                            |
| `LabelStyle`         | 否      | 否      |                                                                                                                            |
| `LatLonBox`          | 是     | 是     |                                                                                                                            |
| `gx:LatLonQuad`      | 是     | 是     |                                                                                                                            |
| `LinearRing`         | 是     | 是     |                                                                                                                            |
| `LineString`         | 是     | 是     |                                                                                                                            |
| `LineStyle`          | 是     | 是     | 不支持 `colorMode`。                                                                                         |
| `Link`               | 是     | 否      | `href`网络链接仅支持属性。                                                                   |
| `MultiGeometry`      | 部分 | 部分 | 读取时可能会分解为单独的功能。                                                                     |
| `name`               | 是     | 是     |                                                                                                                            |
| `NetworkLink`        | 是     | 否      | 链接需要位于文档所在的同一域中。                                                                  |
| `NetworkLinkControl` | 否      | 否      |                                                                                                                            |
| `north`              | 是     | 是     |                                                                                                                            |
| `open`               | 是     | 是     |                                                                                                                            |
| `outerBoundaryIs`    | 是     | 是     |                                                                                                                            |
| `outline`            | 是     | 是     |                                                                                                                            |
| `overlayXY`          | 否      | 否      |                                                                                                                            |
| `Pair`               | 部分 | 否      | 仅 `normal` 支持中的样式 `StyleMap` 。 不支持 `highlight`。                                   |
| `phoneNumber`        | 是     | 是     |                                                                                                                            |
| `PhotoOverlay`       | 否      | 否      |                                                                                                                            |
| `Placemark`          | 是     | 是     |                                                                                                                            |
| `Point`              | 是     | 是     |                                                                                                                            |
| `Polygon`            | 是     | 是     |                                                                                                                            |
| `PolyStyle`          | 是     | 是     |                                                                                                                            |
| `Region`             | 部分 | 部分 | `LatLongBox` 在文档级别支持。                                                                      |
| `rotation`           | 否      | 否      |                                                                                                                            |
| `rotationXY`         | 否      | 否      |                                                                                                                            |
| `scale`              | 否      | 否      |                                                                                                                            |
| `Schema`             | 是     | 是     |                                                                                                                            |
| `SchemaData`         | 是     | 是     |                                                                                                                            |
| `schemaUrl`          | 部分 | 是     | 不支持从不包含在 KMZ 中的外部文档加载样式。                             |
| `ScreenOverlay`      | 否      | 否      |                                                                                                                            |
| `screenXY`           | 否      | 否      |                                                                                                                            |
| `SimpleData`         | 是     | 是     |                                                                                                                            |
| `SimpleField`        | 是     | 是     |                                                                                                                            |
| `size`               | 否      | 否      |                                                                                                                            |
| `Snippet`            | 部分 | 部分 | `maxLines` 特性被忽略。                                                                                  |
| `south`              | 是     | 是     |                                                                                                                            |
| `Style`              | 是     | 是     |                                                                                                                            |
| `StyleMap`           | 部分 | 否      | 仅支持中的正常样式 `StyleMap` 。                                                                        |
| `styleUrl`           | 部分 | 是     | 外部样式 Url 不受支持。                                                                         |
| `text`               | 是     | 是     | `$[geDirections]`不支持的替换                                                                          |
| `textColor`          | 是     | 是     |                                                                                                                            |
| `TimeSpan`           | 是     | 是     |                                                                                                                            |
| `TimeStamp`          | 是     | 是     |                                                                                                                            |
| `value`              | 是     | 是     |                                                                                                                            |
| `viewRefreshMode`    | 部分 | 否      |  如果指向 WMS 服务，则仅 `onStop` 支持地面覆盖。 将追加 `BBOX=[bboxWest],[bboxSouth],[bboxEast],[bboxNorth]` 到 URL，并在地图移动时进行更新。  |
| `visibility`         | 是     | 是     |                                                                                                                            |
| `west`               | 是     | 是     |                                                                                                                            |
| `when`               | 是     | 是     |                                                                                                                            |
| `width`              | 是     | 是     |                                                                                                                            |

### <a name="georss-elements"></a>GeoRSS 元素

空间 IO 模块支持以下 GeoRSS 元素。

| 元素名称             | 读取    | 写入 | 注意                                                                                          |
|--------------------------|---------|-------|------------------------------------------------------------------------------------------------|
| `atom:author`            | 是     | 是   |                                                                                                |
| `atom:category`          | 是     | 是   |                                                                                                |
| `atom:content`           | 是     | 是   |                                                                                                |
| `atom:contributor`       | 是     | 是   |                                                                                                |
| `atom:email`             | 是     | 是   |                                                                                                |
| `atom:entry`             | 是     | 是   |                                                                                                |
| `atom:feed`              | 是     | 是   |                                                                                                |
| `atom:icon`              | 是     | 是   |                                                                                                |
| `atom:id`                | 是     | 是   |                                                                                                |
| `atom:link`              | 是     | 是   |                                                                                                |
| `atom:logo`              | 是     | 是   |                                                                                                |
| `atom:name`              | 是     | 是   |                                                                                                |
| `atom:published`         | 是     | 是   |                                                                                                |
| `atom:rights`            | 是     | 是   |                                                                                                |
| `atom:source`            | 是     | 是   |                                                                                                |
| `atom:subtitle`          | 是     | 是   |                                                                                                |
| `atom:summary`           | 是     | 是   |                                                                                                |
| `atom:title`             | 是     | 是   |                                                                                                |
| `atom:updated`           | 是     | 是   |                                                                                                |
| `atom:uri`               | 是     | 是   |                                                                                                |
| `geo:lat`                | 是     | 否    | 编写为 `georss:point` 。                                                                   |
| `geo:lon`                | 是     | 否    | 编写为 `georss:point` 。                                                                   |
| `geo:long`               | 是     | 否    | 编写为 `georss:point` 。                                                                   |
| `georss:box`             | 是     | 否    | 作为多边形读取并给定 `subType` 属性 "矩形"                                |
| `georss:circle`          | 是     | 是   |                                                                                                |
| `georss:elev`            | 是     | 是   |                                                                                                |
| `georss:featurename`     | 是     | 是   |                                                                                                |
| `georss:featuretypetag`  | 是     | 是   |                                                                                                |
| `georss:floor`           | 是     | 是   |                                                                                                |
| `georss:line`            | 是     | 是   |                                                                                                |
| `georss:point`           | 是     | 是   |                                                                                                |
| `georss:polygon`         | 是     | 是   |                                                                                                |
| `georss:radius`          | 是     | 是   |                                                                                                |
| `georss:relationshiptag` | 是     | 是   |                                                                                                |
| `georss:where`           | 是     | 是   |                                                                                                |
| `geourl:latitude`        | 是     | 否    | 编写为 `georss:point` 。                                                                   |
| `geourl:longitude`       | 是     | 否    | 编写为 `georss:point` 。                                                                   |
| `position`               | 是     | 否    | 某些 XML 源会将 GML 与位置标记一起打包，而不是使用 `georss:where` 标记来包装。 将读取此标记，但将使用标记写入 `georss:where` 。 |
| `rss`                    | 是     | 否    | GeoRSS 以 ATOM 格式编写的。                                                                 |
| `rss:author`             | 是     | 部分 | 编写为 `atom:author` 。                                                                 |
| `rss:category`           | 是     | 部分 | 编写为 `atom:category` 。                                                               |
| `rss:channel`            | 是     | 否    |                                                                                                |
| `rss:cloud`              | 是     | 否    |                                                                                                |
| `rss:comments`           | 是     | 否    |                                                                                                |
| `rss:copyright`          | 是     | 部分 | 作为 if 形状编写时，没有 `atom:rights` `rights` `properties` 属性。       |
| `rss:description`        | 是     | 部分 | 作为 if 形状编写时，没有 `atom:content` `content` `properties` 属性。      |
| `rss:docs`               | 是     | 否    |                                                                                                |
| `rss:enclosure`          | 是     | 否    |                                                                                                |
| `rss:generator`          | 是     | 否    |                                                                                                |
| `rss:guid`               | 是     | 部分 | 作为 if 形状编写时，没有 `atom:id` `id` `properties` 属性。         |
| `rss:image`              | 是     | 部分 | 作为 if 形状编写时，没有 `atom:logo` `logo` `properties` 属性。      |
| `rss:item`               | 是     | 部分 | 编写为 `atom:entry` 。                                                                  |
| `rss:language`           | 是     | 否    |                                                                                                |
| `rss:lastBuildDate`      | 是     | 部分 | 作为 if 形状编写时，没有 `atom:updated` `updated` `properties` 属性。     |
| `rss:link`               | 是     | 部分 | 编写为 `atom:link` 。                                                                   |
| `rss:managingEditor`     | 是     | 部分 | 编写为 `atom:contributor` 。                                                            |
| `rss:pubDate`            | 是     | 部分 | 作为 if 形状编写时，没有 `atom:published` `published` `properties` 属性。  |
| `rss:rating`             | 是     | 否    |                                                                                                |
| `rss:skipDays`           | 是     | 否    |                                                                                                |
| `rss:skipHours`          | 是     | 否    |                                                                                                |
| `rss:source`             | 是     | 部分 | 编写为 `atom:source` 包含的 `atom:link` 。                                       |
| `rss:textInput`          | 是     | 否    |                                                                                                |
| `rss:title`              | 是     | 部分 | 编写为 `atom:title` 。                                                                  |
| `rss:ttl`                | 是     | 否    |                                                                                                |
| `rss:webMaster`          | 是     | 否    |                                                                                                |

### <a name="gml-elements"></a>GML 元素

空间 IO 模块支持以下 GML 元素。 

| 元素名称            | 读取 | 写入 | 注意                                                                                  |
|-------------------------|------|-------|----------------------------------------------------------------------------------------|
| `gml:coordinates`       | 是  | 否    | 编写为 `gml:posList` 。                                                              |
| `gml:curveMember`       | 是  | 否    |                                                                                        |
| `gml:curveMembers`      | 是  | 否    |                                                                                        |
| `gml:Box`               | 是  | 否    | 编写为 `gml:Envelope` 。                                                             |
| `gml:description`       | 是  | 是   |                                                                                        |
| `gml:Envelope`          | 是  | 是   |                                                                                        |
| `gml:exterior`          | 是  | 是   |                                                                                        |
| `gml:Feature`           | 是  | 否    | 编写为形状。                                                                    |
| `gml:FeatureCollection` | 是  | 否    | 编写为几何图形集合。                                                      |
| `gml:featureMember`     | 是  | 否    | 编写为几何图形集合。                                                      |
| `gml:geometry`          | 是  | 否    | 编写为形状。                                                                    |
| `gml:geometryMember`    | 是  | 是   |                                                                                        |
| `gml:geometryMembers`   | 是  | 是   |                                                                                        |
| `gml:identifier`        | 是  | 是   |                                                                                        |
| `gml:innerBoundaryIs`   | 是  | 否    | 使用编写 `gml.interior` 。                                                          |
| `gml:interior`          | 是  | 是   |                                                                                        |
| `gml:LinearRing`        | 是  | 是   |                                                                                        |
| `gml:LineString`        | 是  | 是   |                                                                                        |
| `gml:lineStringMember`  | 是  | 是   |                                                                                        |
| `gml:lineStringMembers` | 是  | 否    |                                                                                        |
| `gml:MultiCurve`        | 是  | 否    | 仅读取 `gml:LineString` 成员。 编写方式 `gml.MultiLineString`                  |
| `gml:MultiGeometry`     | 部分  | 部分   | 仅读取 FeatureCollection。                                              |
| `gml:MultiLineString`   | 是  | 是   |                                                                                        |
| `gml:MultiPoint`        | 是  | 是   |                                                                                        |
| `gml:MultiPolygon`      | 是  | 是   |                                                                                        |
| `gml:MultiSurface`      | 是  | 否    | 仅读取 `gml:Polygon` 成员。 编写方式 `gml.MultiPolygon`                        |
| `gml:name`              | 是  | 是   |                                                                                        |
| `gml:outerBoundaryIs`   | 是  | 否    | 使用编写 `gml.exterior` 。                                                          |
| `gml:Point`             | 是  | 是   |                                                                                        |
| `gml:pointMember`       | 是  | 是   |                                                                                        |
| `gml:pointMembers`      | 是  | 否    |                                                                                        |
| `gml:Polygon`           | 是  | 是   |                                                                                        |
| `gml:polygonMember`     | 是  | 是   |                                                                                        |
| `gml:polygonMembers`    | 是  | 否    |                                                                                        |
| `gml:pos`               | 是  | 是   |                                                                                        |
| `gml:posList`           | 是  | 是   |                                                                                        |
| `gml:surfaceMember`     | 是  | 是   |                                                                                        |

#### <a name="additional-notes"></a>其他说明

- 将搜索成员元素，查找可能在子元素中隐藏的几何图形。 此搜索操作是必需的，因为从 GML 扩展的 XML 格式不能将几何图形作为成员元素的直接子级。
- `srsName` 部分支持 WGS84 坐标和以下代码：[EPSG： 4326](https://epsg.io/4326)) 和 web Mercator ([EPSG： 3857](https://epsg.io/3857) 或其替代代码之一。 任何其他坐标系统都将按原样分析为 WGS84。
- 除非在读取 XML 源时指定，否则，将根据 XML 源中的提示来确定轴顺序。 为 "纬度，经度" 轴顺序提供了一个首选项。
- 除非在写入 GML 文件时为属性指定了自定义 GML 命名空间，否则将不会添加其他属性信息。

### <a name="gpx-elements"></a>.GPX) 元素

空间 IO 模块支持以下 .GPX) 元素。

| 元素名称             | 读取    | 写入   | 注意                                                                                       |
|--------------------------|---------|---------|---------------------------------------------------------------------------------------------|
| `gpx:ageofdgpsdata`      | 是     | 是     |                                                                                             |
| `gpx:author`             | 是     | 是     |                                                                                             |
| `gpx:bounds`             | 是     | 是     | 读取时转换为 LocationRect。                                                    |
| `gpx:cmt`                | 是     | 是     |                                                                                             |
| `gpx:copyright`          | 是     | 是     |                                                                                             |
| `gpx:desc`               | 是     | 是     | 在读取时复制到 description 属性以与其他 XML 格式对齐。               |
| `gpx:dgpsid`             | 是     | 是     |                                                                                             |
| `gpx:ele`                | 是     | 是     |                                                                                             |
| `gpx:extensions`         | 部分 | 部分 | 读取时，将提取样式信息。 所有其他扩展将平展为一个简单的 JSON 对象。 仅写入形状样式信息。 |
| `gpx:geoidheight`        | 是     | 是     |                                                                                             |
| `gpx:gpx`                | 是     | 是     |                                                                                             |
| `gpx:hdop`               | 是     | 是     |                                                                                             |
| `gpx:link`               | 是     | 是     |                                                                                             |
| `gpx:magvar`             | 是     | 是     |                                                                                             |
| `gpx:metadata`           | 是     | 是     |                                                                                             |
| `gpx:name`               | 是     | 是     |                                                                                             |
| `gpx:pdop`               | 是     | 是     |                                                                                             |
| `gpx:rte`                | 是     | 是     |                                                                                             |
| `gpx:rtept`              | 是     | 是     |                                                                                             |
| `gpx:sat`                | 是     | 是     |                                                                                             |
| `gpx:src`                | 是     | 是     |                                                                                             |
| `gpx:sym`                | 是     | 是     | 捕获值，但不用于更改图钉图标。                               |
| `gpx:text`               | 是     | 是     |                                                                                             |
| `gpx:time`               | 是     | 是     |                                                                                             |
| `gpx:trk`                | 是     | 是     |                                                                                             |
| `gpx:trkpt`              | 是     | 是     |                                                                                             |
| `gpx:trkseg`             | 是     | 是     |                                                                                             |
| `gpx:type`               | 是     | 是     |                                                                                             |
| `gpx:vdop`               | 是     | 是     |                                                                                             |
| `gpx:wpt`                | 是     | 是     |                                                                                             |
| `gpx_style:color`        | 是     | 是     |                                                                                             |
| `gpx_style:line`         | 部分 | 部分 | `color``opacity`支持、、 `width` 和 `lineCap` 。                                           |
| `gpx_style:opacity`      | 是     | 是     |                                                                                             |
| `gpx_style:width`        | 是     | 是     |                                                                                             |
| `gpxx:DisplayColor`      | 是     | 否      | 用于指定形状的颜色。 写入时， `gpx_style:line` 将改为使用颜色。  |
| `gpxx:RouteExtension`    | 部分 | 否      | 所有属性都读入 `properties` 。 仅使用 `DisplayColor`。                     |
| `gpxx:TrackExtension`    | 部分 | 否      | 所有属性都读入 `properties` 。 仅使用 `DisplayColor`。                     |
| `gpxx:WaypointExtension` | 部分 | 否      | 所有属性都读入 `properties` 。 仅使用 `DisplayColor`。                     |
| `gpx:keywords`           | 是     | 是     |                                                                                             |
| `gpx:fix`                | 是     | 是     |                                                                                             |

#### <a name="additional-notes"></a>其他说明

写入时;

- MultiPoints 将分解为单个 waypoints。
- 多边形和 MultiPolygons 将以磁道的形式写入。 
  
## <a name="supported-well-known-text-geometry-types"></a>支持的 Well-Known 文本几何类型

| Geometry 类型 | 读取 | 写入 |
|--------------|:----:|:-----:|
| 点 | x | x |
| 点 Z | x | x | 
| 点 M | x | x<sup>[2]</sup> |
| 点 ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| LINESTRING | x | x |
| LINESTRING Z | x | x | 
| LINESTRING M | x | x<sup>[2]</sup> |
| LINESTRING ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| 各 | x | x |
| 多边形 Z | x | x |
| 多边形 M | x | x<sup>[2]</sup> |
| 多边形 ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| 单 | x | x |
| MULTIPOINT Z | x | x | 
| MULTIPOINT M | x | x<sup>[2]</sup> |
| POMULTIPOINTINT ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| MULTILINESTRING | x | x |
| MULTILINESTRING Z | x | x | 
| MULTILINESTRING M | x | x<sup>[2]</sup> |
| MULTILINESTRING ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| MULTIPOLYGON | x | x |
| MULTIPOLYGON Z | x | x | 
| MULTIPOLYGON M | x | x<sup>[2]</sup> |
| MULTIPOLYGON ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| GEOMETRYCOLLECTION | x | x |
| GEOMETRYCOLLECTION Z | x | x | 
| GEOMETRYCOLLECTION M | x | x<sup>[2]</sup> | 
| GEOMETRYCOLLECTION ZM | x<sup>[1]</sup><sup>[2]</sup> | x | 

\[1 \] 仅捕获 Z 参数，并将其作为第三个值添加到位置值中。

\[未捕获到2个 \] M 参数。

## <a name="delimited-spatial-data-support"></a>分隔空间数据支持

分隔空间数据（如逗号分隔值文件 (CSV) ）通常包含包含空间数据的列。 例如，可能存在包含纬度和经度信息的列。 在 Well-Known 文本格式中，可能有包含空间几何数据的列。

### <a name="spatial-data-column-detection"></a>空间数据列检测

在读取包含空间数据的带分隔符的文件时，将分析该标头以确定哪些列包含位置字段。 如果标头包含类型信息，则它将用于将单元值强制转换为适当的类型。 如果未指定标头，则将分析第一行并将其用于生成标头。 分析第一行时，将执行检查，以将具有以下名称的列名称与不区分大小写的方法匹配。 如果文件中有两个或多个名称，则名称的顺序就是优先级。

#### <a name="latitude"></a>纬度

- `latitude`
- `lat`
- `latdd`
- `lat_dd`
- `latitude83`
- `latdecdeg`
- `y`
- `ycenter`
- `point-y`

#### <a name="longitude"></a>经度

- `longitude`
- `lon`
- `lng`
- `long`
- `longdd`
- `long_dd`
- `longitude83`
- `longdecdeg`
- `x`
- `xcenter`
- `point-x`

#### <a name="elevation"></a>Elevation

- `elevation`
- `elv`
- `altitude`
- `alt`
- `z`

#### <a name="geography"></a>地理位置

将扫描第一行数据，查找 Well-Known 文本格式的字符串。 

### <a name="delimited-data-column-types"></a>分隔的数据列类型

在扫描标题行时，将提取列名中的任何类型信息，并将其用于转换列中的单元格。 下面是一个类型值为 "ColumnName (typeName) " 的列名称的示例。 支持以下不区分大小写的类型名称：

#### <a name="numbers"></a>数字

- edm
- int
- long
- edm。 double
- FLOAT
- double
- 数字

#### <a name="booleans"></a>布尔型

- edm
- bool
- boolean

#### <a name="dates"></a>日期

- edm. datetime
- date
- datetime

#### <a name="geography"></a>地理位置

- edm
- geography

#### <a name="strings"></a>字符串

- edm
- varchar
- text
- 字符串

如果没有可以从标头中提取类型信息，并且在读取时启用了动态键入选项，则会对每个单元格进行单独分析以确定最适合转换为的数据类型。

## <a name="next-steps"></a>后续步骤

有关可向地图添加的更多代码示例，请参阅以下文章：

[读取和写入空间数据](spatial-io-read-write-spatial-data.md)
