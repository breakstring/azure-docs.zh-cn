---
title: 什么是 Azure HDInsight？
description: 介绍 HDInsight 以及 Apache Hadoop 和 Apache Spark 技术堆栈和组件，包括适用于大数据分析的 Kafka、Hive、Storm 和 HBase。
ms.service: hdinsight
ms.topic: overview
ms.custom: contperf-fy21q1
ms.date: 08/21/2020
ms.openlocfilehash: d1c32bf749850ac40e23c1a9cb9c5cd7755d45c6
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98939432"
---
# <a name="what-is-azure-hdinsight"></a>什么是 Azure HDInsight？

Azure HDInsight 是面向企业的云中托管、全方位的开源分析服务。 可以使用开源框架，例如 Hadoop、Apache Spark、Apache Hive、LLAP、Apache Kafka、Apache Storm、R 等等。

## <a name="what-is-hdinsight-and-the-hadoop-technology-stack"></a>什么是 HDInsight 和 Hadoop 技术堆栈？

Azure HDInsight 是 Hadoop 组件的云分发版。 可以通过 Azure HDInsight 轻松、快速且经济有效地处理大量数据。 可以使用 Hadoop、Spark、Hive、LLAP、Kafka、Storm、R 等最常用的开源框架。 可以通过这些框架启用各种各样的方案，例如提取、转换和加载 (ETL)；数据仓库操作；机器学习；IoT。

