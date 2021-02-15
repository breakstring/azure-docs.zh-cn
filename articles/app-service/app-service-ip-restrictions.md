---
title: Azure 应用服务访问限制
description: 了解如何通过设置访问限制，在 Azure App Service 中保护应用。
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 12/17/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 1bcd0ea253db01d626ef1887ef0983d30ca794b9
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209934"
---
# <a name="set-up-azure-app-service-access-restrictions"></a>设置 Azure 应用服务访问限制

通过设置访问限制可以定义一个按优先级排序的允许/拒绝列表，用于控制在网络中对应用的访问。 该列表可以包含 IP 地址或 Azure 虚拟网络子网。 如果存在一个或多个条目，则列表末尾会存在一个隐式的“全部拒绝”。

访问限制功能适用于所有 Azure App Service 托管的工作负载。 工作负荷可以包括 web 应用、API 应用、Linux 应用、Linux 容器应用和功能。

向你的应用程序发出请求时，将根据访问限制列表中的规则对 "发件人" 地址进行评估。 如果 "发件人" 地址在配置了 "服务终结点到 Microsoft" 的子网中，则会将源子网与访问限制列表中的虚拟网络规则进行比较。 如果不允许根据列表中的规则访问该地址，服务会使用 [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) 状态代码进行答复。

访问限制功能在应用服务前端角色中实现，这些角色是运行代码的辅助角色主机的上游。 因此，访问限制是有效的网络访问控制列表 (ACL)。

限制从 Azure 虚拟网络访问 Web 应用的功能通过[服务终结点][serviceendpoints]启用。 使用服务终结点，你可以限制从所选子网对多租户服务的访问。 对托管在应用服务环境中的应用的流量进行限制不起作用。 如果处于应用服务环境中，可应用 IP 地址规则控制对应用的访问。

> [!NOTE]
> 必须在网络端以及要对其启用服务终结点的 Azure 服务上同时启用服务终结点。 有关支持服务终结点的 Azure 服务列表，请参阅[虚拟网络服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)。
>

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-flow.png" alt-text="访问限制流程图。":::

## <a name="manage-access-restriction-rules-in-the-portal"></a>管理门户中的访问限制规则

若要向应用添加访问限制规则，请执行以下操作：

1. 登录到 Azure 门户。

1. 在左窗格中，选择“网络”。

1. 在“网络”窗格的“访问限制”下，选择“配置访问限制”  。

    :::image type="content" source="media/app-service-ip-restrictions/access-restrictions.png" alt-text="Azure 门户中的“应用服务网络选项”窗格的屏幕截图。":::

1. 在 " **访问限制** " 页上，查看为应用定义的访问限制规则的列表。

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-browse.png" alt-text="Azure 门户中的 &quot;访问限制&quot; 页的屏幕截图，显示为所选应用定义的访问限制规则的列表。":::

   列表显示当前应用于该应用的所有限制。 如果你的应用有虚拟网络限制，则该表将显示是否为 Microsoft 启用了服务终结点。 如果未对应用定义任何限制，则可从任何位置访问该应用。

### <a name="add-an-access-restriction-rule"></a>添加访问限制规则

若要向应用添加访问限制规则，请在 " **访问限制** " 窗格中选择 " **添加规则**"。 规则在添加后会立即生效。 

规则会从“优先级”列中最小的数字开始，按优先级顺序强制执行。 即使只添加了一条规则，一个隐式的“全部拒绝”也会立即生效。

在“添加访问限制”窗格上创建规则时，请执行以下操作：

1. 在“操作”下，选择“允许”或“拒绝”  。  

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-ip-add.png?v2" alt-text="&quot;添加访问限制&quot; 窗格的屏幕截图。":::

1. （可选）输入规则名称和说明。
1. 在“优先级”框中，输入一个优先级值。
1. 在“类型”下拉列表中选择规则类型。

以下各节介绍了不同类型的规则。

> [!NOTE]
> - 访问限制规则限制为512。 如果需要512个以上的访问限制规则，我们建议你考虑安装独立的安全产品，如 Azure 前门、Azure 应用网关或 WAF。
>
#### <a name="set-an-ip-address-based-rule"></a>设置基于 IP 地址的规则

按照上一部分中所述的过程进行操作，但要添加以下内容：
* 在步骤4中，在 " **类型** " 下拉列表中选择 " **IPv4** " 或 " **IPv6**"。 

在无类别 Inter-Domain 路由 (CIDR) 表示法中指定 IPv4 和 IPv6 地址的 **IP 地址块** 。 若要指定地址，可以使用类似 1.2.3.4/32 的格式，其中前四个八位字节代表自己的 IP 地址，/32 为掩码 。 所有地址的 IPv4 CIDR 表示法都为 0.0.0.0/0。 若要了解有关 CIDR 表示法的详细信息，请参阅无 [类别 Inter-Domain 路由](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)。 

