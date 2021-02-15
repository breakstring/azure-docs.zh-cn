---
title: 创建 Azure 网络观察程序实例 | Microsoft 文档
description: 了解如何使用 Azure 门户或其他技术在 Azure 区域中创建 Azure 网络观察程序，以及如何删除网络观察程序。
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: b1314119-0b87-4f4d-b44c-2c4d0547fb76
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: c308824afdcae5f5c04a316c199bad71ad84a429
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94961985"
---
# <a name="create-an-azure-network-watcher-instance"></a>创建 Azure 网络观察程序实例

网络观察程序是一个区域性服务，可用于在网络方案级别监视和诊断 Azure 内部以及传入和传出 Azure 的流量的状态。 使用方案级别监视可以诊断端到端网络级别视图的问题。 借助网络观察程序随附的网络诊断和可视化工具，可以了解、诊断和洞察 Azure 中的网络。 通过创建网络观察程序资源启用网络观察程序。 使用此资源，可利用网络观察程序功能。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="network-watcher-is-automatically-enabled"></a>自动启用网络观察程序
在订阅中创建或更新虚拟网络时，将在虚拟网络的区域中自动启用网络观察程序。 自动启用网络观察程序对资源或相关费用没有任何影响。

#### <a name="opt-out-of-network-watcher-automatic-enablement"></a>选择退出网络观察程序自动启用
如果想要退出网络观察程序自动启用，可以通过运行以下命令来执行此操作：

> [!WARNING]
> 选择退出网络观察程序自动启用是一项永久性更改。 你选择退出后，就不能在没有[联系支持人员](https://azure.microsoft.com/support/options/)的情况下选择加入

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName DisableNetworkWatcherAutocreation -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

```azurecli-interactive
az feature register --name DisableNetworkWatcherAutocreation --namespace Microsoft.Network
az provider register -n Microsoft.Network
```



## <a name="create-a-network-watcher-in-the-portal"></a>在门户中创建网络观察程序

导航到“所有服务” > “网络” > “网络观察程序”。 可以选择要为其启用网络观察程序的所有订阅。 此操作在每个可用的区域中创建网络观察程序。

![创建网络观察程序](./media/network-watcher-create/figure1.png)

使用门户启用网络观察程序时，网络观察程序实例的名称会自动设置为 *NetworkWatcher_region_name*，其中，*region_name* 对应于启用了该实例的 Azure 区域。 例如，在美国中西部区域启用的网络观察程序将被命名为 *NetworkWatcher_westcentralus*。

将自动在名为 *NetworkWatcherRG* 的资源组中创建网络观察程序实例。 如果该资源组尚不存在，则会创建该资源组。

如果希望自定义网络观察程序实例的名称以及将其置于其中的资源组，可以使用以下各节中介绍的 PowerShell、Azure CLI、REST API 或 ARMClient 方法。 在每个选项中，都必须存在资源组，然后才能在其中创建网络观察程序。  

## <a name="create-a-network-watcher-with-powershell"></a>使用 PowerShell 创建网络观察程序

若要创建网络观察程序的实例，请运行以下示例：

```powershell
New-AzNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-azure-cli"></a>使用 Azure CLI 创建网络观察程序

若要创建网络观察程序的实例，请运行以下示例：

```azurecli
az network watcher configure --resource-group NetworkWatcherRG --locations westcentralus --enabled
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>使用 REST API 创建网络观察程序

通过 PowerShell 调用 REST API 时，使用的是 ARMclient。 根据 [Chocolatey 上的 ARMClient](https://chocolatey.org/packages/ARMClient) 中所述在 chocolatey 上找到 ARMClient。

### <a name="log-in-with-armclient"></a>使用 ARMClient 登录

```powerShell
armclient login
```

### <a name="create-the-network-watcher"></a>创建网络观察程序

```powershell
$subscriptionId = '<subscription id>'
$networkWatcherName = '<name of network watcher>'
$resourceGroupName = '<resource group name>'
$apiversion = "2016-09-01"
$requestBody = @"
{
'location': 'West Central US'
}
"@

armclient put "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}?api-version=${api-version}" $requestBody
```

## <a name="create-a-network-watcher-using-azure-quickstart-template"></a>使用 Azure 快速入门模板创建网络观察程序

若要创建网络观察程序实例，请参阅此 [快速入门模板](https://azure.microsoft.com/resources/templates/101-networkwatcher-create/)

## <a name="delete-a-network-watcher-in-the-portal"></a>在门户中删除网络观察程序

导航到“所有服务” > “网络” > “网络观察程序”。

选择“概述”选项卡（如果尚未在该选项卡上）。 使用下拉列表选择要在其中禁用网络观察程序的订阅。
通过单击箭头，展开所选订阅的区域列表。 对于任何给定区域，使用右侧的 3 个点访问上下文菜单。
单击“禁用网络观察程序”以开始禁用。 系统会要求你确认此步骤。 单击“是”以继续。
在门户上，你必须对每个订阅中的每个区域分别执行此操作。


## <a name="delete-a-network-watcher-with-powershell"></a>使用 PowerShell 删除网络观察程序

若要删除网络观察程序的实例，请运行以下示例：

```powershell
New-AzResourceGroup -Name NetworkWatcherRG -Location westcentralus
New-AzNetworkWatcher -Name NetworkWatcher_westcentralus -ResourceGroup NetworkWatcherRG -Location westcentralus
Remove-AzNetworkWatcher -Name NetworkWatcher_westcentralus -ResourceGroup NetworkWatcherRG
```

## <a name="next-steps"></a>后续步骤

现在，已有网络观察程序实例，请了解可用功能：

* [拓扑](./view-network-topology.md)
* [数据包捕获](network-watcher-packet-capture-overview.md)
* [IP 流验证](network-watcher-ip-flow-verify-overview.md)
* [下一跃点](network-watcher-next-hop-overview.md)
* [安全组视图](network-watcher-security-group-view-overview.md)
* [NSG 流日志记录](network-watcher-nsg-flow-logging-overview.md)
* [虚拟网络网关故障排除](network-watcher-troubleshoot-overview.md)