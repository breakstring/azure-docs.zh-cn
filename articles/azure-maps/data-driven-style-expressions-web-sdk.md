---
title: Azure Maps Web SDK 中的数据驱动样式表达式 |Microsoft Azure 映射
description: 了解数据驱动样式表达式。 请参阅如何在 Azure Maps Web SDK 中使用这些表达式来调整地图中的样式。
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 41a117c9ea8b47afcedaa1714abc2031d3be6c21
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680061"
---
# <a name="data-driven-style-expressions-web-sdk"></a>Web SDK (的数据驱动样式表达式) 

使用表达式可以将业务逻辑应用到样式选项，这些选项可观察数据源中每个形状中定义的属性。 表达式可以筛选数据源或层中的数据。 表达式可以包含条件逻辑，如 if 语句。 而且，它们可用于使用：字符串运算符、逻辑运算符和数学运算符来操作数据。

数据驱动的样式可减少在样式设置周围实现业务逻辑所需的代码量。 与层一起使用时，将在单独的线程上的呈现时计算表达式。 与评估 UI 线程上的业务逻辑相比，此功能可提供更高的性能。

此视频概述了 Azure Maps Web SDK 中的数据驱动样式。

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player?format=ny]

表达式表示为 JSON 数组。 数组中表达式的第一个元素是一个字符串，该字符串指定表达式运算符的名称。 例如，"+" 或 "case"。 如果任何) 是表达式的参数，则后面的元素 (。 每个参数都是一个文本值 (字符串、数字、布尔值、 `null`) 或其他表达式数组。 以下伪代码定义表达式的基本结构。 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

Azure Maps Web SDK 支持多种类型的表达式。 表达式可以单独使用，也可以与其他表达式结合使用。

| 表达式的类型 | 说明 |
|---------------------|-------------|
| [聚合表达式](#aggregate-expression) | 一个表达式，该表达式定义对一组数据进行处理并可与选项一起使用的计算 `clusterProperties` `DataSource` 。 |
| [布尔表达式](#boolean-expressions) | 布尔表达式提供一组用于计算布尔值比较的布尔运算符表达式。 |
| [颜色表达式](#color-expressions) | 颜色表达式使您可以更轻松地创建和操作颜色值。 |
| [条件表达式](#conditional-expressions) | 条件表达式提供类似于 if 语句的逻辑运算。 |
| [数据表达式](#data-expressions) | 提供对功能中的属性数据的访问。 |
| [内插和单步表达式](#interpolate-and-step-expressions) | 内插和单步表达式可用于沿内插曲线或单步执行函数计算值。 |
| [层特定的表达式](#layer-specific-expressions) | 仅适用于单个层的特殊表达式。 |
| [数学表达式](#math-expressions) | 提供数学运算符，用于在 expression framework 内执行数据驱动的计算。 |
| [字符串运算符表达式](#string-operator-expressions) | 字符串运算符表达式对字符串执行转换操作，例如连接和转换大小写。 |
| [类型表达式](#type-expressions) | 类型表达式提供了用于测试和转换不同数据类型（如字符串、数字和布尔值）的工具。 |
| [变量绑定表达式](#variable-binding-expressions) | 变量绑定表达式将计算的结果存储在一个变量中，并多次引用表达式中的其他位置，而无需重新计算存储的值。 |
| [Zoom 表达式](#zoom-expression) | 在呈现时检索地图的当前缩放级别。 |

本文档中的所有示例都使用以下功能演示了不同类型的表达式的不同使用方式。 

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": { 
        "id": 123,
        "entityType": "restaurant",
        "revenue": 12345,
        "subTitle": "Building 40", 
        "temperature": 64,
        "title": "Cafeteria", 
        "zoneColor": "purple",
        "abcArray": ["a", "b", "c"],
        "array2d": [["a", "b"], ["x", "y"]],
        "_style": {
            "fillColor": "red"
        }
    }
}
```

## <a name="data-expressions"></a>数据表达式

数据表达式提供对功能中的属性数据的访问。 

| Expression | 返回类型 | 说明 |
|------------|-------------|-------------|
| `['at', number, array]` | value | 从数组中检索项。 |
| `['geometry-type']` | string | 获取功能的几何图形类型： Point、MultiPoint、LineString、MultiLineString、多边形、MultiPolygon。 |
| `['get', string]` | value | 从当前功能的属性获取属性值。 如果缺少请求的属性，则返回 null。 |
| `['get', string, object]` | value | 从提供的对象的属性获取属性值。 如果缺少请求的属性，则返回 null。 |
| `['has', string]` | boolean | 确定功能的属性是否具有指定的属性。 |
| `['has', string, object]` | boolean | 确定对象的属性是否具有指定的属性。 |
| `['id']` | value | 获取功能的 ID （如果有）。 |
| `['in', boolean | string | number, array]` | boolean | 确定某一项是否存在于数组中 |
| `['in', substring, string]` | boolean | 确定字符串中是否存在子字符串 |
| `['index-of', boolean | string | number, array | string]`<br/><br/>`['index-of', boolean | string | number, array | string, number]` | 数字 | 返回可在数组中找到项的第一个位置，或者如果找不到输入，则可以在字符串中找到子字符串 `-1` 。 接受从其开始搜索的可选索引。 |
| `['length', string | array]` | 数字 | 获取字符串或数组的长度。 |
| `['slice', array | string, number]`<br/><br/>`['slice', array | string, number, number]` | 字符串 \| 数组 | 返回来自指定开始索引的字符串中的数组或子字符串的项，如果已设置，则从开始索引和结束索引之间返回。 返回值包含开始索引，但不包含在结束索引中。 |

**示例**

通过使用表达式，可以在表达式中直接访问功能的属性 `get` 。 此示例使用 `zoneColor` 功能的值来指定气泡图层的颜色属性。 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

如果所有点功能都具有属性，则上述示例将正常运行 `zoneColor` 。 否则，颜色可能会回退到 "黑色"。 若要修改回退颜色，请将 `case` 表达式与表达式结合使用 `has` 以检查该属性是否存在。 如果该属性不存在，则返回回退颜色。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case', //Use a conditional case expression.

        ['has', 'zoneColor'],   //Check to see if feature has a "zoneColor" property
        ['get', 'zoneColor'],   //If it does, use it.

        'blue'  //If it doesn't, default to blue.
    ]
});
```

默认情况下，气泡图和符号层将呈现数据源中所有形状的坐标。 此行为可以突出显示多边形或线条的顶点。 `filter`通过使用布尔表达式中的表达式，层的选项可用于限制其呈现的功能的几何类型 `['geometry-type']` 。 下面的示例将限制气泡图层，以便仅 `Point` 呈现功能。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

下面的示例允许 `Point` 呈现和 `MultiPoint` 功能。 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

同样，多边形的轮廓将以线条层呈现。 若要在线条层中禁用此行为，请添加只允许和功能的筛选器 `LineString` `MultiLineString` 。  

下面是有关如何使用数据表达式的一些其他示例：

```javascript
//Get item [2] from an array "properties.abcArray[1]" = "c"
['at', 2, ['get', 'abcArray']]

//Get item [0][1] from a 2D array "properties.array2d[0][1]" = "b"
['at', 1, ['at', 0, ['get', 'array2d']]]

//Check to see if a value is in an array "properties.abcArray.indexOf('a') !== -1" = true
['in', 'a', ['get', 'abcArray']]

//Gets the index of the value 'b' in an array "properties.abcArray.indexOf('b')" = 1
['index-of', 'b', ['get', 'abcArray']]

//Get the length of an array "properties.abcArray.length" = 3
['length', ['get', 'abcArray']]

//Get the value of a subproperty "properties._style.fillColor" = "red"
['get', 'fillColor', ['get', '_style']]

//Check that "fillColor" exists as a subproperty of "_style".
['has', 'fillColor', ['get', '_style']]

//Slice an array starting at index 2 "properties.abcArray.slice(2)" = ['c']
['slice', ['get', 'abcArray'], 2]

//Slice a string from index 0 to index 4 "properties.entityType.slice(0, 4)" = 'rest'
['slice', ['get', 'entityType'], 0, 4]
```

## <a name="math-expressions"></a>数学表达式

数学表达式提供数学运算符，用于在 expression framework 内执行数据驱动的计算。

| Expression | 返回类型 | 说明 |
|------------|-------------|-------------|
| `['+', number, number, …]` | 数字 | 计算指定数字的和。 |
| `['-', number]` | 数字 | 将0减去指定数字。 |
| `['-', number, number]` | 数字 | 用第二个数字减去第一个数字。 |
| `['*', number, number, …]` | 数字 | 将指定的数字相乘。 |
| `['/', number, number]` | 数字 | 将第一个数字除以第二个数字。 |
| `['%', number, number]` | 数字 | 计算第一个数字除以第二个数字时的余数。 |
| `['^', number, number]` | 数字 | 计算第一个值的值，该值为第二个数字的幂。 |
| `['abs', number]` | 数字 | 计算指定数字的绝对值。 |
| `['acos', number]` | 数字 | 计算指定数字的反余弦值。 |
| `['asin', number]` | 数字 | 计算指定数字的反正弦值。 |
| `['atan', number]` | 数字 | 计算指定数字的反正切值。 |
| `['ceil', number]` | 数字 | 将数字向上舍入到下一个整数。 |
| `['cos', number]` | 数字 | 计算指定数字的 cos。 |
| `['e']` | 数字 | 返回数学常量 `e` 。 |
| `['floor', number]` | 数字 | 将数字向下舍入到上一个整数。 |
| `['ln', number]` | 数字 | 计算指定数字的自然对数。 |
| `['ln2']` | 数字 | 返回数学常量 `ln(2)` 。 |
| `['log10', number]` | 数字 | 计算指定数字的以10为底的对数。 |
| `['log2', number]` | 数字 | 计算指定数字的以2为底的对数。 |
| `['max', number, number, …]` | 数字 | 计算指定的一组数字中的最大数目。 |
| `['min', number, number, …]` | 数字 | 计算指定的一组数字中的最小数目。 |
| `['pi']` | 数字 | 返回数学常量 `PI` 。 |
| `['round', number]` | 数字 | 将数字舍入到最接近的整数。 将值从零向外舍入。 例如， `['round', -1.5]` 计算结果为 `-2` 。 |
| `['sin', number]` | 数字 | 计算指定数字的正弦值。 |
| `['sqrt', number]` | 数字 | 计算指定数字的平方根。 |
| `['tan', number]` | 数字 | 计算指定数值的正切值。 |

## <a name="aggregate-expression"></a>聚合表达式

聚合表达式定义通过一组数据处理的计算，并可与选项一起使用 `clusterProperties` `DataSource` 。 这些表达式的输出必须是数字或布尔值。 

聚合表达式采用三个值：运算符值和初始值，以及从数据中的每个功能检索属性以对其应用聚合运算的表达式。 此表达式的格式如下：

```javascript
[operator: string, initialValue: boolean | number, mapExpression: Expression]
```

- 运算符：一个表达式函数，然后将其应用于由 `mapExpression` 为分类中的每个点计算的所有值。 支持的运算符： 
    - 对于数字： `+` 、 `*` 、 `max` 、 `min`
    - 对于布尔值： `all` ， `any`
- initialValue：用于聚合第一个计算值的初始值。
- mapExpression：应用于数据集中的每个点的表达式。

**示例**

如果数据集中的所有功能都有一个 `revenue` 数字属性，则为。 然后，可以计算群集中从数据集创建的所有点的总收入。 此计算使用以下聚合表达式完成： `['+', 0, ['get', 'revenue']]`

### <a name="accumulated-expression"></a>累积表达式

`accumulated`表达式获取到目前为止累积的群集属性的值。 这只能在 `clusterProperties` 聚集源的选项中使用 `DataSource` 。

**使用情况**

```javascript
["accumulated"]
```

## <a name="boolean-expressions"></a>布尔表达式

布尔表达式提供一组用于计算布尔值比较的布尔运算符表达式。

比较值时，将严格类型化比较。 不同类型的值始终被视为不相等。 在分析时已知不同类型的情况被视为无效，并将生成分析错误。 

| Expression | 返回类型 | 说明 |
|------------|-------------|-------------|
| `['!', boolean]` | boolean | 逻辑求反。 `true`如果输入为，则返回 `false` ; `false` 如果输入为，则返回 `true` 。 |
| `['!=', value, value]` | boolean | `true`如果输入值不相等，则返回 `false` ; 否则返回。 |
| `['<', value, value]` | boolean | `true`如果第一个输入严格小于第二个输入，则返回 `false` ; 否则返回。 参数需要同时为字符串和/或数字。 |
| `['<=', value, value]` | boolean | `true`如果第一个输入小于或等于第二个输入，则返回 `false` ; 否则返回。 参数需要同时为字符串和/或数字。 |
| `['==', value, value]` | boolean | `true`如果输入值相等，则返回 `false` ; 否则返回。 参数需要同时为字符串和/或数字。 |
| `['>', value, value]` | boolean | `true`如果第一个输入严格大于第二个输入，则返回 `false` ; 否则返回。 参数需要同时为字符串和/或数字。 |
| `['>=' value, value]` | boolean | `true`如果第一个输入大于或等于第二个输入，则返回 `false` ; 否则返回。 参数需要同时为字符串和/或数字。 |
| `['all', boolean, boolean, …]` | boolean | `true`如果所有输入都为，则返回 `true` `false` ; 否则返回。 |
| `['any', boolean, boolean, …]` | boolean | `true`如果任何输入为，则返回 `true` `false` ; 否则返回。 |
| `['within', Polygon | MultiPolygon | Feature<Polygon | MultiPolygon>]` | boolean | `true`如果计算的特征完全包含在输入几何的边界内，则返回 false; 否则返回 false。 输入值可以是类型为 `Polygon` 、、或的有效 `MultiPolygon` GeoJSON `Feature` `FeatureCollection` 。 支持的计算功能：<br/><br/>-Point： `false` 如果某个点在边界上或位于边界外，则返回。<br/>-LineString： `false` 如果行的任何部分落在边界之外，则该线条与边界相交，或者线条的端点位于边界上。 |

## <a name="conditional-expressions"></a>条件表达式

条件表达式提供类似于 if 语句的逻辑运算。

以下表达式对输入数据执行条件逻辑操作。 例如， `case` 表达式提供 "if/then/else" 逻辑，而 `match` 表达式就像 "switch 语句"。 

### <a name="case-expression"></a>Case 表达式

`case`表达式是一种提供 "if/then/else" 逻辑的条件表达式。 此类型的表达式逐步完成布尔条件的列表。 它将返回第一个布尔条件的输出值，其计算结果为 true。

以下伪代码定义表达式的结构 `case` 。 

```javascript
[
    'case',
    condition1: boolean, 
    output1: value,
    condition2: boolean, 
    output2: value,
    ...,
    fallback: value
]
```

**示例**

下面的示例单步执行不同的布尔条件，直到找到计算结果为的 `true` 值，然后返回该关联值。 如果没有布尔条件的计算结果为 `true` ，则返回回退值。 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case',

        //Check to see if the first boolean expression is true, and if it is, return its assigned result.
        ['has', 'zoneColor'],
        ['get', 'zoneColor'],

        //Check to see if the second boolean expression is true, and if it is, return its assigned result.
        ['all', ['has', ' temperature '], ['>', ['get', 'temperature'], 100]],
        'red',

        //Specify a default value to return.
        'green'
    ]
});
```

### <a name="match-expression"></a>Match 表达式

`match`表达式是一种提供类似于逻辑的 switch 语句的条件表达式。 输入可以是任何表达式 `['get', 'entityType']` ，如返回字符串或数字。 每个标签都必须是单个文本值或文本值的数组，其值必须是所有字符串或所有数字。 如果数组中的任何值匹配，则输入匹配。 每个标签都必须是唯一的。 如果输入类型与标签类型不匹配，则结果将为回退值。

以下伪代码定义表达式的结构 `match` 。 

```javascript
[
    'match',
    input: number | string,
    label1: number | string | (number | string)[], 
    output1: value,
    label2: number | string | (number | string)[], 
    output2: value,
    ...,
    fallback: value
]
```

**示例**

下面的示例在 `entityType` 气泡图层中查找点功能的属性搜索匹配项。 如果找到匹配项，则返回指定的值或返回回退值。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        'restaurant', 'red',
        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

下面的示例使用数组列出一组应返回相同值的标签。 此方法比单独列出每个标签要高效得多。 在这种情况下，如果 `entityType` 属性为 "餐馆" 或 "grocery_store"，则将返回颜色 "red"。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        ['restaurant', 'grocery_store'], 'red',

        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

### <a name="coalesce-expression"></a>合并表达式

`coalesce`表达式逐句通过一组表达式，直到获取第一个非 null 值并返回该值。 

以下伪代码定义表达式的结构 `coalesce` 。 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**示例**

下面的示例使用 `coalesce` 表达式来设置 `textField` 符号层的选项。 如果 `title` 特性缺少该特性或将其设置为 `null` ，则该表达式将尝试查找 `subTitle` 属性（如果缺少或 `null` ，它将回退到空字符串）。 

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'coalesce',

            //Try getting the title property.
            ['get', 'title'],

            //If there is no title, try getting the subTitle. 
            ['get', 'subTitle'],

            //Default to an empty string.
            ''
        ]
    }
});
```

下面的示例使用 `coalesce` 表达式从指定的图像名称列表中检索 "映射" 子画面中可用的第一个可用图像图标。

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    iconOptions: {
        image: [
            'coalesce',

            //Try getting the image with id 'missing-image'.
            ['image', 'missing-image'],

            //Specify an image id to fallback to. 
            'marker-blue'
        ]
    }
});
``` 

## <a name="type-expressions"></a>类型表达式

类型表达式提供了用于测试和转换不同数据类型（如字符串、数字和布尔值）的工具。

| Expression | 返回类型 | 说明 |
|------------|-------------|-------------|
| `['array', value]` \| `['array', type: "string" | "number" | "boolean", value]` | Object [] | 断言输入为数组。 |
| `['boolean', value]` \| `["boolean", value, fallback: value, fallback: value, ...]` | boolean | 断言输入值为布尔值。 如果提供了多个值，则将按顺序对每个值进行计算，直到获取布尔值。 如果没有任何输入为布尔值，则表达式为错误。 |
| `['collator', { 'case-sensitive': boolean, 'diacritic-sensitive': boolean, 'locale': string }]` | 机 | 返回要在与区域设置相关的比较操作中使用的排序程序。 区分大小写和音调符号的选项默认为 false。 Locale 参数指定要使用的区域设置的 IETF 语言标记。 如果未提供任何值，则使用默认区域设置。 如果请求的区域设置不可用，则排序程序将使用系统定义的回退区域设置。 使用已解决的区域设置测试区域设置回退行为的结果。 |
| `['literal', array]`<br/><br/>`['literal', object]` | array \| 对象 | 返回文本数组或对象值。 使用此表达式可防止将数组或对象作为表达式进行计算。 当表达式需要返回数组或对象时，这是必需的。 |
| `['image', string]` | string | 检查是否已将指定的映像 ID 加载到 maps 图像 sprite。 如果为，则返回 ID，否则返回 null。 |
| `['number', value]` \| `["number", value, fallback: value, fallback: value, ...]` | 数字 | 断言输入值是一个数字。 如果提供了多个值，则将按顺序对每个值进行计算，直到获取数值。 如果所有输入都不是数字，则表达式为错误。 |
| `['object', value]`  \| `["object", value, fallback: value, fallback: value, ...]` | 对象 | 断言输入值是一个对象。  如果提供了多个值，则将按顺序对每个值进行计算，直到获取一个对象。 如果没有任何输入为对象，则表达式为错误。 |
| `['string', value]` \| `["string", value, fallback: value, fallback: value, ...]` | string | 断言输入值为字符串。 如果提供了多个值，则将按顺序对每个值进行计算，直到获取了一个字符串。 如果没有任何输入为字符串，则表达式为错误。 |
| `['to-boolean', value]` | boolean | 将输入值转换为布尔值。 `false`如果输入为空字符串、、、或，则结果为 `0` `false` `null` `NaN` ; 否则为 `true` 。 |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | color | 将输入值转换为颜色。 如果提供了多个值，则将按顺序对每个值进行计算，直到获取第一个成功的转换。 如果没有任何输入可转换，则表达式为错误。 |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | 数字 | 如果可能，将输入值转换为数字。 如果输入为 `null` 或 `false` ，则结果为0。 如果输入为 `true` ，则结果为1。 如果输入是字符串，则使用 ECMAScript 语言规范的 [ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) 字符串函数将其转换为数字。 如果提供了多个值，则将按顺序对每个值进行计算，直到获取第一个成功的转换。 如果没有任何输入可转换，则表达式为错误。 |
| `['to-string', value]` | string | 将输入值转换为字符串。 如果输入为 `null` ，则结果为 `""` 。 如果输入为布尔值，则结果为 `"true"` 或 `"false"` 。 如果输入是一个数字，则使用 ECMAScript 语言规范的 [ToString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) number 函数将其转换为字符串。 如果输入是一种颜色，则将其转换为 CSS RGBA 颜色字符串 `"rgba(r,g,b,a)"` 。 否则，使用 ECMAScript 语言规范的 [json.stringify](https://tc39.github.io/ecma262/#sec-json.stringify) 函数将输入转换为字符串。 |
| `['typeof', value]` | string | 返回一个字符串，该字符串描述给定值的类型。 |

> [!TIP]
> 如果 `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].` 浏览器控制台中出现类似于的错误消息，则表示代码中的某个位置有一个数组，该表达式的第一个值没有字符串。 如果希望表达式返回数组，请使用表达式包装数组 `literal` 。 下面的示例 `offset` 通过使用表达式，根据 `match` 点功能的属性的值在两个偏移值之间进行选择，设置符号层的图标选项，该选项必须是包含两个数字的数组  `entityType` 。
>
> ```javascript
> var layer = new atlas.layer.SymbolLayer(datasource, null, {
>     iconOptions: {
>         offset: [
>             'match',
>
>             //Get the entityType value.
>             ['get', 'entityType'],
>
>             //If the entity type is 'restaurant', return a different pixel offset. 
>             'restaurant', ['literal', [0, -10]],
>
>             //Default to value.
>             ['literal', [0, 0]]
>         ]
>     }
> });
> ```

## <a name="color-expressions"></a>颜色表达式

颜色表达式使您可以更轻松地创建和操作颜色值。

| Expression | 返回类型 | 说明 |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | color | 从 *红色*、 *绿色* 和 *蓝色* 分量创建颜色值，该颜色值必须介于和之间， `0` `255` 并具有的 alpha 分量 `1` 。 如果任何组件超出范围，则表达式为错误。 |
| `['rgba', number, number, number, number]` | color | 从 *红色*、 *绿色*、 *蓝色* 分量创建颜色值，这些分量必须介于和之间， `0` 并且在 `255` 和范围内的 alpha `0` 分量 `1` 。 如果任何组件超出范围，则表达式为错误。 |
| `['to-rgba']` | \[number、number、number、number\] | 返回一个由四个元素组成的数组，其中包含输入颜色的 *红色*、 *绿色*、 *蓝色* 和 *alpha* 分量，按顺序排列。 |

**示例**

下面的示例创建一个 RGB 颜色值，其 *红色* 值为 `255` ，而 *绿色* 和 *蓝色* 值是通过与属性的值相乘计算得出的 `2.5` `temperature` 。 温度变化时，颜色将变为不同的 *红色* 阴影。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'rgb', //Create a RGB color value.

        255,    //Set red value to 255.

        ['*', 2.5, ['get', 'temperature']], //Multiple the temperature by 2.5 and set the green value.

        ['*', 2.5, ['get', 'temperature']]  //Multiple the temperature by 2.5 and set the blue value.
    ]
});
```

## <a name="string-operator-expressions"></a>字符串运算符表达式

字符串运算符表达式对字符串执行转换操作，例如连接和转换大小写。 

| Expression | 返回类型 | 说明 |
|------------|-------------|-------------|
| `['concat', string, string, …]` | string | 将多个字符串连接在一起。 每个值必须是字符串。 如果需要，请使用 `to-string` 类型表达式将其他值类型转换为字符串。 |
| `['downcase', string]` | string | 将指定的字符串转换为小写。 |
| `['is-supported-script', string]` \| `['is-supported-script', Expression]`| boolean | 确定输入字符串是否使用当前字体堆栈支持的字符集。 例如：`['is-supported-script', 'ಗೌರವಾರ್ಥವಾಗಿ']` |
| `['resolved-locale', string]` | string | 返回所提供的排序程序所使用的区域设置的 IETF 语言标记。 这可用于确定默认的系统区域设置，或确定是否已成功加载请求的区域设置。 |
| `['upcase', string]` | string | 将指定的字符串转换为大写。 |

**示例**

下面的示例将 `temperature` 点功能的属性转换为字符串，然后将 "° f" 连接到该字符串的末尾。

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: ['concat', ['to-string', ['get', 'temperature']], '°F'],

        //Some additional style options.
        offset: [0, -1.5],
        size: 12,
        color: 'white'
    }
});
```

上面的表达式在地图上呈现文本 "64 ° F" 的 pin，如下图所示。

<center>

![字符串运算符表达式示例 ](media/how-to-expressions/string-operator-expression.png)</center>

## <a name="interpolate-and-step-expressions"></a>内插和单步表达式

内插和单步表达式可用于沿内插曲线或单步执行函数计算值。 这些表达式采用返回数值作为输入的表达式，例如 `['get',  'temperature']` 。 根据输入和输出值对计算输入值，以确定最适合于内插曲线或单步执行函数的值。 输出值称为 "停止"。 每个停止的输入值必须是数字，并且必须是升序。 输出值必须是数字、数字数组或颜色。

### <a name="interpolate-expression"></a>内插表达式

`interpolate`表达式可用于通过在 stop 值之间进行插值计算来计算连续平滑的值集。 `interpolate`返回颜色值的表达式将产生从中选择结果值的颜色渐变。

可在表达式中使用三种类型的内插方法 `interpolate` ：
 
* `['linear']` -在停止对之间的线性内插。
* `['exponential', base]` -停止之间以指数方式插。 `base`值控制输出的增加速度。 值越大，输出就会增加到范围的高端。 `base`接近1的值将生成一个输出，该输出将增加线性增长。
* `['cubic-bezier', x1, y1, x2, y2]` -使用由给定控制点定义的一 [条三次方贝塞尔曲线](https://developer.mozilla.org/docs/Web/CSS/timing-function) 。

下面是这些不同类型的内插的示例。 

| 线性  | 指数 | 三次方贝塞尔 |
|---------|-------------|--------------|
| ![线性内插图](media/how-to-expressions/linear-interpolation.png) | ![指数内插关系图](media/how-to-expressions/exponential-interpolation.png) | ![三次方贝塞尔插值图](media/how-to-expressions/bezier-curve-interpolation.png) |

以下伪代码定义表达式的结构 `interpolate` 。 

```javascript
[
    'interpolate',
    interpolation: ['linear'] | ['exponential', base] | ['cubic-bezier', x1, y1, x2, y2],
    input: number,
    stopInput1: number, 
    stopOutput1: value1,
    stopInput2: number, 
    stopOutput2: value2, 
    ...
]
```

**示例**

下面的示例使用 `linear interpolate` 表达式 `color` 基于点功能的属性设置气泡图层的属性 `temperature` 。 如果 `temperature` 值小于60，则将返回 "blue"。 如果介于60到70之间，则将返回黄色。 如果介于70到80之间，则将返回 "橙色"。 如果为80或更高，则返回 "red"。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['get', 'temperature'],
        50, 'blue',
        60, 'yellow',
        70, 'orange',
        80, 'red'
    ]
});
```

下图演示了如何为上面的表达式选择颜色。
 
<center>

![内插表达式示例 ](media/how-to-expressions/interpolate-expression-example.png)</center>

### <a name="step-expression"></a>步骤表达式

`step`通过计算由停止定义的[分段函数](http://mathworld.wolfram.com/PiecewiseConstantFunction.html)，表达式可用于计算离散的阶数结果值。 

以下伪代码定义表达式的结构 `step` 。 

```javascript
[
    'step',
    input: number,
    output0: value0,
    stop1: number, 
    output1: value1,
    stop2: number, 
    output2: value2, 
    ...
]
```

步骤表达式返回的输出值刚好早于输入值，或第一个输入值（如果输入小于第一次停止）。 

**示例**

下面的示例使用 `step` 表达式 `color` 基于点功能的属性设置气泡图层的属性 `temperature` 。 如果 `temperature` 值小于60，则将返回 "blue"。 如果介于60到70之间，则将返回 "黄色"。 如果介于70到80之间，则将返回 "橙色"。 如果为80或更高，则返回 "red"。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'step',
        ['get', 'temperature'],
        'blue',
        60, 'yellow',
        70, 'orange',
        80, 'red'
    ]
});
```

下图演示了如何为上面的表达式选择颜色。
 
<center>

![步骤表达式示例](media/how-to-expressions/step-expression-example.png)
</center>

## <a name="layer-specific-expressions"></a>层特定的表达式

仅适用于特定层的特殊表达式。

### <a name="heat-map-density-expression"></a>热度地图密度表达式

热度地图密度表达式检索热度地图层中每个像素的热度地图密度值，并将其定义为 `['heatmap-density']` 。 此值是一个介于和之间的数字 `0` `1` 。 它与 `interpolation` or 表达式结合使用 `step` 来定义用于着色热度地图的颜色渐变。 此表达式只能用于热度地图层的 " [颜色" 选项](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions#color) 。

> [!TIP]
> 位于索引0、内插表达式或步骤颜色的默认颜色中的颜色定义了没有数据的区域的颜色。 索引0处的颜色可用于定义背景色。 很多用户喜欢将此值设置为透明或半透明黑色。

**示例**

此示例使用 "内插内插" 表达式为呈现热度地图创建平滑颜色渐变。 

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['heatmap-density'],
        0, 'transparent',
        0.01, 'purple',
        0.5, '#fb00fb',
        1, '#00c3ff'
    ]
});
```

