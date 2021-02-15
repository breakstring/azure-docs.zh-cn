---
title: PowerShell：连接到 SQL 数据库
description: 了解如何使用 Azure PowerShell 自动部署和管理应用服务。 此示例演示如何将应用连接到 SQL 数据库。
tags: azure-service-management
ms.assetid: 055440a9-fff1-49b2-b964-9c95b364e533
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 3b355a830703b51264d2f4b819d5be7f2627b4e2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89077783"
---
# <a name="connect-an-app-service-app-to-sql-database"></a>将应用服务应用连接到 SQL 数据库

在此方案中，将了解如何创建 Azure SQL 数据库中的数据库和应用服务应用。 然后，将使用应用设置将数据库链接到应用。

必要时，请使用 [Azure PowerShell 指南](/powershell/azure/)中的说明安装 Azure PowerShell，并运行 `Connect-AzAccount` 创建与 Azure 的连接。

## <a name="sample-script"></a>示例脚本

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/connect-to-sql/connect-to-sql.ps1?highlight=13 "Connect an app to SQL Database")]

## <a name="clean-up-deployment"></a>清理部署 

运行脚本示例后，可以使用以下命令删除资源组、应用服务应用以及所有相关资源。

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 创建用于存储所有资源的资源组。 |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | 创建应用服务计划。 |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | 创建应用服务应用。 |
| [New-AzSQLServer](/powershell/module/az.sql/new-azsqlserver) | 创建服务器。 |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | 创建服务器级防火墙规则。 |
| [New-AzSQLDatabase](/powershell/module/az.sql/new-azsqldatabase) | 创建数据库或弹性数据库。 |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | 修改应用服务应用的配置。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/)。

可以在 [Azure PowerShell 示例](../samples-powershell.md)中找到 Azure 应用服务的其他 Azure Powershell 示例。
