---
title: 使用无服务器 SQL 池查询 JSON 文件
description: 本部分介绍如何在 Azure Synapse Analytics 中使用无服务器 SQL 池读取 JSON 文件。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 8dc07a3aa954a74ba594eb99da1ea3ee59610c9b
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678315"
---
# <a name="query-json-files-using-serverless-sql-pool-in-azure-synapse-analytics"></a>使用 Azure Synapse Analytics 中的无服务器 SQL 池查询 JSON 文件

本文介绍如何在 Azure Synapse Analytics 中使用无服务器 SQL 池编写查询。 查询的目标是使用 [OPENROWSET](develop-openrowset.md)读取 JSON 文件。 
- 将多个 JSON 文档存储为 JSON 数组的标准 JSON 文件。
- 行分隔的 JSON 文件，其中 JSON 文档用换行符分隔。 这些类型的文件的常见扩展为 `jsonl` 、 `ldjson` 和 `ndjson` 。

## <a name="read-json-documents"></a>读取 JSON 文档

查看 JSON 文件内容的最简单方法是提供函数的文件 URL `OPENROWSET` ，指定 csv `FORMAT` ，并 `0x0b` 为和设置值 `fieldterminator` `fieldquote` 。 如果需要读取行分隔的 JSON 文件，这就足够了。 如果你有经典 JSON 文件，则需要为设置值 `0x0b` `rowterminator` 。 `OPENROWSET` 函数将分析 JSON 并返回以下格式的每个文档：

| 文档 |
| --- |
|{"date_rep"： "2020-7"、"day"：24、"month"：7、"year"：2020、"事例"：3，"deaths"：0，"geo_id"： "AF"}|
|{"date_rep"： "2020-07-25"，"day"：25，"month"：7，"year"：2020，"事例"：7，"deaths"：0，"geo_id"： "AF"}|
|{"date_rep"： "2020-26"、"day"：26、"month"：7、"year"：2020、"事例"：4，"deaths"：0，"geo_id"： "AF"}|
|{"date_rep"： "2020-27"，"day"：27，"month"：7，"year"：2020，"事例"：8，"deaths"：0，"geo_id"： "AF"}|

如果该文件公开可用，或者您的 Azure AD 标识可以访问此文件，则应使用类似于下面的示例中所示的查询来查看文件的内容。

### <a name="read-json-files"></a>读取 JSON 文件

下面的示例查询读取 JSON 和行分隔的 JSON 文件，并将每个文档作为单独的行返回。

```sql
select top 10 *
from openrowset(
        bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.jsonl',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
go
select top 10 *
from openrowset(
        bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b',
        rowterminator = '0x0b' --> You need to override rowterminator to read classic JSON
    ) with (doc nvarchar(max)) as rows
```

前面的示例查询中的 JSON 文档包含对象的数组。 查询将每个对象作为单独的行返回到结果集中。 请确保可以访问此文件。 如果文件受到 SAS 密钥或自定义标识的保护，则需要为 [sql 登录设置服务器级别凭据](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential)。 

### <a name="data-source-usage"></a>数据源使用情况

前面的示例使用文件的完整路径。 作为替代方法，可以创建一个外部数据源，其中包含指向存储根文件夹的位置，并使用该数据源和函数中的文件的相对路径 `OPENROWSET` ：

```sql
create external data source covid
with ( location = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases' );
go
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.jsonl',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
go
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.json',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b',
        rowterminator = '0x0b' --> You need to override rowterminator to read classic JSON
    ) with (doc nvarchar(max)) as rows
```

如果使用 SAS 密钥或自定义标识来保护数据源，则可以 [使用数据库范围凭据配置数据源](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential)。

在以下部分中，可以了解如何查询各种类型的 JSON 文件。

## <a name="parse-json-documents"></a>分析 JSON 文档

前面示例中的查询将每个 JSON 文档作为单个字符串返回到结果集的单独行中。 您可以使用函数 `JSON_VALUE` 和 `OPENJSON` 分析 JSON 文档中的值，并将它们作为关系值返回，如以下示例中所示：

| 日期 \_ 代表 | cases | 地域 \_ id |
| --- | --- | --- |
| 2020-07-24 | 3 | AF |
| 2020-07-25 | 7 | AF |
| 2020-07-26 | 4 | AF |
| 2020-07-27 | 8| AF |

### <a name="sample-json-document"></a>示例 JSON 文档

查询示例读取包含具有以下结构的文档的 *json* 文件：

```json
{
    "date_rep":"2020-07-24",
    "day":24,"month":7,"year":2020,
    "cases":13,"deaths":0,
    "countries_and_territories":"Afghanistan",
    "geo_id":"AF",
    "country_territory_code":"AFG",
    "continent_exp":"Asia",
    "load_date":"2020-07-25 00:05:14",
    "iso_country":"AF"
}
```

> [!NOTE]
> 如果这些文档存储为以行分隔的 JSON，则需要将 `FIELDTERMINATOR` 和设置 `FIELDQUOTE` 为0x0b。 如果具有标准 JSON 格式，则需要将设置 `ROWTERMINATOR` 为0x0b。

### <a name="query-json-files-using-json_value"></a>使用 JSON_VALUE 查询 JSON 文件

下面的查询演示了如何使用 [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 从 JSON 文档 (标题、发布者) 检索标量值：

```sql
select
    JSON_VALUE(doc, '$.date_rep') AS date_reported,
    JSON_VALUE(doc, '$.countries_and_territories') AS country,
    JSON_VALUE(doc, '$.cases') as cases,
    doc
from openrowset(
        bulk 'latest/ecdc_cases.jsonl',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
order by JSON_VALUE(doc, '$.geo_id') desc
```

### <a name="query-json-files-using-openjson"></a>使用 OPENJSON 查询 JSON 文件

以下查询使用 [OPENJSON](/sql/t-sql/functions/openjson-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)。 它将检索在塞尔维亚：

```sql
select
    *
from openrowset(
        bulk 'latest/ecdc_cases.jsonl',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
    cross apply openjson (doc)
        with (  date_rep datetime2,
                cases int,
                fatal int '$.deaths',
                country varchar(100) '$.countries_and_territories')
where country = 'Serbia'
order by country, date_rep desc;
```

## <a name="next-steps"></a>后续步骤

本系列文章中的下一篇文章将演示如何：

- [查询文件夹和多个文件](query-folders-multiple-csv-files.md)
- [创建和使用视图](create-use-views.md)
