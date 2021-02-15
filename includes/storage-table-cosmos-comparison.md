---
title: include 文件
description: include 文件
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: include
ms.date: 01/08/2021
ms.author: mimig
ms.custom: include file
ms.openlocfilehash: a7e34f077ce1b2541168df40f2806fdb24a63a79
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98050743"
---
如果当前使用 Azure 表存储，那么改用 Azure Cosmos DB 表 API 可以获得以下好处：

|Feature | Azure 表存储 | Azure Cosmos DB 表 API |
| --- | --- | --- |
| 延迟 | 快速，但对延迟没有上限。 | 读取操作和写入操作的低至个位数的毫秒级延迟（通过以下统计数据提供支持：在全球任何位置，对于任何规模，99% 的情况下读取操作的延迟 < 10 毫秒、写入操作的延迟 < 15 毫秒）。 |
| 吞吐量 | 可变吞吐量模型。 表的可伸缩性限制为 20,000 个操作/秒。 | 使用 SLA 支持的[每个表专用保留吞吐量](../articles/cosmos-db/request-units.md)实现高度可缩放。 帐户没有吞吐量上限，每个表支持 >1000 万个操作/秒（在预配的吞吐量模式下）。 |
| 全球分布 | 具有一个可选可读辅助读取区域以实现高可用性的单一区域。 不能启动故障转移。 | 从 1 个到 30 多个区域进行[统包式全局分发](../articles/cosmos-db/distribute-data-globally.md)。 支持在全球各地随时[自动和手动故障转移](../articles/cosmos-db/high-availability.md)。 |
| 索引 | 仅对 PartitionKey 和 RowKey 建立主索引。 没有辅助索引。 | 自动对所有属性完成编制索引，没有索引管理。 |
| 查询 | 执行查询时使用主键的索引，否则进行扫描。 | 查询可以利用属性的自动索引缩短查询时间。 |
| 一致性 | 在主要区域内实现强一致性。 在辅助区域内实现最终一致性。 | [五个定义完善的一致性级别](../articles/cosmos-db/consistency-levels.md)可基于应用程序需要权衡可用性、延迟、吞吐量和一致性。 |
| 定价 | 基于使用量。 | 提供[基于使用量](../articles/cosmos-db/serverless.md)和[预配容量](../articles/cosmos-db/set-throughput.md)两种模式。 |
| SLA | 99.99% 可用性。 | 在公开发布时，为所有单区域帐户和具有松散一致性的所有多区域帐户提供 99.99% 的可用性 SLA，为所有多区域数据库帐户提供 99.999% 的读取可用性方面的[行业领先的综合 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/)。 |
