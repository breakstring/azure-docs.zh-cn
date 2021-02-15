---
title: 配置参数-Azure Database for PostgreSQL-单服务器
description: 本文介绍如何使用 Azure CLI 在 Azure Database for PostgreSQL 单服务器中配置 Postgres 参数。
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 06/19/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4231f348f99073406fcb6a5bef9bf0f84cacf2eb
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005565"
---
# <a name="customize-server-configuration-parameters-for-azure-database-for-postgresql---single-server-using-azure-cli"></a>使用 Azure CLI 自定义 Azure Database for PostgreSQL（单一服务器）的服务器配置参数
可以使用命令行接口 (Azure CLI) 列出、显示和更新 Azure PostgreSQL 服务器的配置参数。 会在服务器级别公开引擎配置的一个子集，并且可以进行修改。 

## <a name="prerequisites"></a>先决条件
若要逐步执行本操作方法指南，需要：
- 按照[创建 Azure Database for PostgreSQL](quickstart-create-server-database-azure-cli.md) 创建 Azure Database for PostgreSQL 服务器和数据库
- 将 [Azure CLI](/cli/azure/install-azure-cli) 命令行接口安装在计算机上或通过浏览器在 Azure 门户中使用 [Azure Cloud Shell](../cloud-shell/overview.md)。

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>列出 Azure Database for PostgreSQL 服务器的服务器配置参数
若要列出服务器中的所有可修改参数及其值，请运行 [az postgres server configuration list](/cli/azure/postgres/server/configuration) 命令。

可以列出资源组“myresourcegroup”下服务器 mydemoserver.postgres.database.azure.com 的服务器配置参数。
```azurecli-interactive
az postgres server configuration list --resource-group myresourcegroup --server mydemoserver
```
## <a name="show-server-configuration-parameter-details"></a>显示服务器配置参数详细信息
若要显示服务器的某个特定配置参数的详细信息，请运行 [az postgres server configuration show](/cli/azure/postgres/server/configuration) 命令。

此示例显示了资源组“myresourcegroup”下服务器 mydemoserver.postgres.database.azure.com 的服务器配置参数 log\_min\_messages 的详细信息。
```azurecli-interactive
az postgres server configuration show --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-server-configuration-parameter-value"></a>修改服务器配置参数值
还可以修改某个服务器配置参数的值，这会更新 PostgreSQL 服务器引擎的基础配置值。 若要更新配置，请使用 [az postgres server configuration set](/cli/azure/postgres/server/configuration) 命令。 

更新资源组“myresourcegroup”下服务器 mydemoserver.postgres.database.azure.com 的服务器配置参数 log\_min\_message。
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver --value INFO
```
若要重置配置参数的值，只需选择省略可选的 `--value` 参数，服务将应用默认值。 以上述示例为例，它类似于下面这样：
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
此命令会将 **log\_min\_messages** 配置重置为默认值 **WARNING**。 有关服务器配置和允许值的详细信息，请参阅有关[服务器配置](https://www.postgresql.org/docs/9.6/static/runtime-config.html)的 PostgreSQL 文档。

## <a name="next-steps"></a>后续步骤
- [了解如何重启服务器](howto-restart-server-cli.md)
- 若要配置和访问服务器日志，请参阅 [Azure Database for PostgreSQL 中的服务器日志](concepts-server-logs.md)