除了使用平滑渐变来着色热度地图外，还可以使用表达式在一组范围内指定颜色 `step` 。 使用 `step` 用于着色热度地图的表达式直观地将密度分解为类似于等高线或雷达样式图的范围。  

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'step',
        ['heatmap-density'],
        'transparent',
        0.01, 'navy',
        0.25, 'navy',
        0.5, 'green',
        0.75, 'yellow',
        1, 'red'
    ]
});
```

有关详细信息，请参阅 [添加热度地图层](map-add-heat-map-layer.md) 文档。

### <a name="line-progress-expression"></a>行进度表达式

行进度表达式在直线层中沿渐变线检索进度，并将其定义为 `['line-progress']` 。 此值是一个介于0到1之间的数字。 它与 `interpolation` or 表达式结合使用 `step` 。 此表达式只能与线条层的 [strokeGradient 选项]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions#strokegradient) 一起使用。 

> [!NOTE]
> `strokeGradient`线条层的选项要求将 `lineMetrics` 数据源的选项设置为 `true` 。

**示例**

此示例使用 `['line-progress']` 表达式对线条的笔划应用颜色渐变。

```javascript
var layer = new atlas.layer.LineLayer(datasource, null, {
    strokeGradient: [
        'interpolate',
        ['linear'],
        ['line-progress'],
        0, "blue",
        0.1, "royalblue",
        0.3, "cyan",
        0.5, "lime",
        0.7, "yellow",
        1, "red"
    ]
});
```

[查看实时示例](map-add-line-layer.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>文本字段格式表达式

文本字段格式表达式可与 " `textField` 符号层" 属性的选项一起使用 `textOptions` ，以提供混合文本格式。 此表达式允许指定一组输入字符串和格式设置选项。 可以为此表达式中的每个输入字符串指定以下选项。

 * `'font-scale'` -指定字体大小的缩放系数。 如果指定，此值将重写 `size` 单个字符串的的属性 `textOptions` 。
 * `'text-font'` -指定应对此字符串使用的一个或多个字体系列。 如果指定，此值将重写 `font` 单个字符串的的属性 `textOptions` 。

以下伪代码定义文本字段格式表达式的结构。 

```javascript
[
    'format', 
    input1: string, 
    options1: { 
        'font-scale': number, 
        'text-font': string[]
    },
    input2: string, 
    options2: { 
        'font-scale': number, 
        'text-font': string[]
    },
    …
]
```

**示例**

下面的示例通过添加粗体字体并放大功能的属性的字体大小，设置文本字段的格式 `title` 。 此示例还将功能的属性添加到 `subTitle` 带缩小字体大小的换行符上。

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'format',

            //Bold the title property and scale its font size up.
            ['get', 'title'],
            {
                'text-font': ['literal', ['StandardFont-Bold']],
                'font-scale': 1.25
            },

            '\n', {},   //Add a new line without any formatting.

            //Scale the font size down of the subTitle property. 
            ['get', 'subTitle'],
            { 
                'font-scale': 0.75
            }
        ]
    }
});
```

