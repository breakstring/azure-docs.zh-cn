---
title: 什么是 Azure 专用链接？
description: Azure 专用链接功能、体系结构和实施方案的概述。 了解 Azure 专用终结点和 Azure 专用链接服务的工作原理及其用法。
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: overview
ms.date: 01/28/2021
ms.author: allensu
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: 681e4efc8a9edda896249fdc939d19b4f644f246
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986473"
---
# <a name="what-is-azure-private-link"></a>什么是 Azure 专用链接？ 
使用 Azure 专用链接，可以通过虚拟网络中的[专用终结点](private-endpoint-overview.md)访问 Azure PaaS 服务（例如，Azure 存储和 SQL 数据库）和 Azure 托管的客户拥有的服务/合作伙伴服务。

虚拟网络与服务之间的流量将遍历 Microsoft 主干网络。 不再需要向公共 Internet 公开服务。 可以在虚拟网络中创建自己的[专用链接服务](private-link-service-overview.md)，并将其交付给客户。 使用 Azure 专用链接的设置和使用体验在 Azure PaaS、客户自有服务和共享合作伙伴服务中是一致的。

> [!IMPORTANT]
> Azure 专用链接现已推出正式版。 专用终结点和专用链接服务（标准负载均衡器后面的服务）都已推出正式版。 不同的 Azure PaaS 会按不同计划加入 Azure 专用链接。 请查看本文中的[可用性](#availability)部分，了解专用链接上 Azure PaaS 的准确状态。 有关已知的限制，请参阅[专用终结点](private-endpoint-overview.md#limitations)和[专用链接服务](private-link-service-overview.md#limitations)。 

:::image type="content" source="./media/private-link-overview/private-link-center.png" alt-text="Azure 门户中的 Azure 专用链接中心" border="false":::

## <a name="key-benefits"></a>主要优点
Azure 专用链接提供以下优势：  
- **以私密方式访问 Azure 平台上的服务**：无需在源或目标上使用公共 IP 地址，即可将虚拟网络连接到 Azure 中的服务。 服务提供商可在自己的虚拟网络中呈现其服务，而使用者可在其本地虚拟网络中访问这些服务。 专用链接平台将通过 Azure 主干网络处理使用者与服务之间的连接。 
 
- **本地网络和对等互连的网络**：使用专用终结点通过 ExpressRoute 专用对等互连、VPN 隧道和对等互连的虚拟网络从本地访问 Azure 中运行的服务。 无需配置 ExpressRoute Microsoft 对等互连或遍历 Internet 即可访问服务。 专用链接可让客户安全地将工作负荷迁移到 Azure。
 
- **防范数据泄露**：专用终结点映射到 PaaS 资源的某个实例，而不是映射到整个服务。 使用者只能连接到特定的资源。 对服务中任何其他资源的访问将遭到阻止。 此机制可以防范数据泄露风险。 
 
- **全球覆盖**：以私密方式连接到在其他区域中运行的服务。 使用者的虚拟网络可以位于区域 A，而且可以连接到区域 B 中专用链接后面的服务。  
 
- **扩展到自己的服务**：实现相同的体验和功能，以私密方式将服务呈现给 Azure 中的使用者。 将服务放在标准 Azure 负载均衡器的后面即可为其启用专用链接。 然后，使用者可以使用其自己的虚拟网络中的专用终结点直接连接到你的服务。 可以使用审批调用流来管理这些连接请求。 Azure 专用链接适用于属于不同 Azure Active Directory 租户的使用者和服务。 

## <a name="availability"></a>可用性 
 下表列出了专用链接服务及其适用的区域。 

|支持的服务  |可用区域 | 其他注意事项 | 状态  |
|:-------------------|:-----------------|:----------------|:--------|
|标准 Azure 负载均衡器后面的专用链接服务 | 所有公共区域<br/> 所有政府区域<br/>所有中国区域  | 在标准负载均衡器上受支持 | GA <br/> [了解如何创建专用链接服务。](create-private-link-service-portal.md) |
| Azure Blob 存储（包括 Data Lake Storage Gen2）       |  所有公共区域<br/> 所有政府区域       |  在帐户类型常规用途 V2 上受支持 | GA <br/> [了解如何为 Blob 存储创建专用终结点。](tutorial-private-endpoint-storage-portal.md)  |
| Azure 文件 | 所有公共区域<br/> 所有政府区域      | |   GA <br/> [了解如何创建 Azure 文件存储网络终结点。](../storage/files/storage-files-networking-endpoints.md)   |
| Azure 文件同步 | 所有公共区域      | |   GA <br/> [了解如何创建 Azure 文件存储网络终结点。](../storage/files/storage-sync-files-networking-endpoints.md)   |
| Azure 队列存储       |  所有公共区域<br/> 所有政府区域       |  在帐户类型常规用途 V2 上受支持 | GA <br/> [了解如何为队列存储创建专用终结点。](tutorial-private-endpoint-storage-portal.md) |
| Azure 表存储       |  所有公共区域<br/> 所有政府区域       |  在帐户类型常规用途 V2 上受支持 | GA <br/> [了解如何为表存储创建专用终结点。](tutorial-private-endpoint-storage-portal.md)  |
|  Azure SQL Database         | 所有公共区域 <br/> 所有政府区域<br/>所有中国区域      |  支持代理[连接策略](../azure-sql/database/connectivity-architecture.md#connection-policy) | GA <br/> [了解如何为 Azure SQL 创建专用终结点](create-private-endpoint-portal.md)      |
|Azure Synapse Analytics| 所有公共区域 <br/> 所有政府区域 |  支持代理[连接策略](../azure-sql/database/connectivity-architecture.md#connection-policy) |GA <br/> [了解如何为 Azure Synapse Analytics 创建专用终结点。](../azure-sql/database/private-endpoint-overview.md)|
|Azure Cosmos DB|  所有公共区域<br/> 所有政府区域</br> 所有中国区域 | |GA <br/> [了解如何为 Cosmos DB 创建专用终结点。](./tutorial-private-endpoint-cosmosdb-portal.md)|
|  Azure Database for PostgreSQL - 单一服务器         | 所有公共区域 <br/> 所有政府区域<br/>所有中国区域     | 在常规用途和内存优化定价层中受支持 | GA <br/> [了解如何为 Azure Database for PostgreSQL 创建专用终结点。](../postgresql/concepts-data-access-and-security-private-link.md)      |
|  Azure Database for MySQL         | 所有公共区域<br/> 所有政府区域<br/>所有中国区域      |  | GA <br/> [了解如何为 Azure Database for MySQL 创建专用终结点。](../mysql/concepts-data-access-security-private-link.md)     |
|  Azure Database for MariaDB         | 所有公共区域<br/> 所有政府区域<br/>所有中国区域     |  | GA <br/> [了解如何为 Azure Database for MariaDB 创建专用终结点。](../mariadb/concepts-data-access-security-private-link.md)      |
|  Azure 数字孪生         | Azure 数字孪生支持的所有公共区域     |  | 预览 <br/> [了解如何为 Azure 数字孪生创建专用终结点。](../digital-twins/how-to-enable-private-link.md)      |
|  Azure Key Vault         | 所有公共区域<br/> 所有政府区域      |  | GA   <br/> [了解如何为 Azure Key Vault 创建专用终结点。](../key-vault/general/private-link-service.md)   |
|Azure Kubernetes 服务 - Kubernetes API | 所有公共区域      |  | GA   <br/> [了解如何为 Azure Kubernetes 服务创建专用终结点。](../aks/private-clusters.md)   |
|Azure 搜索 | 所有公共区域 <br/> 所有政府区域 | 在隐私模式下的服务中受支持 | GA   <br/> [了解如何为 Azure 搜索服务创建专用终结点。](../search/service-create-private-endpoint.md)    |
|Azure 容器注册表 | 所有公共区域<br/> 所有政府区域    | 在容器注册表的高级层中受支持。 [针对层级进行选择](../container-registry/container-registry-skus.md)| GA   <br/> [了解如何为 Azure 容器注册表创建专用终结点。](../container-registry/container-registry-private-link.md)   |
|Azure 应用配置 | 所有公共区域      |  | 预览  </br> [了解如何为 Azure 应用程序配置服务创建专用终结点](../azure-app-configuration/concept-private-endpoint.md) |
|Azure 备份 | 所有公共区域<br/> 所有政府区域   |  | GA   <br/> [了解如何为 Azure 备份服务创建专用终结点。](../backup/private-endpoints.md)   |
|Azure 事件中心 | 所有公共区域<br/>所有政府区域      |   | GA   <br/> [了解如何为 Azure 事件中心创建专用终结点。](../event-hubs/private-link-service.md)  |
|Azure 服务总线 | 所有公共区域<br/>所有政府区域  | 在 Azure 服务总线的高级层受支持。 [针对层级进行选择](../service-bus-messaging/service-bus-premium-messaging.md) | GA   <br/> [了解如何为 Azure 服务总线创建专用终结点。](../service-bus-messaging/private-link-service.md)    |
|Azure 中继 | 所有公共区域      |  | 预览 <br/> [了解如何为 Azure 中继服务创建专用终结点。](../azure-relay/private-link-service.md)  |
|Azure 事件网格| 所有公共区域<br/> 所有政府区域       |  | GA   <br/> [了解如何为 Azure 事件网格创建专用终结点。](../event-grid/network-security.md) |
|Azure Web 应用 | 所有公共区域      | 支持 PremiumV2、PremiumV3 或 Function Premium 计划  | GA   <br/> [了解如何为 Azure Web 应用创建专用终结点。](./tutorial-private-endpoint-webapp-portal.md)   |
|Azure 机器学习 | 所有公共区域    |  | GA   <br/> [了解如何为 Azure 机器学习创建专用终结点。](../machine-learning/how-to-configure-private-link.md)   |
| Azure 自动化  | 所有公共区域<br/> 所有政府区域 |  | 预览 </br> [了解如何为 Azure 自动化服务创建专用终结点。](../automation/how-to/private-link-security.md)| |
| Azure IoT 中心 | 所有公共区域    |  | GA   <br/> [了解如何为 Azure IoT 中心创建专用终结点。](../iot-hub/virtual-network-support.md) |
| Azure SignalR | 美国东部、美国中南部、<br/>美国西部 2、所有中国区域      |  | 预览   <br/> [了解如何为 Azure SignalR 创建专用终结点。](../azure-signalr/howto-private-endpoints.md)   |
| Azure Monitor <br/>（Log Analytics 和 Application Insights） | 所有公共区域      |  | GA   <br/> [了解如何为 AAzure Monitor 创建专用终结点。](../azure-monitor/platform/private-link-security.md)   | 
| Azure Batch | 除以下区域外的所有公共区域：德国中部、德国东北部 <br/> 所有政府区域  | | GA <br/> [了解如何为 Azure Batch 创建专用终结点。](../batch/private-connectivity.md) |
|Azure 数据工厂 | 所有公共区域<br/> 所有政府区域<br/>所有中国区域    | 凭据需要存储在某个 Azure 密钥保管库中| GA   <br/> [了解如何为 Azure 数据工厂创建专用终结点。](../data-factory/data-factory-private-link.md)   |
|Azure 托管磁盘 | 所有公共区域<br/> 所有政府区域<br/>所有中国区域    | [单击此处了解已知限制](../virtual-machines/disks-enable-private-links-for-import-export-portal.md#limitations) | GA   <br/> [了解如何为 Azure 托管磁盘创建专用终结点。](../virtual-machines/disks-enable-private-links-for-import-export-portal.md)   |



有关最新通知，请查看 [Azure 专用链接更新页面](https://azure.microsoft.com/updates/?product=private-link)。

## <a name="logging-and-monitoring"></a>日志记录和监视

Azure 专用链接可与 Azure Monitor 集成。 通过这种组合可以：

 - 将日志存档到存储帐户。
 - 将事件流式传输到事件中心。
 - 启用 Azure Monitor 日志记录。

可以在 Azure Monitor 中访问以下信息： 
- **专用终结点**： 
    - 专用终结点处理的数据（传入/传出）
 
- **专用链接服务**：
    - 专用链接服务处理的数据（传入/传出）
    - NAT 端口可用性  
 
## <a name="pricing"></a>定价   
有关定价详细信息，请参阅 [Azure 专用链接定价](https://azure.microsoft.com/pricing/details/private-link/)。
 
## <a name="faqs"></a>常见问题解答  
有关常见问题解答，请参阅 [Azure 专用链接常见问题解答](private-link-faq.md)。
 
## <a name="limits"></a>限制  
有关限制，请参阅 [Azure 专用链接的限制](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits)。

## <a name="service-level-agreement"></a>服务级别协议
有关 SLA，请参阅 [Azure 专用链接的 SLA](https://azure.microsoft.com/support/legal/sla/private-link/v1_0/)。

## <a name="next-steps"></a>后续步骤

- [快速入门：使用 Azure 门户创建专用终结点](create-private-endpoint-portal.md)
- [快速入门：使用 Azure 门户创建专用链接服务](create-private-link-service-portal.md)