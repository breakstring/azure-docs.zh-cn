---
title: CLI：创建计划的备份
description: 了解如何使用 Azure CLI 自动部署和管理应用服务应用。 此示例演示如何为应用创建计划的备份。
author: msangapu-msft
tags: azure-service-management
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.reviewer: cephalin
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 500ac99cd35cfdf601be75a19a1d43f84795cbe8
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97006412"
---
# <a name="create-a-scheduled-backup-for-an-app-service-app-using-cli"></a>使用 CLI 为应用服务应用创建计划备份

此示例脚本使用其相关资源，在应用服务中创建应用，然后为其创建计划备份。 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，需要 Azure CLI 2.0 版或更高版本。 要查找版本，请运行 `az --version`。 如需进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。 

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/backup-scheduled/backup-scheduled.sh?highlight=3-7 "Create a scheduled backup for an app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [`az group create`](/cli/azure/group#az-group-create) | 创建用于存储所有资源的资源组。 |
| [`az storage account create`](/cli/azure/storage/account#az-storage-account-create) | 创建存储帐户。 |
| [`az storage container create`](/cli/azure/storage/container#az-storage-container-create) | 创建 Azure 存储容器。 |
| [`az storage container generate-sas`](/cli/azure/storage/container#az-storage-container-generate-sas) | 生成 Azure 存储容器的 SAS 令牌。  |
| [`az appservice plan create`](/cli/azure/appservice/plan#az-appservice-plan-create) | 创建应用服务计划。 |
| [`az webapp create`](/cli/azure/webapp#az-webapp-create) | 创建应用服务应用。 |
| [`az webapp config backup update`](/cli/azure/webapp/config/backup#az-webapp-config-backup-update) | 为应用服务应用配置新的备份计划。 |
| [`az webapp config backup show`](/cli/azure/webapp/config/backup#az-webapp-config-backup-show) | 显示应用服务应用的备份计划。 |
| [`az webapp config backup list`](/cli/azure/webapp/config/backup#az-webapp-config-backup-list) | 获取应用服务应用的备份列表。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

可以在 [Azure 应用服务文档](../samples-cli.md)中找到其他应用服务 CLI 脚本示例。
