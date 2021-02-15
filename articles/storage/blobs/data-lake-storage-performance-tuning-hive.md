---
title: 调整性能：Hive、HDInsight 和 Azure Data Lake Storage Gen2 | Microsoft Docs
description: 了解使用 Hive、HDInsight 和 Azure Data Lake Storage Gen2 的 I/O 密集型查询的优化指南。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 4b1e5dd3c72122ade2fd4d4092bb18a7acf215f5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95912937"
---
# <a name="tune-performance-hive-hdinsight--azure-data-lake-storage-gen2"></a>调整性能：Hive、HDInsight 和 Azure Data Lake Storage Gen2

已设置默认设置，以便针对许多不同用例提供良好性能。  对于 I/O 密集型查询，可以优化 Hive 以获取更好的 Azure Data Lake Storage Gen2 性能。  

## <a name="prerequisites"></a>先决条件

* **一个 Azure 订阅**。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* **Data Lake Storage Gen2 帐户**。 有关如何创建帐户的说明，请参阅[快速入门：创建 Azure Data Lake Storage Gen2 存储帐户](../common/storage-account-create.md)
* 具有 Data Lake Storage Gen2 帐户访问权限的 Azure HDInsight 群集  。 请参阅[配合使用 Azure Data Lake Storage Gen2 和 Azure HDInsight 群集](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md)
* **在 HDInsight 上运行 Hive**。  若要了解有关在 HDInsight 上运行 Hive 作业的信息，请参阅[在 HDInsight 上使用 Hive](../../hdinsight/hadoop/hdinsight-use-hive.md)
* Data Lake Storage Gen2 的性能优化指南  。  有关一般的性能概念，请参阅 [Data Lake Storage Gen2 性能优化指南](data-lake-storage-performance-tuning-guidance.md)

## <a name="parameters"></a>parameters

下面是为提高 Data Lake Storage Gen2 性能要优化的最重要设置：

* **hive.tez.container.size** - 每个任务使用的内存量

* **tez.grouping.min-size** - 每个映射器的最小大小

* **tez.grouping.max-size** - 每个映射器的最大大小

* **hive.exec.reducer.bytes.per.reducer** - 每个化简器的大小

**hive.tez.container.size** - 容器大小确定可供每个任务使用的内存量。  这是用于控制 Hive 中的并发性的主要输入。  

**tez.grouping.min-size** - 使用此参数可以设置每个映射器的最小大小。  如果 Tez 选择的映射器数小于此参数的值，则 Tez 将使用此处设置的值。

**tez.grouping.max-size** - 使用此参数可以设置每个映射器的最大大小。  如果 Tez 选择的映射器数大于此参数的值，则 Tez 将使用此处设置的值。

**hive.exec.reducer.bytes.per.reducer** - 此参数设置每个化简器的大小。  默认情况下，每个化简器为 256 MB。  

## <a name="guidance"></a>指南

**设置 hive.exec.reducer.bytes.per.reducer** - 默认值适用于数据未压缩时。  对于已压缩的数据，应减小化简器。  

**设置 hive.tez.container.size** - 在每个节点中，内存由 yarn.nodemanager.resource.memory-mb 指定，并且默认情况下应在 HDI 群集上正确设置。  有关在 YARN 中设置相应内存的其他信息，请参阅此[文章](../../hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom.md)。

通过减小 Tez 容器可增加并行度，I/O 密集型工作负荷可以从中受益。 这样可为用户提供更多容器，从而提高并发性。  但是，某些 Hive 查询（例如 MapJoin）需要占用大量内存。  如果任务没有足够的内存，则在运行时会出现“内存不足”异常。  如果收到“内存不足”异常，则应增加内存。   

正在运行的并发任务数或平行度将受到总 YARN 内存量的限制。  YARN 容器数将决定可运行多少个并发任务。  若要查找每个节点的 YARN 内存量，可以转到 Ambari。  导航到 YARN 并查看“配置”选项卡。YARN 内存量会显示在此窗口中。  

- 总 YARN 内存 = 节点数 * 每个节点的 YARN 内存
- YARN 容器的 \# = 总 YARN 内存 / Tez 容器大小

提高使用 Data Lake Storage Gen2 的性能的关键是尽可能多地增加并发性。  Tez 会自动计算应创建的任务数，因此无需设置它。   

## <a name="example-calculation"></a>示例计算

让我们假设你有一个由 8 个节点组成的 D14 群集。  

- 总 YARN 内存 = 节点数 * 每个节点的 YARN 内存
- 总 YARN 内存 = 8 个节点 * 96GB = 768GB
- YARN 容器的 \# = 768GB / 3072MB = 256

## <a name="further-information-on-hive-tuning"></a>有关 Hive 优化的详细信息

下面是将帮助优化 Hive 查询的几个博客：
* [在 Hdinsight 中优化 Hadoop 的 Hive 查询](../../hdinsight/hdinsight-hadoop-optimize-hive-query.md)
* [优化 Azure HDInsight 中的 Apache Hive 查询](../../hdinsight/hdinsight-hadoop-optimize-hive-query.md)
* [有关优化 Hive on HDInsight 的 Ignite 讨论](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)