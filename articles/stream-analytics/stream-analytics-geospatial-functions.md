---
title: Azure 流分析地理空间函数的简介
description: 本文介绍了 Azure 流分析作业中使用的地理空间函数。
author: krishna0815
ms.author: krishmam
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: dc590593b9bff8f646ee6155d32a2ce3f9790f6e
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98625242"
---
# <a name="introduction-to-stream-analytics-geospatial-functions"></a>流分析地理空间函数的简介

Azure 流分析中的地理空间函数支持对流式处理的地理空间数据进行实时分析。 只需几行代码，即可为复杂的方案开发出生产级解决方案。 这些函数支持所有 WKT 类型和 GeoJSON 点、多边形和 LineString。

可从地理空间函数中受益的示例方案包括：

* 拼车
* 车队管理
* 资产跟踪
* 地域隔离
* 蜂窝站点之间的电话跟踪

流分析查询语言具有七个内置的地理空间函数：CreateLineString、CreatePoint、CreatePolygon、ST_DISTANCE、ST_OVERLAPS、ST_INTERSECTS 和 ST_WITHIN。

## <a name="createlinestring"></a>CreateLineString

`CreateLineString` 函数接受点并返回 GeoJSON LineString，这可在地图上被标绘成一个线条。 必须至少具有两个点才能创建 LineString。 LineString 点将按顺序相连。

下面的查询使用 `CreateLineString` 创建使用三个点的 LineString。 第一个点创建自流式处理输入数据，而另外两个点则是手动创建的。

```SQL 
SELECT  
     CreateLineString(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5))  
FROM input  
```  

### <a name="input-example"></a>输入示例  
  
|latitude|longitude|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>输出示例  

 {"type" : "LineString", "coordinates" : [ [-10.2, 3.0], [10.0, 10.0], [10.5, 10.5] ]}

 {"type" : "LineString", "coordinates" : [ [20.2321, -87.33], [10.0, 10.0], [10.5, 10.5] ]}

若要了解详细信息，请访问 [CreateLineString](/stream-analytics-query/createlinestring) 引用。

## <a name="createpoint"></a>CreatePoint

`CreatePoint` 函数接受纬度和经度，并返回一个 GeoJSON 点，这可在地图上被标绘出来。 维度和精度必须为 float 数据类型。

下面的示例查询使用 `CreatePoint` 创建使用流式处理输入数据中的纬度和经度的点。

```SQL 
SELECT  
     CreatePoint(input.latitude, input.longitude)  
FROM input 
```  

### <a name="input-example"></a>输入示例  
  
|latitude|longitude|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>输出示例
  
 {"type" : "Point", "coordinates" : [-10.2, 3.0]}  
  
 {"type" : "Point", "coordinates" : [20.2321, -87.33]}  

若要了解详细信息，请访问 [CreatePoint](/stream-analytics-query/createpoint) 引用。

## <a name="createpolygon"></a>CreatePolygon

`CreatePolygon` 函数接受点并返回一条 GeoJSON 多边形记录。 这些点的顺序必须遵循向右侧的环方向，否则为逆时针方向。 假设以声明的顺序从一个点走到另一个点。 在整个时间段内，此多边形的中心将偏向左侧。

下面的示例查询使用 `CreatePolygon` 通过三个点来创建多边形。 前两个点是手动创建的，最后一个点是通过输入数据创建的。

```SQL 
SELECT  
     CreatePolygon(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5), CreatePoint(input.latitude, input.longitude))  
FROM input  
```  

### <a name="input-example"></a>输入示例  
  
|latitude|longitude|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>输出示例  

 {"type" : "Polygon", "coordinates" : [[ [-10.2, 3.0], [10.0, 10.0], [10.5, 10.5], [-10.2, 3.0] ]]}
 
 {"type" : "Polygon", "coordinates" : [[ [20.2321, -87.33], [10.0, 10.0], [10.5, 10.5], [20.2321, -87.33] ]]}

若要了解详细信息，请访问 [CreatePolygon](/stream-analytics-query/createpolygon) 引用。


## <a name="st_distance"></a>ST_DISTANCE
`ST_DISTANCE`函数返回两个几何图形之间的距离（以米为单位）。 

