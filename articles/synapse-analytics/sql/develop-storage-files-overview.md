---
title: 在无服务器 SQL 池中访问存储中的文件
description: 介绍如何在 Azure Synapse Analytics 中使用无服务器 SQL 池查询存储文件。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/19/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: f398f80e4e283f971e0d947d0dda131e12fe88a7
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2021
ms.locfileid: "98120387"
---
# <a name="access-external-storage-using-serverless-sql-pool-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中使用无服务器 SQL 池访问外部存储

本文档介绍用户如何从无服务器 SQL 池的 Azure 存储中存储的文件读取数据。 用户具有以下用于访问存储的选项：

- [OPENROWSET](develop-openrowset.md) 函数，可对 Azure 存储中的文件进行即席查询。
- [外部表](develop-tables-external-tables.md)，它是基于一组外部文件生成的预定义数据结构。

用户可以使用[不同的身份验证方法](develop-storage-files-storage-access-control.md)，例如 Azure AD 传递身份验证（Azure AD 主体的默认方法）和 SAS 身份验证（SQL 主体的默认方法）。

## <a name="query-files-using-openrowset"></a>使用 OPENROWSET 查询文件

借助 OPENROWSET，用户可以查询 Azure 存储中的外部文件，前提是他们有权访问该存储。 连接到无服务器 SQL 池的用户应使用以下查询来读取 Azure 存储中文件的内容：

```sql
SELECT * FROM
 OPENROWSET(BULK 'https://<storage_account>.dfs.core.windows.net/<container>/<path>/*.parquet', format= 'parquet') as rows
```

用户可以使用以下访问规则来访问存储：

- Azure AD 用户 - `OPENROWSET` 将使用调用方的 Azure AD 标识来访问 Azure 存储或通过匿名访问来访问存储。
- SQL 用户 - `OPENROWSET` 将通过匿名访问来访问存储；或者可使用 SAS 令牌或工作区的托管标识模拟它。

### <a name="impersonation"></a>[模拟](#tab/impersonation)

SQL 主体也可以使用 OPENROWSET 直接查询受 SAS 令牌保护的文件或工作区的托管标识。 如果 SQL 用户执行此函数，则具有 `ALTER ANY CREDENTIAL` 权限的 Power User 必须创建服务器范围的凭据，该凭据与该函数（使用存储名称和容器）中的 URL 匹配，并向 OPENROWSET 函数的调用方授予此凭据的 REFERENCES 权限：

```sql
EXECUTE AS somepoweruser

CREATE CREDENTIAL [https://<storage_account>.dfs.core.windows.net/<container>]
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sas token';

GRANT REFERENCES CREDENTIAL::[https://<storage_account>.dfs.core.windows.net/<container>] TO sqluser
```

如果没有与 URL 匹配的服务器级凭据，或 SQL 用户没有此凭据的引用权限，则会返回错误。 SQL 主体无法使用某个 Azure AD 标识进行模拟。

### <a name="direct-access"></a>[直接访问](#tab/direct-access)

无需额外的设置即可使 Azure AD 用户能够使用其标识访问文件。
任何用户都可以访问允许匿名访问的 Azure 存储（无需进行其他设置）。

---

> [!NOTE]
> 此版本的 OPENROWSET 旨在使用默认的身份验证快速轻松地浏览数据。 若要利用模拟或托管标识，请将 OPENROWSET 与下一部分中所述的 DATASOURCE 一起使用。

## <a name="query-data-sources-using-openrowset"></a>使用 OPENROWSET 查询数据源

借助 OPENROWSET，用户可以查询放置在某些外部数据源上的文件：

```sql
SELECT * FROM
 OPENROWSET(BULK 'file/path/*.parquet',
 DATASOURCE = MyAzureInvoices,
 FORMAT= 'parquet') as rows
```

执行此查询的用户必须能够访问文件。 如果用户无法通过其 [Azure AD 标识](develop-storage-files-storage-access-control.md?tabs=user-identity)或[匿名访问](develop-storage-files-storage-access-control.md?tabs=public-access)来直接访问文件，则必须使用 [SAS 令牌](develop-storage-files-storage-access-control.md?tabs=shared-access-signature)或[工作区的托管标识](develop-storage-files-storage-access-control.md?tabs=managed-identity)来模拟用户。

### <a name="impersonation"></a>[模拟](#tab/impersonation)

`DATABASE SCOPED CREDENTIAL` 指定如何访问参考数据源（当前为 SAS 和托管标识）上的文件。 具有 `CONTROL DATABASE` 权限的 Power User 将需要创建 `DATABASE SCOPED CREDENTIAL`（将用于访问存储）和 `EXTERNAL DATA SOURCE`（指定应使用的数据源和凭据的 URL）：

```sql
EXECUTE AS somepoweruser;

-- Create MASTER KEY if it doesn't exists in database
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'some very strong password';

CREATE DATABASE SCOPED CREDENTIAL AccessAzureInvoices
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
 SECRET = '******srt=sco&amp;sp=rwac&amp;se=2017-02-01T00:55:34Z&amp;st=201********' ;

CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>/' ,
 CREDENTIAL = AccessAzureInvoices) ;
```

调用方必须具有以下权限之一才能执行 OPENROWSET 函数：

- 执行 OPENROWSET 所需的权限之一：
  - `ADMINISTER BULK OPERATIONS`，使登录名可以执行 OPENROWSET 函数。
  - `ADMINISTER DATABASE BULK OPERATIONS`，使数据库范围内的用户可以执行 OPENROWSET 函数。
- `EXTERNAL DATA SOURCE` 中引用的凭据的 `REFERENCES DATABASE SCOPED CREDENTIAL`。

### <a name="direct-access"></a>[直接访问](#tab/direct-access)

