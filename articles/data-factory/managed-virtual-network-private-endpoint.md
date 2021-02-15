---
title: 托管的虚拟网络 & 托管的专用终结点
description: 了解 Azure 数据工厂中托管的虚拟网络和托管的专用终结点。
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 07/15/2020
ms.openlocfilehash: d950b05dd34788c2c5ef0b34b8ec8ac0b20ad4b6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100379567"
---
# <a name="azure-data-factory-managed-virtual-network-preview"></a>Azure 数据工厂托管的虚拟网络 (预览) 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文将介绍 Azure 数据工厂中的托管虚拟网络和托管的专用终结点。


## <a name="managed-virtual-network"></a>托管虚拟网络

当你在 Azure 数据工厂托管的虚拟网络 (VNET) 中创建 Azure Integration Runtime (IR) 时，将使用托管虚拟网络预配集成运行时，并将利用专用终结点安全连接到支持的数据存储。 

在托管的虚拟网络中创建 Azure IR 可确保隔离和保护数据集成过程。 

使用托管虚拟网络的好处：

- 使用托管虚拟网络，可以将管理虚拟网络的负担分担到 Azure 数据工厂。 不需要为从虚拟网络最终使用多个专用 Ip 的 Azure Integration Runtime 创建一个子网，并且需要预先进行网络基础结构计划。 
- 它不需要深入的 Azure 网络知识即可安全地执行数据集成。 从安全 ETL 开始，为数据工程师提供了很多简化。 
- 托管的虚拟网络和托管的专用终结点可防止数据渗透。 

> [!IMPORTANT]
>目前，托管 VNet 仅在与 Azure 数据工厂区域相同的区域中受支持。
 

![ADF 托管虚拟网络体系结构](./media/managed-vnet/managed-vnet-architecture-diagram.png)

## <a name="managed-private-endpoints"></a>托管专用终结点

托管专用终结点是在 Azure 数据工厂托管的虚拟网络中创建的专用终结点，用于建立到 Azure 资源的专用链接。 Azure 数据工厂代表你管理这些专用终结点。 

![新托管专用终结点](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png)

Azure 数据工厂支持专用链接。 通过专用链接，你可以访问 Azure (PaaS) 服务 (如 Azure 存储、Azure Cosmos DB、Azure Synapse Analytics) 。

使用专用链接时，数据存储和托管虚拟网络之间的流量完全通过 Microsoft 骨干网络进行遍历。 专用链接可防止数据泄露风险。 可以通过创建专用终结点来建立到资源的专用链接。

专用终结点在托管的虚拟网络中使用专用 IP 地址，以有效地将服务引入其中。 专用终结点映射到 Azure 中的特定资源，而不是整个服务。 客户可以将连接限制到其组织批准的特定资源。 详细了解[专用链接和专用终结点](../private-link/index.yml)。

> [!NOTE]
> 建议你创建托管专用终结点来连接到所有 Azure 数据源。 
 
> [!WARNING]
> 如果 PaaS 数据存储 (Blob、ADLS Gen2、Azure Synapse Analytics) 已经为其创建了专用终结点，即使它允许来自所有网络的访问，ADF 也只能使用托管的专用终结点来访问它。 请确保在此类方案中创建专用终结点。 

在 Azure 数据工厂中创建托管专用终结点时，将以 "挂起" 状态创建专用终结点连接。 将启动审批工作流。 专用链接资源所有者负责批准或拒绝该连接。

![管理专用终结点](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png)

如果所有者批准该连接，则会建立专用链接。 否则，将不会建立专用链接。 在任一情况下，都会以连接状态更新托管专用终结点。

![批准托管专用终结点](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

只有处于已批准状态的托管专用终结点才能将流量发送到给定的专用链接资源。

## <a name="interactive-authoring"></a>交互式创作
交互式创作功能用于功能，如测试连接、浏览文件夹列表和表列表、获取架构和预览数据。 可以在创建或编辑处于 ADF 托管的虚拟网络中的 Azure Integration Runtime 时启用交互式创作。 后端服务将为交互式创作功能预先分配计算。 否则，每次执行任何交互式操作时都会分配计算，这将花费更多时间。 交互式创作的生存时间 (TTL) 为60分钟，这意味着它将在上次交互创作操作60分钟后自动变为禁用状态。

![交互式创作](./media/managed-vnet/interactive-authoring.png)

## <a name="limitations-and-known-issues"></a>限制和已知问题
### <a name="supported-data-sources"></a>支持的数据源
以下数据源支持通过来自 ADF 托管虚拟网络的私有链接进行连接。
- Azure Blob 存储
- Azure 表存储
- Azure 文件
- Azure Data Lake Gen2
- Azure SQL Database (不包括 Azure SQL 托管实例) 
- Azure Synapse Analytics
- Azure CosmosDB SQL
- Azure Key Vault
- Azure 专用链接服务
- Azure 搜索
- Azure Database for MySQL
- Azure Database for PostgreSQL
- Azure Database for MariaDB

### <a name="azure-data-factory-managed-virtual-network-is-available-in-the-following-azure-regions"></a>Azure 数据工厂托管的虚拟网络在以下 Azure 区域中提供：
- 美国东部
- 美国东部 2
- 美国中西部
- 美国西部
- 美国西部 2
- 美国中南部
- Central US
- 北欧
- 西欧
- 英国南部
- Southeast Asia
- 澳大利亚东部
- 澳大利亚东南部

### <a name="outbound-communications-through-public-endpoint-from-adf-managed-virtual-network"></a>从 ADF 托管虚拟网络通过公共终结点进行的出站通信
- 对于出站通信，只打开端口443。
- 不支持将 Azure 存储和 Azure Data Lake Gen2 从 ADF 托管的虚拟网络通过公共终结点进行连接。

### <a name="linked-service-creation-of-azure-key-vault"></a>Azure Key Vault 的链接服务创建 
- 为 Azure Key Vault 创建链接服务时，没有 Azure Integration Runtime 引用。 因此，在 Azure Key Vault 的链接服务创建过程中无法创建专用终结点。 但是，当你为引用 Azure Key Vault 链接服务的数据存储创建链接服务，并且此链接服务引用启用了托管虚拟网络 Azure Integration Runtime 时，你可以在创建期间为 Azure Key Vault 链接的服务创建专用终结点。 
- Azure Key Vault 的链接服务的 **测试连接** 操作仅验证 URL 格式，但不执行任何网络操作。

## <a name="next-steps"></a>后续步骤

- 教程： [使用托管的虚拟网络和专用终结点构建复制管道](tutorial-copy-data-portal-private.md) 
- 教程： [使用托管的虚拟网络和专用终结点生成映射数据流管道](tutorial-data-flow-private.md)