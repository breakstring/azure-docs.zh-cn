---
title: Azure CLI 脚本示例 - 管理 Web 流量 | Microsoft Docs
description: Azure CLI 脚本示例 - 使用应用程序网关和虚拟机规模集管理 Web 流量。
services: application-gateway
documentationcenter: networking
author: vhorne
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/29/2018
ms.author: victorh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 2e766bcdee2afa3ff4ed75476c619326c4c0e0c2
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99591643"
---
# <a name="manage-web-traffic-using-the-azure-cli"></a>使用 Azure CLI 管理 Web 流量

此脚本创建使用虚拟机规模集作为后端服务器的应用程序网关。 然后，可以对应用程序网关进行配置来管理 Web 流量。 在运行脚本后，可以使用应用程序网关的公共 IP 地址测试该网关。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/application-gateway/create-vmss/create-vmss.sh "Create application gateway")]

## <a name="clean-up-deployment"></a>清理部署 

运行以下命令来删除资源组、应用程序网关和所有相关资源。

```azurecli-interactive 
az group delete --name myResourceGroupAG --yes
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建部署。 表中的每一项均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [az group create](/cli/azure/group) | 创建用于存储所有资源的资源组。 |
| [az network vnet create](/cli/azure/network/vnet) | 创建虚拟网络。 |
| [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) | 在虚拟网络中创建子网。 |
| [az network public-ip create](/cli/azure/network/public-ip) | 创建应用程序网关的公共 IP 地址。 |
| [az network application-gateway create](/cli/azure/network/application-gateway) | 创建应用程序网关。 |
| [az vmss create](/cli/azure/vmss) | 创建虚拟机规模集。 |
| [az network public-ip show](/cli/azure/network/public-ip) | 获取应用程序网关的公共 IP 地址。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure/overview)。

可以在 [Azure Windows VM 文档](../cli-samples.md)中找到其他应用程序网关 CLI 脚本示例。
