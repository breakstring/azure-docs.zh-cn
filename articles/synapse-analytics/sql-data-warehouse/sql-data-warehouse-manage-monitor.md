---
title: 使用 DMV 监视专用 SQL 池工作负载
description: 了解如何使用 DMV 监视 Azure Synapse Analytics 专用 SQL 池工作负载和查询执行情况。
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/24/2020
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: synapse-analytics
ms.openlocfilehash: 62064eaae6aa7fb3438845170497035473227d30
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685202"
---
# <a name="monitor-your-azure-synapse-analytics-dedicated-sql-pool-workload-using-dmvs"></a>使用 DMV 监视 Azure Synapse Analytics 专用 SQL 池工作负载

本文介绍如何使用动态管理视图 (DMV) 监视工作负载，包括调查 SQL 池中的查询执行情况。

## <a name="permissions"></a>权限

若要查询本文中的 DMV，需要 VIEW DATABASE STATE  或 CONTROL  权限。 通常情况下，首选授予 VIEW DATABASE STATE  权限，因为该权限的限制要大得多。

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>监视连接

所有到数据仓库的登录都会记录到 [sys.dm_pdw_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-sessions-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 中。  此 DMV 包含最后 10,000 个登录。  session_id 是主键，每次进行新的登录时按顺序分配。

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>监视查询执行

所有在 SQL 池上执行的查询都会记录到 [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 中。  此 DMV 包含最后 10,000 个执行的查询。  request_id 对每个查询进行唯一标识，并且为此 DMV 的主键。  request_id 在每次进行新的查询时按顺序分配，并会加上前缀 QID，代表查询 ID。  针对给定 session_id 查询此 DMV 将显示给定登录的所有查询。

> [!NOTE]
> 存储过程使用多个请求 ID。  按先后顺序分配请求 ID。

以下是调查特定查询的查询执行计划和时间所要遵循的步骤。

### <a name="step-1-identify-the-query-you-wish-to-investigate"></a>步骤 1：确定想要调查的查询

```sql
-- Monitor active queries
SELECT *
FROM sys.dm_pdw_exec_requests
WHERE status not in ('Completed','Failed','Cancelled')
  AND session_id <> session_id()
ORDER BY submit_time DESC;

-- Find top 10 queries longest running queries
SELECT TOP 10 *
FROM sys.dm_pdw_exec_requests
ORDER BY total_elapsed_time DESC;

```

从前面的查询结果中，记下想要调查的查询的 **请求 ID** 。

由于存在大量活动的运行查询，因此处于“挂起”  状态的查询可以排队。 这些查询也出现在类型为 UserConcurrencyResourceType 的 [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 等待查询中。 有关并发限制的信息，请参阅[内存和并发限制](memory-concurrency-limits.md)或[用于工作负载管理的资源类](resource-classes-for-workload-management.md)。 查询也可能因其他原因（如对象锁定）处于等待状态。  如果查询正在等待资源，请参阅本文后面的[调查等待资源的查询](#monitor-waiting-queries)。

为了简化查询在 [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 表中的查找，请使用 [LABEL](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 为查询分配一条注释，可以在 sys.dm_pdw_exec_requests 视图中查询到该注释。

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;

-- Find a query with the Label 'My Query'
-- Use brackets when querying the label column, as it it a key word
SELECT  *
FROM    sys.dm_pdw_exec_requests
WHERE   [label] = 'My Query';
```

### <a name="step-2-investigate-the-query-plan"></a>步骤 2：调查查询计划

使用请求 ID 从 [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 检索查询的分布式 SQL (DSQL) 计划

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

当 DSQL 计划的执行时间超出预期时，原因可能是计划很复杂，包含许多 DSQL 步骤，也可能是一个步骤占用很长的时间。  如果计划有很多步骤，包含多个移动操作，可考虑优化表分布，减少数据移动。 [表分布](sql-data-warehouse-tables-distribute.md)一文说明了为何必须移动数据才能解决查询。 该文还介绍了一些用于最大程度减少数据移动的分布策略。

若要进一步调查单个步骤的详细信息，可检查长时间运行的查询步骤的 *operation_type* 列并记下 **步骤索引**：

* 针对 SQL 操作继续执行步骤 3：OnOperation、RemoteOperation、ReturnOperation。
* 针对数据移动操作继续执行步骤 4：ShuffleMoveOperation、BroadcastMoveOperation、TrimMoveOperation、PartitionMoveOperation、MoveOperation、CopyOperation。

### <a name="step-3-investigate-sql-on-the-distributed-databases"></a>步骤 3：调查分布式数据库上的 SQL

使用请求 ID 和步骤索引从 [sys.dm_pdw_sql_requests](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 中检索详细信息，其中包含所有分布式数据库上的查询步骤的执行信息。

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

当查询步骤正在运行时，可以使用 [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 从 SQL Server 计划缓存中检索 SQL Server 估计计划，了解在特定分布基础上运行的步骤。

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL pool or control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-4-investigate-data-movement-on-the-distributed-databases"></a>步骤 4：调查在分布式数据库上进行的数据移动

使用请求 ID 和步骤索引检索在 [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 中的每个分布上运行的数据移动步骤的相关信息。

```sql
-- Find information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

* 检查 *total_elapsed_time* 列，以查看是否有特定分布在数据移动上比其他分布花费了更多时间。
* 对于长时间运行的分布，请检查 *rows_processed* 列，以查看从该分布移动的行数是否远远多于其他分布。 如果是这样，此发现可能指示基础数据倾斜。 数据偏斜的一个原因是在具有多个 NULL 值的列上分布（其行将全部位于同一分布中）。 通过避免在这些类型的列上分发或筛选查询以尽可能消除 NULL 来防止查询速度过慢。 

如果查询正在运行，则可以使用 [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 从 SQL Server 计划缓存中检索 SQL Server 估计计划，以了解特定分布中当前正在运行的 SQL 步骤。

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL pool Compute or control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>

## <a name="monitor-waiting-queries"></a>监视正在等待的查询

如果查询未取得进展（因其正在等待资源），下面是显示查询正在等待的所有资源的查询。

```sql
-- Find queries
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,
      waits.state,
      waits.object_type,
      waits.object_name
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID####'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

如果查询正在主动等待另一个查询中的资源，则状态将为 **AcquireResources**。  如果查询具有全部所需资源，则状态为 **Granted**。

## <a name="monitor-tempdb"></a>监视 tempdb

tempdb 用于在查询执行期间保存临时结果。 重度使用 tempdb 数据库可能会导致查询性能变慢。 对于配置的每个 DW100c，将分配 399 GB 的 tempdb 空间（DW1000c 的总 tempdb 空间为 3.99 TB）。  下面是有关监视 tempdb 用量以及在查询中减少 tempdb 用量的提示。

### <a name="monitoring-tempdb-with-views"></a>使用视图监视 tempdb

若要监视 tempdb 用量，请先从[适用于 SQL 池的 Microsoft 工具包](https://github.com/Microsoft/sql-data-warehouse-samples/tree/master/solutions/monitoring)安装 [microsoft.vw_sql_requests](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/solutions/monitoring/scripts/views/microsoft.vw_sql_requests.sql) 视图。 然后可执行以下查询，以查看在每个节点中执行的所有查询所消耗的 tempdb 用量：

```sql
-- Monitor tempdb
SELECT
    sr.request_id,
    ssu.session_id,
    ssu.pdw_node_id,
    sr.command,
    sr.total_elapsed_time,
    es.login_name AS 'LoginName',
    DB_NAME(ssu.database_id) AS 'DatabaseName',
    (es.memory_usage * 8) AS 'MemoryUsage (in KB)',
    (ssu.user_objects_alloc_page_count * 8) AS 'Space Allocated For User Objects (in KB)',
    (ssu.user_objects_dealloc_page_count * 8) AS 'Space Deallocated For User Objects (in KB)',
    (ssu.internal_objects_alloc_page_count * 8) AS 'Space Allocated For Internal Objects (in KB)',
    (ssu.internal_objects_dealloc_page_count * 8) AS 'Space Deallocated For Internal Objects (in KB)',
    CASE es.is_user_process
    WHEN 1 THEN 'User Session'
    WHEN 0 THEN 'System Session'
    END AS 'SessionType',
    es.row_count AS 'RowCount'
FROM sys.dm_pdw_nodes_db_session_space_usage AS ssu
    INNER JOIN sys.dm_pdw_nodes_exec_sessions AS es ON ssu.session_id = es.session_id AND ssu.pdw_node_id = es.pdw_node_id
    INNER JOIN sys.dm_pdw_nodes_exec_connections AS er ON ssu.session_id = er.session_id AND ssu.pdw_node_id = er.pdw_node_id
    INNER JOIN microsoft.vw_sql_requests AS sr ON ssu.session_id = sr.spid AND ssu.pdw_node_id = sr.pdw_node_id
WHERE DB_NAME(ssu.database_id) = 'tempdb'
    AND es.session_id <> @@SPID
    AND es.login_name <> 'sa'
ORDER BY sr.request_id;
```

如果某个查询消耗了大量内存或收到了与 tempdb 分配相关的错误消息，原因可能是运行的极大规模 [CREATE TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) 或 [INSERT SELECT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 语句在最终的数据移动操作期间失败。 在分布式查询计划中，紧靠在最后一条 INSERT SELECT 前面的语句会将此操作识别为 ShuffleMove 操作。  使用 [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 监视 ShuffleMove 操作。

最常见的缓解措施是将 CTAS 或 INSERT SELECT 语句分解为多个加载语句，使数据量不会超过每个节点不超过 1 TB tempdb 空间的限制。 也可以将群集扩展到更大的大小，以便将 tempdb 大小分散到更多的节点，从而减少每个节点的 tempdb 空间量。

除了 CTAS 和 INSERT SELECT 语句外，在内存不足的情况下运行的大型复杂查询可能会溢出到 tempdb 中，导致查询失败。  请考虑使用更大的[资源类](resource-classes-for-workload-management.md)运行，以避免溢出到 tempdb。

## <a name="monitor-memory"></a>监视内存

内存可能是性能缓慢和内存不足问题的根本原因。 如果发现 SQL Server 内存用量在执行查询的过程中达到其限制，请考虑扩展数据仓库。

以下查询返回每个节点的 SQL Server 内存用量和内存压力：

```sql
-- Memory consumption
SELECT
  pc1.cntr_value as Curr_Mem_KB,
  pc1.cntr_value/1024.0 as Curr_Mem_MB,
  (pc1.cntr_value/1048576.0) as Curr_Mem_GB,
  pc2.cntr_value as Max_Mem_KB,
  pc2.cntr_value/1024.0 as Max_Mem_MB,
  (pc2.cntr_value/1048576.0) as Max_Mem_GB,
  pc1.cntr_value * 100.0/pc2.cntr_value AS Memory_Utilization_Percentage,
  pc1.pdw_node_id
FROM
-- pc1: current memory
sys.dm_pdw_nodes_os_performance_counters AS pc1
-- pc2: total memory allowed for this SQL instance
JOIN sys.dm_pdw_nodes_os_performance_counters AS pc2
ON pc1.object_name = pc2.object_name AND pc1.pdw_node_id = pc2.pdw_node_id
WHERE
pc1.counter_name = 'Total Server Memory (KB)'
AND pc2.counter_name = 'Target Server Memory (KB)'
```

## <a name="monitor-transaction-log-size"></a>监视事务日志大小

以下查询返回每个分布区的事务日志大小。 如果其中一个日志文件将达到 160 GB，则应考虑纵向扩展实例或限制事务大小。

```sql
-- Transaction log size
SELECT
  instance_name as distribution_db,
  cntr_value*1.0/1048576 as log_file_size_used_GB,
  pdw_node_id
FROM sys.dm_pdw_nodes_os_performance_counters
WHERE
instance_name like 'Distribution_%'
AND counter_name = 'Log File(s) Used Size (KB)'
```

## <a name="monitor-transaction-log-rollback"></a>监视事务日志回滚

如果查询失败或需要花费很长时间才能继续，则你可以检查并监视是否发生了任何事务回滚。

```sql
-- Monitor rollback
SELECT
    SUM(CASE WHEN t.database_transaction_next_undo_lsn IS NOT NULL THEN 1 ELSE 0 END),
    t.pdw_node_id,
    nod.[type]
FROM sys.dm_pdw_nodes_tran_database_transactions t
JOIN sys.dm_pdw_nodes nod ON t.pdw_node_id = nod.pdw_node_id
GROUP BY t.pdw_node_id, nod.[type]
```

## <a name="monitor-polybase-load"></a>监视 PolyBase 负载

以下查询提供了负载进度的大致估计。 查询仅显示当前正在处理的文件。

```sql

-- To track bytes and files
SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files,
    sum(s.bytes_processed)/1024/1024/1024 as gb_processed
FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
        on r.request_id = s.request_id
GROUP BY
    r.command,
    s.request_id,
    r.status
ORDER BY
    nbr_files desc,
    gb_processed desc;
```

## <a name="next-steps"></a>后续步骤

有关 DMV 的详细信息，请参阅[系统视图](../sql/reference-tsql-system-views.md)。
