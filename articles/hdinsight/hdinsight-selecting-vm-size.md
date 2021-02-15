---
title: 如何为 Azure HDInsight 群集选择适当的 VM 大小
description: 了解如何为 HDInsight 群集选择适当的 VM 大小。
keywords: vm 大小, 群集大小, 群集配置
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: 51043f0a1009994528783a1b56ec5ccec68e99b3
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98931775"
---
# <a name="selecting-the-right-vm-size-for-your-azure-hdinsight-cluster"></a>为 Azure HDInsight 群集选择适当的 VM 大小

本文介绍如何为 HDInsight 群集中的各个节点选择适当的 VM 大小。 

首先，请了解虚拟机的属性（例如 CPU 处理、RAM 大小和网络延迟）如何影响工作负荷的处理。 接下来，请考虑自己的应用程序，以及它如何与不同的优化 VM 系列相搭配。 请确保你要使用的 VM 系列与你打算部署的群集类型兼容。 有关每种群集类型的所有支持和建议的 VM 大小的列表，请参阅 [Azure HDInsight 支持的节点配置](hdinsight-supported-node-configuration.md)。 最后，可以使用基准测试过程来测试某些示例工作负荷，并检查该系列中的哪个 SKU 适合自己。

有关群集其他方面（例如选择存储类型或群集大小）的规划详细信息，请参阅 [HDInsight 群集的容量规划](hdinsight-capacity-planning.md)。

## <a name="vm-properties-and-big-data-workloads"></a>VM 属性和大数据工作负荷

VM 大小和类型由 CPU 处理能力、RAM 大小和网络延迟决定：

- CPU：VM 大小支配核心数。 核心越多，每个节点可实现的并行计算度就越大。 此外，某些 VM 类型的核心更快。

- RAM：VM 大小还支配 VM 中可用的 RAM 量。 对于在内存中存储而不是从磁盘读取待处理数据的工作负荷，请确保工作节点能够提供足够的内存来容纳这些数据。

- 网络：对于大多数群集类型，群集处理的数据并不在本地磁盘上，而是在 Data Lake Storage 或 Azure 存储之类的外部存储服务中。 考虑节点 VM 与存储服务之间的网络带宽和吞吐量。 通常，更大 VM 的可用网络带宽越高。 有关详细信息，请参阅 [VM 大小概述](../virtual-machines/sizes.md)。

## <a name="understanding-vm-optimization"></a>了解 VM 优化

Azure 中的虚拟机系列经过优化，可以适应不同的用例。 在下表中，可以找到一些最常用的用例，以及与它们匹配的 VM 系列。

| 类型                     | 大小           |    说明       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [入门级](../virtual-machines/sizes-general.md)          | A、Av2  | CPU 性能和内存配置非常适合开发和测试等入门级工作负荷。 它们的性价比很高，是在 Azure 中入门的低成本选项。 |
| [常规用途](../virtual-machines/sizes-general.md)          | D、DSv2、Dv2  | CPU 与内存之比平衡。 适用于测试和开发、小到中型数据库和低到中等流量 Web 服务器。 |
| [计算优化](../virtual-machines/sizes-compute.md)        | F           | 高 CPU 与内存之比。 适用于中等流量的 Web 服务器、网络设备、批处理和应用程序服务器。        |
| [内存优化](../virtual-machines/sizes-memory.md)         | Esv3、Ev3  | 高内存与 CPU 之比。 适用于关系数据库服务器、中到大型规模的缓存和内存中分析。                 |

- 有关 HDInsight 支持的不同区域中可用 VM 实例的定价信息，请参阅 [HDInsight 定价](https://azure.microsoft.com/pricing/details/hdinsight/)。

## <a name="cost-saving-vm-types-for-light-workloads"></a>适用于轻量工作负荷的成本节省型 VM 类型

如果你的处理要求不高，[F 系列](https://azure.microsoft.com/blog/f-series-vm-size/)可能是在 HDInsight 中入门的一个不错选项。 根据每个 vCPU 的 Azure 计算单位 (ACU)，F 系列以较低的小时定价，在 Azure 产品组合中具有最高性价比。

下表描述了可以使用 Fsv2 系列 VM 创建的群集类型和节点类型。

| 群集类型 | Version | 工作器节点 | 头节点 | ZooKeeper 节点 |
|---|---|---|---|---|
| Spark | 全部 | F4 和更大 | 否 | 否 |
| Hadoop | 全部 | F4 和更大 | 否 | 否 |
| Kafka | 全部 | F4 和更大 | 否 | 否 |
| HBase | 全部 | F4 和更大 | 否 | 否 |
| LLAP | disabled | 否 | 否 | 否 |
| Storm | disabled | 否 | 否 | 否 |
| ML 服务 | 仅限 HDI 3。6 | F4 和更大 | 否 | 否 |

若要查看每个 F 系列 SKU 的规格，请参阅 [F 系列 VM 大小](https://azure.microsoft.com/blog/f-series-vm-size/)。

## <a name="benchmarking"></a>基准测试

基准测试是在不同的 VM 上运行模拟工作负荷，以测量它们在生产工作负荷中的表现的过程。 

有关 VM SKU 和群集大小基准测试的详细信息，请参阅 [Azure HDInsight 中的群集容量规划](hdinsight-capacity-planning.md#choose-the-vm-size-and-type)。

## <a name="next-steps"></a>后续步骤

- [Azure HDInsight 支持的节点配置](hdinsight-supported-node-configuration.md)
- [Azure 中 Linux 虚拟机的大小](../virtual-machines/sizes.md)