---
title: CLI 脚本 - 缩放服务器 - Azure Database for MySQL
description: 此示例 CLI 脚本在查询指标后用于 MySQL 服务器的 Azure 数据库缩放为不同的性能级别。
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: 31cf6d1f3bfcc44887ec939cb2f27df77f12cea3
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94539254"
---
# <a name="monitor-and-scale-an-azure-database-for-mysql-server-using-azure-cli"></a>使用 Azure CLI 监视和缩放用于 MySQL 服务器的 Azure 数据库
此示例 CLI 脚本在查询指标后为单个 Azure Database for MySQL 服务器缩放计算和存储。 计算可以增加或减少。 存储只能增加。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要 Azure CLI 版本 2.0 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。 

## <a name="sample-script"></a>示例脚本
使用你的订阅 ID 更新脚本。
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/scale-mysql-server/scale-mysql-server.sh "Create and scale Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>清理部署
运行脚本示例后，请使用以下命令删除资源组以及与其关联的所有资源。 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/scale-mysql-server/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>脚本说明
此脚本使用下表中列出的命令：

| **命令** | **说明** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 创建用于存储所有资源的资源组。 |
| [az mysql server create](/cli/azure/mysql/server#az-mysql-server-create) | 创建用于托管数据库的 MySQL 服务器。 |
| [az mysql server update](/cli/azure/mysql/server#az-mysql-server-update) | 更新 MySQL 服务器的属性。 |
| [az monitor metrics list](/cli/azure/monitor/metrics#az-monitor-metrics-list) | 列出资源的指标值。 |
| [az group delete](/cli/azure/group#az-group-delete) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤
- 详细了解 [Azure Database for MySQL 计算和存储](../concepts-pricing-tiers.md)
- 请尝试其他脚本：[用于 MySQL 的 Azure 数据库的 Azure CLI 示例](../sample-scripts-azure-cli.md)
- 详细了解 [Azure CLI](/cli/azure)