若要查看 HDInsight 上的可用 Hadoop 技术堆栈组件，请参阅[可以与 HDInsight 配合使用的组件和版本](./hdinsight-component-versioning.md)。 若要详细了解 HDInsight 中的 Hadoop，请参阅 [HDInsight 的 Azure 功能页](https://azure.microsoft.com/services/hdinsight/)。

## <a name="what-is-big-data"></a>什么是大数据？

与以前相比，大数据的收集量在增加，收集速度在加快，收集格式在增多。 大数据可以是历史数据（即已存储的数据），也可以是实时数据（即从数据源流式传输的数据）。 请参阅[使用 HDInsight 的方案](#scenarios-for-using-hdinsight)，了解大数据的最常见用例。

## <a name="why-should-i-use-azure-hdinsight"></a>为何应使用 Azure HDInsight？

本部分列出了 Azure HDInsight 的功能。

|功能  |说明  |
|---------|---------|
|云原生     |     可以使用 Azure HDInsight 在 Azure 上为 Hadoop、Spark、 [交互式查询 (LLAP)](./interactive-query/apache-interactive-query-get-started.md)、Kafka、Storm、HBase 和 ML Services 创建优化群集。 HDInsight 还在所有生产工作负荷上提供端到端 SLA。  |
|低成本且可缩放     | 可以通过 HDInsight 纵向缩放工作负荷。可以通过创建按需群集来降低成本，只为自己使用的资源付费。 还可以生成数据管道，使作业可操作化。 使计算和存储分离，提高性能和灵活性。 |
|既安全又合规    | HDInsight 允许通过 Azure 虚拟网络、加密以及与 Azure Active Directory 集成来保护企业数据资产。 HDInsight 还满足最常用的行业和政府符合性标准。        |
|监视    | Azure HDInsight 集成 Azure Monitor 日志，可以通过单个界面来监视所有群集。        |
|全球推出 | 提供 HDInsight 的区域要多于提供任何其他大数据分析产品/服务的区域。 Azure HDInsight 还在中国和德国的 Azure 政府版中提供，满足关键主权领域的企业需求。 |  
|工作效率     |  Azure HDInsight 允许将各种适用于 Hadoop 和 Spark 的高效工具与首选的开发环境配合使用。 这些开发环境包括 Visual Studio、VSCode、Eclipse 和 IntelliJ，可以提供 Scala、Python、R、Java 和 .NET 支持。 数据科学家也可以使用 Jupyter 和 Zeppelin 等常用 Notebook 进行协作。    |
|扩展性     |  可以使用脚本操作通过安装的组件（Hue、Presto 等）来扩展 HDInsight 群集，具体方法是：添加边缘节点或集成其他大数据认证应用程序。 HDInsight 允许通过单击部署方式无缝集成最常用的大数据解决方案。|

## <a name="scenarios-for-using-hdinsight"></a>使用 HDInsight 的方案

Azure HDInsight 适用于各种方案的大数据处理。 大数据可以是历史数据（已收集和存储的数据），也可以是实时数据（直接从源流式传输的数据）。 处理此类数据的方案可以汇总成以下类别：

### <a name="batch-processing-etl"></a>批处理 (ETL)

提取、转换和加载 (ETL) 是指将非结构化或结构化数据从异类数据源中提取出来， 转换成某种结构化格式，然后加载到数据存储中这一过程。 可以将转换的数据用于数据科学或数据仓库。

### <a name="data-warehousing"></a>数据仓库

可以使用 HDInsight 对任何格式的结构化或非结构化数据执行 PB 规模的交互式查询。 也可以通过生成模型将其连接到 BI 工具。

HDInsight 体系结构：数据仓库

### <a name="internet-of-things-iot"></a>物联网 (IoT)

可以使用 HDInsight 处理从各种设备实时接收的流数据。 有关详细信息，请阅读 Azure 提供的[此博客文章](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/)，了解使用 Azure 托管磁盘的 Apache Kafka on HDInsight 公共预览版。

HDInsight 体系结构：物联网

### <a name="data-science"></a>数据科学

可以使用 HDInsight 生成从数据中提取关键见解的应用程序。 也可在此基础上使用 Azure 机器学习来预测业务的未来趋势。 有关详细信息，请[阅读此客户经历](https://customers.microsoft.com/story/pros)。

HDInsight 体系结构：数据科学

### <a name="hybrid"></a>混合

可以使用 HDInsight 将现有的本地大数据基础结构扩展到 Azure，充分利用云的高级分析功能。

HDInsight 体系结构：混合

## <a name="cluster-types-in-hdinsight"></a>HDInsight 中的群集类型

HDInsight 包括特定的群集类型和群集自定义功能，例如添加组件、实用程序和语言的功能。 HDInsight 提供以下群集类型：

|群集类型 | 说明 |
|---|---|
|[Apache Hadoop](./hadoop/apache-hadoop-introduction.md)|一个框架，使用 HDFS、YARN 资源管理和简单的 MapReduce 编程模型并行处理和分析批处理数据。|
|Apache Spark[](./spark/apache-spark-overview.md)|一种开源并行处理框架，支持使用内存中处理来提升大数据分析应用程序的性能。 请参阅[什么是 HDInsight 中的 Apache Spark？](./spark/apache-spark-overview.md)|
|[Apache HBase](./hbase/apache-hbase-overview.md)|基于 Hadoop 上的 NoSQL 数据库构建，为大量非结构化和半结构化数据（可能为数十亿行乘以数百万列）提供随机访问和高度一致性。 请参阅[什么是 HDInsight 上的 HBase？](./hbase/apache-hbase-overview.md)|
|[ML Services](./r-server/r-server-overview.md)|用于托管和管理并行分布式 R 进程的服务器。 它可让数据科研人员、统计人员和 R 程序员根据需要访问 HDInsight 上可缩放的分布式分析方法。 请参阅 [HDInsight 上的 ML Services 概述](./r-server/r-server-overview.md)。|
|[Apache Storm](./storm/apache-storm-overview.md)|分布式实时计算系统，用于快速处理大型数据流。 Storm 以 HDInsight 中的托管群集形式提供。 请参阅 [使用 Storm 和 Hadoop 分析实时传感器数据](./storm/apache-storm-overview.md)。|
|[Apache 交互式查询](./interactive-query/apache-interactive-query-get-started.md)|更快的交互式 Hive 查询的内存中缓存。 请参阅[在 HDInsight 中使用交互式查询](./interactive-query/apache-interactive-query-get-started.md)。|
|[Apache Kafka](./kafka/apache-kafka-introduction.md)|一种开源平台，用于生成流式处理的数据管道和应用程序。 Kafka 还提供了消息队列功能，允许用户发布和订阅数据流。 请参阅 [Apache Kafka on HDInsight 简介](./kafka/apache-kafka-introduction.md)。|

## <a name="open-source-components-in-hdinsight"></a>HDInsight 中的开源组件

通过 Azure HDInsight，可以使用 Hadoop、Spark、Hive、LLAP、Kafka、Storm、HBase 和 R 之类的开源框架来创建群集。这些群集默认情况下附带其他开源组件，例如 Apache Ambari5、Avro5、Apache Hive3、HCatalog2、Apache Mahout2、Apache Hadoop MapReduce3、Apache Hadoop YARN2、Apache Phoenix3、Apache Pig3、Apache Sqoop3、Apache Tez3、Apache Oozie2 和 Apache ZooKeeper5。  

## <a name="programming-languages-in-hdinsight"></a>HDInsight 中的编程语言

HDInsight 群集包括 Spark、HBase、Kafka、Hadoop 和其他群集，支持多种编程语言。 某些编程语言默认情况下未安装。 对于默认情况下未安装的库、模块或程序包，请使用脚本操作来安装组件。

|编程语言  |信息  |
|---------|---------|
|默认编程语言支持     | 默认情况下，HDInsight 群集支持：<ul><li>Java</li><li>Python</li><li>.NET</li><li>Go</li></ul>  |
|Java 虚拟机 (JVM) 语言     | 除 Java 之外的许多语言都可以在 Java 虚拟机 (JVM) 上运行。 但是，运行这其中的部分语言时，可能必须在群集上安装其他组件。 HDInsight 群集支持以下基于 JVM 的语言： <ul><li>Clojure</li><li>Jython (Python for Java)</li><li>Scala</li></ul>     |
|Hadoop 特定的语言     | HDInsight 群集支持以下特定于 Hadoop 技术堆栈的语言： <ul><li>用于 Pig 作业的 Pig Latin</li><li>用于 Hive 作业的 HiveQL 和 SparkSQL</li></ul>        |

## <a name="development-tools-for-hdinsight"></a>适用于 HDInsight 的开发工具

可以使用 HDInsight 开发工具（包括 IntelliJ、Eclipse、Visual Studio Code 和 Visual Studio）通过与 Azure 的无缝集成来创作和提交 HDInsight 数据查询和作业。

* Azure Toolkit for IntelliJ10
* Azure Toolkit for Eclipse6
* 用于 VS Code 的 Azure HDInsight 工具13
* 用于 Visual Studio 的 Azure Data Lake 工具9

## <a name="business-intelligence-on-hdinsight"></a>HDInsight 上的商业智能

大家熟悉的商业智能 (BI) 工具使用 Power Query 外接程序或 Microsoft Hive ODBC 驱动程序来检索、分析和报告与 HDInsight 集成的数据：

* [使用 Azure HDInsight 上的数据可视化工具的 Apache Spark BI](./spark/apache-spark-use-bi-tools.md)

* [在 Azure HDInsight 中使用 Microsoft Power BI 直观显示 Apache Hive 数据](./hadoop/apache-hadoop-connect-hive-power-bi.md)

* [在 Azure HDInsight 中使用 Power BI 可视化交互式查询 Hive 数据](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)

* [使用 Power Query 将 Excel 连接到 Apache Hadoop](./hadoop/apache-hadoop-connect-excel-power-query.md)（需使用 Windows）

* [使用 Microsoft Hive ODBC 驱动程序将 Excel 连接到 Apache Hadoop](./hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)（需使用 Windows）


## <a name="in-region-data-residency"></a>区域内数据驻留 

Spark、Hadoop、LLAP、Storm 和 MLService 不会存储客户数据，因此，这些服务会自动满足区域内数据驻留要求，包括[信任中心](https://azuredatacentermap.azurewebsites.net/)内指定的要求。 

Kafka 和 HBase 确实会存储客户数据。 Kafka 和 HBase 会自动将此数据存储在单个区域中，因此此服务会满足区域内数据驻留要求，包括[信任中心](https://azuredatacentermap.azurewebsites.net/)内指定的要求。 


大家熟悉的商业智能 (BI) 工具使用 Power Query 加载项或 Microsoft Hive ODBC 驱动程序来检索、分析和报告与 HDInsight 集成的数据。

## <a name="next-steps"></a>后续步骤

* [在 HDInsight 中创建 Apache Hadoop 群集](./hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)
* 创建 Apache Spark 群集 - 门户
* [Azure HDInsight 中的企业安全性](./domain-joined/hdinsight-security-overview.md)