#### <a name="set-a-service-endpoint-based-rule"></a>设置基于服务终结点的规则

* 对于步骤4，请在 " **类型** " 下拉列表中选择 " **虚拟网络**"。

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-vnet-add.png?v2" alt-text="&quot;添加限制&quot; 窗格的屏幕截图，其中选择了虚拟网络类型。":::

指定 " **订阅**"、" **虚拟网络**" 和 " **子网** " 下拉列表，匹配要限制对其进行访问的内容。

使用服务终结点可以限制对所选 Azure 虚拟网络子网的访问。 如果尚未为所选子网的 Microsoft.Web 启用服务终结点，它们会自动启用，除非你选择了“忽略缺少的 Microsoft.Web 服务终结点”复选框。 在应用上而不是子网上启用服务终结点的方案主要取决于你是否有权在子网上启用它们。 

如果需要其他人在子网上启用服务终结点，请选择“忽略缺少的 Microsoft.Web 服务终结点”复选框。 将为服务终结点配置应用，以便稍后在子网中启用它们。 

不能使用服务终结点来限制对在应用服务环境中运行的应用程序的访问。 当应用处于应用服务环境中时，可以应用 IP 访问规则来控制对它的访问。 

对于服务终结点，你可以配置应用程序网关或其他 web 应用程序防火墙 (WAF) 设备。 你还可以配置具有安全后端的多层应用程序。 有关详细信息，请参阅 [联网功能和应用服务](networking-features.md) 以及 [与服务终结点的应用程序网关集成](networking/app-gateway-with-service-endpoints.md)。

> [!NOTE]
> - 使用 IP 安全套接字层 (SSL) 虚拟 IP (VIP) 的 web 应用目前不支持服务终结点。
>
#### <a name="set-a-service-tag-based-rule-preview"></a> (预览中设置基于服务标记的规则) 

* 对于步骤4，在 " **类型** " 下拉列表中，选择 " **服务标记 (预览")**。

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-service-tag-add.png" alt-text="已选择服务标记类型的 &quot;添加限制&quot; 窗格的屏幕截图。":::

每个服务标记代表 Azure 服务中的 IP 范围列表。 [服务标记文档][servicetags]中可以找到这些服务的列表和指向特定范围的链接。

预览阶段的访问限制规则支持以下服务标记列表：
* ActionGroup
* AzureCloud
* AzureCognitiveSearch
* AzureConnectors
* AzureEventGrid
* AzureFrontDoor.Backend
* AzureMachineLearning
* AzureSignalR
* AzureTrafficManager
* LogicApps
* ServiceFabric

### <a name="edit-a-rule"></a>编辑规则

1. 若要开始编辑现有访问限制规则，请在 " **访问限制** " 页上，选择要编辑的规则。

1. 在 " **编辑访问限制** " 窗格中进行更改，然后选择 " **更新规则**"。 编辑的内容会立即生效，包括在优先级排序方面的更改。

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-ip-edit.png?v2" alt-text="Azure 门户中 &quot;编辑访问限制&quot; 窗格的屏幕截图，显示现有访问限制规则的字段。":::

   > [!NOTE]
   > 编辑规则时，无法在规则类型之间切换。 

### <a name="delete-a-rule"></a>删除规则

若要删除规则，请在“访问限制”页上选择要删除的规则旁边的省略号 (...)，然后选择“删除”  。

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-delete.png" alt-text="&quot;访问限制&quot; 页的屏幕截图，显示要删除的访问限制规则旁边的 &quot;删除&quot; 省略号。":::

## <a name="access-restriction-advanced-scenarios"></a>访问限制高级方案
以下部分介绍了一些使用访问限制的高级方案。
### <a name="block-a-single-ip-address"></a>阻止单个 IP 地址

添加第一个访问限制规则时，服务将添加优先级为2147483647的显式 *拒绝全部* 规则。 实际上，显式“全部拒绝”规则将是最后执行的规则，并将阻止访问未被“允许”规则明确允许的任何 IP 地址 。

如果你希望显式阻止单个 IP 地址或 IP 地址块，但允许所有其他访问，请添加一个显式的“全部允许”规则。

:::image type="content" source="media/app-service-ip-restrictions/block-single-address.png" alt-text="Azure 门户中的 &quot;访问限制&quot; 页的屏幕截图，显示单个阻止的 IP 地址。":::

