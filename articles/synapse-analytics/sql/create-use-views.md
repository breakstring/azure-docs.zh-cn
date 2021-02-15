---
title: 在无服务器 SQL 池中创建和使用视图
description: 本部分介绍了如何创建和使用视图，以便包装无服务器 SQL 池查询。 可以通过视图重复使用这些查询。 如果希望将 Power BI 之类的工具与无服务器 SQL 池结合使用，也需使用视图。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 0948c7c82d7577bae07057bff9d1be4d7e09f978
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462283"
---
# <a name="create-and-use-views-using-serverless-sql-pool-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中通过无服务器 SQL 池创建和使用视图

本部分介绍了如何创建和使用视图，以便包装无服务器 SQL 池查询。 可以通过视图重复使用这些查询。 如果希望将 Power BI 之类的工具与无服务器 SQL 池结合使用，也需使用视图。

## <a name="prerequisites"></a>先决条件

第一步是创建一个数据库，将在该数据库中创建视图，并通过对该数据库执行[安装脚本](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)来初始化在 Azure 存储上进行身份验证所需的对象。 本文中的所有查询将在示例数据库上执行。

## <a name="create-a-view"></a>创建视图

可以采用与创建常规 SQL Server 视图相同的方式来创建视图。 下面的查询创建一个视图，该视图读取 population.csv 文件。

> [!NOTE]
> 更改查询中的第一行（即 [mydbname]），以便使用你创建的数据库。

```sql
USE [mydbname];
GO

DROP VIEW IF EXISTS populationView;
GO

CREATE VIEW populationView AS
SELECT * 
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', 
        FIELDTERMINATOR =',', 
        ROWTERMINATOR = '\n'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r];
```

此示例中的视图使用 `OPENROWSET` 函数，该函数使用指向基础文件的绝对路径。 如果 `EXTERNAL DATA SOURCE` 包含存储的根 URL，则可以将 `OPENROWSET` 与 `DATA_SOURCE` 和相对文件路径一起使用：

```sql
CREATE VIEW TaxiView
AS SELECT *, nyc.filepath(1) AS [year], nyc.filepath(2) AS [month]
FROM
    OPENROWSET(
        BULK 'parquet/taxi/year=*/month=*/*.parquet',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT='PARQUET'
    ) AS nyc
```

## <a name="use-a-view"></a>使用视图

可以在查询中使用视图，其方式与在 SQL Server 查询中使用视图的方式相同。

以下查询演示了如何使用在[创建视图](#create-a-view)中创建的 population_csv 视图。 它按降序返回国家/地区名称及其 2019 年的人口。

> [!NOTE]
> 更改查询中的第一行（即 [mydbname]），以便使用你创建的数据库。

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationView
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>后续步骤

若要了解如何查询不同的文件类型，请参阅以下文章：[查询单个 CSV 文件](query-single-csv-file.md)、[查询 Parquet 文件](query-parquet-files.md)和[查询 JSON 文件](query-json-files.md)。
