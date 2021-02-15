---
title: 了解 Azure Data Lake Analytics 的 SQL 开发人员 Apache Spark 代码概念。
description: 本文介绍 Apache Spark 的概念，以帮助 SQL 开发人员了解 Spark 代码概念。
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.custom: Understand-apache-spark-code-concepts
ms.date: 10/15/2019
ms.openlocfilehash: 2abd5882e310b17c633a82009f44624fad156f14
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/20/2020
ms.locfileid: "92221122"
---
# <a name="understand-apache-spark-code-for-u-sql-developers"></a>了解适用于 SQL 开发人员的 Apache Spark 代码

本部分提供有关将 U SQL 脚本转换为 Apache Spark 的高级指南。

- 它从[两种语言的处理模式比较](#understand-the-u-sql-and-spark-language-and-processing-paradigms)开始
- 提供有关如何执行以下操作的提示：
   - [转换脚本](#transform-u-sql-scripts) ，包括 U 型 [行集表达式](#transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions)
   - [.NET 代码](#transform-net-code)
   - [数据类型](#transform-typed-values)
   - [目录对象](#transform-u-sql-catalog-objects)。

## <a name="understand-the-u-sql-and-spark-language-and-processing-paradigms"></a>了解 U SQL 和 Spark 语言和处理模式

开始将 Azure Data Lake Analytics "U-SQL 脚本迁移到 Spark 之前，了解两个系统的一般语言和处理理念非常有用。

U SQL 是一种类似 SQL 的声明性查询语言，它使用数据流模式，可让你轻松地嵌入和横向扩展以 .NET 编写的用户代码 (例如 c # ) 、Python 和 R。用户扩展可以实现简单的表达式或用户定义的函数，但也可以使用户能够实现称为用户定义的运算符，该运算符实现自定义运算符以执行行集级别转换、提取和写入输出。

Spark 是一种横向扩展框架，提供 Scala、Java、Python、.NET 等中的几种语言绑定。在这些语言中，你主要以其中一种语言编写代码，创建称为弹性分布式数据集的数据抽象 (RDD) 、dataframes 和数据集，然后使用类似 LINQ 的域特定语言 (DSL) 来转换它们。 它还将 SparkSQL 作为数据帧和数据集抽象上的声明性子语言提供。 DSL 提供两类操作：转换和操作。 将转换应用于数据抽象不会执行转换，而是生成将使用操作提交以供评估的执行计划 (例如，将结果写入到临时表或文件中，或将结果打印) 。

因此，在将 SQL 脚本转换为 Spark 程序时，必须确定要使用哪种语言才能至少生成数据帧抽象 (当前最常使用的数据抽象) ，以及是否要使用 DSL 或 SparkSQL 编写声明性数据流转换。 在一些更复杂的情况下，你可能需要将你的 U SQL 脚本拆分为一系列 Spark 和使用 Azure Batch 或 Azure Functions 实现的其他步骤。

此外，Azure Data Lake Analytics 在无服务器作业服务环境中提供了 U-SQL，而 Azure Databricks 和 Azure HDInsight 都提供群集服务形式的 Spark。 转换应用程序时，必须考虑现在创建、调整大小、缩放和解除群集的影响。

## <a name="transform-u-sql-scripts"></a>转换 SQL 脚本

U-SQL 脚本遵循以下处理模式：

1. 数据从非结构化文件读取、使用 `EXTRACT` 语句、位置或文件集规范、内置或用户定义的提取器和所需的架构，或从 (托管表或外部表) 的 U SQL 表中读取数据。 它表示为一个行集。
2. 在将 U SQL 表达式应用于行集并生成新行集的多个 U SQL 语句中转换行集。
3. 最后，生成的行集将使用 `OUTPUT` 指定 () 位置的语句和内置或用户定义的输出器，或插入到 U 型表，输出到任一文件中。

此脚本将延迟计算，这意味着每个提取和转换步骤均组成一个表达式树，并 (数据流) 全局计算。

Spark 程序类似于，你将使用 Spark 连接器来读取数据和创建 dataframes，然后使用类似 LINQ 的 DSL 或 SparkSQL 在 dataframes 上应用转换，然后将结果写入文件、临时 Spark 表、某些编程语言类型或控制台。

## <a name="transform-net-code"></a>转换 .NET 代码

U SQL 的表达式语言是 c #，它提供了多种方法来扩展自定义 .NET 代码。

由于 Spark 当前不支持执行 .NET 代码，因此你必须将表达式重写为等效的 Spark、Scala、Java 或 Python 表达式，或者查找一种方法来调入 .NET 代码。 如果脚本使用 .NET 库，则可以使用以下选项：

- 将 .NET 代码转换为 Scala 或 Python。
- 将你的 U SQL 脚本拆分为多个步骤，在此过程中，你可以使用 Azure Batch 过程来应用 .NET 转换 (如果你可以获得可接受的规模) 
- 使用名为 Moebius 的开源中可用的 .NET 语言绑定。 此项目的状态不受支持。

在任何情况下，如果你的 U SQL 脚本中有大量的 .NET 逻辑，请通过 Microsoft 客户代表与我们联系以获取进一步指导。

下面的详细信息适用于 .NET 和 c # 中的不同情况。

### <a name="transform-scalar-inline-u-sql-c-expressions"></a>转换标量内联 SQL-DMO c # 表达式

C # 中的表达式语言是 c #。 许多标量内联 U 型表达式在本机实现以提高性能，而更复杂的表达式可通过调用 .NET framework 来执行。

Spark 具有自己的标量表达式语言 (作为 DSL 或 SparkSQL) 的一部分，并允许调用以其托管语言编写的用户定义函数。

如果在 U SQL 中使用标量表达式，则应首先找到最适合的本机理解 Spark 标量表达式，以获得最大性能，然后将其他表达式映射到所选 Spark 托管语言的用户定义函数。

请注意，.NET 和 c # 的类型语义不同于 Spark 托管语言和 Spark 的 DSL。 有关类型系统差异的详细信息，请参阅 [下文](#transform-typed-values) 。

### <a name="transform-user-defined-scalar-net-functions-and-user-defined-aggregators"></a>转换用户定义的标量 .NET 函数和用户定义的聚合型

U-SQL 提供调用任意标量 .NET 函数和调用以 .NET 编写的用户定义的聚合程序的方法。

Spark 还为用户定义的函数和用户定义的聚合函数提供支持，这些聚合函数是通过 Spark DSL 和 SparkSQL 调用的大部分托管语言编写的。

### <a name="transform-user-defined-operators-udos"></a>将用户定义的运算符转换 (Udo) 

U SQL 提供多个用户定义的运算符， (Udo) 例如提取器、输出器、化简器、处理器、应用器和合并器，它们可以用 .NET (编写，甚至可以在某个范围的 Python 和 R) 中写入。

Spark 不提供与运算符相同的扩展性模型，但对于某些运算符具有等效的功能。

与提取器和输出器等效的 Spark 是 Spark 连接器。 对于很多的提取器，可在 Spark 社区中找到等效的连接器。 对于其他用户，必须编写自定义连接器。 如果 U SQL 提取程序很复杂，并使用多个 .NET 库，则最好是在 Scala 中生成一个连接器，使用互操作调用对数据进行实际处理的 .NET 库。 在这种情况下，必须将 .NET Core 运行时部署到 Spark 群集，并确保引用的 .NET 库 .NET Standard 2.0 兼容。

需要使用用户定义的函数和聚合函数以及语义适当的 Spark DLS 或 SparkSQL 表达式重写其他类型的 U SQL Udo。 例如，可以将处理器映射到多种 UDF 调用，并将其打包为采用数据帧作为参数并返回数据帧的函数。

### <a name="transform-u-sqls-optional-libraries"></a>转换 U SQL 的可选库

U-SQL 提供一组可选的和演示库，它们提供 [Python](data-lake-analytics-u-sql-python-extensions.md)、 [R](data-lake-analytics-u-sql-r-extensions.md)、 [JSON、XML、AVRO 支持](https://github.com/Azure/usql/tree/master/Examples/DataFormats)和一些 [认知服务功能](data-lake-analytics-u-sql-cognitive.md)。

Spark 分别提供自己的 Python 和 R 集成、pySpark 和 SparkR，并提供用于读取和写入 JSON、XML 和 AVRO 的连接器。

如果需要转换引用认知服务库的脚本，建议通过 Microsoft 客户代表联系我们。

## <a name="transform-typed-values"></a>转换类型化值

由于 U 型系统基于 .NET 类型系统并且 Spark 具有其自己的类型系统（受主机语言绑定的影响），因此您必须确保您正在操作的类型已关闭，并且对于某些类型，类型范围、精度和/或小数位数可能略有不同。 此外，U SQL 和 Spark 会 `null` 以不同的方式对待值。

### <a name="data-types"></a>数据类型

下表提供了 Spark、Scala 和 PySpark 中给定的 U SQL 类型的等效类型。

| U-SQL | Spark |  Scala | PySpark |
| ------ | ------ | ------ | ------ |
|`byte`       ||||
|`sbyte`      |`ByteType` |`Byte` | `ByteType`|
|`int`        |`IntegerType` |`Int` | `IntegerType`|
|`uint`       ||||
|`long`       |`LongType` |`Long` | `LongType`|
|`ulong`      ||||
|`float`      |`FloatType` |`Float` | `FloatType`|
|`double`     |`DoubleType` |`Double` | `DoubleType`|
|`decimal`    |`DecimalType` |`java.math.BigDecimal` | `DecimalType`|
|`short`      |`ShortType` |`Short` | `ShortType`|
|`ushort`     ||||
|`char`   | |`Char`||
|`string` |`StringType` |`String` |`StringType` |
|`DateTime`   |`DateType`, `TimestampType` |`java.sql.Date`, `java.sql.Timestamp` | `DateType`, `TimestampType`|
|`bool`   |`BooleanType` |`Boolean` | `BooleanType`|
|`Guid`   ||||
|`byte[]` |`BinaryType` |`Array[Byte]` | `BinaryType`|
|`SQL.MAP<K,V>`   |`MapType(keyType, valueType, valueContainsNull)` |`scala.collection.Map` | `MapType(keyType, valueType, valueContainsNull=True)`|
|`SQL.ARRAY<T>`   |`ArrayType(elementType, containsNull)` |`scala.collection.Seq` | `ArrayType(elementType, containsNull=True)`|

有关详细信息，请参阅：

- [org. .sql. 类型](https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.types.package)
- [Spark SQL 和 DataFrames 类型](https://spark.apache.org/docs/latest/sql-ref-datatypes.html)
- [Scala 值类型](https://www.scala-lang.org/api/current/scala/AnyVal.html)
- [pyspark](https://spark.apache.org/docs/latest/api/python/pyspark.sql.html#module-pyspark.sql.types)

### <a name="treatment-of-null"></a>处理 NULL

在 Spark 中，每个默认类型允许 NULL 值，而在 U SQL 中，将标量、非对象显式标记为可为 null。 Spark 允许将列定义为不可为 null，但不会强制约束，并且 [可能导致错误的结果](https://medium.com/@weshoffman/apache-spark-parquet-and-troublesome-nulls-28712b06f836)。

在 Spark 中，NULL 指示值未知。 Spark NULL 值不同于任何值，包括自身。 两个 Spark NULL 值之间或 NULL 值与任何其他值之间的比较返回未知，因为每个 NULL 的值都是未知的。  

此行为与 c # 语义不同，后者遵循 c # 语义，其中 `null` 不同于任何值，但等于其自身。  

因此 `SELECT` ，即使在中存在 NULL 值，使用的 SparkSQL 语句也将 `WHERE column_name = NULL` 返回零行， `column_name` 而在 U SQL 中，它将返回 `column_name` 设置为的行 `null` 。 同样，即使在中 `SELECT` `WHERE column_name != NULL` 有非 null 值，使用的 Spark 语句也返回零行， `column_name` 而在 U SQL 中，它将返回具有非 null 值的行。 因此，如果你想要使用双 SQL null 检查语义，则应该分别使用 [isnull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnull) 和 [isnotnull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnotnull) (或其 DSL 等效) 。

## <a name="transform-u-sql-catalog-objects"></a>转换 U SQL 目录对象

一个主要区别是，可以使用多个 SQL 脚本来利用它的目录对象，其中许多对象没有直接的 Spark 等效项。

Spark 为 Hive 元存储概念提供支持，主要是数据库和表，因此你可以将 tvf 数据库和架构映射到 Hive 数据库，将 u SQL 表映射到 Spark 表 (查看将 [存储在 U SQL 表中的数据](understand-spark-data-formats.md#move-data-stored-in-u-sql-tables)) ，但它不支持视图、表值函数 () 、存储过程、U SQL 程序集、外部数据源等。

可以通过 Spark 中的代码函数和库对 U SQL 代码对象（如视图、Tvf、存储过程和程序集）进行建模，并使用宿主语言的函数和过程抽象机制进行引用 (例如，通过导入 Python 模块或引用 Scala 函数) 。

如果使用了 U SQL 目录跨项目和团队共享数据和代码对象，则必须使用等效的共享机制 (例如，Maven 用于共享代码对象) 。

## <a name="transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions"></a>转换 U 型行集表达式和基于 SQL 的标量表达式

U 型核心语言正在转换行集，并基于 SQL。 下面是在 U SQL 中提供的最常见行集表达式的不完整列表：

- `SELECT`/`FROM`/`WHERE`/`GROUP BY`+ 聚合 +`HAVING`/`ORDER BY`+`FETCH`
- `INNER`/`OUTER`/`CROSS`/`SEMI``JOIN`表达式
- `CROSS`/`OUTER``APPLY`表达式
- `PIVOT`/`UNPIVOT` 表达
- `VALUES` 行集构造函数

- 集表达式 `UNION`/`OUTER UNION`/`INTERSECT`/`EXCEPT`

此外，U SQL 还提供各种基于 SQL 的标量表达式，如

- `OVER` 窗口表达式
-  (等各种内置聚合函数和排名函数 `SUM` `FIRST` ) 
- 一些最熟悉的 SQL 标量表达式： `CASE` 、 `LIKE` 、 (`NOT`) 、等 `IN` `AND` `OR` 。

Spark 在其 DSL 和 SparkSQL 形式提供等效的表达式，适用于大多数这类表达式。 必须使用本机 Spark 表达式和语义等效模式的组合来重新编写某些 Spark 中不支持的表达式。 例如， `OUTER UNION` 必须将转换为等效的投影和联合组合。

由于对 NULL 值进行了不同的处理，因此，如果两个比较的列都包含 NULL 值，则在 Spark 中的联接将始终匹配行，除非添加显式 NULL 检查。

## <a name="transform-other-u-sql-concepts"></a>转换其他的 U SQL 概念

U SQL 还提供各种其他功能和概念，如针对 SQL Server 数据库、参数、标量和 lambda 表达式变量、系统变量和提示的联合查询 `OPTION` 。

### <a name="federated-queries-against-sql-server-databasesexternal-tables"></a>针对 SQL Server 数据库/外部表的联合查询

U SQL 提供数据源和外部表以及对 Azure SQL 数据库的直接查询。 虽然 Spark 不提供相同的对象抽象，但它为可用于查询 SQL 数据库的 [AZURE SQL 数据库提供 Spark 连接器](../azure-sql/database/spark-connector.md) 。

### <a name="u-sql-parameters-and-variables"></a>U-SQL 参数和变量

参数和用户变量在 Spark 及其托管语言中具有等效的概念。

例如，在 Scala 中，可以使用关键字定义一个变量 `var` ：

```
var x = 2 * 3;
println(x)
```

从) 开始的 U SQL 系统变量 (`@@` 可以分为两类：

- 可设置的系统变量，可将其设置为特定值以影响脚本行为
- 查询系统和作业级别信息的信息系统变量

大多数可设置的系统变量在 Spark 中没有直接等效项。 在作业执行期间，可以通过将信息作为参数传递来建模某些信息系统变量，而其他变量可能在 Spark 的托管语言中具有等效的函数。

### <a name="u-sql-hints"></a>U-SQL 提示

U-SQL 提供多种语义方法来向查询优化器和执行引擎提供提示：  

- 设置 U SQL 系统变量
- `OPTION`与用于提供数据或计划提示的行集表达式关联的子句
- 联接表达式语法中的联接提示 (例如， `BROADCASTLEFT`) 

Spark 的基于成本的查询优化器具有其自己的功能，可提供提示并优化查询性能。 请参阅相应的文档。

## <a name="next-steps"></a>后续步骤

- [了解适用于 SQL 开发人员的 Spark 数据格式](understand-spark-data-formats.md)
- [.NET for Apache Spark](/dotnet/spark/what-is-apache-spark-dotnet)
- [将大数据分析解决方案从 Azure Data Lake Storage Gen1 升级到 Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md)
- [在 Azure 数据工厂中使用 Spark 活动转换数据](../data-factory/transform-data-using-spark.md)
- [在 Azure 数据工厂中使用 Hadoop Hive 活动转换数据](../data-factory/transform-data-using-hadoop-hive.md)
- [Azure HDInsight 中的 Apache Spark 是什么](../hdinsight/spark/apache-spark-overview.md)