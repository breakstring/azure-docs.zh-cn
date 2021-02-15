---
title: 复原 & 高可用性
titleSuffix: Azure Machine Learning
description: 了解如何通过使用高可用性配置提高 Azure 机器学习资源的中断复原能力。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 09/16/2020
ms.openlocfilehash: 7a1a63893e6e2988fc5f21e84f21c74315d856b4
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2020
ms.locfileid: "93325483"
---
# <a name="increase-azure-machine-learning-resiliency"></a>提高 Azure 机器学习的复原能力



本文介绍如何通过使用高可用性配置提高 Azure 机器学习资源的复原能力。 可以配置 Azure 机器学习所依赖的 Azure 服务以实现高可用性。 本文介绍了可以配置哪些服务来实现高可用性，并列出了多个链接来提供有关如何配置这些资源的其他信息。

> [!NOTE]
> Azure 机器学习本身不提供灾难恢复选项。

## <a name="understand-azure-services-for-azure-machine-learning"></a>了解适用于 Azure 机器学习的 Azure 服务

Azure 机器学习依赖于多个 Azure 服务，并具有多个层。 其中一些服务已在（客户）订阅中预配。 你负责这些服务的高可用性配置。 其他服务在 Microsoft 订阅中创建，并由 Microsoft 管理。 

Azure 服务包括：

* **Azure 机器学习基础结构** ：适用于 Azure 机器学习工作区的 Microsoft 托管环境。

* **关联资源** ：Azure 机器学习工作区创建期间在订阅中预配的资源。 这些资源包括 Azure 存储、Azure Key Vault、Azure 容器注册表和 Application Insights。 你负责配置这些资源的高可用性设置。
  * 默认存储具有模型、训练日志数据和数据集等数据。
  * Key Vault 具有 Azure 存储、容器注册表和数据存储的凭据。
  * 容器注册表具有用于训练和推理环境的 Docker 映像。
  * Application Insights 用于监视 Azure 机器学习。

* **计算资源** ：在部署工作区之后创建的资源。 例如，可能会创建一个计算实例或计算群集来训练机器学习模型。
  * 计算实例和计算群集：Microsoft 托管模型开发环境。
  * 其他资源：可附加到 Azure 机器学习的 Microsoft 计算资源，例如 Azure Kubernetes 服务 (AKS)、Azure Databricks、Azure 容器实例和 Azure HDInsight。 你负责配置这些资源的高可用性设置。

* **其他数据存储** ：Azure 机器学习可以装载其他数据存储（例如 Azure 存储、Azure Data Lake Storage 和 Azure SQL 数据库）用于训练数据。  这些数据存储已在订阅中预配。 你负责配置它们的高可用性设置。

下表显示由 Microsoft 管理的 Azure 服务、由你管理的服务以及默认具有高可用性的服务。

| 服务 | 管理者 | 默认具有高可用性 |
| ----- | ----- | ----- |
| **Azure 机器学习基础结构** | Microsoft | |
| **关联资源** |
| Azure 存储 | 你 | |
| Key Vault | 你 | ✓ |
| 容器注册表 | 你 | |
| Application Insights | 你 | NA |
| **计算资源** |
| 计算实例 | Microsoft |  |
| 计算群集 | Microsoft |  |
| 其他计算资源，例如 AKS、 <br>Azure Databricks、容器实例、HDInsight | 你 |  |
| **其他数据存储** ，例如 Azure 存储、Azure SQL 数据库、<br> Azure Database for PostgreSQL、Azure Database for MySQL、 <br>Azure Databricks 文件系统 | 你 | |

本文的其余部分介绍如何使这些服务具有高可用性。

## <a name="associated-resources"></a>关联的资源

> [!IMPORTANT]
> Azure 机器学习不支持使用异地冗余存储 (GRS)、异地区域冗余存储 (GZRS)、读取访问异地冗余存储 (RA-GRS) 或读取访问异地区域冗余存储 (RA-GZRS) 的默认存储帐户故障转移。

请参阅以下文档，确保配置每个资源的高可用性设置：

* **Azure 存储** ：若要配置高可用性设置，请参阅 [Azure 存储冗余](../storage/common/storage-redundancy.md)。
* **Key Vault** ：Key Vault 默认提供高可用性，无需用户操作。  请参阅 [Azure 密钥保管库可用性和冗余](../key-vault/general/disaster-recovery-guidance.md)。
* **容器注册表** ：为异地复制选择高级注册表选项。 请参阅 [Azure 容器注册表中的异地复制](../container-registry/container-registry-geo-replication.md)。
* **Application Insights** ：Application Insights 不提供高可用性设置。 若要调整数据保留期和详细信息，请参阅 [Application Insights 中的数据收集、保留和存储](../azure-monitor/app/data-retention-privacy.md#how-long-is-the-data-kept)。

## <a name="compute-resources"></a>计算资源

请参阅以下文档，确保配置每个资源的高可用性设置：

* **Azure Kubernetes 服务** ：请参阅 [Azure Kubernetes 服务 (AKS) 中实现业务连续性和灾难恢复的最佳做法](../aks/operator-best-practices-multi-region.md)和 [创建使用可用性区域的 Azure Kubernetes 服务 (AKS) 群集](../aks/availability-zones.md)。 如果 AKS 群集是使用 Azure 机器学习工作室、SDK 或 CLI 创建的，则不支持跨区域高可用性。
* **Azure Databricks** ：请参阅 [Azure Databricks 群集的区域性灾难恢复](/azure/databricks/scenarios/howto-regional-disaster-recovery)。
* **容器实例** ：业务流程协调程序负责故障转移。 请参阅 [Azure 容器实例和容器业务流程协调程序](../container-instances/container-instances-orchestrator-relationship.md)。
* **HDInsight** ：请参阅 [Azure HDInsight 支持的高可用性服务](../hdinsight/hdinsight-high-availability-components.md)。

## <a name="additional-data-stores"></a>其他数据存储

请参阅以下文档，确保配置每个资源的高可用性设置：

* **Azure Blob 容器/Azure 文件存储/Azure Data Lake Gen2** ：与默认存储相同。
* **Data Lake Storage Gen1** ：请参阅 [Data Lake Storage Gen1 的高可用性和灾难恢复指南](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)。
* **SQL 数据库** ：请参阅 [Azure SQL 数据库和 SQL 托管实例的高可用性](../azure-sql/database/high-availability-sla.md)。
* **Azure Database for PostgreSQL** ：请参阅 [Azure Database for PostgreSQL - 单一服务器中的高可用性概念](../postgresql/concepts-high-availability.md)。
* **Azure Database for MySQL** ：请参阅 [了解 Azure Database for MySQL 中的业务连续性](../mysql/concepts-business-continuity.md)。
* **Azure Databricks 文件系统** ：请参阅 [Azure Databricks 群集的区域灾难恢复](/azure/databricks/scenarios/howto-regional-disaster-recovery)。

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

如果你提供自己的客户管理的密钥来部署 Azure 机器学习工作区，则还会在订阅中预配 Azure Cosmos DB。 在这种情况下，你应负责配置其高可用性设置。 请参阅[使用 Azure Cosmos DB 实现高可用性](../cosmos-db/high-availability.md)。

## <a name="next-steps"></a>后续步骤

若要使用关联资源和高可用性设置部署 Azure 机器学习，请使用 [Azure 资源管理器模板](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced)。