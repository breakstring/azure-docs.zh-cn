---
title: 常见问题解答 - Azure Synapse Analytics
description: Azure Synapse Analytics 常见问题解答
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 10/25/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: a7ee4e205851a751f7a50ac0ddadfb4e4c7eb81a
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2021
ms.locfileid: "98247397"
---
# <a name="azure-synapse-analytics-frequently-asked-questions"></a>Azure Synapse Analytics 常见问题解答

本指南解答有关 Azure Synapse Analytics 的最常见问题。

## <a name="general"></a>常规

### <a name="q-how-can-i-use-rbac-roles-to-secure-my-workspace"></a>问：如何使用 RBAC 角色来保护我的工作区？

答：Azure Synapse 引入了很多角色及用于分配角色的范围，可以简化保护工作区这一任务。

Synapse RBAC 角色：
* Synapse 管理员
* Synapse SQL 管理员
* Synapse Spark 管理员
* Synapse 参与者（预览）
* Synapse 项目发布者（预览）
* Synapse 项目用户（预览）
* Synapse 计算操作员（预览）
* Synapse 凭据用户（预览）

若要保护 Synapse 工作区，请将 RBAC 角色分配给这些 RBAC 范围：
* 工作区
* Spark 池
* 集成运行时
* 链接服务
* 凭据

此外，对于专用 SQL 池，你可以使用你熟悉和喜爱的所有安全功能。

### <a name="q-how-do-i-control-cont-dedicated-sql-pools-serverless-sql-pools-and-serverless-spark-pools"></a>问：如何控制专用 SQL 池、无服务器 SQL 池和无服务器 Spark 池？

答：可从将 Azure Synapse 与 Azure 订阅级别提供的内置成本分析和成本警报结合开始使用。

- 专用 SQL 池 - 可以直接查看成本并控制成本，因为将由你创建专用 SQL 池并指定其大小。 可以通过 Azure RBAC 角色进一步控制哪些用户可以创建或缩放专用 SQL 池。

- 无服务器 SQL 池 - 具有监视和成本管理控件，可通过它们在每天、每周和每月级别限制支出。 有关详细信息，[请参阅无服务器 SQL 池的成本管理](./sql/data-processed.md)。 

- 无服务器 Spark 池 - 可以通过 Synapse RBAC 角色限制谁能创建 Spark 池。  

### <a name="q-will-synapse-workspace-support-folder-organization-of-objects-and-granularity-at-ga"></a>问：正式发布时，Synapse 工作区是否会支持对象和粒度的文件夹组织？

答：Synapse 工作区支持用户定义的文件夹。

### <a name="q-can-i-link-more-than-one-power-bi-workspace-to-a-single-azure-synapse-workspace"></a>问：能否将多个 Power BI 工作区链接到单个 Azure Synapse 工作区？
    
答：目前，只能将一个 Power BI 工作区链接到一个 Azure Synapse 工作区。 

### <a name="q-is-synapse-link-to-cosmos-db-ga"></a>问：针对 Cosmos DB 的 Synapse Link 正式发布了吗？

答：适用于 Apache Spark 的 Synapse Link 已正式发布。 针对无服务器 SQL 池的 Synapse Link 提供公共预览版。

### <a name="q-does-azure-synapse-workspace-support-cicd"></a>问：Azure Synapse 工作区是否支持 CI/CD？ 

答：是的！ 所有管道工件、笔记本、SQL 脚本和 Spark 作业定义都将驻留在 Git 中。 所有池定义都将作为 ARM 模板存储在 Git 中。 专用 SQL 池对象（架构、表、视图等）将通过具有 CI/CD 支持的数据库项目进行管理。

## <a name="pipelines"></a>管道

### <a name="q-how-do-i-ensure-i-know-what-credential-is-being-used-to-run-a-pipeline"></a>问：如何确保我了解使用了哪些凭据来运行管道？ 

答：使用在链接服务中指定的凭据来执行 Synapse 管道中的每个活动。

### <a name="q-are-ssis-irs-supported-in-synapse-integrate"></a>问：Synapse 集成是否支持 SSIS IR？

答：暂时没有。 

### <a name="q-how-do-i-migrate-existing-pipelines-from-azure-data-factory-to-an-azure-synapse-workspace"></a>问：如何将现有管道从 Azure 数据工厂迁移到 Azure Synapse 工作区？

答：此时，必须通过从原始管道导出 JSON 并将其导入 Synapse 工作区来手动重新创建 Azure 数据工厂管道和相关项目。

## <a name="apache-spark"></a>Apache Spark