此层将呈现点功能，如下图所示：
 
<center>

![带格式文本字段 ](media/how-to-expressions/text-field-format-expression.png) 的点功能图像 </center>

### <a name="number-format-expression"></a>数字格式表达式

`number-format`表达式只能与 `textField` 符号层的选项一起使用。 此表达式将提供的数字转换为带格式的字符串。 此表达式包装 JavaScript 的 [toLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) 函数，并支持以下选项集。

 * `locale` -指定此选项，以便将数字转换为字符串，使其与指定语言对齐。 将 [BCP 47 语言标记](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation) 传递到此选项。
 * `currency` -将数字转换为表示货币的字符串。 可能的值为 [ISO 4217 币种代码](https://en.wikipedia.org/wiki/ISO_4217)，如美元的美元 "USD"、欧元的 "EUR" 或中文 RMB 的 "CNY"。
 * `'min-fraction-digits'` -指定要包含在数字的字符串版本中的最小小数位数。
 * `'max-fraction-digits'` -指定要包含在数字字符串版本中的小数位数的最大位数。

以下伪代码定义文本字段格式表达式的结构。 

```javascript
[
    'number-format', 
    input: number, 
    options: {
        locale: string, 
        currency: string, 
        'min-fraction-digits': number, 
        'max-fraction-digits': number
    }
]
```

**示例**

下面的示例使用 `number-format` 表达式来修改 "点" `revenue` 功能的属性在符号层选项中的呈现方式 `textField` ，使其显示为美元值。

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'number-format', 
            ['get', 'revenue'], 
            { 'currency': 'USD' }
        ],

        offset: [0, 0.75]
    }
});
```

此层将呈现点功能，如下图所示：

<center>

![数字格式表达式示例 ](media/how-to-expressions/number-format-expression.png)</center>

### <a name="image-expression"></a>图像表达式

图像表达式可与 `image` 符号层的和选项一起使用 `textField` ，以及 `fillPattern` 多边形层的选项。 此表达式将检查请求的图像是否存在于样式中，并将返回已解析的图像名称或 `null` ，这取决于图像当前是否为样式。 此验证过程是同步的，需要先将该图像添加到该样式，然后才能在 image 参数中请求它。

**示例**

下面的示例使用 `image` 表达式向符号层中的文本添加一个嵌入图标。 

```javascript
 //Load the custom image icon into the map resources.
