---
title: Azure CLI 脚本示例 - 限制 Web 流量 | Microsoft Docs
description: Azure CLI 脚本示例 - 使用 Web 应用程序防火墙和使用 OWASP 规则限制流量的虚拟机规模集创建应用程序网关。
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
ms.openlocfilehash: e9201f41c9552b6a60f9ccd8eacda60ac46f89eb
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99591612"
---
# <a name="restrict-web-traffic-using-the-azure-cli"></a>使用 Azure CLI 限制 Web 流量

此脚本创建具有 Web 应用程序防火墙且使用虚拟机规模集作为后端服务器的应用程序网关。 Web 应用程序防火墙基于 OWASP 规则限制 Web 流量。 在运行脚本后，可以使用应用程序网关的公共 IP 地址测试该网关。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/application-gateway/create-vmss/create-vmss-waf.sh "Create application gateway")]

## <a name="clean-up-deployment"></a>清理部署 

运行以下命令来删除资源组、应用程序网关和所有相关资源。

```azurecli-interactive 
az group delete --name myResourceGroupAG --yes
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建部署。 表中的每一项均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 创建用于存储所有资源的资源组。 |
| [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) | 创建虚拟网络。 |
| [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) | 在虚拟网络中创建子网。 |
| [az network public-ip create](/cli/azure/network/public-ip) | 创建应用程序网关的公共 IP 地址。 |
| [az network application-gateway create](/cli/azure/network/application-gateway) | 创建应用程序网关。 |
| [az vmss create](/cli/azure/vmss#az-vmss-create) | 创建虚拟机规模集。 |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | 创建存储帐户。 |
| [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) | 创建存储帐户。 |
| [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) | 获取应用程序网关的公共 IP 地址。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure/overview)。

可以在 [Azure 应用程序网关文档](../cli-samples.md)中找到其他应用程序网关 CLI 脚本示例。
