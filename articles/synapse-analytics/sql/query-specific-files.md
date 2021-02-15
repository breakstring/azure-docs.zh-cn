---
title: 在查询中使用文件元数据
description: OPENROWSET 函数提供有关在查询中使用的每个文件的文件和路径信息，以便根据文件名和/或文件夹路径筛选或分析数据。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: d24ae1f42c685589309506b2d5e0eab157b2bc42
ms.sourcegitcommit: 5e2f5efba1957ba40bd951c3dcad42f4a00734ff
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2020
ms.locfileid: "96299609"
---
# <a name="use-file-metadata-in-serverless-sql-pool-queries"></a>在无服务器 SQL 池查询中使用文件元数据

无服务器 SQL 池可以处理多个文件和文件夹，如 [查询文件夹和多个文件](query-folders-multiple-csv-files.md) 一文中所述。 本文介绍如何在查询中使用有关文件和文件夹名称的元数据信息。

有时，可能会需要知道哪个文件或文件夹源与结果集中的某个特定行相关。

可以使用函数 `filepath` 和 `filename` 在结果集中返回文件名称和/或路径。 或者，可以使用它们根据文件名和/或文件夹路径来筛选数据。 语法部分 [filename 函数](query-data-storage.md#filename-function)和 [filepath 函数](query-data-storage.md#filepath-function)中对这些函数进行了介绍。 在以下部分中，你将找到有关示例的简短说明。

## <a name="prerequisites"></a>先决条件

第一步是 **创建数据库**，其中包含了引用存储帐户的数据源。 然后通过对该数据库执行[安装脚本](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)来初始化这些对象。 此安装脚本将创建数据源、数据库范围的凭据以及在这些示例中使用的外部文件格式。

## <a name="functions"></a>函数

### <a name="filename"></a>文件名

此函数返回该行所源自的文件的名称。

下面的示例读取 2017 年最后三个月纽约市黄色出租车的数据文件，并按文件返回搭乘数。 查询的 OPENROWSET 部分指定将读取哪些文件。

```sql
SELECT
    nyc.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM  
    OPENROWSET(
        BULK 'parquet/taxi/year=2017/month=9/*.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) nyc
GROUP BY nyc.filename();
```

下面的示例演示如何在 WHERE 子句中使用 filename()，以筛选要读取的文件。 它访问查询的 OPENROWSET 部分中的整个文件夹，并在 WHERE 子句中筛选文件。

得到的结果将与前面的示例相同。

```sql
SELECT
    r.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
    BULK 'csv/taxi/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        PARSER_VERSION = '2.0',
        FIRSTROW = 2) 
        WITH (C1 varchar(200) ) AS [r]
WHERE
    r.filename() IN ('yellow_tripdata_2017-10.csv', 'yellow_tripdata_2017-11.csv', 'yellow_tripdata_2017-12.csv')
GROUP BY
    r.filename()
ORDER BY
    [filename];
```

### <a name="filepath"></a>Filepath

Filepath 函数返回完整路径或部分路径：

- 如果在不使用参数的情况下调用此函数，此函数将返回行的来源文件的完整路径。 当在 OPENROWSET 中使用 DATA_SOURCE 时，它将返回相对于 DATA_SOURCE 的路径。 
- 如果在使用参数的情况下调用此函数，此函数将返回与该参数中指定的位置上的通配符相匹配的路径部分。 例如，参数值 1 将返回与第一个通配符匹配的路径部分。

下面的示例读取 2017 年最后三个月纽约市黄色出租车的数据文件。 它按文件路径返回搭乘数。 查询的 OPENROWSET 部分指定将读取哪些文件。

```sql
SELECT
    r.filepath() AS filepath
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'csv/taxi/yellow_tripdata_2017-1*.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id INT
    ) AS [r]
GROUP BY
    r.filepath()
ORDER BY
    filepath;
```

下面的示例演示如何在 WHERE 子句中使用 filepath()，以筛选要读取的文件。

可以在查询的 OPENROWSET 部分中使用通配符，并在 WHERE 子句中筛选文件。 得到的结果将与前面的示例相同。

```sql
SELECT
    r.filepath() AS filepath
    ,r.filepath(1) AS [year]
    ,r.filepath(2) AS [month]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'csv/taxi/yellow_tripdata_*-*.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        PARSER_VERSION = '2.0',        
        FIRSTROW = 2
    )
WITH (
    vendor_id INT
) AS [r]
WHERE
    r.filepath(1) IN ('2017')
    AND r.filepath(2) IN ('10', '11', '12')
GROUP BY
    r.filepath()
    ,r.filepath(1)
    ,r.filepath(2)
ORDER BY
    filepath;
```

## <a name="next-steps"></a>后续步骤

下一篇文章介绍如何[查询 Parquet 文件](query-parquet-files.md)。
