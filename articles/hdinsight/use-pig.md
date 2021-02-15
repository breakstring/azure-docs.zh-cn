---
title: 使用 Apache Pig
titleSuffix: Azure HDInsight
description: 了解如何将 Pig 与 Apache Hadoop on HDInsight 配合使用。
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 01/28/2020
ms.openlocfilehash: 7b74a41f7d6b636dddce0388d5ee0e0a12658d52
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944616"
---
# <a name="use-apache-pig-with-apache-hadoop-on-hdinsight"></a>将 Apache Pig 与 Apache Hadoop on HDInsight 配合使用

了解如何将 [Apache Pig](https://pig.apache.org/) 与 HDInsight 配合使用。

Apache Pig 是一个平台，用于使用名为 *Pig Latin* 的过程语言为 Apache Hadoop 创建程序。 Pig 可以替代 Java 来创建 *MapReduce* 解决方案，它已包括在 Azure HDInsight 中。 使用下表可以找出将 Pig 与 HDInsight 配合使用的各种方法：

## <a name="why-use-apache-pig"></a><a id="why"></a>为什么使用 Apache Pig

在 Hadoop 中使用 MapReduce 来处理数据时，其中一项难题是仅使用映射和化简函数来实现处理逻辑。 进行复杂的处理时，通常必须将处理分解成多个 MapReduce 操作，这些操作合在一起即可获得所需的结果。

使用 Pig，可以将处理定义成一系列转换，相关数据经过这些转换即可生成所需的输出。

通过 Pig Latin 语言，可以描述从原始输入开始，经过一个或多个转换，最终生成所需输出的数据流。 Pig Latin 程序遵循下述常规模式：

* **加载**：从文件系统中读取要操作的数据。

* **转换**：操作该数据。

* **转储或存储**：将数据输出到屏幕或将其存储后再进行处理。

### <a name="user-defined-functions"></a>用户定义的函数

Pig Latin 还支持使用用户定义函数 (UDF) 来调用外部组件，以便实现难以在 Pig Latin 中建模的逻辑。

有关 Pig Latin 的详细信息，请参阅 [Pig Latin Reference Manual 1](https://archive.cloudera.com/cdh/3/pig/piglatin_ref1.html)（Pig Latin 参考手册 1）和 [Pig Latin Reference Manual 2](https://archive.cloudera.com/cdh/3/pig/piglatin_ref2.html)（Pig Latin 参考手册 2）。

## <a name="example-data"></a><a id="data"></a>示例数据

HDInsight 提供各种示例数据集，它们存储在 `/example/data` 和 `/HdiSamples` 目录中。 这些目录位于群集的默认存储中。 本文档中的 Pig 示例使用来自 `/example/data/sample.log` 的 *log4j* 文件。

该文件中的每个日志都包含一行字段，其中包含一个 `[LOG LEVEL]` 字段，用于显示类型和严重性，例如：

```output
2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937
```

在前面的示例中，日志级别为 ERROR。

> [!NOTE]  
> 还可以使用 [Apache Log4j](https://en.wikipedia.org/wiki/Log4j) 日志记录工具来生成 log4j 文件，并将该文件上传到 Blob。 请参阅[将数据上传到 HDInsight](hdinsight-upload-data.md) 以了解相关说明。 有关如何将 Azure 存储中的 Blob 用于 HDInsight 的详细信息，请参阅[将 Azure Blob 存储与 HDInsight 配合使用](hdinsight-hadoop-use-blob-storage.md)。

## <a name="example-job"></a><a id="job"></a>示例作业

下面的 Pig Latin 作业从 HDInsight 群集的默认存储加载 `sample.log` 文件。 然后，它会执行一系列转换，对输入数据中出现的每个日志级别进行计数。 结果会写入 STDOUT。

```output
LOGS = LOAD 'wasb:///example/data/sample.log';
LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
RESULT = order FREQUENCIES by COUNT desc;
DUMP RESULT;
```

下图概要显示每个转换对数据的影响。

![转换的图形表示形式][image-hdi-pig-data-transformation]

## <a name="run-the-pig-latin-job"></a><a id="run"></a>运行 Pig Latin 作业

HDInsight 可以使用各种方法来运行 Pig Latin 作业。 使用下表来确定哪种方法最适合用户，并访问此链接进行演练。

## <a name="pig-and-sql-server-integration-services"></a>Pig 和 SQL Server Integration Services

可以使用 SQL Server Integration Services (SSIS) 来运行 Pig 作业。 Azure Feature Pack for SSIS 提供适用于 HDInsight 上的 Pig 作业的以下组件。

* [Azure HDInsight Pig 任务][pigtask]

* [Azure 订阅连接管理器][connectionmanager]

若要详细了解 Azure Feature Pack for SSIS，请查看[此处][ssispack]。

## <a name="next-steps"></a><a id="nextsteps"></a>后续步骤

现在，已了解如何将 Pig 与 HDInsight 配合使用，请使用以下链接来学习 Azure HDInsight 的其他用法。

* [将数据上传到 HDInsight](hdinsight-upload-data.md)
* [将 Apache Hive 和 HDInsight 配合使用](./hadoop/hdinsight-use-hive.md)
* [将 Apache Sqoop 与 HDInsight 配合使用](./hadoop/hdinsight-use-sqoop.md)
* [将 MapReduce 作业与 HDInsight 配合使用](./hadoop/hdinsight-use-mapreduce.md)

[apachepig-home]: https://pig.apache.org/
[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: https://curl.haxx.se/
[pigtask]: /sql/integration-services/control-flow/azure-hdinsight-pig-task?viewFallbackFrom=sql-server-2014
[connectionmanager]: /sql/integration-services/connection-manager/azure-subscription-connection-manager?viewFallbackFrom=sql-server-2014
[ssispack]: /sql/integration-services/azure-feature-pack-for-integration-services-ssis?viewFallbackFrom=sql-server-2014
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]:../hdinsight-use-hive.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: /powershell/azure/

[powershell-start]: https://technet.microsoft.com/library/hh847889.aspx


[image-hdi-pig-data-transformation]: ./media/use-pig/hdi-data-transformation.gif