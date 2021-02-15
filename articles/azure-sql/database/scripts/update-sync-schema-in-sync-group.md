---
title: PowerShell：更新 SQL 数据同步的同步架构
description: 用于更新 SQL 数据同步的同步架构的 Azure PowerShell 示例脚本
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 7d346d1ff30c138667749822b258bab4c6a621f4
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792712"
---
# <a name="use-powershell-to-update-the-sync-schema-in-an-existing-sync-group"></a>使用 PowerShell 更新现有同步组中的同步架构
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

此 Azure PowerShell 示例更新现有“SQL 数据同步”同步组中的同步架构。 同步多个表时，此脚本可帮助你有效地更新同步架构。 此示例演示如何使用 **UpdateSyncSchema** 脚本，该脚本在 GitHub 上以 [UpdateSyncSchema.ps1](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/sql-data-sync/UpdateSyncSchema.ps1) 的形式提供。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 PowerShell，则本教程需要 Az PowerShell 1.4.0 或更高版本。 如果需要进行升级，请参阅 [Install Azure PowerShell module](/powershell/azure/install-az-ps)（安装 Azure PowerShell 模块）。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 来创建与 Azure 的连接。

有关 SQL 数据同步的概述，请参阅[使用 Azure SQL 数据同步跨多个云和本地数据库同步数据](../sql-data-sync-data-sql-server-sql-database.md)。

> [!IMPORTANT]
> 目前，SQL 数据同步不支持 Azure SQL 托管实例。

## <a name="examples"></a>示例

### <a name="add-all-tables-to-the-sync-schema"></a>将所有表添加到同步架构

以下示例可刷新数据库架构，并可将中心数据库中的所有有效表添加到同步架构。

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscriptionId> -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> `
    -SyncGroupName <syncGroupName> -RefreshDatabaseSchema $true -AddAllTables $true
```

### <a name="add-and-remove-tables-and-columns"></a>添加和删除表和列

以下示例将 `[dbo].[Table1]` 和 `[dbo].[Table2].[Column1]` 添加到同步架构并删除 `[dbo].[Table3]`。

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscriptionId> -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> `
    -SyncGroupName <syncGroupName> -TablesAndColumnsToAdd "[dbo].[Table1],[dbo].[Table2].[Column1]" -TablesAndColumnsToRemove "[dbo].[Table3]"
```

## <a name="script-parameters"></a>脚本参数

**UpdateSyncSchema** 脚本具有以下参数：

| 参数 | 注释 |
|---|---|
| $subscriptionId | 在其中创建同步组的订阅。 |
| $resourceGroupName | 在其中创建同步组的资源组。|
| $serverName | 中心数据库的服务器名称。|
| $databaseName | 中心数据库名称。 |
| $syncGroupName | 同步组名称。 |
| $memberName | 如果要从同步成员而不是中心数据库加载数据库架构，请指定成员名称。 如果要从中心加载数据库架构，则将此参数保留为空。 |
| $timeoutInSeconds | 该脚本刷新数据库架构时超时。 默认为 900 秒。 |
| $refreshDatabaseSchema | 指定该脚本是否需要刷新数据库架构。 如果数据库架构已从以前的配置更改（例如，如果添加了新表或新列），则需要在重新配置之前刷新架构。 默认值为 false。 |
| $addAllTables | 如果此值为 true，所有有效的表和列都将添加到同步架构。 $TablesAndColumnsToAdd 和 $TablesAndColumnsToRemove 的值将被忽略。 |
| $tablesAndColumnsToAdd | 指定要添加到同步架构的表或列。 每个表名或列名必须与架构名称完全分隔。 例如：`[dbo].[Table1]`、`[dbo].[Table2].[Column1]`。 可以指定多个表名或列名，并将其用逗号 (,) 分隔。 |
| $tablesAndColumnsToRemove | 指定要从同步架构中删除的表或列。 每个表名或列名必须与架构名称完全分隔。 例如：`[dbo].[Table1]`、`[dbo].[Table2].[Column1]`。 可以指定多个表名或列名，并将其用逗号 (,) 分隔。 |

## <a name="script-explanation"></a>脚本说明

**UpdateSyncSchema** 脚本使用以下命令。 表中的每条命令链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [Get-AzSqlSyncGroup](/powershell/module/az.sql/get-azsqlsyncgroup) | 返回有关同步组的信息。 |
| [Update-AzSqlSyncGroup](/powershell/module/az.sql/update-azsqlsyncgroup) | 更新同步组。 |
| [Get-AzSqlSyncMember](/powershell/module/az.sql/get-azsqlsyncmember) | 返回有关同步成员的信息。 |
| [Get-AzSqlSyncSchema](/powershell/module/az.sql/get-azsqlsyncschema) | 返回有关同步架构的信息。 |
| [Update-AzSqlSyncSchema](/powershell/module/az.sql/update-azsqlsyncschema) | 更新同步架构。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/)。

可以在 [Azure SQL 数据库 PowerShell 脚本](../powershell-script-content-guide.md)中找到更多 SQL 数据库 PowerShell 脚本示例。

有关 SQL 数据同步的详细信息，请参阅：

- 概述 - [使用 Azure 中的 SQL 数据同步在 Azure SQL 数据库和 SQL Server 之间同步数据](../sql-data-sync-data-sql-server-sql-database.md)
- 设置数据同步
    - 使用 Azure 门户 - [教程：设置 SQL 数据同步，以在 Azure SQL 数据库和 SQL Server 之间同步数据](../sql-data-sync-sql-server-configure.md)
    - 使用 PowerShell
        -  [使用 PowerShell 在 Azure SQL 数据库中的多个数据库之间同步数据](sql-data-sync-sync-data-between-sql-databases.md)
        -  [使用 PowerShell 在 Azure SQL 数据库和 SQL Server 之间同步数据](sql-data-sync-sync-data-between-azure-onprem.md)
- Data Sync Agent - [Azure 中 SQL 数据同步的 Data Sync Agent](../sql-data-sync-agent-overview.md)
- 最佳做法 - [Azure 中 SQL 数据同步的最佳做法](../sql-data-sync-best-practices.md)
- 监视 - [使用 Azure Monitor 日志监视 SQL 数据同步](../monitor-tune-overview.md)
- 故障排除 - [排查 Azure 中的 SQL 数据同步问题](../sql-data-sync-troubleshoot.md)
- 更新同步架构
    - 使用 Transact-SQL - [在 Azure 中的 SQL 数据同步中自动复制架构更改](../sql-data-sync-update-sync-schema.md)

有关 SQL 数据库的详细信息，请参阅：

- [SQL 数据库概述](../sql-database-paas-overview.md)
- [数据库生命周期管理](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))