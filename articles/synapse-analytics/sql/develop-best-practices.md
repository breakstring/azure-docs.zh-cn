---
title: Synapse SQL 开发最佳做法
description: 针对 Synapse SQL 进行开发时应知道的建议和最佳做法。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 1fd7649cac6b636873ca529fe9780429d86697c6
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2021
ms.locfileid: "98120897"
---
# <a name="development-best-practices-for-synapse-sql"></a>Synapse SQL 开发最佳做法

本文介绍在开发数据仓库解决方案时的指导和最佳做法。 

## <a name="dedicated-sql-pool-development-best-practices"></a>专用 SQL 池开发最佳做法

### <a name="reduce-cost-with-pause-and-scale"></a>使用暂停和缩放来降低成本

若要了解如何通过暂停和缩放来降低成本，请参阅[管理计算](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。

### <a name="maintain-statistics"></a>维护统计信息

请确保每日或每次加载后更新统计信息。  创建和更新统计信息的性能与成本之间总有一些取舍。 如果发现维护所有统计信息所需的时间太长，则更有选择性地了解哪些列有统计信息，哪些列需要频繁更新。  

例如，你可能想要更新日期列，在这种情况下，可能会每天添加新的值。 

> [!NOTE]
> 对涉及联接的列、WHERE 子句中使用的列、在 GROUP BY 中找到的列进行信息统计，可以获得最大效益。

另请参阅[管理表统计信息](develop-tables-statistics.md)、[CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)、[UPDATE STATISTICS](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)。

### <a name="hash-distribute-large-tables"></a>哈希分布大型表

默认情况下，表是以轮循机制分布的。 利用此功能，用户可以轻松地开始创建表，而无需决定如何分散表。  轮循机制表的性能应可满足某些工作负荷的要求。 但在大多数情况下，选择分布列可获得更佳的性能。  

按列分布表的性能远远高于轮循机制表的最常见例子是联接两个大型事实表。  

例如，如果您有一个按 order_id 分布的 orders 表，并且通过 order_id 分发了一个事务表，则将 orders 表联接到 order_id 上的事务表时，此查询将变成传递查询。 

这意味着不再需要进行数据移动操作。  减少步骤意味着加快查询速度。  更少的数据移动也将让查询更快。

> [!TIP]
> 加载分布的表时，请确保传入数据的分布键没有排序，因为这会拖慢加载速度。  

请参阅以下链接，了解有关选择分布列如何能提升性能，以及如何在 CREATE TABLES 语句的 WITH 子句中定义分布式表的额外详细信息。

另请参阅[表概述](develop-tables-overview.md)、[表分布](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)、[选择表分布](/archive/blogs/sqlcat/choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service)、[CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 以及 [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)。

### <a name="do-not-over-partition"></a>不要过度分区
尽管数据分区对于维护数据（通过分区切换）或优化扫描（通过分区排除）很有效，但分区过多会减慢查询速度。  通常，可以很好地处理 SQL Server 的高粒度分区策略可能无法很好地在专用 SQL 池上工作。  

> [!NOTE]
> 通常，可以很好地处理 SQL Server 的高粒度分区策略可能无法很好地在专用 SQL 池上工作。  

如果每个分区的行数少于 1 百万，太多分区还会降低聚集列存储索引的效率。 专用 SQL 池将你的数据分区到60数据库。 

因此，如果创建具有 100 个分区的表，将得到 6000 个分区。  每个工作负荷都不同，因此最佳建议是尝试不同的分区，找出最适合工作负荷的分区。  

可以考虑的一种选择是，使用比 SQL Server 中适当粒度更粗的粒度。  例如，考虑使用每周或每月分区，而不是每日分区。

另请参阅[表分区](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。

### <a name="minimize-transaction-sizes"></a>最小化事务大小

在事务中运行的 INSERT、UPDATE、DELETE 语句，失败时必须回滚。  为了将长时间回滚的可能性降到最低，请尽可能将事务大小最小化。  这可以通过将 INSERT、UPDATE、DELETE 语句分成小部分来达成。  

例如，如果预期 INSERT 需要 1 小时，可将 INSERT 分成四个部分，以便将每次运行缩短到 15 分钟。

> [!TIP]
> 使用特殊的最低限度日志记录方案，像是 CTAS、TRUNCATE、DROP TABLE 或 INSERT 空表，来降低回滚的风险。  

另一个消除回滚的作法是使用“仅元数据”操作（像是分区切换）进行数据管理。  

例如，不要运行 DELETE 语句来删除表中所有 order_date 为 2001 年 10 月的行，而是将数据每月分区后，再从另一个表将有空分区之数据的分区调动出来（请参阅 ALTER TABLE 示例）。  

对于未分区的表，请考虑使用 CTAS 将想要保留的数据写入表中，而不是使用 DELETE。  如果 CTAS 花费的时间相同，则运行起来要安全得多，因为它的事务日志记录最少，并且可以根据需要快速取消。

另请参阅[了解事务](develop-transactions.md)、[优化事务](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)、[表分区](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)、[TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)、[ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 和 [Create table as select (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。

### <a name="use-the-smallest-possible-column-size"></a>使用最小可能的列大小

定义 DDL 时，使用支持数据的最小数据类型将提高查询性能。 此操作对于 CHAR 和 VARCHAR 列尤其重要。  

如果列中最长的值是 25 个字符，请将列定义为 VARCHAR(25)。  避免将所有字符列定义为较大的默认长度。  此外，将列定义为 VARCHAR，这是所需要的，而不是使用 NVARCHAR。

另请参阅[表概述](develop-tables-overview.md)、[表数据类型](develop-tables-data-types.md)和 [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)。

### <a name="optimize-clustered-columnstore-tables"></a>优化聚集列存储表

聚集列存储索引是将数据存储在专用 SQL 池中的最有效方法之一。  默认情况下，专用 SQL 池中的表创建为聚集列存储。  

为了让列存储表的查询获得最佳性能，良好的分段质量很重要。  当行在内存不足的状态下写入列存储表时，列存储分段质量可能降低。  

压缩行组中的行数可以测量分段质量。  有关检测和改善聚集列存储表的段质量的分步说明，请参阅 [列存储索引质量不佳的原因](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality) 和 [表索引](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 一文。  

由于高质量列存储段很重要，因此可以考虑使用中型或大型资源类中的用户 ID 来加载数据。 使用较低的[数据仓库单位](resource-consumption-models.md)值意味着需要向加载用户分配较大的资源类。

由于列存储表通常不会将数据推送到压缩的列存储段，因此，每个表中的行数超过1000000，并且每个专用 SQL 池表都分区为60表，除非该表的行数超过了60000000，否则列存储表将不会对查询产生益处。  

> [!TIP]
> 对于少于 6,000 万行的表，列存储索引可能不是最佳解决方案。  

此外，如果对数据进行分区，则需要考虑每个分区都需要有1000000行才能受益于聚集列存储索引。  如果表有 100 个分区，则它至少必须有 60 亿行才能受益于聚集列存储（60 个分，100 个分区，100 万行）。  

如果表中不到 60 亿行，请减少分区数目，或考虑改用堆表。  使用具有辅助索引的堆表而不是列存储表也许能提升性能，值得试验。

查询列存储表时，如果只选择需要的列，查询运行将更快速。  

另请参阅[表索引](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)、[列存储索引指南](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)、[重新生成列存储索引](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#rebuilding-indexes-to-improve-segment-quality)。

## <a name="serverless-sql-pool-development-best-practices"></a>无服务器 SQL 池开发最佳实践

### <a name="general-considerations"></a>一般注意事项

无服务器 SQL 池允许查询 Azure 存储帐户中的文件。 它没有本地存储或引入功能，这意味着，查询所面向的所有文件都是无服务器 SQL 池的外部。 因此，与从存储读取文件相关的所有内容都可能会影响查询性能。

### <a name="colocate-azure-storage-account-and-serverless-sql-pool"></a>归置 Azure 存储帐户和无服务器 SQL 池

若要最大程度地减少延迟，请归置你的 Azure 存储帐户和无服务器 SQL 池终结点。 在创建工作区期间预配的存储帐户和终结点位于同一区域。

为了获得最佳性能，如果访问具有无服务器 SQL 池的其他存储帐户，请确保它们位于同一区域。 如果它们不在同一区域，那么从远程区域到终结点区域的数据网络传输延迟将会增加。

### <a name="azure-storage-throttling"></a>Azure 存储限制

多个应用和服务可以访问你的存储帐户。 当应用程序、服务和无服务器 SQL 池工作负荷生成的组合 IOPS 或吞吐量超过存储帐户的限制时，会进行存储限制。 发生存储限制时，将对查询性能造成严重的负面影响。

检测到限制后，无服务器 SQL 池有此方案的内置处理。 无服务器 SQL 池会以较慢的速度向存储请求请求，直到限制得以解决。 

但是，为实现最佳查询执行，建议你不要在执行查询的过程中将存储帐户用于其他工作负荷。

### <a name="prepare-files-for-querying"></a>准备文件以供查询

如果可以，尽可能准备文件来提升性能：

- 将 CSV 转换为 Parquet（Parquet 是分栏格式）。 由于它是压缩的，因此它的文件大小比具有相同数据的 CSV 文件的文件大小小，无服务器 SQL 池将需要更少的时间和存储请求来进行读取。
- 如果查询目标是一个大文件，那么把它拆分为多个较小文件将会让你受益匪浅。
- 尽量让 CSV 文件大小小于 10GB。
- 对于单个 OPENROWSET 路径或外部表 LOCATION，最好有相等大小的文件。
- 通过将分区存储到不同的文件夹或文件名，对数据进行分区（请查看[使用 filename 和 filepath 函数定目标到特定分区](#use-fileinfo-and-filepath-functions-to-target-specific-partitions)）。

### <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>使用 fileinfo 和 filepath 函数定目标到特定分区

数据通常是以分区形式组织。 可以指示无服务器 SQL 池查询特定文件夹和文件。 这样做可以减少查询读取和处理所需的文件数和数据量。 

因此，将获得更好的性能。 有关详细信息，请查看 [filename](query-data-storage.md#filename-function) 和 [filepath](query-data-storage.md#filepath-function) 函数，以及有关如何[查询特定文件](query-specific-files.md)的示例。

如果存储中的数据没有进行分区，可以考虑对其进行分区，以便使用这些函数来优化以这些文件为目标的查询。

查询无服务器 SQL 池中 [的 Azure Synapse 外部表的分区 Apache Spark](develop-storage-files-spark-tables.md) 时，查询将自动仅针对所需的文件。

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>使用 CETAS 增强查询性能和联接

[CETAS](develop-tables-cetas.md) 是无服务器 SQL 池中可用的最重要的功能之一。 CETAS 是一种并行操作，用于创建外部表元数据，并将 SELECT 查询的结果导出到存储帐户中的一组文件。

可以使用 CETAS 将查询的常用部分（如联接的引用表）存储到一组新的文件中。 接下来，可以联接到这一个外部表，而不是在多个查询中重复常用联接。 

当 CETAS 生成 Parquet 文件时，当第一个查询针对此外部表时，将自动创建统计信息，并且你将获得更高的性能。

### <a name="next-steps"></a>后续步骤

如果你需要本文中未提供的信息，请使用此页面左侧的 " **搜索 doc** 函数" 以搜索所有 SQL 池文档。  [Microsoft 问答&一个用于 Azure Synapse analytics 的问题页](/answers/topics/azure-synapse-analytics.html)，可以向其他用户和 Azure Synapse Analytics 产品组提出问题。 我们会主动观察此论坛，确保用户的问题获得其他用户或我们的回答。  

如果你希望在 Stack Overflow 上提出问题，我们还会获得一个 [Azure Synapse Analytics Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-sqldw)。