map.imageSprite.add('wifi-icon', 'wifi.png').then(function () {

    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);

    //Create a point feature and add it to the data source.
    datasource.add(new atlas.data.Point(map.getCamera().center));

    //Add a layer for rendering point data as symbols.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'none'
        },
        textOptions: {
            //Create a formatted text string that has an icon in it.
            textField: ["format", 'Ricky\'s ', ["image", "wifi-icon"], ' Palace']
        }
    }));
});
```

此层将呈现符号层中的文本字段，如下图所示：

<center>

![图像表达式示例 ](media/how-to-expressions/image-expression.png)</center>

## <a name="zoom-expression"></a>Zoom 表达式

`zoom`表达式用于在呈现时检索地图的当前缩放级别，并定义为 `['zoom']` 。 此表达式在地图的最小和最大缩放级别范围之间返回一个数字。 适用于 web 和 Android 的 Azure Maps 交互式地图控件支持25个缩放级别，其编号为0到24。 通过使用 `zoom` 表达式，可以在更改地图的缩放级别时动态修改样式。 `zoom`表达式只能与和表达式一起使用 `interpolate` `step` 。

**示例**

默认情况下，热度地图层中呈现的数据点的半径对于所有缩放级别都具有固定像素半径。 放大地图后，数据聚合在一起，热度地图层看起来不同。 `zoom`表达式可用于缩放每个缩放级别的半径，使每个数据点都涵盖地图的同一物理区域。 这会使热度地图层看起来更加静态和一致。 地图的每个缩放级别都具有两倍于与上一个缩放级别垂直和水平的像素。 缩放 radius，使其与每个缩放级别翻倍，将创建一个在所有缩放级别上都保持一致的热度地图。 可以使用 `zoom` 表达式和表达式来实现此目的 `base 2 exponential interpolation` ，并为最小缩放级别设置像素半径，并为计算的最大缩放级别设置缩放半径，如下 `2 * Math.pow(2, minZoom - maxZoom)` 所示。

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    radius: [
        'interpolate',
        ['exponential', 2],
        ['zoom'],
        
        //For zoom level 1 set the radius to 2 pixels.
        1, 2,

        //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * Math.pow(2, 19 - 1) pixels (524,288 pixels).
        19, 2 * Math.pow(2, 19 - 1)
    ]
};
```

