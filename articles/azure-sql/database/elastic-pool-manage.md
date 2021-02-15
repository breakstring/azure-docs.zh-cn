---
title: 管理弹性池
description: 使用 Azure 门户、PowerShell、Azure CLI、Transact-SQL (T-SQL) 和 Rest API 创建和管理 Azure SQL 数据库弹性池。
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein
ms.date: 03/12/2019
ms.custom: seoapril2019 sqldbrb=1, devx-track-azurecli
ms.openlocfilehash: 9c9af6e3bc3dfd798f4b3f0cad9319aa573c425d
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96455988"
---
# <a name="manage-elastic-pools-in-azure-sql-database"></a>在 Azure SQL 数据库中管理弹性池
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

使用弹性池，可以确定弹性池处理其数据库工作负荷所需的资源量，以及每个共用数据库的资源量。

## <a name="azure-portal"></a>Azure 门户

可在一个位置找到所有池设置：“配置池”边栏选项卡。 若要转到该位置，请在 Azure 门户中找到所需的弹性池，然后在边栏选项卡顶部，或者在左侧资源菜单中单击“配置池”。

在此处，可以进行以下任意更改组合，并将所有这些更改保存到一个批处理文件中：

1. 更改池的服务层级
2. 缩放性能（DTU 或 vCore）和存储
3. 在池中添加或删除数据库
4. 为池中的数据库设置最小（保证）和最大性能限制
5. 检查成本摘要，以查看做出新选择后帐单发生的任何更改

