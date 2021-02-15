---
title: 教程：设计 Azure Database for PostgreSQL - 单一服务器 - Azure PowerShell
description: 本教程演示如何使用 Azure PowerShell 创建、配置和查询你的第一个 Azure Database for PostgreSQL - 单一服务器。
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurepowershell
ms.topic: tutorial
ms.date: 06/08/2020
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: cf84a8abcc0f1c890a51bf07056ac31d37107e53
ms.sourcegitcommit: 003ac3b45abcdb05dc4406661aca067ece84389f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2020
ms.locfileid: "96749107"
---
# <a name="tutorial-design-an-azure-database-for-postgresql---single-server-using-powershell"></a>教程：使用 PowerShell 设计 Azure Database for PostgreSQL - 单一服务器

Azure Database for PostgreSQL 是 Microsoft 云中基于 PostgreSQL 社区版数据库引擎的一种关系数据库服务。 本教程介绍如何使用 PowerShell 和其他实用程序来执行以下操作：

> [!div class="checklist"]
> - 创建用于 PostgreSQL 的 Azure 数据库
> - 配置服务器防火墙
> - 使用 [psql  ](https://www.postgresql.org/docs/9.6/static/app-psql.html) 实用工具创建数据库
> - 加载示例数据
> - 查询数据
> - 更新数据
> - 还原数据

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

如果选择在本地使用 PowerShell，则本文要求安装 Az PowerShell 模块，并使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 连接到 Azure 帐户。 有关安装 Az PowerShell 模块的详细信息，请参阅[安装 Azure PowerShell](/powershell/azure/install-az-ps)。

> [!IMPORTANT]
> 尽管 Az.PostgreSql PowerShell 模块为预览版，但必须使用以下命令从 Az PowerShell 模块单独安装它：`Install-Module -Name Az.PostgreSql -AllowPrerelease`。
> Az.PostgreSql PowerShell 模块正式版推出后，它会包含在将来的 Az PowerShell 模块发行版中，并在 Azure Cloud Shell 中原生提供。

如果这是你第一次使用 Azure Database for PostgreSQL 服务，必须注册 Microsoft.DBforPostgreSQL 资源提供程序。

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.DBforPostgreSQL
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

如果有多个 Azure 订阅，请选择应当计费的资源所在的相应订阅。 使用 [Set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet 选择特定的订阅 ID。

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>创建资源组

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet 创建 [Azure 资源组](../azure-resource-manager/management/overview.md)。 资源组是在其中以组的形式部署和管理 Azure 资源的逻辑容器。

以下示例在“美国西部”区域中创建名为“myresourcegroup” 的资源组。

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroup -Location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>创建 Azure Database for PostgreSQL 服务器

使用 `New-AzPostgreSqlServer` cmdlet 创建 Azure Database for PostgreSQL 服务器。 一个服务器可以管理多个数据库。 通常，每个项目或每个用户使用一个单独的数据库。

以下示例使用服务器管理员登录名 myadmin 在“美国西部”区域中的“myresourcegroup”资源组内创建名为“mydemoserver”的 PostgreSQL 服务器   。 此服务器是常规用途定价层中的第 5 代服务器，其中启用了 2 个 vCore 和异地冗余备份。 记下示例的第一行中使用的密码，因为这是 PostgreSQL 服务器管理员帐户的密码。

> [!TIP]
> 服务器名称映射到 DNS 名称，必须在 Azure 中全局唯一。

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

Sku 参数值遵循 pricing-tier\_compute-generation\_vCores 约定，如以下示例所示 。

- `-Sku B_Gen5_1` 映射到基本、第 5 代和 1 个 vCore。 此选项是可用的最小 SKU。
- `-Sku GP_Gen5_32` 映射到常规用途、第 5 层和 32 个 vCore。
- `-Sku MO_Gen5_2` 映射到内存优化、第 5 层和 2 个 vCore。

有关各区域和层级的有效 Sku 值的信息，请参阅 [Azure Database for PostgreSQL 定价层](./concepts-pricing-tiers.md)。

如果轻量级计算和 I/O 足以满足工作负载要求，请考虑使用基本定价层。

> [!IMPORTANT]
> 在基本定价层中创建的服务器以后无法扩展到常规用途或内存优化层级，并且无法异地复制。

## <a name="configure-a-firewall-rule"></a>配置防火墙规则

使用 `New-AzPostgreSqlFirewallRule` cmdlet 创建 Azure Database for PostgreSQL 服务器级防火墙规则。 服务器级防火墙规则允许外部应用程序（如 `psql` 命令行工具或 PostgreSQL Workbench）穿过 Azure Database for PostgreSQL 服务防火墙连接到服务器。

以下示例创建名为 AllowMyIP 的防火墙规则，该规则允许从特定的 IP 地址 (192.168.0.1) 进行连接。 替换与要从中进行连接的位置相对应的 IP 地址或 IP 地址范围。

```azurepowershell-interactive
New-AzPostgreSqlFirewallRule -Name AllowMyIP -ResourceGroupName myresourcegroup -ServerName mydemoserver -StartIPAddress 192.168.0.1 -EndIPAddress 192.168.0.1
```

> [!NOTE]
> 连接到 Azure Database for PostgreSQL 时，经端口 5432 进行通信。 如果尝试从企业网络内部进行连接，则可能不允许经端口 5432 的出站流量。 这种情况下，只有在 IT 部门打开了端口 5432 时，才能连接到服务器。

## <a name="get-the-connection-information"></a>获取连接信息

若要连接到服务器，需要提供主机信息和访问凭据。 使用以下示例来确定连接信息。 记下 FullyQualifiedDomainName 和 AdministratorLogin 的值。

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Select-Object -Property FullyQualifiedDomainName, AdministratorLogin
```

```Output
FullyQualifiedDomainName                    AdministratorLogin
------------------------                    ------------------
mydemoserver.postgresql.database.azure.com       myadmin
```

## <a name="connect-to-postgresql-database-using-psql"></a>使用 psql 连接到 PostgreSQL 数据库

如果客户端计算机已安装 PostgreSQL，则可以使用 [psql](https://www.postgresql.org/docs/current/static/app-psql.html) 的本地实例连接到 Azure PostgreSQL 服务器。 还可以通过在本文的代码示例中选择“试用”按钮，在 Azure Cloud Shell 中访问预装版本的 `psql` 命令行工具。 访问 Azure Cloud Shell 的其他方式包括：在 Azure 门户右上角的工具栏上选择“>_ ” 按钮，或访问 [shell.azure.com](https://shell.azure.com/)。

1. 使用 `psql` 命令行实用工具连接到 Azure PostgreSQL 服务器。

   ```azurepowershell-interactive
   psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
   ```

   例如，以下命令使用访问凭据连接到 PostgreSQL 服务器 `mydemoserver.postgres.database.azure.com` 上名为“postgres”的默认数据库。 提示输入密码时，输入之前选择的 `<server_admin_password>`。

   ```azurepowershell-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > 如果更喜欢使用 URL 路径连接到 Postgres，则 URL 会使用 `%40` 对用户名中的 @ 符号进行编码。 例如，psql 的连接字符串将是 `psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres`

1. 连接到服务器后，在出现提示时创建空数据库。

   ```sql
   CREATE DATABASE mypgsqldb;
   ```

1. 出现提示时，请执行以下命令，将连接切换到新建的数据库 mypgsqldb：

   ```sql
   \c mypgsqldb
   ```

## <a name="create-tables-in-the-database"></a>在数据库中创建表

现已介绍了如何连接 Azure Database for PostgreSQL 数据库，接下来可以完成一些基本任务。

首先，创建表并加载一些数据。 创建一个存储清单信息的表。

```sql
CREATE TABLE inventory (
  id serial PRIMARY KEY,
  name VARCHAR(50),
  quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>将数据加载到表

表格创建好后，可向其插入一些数据。 在打开的命令提示窗口中，运行以下查询来插入几行数据。

```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150);
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

现已将两行示例数据加载到了之前创建的表中。

## <a name="query-and-update-the-data-in-the-tables"></a>查询和更新表中的数据

执行以下查询，从数据库表中检索信息。

```sql
SELECT * FROM inventory;
```

还可以更新表中的数据。

```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

检索数据时行也会相应进行更新。

```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>将数据库还原到以前的时间点

可以将服务器还原到以前的某个时间点。 将还原的数据复制到新服务器，现有服务器保持不变。 例如，如果意外删除了某个表，可以还原到删除时的时间点。 然后可以从服务器的已还原副本中检索缺少的表和数据。

若要还原服务器，请使用 `Restore-AzPostgreSqlServer` PowerShell cmdlet。

### <a name="run-the-restore-command"></a>运行还原命令

若要还原服务器，请在 PowerShell 中运行以下示例。

```azurepowershell-interactive
$restorePointInTime = (Get-Date).AddMinutes(-10)
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzPostgreSqlServer -Name mydemoserver-restored -ResourceGroupName myresourcegroup -RestorePointInTime $restorePointInTime -UsePointInTimeRestore
```

将服务器还原到以前的某个时间点时，会创建新服务器。 原始服务器及其从指定时间点开始创建的数据库会复制到新服务器。

还原的服务器的位置值和定价层值与原始服务器保持相同。

还原过程完成后，找到新服务器，验证数据是否已按预期还原。 新服务器具有相同的服务器管理员登录名和密码，该登录名和密码在开始还原时对现有服务器有效。 可以从新服务器的“概述”页更改密码。

还原期间创建的新服务器没有原始服务器上存在的 VNet 服务终结点。 必须单独为新服务器设置这些规则。 将从原始服务器还原防火墙规则。

## <a name="clean-up-resources"></a>清理资源

在前面的步骤中，你已在服务器组中创建了 Azure 资源。 如果你认为以后不需要这些资源，请删除该服务器组。 在服务器组的“概述”页中，按“删除”按钮   。 弹出页面上出现提示时，请确认服务器组的名称，然后单击最后一个“删除”按钮  。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [如何使用 PowerShell 来备份和还原 Azure Database for PostgreSQL 服务器](howto-restore-server-powershell.md)
