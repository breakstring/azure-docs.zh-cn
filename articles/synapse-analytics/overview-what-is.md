---
title: 什么是 Azure Synapse Analytics？
description: Azure Synapse Analytics 概述
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 10/28/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 7882fc6a6823a93d09c04a8ae2c19005df4843d8
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98917087"
---
# <a name="what-is-azure-synapse-analytics"></a>什么是 Azure Synapse Analytics？

企业分析需要大规模地处理任何类型的数据（无论是原始数据、提炼的数据还是特选程度很高的数据）。 这通常要求企业将大数据和数据仓库技术结合到可以对关系存储区和数据湖中的数据进行处理的复杂数据管道中。 很难构建、维护和保护这类解决方案。 这种复杂性会延迟企业所需见解的交付。

Azure Synapse 是一项集成式分析服务，可以缩短在数据仓库和大数据系统中进行见解提取所需的时间。 Azure Synapse 汇集了企业数据仓库中所用 SQL 技术的精华、用于大数据的 Spark 技术、用于数据集成和 ETL/ELT 的 Pipelines，以及与其他 Azure 服务（Power BI、CosmosDB 和 AzureML）的深度集成。     。

## <a name="key-features--benefits"></a>重要功能和优势

### <a name="industry-leading-sql"></a>行业领先的 SQL

* Synapse SQL 是 T-SQL 的分布式查询系统，它实现了数据仓库和数据虚拟化方案，还扩展了 T-SQL 来应对流式处理和计算机学习方案。
* Synapse SQL 提供无服务器和专用资源模型，并根据需求提供消耗和计费选项。  若要使性能和成本可预测，可以创建专用 SQL 池，以保留对 SQL 表中存储的数据进行处理的能力。 对于计划外或突发性的工作负荷，请使用始终可用的无服务器 SQL 终结点。
* 使用内置的流式处理功能可将数据从云数据源载入到 SQL 表中
* 通过使用机器学习模型并使用 [T-SQL PREDICT 函数](/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest&preserve-view=true)对数据进行评分，将 AI 与 SQL 集成

### <a name="industry-standard-apache-spark"></a>行业标准 Apache Spark

Apache Spark for Azure Synapse 深入无缝地集成了 Apache Spark - 用于数据准备、数据工程、ETL 和机器学习的最流行的开源大数据引擎。

* Apache Spark 2.4 的集成了 SparkML 算法和 AzureML 的 ML 模型原生支持 Linux Foundation Delta Lake。
* 简化的资源模型使你无需担心群集的管理。
* Spark 可以快速启动并主动自动缩放。
* 原生支持 .NET for Spark，使你能够在 Spark 应用程序中重复利用自己的 C# 专业知识和现有的 .Net 代码。

### <a name="interop-of-sql-and-apache-spark-on-your-data-lake"></a>SQL 和 Apache Spark 在 Data Lake 上的互操作

Azure Synapse 消除了结合使用 SQL 和 Spark 时存在的传统技术阻碍。 你可以根据需求和专业知识无缝混搭使用这两种技术。

* 与 Hive 兼容的共享元数据系统使得 Spark 或 Hive 可以无缝使用基于 Data Lake 中的文件定义的表。
* SQL 和 Spark 可以直接浏览和分析 Data Lake 中存储的 Parquet、CSV、TSV 和 JSON 文件。
* 在 SQL 与 Spark 数据库之间以可缩放的方式快速加载和卸载数据

### <a name="built-in-data-integration-via-pipelines"></a>通过管道内置的数据集成

Azure Synapse 内置了与 Azure 数据工厂相同的数据集成引擎和体验，使你可以创建丰富的大规模 ETL 管道，而无需离开 Azure Synapse Analytics。

* 从 90 多个数据源引入数据
* 使用数据流活动的无代码 ETL
* 协调 Notebooks、Spark 作业、存储过程、SQL 脚本等

### <a name="unified-management-monitoring-and-security"></a>统一的管理、监视和安全性

Azure Synapse 为企业提供单一方式来管理分析资源、监视使用情况和活动，以及强制实施安全措施。

* 将用户分配到角色可以简化对分析资源的访问
* 对数据和代码进行精细的访问控制
* 可在单个仪表板中监视 SQL 与 Spark 中的资源、使用情况和用户

### <a name="synapse-studio"></a>Synapse Studio

Synapse Studio 是一个 Web 原生的体验，它将数据工程师所需的全部信息整合在一起，使他们能够在一个位置执行生成完整解决方案所需完成的每项任务。

* 在一个位置生成端到端的分析解决方案：引入、浏览、准备、协调、可视化
* 让编写 SQL 或 Spark 代码的数据工程师保持行业领先的工作效率：创作、调试和性能优化
* 与企业 CI/CD 过程集成

## <a name="engage-with-the-synapse-engineering-team"></a>与 Synapse 工程团队合作

- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-synapse)：询问开发问题。
- [Microsoft 问答页](/answers/topics/azure-synapse-analytics.html)：询问技术问题。

## <a name="next-steps"></a>后续步骤

* [Azure Synapse Analytics 入门](get-started.md)
* [创建工作区](quickstart-create-workspace.md)
* [使用无服务器 SQL 池](quickstart-sql-on-demand.md)
