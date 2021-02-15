---
title: 连接模式和要求
description: 介绍从环境到 Azure 的 Azure Arc 启用的数据服务连接选项
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: d148509af45b93dce8dbd99b9afc674276b149b6
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493966"
---
# <a name="connectivity-modes-and-requirements"></a>连接模式和要求

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connectivity-modes"></a>连接模式

从启用了 Azure Arc 的数据服务环境到 Azure 的连接度有多个选项。 根据业务策略、政府法规或与 Azure 的网络连接的可用性不同，您可以从以下连接模式中进行选择。

启用 azure Arc 的数据服务可让你选择在两种不同的 *连接模式下* 连接到 Azure： 

- 直接连接 
- 间接连接

利用连接模式，你可以灵活地选择发送到 Azure 的数据量，以及用户如何与 Arc 数据控制器交互。 根据所选的连接模式，启用了 Azure Arc 的数据服务的某些功能可能会也可能不可用。

重要的是，如果启用了 Azure Arc 的数据服务直接连接到 Azure，则用户可以使用 [azure 资源管理器 api](/rest/api/resources/)、Azure CLI 和 Azure 门户来操作 azure arc 数据服务。 直接连接模式的体验非常类似于在 Azure 门户中使用任何其他 Azure 服务，其中包括预配/取消预配、缩放、配置等。  如果启用了 Azure Arc 的数据服务间接连接到 Azure，则 Azure 门户为只读视图。 你可以查看已部署的 SQL 托管实例和 Postgres 超大规模实例的清单，以及有关它们的详细信息，但无法在 Azure 门户中对其执行操作。  在间接连接模式下，必须使用 Azure Data Studio、 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 或 Kubernetes 本机工具（如 kubectl）以本地方式执行所有操作。

此外，Azure Active Directory 和 Azure Role-Based Access Control 只能在直接连接模式中使用，因为这种方式依赖于与 Azure 之间的连续直接连接来提供此功能。

某些 Azure 附加服务仅在可直接访问时才可用，例如 Azure Defender 安全服务、容器见解和 Azure 备份到 blob 存储。

||**间接连接**|**直接连接**|**从未连接**|
|---|---|---|---|
|**描述**|间接连接模式在环境中本地提供大多数管理服务，而不直接连接到 Azure。  必须将最小数量的数据发送到 Azure，才能 _仅_ 用于库存和计费目的。 此文件将导出到文件中，并且至少每月上传到 Azure。  不需要直接或连续连接到 Azure。  某些需要连接到 Azure 的功能和服务将无法使用。|直接连接模式提供了可与 Azure 建立直接连接时的所有可用服务。 连接始终 _从_ 环境启动到 Azure，并使用标准端口和协议，如 HTTPS/443。|不能以任何方式向 Azure 或从 Azure 发送数据。|
|**当前可用性**| 以预览版提供。|以预览版提供。|目前尚不支持。|
|**典型用例**|本地数据中心，它们不允许在数据中心的数据区域内或不能连接到数据中心，因为其业务或法规符合性策略或外部攻击或数据渗透的问题。  典型示例：金融机构、卫生保健、政府。 <br/><br/>边缘站点通常未连接到 Internet 的边缘站点位置。  典型示例：石油/天然气或军事现场应用。  <br/><br/>与长时间中断连接的边缘站点位置。  典型示例：体育场、巡航交付。 | 使用公有云的组织。  典型示例： Azure、AWS 或 Google Cloud。<br/><br/>通常存在和允许 Internet 连接的边缘站点位置。  典型示例：零售商店、制造。<br/><br/>公司数据中心，具有更多的策略，可用于从数据中心的数据区域连接到 Internet。  典型示例：非管控企业、中小型企业|真正的 "有气流的" 环境，在任何情况下都不能提供数据环境。 典型示例：绝密政府设施。|
|**如何将数据发送到 Azure**|有三个选项可用于向 Azure 发送计费和清单数据：<br><br> 1) 通过可连接到安全数据区域和 Azure 的自动化过程，将数据导出到数据区域。<br><br>2通过数据区域中的自动化过程将数据从数据区域导出) 数据，自动将其复制到不太安全的区域，并且不太安全的区域中的自动过程会将数据上传到 Azure。<br><br>3) 数据由安全区域内的用户手动导出，手动将其放在安全区域，并手动上传到 Azure。 <br><br>前两个选项是一个自动持续的过程，可将其安排为经常运行，因此，仅限将数据传输到 Azure 时所需的延迟仅限于 Azure 的可用连接。|数据自动并持续发送到 Azure。|数据永远不会发送到 Azure。|

