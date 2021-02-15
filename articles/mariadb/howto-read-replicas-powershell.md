---
title: 管理只读副本 - Azure PowerShell - Azure Database for MariaDB
description: 了解如何使用 PowerShell 在 Azure Database for MariaDB 中设置和管理只读副本。
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: how-to
ms.date: 6/10/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 43f0de153a19c0ee7ef44539407c0af4fda61c72
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664981"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-powershell"></a>如何使用 PowerShell 在 Azure Database for MariaDB 中创建和管理只读副本

本文介绍如何使用 PowerShell 在 Azure Database for MariaDB 服务中创建和管理只读副本。 若要详细了解只读副本，请参阅[概述](concepts-read-replicas.md)。

## <a name="azure-powershell"></a>Azure PowerShell

可以使用 PowerShell 创建和管理只读副本。

## <a name="prerequisites"></a>先决条件

若要完成本操作指南，需要：

- 在本地安装[Az PowerShell 模块](/powershell/azure/install-az-ps)或在浏览器中[Azure Cloud Shell](https://shell.azure.com/)
- [Azure Database for MariaDB 服务器](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> 尽管 Az.MariaDb PowerShell 模块为预览版，但必须使用以下命令从 Az PowerShell 模块单独安装它：`Install-Module -Name Az.MariaDb -AllowPrerelease`。
> Az.MariaDb PowerShell 模块正式版推出后，它会包含在将来的 Az PowerShell 模块发行版中，并在 Azure Cloud Shell 中原生提供。

如果选择在本地使用 PowerShell，请使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 连接到 Azure 帐户。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> 只读副本功能仅适用于“常规用途”或“内存优化”定价层中的 Azure Database for MariaDB 服务器。 请确保源服务器位于其中一个定价层中。

### <a name="create-a-read-replica"></a>创建只读副本

> [!IMPORTANT]
> 如果为没有现有副本的源服务器创建副本，源服务器将首先重启，以便为复制做好准备。 请考虑这一点并在非高峰期执行这些操作。

可以使用以下命令创建只读副本服务器：

```azurepowershell-interactive
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzMariaDbServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

`New-AzMariaDbServerReplica` 命令需要以下参数：

| 设置 | 示例值 | 说明  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  在其中创建副本服务器的资源组。  |
| 名称 | mydemoreplicaserver | 所创建的新副本服务器的名称。 |

若要创建跨区域只读副本，请使用 Location 参数。 以下示例在 **美国西部** 区域创建一个副本。

```azurepowershell-interactive
Get-AzMariaDbServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzMariaDServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

若要详细了解可以在哪些区域中创建副本，请访问[只读副本概念文章](concepts-read-replicas.md)。

默认情况下，除非指定了 Sku 参数，否则将使用与源服务器相同的服务器配置来创建只读副本。

> [!NOTE]
> 建议副本服务器的配置应始终采用与源服务器相同或更大的值，以确保副本能够与主服务器保持一致。

### <a name="list-replicas-for-a-source-server"></a>列出源服务器的副本

若要查看给定源服务器的所有副本，请运行以下命令：

```azurepowershell-interactive
Get-AzMariaDReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

`Get-AzMariaDReplica` 命令需要以下参数：

| 设置 | 示例值 | 说明  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  要在其中创建副本服务器的资源组。  |
| ServerName | mydemoserver | 源服务器的名称或 ID。 |

### <a name="delete-a-replica-server"></a>删除副本服务器

可以通过运行 `Remove-AzMariaDbServer` cmdlet 来删除只读副本服务器。

```azurepowershell-interactive
Remove-AzMariaDbServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### <a name="delete-a-source-server"></a>删除源服务器

> [!IMPORTANT]
> 删除源服务器会停止复制到所有副本服务器，并删除源服务器本身。 副本服务器成为现在支持读取和写入的独立服务器。

若要删除源服务器，可以运行 `Remove-AzMariaDbServer` cmdlet。

```azurepowershell-interactive
Remove-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 PowerShell 重启 Azure Database for MariaDB 服务器](howto-restart-server-powershell.md)