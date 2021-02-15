---
title: Azure 流分析 JavaScript 用户定义的函数
description: 本文介绍了流分析中的 JavaScript 用户定义函数。
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: tutorial
ms.custom: mvc, devx-track-js
ms.date: 12/15/2020
ms.openlocfilehash: 70015ef24039694789ce96a6c4853221fe2377c3
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020377"
---
# <a name="javascript-user-defined-functions-in-azure-stream-analytics"></a>Azure 流分析中 JavaScript 用户定义的函数
 
Azure 流分析支持以 JavaScript 编写的用户定义的函数。 利用 JavaScript 提供的丰富 **String**、**RegExp**、**Math**、**Array** 和 **Date** 方法，可以更轻松地创建包含流分析作业的复杂数据转换。

## <a name="overview"></a>概述

JavaScript 用户定义的函数支持仅用于计算的且不需要外部连接的无状态标量函数。 函数的返回值只能是标量（单个）值。 将某个 JavaScript 用户定义的函数添加到作业后，可在查询中的任意位置使用该函数，如同内置标量函数一样。

下面是 JavaScript 用户定义的函数可派上用场的一些情景：
* 使用 **Regexp_Replace()** 和 **Regexp_Extract()** 等正则表达式函数分析和处理字符串
* 解码和编码数据，例如，二进制到十六进制的转换
* 使用 JavaScript **数学** 函数进行数学计算
* 执行排序、联接、查找和填充等数组操作

使用流分析中的 JavaScript 用户定义的函数无法实现以下目的：
* 调用外部 REST 终结点，例如，执行反向 IP 查找，或者从外部源提取引用数据
* 针对输入/输出执行自定义事件格式序列化或反序列化
* 创建自定义聚合

尽管函数定义中并不禁止 **Date.GetDate()** 或 **Math.random()** 等函数，但应该避免使用这些函数。 这些函数在每次被调用时 **不会** 返回相同的结果，并且 Azure 流分析服务不会保留函数调用和返回结果的日记。 当你或流分析服务重新启动某个作业时，如果某个函数针对相同的事件返回不同的结果，将无法保证可重复性。

## <a name="add-a-javascript-user-defined-function-to-your-job"></a>向作业中添加 JavaScript 用户定义的函数