## <a name="feature-availability-by-connectivity-mode"></a>按连接模式的功能可用性

|**功能**|**间接连接**|**直接连接**|
|---|---|---|
|**自动高可用性**|支持|支持|
|**自助式预配**|支持<br/>可以通过 Azure Data Studio、 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 或 Kubernetes 本机工具 (helm、kubectl、oc 等 ) 或使用启用了 Azure Arc Kubernetes GitOps 预配来完成创建。|支持<br/>除了间接连接模式创建选项以外，还可以通过 Azure 门户、Azure 资源管理器 Api、Azure CLI 或 ARM 模板进行创建。 **正在等待直接连接模式的可用性**
|**弹性的可伸缩性**|支持|支持<br/>**正在等待直接连接模式的可用性**|
|**Billing**|支持<br/>计费数据会定期导出并发送到 Azure。|支持<br/>计费数据自动并持续发送到 Azure 并以近乎实时的形式反映。 **正在等待直接连接模式的可用性**|
|**库存管理**|支持<br/>清单数据定期导出并发送到 Azure。<br/><br/>使用客户端工具（如 Azure Data Studio）、Azure 数据 CLI 或 `kubectl` 在本地查看和管理清单。|支持<br/>清单数据自动并持续发送到 Azure 并以近乎实时的形式反映。 因此，你可以直接从 Azure 门户管理清单。 **正在等待直接连接模式的可用性**|
|**自动升级和修补**|支持<br/>数据控制器必须能够直接访问 Microsoft 容器注册表 (MCR) 或者需要从 MCR 请求容器映像，并将其推送到数据控制器有权访问的本地专用容器注册表。|支持<br/>**正在等待直接连接模式的可用性**|
|**自动备份和还原**|支持<br/>自动本地备份和还原。|支持<br/>除了自动本地备份和还原外，还可以 _选择_ 将备份发送到 Azure 备份，以实现长期、异地保留。 **正在等待直接连接模式的可用性**|
|**Monitoring**|支持<br/>使用 Grafana 和 Kibana 仪表板进行本地监视。|支持<br/>除了本地监视仪表板，你还可以 _选择_ 将监视数据和日志发送到 Azure Monitor，以便在一个位置对多个站点进行大规模监视。 **正在等待直接连接模式的可用性**|
|**身份验证**|使用本地用户名/密码进行数据控制器和仪表板身份验证。 使用 SQL 和 Postgres 登录名或 Active Directory 连接到数据库实例。  使用 K8s authentication 提供程序对 Kubernetes API 进行身份验证。|除了或而不是间接连接模式的身份验证方法，还可以 _选择_ 使用 Azure Active Directory。 **正在等待直接连接模式的可用性**|
|**基于角色的访问控制 (RBAC)**|在 Kubernetes API 上使用 Kubernetes RBAC。 对数据库实例使用 SQL 和 Postgres RBAC。|你可以选择与 Azure Active Directory 和 Azure RBAC 集成。 **正在等待直接连接模式的可用性**|
|**Azure Defender**|不支持|规划未来|

## <a name="connectivity-requirements"></a>连接要求

**某些功能需要连接到 Azure。**

**与 Azure 的所有通信始终从你的环境启动。** 即使对于操作（由 Azure 门户中的用户启动）也是如此。  在这种情况下，实际上是在 Azure 中排队的任务。  你的环境中的代理启动与 Azure 的通信，以查看队列中有哪些任务，运行任务，并将状态/完成/失败报告回 Azure。

