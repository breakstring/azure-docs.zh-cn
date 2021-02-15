---
title: 使用 Scala 的 PySpark 分析 HDInsight Spark - Team Data Science Process
description: 团队数据科学过程的示例，指导在 Azure HDInsight Spark 上使用 PySpark 和 Scala。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 1662af6cd3499fdf851d4e1bd8a0db48da7635b4
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2020
ms.locfileid: "93320127"
---
# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>在 Azure 上使用 PySpark 和 Scala 进行 HDInsight Spark 数据科学演练

这些演练在 Azure Spark 群集上使用 PySpark 和 Scala 执行预测分析。 它们遵循 Team Data Science Process 中所述的步骤。 有关 Team Data Science Process 的概述，请参阅[数据科学过程](overview.md)。 有关 Spark on HDInsight 的概述，请参阅 [Spark on HDInsight 简介](../../hdinsight/spark/apache-spark-overview.md)。

其他执行 Team Data Science Process 的数据科学演练按所使用的 **平台** 分组。 有关这些示例的明细，请参阅[执行 Team Data Science Process 的演练](walkthroughs.md)。

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>在 Azure Spark 上使用 PySpark 预测出租车小费

使用纽约出租车数据，在 [Azure HDInsight 上使用 Spark](spark-overview.md) 演练可预测是否支付了刀尖和预期量的范围。 此示例使用 [Azure HDInsight Spark 群集](https://azure.microsoft.com/services/hdinsight/) 的方案中的团队数据科学过程，通过公开提供的 NYC 出租车行程和费用数据集来存储、浏览和查看工程数据。 本概述主题使用 HDInsight Spark 群集和 Jupyter PySpark 笔记本。 这些 Notebook 演示如何浏览数据以及如何创建和使用模型。 高级数据浏览和建模笔记本介绍了如何包括交叉验证、超参数扫描和模型评估。

### <a name="data-exploration-and-modeling-with-spark"></a>使用 Spark 进行数据探索和建模 
通过完成[使用 Spark MLlib 工具包为数据创建二元分类和回归模型](spark-data-exploration-modeling.md)主题，探索数据集并创建、评分和评估机器学习模型。

### <a name="model-consumption"></a>使用模型
若要了解如何评分在本主题中创建的分类和回归模型，请参阅[评分和评估 Spark 生成的机器学习模型](spark-model-consumption.md)。

### <a name="cross-validation-and-hyperparameter-sweeping"></a>交叉验证和超参数扫描
参阅[使用 Spark 进行高级数据探索和建模](spark-advanced-data-exploration-modeling.md)，了解如何使用交叉验证和超参数扫描训练模型。


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>在 Azure Spark 上使用 Scala 预测出租车小费

将 [Scala 与在 Azure 上](scala-walkthrough.md) 运行的 Spark 演练一起，可预测是否支付了提示，以及要支付的金额范围。 该演练介绍如何通过 Azure HDInsight Spark 群集上的 Spark 机器学习库 (MLlib) 和 SparkML 包，对受监督的机器学习任务使用 Scala。 它指导完成构成[数据科学过程](./index.yml)的任务：数据引入和浏览、可视化、特征工程、建模和模型使用。 生成的模型包括逻辑和线性回归、随机林和梯度提升树。


## <a name="next-steps"></a>后续步骤

有关团队数据科学过程的概述，请参阅 [团队数据科学过程概述](overview.md)。

有关 Team Data Science Process 生命周期的讨论，请参阅 [Team Data Science Process 生命周期](lifecycle.md)。 此生命周期概述了执行项目时，其从开始到结束所遵循的步骤。