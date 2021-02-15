---
title: '数据转换：处理 & 转换数据 '
description: 了解如何在 Azure 数据工厂中使用 Hadoop、Azure 机器学习 Studio (经典) 或 Azure Data Lake Analytics 来转换数据或处理数据。
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: c9818bfd2a9519cd14d34ecc810179d66aa57e52
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100363978"
---
# <a name="transform-data-in-azure-data-factory-version-1"></a>转换 Azure 数据工厂版本1中的数据
> [!div class="op_single_selector"]
> * [Hive](data-factory-hive-activity.md)  
> * [Pig](data-factory-pig-activity.md)  
> * [MapReduce](data-factory-map-reduce.md)  
> * [Hadoop 流式处理](data-factory-hadoop-streaming-activity.md)
> * [Azure 机器学习工作室（经典）](data-factory-azure-ml-batch-execution-activity.md) 
> * [存储过程](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL](data-factory-usql-activity.md)
> * [.NET 自定义](data-factory-use-custom-activities.md)

## <a name="overview"></a>概述
> [!NOTE]
> 本文适用于数据工厂版本 1。 如果使用当前版本数据工厂服务，请参阅[数据工厂中的数据转换活动](../transform-data.md)。

本文介绍了 Azure 数据工厂中的数据转换活动，可利用这些活动将原始数据转换和处理为预测和见解。 转换活动在计算环境（例如 Azure HDInsight 群集或 Azure Batch）中执行。 其提供了相关文章链接，内附各转换活动的详细信息。

数据工厂支持以下数据转换活动，这些活动可单独添加到[管道](data-factory-create-pipelines.md)，还可与其他活动关联在一起。

> [!NOTE]
> 有关分步说明演练，请参阅[使用 Hive 转换创建管道](data-factory-build-your-first-pipeline.md)。  
> 
> 

## <a name="hdinsight-hive-activity"></a>HDInsight Hive 活动
数据工厂管道中的 HDInsight Hive 活动会在自己的或基于 Windows/Linux 的按需 HDInsight 群集上执行 Hive 查询。 有关此活动的详细信息，请参阅 [Hive 活动](data-factory-hive-activity.md) 一文。 

## <a name="hdinsight-pig-activity"></a>HDInsight Pig 活动
数据工厂管道中的 HDInsight Pig 活动会在自己或基于 Windows/Linux 的按需 HDInsight 群集上执行 Pig 查询。 有关此活动的详细信息，请参阅 [Pig 活动](data-factory-pig-activity.md) 一文。 

## <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce 活动
数据工厂管道中的 HDInsight MapReduce 活动会在自己或基于 Windows/Linux 的按需 HDInsight 群集上执行 MapReduce 程序。 有关此活动的详细信息，请参阅 [MapReduce 活动](data-factory-map-reduce.md) 一文。

## <a name="hdinsight-streaming-activity"></a>HDInsight Streaming 活动
数据工厂管道中的 HDInsight Streaming 活动会在自己或基于 Windows/Linux 的按需 HDInsight 群集上执行 HDInsight Streaming 程序。 有关此活动的详细信息，请参阅 [HDInsight Streaming 活动](data-factory-hadoop-streaming-activity.md)。

## <a name="hdinsight-spark-activity"></a>HDInsight Spark 活动
数据工厂管道中的 HDInsight Spark 活动在自己的 HDInsight 群集上执行 Spark 程序。 有关详细信息，请参阅[从 Azure 数据工厂调用 Spark 程序](data-factory-spark.md)。 

## <a name="azure-machine-learning-studio-classic-activities"></a>Azure 机器学习 Studio (经典) 活动
使用 Azure 数据工厂，可轻松创建使用已发布的 Azure 机器学习 Studio (经典) web 服务进行预测分析的管道。 使用 Azure 数据工厂管道中的 [批处理执行活动](data-factory-azure-ml-batch-execution-activity.md#invoking-a-web-service-using-batch-execution-activity) ，可以调用 Studio (经典) web 服务对批处理中的数据进行预测。

随着时间的推移，Studio 中的预测模型 (经典) 评分试验需要使用新的输入数据集重新训练。 完成重新训练后，需要用重新训练机器学习模型更新评分 web 服务。 您可以使用 " [更新资源" 活动](data-factory-azure-ml-batch-execution-activity.md#updating-models-using-update-resource-activity) ，使用新训练的模型更新 web 服务。  

有关这些 Studio (经典) 活动的详细信息，请参阅 [使用 Azure 机器学习 studio (经典) 活动](data-factory-azure-ml-batch-execution-activity.md) 。 

## <a name="stored-procedure-activity"></a>存储过程活动
可使用数据工厂管道中的 SQL Server 存储过程活动调用以下数据存储之一中的存储过程：你的企业或 Azure VM 中的 Azure SQL 数据库、Azure Synapse Analytics、SQL Server 数据库。 有关详细信息，请参阅 [存储过程活动](data-factory-stored-proc-activity.md) 一文。  

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL 活动
Data Lake Analytics U-SQL 活动在 Azure Data Lake Analytics 群集上运行 U-SQL 脚本。 有关详细信息，请参阅 [数据分析 U-SQL 活动](data-factory-usql-activity.md) 。 

## <a name="net-custom-activity"></a>.NET 自定义活动
如果需要采用数据工厂不支持的方式转换数据，可以使用自己的数据处理逻辑创建自定义活动，并在管道中使用该活动。 可以使用 Azure Batch 服务或 Azure HDInsight 群集配置要运行的自定义 .NET 活动。 有关详细信息，请参阅[使用自定义活动](data-factory-use-custom-activities.md)文章。 

可以创建一项自定义活动，在安装了 R 的 HDInsight 群集上运行 R 脚本。 请参阅 [Run R Script using Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)（使用 Azure 数据工厂运行 R 脚本）。 

## <a name="compute-environments"></a>计算环境
为计算环境创建链接服务，并在定义转换活动时使用该服务。 数据工厂支持两类计算环境。 

1. **按需**：在这种情况下，计算环境由数据工厂完全托管。 作业提交到进程数据前，数据工厂服务会自动创建计算环境，作业完成后则自动将其删除。 针对作业执行、群集管理和启动操作，可以配置和控制按需计算环境的粒度设置。 
2. **自带**：在这种情况下，可将自己的计算环境（例如 HDInsight 群集）注册为数据工厂中的链接服务。 计算环境由用户进行管理，数据工厂服务使用它执行活动。 

有关数据工厂支持的计算服务列表，请参阅 [计算链接服务](data-factory-compute-linked-services.md)文章。 

## <a name="summary"></a>总结
Azure 数据工厂支持以下数据转换活动和活动的计算环境。 这些活动可单独添加到管道，也可与其他活动关联在一起。

| 数据转换活动 | 计算环境 |
|:--- |:--- |
| [Hive](data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Hadoop 流式处理](data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Azure 机器学习工作室（经典版）活动：批处理执行和更新资源](data-factory-azure-ml-batch-execution-activity.md) |Azure VM |
| [存储过程](data-factory-stored-proc-activity.md) |Azure SQL、Azure Synapse Analytics 或 SQL Server |
| [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |Azure Data Lake Analytics |
| [DotNet](data-factory-use-custom-activities.md) |HDInsight [Hadoop] 或 Azure Batch |

