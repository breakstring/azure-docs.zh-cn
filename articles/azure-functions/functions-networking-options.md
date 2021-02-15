---
title: Azure Functions 网络选项
description: 在 Azure Functions 中可用的所有网络选项的概述。
author: cachai2
ms.topic: conceptual
ms.date: 1/21/2021
ms.author: cachai
ms.openlocfilehash: 2c3f207e98f574bb6c43f87d34b0a404e263e83c
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "98806981"
---
# <a name="azure-functions-networking-options"></a>Azure Functions 网络选项

本文介绍适用于 Azure Functions 的托管选项的网络功能。 通过以下所有网络选项，你可以在不使用可通过 Internet 路由的地址的情况下访问资源，或限制对函数应用的 Internet 访问。

托管模型提供不同级别的网络隔离。 选择正确模型有助于满足网络隔离要求。

可以通过多种方式托管函数应用：

* 可以从在多租户基础结构上运行的计划选项中进行选择，其中包含不同级别的虚拟网络连接和缩放选项：
    * [消耗计划](consumption-plan.md)会动态缩放以响应负载，并提供最少的网络隔离选项。
    * [高级计划](functions-premium-plan.md)也可以动态缩放，并提供更全面的网络隔离。
    * Azure [应用服务计划](dedicated-plan.md)按固定规模运行，提供类似于高级计划的网络隔离。
* 可以在[应用服务环境](../app-service/environment/intro.md)中运行函数。 此方法可以将函数部署到虚拟网络中，并且可以进行完全的网络控制和隔离。

## <a name="matrix-of-networking-features"></a>网络功能矩阵

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

## <a name="inbound-access-restrictions"></a>入站访问限制

