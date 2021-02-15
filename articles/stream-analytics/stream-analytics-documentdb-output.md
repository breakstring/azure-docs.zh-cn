---
title: Azure Cosmos DB 的 Azure 流分析输出
description: 本文介绍如何使用 Azure 流分析保存 Azure Cosmos DB 的输出以进行 JSON 输出，从而实现对非结构化 JSON 数据进行数据存档和低延迟查询。
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/2/2020
ms.custom: seodec18
ms.openlocfilehash: 2d00d489ff248ecf5599d78e0a351c93248cf8ee
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98018083"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Azure Cosmos DB 的 Azure 流分析输出  
Azure 流分析可以针对 [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) 进行 JSON 输出，从而支持对非结构化 JSON 数据进行数据存档和低延迟查询。 本文档包括用于实现此配置的一些最佳做法。 使用 Azure Cosmos DB 作为输出时，建议你将作业设置为兼容级别 1.2。

如果不熟悉 Azure Cosmos DB，请参阅 [Azure Cosmos DB 文档](../cosmos-db/index.yml)了解入门知识。 

> [!Note]
> 目前，流分析只支持通过 SQL API 连接到 Azure Cosmos DB。
> 尚不支持使用其他 Azure Cosmos DB API。 如果使用其他 API 将流分析指向已创建的 Azure Cosmos DB 帐户，则可能无法正确存储数据。 

## <a name="basics-of-azure-cosmos-db-as-an-output-target"></a>作为输出目标的 Azure Cosmos DB 的基础知识
使用流分析中的 Azure Cosmos DB 输出可以将流处理结果作为 JSON 输出写入到 Azure Cosmos DB 容器中。 

流分析不会在数据库中创建容器。 而是需要预先创建它们。 然后，你可以控制 Azure Cosmos DB 容器的计费成本。 还可以使用 [Azure Cosmos DB APIs](/rest/api/cosmos-db/) 直接调整容器的性能、一致性和容量。

> [!Note]
> 必须从 Azure Cosmos DB 防火墙中将 0.0.0.0 添加到允许的 IP 列表。

以下部分详细介绍了 Azure Cosmos DB 的一些容器选项。

## <a name="tuning-consistency-availability-and-latency"></a>调整一致性、可用性和延迟
为了满足应用程序的要求，Azure Cosmos DB 允许你微调数据库和容器，并在一致性、可用性、延迟和吞吐量之间进行权衡。 

根据方案针对读写延迟需要什么级别的读取一致性，可以在数据库帐户上选择一致性级别。 可以通过纵向扩展容器中的请求单位 (RU) 数来提高吞吐量。 

另外，默认情况下，Azure Cosmos DB 会对容器的每个 CRUD 操作启用同步索引。 这是另一个有用选项，可控制 Azure Cosmos DB 中的读/写性能。 

有关详细信息，请参阅[更改数据库和查询的一致性级别](../cosmos-db/consistency-levels.md)一文。

## <a name="upserts-from-stream-analytics"></a>从流分析进行 Upsert 操作
由于流分析与 Azure Cosmos DB 集成，因此可以在容器中基于给定的“文档 ID”列插入或更新记录。 这也称为“upsert”。

流分析使用乐观 Upsert 方法。 即仅当由于文档 ID 冲突而插入失败时才进行更新。 

在 1.0 兼容级别中，流分析将此更新作为 PATCH 操作执行，因此可以对文档进行部分更新。 流分析添加新属性或增量替换现有属性。 但是，JSON 文档中数组属性值的更改会导致覆盖整个数组。 也就是说，不会合并数组。 

在 1.2 级别中，更新插入行为已修改为插入或替换文档。 关于兼容性级别 1.2 的部分，后面将进一步描述这种行为。

如果传入的 JSON 文档具有现有的 ID 字段，则该字段将自动用作 Azure Cosmos DB 中的“文档 ID”列。 任何后续的写入操作都以这种方式进行处理，这会导致以下情况之一：

- 唯一ID 导致插入。
- 重复的 ID 和设置为“ID”的“文档 ID”导致更新插入 。
- 在第一个文档之后，重复的 ID 和未设置的“文档 ID”导致错误。

