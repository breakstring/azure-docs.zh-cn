---
title: PowerShell - 将外部用户添加到 Azure 开发测试实验室中的实验室
description: 本文提供 Azure PowerShell 脚本，用于将外部用户添加到 Azure 开发测试实验室中的实验室。
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 76fbb5e4d7f5db39a0aadba9098ebf1064b3cda5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022388"
---
# <a name="use-powershell-to-add-an-external-user-to-a-lab-in-azure-devtest-labs"></a>使用 PowerShell 将外部用户添加到 Azure 开发测试实验室中的实验室

此示例 PowerShell 脚本将外部用户添加到 Azure 开发测试实验室中的实验室。 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>先决条件
* 实验室。 此脚本要求拥有现有的实验室。 

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-external-user-to-lab/add-external-user-to-custom-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令： 

| 命令 | 注释 |
|---|---|
| [Get-AzADUser](/powershell/module/az.resources/get-azaduser) | 从 Azure active directory 重试用户对象。 |
| [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) | 在指定范围内将指定的角色分配给指定的主体。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](/powershell/)。

可以在 [Azure 实验室服务 PowerShell 示例](../samples-powershell.md)中找到其他 Azure 实验室服务 PowerShell 脚本示例。
