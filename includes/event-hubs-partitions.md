---
title: include 文件
description: include 文件
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 01/05/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 780da47e6f071d854a16ca1d1c5cd02dbdd6bef0
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2021
ms.locfileid: "97955632"
---
事件中心将事件序列组织到一个或多个分区中。 当较新的事件到达时，它们将添加到此序列的末尾。 可以将分区视为“提交日志”。

分区保存事件数据，这些数据包含事件的主体、描述事件的用户定义属性包以及元数据（例如它在分区中的偏移量、它在流序列中的编号以及它被接受时的服务端时间戳）。

![显示从旧到新的事件序列的示意图。](./media/event-hubs-partitions/partition.png)

事件中心旨在帮助处理量较大的事件，分区通过两种方式对此提供帮助：

首先，尽管事件中心是一项 PaaS 服务，但其背后存在一个物理现实，并且维护一个保持事件顺序的日志需要将这些事件一起保存在基础存储及其副本中，这将导致出现针对此类日志的吞吐量上限。 分区允许将多个并行日志用于同一个事件中心，从而使可用的原始 IO 吞吐容量倍增。

其次，你自己的应用程序必须能够及时处理要发送到事件中心的事件量。 这可能很复杂，并且需要大量的横向扩展并行处理容量。 分区的基本原理同上：单个进程处理事件的容量是有限的，因此你需要多个进程，而分区就是解决方案为这些进程提供信息，同时确保每个事件都有一个明确的处理所有者的方式。 

事件中心在配置的保留时间内保留事件，该时间适用于所有分区。 达到保持期后，事件自动被删除。 如果指定的保留期为一天，则该事件将在得到接受后的 24 小时后变为不可用。 你无法显式地删除事件。 

对于事件中心标准层，允许的保留时间最多为 7 天；对于事件中心专用层，该保留时间则为 90 天。 如果需要将事件存档超过允许的保持期，可以将它们[自动存储在 Azure 存储或 Azure Data Lake 中，方法是启用“事件中心捕获”功能](../articles/event-hubs/event-hubs-capture-overview.md)，如果需要搜索或分析此类深层存档，可以[轻松地将它们导入 Azure Synapse](../articles/event-hubs/store-captured-data-data-warehouse.md) 或其他类似存储和分析平台。 

事件中心的数据保留限制以时间为基础，其原因是为了防止大量的历史客户数据被捕获到仅由时间戳索引且仅允许顺序访问的深层存储中。 此处的体系结构理念是，历史数据需要比事件中心或 Kafka 提供的实时事件接口更丰富的索引和更直接的访问。 事件流引擎不太适合充当用于事件溯源的数据湖或长期存档。 

由于分区互相独立且包含自身的数据序列，因此通常按不同速率增大。 在事件中心，这并不像在 Apache Kafka 那样需要管理干预，但是不均匀的分布将导致下游事件处理器上的负载不均衡。

![事件中心](./media/event-hubs-partitions/multiple-partitions.png)

分区数在创建时指定，并且在事件中心标准层中必须介于 1 和 32 之间。 在事件中心专用层中，每个容量单位的分区计数最多可达 2000 个分区。 

建议在特定事件中心的应用程序峰值负载期间，至少选择你预期需要的持续[吞吐量单位 (TU)](../articles/event-hubs/event-hubs-faq.md#what-are-event-hubs-throughput-units) 数量的分区。 应该以吞吐容量为 1 TU（1 MByte 输入，2 MByte 输出）的单个分区进行计算。 你可以扩展命名空间上的 TU 或群集的容量单位，而不依赖分区计数。 当命名空间设置为 1 TU 容量时，具有 32 个分区的事件中心或具有 1 个分区的事件中心会产生完全相同的费用。 

应用程序通过以下三种方式之一来控制事件到分区的映射：

- 通过指定分区键，分区键被一致地映射（使用哈希函数）到一个可用分区。 
- 通过不指定分区键，这样可以使代理随机为给定事件选择分区。
- 通过将事件显式地发送到特定分区。

通过指定分区键，可使相关事件保持在同一分区中，并按其发送的确切顺序排列。 分区键是派生自应用程序上下文并标识事件之间的相互关系的字符串。

分区键标识的事件序列是一个流。 分区是针对许多此类流的多路复用日志存储。 

创建事件中心后，可以[增加](../articles/event-hubs/dynamically-add-partitions.md)[专用事件中心群集](../articles/event-hubs/event-hubs-dedicated-overview.md)中事件中心的分区计数，但当分区键到分区的映射发生更改时，流在分区之间的分布也会发生更改，因此如果应用程序中事件的相对顺序很重要，你应该尽力避免此类更改。

将分区数设置为允许的最大值很有吸引力，但请始终记住，事件流需要进行结构化，这样你才能真正利用多个分区。 如果需要跨所有事件或仅少数几个子流保持绝对顺序，则你可能无法利用多个分区。 而且，多个分区会使处理端更加复杂。 

虽然可以直接发送分区，但不建议这样做。 而应使用[事件发布者](../articles/event-hubs/event-hubs-features.md#event-publishers)部分介绍的更高级构造。 

如需详细了解分区以及如何在可用性和可靠性之间进行取舍，请参阅[事件中心编程指南](../articles/event-hubs/event-hubs-programming-guide.md#partition-key)和[事件中心中的可用性和一致性](../articles/event-hubs/event-hubs-availability-and-consistency.md)这两篇文章。