### <a name="q-what-is-the-difference-between-apache-spark-for-synapse-and-apache-spark"></a>问：Apache Spark for Synapse 和 Apache Spark 之间有什么区别？

答：Apache Spark for Synapse 就是 Apache Spark，只是增加了对与（AAD、AzureML 等）其他服务集成的支持，还有更多的库（mssparktuils、Hummingbird）和预优化的性能配置。

目前在 Apache Spark 上运行的任何工作负载都可按原样在 Apache Spark for Azure Synapse 上运行。 

### <a name="q-what-versions-of-spark-are-available"></a>问：Spark 有哪些版本可用？

答：Azure Synapse Apache Spark 完全支持 Spark 2.4。 有关核心组件和当前支持的版本的完整列表，请参阅 [Apache Spark 版本支持](./spark/apache-spark-version-support.md)。

### <a name="q-is-there-an-equivalent-of-dbutils-in-azure-synapse-spark"></a>问：Azure Synapse Spark 中是否有 DButils 的等效内容？

答：是的，Azure Synapse Apache Spark 提供 mssparkutils 库。 有关该实用工具的完整文档，请参阅 [Microsoft Spark 实用工具简介](./spark/microsoft-spark-utilities.md)。

### <a name="q-how-do-i-set-session-parameters-in-apache-spark"></a>问：如何在 Apache Spark 中设置会话参数？

答：若要设置会话参数，请使用 %%configure magic available。 要使参数生效，需要重启会话。 

### <a name="q-how-do-i-set-cluster-level-parameters-in-a-serverless-spark-pool"></a>问：如何在无服务器 Spark 池中设置群集级别参数？

答：若要设置群集级别参数，可以为 Spark 池提供 spark.conf 文件。 然后，此池将采用配置文件中传递的参数。 

### <a name="q-can-i-run-a-multi-user-spark-cluster-in-azure-synapse-analytics"></a>问：能否在 Azure Synapse Analytics 中运行多用户 Spark 群集？
 
答：Azure Synapse 为特定用例提供专门构建的引擎。 Apache Spark for Synapse 是作为作业服务（而不是群集模型）设计。 有两种情况需要多用户群集模型。

**场景 1：许多用户出于 BI 目的访问用于提供数据的群集。**

完成此任务的最简单方法是，使用 Spark 调整数据，然后利用 Synapse SQL 提供的服务功能，将 Power BI 连接到这些数据集。

**场景 2：让多个开发人员共用一个群集以节约资金。**
 
为满足此场景，应为每个开发人员提供一个无服务器 Spark 池，并将其设置为使用少量 Spark 资源。 由于无服务器 Spark 池在被主动使用之前不会产生任何费用，因此有多个开发人员时可以将成本降到最低。 池共享元数据（Spark 表），因此这些开发人员可以轻松合作。

### <a name="q-how-do-i-include-manage-and-install-libraries"></a>问：如何包括、管理和安装库？

答：可以在从 synapse 工作区或 Azure 门户创建 Spark 池时安装外部包。 请参阅[在 Azure Synapse Analytics 中管理 Apache Spark 库](./spark/apache-spark-azure-portal-add-libraries.md)。

## <a name="dedicated-sql-pools"></a>专用 SQL 池

### <a name="q-what-are-the-functional-differences-between-dedicated-sql-pools-and-serverless-pools"></a>问：专用 SQL 池和无服务器池之间有什么功能差异？

答：可以在 [Synapse SQL 中的 T-SQL 功能差异](./sql/overview-features.md)中找到差异的完整列表。

### <a name="q-now-that-azure-synapse-is-ga-how-do-i-move-my-dedicated-sql-pools-that-were-previously-standalone-into-azure-synapse"></a>问：既然 Azure Synapse 已正式发布，如何将以前独立的专用 SQL 池迁移到 Azure Synapse？ 

答：不存在“移动”或“迁移”。 你可以选择在现有池上启用新的工作区功能。 如果这样做，不会有中断性变更，只是你将可以使用 Synapse Studio、Spark 和无服务器 SQL 池等新功能。

### <a name="q-what-is-the-default-deployment-of-dedicated-sql-pools-now"></a>问：专用 SQL 池现在的默认部署是什么？ 

答：默认情况下，所有新的专用 SQL 池都将部署到工作区；但是如果有需要，仍然可以使用独立的外形规格创建专用 SQL 池（以前称为 SQL DW）。 

## <a name="next-steps"></a>后续步骤

* [Azure Synapse Analytics 入门](get-started.md)
* [创建工作区](quickstart-create-workspace.md)
* [使用无服务器 SQL 池](quickstart-sql-on-demand.md)
