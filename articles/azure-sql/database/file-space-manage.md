---
title: Azure SQL 数据库文件空间管理
description: 本页介绍了如何管理 Azure SQL 数据库中的单一数据库和共用数据库的文件空间，并提供了代码示例来演示如何确定是否需要收缩单一数据库或共用数据库，以及如何执行数据库收缩操作。
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: jrasnick, sstein
ms.date: 12/22/2020
ms.openlocfilehash: 7bb754b892715adffc6ead99f3d866f9f9d8af9b
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2021
ms.locfileid: "99096485"
---
# <a name="manage-file-space-for-databases-in-azure-sql-database"></a>管理 Azure SQL 数据库中的数据库的文件空间
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

本文介绍 Azure SQL 数据库中不同类型的存储空间，以及当需要显式管理分配的文件空间时可以执行的步骤。

> [!NOTE]
> 本文不适用于 Azure SQL 托管实例。

## <a name="overview"></a>概述

使用 Azure SQL 数据库，在某些工作负载模式下，数据库的基础数据文件的分配可能会大于已使用数据页的数量。 如果使用的空间不断增大，并且后续删除了数据，则可能会出现这种情况。 这是因为分配的文件空间不会自动回收。

在以下情况下，可能需要监视文件空间用量并收缩数据文件：

- 当分配给数据库的文件空间达到池的最大大小时，允许在弹性池中增大数据。
- 允许减少单一数据库或弹性池的最大大小。
- 允许将单一数据库或弹性池更改为最大大小更小的其他服务层级或性能层。

### <a name="monitoring-file-space-usage"></a>监视文件空间用量

Azure 门户和以下 API 中显示的大多数存储空间指标仅度量已用数据页面的大小：

- 基于 Azure 资源管理器的指标 API，包括 PowerShell [get-metrics](/powershell/module/az.monitor/get-azmetric)
- T-SQL：[sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)

但是，以下 API 还度量分配给数据库和弹性池的空间大小：

- T-SQL：[sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)
- T-SQL：[sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)

### <a name="shrinking-data-files"></a>收缩数据文件