[查看实时示例](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>变量绑定表达式

变量绑定表达式将计算结果存储在变量中。 因此，可以多次在表达式中的其他地方引用计算结果。 对于涉及许多计算的表达式，它是一个有用的优化。

| Expression | 返回类型 | 说明 |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"let"，<br/>&nbsp;&nbsp;&nbsp;&nbsp;name1： string，<br/>&nbsp;&nbsp;&nbsp;&nbsp;value1： any、<br/>&nbsp;&nbsp;&nbsp;&nbsp;name2： string，<br/>&nbsp;&nbsp;&nbsp;&nbsp;value2： any，<br/>&nbsp;&nbsp;&nbsp;&nbsp;…<br/>&nbsp;&nbsp;&nbsp;&nbsp;childExpression<br/>\] | | 将一个或多个值作为变量存储，以供 `var` 返回结果的子表达式中的表达式使用。 |
| `['var', name: string]` | any | 引用使用表达式创建的变量 `let` 。 |

**示例**

此示例使用一个表达式，该表达式计算相对于温度比率的收入，并使用 `case` 表达式来计算对该值的不同布尔运算。 该 `let` 表达式用于存储相对于温度比率的收入，因此只需计算一次。 表达式将根据 `var` 需要经常引用此变量，而不必重新计算此变量。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
        'let', 'ratio', ['/', ['get', 'revenue'], ['get', 'temperature']],
        //Evaluate the child expression in which the stored variable will be used.
        [
            'case',

            //Check to see if the ratio is less than 100, return 'red'.
            ['<', ['var', 'ratio'], 100],
            'red',

            //Check to see if the ratio is less than 200, return 'green'.
            ['<', ['var', 'ratio'], 200],
            'green',

            //Return `blue` for values greater or equal to 200.
            'blue'
        ]
    ]
});
```

## <a name="next-steps"></a>后续步骤

有关实现表达式的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"] 
> [添加符号层](map-add-pin.md)

> [!div class="nextstepaction"] 
> [添加气泡层](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [添加线条层](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [添加多边形层](map-add-shape.md)

> [!div class="nextstepaction"] 
> [添加热度地图层](map-add-heat-map-layer.md)

详细了解支持表达式的层选项：

> [!div class="nextstepaction"] 
> [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions)

> [!div class="nextstepaction"] 
> [HeatMapLayerOptions](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions)

> [!div class="nextstepaction"] 
> [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions)

> [!div class="nextstepaction"] 
> [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)

> [!div class="nextstepaction"] 
> [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions)