---
title: 配置服务器参数 - Azure PowerShell - Azure Database for MariaDB
description: 本文介绍如何使用 PowerShell 在 Azure Database for MariaDB 中配置服务参数。
author: savjani
ms.author: pariks
ms.service: jroth
ms.devlang: azurepowershell
ms.topic: how-to
ms.date: 10/1/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 891cb5c6e570269d0414e7b7cece760af143927f
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98662451"
---
# <a name="configure-server-parameters-in-azure-database-for-mariadb-using-powershell"></a>使用 PowerShell 在 Azure Database for MariaDB 中配置服务器参数

可以使用 PowerShell 列出、显示和更新 Azure Database for MariaDB 服务器的配置参数。 在服务器级别会公开引擎配置的一个子集，并可以进行修改。

>[!Note]
> 可在服务器级别全局更新服务器参数，方式是使用 [Azure CLI](./howto-configure-server-parameters-cli.md)、[PowerShell](./howto-configure-server-parameters-using-powershell.md) 或 [Azure 门户](./howto-server-parameters.md)。

## <a name="prerequisites"></a>先决条件

若要完成本操作指南，需要：

- 在本地安装[Az PowerShell 模块](/powershell/azure/install-az-ps)或在浏览器中[Azure Cloud Shell](https://shell.azure.com/)
- [Azure Database for MariaDB 服务器](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> 尽管 Az.MariaDb PowerShell 模块为预览版，但必须使用以下命令从 Az PowerShell 模块单独安装它：`Install-Module -Name Az.MariaDb -AllowPrerelease`。
> Az.MariaDb PowerShell 模块正式版推出后，它会包含在将来的 Az PowerShell 模块发行版中，并在 Azure Cloud Shell 中原生提供。

如果选择在本地使用 PowerShell，请使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 连接到 Azure 帐户。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="list-server-configuration-parameters-for-azure-database-for-mariadb-server"></a>列出 Azure Database for MariaDB 服务器的服务器配置参数

若要列出服务器中的所有可修改参数及其值，请运行 `Get-AzMariaDbConfiguration` cmdlet。

以下示例列出了资源组 myresourcegroup 中服务器 mydemoserver 的服务器配置参数。

```azurepowershell-interactive
Get-AzMariaDbConfiguration -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

有关每个列出参数的定义，请参阅[服务器系统变量](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html)上的 MariaDB 参考部分。

## <a name="show-server-configuration-parameter-details"></a>显示服务器配置参数详细信息

若要显示有关服务器的特定配置参数的详细信息，请运行 `Get-AzMariaDbConfiguration` cmdlet 并指定 Name 参数。

本示例显示了资源组“myresourcegroup”下服务器 mydemoserver 的服务器配置参数 slow\_query\_log 的详细信息。

```azurepowershell-interactive
Get-AzMariaDbConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>修改服务器配置参数值

此外，你还可以修改某个服务器配置参数的值，这会更新 MariaDB 服务器引擎的基础配置值。 若要更新配置，请使用 `Update-AzMariaDbConfiguration` cmdlet。

更新资源组“myresourcegroup”下服务器 mydemoserver 的服务器配置参数 slow\_query\_log。

```azurepowershell-interactive
Update-AzMariaDbConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver -Value On
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 PowerShell 实现 Azure Database for MariaDB 服务器存储的自动增长](howto-auto-grow-storage-powershell.md)。