由于对数据库性能的潜在影响，Azure SQL 数据库不会自动收缩数据文件以回收已分配但未使用的空间。  但是，客户可遵循[回收未使用的分配空间](#reclaim-unused-allocated-space)中所述的步骤，在其选定的时间通过自助式操作收缩数据文件。

> [!NOTE]
> 与数据文件不同，Azure SQL 数据库会自动收缩日志文件，因为该操作不会影响数据库的性能。

## <a name="understanding-types-of-storage-space-for-a-database"></a>了解数据库存储空间的类型

了解以下存储空间数量对于管理数据库的文件空间非常重要。

|数据库数量|定义|注释|
|---|---|---|
|**已用数据空间**|用于存储数据库数据的空间量，以 8 KB 页面数为单位。|通常，已用空间会在执行插入操作时增大，在执行删除操作时减小。 在某些情况下，已用空间不会在执行插入或删除操作时发生变化，具体取决于该操作涉及的数据数量和模式，以及是否有任何碎片。 例如，从每个数据页中删除一行不一定会减小已用空间。|
|**已分配的数据空间**|可用于存储数据库数据的格式化文件空间量。|已分配的空间量会自动增长，但执行删除操作后永远不会减小。 此行为可确保将来的插入操作速度更快，因为不需要重新设置空间的格式。|
|**已分配但未使用的数据空间**|已分配的数据空间量与已使用的数据空间量之间的差值。|此数量表示通过收缩数据库数据文件可回收的最大可用空间量。|
|**数据最大大小**|可用于存储数据库数据的最大空间量。|已分配的数据空间量在增长后不能超过数据最大大小。|

下图演示了数据库的不同存储空间类型之间的关系。

![存储空间类型和关系](./media/file-space-manage/storage-types.png)

## <a name="query-a-single-database-for-storage-space-information"></a>查询单一数据库的存储空间信息

可使用以下查询来确定单一数据库的存储空间数量。  

### <a name="database-data-space-used"></a>已用的数据库数据空间

修改以下查询，返回已用的数据库数据空间量。  查询结果以 MB 为单位。

```sql
-- Connect to master
-- Database data space used in MB
SELECT TOP 1 storage_in_megabytes AS DatabaseDataSpaceUsedInMB
FROM sys.resource_stats
WHERE database_name = 'db1'
ORDER BY end_time DESC;
```

### <a name="database-data-space-allocated-and-unused-allocated-space"></a>已分配的，以及已分配但未使用的数据库数据空间

使用以下查询，返回已分配的，以及已分配但未使用的数据库数据空间量。  查询结果以 MB 为单位。

```sql
-- Connect to database
-- Database data space allocated in MB and database data space allocated unused in MB
SELECT SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB,
SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB
FROM sys.database_files
GROUP BY type_desc
HAVING type_desc = 'ROWS';
```

### <a name="database-data-max-size"></a>数据库数据最大大小

修改以下查询，返回数据库数据最大大小。  查询结果以字节为单位。

```sql
-- Connect to database
-- Database data max size in bytes
SELECT DATABASEPROPERTYEX('db1', 'MaxSizeInBytes') AS DatabaseDataMaxSizeInBytes;
```

## <a name="understanding-types-of-storage-space-for-an-elastic-pool"></a>了解弹性池存储空间的类型

了解以下存储空间数量对于管理弹性池的文件空间非常重要。

|弹性池数量|定义|注释|
|---|---|---|
|**已用数据空间**|弹性池中所有数据库已使用的数据空间总和。||
|**已分配的数据空间**|弹性池中所有数据库已分配的数据空间总和。||
|**已分配但未使用的数据空间**|弹性池中所有数据库已分配的数据空间量与已使用的数据空间量之间的差值。|此数量表示弹性池通过收缩数据库数据文件可回收的最大空间量。|
|**数据最大大小**|可由弹性池用于其所有数据库的最大数据空间量。|为弹性池分配的空间不应超过弹性池最大大小。  如果发生这种情况，可通过收缩数据库数据文件来回收未使用的空间。|

> [!NOTE]
> 错误消息 "弹性池已达到其存储限制" 表示数据库对象分配了足够的空间来满足弹性池存储限制，但数据空间分配中可能存在未使用的空间。 请考虑增加弹性池的存储限制或短期解决方案，使用下面的 [**回收未使用的已分配空间**](#reclaim-unused-allocated-space) 部分释放数据空间。 还应注意缩减数据库文件对性能产生负面影响的可能性，请参阅下面的 " [**重新生成索引**](#rebuild-indexes) " 一节。

## <a name="query-an-elastic-pool-for-storage-space-information"></a>查询弹性池的存储空间信息

可使用以下查询确定弹性池的存储空间数量。  

### <a name="elastic-pool-data-space-used"></a>已用的弹性池数据空间

修改以下查询，返回已用的弹性池数据空间量。  查询结果以 MB 为单位。

```sql
-- Connect to master
-- Elastic pool data space used in MB  
SELECT TOP 1 avg_storage_percent / 100.0 * elastic_pool_storage_limit_mb AS ElasticPoolDataSpaceUsedInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC;
```

### <a name="elastic-pool-data-space-allocated-and-unused-allocated-space"></a>已分配的，以及已分配但未使用的弹性池数据空间

修改以下示例来返回一个表，该表列出为弹性池中每个数据库分配的空间，以及已分配但未使用的空间。 该表中数据库的排序顺序为：已分配但未使用空间量最大的数据库排在最前，已分配但未使用空间量最小的数据库排在最后。  查询结果以 MB 为单位。  

将查询结果（确定分配给池中每个数据库的空间）相加，可以确定为弹性池分配的总空间。 分配的弹性池空间不应超过弹性池最大大小。  

> [!IMPORTANT]
> PowerShell Azure 资源管理器模块仍受 Azure SQL 数据库的支持，但所有未来的开发都是针对 Az.Sql 模块的。 AzureRM 模块至少在 2020 年 12 月之前将继续接收 bug 修补程序。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。 若要详细了解其兼容性，请参阅[新 Azure PowerShell Az 模块简介](/powershell/azure/new-azureps-module-az)。

PowerShell 脚本需要 SQL Server PowerShell 模块–请参阅 [下载 PowerShell 模块](/sql/powershell/download-sql-server-ps-module) 进行安装。

```powershell
$resourceGroupName = "<resourceGroupName>"
$serverName = "<serverName>"
$poolName = "<poolName>"
$userName = "<userName>"
$password = "<password>"

# get list of databases in elastic pool
$databasesInPool = Get-AzSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName `
    -ServerName $serverName -ElasticPoolName $poolName
$databaseStorageMetrics = @()

# for each database in the elastic pool, get space allocated in MB and space allocated unused in MB
foreach ($database in $databasesInPool) {
    $sqlCommand = "SELECT DB_NAME() as DatabaseName, `
    SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, `
    SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB `
    FROM sys.database_files `
    GROUP BY type_desc `
    HAVING type_desc = 'ROWS'"
    $serverFqdn = "tcp:" + $serverName + ".database.windows.net,1433"
    $databaseStorageMetrics = $databaseStorageMetrics + 
        (Invoke-Sqlcmd -ServerInstance $serverFqdn -Database $database.DatabaseName `
            -Username $userName -Password $password -Query $sqlCommand)
}

# display databases in descending order of space allocated unused
Write-Output "`n" "ElasticPoolName: $poolName"
Write-Output $databaseStorageMetrics | Sort -Property DatabaseDataSpaceAllocatedUnusedInMB -Descending | Format-Table
```

以下屏幕截图显示了脚本输出的示例：

![已分配的，以及已分配但未使用的弹性池空间示例](./media/file-space-manage/elastic-pool-allocated-unused.png)

### <a name="elastic-pool-data-max-size"></a>弹性池数据最大大小

修改以下 T-sql 查询，返回上次记录的弹性池数据最大大小。  查询结果以 MB 为单位。

```sql
-- Connect to master
-- Elastic pools max size in MB
SELECT TOP 1 elastic_pool_storage_limit_mb AS ElasticPoolMaxSizeInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC;
```

## <a name="reclaim-unused-allocated-space"></a>回收已分配但未使用的空间

> [!NOTE]
> 收缩命令在运行时影响数据库性能，如果可能，应在使用率较低的时间段内运行。

### <a name="dbcc-shrink"></a>DBCC 收缩

识别可回收已分配但未使用的空间的数据库后，请修改以下命令中的数据库名称，以收缩每个数据库的数据文件。

```sql
-- Shrink database data space allocated.
DBCC SHRINKDATABASE (N'db1');
```

收缩命令在运行时影响数据库性能，如果可能，应在使用率较低的时间段内运行。  

还应注意缩减数据库文件对性能产生负面影响的可能性，请参阅下面的 " [**重新生成索引**](#rebuild-indexes) " 一节。

有关此命令的详细信息，请参阅 [SHRINKDATABASE](/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql)。

### <a name="auto-shrink"></a>自动收缩

或者，可以为数据库启用自动收缩。  自动收缩可降低文件管理的复杂性，并且与 `SHRINKDATABASE` 或 `SHRINKFILE` 相比，对数据库性能的影响更小。  在管理包含多个数据库的弹性池时，自动收缩可能特别有用。  但是，与 `SHRINKDATABASE` 和 `SHRINKFILE` 相比，自动收缩在回收文件空间方面的效率更低。
默认情况下，按建议为大多数数据库禁用自动收缩。 有关详细信息，请参阅 [AUTO_SHRINK 注意事项](/troubleshoot/sql/admin/considerations-autogrow-autoshrink#considerations-for-auto_shrink)。

若要启用自动收缩，请修改以下命令中的数据库名称。

```sql
-- Enable auto-shrink for the database.
ALTER DATABASE [db1] SET AUTO_SHRINK ON;
```

有关此命令的详细信息，请参阅 [DATABASE SET](/sql/t-sql/statements/alter-database-transact-sql-set-options) 选项。

### <a name="rebuild-indexes"></a>重建索引

收缩数据库数据文件后，索引可能会碎片化，失去其性能优化效力。 如果性能降低，请考虑重建数据库索引。 有关碎片和重新生成索引的详细信息，请参阅[重新组织和重新生成索引](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes)。

## <a name="next-steps"></a>后续步骤

- 有关数据库最大大小的信息，请参阅：
  - [适用于单一数据库的 Azure SQL 数据库基于 vCore 的购买模型限制](resource-limits-vcore-single-databases.md)
  - [使用基于 DTU 的购买模型的单一数据库的资源限制](resource-limits-dtu-single-databases.md)
  - [适用于弹性池的 Azure SQL 数据库基于 vCore 的购买模型限制](resource-limits-vcore-elastic-pools.md)
  - [使用基于 DTU 的购买模型的弹性池的资源限制](resource-limits-dtu-elastic-pools.md)
- 有关 `SHRINKDATABASE` 命令的详细信息，请参阅 [SHRINKDATABASE](/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql)。
- 有关碎片和重新生成索引的详细信息，请参阅[重新组织和重新生成索引](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes)。