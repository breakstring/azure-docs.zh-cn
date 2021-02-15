---
title: Azure ExpressRoute：配置 ExpressRoute Global Reach： CLI
description: 了解如何将 ExpressRoute 线路链接在一起以在本地网络之间建立专用网络，以及如何使用 Azure CLI 启用 Global Reach。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/07/2021
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: 55f1e4c030dc0e7c8d0011dcff0cc1c9f8fec06e
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99260225"
---
# <a name="configure-expressroute-global-reach-by-using-the-azure-cli"></a>使用 Azure CLI 配置 ExpressRoute Global Reach

本文帮助你使用 Azure CLI 配置 Azure ExpressRoute Global Reach。 有关详细信息，请参阅 [ExpressRoute Global Reach](expressroute-global-reach.md)。
 
在开始配置前，请满足以下要求：

* 安装最新版本的 Azure CLI。 请参阅[安装 Azure CLI](/cli/azure/install-azure-cli) 和 [Azure CLI 入门](/cli/azure/get-started-with-azure-cli)。
* 了解 ExpressRoute 线路预配[工作流](expressroute-workflows.md)。
* 确保 ExpressRoute 线路处于“已预配”状态。
* 确保在 ExpressRoute 线路上配置了 Azure 专用对等互连。  

### <a name="sign-in-to-your-azure-account"></a>登录到 Azure 帐户

若要开始配置，请登录到 Azure 帐户。 以下命令会打开默认浏览器并提示输入 Azure 帐户的登录凭据：  

```azurecli
az login
```

如果有多个 Azure 订阅，请查看该帐户的订阅：

```azurecli
az account list
```

指定要使用的订阅：

```azurecli
az account set --subscription <your subscription ID>
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>确定用于配置的 ExpressRoute 线路

可以在任意两个 ExpressRoute 线路之间启用 ExpressRoute Global Reach。 线路必须位于受支持的国家/地区，并且是在不同的对等位置创建的。 如果订阅拥有这两个线路，则可以选择任一线路来运行配置。 但是，如果两个线路位于不同的 Azure 订阅中，则必须从某个线路创建一个授权密钥。 使用从第一条线路生成的授权密钥，可以在第二个线路上启用 Global Reach。

## <a name="enable-connectivity-between-your-on-premises-networks"></a>启用本地网络之间的连接

运行该命令以启用连接时，请注意参数值的以下要求：

* *peer-circuit* 应当是完整的资源 ID。 例如：

  > /subscriptions/{vm your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering

* *地址前缀* 必须是 IPv4 子网 (例如，"10.0.0.0/29" ) 。 我们使用此子网中的 IP 地址在两条 ExpressRoute 线路之间建立连接。 不能在 Azure 虚拟网络或本地网络中使用此子网中的地址。

运行以下 CLI 命令来连接两条 ExpressRoute 线路：

```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29>
```

CLI 输出如下所示：

```output
{
  "addressPrefix": "<__.__.__.__/29>",
  "authorizationKey": null,
  "circuitConnectionStatus": "Connected",
  "etag": "W/\"48d682f9-c232-4151-a09f-fab7cb56369a\"",
  "expressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<ResourceGroupName>"
  },
  "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering/connections/<ConnectionName>",
  "name": "<ConnectionName>",
  "peerExpressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<Circuit2ResourceGroupName>"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "<ResourceGroupName>",
  "type": "Microsoft.Network/expressRouteCircuits/peerings/connections"
}
```

此操作完成后，将通过两条 ExpressRoute 线路在两端的本地网络之间建立连接。

## <a name="enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions"></a>在不同 Azure 订阅中启用 ExpressRoute 线路之间的连接

如果两条线路不在同一 Azure 订阅中，则需要获得授权。 在下面的配置中，你将在2个订阅中生成授权。 然后，将授权密钥传递到线路1。

1. 生成授权密钥：

   ```azurecli
   az network express-route auth create --circuit-name <Circuit2Name> -g <Circuit2ResourceGroupName> -n <AuthorizationName>
   ```

   CLI 输出如下所示：

   ```output
   {
     "authorizationKey": "<authorizationKey>",
     "authorizationUseStatus": "Available",
     "etag": "W/\"cfd15a2f-43a1-4361-9403-6a0be00746ed\"",
     "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/authorizations/<AuthorizationName>",
     "name": "<AuthorizationName>",
     "provisioningState": "Succeeded",
     "resourceGroup": "<Circuit2ResourceGroupName>",
     "type": "Microsoft.Network/expressRouteCircuits/authorizations"
   }
   ```

1. 记录线路 2 的资源 ID 和授权密钥。

1. 针对线路 1 运行以下命令，传入线路 2 的资源 ID 和授权密钥：

   ```azurecli
   az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
   ```

此操作完成后，将通过两条 ExpressRoute 线路在两端的本地网络之间建立连接。

## <a name="get-and-verify-the-configuration"></a>获取并验证配置

使用以下命令验证在其中进行了配置的线路（上例中的线路 1）的配置：

```azurecli
az network express-route show -n <CircuitName> -g <ResourceGroupName>
```

在 CLI 输出中，你将看到 *CircuitConnectionStatus*。 它将指示两条线路之间的连接是已建立（“Connected”）还是未建立（“Disconnected”）。 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>禁用本地网络之间的连接

若要禁用连接，请针对在其中进行了配置的线路（上例中的线路 1）运行以下命令。

```azurecli
az network express-route peering connection delete -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName>
```

使用 ```show``` 命令验证状态。

此操作完成后，本地网络之间将不再具有通过 ExpressRoute 线路建立的连接。

## <a name="next-steps"></a>后续步骤

* [详细了解 ExpressRoute Global Reach](expressroute-global-reach.md)
* [验证 ExpressRoute 连接](expressroute-troubleshooting-expressroute-overview.md)
* [将 ExpressRoute 线路链接到虚拟网络](expressroute-howto-linkvnet-arm.md)
