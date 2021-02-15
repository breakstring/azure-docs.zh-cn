---
title: 高级筛选-Azure 事件网格 IoT Edge |Microsoft Docs
description: IoT Edge 上的事件网格中的高级筛选。
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 64b8956c47cbdbf31bb8253dac0c1e1f12833bf7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001042"
---
# <a name="advanced-filtering"></a>高级筛选
事件网格允许在 json 有效负载中的任何属性上指定筛选器。 这些筛选器将建模为 `AND` 条件集，每个外部条件都有可选的内部 `OR` 条件。 对于每个 `AND` 条件，请指定以下值：

* `OperatorType` -比较的类型。
* `Key` -要对其应用筛选器的属性的 json 路径。
* `Value` -运行筛选器所依据的引用值 (或) `Values` -对其运行筛选器的引用值集。

## <a name="json-syntax"></a>JSON 语法

高级筛选器的 JSON 语法如下所示：

```json
{
    "filter": {
        "advancedFilters": [{
                "operatorType": "NumberGreaterThanOrEquals",
                "key": "Data.Key1",
                "value": 5
            }, {
                "operatorType": "StringContains",
                "key": "Subject",
                "values": ["container1", "container2"]
            }
        ]
    }
}
```

## <a name="filtering-on-array-values"></a>筛选数组值

事件网格目前不支持对值数组进行筛选。 如果传入事件的一个数组值用于高级筛选器的键，则匹配操作将失败。 传入事件结束时与事件订阅不匹配。

## <a name="and-or-not-semantics"></a>和-或-NOT 语义

请注意，在前面给出的 json 示例中， `AdvancedFilters` 是一个数组。 将每个 `AdvancedFilter` 数组元素视为一个 `AND` 条件。

对于支持多个值的运算符 (如 `NumberIn` 、 `NumberNotIn` 、 `StringIn` 等 ) ，每个值都被视为一个 `OR` 条件。 因此， `StringBeginsWith("a", "b", "c")` 将匹配以或或开头的任何字符串值 `a` `b` `c` 。

> [!CAUTION]
> NOT 运算符- `NumberNotIn` 并 `StringNotIn` 在字段中给定的每个值上表现为和条件 `Values` 。
>
> 如果不这样做，会使筛选器 Accept-All 筛选器并实现筛选的目的。

## <a name="floating-point-rounding-behavior"></a>浮点舍入行为

事件网格使用 `decimal` .net 类型处理所有数字值。 在事件订阅 JSON 中指定的数值不受浮点舍入行为限制。

## <a name="case-sensitivity-of-string-filters"></a>字符串筛选器的区分大小写

所有字符串比较都不区分大小写。 目前没有办法改变此行为。

## <a name="allowed-advanced-filter-keys"></a>允许的高级筛选键

`Key`属性可以是已知的顶级属性，也可以是具有多个点的 json 路径，其中每个点表示单步执行嵌套 json 对象。

`$`与 JSONPath 规范不同，事件网格对密钥中的字符没有任何特殊含义。

### <a name="event-grid-schema"></a>事件网格架构

对于事件网格架构中的事件：

* ID
* 主题
* 使用者
* EventType
* DataVersion
* Data. Prop1
* Data. Prop3. Prop4. Prop5

### <a name="custom-event-schema"></a>自定义事件架构

`Key`自定义事件架构中没有任何限制，因为事件网格不会在有效负载上强制任何信封架构。

## <a name="numeric-single-value-filter-examples"></a>单值数值筛选器示例

* NumberGreaterThan
* NumberGreaterThanOrEquals
* NumberLessThan
* NumberLessThanOrEquals

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "NumberGreaterThan",
                "key": "Data.Key1",
                "value": 5
            },
            {
                "operatorType": "NumberGreaterThanOrEquals",
                "key": "Data.Key2",
                "value": *456
            },
            {
                "operatorType": "NumberLessThan",
                "key": "Data.P*P2.P3",
                "value": 1000
            },
            {
                "operatorType": "NumberLessThanOrEquals",
                "key": "Data.P*P2",
                "value": 999
            }
        ]
    }
}
```

## <a name="numeric-range-value-filter-examples"></a>数值范围-值筛选器示例

* NumberIn
* NumberNotIn

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "NumberIn",
                "key": "Data.Key1",
                "values": [1, 10, 100]
            },
            {
                "operatorType": "NumberNotIn",
                "key": "Data.Key2",
                "values": [2, 3, 4.56]
            }
        ]
    }
}
```

## <a name="string-range-value-filter-examples"></a>字符串范围-值筛选器示例

* StringContains
* StringBeginsWith
* StringEndsWith
* StringIn
* StringNotIn

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "StringContains",
                "key": "Data.Key1",
                "values": ["microsoft", "azure"]
            },
            {
                "operatorType": "StringBeginsWith",
                "key": "Data.Key2",
                "values": ["event", "grid"]
            },
            {
                "operatorType": "StringEndsWith",
                "key": "Data.P3.P4",
                "values": ["jpg", "jpeg", "png"]
            },
            {
                "operatorType": "StringIn",
                "key": "RootKey",
                "values": ["exact", "string", "matches"]
            },
            {
                "operatorType": "StringNotIn",
                "key": "RootKey",
                "values": ["aws", "bridge"]
            }
        ]
    }
}
```

## <a name="boolean-single-value-filter-examples"></a>布尔单值筛选器示例

* BoolEquals

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "BoolEquals",
                "key": "BoolKey1",
                "value": true
            },
            {
                "operatorType": "BoolEquals",
                "key": "BoolKey2",
                "value": false
            }
        ]
    }
}
```