|**数据类型**|**方向**|**必需/可选**|**额外成本**|**需要模式**|**备注**|
|---|---|---|---|---|---|
|**容器映像**|Microsoft 容器注册表-> 客户|必选|否|间接或直接|容器映像是分发软件的方法。  在可通过 Internet 连接到 Microsoft 容器注册表 (MCR) 的环境中，可以直接从 MCR 拉取容器映像。  如果部署环境没有直接连接，可以从 MCR 中提取映像，并将其推送到部署环境中的专用容器注册表。  在创建时，可以将创建进程配置为从专用容器注册表而不是从 MCR 请求。 这也适用于自动更新。|
|**资源清单**|客户环境-> Azure|必选|否|间接或直接|数据控制器的清单、 (PostgreSQL 和 SQL) 的数据库实例将保留在 Azure 中以便计费，还用于在一个位置创建所有数据控制器和数据库实例的清单，如果你有多个使用 Azure Arc 数据服务的环境，这一点特别有用。  预配实例时，取消预配、扩展/缩减清单会在 Azure 中更新。|
|**计费遥测数据**|客户环境-> Azure|必选|否|间接或直接|出于计费目的，必须将数据库实例的使用情况发送到 Azure。  预览期间，启用了 Azure Arc 的数据服务不会产生费用。|
|**监视数据和日志**|客户环境-> Azure|可选|可能根据数据量 (参阅 [Azure Monitor 定价](https://azure.microsoft.com/en-us/pricing/details/monitor/)) |间接或直接|你可能想要将本地收集的监视数据和日志发送到 Azure Monitor，以便将跨多个环境的数据聚合到一个位置，还可以使用 Azure 机器学习等 Azure Monitor 服务（如警报）。|
|**Azure RBAC) 的 azure 基于角色的访问控制 (**|客户环境-> Azure > 客户环境|可选|否|仅直接|如果要使用 Azure RBAC，则必须始终使用 Azure 建立连接。  如果你不想使用 Azure RBAC，则可以使用本地 Kubernetes RBAC。  **正在等待直接连接模式的可用性**|
|**Azure Active Directory (AD)**|客户环境-> Azure > 客户环境|可选|也许，但你可能已支付 Azure AD|仅直接|如果要使用 Azure AD 进行身份验证，则必须始终使用 Azure 建立连接。 如果不想使用 Azure AD 进行身份验证，则可以通过 Active Directory Active Directory 联合身份验证服务 (ADFS) 。 **正在等待直接连接模式的可用性**|
|**备份和还原**|客户环境-> 客户环境|必选|否|直接或间接|备份和还原服务可以配置为指向本地存储类。 |
|**Azure 备份-长期保留**| 客户环境-> Azure | 可选| 适用于 Azure 存储 | 仅直接 |你可能想要将在本地执行的备份发送到 Azure 备份，以便长期保留备份，并将其恢复到本地环境以进行还原。 **正在等待直接连接模式的可用性**|
|**Azure Defender 安全服务**|客户环境-> Azure > 客户环境|可选|是|仅直接|**正在等待直接连接模式的可用性**|
|**Azure 门户中的预配和配置更改**|客户环境-> Azure > 客户环境|可选|否|仅直接|预配和配置更改可使用 Azure Data Studio 或在本地完成 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 。  在直接连接模式下，你还可以从 Azure 门户设置和进行配置更改。 **正在等待直接连接模式的可用性**|


## <a name="details-on-internet-addresses-ports-encryption-and-proxy-server-support"></a>有关 internet 地址、端口、加密和代理服务器支持的详细信息

目前，在预览阶段，只支持间接连接模式。 在此模式下，Internet 上的服务仅需要三个连接。 这些连接包括：

- [Microsoft 容器注册表 (MCR) ](#microsoft-container-registry-mcr)
- [Azure 资源管理器 API](#azure-resource-manager-apis)
- [Azure monitor Api](#azure-monitor-apis)

所有与 Azure 的 HTTPS 连接和 Microsoft 容器注册表都使用 SSL/TLS 进行加密，并使用官方签名和可验证的证书。

以下部分提供这些连接的详细信息。 

### <a name="microsoft-container-registry-mcr"></a>Microsoft 容器注册表 (MCR) 

Microsoft 容器注册表承载启用了 Azure Arc 的数据服务容器映像。  可以从 MCR 拉取这些映像，并将其推送到专用容器注册表，并配置数据控制器部署过程以从该专用容器注册表拉取容器映像。

#### <a name="connection-source"></a>连接源

每个 Kubernetes 节点上的 Kubernetes kubelet 提取容器映像。

#### <a name="connection-target"></a>连接目标

`mcr.microsoft.com`

#### <a name="protocol"></a>协议

HTTPS

#### <a name="port"></a>端口

443

#### <a name="can-use-proxy"></a>可以使用代理

是

#### <a name="authentication"></a>Authentication

无

### <a name="azure-resource-manager-apis"></a>Azure 资源管理器 API
Azure Data Studio， [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 并 Azure CLI 连接到 azure 资源管理器 api，为某些功能在 azure 中发送和检索数据。

#### <a name="connection-source"></a>连接源

正在 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 连接到 Azure Azure Data Studio、或 Azure CLI 的计算机。

#### <a name="connection-target"></a>连接目标

- `login.microsoftonline.com`
- `management.azure.com`
- `san-af-eastus-prod.azurewebsites.net`
- `san-af-eastus2-prod.azurewebsites.net`
- `san-af-australiaeast-prod.azurewebsites.net`
- `san-af-centralus-prod.azurewebsites.net`
- `san-af-westus2-prod.azurewebsites.net`
- `san-af-westeurope-prod.azurewebsites.net`
- `san-af-southeastasia-prod.azurewebsites.net`
- `san-af-koreacentral-prod.azurewebsites.net`
- `san-af-northeurope-prod.azurewebsites.net`
- `san-af-westeurope-prod.azurewebsites.net`
- `san-af-uksouth-prod.azurewebsites.net`
- `san-af-francecentral-prod.azurewebsites.net`

#### <a name="protocol"></a>协议

HTTPS

#### <a name="port"></a>端口

443

#### <a name="can-use-proxy"></a>可以使用代理

是

#### <a name="authentication"></a>Authentication 

Azure Active Directory

### <a name="azure-monitor-apis"></a>Azure monitor Api

Azure Data Studio， [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 并 Azure CLI 连接到 azure 资源管理器 api，为某些功能在 azure 中发送和检索数据。

#### <a name="connection-source"></a>连接源

运行 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 或 Azure CLI 的计算机，该计算机将监视指标或日志上载到 Azure Monitor 中。

#### <a name="connection-target"></a>连接目标

- `login.microsoftonline.com`
- `management.azure.com`
- `*.ods.opinsights.azure.com`
- `*.oms.opinsights.azure.com`
- `*.monitoring.azure.com`

#### <a name="protocol"></a>协议

HTTPS

#### <a name="port"></a>端口

443

#### <a name="can-use-proxy"></a>可以使用代理

是

#### <a name="authentication"></a>Authentication 

Azure Active Directory

> [!NOTE]
> 目前，所有与 Grafana 和 Kibana 仪表板的浏览器 HTTPS/443 连接以及从 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 到数据控制器 API，均使用自签名证书进行 SSL 加密。  未来将推出一项功能，该功能允许你提供自己的证书来加密这些 SSL 连接。

从 Azure Data Studio 和 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 到 KUBERNETES API 服务器的连接使用已建立的 Kubernetes authentication 和 encryption。  使用 Azure Data Studio 和的每个用户都 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 必须经过身份验证后连接到 KUBERNETES API，才能执行与支持 Azure Arc 的数据服务相关的许多操作。

