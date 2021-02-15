---
title: 地域隔离区内的 GeoJSON 数据格式 |Microsoft Azure 映射
description: 了解 Azure Maps 地域隔离区内数据。 请参阅如何在检索相对于地域隔离区内的坐标位置时使用 GET 地域隔离区内和 POST 地域隔离区内 Api。
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: e880710b93a6764df50780e685c89b5f569b4ec0
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2020
ms.locfileid: "92897188"
---
# <a name="geofencing-geojson-data"></a>地理围栏 GeoJSON 数据

使用 Azure Maps [GET 地理围栏](/rest/api/maps/spatial/getgeofence)和 [POST 地理围栏](/rest/api/maps/spatial/postgeofence) API 可以检索某个坐标相对于所提供地理围栏或围栏集的邻近性。 本文详细介绍如何准备可在 Azure Maps GET 和 POST API 中使用的地理围栏数据。

地理围栏或地理围栏集的数据由 [rfc7946](https://tools.ietf.org/html/rfc7946) 中定义的、采用 `GeoJSON` 格式的 `Feature` 对象和 `FeatureCollection` 对象表示。 除此之外：

* GeoJSON 对象类型可以是 `Feature` 对象或 `FeatureCollection` 对象。
* 几何对象类型可以是 `Point`、`MultiPoint`、`LineString`、`MultiLineString`、`Polygon`、`MultiPolygon` 和 `GeometryCollection`。
* 所有特征属性应该包含用于标识地理围栏的 `geometryId`。
* 具有 `Point`、`MultiPoint`、`LineString`、`MultiLineString` 的特征必须在属性中包含 `radius`。 `radius` 值的计量单位为米，`radius` 值的范围为 1 到 10000。
* 具有 `polygon` 和 `multipolygon` 几何类型的特征没有半径属性。
* `validityTime` 是可选属性，可让用户为地理围栏数据设置过期时间和有效时间。 如果未指定该属性，则数据永不过期，而是一直有效。
* `expiredTime` 是地理围栏数据的过期日期和时间。 如果请求中 `userTime` 的值晚于此值，则将相应的地理围栏数据视为过期的数据，且不会查询这些数据。 基于这一点，此地理围栏数据的 geometryId 将包含在地理围栏响应中的 `expiredGeofenceGeometryId` 数组内。
* `validityPeriod` 是地理围栏有效时段的列表。 如果请求中 `userTime` 的值超出有效时段，则将相应的地理围栏数据视为无效，且不会查询这些数据。 此地理围栏数据的 geometryId 包含在地理围栏响应中的 `invalidPeriodGeofenceGeometryId` 数组内。 下表显示了 validityPeriod 元素的属性。

| 名称 | 类型 | 必需  | 说明 |
| :------------ |:------------: |:---------------:| :-----|
| startTime | datetime  | 是 | 有效时段的开始日期时间。 |
| endTime   | datetime  | 是 |  有效时段的结束日期时间。 |
| recurrenceType | 字符串 | false |   时段的重复类型。 值可为 `Daily`、`Weekly`、`Monthly` 或 `Yearly`。 默认值为 `Daily`。|
| businessDayOnly | Boolean | false |  指示数据是否仅在工作日有效。 默认值为 `false`。|


* 所有坐标值都表示为中定义的 "经度，纬度" `WGS84` 。
* 对于包含 `MultiPoint`、`MultiLineString`、`MultiPolygon` 或 `GeometryCollection` 的每个特征，属性将应用到所有元素。 例如：中的所有点 `MultiPoint` 都将使用相同的半径形成多个圆形地域隔离区内。
* 在点圆方案中，可以使用具有[扩展 GeoJSON 几何图形](./extend-geojson.md)中所述属性的 `Point` 几何对象来表示圆几何图形。      

以下地理围栏的示例请求正文表示为在 `GeoJSON` 中使用中心点和半径的圆形地理围栏几何图形。 该地理围栏数据的有效时段为从 2018年 10 月 22 日上午 9 点到下午 5 点，除周末外，每天都会重复。 `expiredTime` 指示如果请求中的 `userTime` 晚于 `2019-01-01`，则将此地理围栏数据视为过期。  

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "geometryId" : "1",
        "subType": "Circle",
        "radius": 500,
        "validityTime": 
        {
            "expiredTime": "2019-01-01T00:00:00",
            "validityPeriod": [
                {
                    "startTime": "2018-10-22T09:00:00",
                    "endTime": "2018-10-22T17:00:00",
                    "recurrenceType": "Daily",
                    "recurrenceFrequency": 1,
                    "businessDayOnly": true
                }
            ]
        }
    }
}
```