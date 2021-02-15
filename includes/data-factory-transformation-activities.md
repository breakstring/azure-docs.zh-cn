---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 19927fd274cbb7337248fb2ae8cf9d882612d570
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369211"
---
Azure 数据工厂支持以下转换活动，这些活动既可以单独添加到管道，也可以与其他活动关联在一起添加。

| 数据转换活动 | 计算环境 |
|:--- |:--- |
| [Hive](../articles/data-factory/v1/data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](../articles/data-factory/v1/data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](../articles/data-factory/v1/data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Hadoop 流式处理](../articles/data-factory/v1/data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Spark](../articles/data-factory/v1/data-factory-spark.md) | HDInsight [Hadoop] |
| [Azure 机器学习工作室（经典版）活动：批处理执行和更新资源](../articles/data-factory/v1/data-factory-azure-ml-batch-execution-activity.md) |Azure VM |
| [存储过程](../articles/data-factory/v1/data-factory-stored-proc-activity.md) |Azure SQL、Azure Synapse Analytics 或 SQL Server |
| [Data Lake Analytics U-SQL](../articles/data-factory/v1/data-factory-usql-activity.md) |Azure Data Lake Analytics |
| [DotNet](../articles/data-factory/v1/data-factory-use-custom-activities.md) |HDInsight [Hadoop] 或 Azure Batch |

> [!NOTE]
> 可以通过 MapReduce 活动在 HDInsight Spark 群集上运行 Spark 程序。 有关详细信息，请参阅[从 Azure 数据工厂调用 Spark 程序](../articles/data-factory/v1/data-factory-spark.md)。
> 可以创建一项自定义活动，在安装了 R 的 HDInsight 群集上运行 R 脚本。 请参阅 [Run R Script using Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)（使用 Azure 数据工厂运行 R 脚本）。
> 
> 

