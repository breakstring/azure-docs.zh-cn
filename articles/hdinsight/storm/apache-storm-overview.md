---
title: 什么是 Apache Storm - Azure HDInsight
description: Apache Storm 用于实时处理数据流。 可以通过 Azure HDInsight 轻松地在 Azure 云中创建 Storm 群集。 使用 Visual Studio 时，可以通过 C# 创建 Storm 解决方案，然后将其部署到 HDInsight Storm 群集。
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: a2bca34f683df27c97b131d57c6c3278f7cb175e
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98928906"
---
# <a name="what-is-apache-storm-on-azure-hdinsight"></a>什么是 Azure HDInsight 上的 Apache Storm？

[Apache Storm](https://storm.apache.org/) 是一种容错的分布式开源计算系统。 若要实时处理数据流，可以将 Storm 与 [Apache Hadoop](../hadoop/apache-hadoop-introduction.md) 配合使用。 Storm 解决方案还提供有保障的数据处理功能，能够重播第一次未成功处理的数据。

## <a name="why-use-apache-storm-on-hdinsight"></a>为何使用 Apache Storm on HDInsight？

Storm on HDInsight 提供以下功能：

* __针对 Storm 运行时间的 99% 服务级别协议 (SLA)__ ：Storm on HDInsight 附带了完整的持续支持。 Storm on HDInsight 也提供 99.9% 的 SLA。 这意味着，Microsoft 保证至少 99.9% 的时间 Storm 群集都能建立外部连接。 有关详细信息，请参阅 [Azure 支持](https://azure.microsoft.com/support/options/)。 另请参阅 [HDInsight 的 SLA 信息](https://azure.microsoft.com/support/legal/sla/hdinsight/v1_0/)文档。

* 支持在 Storm 群集创建期间或者创建之后，通过对该群集运行脚本轻松进行自定义。 有关详细信息，请参阅[使用脚本操作自定义 HDInsight 群集](../hdinsight-hadoop-customize-cluster-linux.md)。

* **以多种语言创建解决方案**：可以根据所选语言（例如 Java、C#、Python）编写 Storm 组件。

    * 将 Visual Studio 与 HDInsight 集成，以便开发、管理和监视 C# 拓扑。 有关详细信息，请参阅 [Develop C# Storm topologies with the HDInsight Tools for Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)（使用用于 Visual Studio 的 HDInsight 工具开发 C# Storm 拓扑）。

    * 支持 Trident Java 接口。 可以创建支持一次性消息处理、事务性数据存储持久性和一组常见流分析操作的 Storm 拓扑。

* **动态缩放**：可以在不影响 Storm 拓扑运行的情况下添加或删除辅助角色节点。 若要利用通过缩放操作添加的新节点，请停用正在运行的拓扑，然后再将其重新激活。

* **使用多个 Azure 服务创建流式处理管道**：Storm on HDInsight 集成了其他 Azure 服务。 例如事件中心、SQL 数据库、Azure 存储和 Azure Data Lake Storage。 如需集成 Azure 服务的示例解决方案，请参阅[使用 Apache Storm on HDInsight 处理事件中心的事件](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)。

有关在实时分析解决方案中使用 Apache Storm 的公司的列表，请参阅[使用 Apache Storm 的公司](https://storm.apache.org/Powered-By.html)。

若要开始使用 Storm，请参阅[在 Azure HDInsight 中创建和监视 Apache Storm 拓扑](apache-storm-quickstart.md)。

## <a name="how-does-apache-storm-work"></a>Apache Storm 如何工作

Storm 运行的是拓扑，而不是你可能熟悉的 [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) 作业。 Storm 拓扑由多个以有向无环图 (DAG) 形式排列的组件构成。 数据在该图中的组件之间流动。 每个组件使用一个或多个数据流，并可选择性地发出一个或多个流。 下图演示了一个基本单词计数拓扑中组件之间的数据流动方式：

![Storm 拓扑中组件排列方式的示例](./media/apache-storm-overview/example-apache-storm-topology-diagram.png)

* Spout 组件将数据引入拓扑。 它们将一个或多个流发出到拓扑中。

* Bolt 组件使用 Spout 或其他 Bolt 发出的流。 Bolt 可以选择性地将流发出到拓扑中。 Bolt 还负责将数据写入 HDFS、Kafka 或 HBase 等外部服务或存储。

## <a name="reliability"></a>可靠性

Apache Storm 保证每个传入消息始终受到完全处理，即使数据分析分散在数百个节点。

Nimbus 节点提供的功能与 Apache Hadoop JobTracker 类似。 Nimbus 通过 Apache ZooKeeper 将任务分配给群集中的其他节点。 Zookeeper 节点为群集提供协调功能，并帮助 Nimbus 与工作器节点上的 Supervisor 进程进行通信。 如果处理的一个节点出现故障，Nimbus 节点会得到通知，并分配到另一个节点的任务和关联的数据。

Apache Storm 群集的默认配置是只能有一个 Nimbus 节点。 HDInsight 上的 Storm 提供两个 Nimbus 节点。 如果主节点出现故障，Storm 群集将切换到辅助节点，同时主节点将会恢复。 下图说明了 Storm on HDInsight 的任务流配置：

![nimbus、zookeeper 和 supervisor 示意图](./media/apache-storm-overview/storm-diagram-nimbus.png)

## <a name="ease-of-use"></a>易于使用

|用途 |说明 |
|---|---|
|安全外壳 (SSH) 连接|可以使用 SSH 通过 Internet 访问 Storm 群集的头节点。 可以使用 SSH 直接在群集上运行命令。 有关详细信息，请参阅 [将 SSH 与 HDInsight 配合使用](../hdinsight-hadoop-linux-use-ssh-unix.md)。|
|Web 连接|所有 HDInsight 群集都提供 Ambari Web UI。 可以使用 Ambari Web UI 在群集上轻松监视、配置和管理服务。 Storm 群集还提供 Storm UI。 可以使用 Storm UI，通过浏览器监视和管理 Storm 拓扑的运行。 有关详细信息，请参阅[使用 Apache Ambari Web UI 管理 HDInsight](../hdinsight-hadoop-manage-ambari.md) 和[使用 Apache Storm UI 进行监视和管理](apache-storm-deploy-monitor-topology-linux.md#monitor-and-manage-a-topology-using-the-storm-ui)文档。|
|Azure PowerShell 和 Azure CLI|PowerShell 和 Azure CLI 都提供命令行实用工具，可在客户端系统中使用这些工具来操作 HDInsight 和其他 Azure 服务。|
|Visual Studio 集成|针对 Visual Studio 的 Azure Data Lake 工具包含用于通过 SCP.NET Framework 创建 C# Storm 拓扑的项目模板。 Data Lake 工具还提供用于通过 Storm on HDInsight 部署、监视和管理解决方案的工具。 有关详细信息，请参阅 [Develop C# Storm topologies with the HDInsight Tools for Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)（使用用于 Visual Studio 的 HDInsight 工具开发 C# Storm 拓扑）。|

## <a name="integration-with-other-azure-services"></a>与其他 Azure 服务集成

* __Azure Data Lake Storage__：请参阅 [将 Azure Data Lake Storage 与 Apache Storm on HDInsight 配合使用](apache-storm-write-data-lake-store.md)。

* __事件中心__：有关将事件中心与 Storm 群集配合使用的示例，请参阅以下示例：

    * [使用 Apache Storm on HDInsight 从 Azure 事件中心处理事件 (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)

    * [使用 Apache Storm on HDInsight 从 Azure 事件中心处理事件 (C#)](apache-storm-develop-csharp-event-hub-topology.md)

* __SQL 数据库__、__Cosmos DB__、__事件中心__ 和 __HBase__：针对 Visual Studio 的 Data Lake 工具包含模板示例。 有关详细信息，请参阅[为 Apache Storm on HDInsight 开发 C# 拓扑](apache-storm-develop-csharp-visual-studio-topology.md)。

## <a name="apache-storm-use-cases"></a>Apache Storm 用例

以下是可能使用 HDInsight 上的 Storm 的一些常见方案：

* 物联网 (IoT)
* 欺诈检测
* 社交分析
* 提取、转换和加载 (ETL)
* 网络监视
* 搜索
* 移动应用场景

有关实际方案的信息，请参阅文档 [How companies are using Apache Storm](https://storm.apache.org/Powered-By.html)（公司如何使用 Apache Storm）。

## <a name="development"></a>开发

.NET 开发人员使用针对 Visual Studio 的 Data Lake 工具即可以 C# 语言设计和实施拓扑。 也可以创建使用 Java 和 C# 组件的混合拓扑。 有关详细信息，请参阅 [使用 Visual Studio 开发 Apache Storm on HDInsight 的 C# 拓扑](apache-storm-develop-csharp-visual-studio-topology.md)。

还可以使用所选的 IDE 开发 Java 解决方案。 有关详细信息，请参阅[开发适用于 Apache Storm on HDInsight 的 Java 拓扑](apache-storm-develop-java-topology.md)。

还可以使用 Python 开发 Storm 组件。 有关详细信息，请参阅[使用 Python on HDInsight 开发 Apache Storm 拓扑](apache-storm-develop-python-topology.md)。

## <a name="common-development-patterns"></a>常见开发模式

### <a name="guaranteed-message-processing"></a>有保证的消息处理

Apache Storm 可以提供不同级别的有保证的消息处理。 例如，基本的 Storm 应用程序可以保证至少一次处理，而 Trident 仅可以保证一次处理。 请参阅 apache.org 上的[数据处理保证](https://storm.apache.org/about/guarantees-data-processing.html)。

### <a name="ibasicbolt"></a>IBasicBolt

读取输入元组，发出零个或更多元组，并在执行方法的末尾立即确认输入元组，这种模式很常见。 Storm 提供 [IBasicBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/IBasicBolt.html) 接口来自动执行这种模式。

### <a name="joins"></a>联接

应用程序之间数据流的联接方式各不相同。 例如，可以将多个流中的每个元组联接到一个新流中，或者只联接特定窗口的元组批。 不管什么方式，都可以通过 [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-) 完成联接。 可以通过字段分组来定义将元组路由到 Bolt 的方式。

在以下 Java 实例中，fieldsGrouping 用于将来自组件“1”、“2”和“3”的元组路由至 MyJoiner bolt：

```java
builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));
```

### <a name="batches"></a>批处理

Apache Storm 提供名为“计时周期元组”的内部计时机制。 可以在拓扑中对计时周期元组的发出频率进行设置。

有关从 C# 组件使用计时周期元组的示例，请参阅 [PartialBoltCount.cs](https://github.com/hdinsight/hdinsight-storm-examples/blob/3b2c960549cac122e8874931df4801f0934fffa7/EventCountExample/EventCountTopology/src/main/java/com/microsoft/hdinsight/storm/examples/PartialCountBolt.java)。

### <a name="caches"></a>缓存

内存缓存通常用作加速处理的机制，因为它在内存中存储常用资产。 由于拓扑跨多个节点分布，而每个节点中有多个流程，因此应考虑使用 [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-)。 使用 `fieldsGrouping` 确保将元组（其中包含的字段用于缓存查找）始终路由到同一进程。 此分组功能可以避免在进程间重复缓存条目。

### <a name="stream-top-n"></a>流“top N”

当拓扑依赖于计算 top N 值时，请并行计算 top N 值。 然后将这些计算的输出合并到全局值中。 此操作可以通过 [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-) 完成，以便按字段路由来完成并行处理。 然后即可路由到 Bolt，以便通过全局方式确定 top N 值。

有关计算 top N 值的示例，请参阅 [RollingTopWords](https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/org/apache/storm/starter/RollingTopWords.java) 示例。

## <a name="logging"></a>日志记录

Storm 使用 Apache Log4j 2 来记录信息。 默认情况下，将记录大量的数据，因此很难通过信息排序。 可以让日志记录配置文件包括在 Storm 拓扑中，控制日志记录行为。

有关演示如何配置日志记录的示例拓扑，请参阅适用于 HDInsight 上的 Storm 的 [Java-based WordCount](apache-storm-develop-java-topology.md) （基于 Java 的 WordCount）示例。

## <a name="next-steps"></a>后续步骤

了解有关使用 Apache Storm on HDInsight 构建实时分析解决方案的详细信息：

* [在 Azure HDInsight 中创建和监视 Apache Storm 拓扑](apache-storm-quickstart.md)
* [HDInsight 上的 Apache Storm 的示例拓扑](apache-storm-example-topology.md)