> [!NOTE]
> 这些步骤适用于配置为在云中运行的流分析作业。 如果流分析作业配置为在 Azure IoT Edge 上运行，请改用 Visual Studio 并[使用 C# 编写用户定义的函数](stream-analytics-edge-csharp-udf.md)。

若要在流分析作业中创建 JavaScript 用户定义的函数，请在“作业拓扑”下选择“函数”。 然后，从“+ 添加”下拉菜单中选择“JavaScript UDF”。 

![添加 JavaScript UDF](./media/javascript/stream-analytics-jsudf-add.png)

然后，你必须提供以下属性并选择“保存”。

|属性|说明|
|--------|-----------|
|函数别名|输入一个名称以在查询中调用函数。|
|输出类型|JavaScript 用户定义的函数将向流分析查询返回的类型。|
|函数定义|每次从查询中调用 UDF 时将执行的 JavaScript 函数的实现。|

## <a name="test-and-troubleshoot-javascript-udfs"></a>对 JavaScript UDF 进行测试和故障排除 

可在任何浏览器中测试和调试 JavaScript UDF 逻辑。 流分析门户目前不支持调试和测试这些用户定义函数的逻辑。 函数按预期方式运行后，可以将其添加到流分析作业（如上所述），然后直接从查询调用它。 还可以使用[适用于 Visual Studio 的流分析工具](./stream-analytics-tools-for-visual-studio-install.md)测试包含 JavaScript UDF 的查询逻辑。

JavaScript 运行时错误被视为严重错误，可通过活动日志查看。 如果要检索日志，请在 Azure 门户中转到用户的作业，并选择“活动日志”。

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>在查询中调用 JavaScript 用户定义的函数

可以使用以 **udf** 为前缀的函数别名轻松在查询中调用 JavaScript 函数。 下面是 JavaScript UDF 的一个示例，它将十六进制值转换为在流分析查询中调用的整数。

```SQL
    SELECT
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
```

## <a name="supported-javascript-objects"></a>支持的 JavaScript 对象

Azure 流分析 JavaScript 用户定义的函数支持标准的内置 JavaScript 对象。 有关这些对象的列表，请参阅 [Global Objects](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects)（全局对象）。

### <a name="stream-analytics-and-javascript-type-conversion"></a>流分析和 JavaScript 类型转换

流分析查询语言与 JavaScript 支持的类型有差别。 下表列出了两者之间的转换映射：

流分析 | Javascript
--- | ---
bigint | Number（JavaScript 只能精确呈现最大 2^53 的整数）
DateTime | Date（JavaScript 仅支持毫秒）
Double | Number
nvarchar(MAX) | 字符串
Record | 对象
Array | Array
Null | Null

下面是 JavaScript 到流分析的转换：

Javascript | 流分析
--- | ---
Number | 如果数字已舍入并介于 long.MinValue 和 long.MaxValue 之间，则为 Bigint；否则为 double
Date | DateTime
字符串 | nvarchar(MAX)
对象 | Record
Array | Array
Null、Undefined | Null
其他任何类型（例如函数或错误） | 不支持（导致运行时错误）

JavaScript 语言区分大小写，JavaScript 代码中对象字段的大小写必须与传入数据中字段的大小写匹配。 兼容性级别为 1.0 的作业会将 SQL SELECT 语句中的字段转换为小写。 在兼容性级别 1.1 及更高级别下，SELECT 语句中的字段将具有与 SQL 查询中指定的相同的大小写。

## <a name="other-javascript-user-defined-function-patterns"></a>JavaScript 用户定义的函数的其他模式

### <a name="write-nested-json-to-output"></a>编写要输出的嵌套 JSON

如果后续处理步骤需要使用流分析作业输出作为输入并且要求采用 JSON 格式，可以编写要输出的 JSON 字符串。 以下示例调用 **JSON.stringify()** 函数封装输入的所有名称/值对，并将其写入为输出中的单个字符串值。

**JavaScript 用户定义的函数定义：**

```javascript
function main(x) {
return JSON.stringify(x);
}
```

**示例查询：**
```SQL
SELECT
    DataString,
    DataValue,
    HexValue,
    UDF.jsonstringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

### <a name="cast-string-to-json-object-to-process"></a>将字符串强制转换为要处理的 JSON 对象

如果你有一个 JSON 字符串字段，并想将其转换为 JSON 对象以在 JavaScript UDF 中进行处理，则可以使用 JSON.parse() 函数来创建一个随后可使用的 JSON 对象。

**JavaScript 用户定义的函数定义：**

```javascript
function main(x) {
var person = JSON.parse(x);  
return person.name;
}
```

**示例查询：**
```SQL
SELECT
    UDF.getName(input) AS Name
INTO
    output
FROM
    input
```

### <a name="use-trycatch-for-error-handling"></a>使用 try/catch 进行错误处理

Try/catch 块可帮助确定传递给 JavaScript UDF 的格式错误的输入数据的问题。

**JavaScript 用户定义的函数定义：**

```javascript
function main(input, x) {
    var obj = null;

    try{
        obj = JSON.parse(x);
    }catch(error){
        throw input;
    }
    
    return obj.Value;
}
```

示例查询：将整个记录作为第一个参数进行传递，以便在出现错误时可将其返回。
```SQL
SELECT
    A.context.company AS Company,
    udf.getValue(A, A.context.value) as Value
INTO
    output
FROM
    input A
```

### <a name="tolocalestring"></a>toLocaleString()
JavaScript 中的 toLocaleString 方法可用于返回一个区分语言的字符串，该字符串表示调用此方法的日期时间数据。
尽管 Azure 流分析只接受 UTC 日期时间作为系统时间戳，但此方法可用于将系统时间戳转换为其他区域设置和时区。
此方法遵循的实现行为与 Internet Explorer 中提供的行为相同。

**JavaScript 用户定义的函数定义：**

```javascript
function main(datetime){
    const options = { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' };
    return event.toLocaleDateString('de-DE', options);
}
```

示例查询：将日期/时间作为输入值传递
```SQL
SELECT
    udf.toLocaleString(input.datetime) as localeString
INTO
    output
FROM
    input
```

此查询的输出将是 de-DE 中的输入日期时间，其中包含提供的选项。
```
Samstag, 28. Dezember 2019
```

## <a name="user-logging"></a>用户日志记录
使用日志记录机制，可以在作业运行时捕获自定义信息。 可以使用日志数据实时调试或评估自定义代码的正确性。 此机制通过 Console.Log() 方法提供。

```javascript
console.log('my error message');
```

可以通过[诊断日志](data-errors.md)访问日志消息。
## <a name="next-steps"></a>后续步骤

* [机器学习 UDF](./machine-learning-udf.md)
* [C# UDF](./stream-analytics-edge-csharp-udf-methods.md)