### <a name="restrict-access-to-an-scm-site"></a>限制对 SCM 站点的访问 

除了能够控制对应用的访问以外，还可以限制对应用所用的 SCM 站点的访问。 SCM 站点既是 Web 部署终结点，也是 Kudu 控制台。 对于 SCM 站点，可以分配不同于应用的访问限制；也可以对应用和 SCM 站点使用相同的限制设置。 如果选择“与 \<app name> 相同的限制”复选框，则所有内容都将作废。如果清除该复选框，则会重新应用 SCM 站点设置。 

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-scm-browse.png" alt-text="Azure 门户中的 &quot;访问限制&quot; 页的屏幕截图，显示没有为 SCM 站点或应用设置任何访问限制。":::

### <a name="restrict-access-to-a-specific-azure-front-door-instance-preview"></a> (预览限制对特定 Azure 前门实例的访问) 
从 Azure 前门端到应用程序的流量源自 AzureFrontDoor 服务标记中定义的一组已知 IP 范围。 使用服务标记限制规则，你可以将流量限制为仅源自 Azure 前门。 若要确保流量仅源自特定实例，需要根据 Azure 前门发送的唯一 http 标头进一步筛选传入的请求。 预览期间，可通过 PowerShell 或 REST/ARM 实现此目的。 

* PowerShell 示例 (前门 ID 可在 Azure 门户) 中找到：

   ```azurepowershell-interactive
    $frontdoorId = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName" `
      -Name "Front Door example rule" -Priority 100 -Action Allow -ServiceTag AzureFrontDoor.Backend `
      -HttpHeader @{'x-azure-fdid' = $frontdoorId}
    ```
## <a name="manage-access-restriction-rules-programmatically"></a>以编程方式管理访问限制规则

可以通过执行以下任一操作以编程方式添加访问限制： 

* 使用 [Azure CLI](/cli/azure/webapp/config/access-restriction?view=azure-cli-latest&preserve-view=true)。 例如：
   
  ```azurecli-interactive
  az webapp config access-restriction add --resource-group ResourceGroup --name AppName \
    --rule-name 'IP example rule' --action Allow --ip-address 122.133.144.0/24 --priority 100
  ```

* 使用 [Azure PowerShell](/powershell/module/Az.Websites/Add-AzWebAppAccessRestrictionRule?view=azps-5.2.0&preserve-view=true)。 例如：


  ```azurepowershell-interactive
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName"
      -Name "Ip example rule" -Priority 100 -Action Allow -IpAddress 122.133.144.0/24
  ```
   > [!NOTE]
   > 使用服务标记、http 标头或多源规则至少需要5.1.0 版本。 可以通过以下方式验证已安装模块的版本： **get-installedmodule-Name Az**

还可通过以下任一方法手动设置值：

* 在 Azure 资源管理器中对应用配置使用 [Azure REST API](/rest/api/azure/) PUT 操作。 此信息在 Azure 资源管理器中的位置为：

  management.azure.com/subscriptions/**订阅 ID**/resourceGroups/**资源组**/providers/Microsoft.Web/sites/**Web 应用名称**/config/web？ api 版本 = 2020-06-01

* 使用资源管理器模板。 例如，可以使用 resources.azure.com 并编辑 ipSecurityRestrictions 块以添加所需的 JSON。

  前面的示例的 JSON 语法为：

  ```json
  {
    "properties": {
      "ipSecurityRestrictions": [
        {
          "ipAddress": "122.133.144.0/24",
          "action": "Allow",
          "priority": 100,
          "name": "IP example rule"
        }
      ]
    }
  }
  ```
  使用服务标记和 http 标头限制的高级示例的 JSON 语法为：
  ```json
  {
    "properties": {
      "ipSecurityRestrictions": [
        {
          "ipAddress": "AzureFrontDoor.Backend",
          "tag": "ServiceTag",
          "action": "Allow",
          "priority": 100,
          "name": "Azure Front Door example",
          "headers": {
            "x-azure-fdid": [
              "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
            ]
          }
        }
      ]
    }
  }
  ```
## <a name="set-up-azure-functions-access-restrictions"></a>设置 Azure Functions 访问限制

访问限制也适用于与应用服务计划具有相同功能的函数应用。 启用访问限制时，还可针对任何不允许的 IP 禁用 Azure 门户代码编辑器。

## <a name="next-steps"></a>后续步骤
[Azure Functions 的访问限制](../azure-functions/functions-networking-options.md#inbound-access-restrictions)  
[应用程序网关与服务终结点的集成](networking/app-gateway-with-service-endpoints.md)

<!--Links-->
[serviceendpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[servicetags]: ../virtual-network/service-tags-overview.md