用户可以创建不带 CREDENTIAL 的 EXTERNAL DATA SOURCE 来引用公共访问存储，也可以使用 Azure AD 传递身份验证：

```sql
CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>') ;
```
---
## <a name="external-table"></a>EXTERNAL TABLE

具有读取表权限的用户可以使用基于一组 Azure 存储文件夹和文件创建的 EXTERNAL TABLE 来访问外部文件。

[具有创建外部表权限](/sql/t-sql/statements/create-external-table-transact-sql?preserve-view=true&view=sql-server-ver15#permissions)（例如 CREATE TABLE 和 ALTER ANY CREDENTIAL 或 REFERENCES DATABASE SCOPED CREDENTIAL）的用户可以使用以下脚本基于 Azure 存储数据源创建表：

```sql
CREATE EXTERNAL TABLE [dbo].[DimProductexternal]
( ProductKey int, ProductLabel nvarchar, ProductName nvarchar )
WITH
(
LOCATION='/DimProduct/year=*/month=*' ,
DATA_SOURCE = AzureDataLakeStore ,
FILE_FORMAT = TextFileFormat
) ;
```

从此表中读取数据的用户必须能够访问文件。 如果用户无法使用其 [Azure AD 标识](develop-storage-files-storage-access-control.md?tabs=user-identity)或通过[匿名访问](develop-storage-files-storage-access-control.md?tabs=public-access)来直接访问文件，则必须使用 [SAS 令牌](develop-storage-files-storage-access-control.md?tabs=shared-access-signature)或[工作区的托管标识](develop-storage-files-storage-access-control.md?tabs=managed-identity)来模拟用户。

### <a name="impersonation"></a>[模拟](#tab/impersonation)

DATABASE SCOPED CREDENTIAL 指定如何访问引用的数据源上的文件。 拥有 CONTROL DATABASE 权限的用户将需要创建 DATABASE SCOPED CREDENTIAL（将用于访问存储）和 EXTERNAL DATA SOURCE（指定应使用的数据源和凭据的 URL）：

```sql
EXECUTE AS somepoweruser;

-- Create MASTER KEY if it doesn't exists in database
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'some very strong password';

CREATE DATABASE SCOPED CREDENTIAL cred
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
 SECRET = '******srt=sco&sp=rwac&se=2017-02-01T00:55:34Z&st=201********' ;

CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
 WITH ( LOCATION = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>' ,
 CREDENTIAL = cred
 ) ;
```

### <a name="direct-access"></a>[直接访问](#tab/direct-access)

用户可以创建不带 CREDENTIAL 的 EXTERNAL DATA SOURCE 来引用公共访问存储，也可以使用 Azure AD 传递身份验证：

```sql
CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>') ;
```
---

### <a name="read-external-files-with-external-table"></a>使用 EXTERNAL TABLE 读取外部文件

借助 EXTERNAL TABLE，你可以读取使用标准 SQL SELECT 语句通过数据源引用的文件中的数据：

```sql
SELECT *
FROM dbo.DimProductsExternal
```

调用方必须具有以下权限才能读取数据：
- 对外部表的 `SELECT` 权限
- `REFERENCES DATABASE SCOPED CREDENTIAL` 权限（如果 `DATA SOURCE` 具有 `CREDENTIAL`）

## <a name="permissions"></a>权限

下表列出了上面列出的操作所需的权限。

| 查询 | 所需的权限|
| --- | --- |
| OPENROWSET(BULK)，不包含数据源 | `ADMINISTER BULK OPERATIONS`、`ADMINISTER DATABASE BULK OPERATIONS` 或 SQL 登录名必须对受 SAS 保护的存储具有 REFERENCES CREDENTIAL::\<URL> |
| OPENROWSET(BULK)，包含不带凭据的数据源 | `ADMINISTER BULK OPERATIONS` 或 `ADMINISTER DATABASE BULK OPERATIONS`， |
| OPENROWSET(BULK)，包含带凭据的数据源 | `REFERENCES DATABASE SCOPED CREDENTIAL` 和 `ADMINISTER BULK OPERATIONS` 之一或 `ADMINISTER DATABASE BULK OPERATIONS` 之一 |
| CREATE EXTERNAL DATA SOURCE | `ALTER ANY EXTERNAL DATA SOURCE` 和 `REFERENCES DATABASE SCOPED CREDENTIAL` |
| CREATE EXTERNAL TABLE | `CREATE TABLE`、`ALTER ANY SCHEMA`、`ALTER ANY EXTERNAL FILE FORMAT` 和 `ALTER ANY EXTERNAL DATA SOURCE` |
| SELECT FROM EXTERNAL TABLE | `SELECT TABLE` 和 `REFERENCES DATABASE SCOPED CREDENTIAL` |
| CETAS | 创建表：`CREATE TABLE`、`ALTER ANY SCHEMA`、`ALTER ANY DATA SOURCE` 和 `ALTER ANY EXTERNAL FILE FORMAT`。 读取数据：查询中每个表/视图/函数的 `ADMINISTER BULK OPERATIONS` 或 `REFERENCES CREDENTIAL` 或 `SELECT TABLE` + 对存储的 R/W 权限 |

## <a name="next-steps"></a>后续步骤

现在，你已准备好继续学习以下操作指南文章：

- [查询存储中的数据](query-data-storage.md)

- [查询 CSV 文件](query-single-csv-file.md)

- [查询 Parquet 文件](query-parquet-files.md)

- [查询 JSON 文件](query-json-files.md)

- [查询文件夹和多个文件](query-folders-multiple-csv-files.md)

- [使用分区和元数据函数](query-specific-files.md)

- [查询嵌套类型](query-parquet-nested-types.md)

- [创建和使用视图](create-use-views.md)