如果要保存“所有”文档（包括具有重复 ID 的文档），请重命名查询中的 ID 字段（使用“AS”关键字）。 让 Azure Cosmos DB 创建 ID 字段，或用另一列的值替换 ID（使用“AS”关键字，或者使用“文档 ID”设置） 。

## <a name="data-partitioning-in-azure-cosmos-db"></a>Azure Cosmos DB 中的数据分区
Azure Cosmos DB 会根据工作负载自动缩放分区。 因此，建议使用[无限](../cosmos-db/partitioning-overview.md)容器作为数据分区方式。 当流分析写入到无限制的容器时，它会使用先前查询步骤或输入分区方案中一样多的并行写入器。

> [!NOTE]
> Azure 流分析仅支持顶级分区键的无限制容器。 例如，支持 `/region`。 不支持嵌套分区键（例如 `/region/name`）。 

你可能会收到以下警告，具体取决于你选择的分区键：

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

请务必选择包含许多不同值的分区键属性，并跨这些值均匀分配工作负载。 作为分区的自然项目，涉及同一分区键的请求受到单个分区的最大吞吐量的限制。 

属于同一分区键值的文档的存储大小上限为 20 GB（[物理分区大小上限](../cosmos-db/partitioning-overview.md)为 50 GB）。 [理想的分区键](../cosmos-db/partitioning-overview.md#choose-partitionkey)可以作为筛选器频繁出现在查询中，并具有足够的基数，以确保解决方案可缩放。

用于流分析查询和 Cosmos DB 的分区键不需要完全相同。 完全并行拓扑建议使用输入分区键 `PartitionId` 作为流分析查询的分区键，但是对于 Cosmos DB 容器的分区键，这可能不是建议的选择。

分区键也是 Cosmos DB 的存储过程和触发器中的事务处理的边界。 选择分区键时，应确保在事务中同时出现的文档使用相同的分区键值。 如需详细了解如何选择分区键，请参阅 [Azure Cosmos DB 中的分区](../cosmos-db/partitioning-overview.md)一文。

对于固定的 Azure Cosmos DB 容器，在这些容器已满后，流分析不允许通过任何方式进行纵向或横向扩展。 这些集合的大小上限为 10 GB，吞吐量上限为 10,000 RU/秒。 若要将数据从固定的容器迁移到无限制容器（例如，吞吐量至少为 1,000 RU/秒，且具有分区键），请使用[数据迁移工具](../cosmos-db/import-data.md)或[更改源库](../cosmos-db/change-feed.md)。

即将弃用写入多个固定容器的功能。 不建议将其用于横向扩展流分析作业。

## <a name="improved-throughput-with-compatibility-level-12"></a>使用兼容性级别 1.2 改进了吞吐量
借助兼容性级别 1.2，流分析支持使用本机集成来批量写入到 Azure Cosmos DB。 该集成可以有效地对 Azure Cosmos DB 进行写入，同时可以最大程度地提高吞吐量和有效处理限制请求。 

新兼容性级别改变了更新插入行为，提供一种改进的写入机制。 在 1.2 之前的级别，更新插入行为是插入或合并文档。 在 1.2 级别中，更新插入行为已修改为插入或替换文档。

在 1.2 之前的级别，流分析需使用自定义的存储过程将文档按分区键批量更新插入到 Azure Cosmos DB 中。 其中的批作为事务写入。 即使只有一条记录遇到暂时性错误（限制），也必须重试整个批。 因此，即使受到合理限制的方案的运行速度也会变得相对缓慢。

以下示例显示了从同一 Azure 事件中心输入读取数据的两个相同流分析作业。 这两个流分析作业已使用直通查询[完全分区](./stream-analytics-parallelization.md#embarrassingly-parallel-jobs)，并写入到相同的 Azure Cosmos DB 容器。 左侧的指标来自配置了兼容性级别 1.0 的作业。 右侧的指标来自配置了版本 1.2 的作业。 Azure Cosmos DB 容器的分区键是来自输入事件的唯一 GUID。

![流分析指标比较](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

事件中心的传入事件速率是配置引入的 Azure Cosmos DB 容器 (20K RU) 的 2 倍，因此，预期会在 Azure Cosmos DB 中进行限制。 但是，使用版本 1.2 的作业一贯以更高的吞吐量（输出事件数/分钟）写入，并且其平均 SU% 利用率更低。 在你的环境中，这种差异将取决于多个因素。 这些因素包括：事件格式的选择、输入事件/消息大小、分区键和查询。

![Azure Cosmos DB 指标比较](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

使用版本 1.2 时，流分析可以更智能地利用 Azure Cosmos DB 中 100% 的可用吞吐量，并且在发生节流限制或速率限制的情况下，只需重新提交极少的次数。 对于其他工作负载（例如，同时在容器上运行的查询），这可以提供更好的体验。 如需了解 Azure Cosmos DB 作为接收器（每秒接收 1000 到 10000 条消息）如何横向扩展流分析，请尝试[此 Azure 示例项目](https://github.com/Azure-Samples/streaming-at-scale/tree/main/eventhubs-streamanalytics-cosmosdb)。

1\.0 和 1.1 级别的 Azure Cosmos DB 输出具有相同的吞吐量。 强烈建议对 Azure Cosmos DB 的流分析使用兼容性级别 1.2。

## <a name="azure-cosmos-db-settings-for-json-output"></a>JSON 输出的 Azure Cosmos DB 设置

使用 Azure Cosmos DB 作为流分析中的输出时，会生成以下信息提示。

![Azure Cosmos DB 输出流的信息字段](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|字段           | 说明|
|-------------   | -------------|
|输出别名    | 用于在流分析查询中引用此输出的别名。|
|订阅    | Azure 订阅。|
|帐户 ID      | Azure Cosmos DB 帐户的名称或终结点 URI。|
|帐户密钥     | Azure Cosmos DB 帐户的共享访问密钥。|
|数据库        | Azure Cosmos DB 数据库名称。|
|容器名称 | 容器名称，如 `MyContainer`。 必须存在名为 `MyContainer` 的容器。  |
|文档 ID     | 可选。 输出事件中用作唯一键的列名称，插入或更新操作必须基于该键。 如果将其保留为空，则插入所有事件，但不使用更新选项。|

配置 Azure Cosmos DB 输出后，可以在查询中将其用作 [INTO 语句](/stream-analytics-query/into-azure-stream-analytics)的目标。 当使用这种方式进行 Azure Cosmos DB 输出时，[需要显式设置分区键](./stream-analytics-parallelization.md#partitions-in-inputs-and-outputs)。 

输出记录必须包含一个区分大小写的列，该列以 Azure Cosmos DB 中的分区键命名。 若要实现更大的并行化，该语句可能需要使用同一列的 [PARTITION BY 子句](./stream-analytics-parallelization.md#embarrassingly-parallel-jobs)。

下面是一个示例查询：

```SQL
    SELECT TollBoothId, PartitionId
    INTO CosmosDBOutput
    FROM Input1 PARTITION BY PartitionId
``` 

## <a name="error-handling-and-retries"></a>错误处理和重试

如果流分析将事件发送到 Azure Cosmos DB 时出现了暂时性故障、服务不可用或受到限制，流分析将无限期重试以成功完成操作。 但它不会尝试对以下失败进行重试：

- Unauthorized（HTTP 错误代码 401）
- NotFound（HTTP 错误代码 404）
- Forbidden（HTTP 错误代码 403）
- BadRequest（HTTP 错误代码 400）

## <a name="common-issues"></a>常见问题

1. 将向集合添加唯一索引约束，流分析的输出数据违反此约束。 请确保流分析的输出数据不违反唯一约束，或将约束删除。 有关详细信息，请参阅 [Azure Cosmos DB 中的唯一键约束](../cosmos-db/unique-keys.md)。

2. `PartitionKey` 列不存在。

3. `Id` 列不存在。

## <a name="next-steps"></a>后续步骤

* [了解 Azure 流分析的输出](stream-analytics-define-outputs.md) 
* [从 Azure 流分析输出到 Azure SQL 数据库](stream-analytics-sql-output-perf.md)
* [Azure 流分析自定义 blob 输出分区](stream-analytics-custom-path-patterns-blob-storage-output.md)