![弹性池配置边栏选项卡](./media/elastic-pool-manage/configure-pool.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure 资源管理器模块仍受 Azure SQL 数据库的支持，但所有未来的开发都是针对 Az.Sql 模块的。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。

若要使用 Azure PowerShell 创建并管理 SQL 数据库弹性池和共用数据库，请使用以下 PowerShell cmdlet。 如果需要安装或升级 PowerShell，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 若要创建和管理弹性池的服务器，请参阅[创建和管理服务器](logical-servers.md)。 若要创建和管理防火墙规则，请参阅[使用 PowerShell 创建和管理防火墙规则](firewall-configure.md#use-powershell-to-manage-server-level-ip-firewall-rules)。

> [!TIP]
> 有关 PowerShell 示例脚本，请参阅[使用 PowerShell 创建弹性池、在池之间移动数据库以及将数据库移出池](scripts/move-database-between-elastic-pools-powershell.md)和[使用 PowerShell 监视和缩放 Azure SQL 数据库中的 SQL 弹性池](scripts/monitor-and-scale-pool-powershell.md)。
>

| Cmdlet | 说明 |
| --- | --- |
|[New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool)|创建弹性池。|
|[Get-AzSqlElasticPool](/powershell/module/az.sql/get-azsqlelasticpool)|获取弹性池及其属性值。|
|[Set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool)|修改弹性池的属性。例如，使用“StorageMB”属性修改弹性池的最大存储。|
|[Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool)|删除弹性池。|
|[Get-AzSqlElasticPoolActivity](/powershell/module/az.sql/get-azsqlelasticpoolactivity)|获取弹性池的运行状态|
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|在现有池中创建新数据库或将其创建为单一数据库。 |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|获取一个或多个数据库。|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|设置数据库的属性，将现有数据库移入、移出弹性池或在其之间移动。|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|删除数据库。|

> [!TIP]
> 使用门户或每次只能创建一个单一数据库的 PowerShell cmdlet 在弹性池中创建多个数据库可能需要一段时间。 若要自动创建到弹性池中，请参阅 [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae)。

## <a name="azure-cli"></a>Azure CLI

若要使用 [Azure CLI](/cli/azure) 创建和管理 SQL 数据库弹性池，请使用下面的 [Azure CLI SQL 数据库](/cli/azure/sql/db)命令。 使用 [Cloud Shell](../../cloud-shell/overview.md) 在浏览器中运行 CLI，或者在 macOS、Linux 或 Windows 上[安装](/cli/azure/install-azure-cli)它。

> [!TIP]
> 有关 Azure CLI 示例脚本，请参阅[使用 CLI 移动 SQL 弹性池中 SQL 数据库中的数据库](scripts/move-database-between-elastic-pools-cli.md)和[使用 Azure CLI 缩放 Azure SQL 数据库中的 SQL 弹性池](scripts/scale-pool-cli.md)。
>

| Cmdlet | 说明 |
| --- | --- |
|[az sql elastic-pool create](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-create)|创建弹性池。|
|[az sql elastic-pool list](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list)|返回服务器中弹性池的列表。|
|[az sql elastic-pool list-dbs](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-dbs)|返回弹性池中数据库的列表。|
|[az sql elastic-pool list-editions](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-editions)|还包括可用的池 DTU 设置、存储限制和每数据库设置。 为了降低冗长，默认情况下会隐藏附加的存储限制以及每数据库设置。|
|[az sql elastic-pool update](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)|更新弹性池。|
|[az sql elastic-pool delete](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-delete)|删除弹性池。|

## <a name="transact-sql-t-sql"></a>Transact-SQL (T-SQL)

若要使用 Transact-SQL 在现有弹性池内创建和移动数据库或者返回有关 SQL 数据库弹性池的信息，请使用以下 T-SQL 命令。 可以使用 Azure 门户、[SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio)、[Visual Studio Code](https://code.visualstudio.com/docs) 或可以连接到服务器并传递 Transact-SQL 命令的任何其他程序来发出这些命令。 若要使用 T-SQL 创建和管理防火墙规则，请参阅[使用 Transact-SQL 管理防火墙规则](firewall-configure.md#use-transact-sql-to-manage-ip-firewall-rules)。

> [!IMPORTANT]
> 无法使用 Transact-SQL 创建、更新或删除 Azure SQL 数据库弹性池。 可以在弹性池中添加或删除数据库，并且可以使用 DMV 返回有关现有弹性池的信息。
>

| 命令 | 说明 |
| --- | --- |
|[CREATE DATABASE（Azure SQL 数据库）](/sql/t-sql/statements/create-database-azure-sql-database)|在现有池中创建新数据库或将其创建为单一数据库。 必须连接到 master 数据库，才能新建数据库。|
| [ALTER DATABASE（Azure SQL 数据库）](/sql/t-sql/statements/alter-database-azure-sql-database) |将数据库移入、移出弹性池或在其之间移动。|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|删除数据库。|
|[sys.elastic_pool_resource_stats（Azure SQL 数据库）](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|返回服务器中所有弹性池的资源使用率统计信息。 对于每个弹性池，报告窗口每 15 秒就会提供一行（每分钟四行）。 这包括池中所有数据库的 CPU、IO、日志和存储消耗以及并发的请求/会话利用率。|
|[sys.database_service_objectives（Azure SQL 数据库）](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|为 SQL 数据库或 Azure Synapse 分析中的数据库返回 service 层) 、服务目标 (定价层) 和弹性池名称（如果有）的 (版本。 如果登录到服务器中的 master 数据库，则将返回有关所有数据库的信息。 对于 Azure Synapse Analytics，必须连接到 master 数据库。|

## <a name="rest-api"></a>REST API

若要创建和管理 SQL 数据库弹性池和共用数据库，请使用这些 REST API 请求。

| 命令 | 说明 |
| --- | --- |
|[弹性池 - 创建或更新](/rest/api/sql/elasticpools/createorupdate)|创建新弹性池或更新现有的弹性池。|
|[弹性池 - 删除](/rest/api/sql/elasticpools/delete)|删除弹性池。|
|[弹性池 - 获取](/rest/api/sql/elasticpools/get)|获取弹性池。|
|[弹性池 - 按服务器列出](/rest/api/sql/elasticpools/listbyserver)|返回服务器中弹性池的列表。|
|[弹性池 - 更新](/rest/api/sql/elasticpools/listbyserver)|更新现有的弹性池。|
|[弹性池活动](/rest/api/sql/elasticpoolactivities)|返回弹性池活动。|
|[弹性池数据库活动](/rest/api/sql/elasticpooldatabaseactivities)|返回弹性池内数据库上的活动。|
|[数据库 - 创建或更新](/rest/api/sql/databases/createorupdate)|创建新数据库或更新现有数据库。|
|[数据库 - 获取](/rest/api/sql/databases/get)|获取数据库。|
|[数据库 - 按弹性池列出](/rest/api/sql/databases/listbyelasticpool)|返回弹性池中数据库的列表。|
|[数据库 - 按服务器列出](/rest/api/sql/databases/listbyserver)|返回服务器中的数据库列表。|
|[数据库 - 更新](/rest/api/sql/databases/update)|更新现有的数据库。|

## <a name="next-steps"></a>后续步骤

* 若要深入了解如何通过弹性池设计 SaaS 应用程序的模式，请参阅 [具有 Azure SQL 数据库的多租户 SaaS 应用程序的设计模式](saas-tenancy-app-design-patterns.md)。
* 有关使用弹性池的 SaaS 教程，请参阅 [Wingtip SaaS 应用程序简介](saas-dbpertenant-wingtip-app-overview.md)。