可以使用访问限制来定义被允许或拒绝访问应用的 IP 地址的优先级排序列表。 此列表可以包含 IPv4 和 IPv6 地址，或使用[服务终结点](#use-service-endpoints)的特定虚拟网络子网。 如果存在一个或多个条目，则列表末尾会存在一个隐式的“拒绝所有”。 IP 限制适用于所有函数托管选项。

访问限制在[高级](functions-premium-plan.md)、[消耗](consumption-plan.md)和[应用服务](dedicated-plan.md)中提供。

> [!NOTE]
> 如果进行了网络限制，则只能从虚拟网络内部进行部署，或者在已将用于访问 Azure 门户的计算机的 IP 地址加入安全收件人列表之后进行部署。 不过，你仍然可以使用门户管理该函数。

若要了解详细信息，请参阅 [Azure 应用服务静态访问限制](../app-service/app-service-ip-restrictions.md)。

### <a name="use-service-endpoints"></a>使用服务终结点

使用服务终结点可以限制对所选 Azure 虚拟网络子网的访问。 若要限制对特定子网的访问，请使用“虚拟网络”类型创建限制规则。 然后，可以选择要允许或拒绝访问的订阅、虚拟网络和子网。 

如果尚未为所选子网的 Microsoft.Web 启用服务终结点，它们会自动启用，除非你选择了“忽略缺少的 Microsoft.Web 服务终结点”复选框。 在应用上而不是子网上启用服务终结点的方案主要取决于你是否有权在子网上启用它们。 

如果需要其他人在子网上启用服务终结点，请选择“忽略缺少的 Microsoft.Web 服务终结点”复选框。 将为服务终结点配置应用，以便稍后在子网中启用它们。 

![“添加 IP 限制”窗格的屏幕截图，其中选择了虚拟网络类型。](../app-service/media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

不能使用服务终结点来限制对在应用服务环境中运行的应用程序的访问。 当应用处于应用服务环境中时，可以应用 IP 访问规则来控制对它的访问。 

若要了解如何设置服务终结点，请参阅[建立 Azure Functions 专用站点访问](functions-create-private-site-access.md)。

## <a name="private-endpoint-connections"></a>专用终结点连接

[!INCLUDE [functions-private-site-access](../../includes/functions-private-site-access.md)]

若要调用具有专用终结点连接的其他服务（如存储或服务总线），请确保将应用配置为[对专用终结点进行出站调用](#private-endpoints)。

## <a name="virtual-network-integration"></a>虚拟网络集成

通过虚拟网络集成，函数应用可以访问虚拟网络中的资源。
Azure Functions 支持两种类型的虚拟网络集成：

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

Azure Functions 中的虚拟网络集成将共享基础结构与应用服务 Web 应用结合使用。 若要详细了解这两种类型的虚拟网络集成，请参阅：

* [区域虚拟网络集成](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [需要网关的虚拟网络集成](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

若要了解如何设置虚拟网络集成，请参阅[将函数应用与 Azure 虚拟网络集成](functions-create-vnet.md)。

## <a name="regional-virtual-network-integration"></a>区域虚拟网络集成

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

## <a name="connect-to-service-endpoint-secured-resources"></a>连接到服务终结点保护的资源

若要提供更高级别的安全性，可以使用服务终结点将一些 Azure 服务限制到一个虚拟网络中。 随后必须将函数应用与该虚拟网络集成才能访问资源。 支持虚拟网络集成的所有计划都支持此配置。

若要了解详细信息，请参阅[虚拟网络服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)。

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>将存储帐户限制到虚拟网络中 

创建函数应用时，必须创建或链接到支持 Blob、队列和表存储的常规用途的 Azure 存储帐户。  可以将此存储帐户替换为服务终结点或专用终结点所保护的存储帐户。  此功能目前仅适用于 Windows 高级版计划。  若要使用限制到专用网络的存储帐户来设置函数，请执行以下操作：

1. 使用未启用服务终结点的存储帐户创建一个函数。
1. 将该函数配置为连接到你的虚拟网络。
1. 创建或配置另一存储帐户。  此存储帐户将是我们使用服务终结点保护的存储帐户，可连接我们的函数。
1. 在受保护的存储帐户中[创建文件共享](../storage/files/storage-how-to-create-file-share.md#create-file-share)。
1. 为此存储帐户启用服务终结点或专用终结点。  
    * 如果使用专用终结点连接，此存储帐户将需要一个专用终结点来存储 `file` 和 `blob` 子资源。  如果使用某些功能（如 Durable Functions），则还需要可通过专用终结点连接访问的 `queue` 和 `table`。
    * 如果使用服务终结点，请为存储帐户启用专用于函数应用的子网。
1. （可选）将函数应用存储帐户中的文件和 blob 内容复制到受保护的存储帐户和文件共享。
1. 复制此存储帐户的连接字符串。
1. 将函数应用的“配置”下的“应用程序设置”更新为以下内容：
    - 将 `AzureWebJobsStorage` 更新为受保护存储帐户的连接字符串。
    - 将 `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` 更新为受保护存储帐户的连接字符串。
    - 将 `WEBSITE_CONTENTSHARE` 更新为在受保护存储帐户中创建的文件共享的名称。
    - 使用名称 `WEBSITE_CONTENTOVERVNET` 和值 `1` 创建新设置。
    - 如果存储帐户使用专用终结点连接，请验证或添加以下设置
        - `WEBSITE_VNET_ROUTE_ALL`，值为 `1`。
        - `WEBSITE_DNS_SERVER`，值为 `168.63.129.16` 
1. 保存应用程序设置。  

函数应用会重启，并会立即连接到受保护的存储帐户。

## <a name="use-key-vault-references"></a>使用 Key Vault 引用

可以在不需进行任何代码更改的情况下，通过 Azure Key Vault 引用在 Azure Functions 应用程序中使用 Azure Key Vault 中的机密。 Azure Key Vault 是一项服务，可以提供集中式机密管理，并且可以完全控制访问策略和审核历史记录。

目前，如果密钥保管库使用服务终结点进行保护，则 [Key Vault 引用](../app-service/app-service-key-vault-references.md)不起作用。 若要使用虚拟网络集成连接到密钥保管库，需要在应用程序代码中调用 Key Vault。

## <a name="virtual-network-triggers-non-http"></a>虚拟网络触发器（非 HTTP）

目前，可以通过以下两种方式之一从虚拟网络内使用非 HTTP 触发器函数：

+ 在高级计划中运行函数应用，并启用虚拟网络触发器支持。
+ 在应用服务计划或应用服务环境中运行函数应用。

### <a name="premium-plan-with-virtual-network-triggers"></a>具有虚拟网络触发器的高级计划

运行高级计划时，可以将非 HTTP 触发器函数连接到在虚拟网络中运行的服务。 为此，必须为函数应用启用虚拟网络触发器支持。 在 [Azure 门户](https://portal.azure.com)的“配置” > “函数运行时设置”下可以找到“运行时规模监视”设置。

:::image type="content" source="media/functions-networking-options/virtual-network-trigger-toggle.png" alt-text="VNETToggle":::

还可以使用以下 Azure CLI 命令启用虚拟网络触发器：

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

> [!TIP]
> 启用虚拟网络触发器可能会影响应用程序的性能，因为应用服务计划实例需要监视触发器来确定何时缩放。 这种影响可能非常小。

版本 2.x 和更高版本的 Functions 运行时支持虚拟网络触发器。 支持以下非 HTTP 触发器类型。

| 分机 | 最低版本 |
|-----------|---------| 
|[Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/) | 3.0.10 或更高版本 |
|[Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs)| 4.1.0 或更高版本|
|[Microsoft.Azure.WebJobs.Extensions.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus)| 3.2.0 或更高版本|
|[Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)| 3.0.5 或更高版本|
|[Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)| 2.0.0 或更高版本|

> [!IMPORTANT]
> 启用虚拟网络触发器支持时，只有上表中显示的触发器类型才能随应用程序进行动态缩放。 仍可以使用不在该表中的触发器，但它们不会缩放到超出其预热实例计数。 有关触发器的完整列表，请参阅[触发器和绑定](./functions-triggers-bindings.md#supported-bindings)。

### <a name="app-service-plan-and-app-service-environment-with-virtual-network-triggers"></a>具有虚拟网络触发器的应用服务计划和应用服务环境

函数应用在应用服务计划或应用服务环境中运行时，可以使用非 HTTP 触发器函数。 若要使函数可正确触发，必须使用可以访问触发器连接中定义的资源的访问权限来连接到虚拟网络。

例如，假设要将 Azure Cosmos DB 配置为仅接受来自虚拟网络的流量。 在这种情况下，必须在提供虚拟网络与该虚拟网络集成的应用服务计划中部署函数应用。 集成使函数可以由该 Azure Cosmos DB 资源触发。

## <a name="hybrid-connections"></a>混合连接

[混合连接](../azure-relay/relay-hybrid-connections-protocol.md)是可用于访问其他网络中的应用程序资源的一项 Azure 中继功能。 使用混合连接可以从应用访问应用程序终结点。 无法使用它访问应用程序。 在除消耗计划之外的所有计划中，混合连接可用于 Windows 上运行的函数。

在 Azure Functions 中使用时，每个混合连接与单个 TCP 主机和端口组合相关联。 这意味着，混合连接终结点可以位于任何操作系统和任何应用程序上，前提是你能够访问 TCP 侦听端口。 混合连接功能不知道也不关心应用程序协议或者要访问的内容是什么。 它只提供网络访问。

有关详细信息，请参阅[应用服务文档中的“混合连接”](../app-service/app-service-hybrid-connections.md)。 Azure Functions 支持这些相同配置步骤。

>[!IMPORTANT]
> 只有 Windows 计划才支持混合连接。 不支持 Linux。

## <a name="outbound-ip-restrictions"></a>出站 IP 限制

高级计划、应用服务计划或应用服务环境中提供出站 IP 限制。 可以为部署应用服务环境的虚拟网络配置出站限制。

将高级计划或应用服务计划中的函数应用与虚拟网络集成时，默认情况下，应用仍可对 Internet 进行出站呼叫。 通过添加应用程序设置 `WEBSITE_VNET_ROUTE_ALL=1`，可强制将所有出站流量发送到虚拟网络中，在其中可以使用网络安全组规则限制流量。

## <a name="automation"></a>自动化
以下 API 可让你以编程方式管理区域虚拟网络集成：

+ **Azure CLI**：使用 [`az functionapp vnet-integration`](/cli/azure/functionapp/vnet-integration) 命令可添加、列出或删除区域虚拟网络集成。  
+ **ARM 模板**：可以通过使用 Azure 资源管理器模板来启用区域虚拟网络集成。 有关完整示例，请参阅[此 Functions 快速入门模板](https://azure.microsoft.com/resources/templates/101-function-premium-vnet-integration/)。

## <a name="troubleshooting"></a>故障排除

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="next-steps"></a>后续步骤

若要详细了解网络和 Azure Functions：

* [遵循有关虚拟网络集成入门的教程](./functions-create-vnet.md)
* [阅读 Functions 网络常见问题解答](./functions-networking-faq.md)
* [详细了解虚拟网络与应用服务/Functions 的集成](../app-service/web-sites-integrate-with-vnet.md)
* [详细了解 Azure 中的虚拟网络](../virtual-network/virtual-networks-overview.md)
* [在应用服务环境中允许更多的网络功能和控制](../app-service/environment/intro.md)
* [使用混合连接连接到单独的本地资源而无需更改防火墙](../app-service/app-service-hybrid-connections.md)
