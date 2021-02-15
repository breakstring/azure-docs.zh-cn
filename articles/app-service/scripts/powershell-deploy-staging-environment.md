---
title: PowerShell：将代码部署到过渡槽位
description: 了解如何使用 Azure PowerShell 自动部署和管理应用服务。 此示例演示如何将代码部署到过渡环境。
tags: azure-service-management
ms.assetid: 27cf0680-c3a9-4a58-9f71-6dec09f6b874
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 1024aa360202b11d0cb1317db359f9c2e661ebb9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89070371"
---
# <a name="create-a-web-app-and-deploy-code-to-a-staging-environment"></a>创建 Web 应用并将代码部署到过渡环境

此示例脚本使用名为“过渡”的附加部署槽在应用服务中创建 Web 应用，并将示例应用部署到“过渡”槽。

必要时，请使用 [Azure PowerShell 指南](/powershell/azure/)中的说明安装 Azure PowerShell，并运行 `Connect-AzAccount` 创建与 Azure 的连接。

## <a name="sample-script"></a>示例脚本

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-deployment-slot/deploy-deployment-slot.ps1?highlight=1 "Create a web app and deploy code to a staging environment")]

## <a name="clean-up-deployment"></a>清理部署 

运行脚本示例后，可以使用以下命令删除资源组、Web 应用以及所有相关资源。

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 创建用于存储所有资源的资源组。 |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | 创建应用服务计划。 |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | 创建 Web 应用。 |
| [Set-AzAppServicePlan](/powershell/module/az.websites/set-azappserviceplan) | 修改应用服务计划以更改其定价层。 |
| [New-AzWebAppSlot](/powershell/module/az.websites/new-azwebappslot) | 为 Web 应用创建部署槽。 |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | 修改资源组中的资源。 |
| [Switch-AzWebAppSlot](/powershell/module/az.websites/switch-azwebappslot) | 将 Web 应用的部署槽交换到生产环境。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/)。

可以在 [Azure PowerShell 示例](../samples-powershell.md)中找到 Azure 应用服务 Web 应用的其他 Azure Powershell 示例。