下面的查询使用 `ST_DISTANCE` 在加油站距离汽车不足 10 千米时生成事件。

```SQL
SELECT Cars.Location, Station.Location 
FROM Cars c  
JOIN Station s ON ST_DISTANCE(c.Location, s.Location) < 10 * 1000
```

若要了解详细信息，请访问 [ST_DISTANCE](/stream-analytics-query/st-distance) 引用。

## <a name="st_overlaps"></a>ST_OVERLAPS
`ST_OVERLAPS`函数比较两个几何图形。 如果几何图形重叠，则函数返回1。 如果几何图形不重叠，则函数返回0。 

下面的查询使用 `ST_OVERLAPS` 在某建筑位于可能发生洪灾的区域内时生成事件。

```SQL
SELECT Building.Polygon, Building.Polygon 
FROM Building b 
JOIN Flooding f ON ST_OVERLAPS(b.Polygon, b.Polygon) 
```

下面的示例查询在风暴即将袭击汽车时生成事件。

```SQL
SELECT Cars.Location, Storm.Course
FROM Cars c, Storm s
JOIN Storm s ON ST_OVERLAPS(c.Location, s.Course)
```

若要了解详细信息，请访问 [ST_OVERLAPS](/stream-analytics-query/st-overlaps) 引用。

## <a name="st_intersects"></a>ST_INTERSECTS
`ST_INTERSECTS`函数比较两个几何图形。 如果几何图形相交，则函数返回1。 如果几何图形不彼此相交，则该函数返回0。

下面的示例查询使用 `ST_INTERSECTS` 来确定柏油路是否与泥路相交。

```SQL 
SELECT  
     ST_INTERSECTS(input.pavedRoad, input.dirtRoad)  
FROM input  
```  

### <a name="input-example"></a>输入示例  
  
|datacenterArea|stormArea|  
|--------------------|---------------|  
|{"type":"LineString", "coordinates": [ [-10.0, 0.0], [0.0, 0.0], [10.0, 0.0] ]}|{"type":"LineString", "coordinates": [ [0.0, 10.0], [0.0, 0.0], [0.0, -10.0] ]}|  
|{"type":"LineString", "coordinates": [ [-10.0, 0.0], [0.0, 0.0], [10.0, 0.0] ]}|{"type":"LineString", "coordinates": [ [-10.0, 10.0], [0.0, 10.0], [10.0, 10.0] ]}|  
  
### <a name="output-example"></a>输出示例  

 1  
  
 0  

若要了解详细信息，请访问 [ST_INTERSECTS](/stream-analytics-query/st-intersects) 引用。

## <a name="st_within"></a>ST_WITHIN
`ST_WITHIN`函数确定几何图形是否在另一个几何图形内。 如果第一个包含在最后一个中，则函数将返回1。 如果第一个几何图形不位于最后一个几何图形内，则该函数将返回0。

下面的示例查询使用 `ST_WITHIN` 来确定交付目标点是否位于给定的仓库多边形内。

```SQL 
SELECT  
     ST_WITHIN(input.deliveryDestination, input.warehouse)  
FROM input 
```  

### <a name="input-example"></a>输入示例  
  
|deliveryDestination|warehouse|  
|-------------------------|---------------|  
|{"type":"Point", "coordinates": [76.6, 10.1]}|{"type":"Polygon", "coordinates": [ [0.0, 0.0], [10.0, 0.0], [10.0, 10.0], [0.0, 10.0], [0.0, 0.0] ]}|  
|{"type":"Point", "coordinates": [15.0, 15.0]}|{"type":"Polygon", "coordinates": [ [10.0, 10.0], [20.0, 10.0], [20.0, 20.0], [10.0, 20.0], [10.0, 10.0] ]}|  
  
### <a name="output-example"></a>输出示例  

 0  
  
 1  

若要了解详细信息，请访问 [ST_WITHIN](/stream-analytics-query/st-within) 引用。

## <a name="next-steps"></a>后续步骤

* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-real-time-fraud-detection.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 流分析管理 REST API 参考](/rest/api/streamanalytics/)
