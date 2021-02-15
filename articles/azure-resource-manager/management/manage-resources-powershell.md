---
title: 管理资源 - Azure PowerShell
description: 使用 Azure PowerShell 和 Azure 资源管理器管理资源。 演示如何部署和删除资源。
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: ce3b1d9f8d01e722e4ec5078d92ef4148072da25
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185736"
---
# <a name="manage-azure-resources-by-using-azure-powershell"></a>使用 Azure PowerShell 管理 Azure 资源

了解如何将 Azure PowerShell 与 [Azure 资源管理器](overview.md)配合使用来管理 Azure 资源。 若要管理资源组，请参阅[使用 Azure PowerShell 管理 Azure 资源组](manage-resource-groups-powershell.md)。

有关资源管理的其他文章：

- [使用 Azure 门户管理 Azure 资源](manage-resources-portal.md)
- [使用 Azure CLI 管理 Azure 资源](manage-resources-cli.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>将资源部署到现有的资源组

可以使用 Azure PowerShell 直接部署 Azure 资源，也可以部署资源管理器模板来创建 Azure 资源。

### <a name="deploy-a-resource"></a>部署资源

以下脚本创建一个存储帐户。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

# Create the storage account.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
  -Name $storageAccountName `
  -Location $location `
  -SkuName "Standard_LRS"

# Retrieve the context.
$ctx = $storageAccount.Context
```

### <a name="deploy-a-template"></a>部署模板

以下脚本通过部署快速入门模板来创建存储帐户。 有关详细信息，请参阅[快速入门：使用 Visual Studio Code 创建 Azure 资源管理器模板](../templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell)。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -Location $location
```

有关详细信息，请参阅[使用资源管理器模板和 Azure PowerShell 部署资源](../templates/deploy-powershell.md)。

## <a name="deploy-a-resource-group-and-resources"></a>部署资源组和资源

可以创建一个资源组，然后将资源部署到该组。 有关详细信息，请参阅[创建资源组并部署资源](../templates/deploy-to-subscription.md#resource-groups)。

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>将资源部署到多个订阅或资源组

通常情况下，将模板中的所有资源部署到单个资源组。 不过，在某些情况下，你可能希望将一组资源部署在一起但将其放置在不同的资源组或订阅中。 有关详细信息，请参阅[将 Azure 资源部署到多个订阅或资源组](../templates/deploy-to-resource-group.md)。

## <a name="delete-resources"></a>删除资源

以下脚本演示如何删除存储帐户。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Remove-AzStorageAccount -ResourceGroupName $resourceGroupName -AccountName $storageAccountName
```

若要详细了解 Azure 资源管理器如何控制资源的删除，请参阅 [Azure 资源管理器资源组的删除](delete-resource-group.md)。

## <a name="move-resources"></a>移动资源

以下脚本演示如何将存储帐户从一个资源组移到另一个资源组。

```azurepowershell-interactive
$srcResourceGroupName = Read-Host -Prompt "Enter the source Resource Group name"
$destResourceGroupName = Read-Host -Prompt "Enter the destination Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$storageAccount = Get-AzResource -ResourceGroupName $srcResourceGroupName -ResourceName $storageAccountName
Move-AzResource -DestinationResourceGroupName $destResourceGroupName -ResourceId $storageAccount.ResourceId
```

有关详细信息，请参阅[将资源移到新资源组或订阅](move-resource-group-and-subscription.md)。

## <a name="lock-resources"></a>锁定资源

锁定可以防止组织中的其他用户意外删除或修改关键资源，例如 Azure 订阅、资源组或资源。 

以下脚本锁定一个存储帐户，因此无法删除该帐户。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

New-AzResourceLock -LockName LockStorage -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts 
```

以下脚本获取存储帐户的所有锁：

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts
```

以下脚本删除存储帐户的锁：

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$lockId = (Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts).LockId
Remove-AzResourceLock -LockId $lockId
```

更多信息请参阅 [使用 Azure Resource Manager 锁定资源](lock-resources.md)。

## <a name="tag-resources"></a>标记资源

标记有助于按逻辑方式组织资源组和资源。 有关信息，请参阅[使用标记组织 Azure 资源](tag-resources.md#powershell)。

## <a name="manage-access-to-resources"></a>管理对资源的访问

可以通过 [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md) 管理对 Azure 中资源的访问权限。 有关详细信息，请参阅[使用 Azure PowerShell 添加或删除 Azure 角色分配](../../role-based-access-control/role-assignments-powershell.md)。

## <a name="next-steps"></a>后续步骤

- 若要了解 Azure 资源管理器，请参阅 [Azure 资源管理器概述](overview.md)。
- 若要了解资源管理器模板语法，请参阅[了解 Azure 资源管理器模板的结构和语法](../templates/template-syntax.md)。
- 若要了解如何开发模板，请参阅[分步教程](../index.yml)。
- 若要查看 Azure 资源管理器模板架构，请参阅[模板参考](/azure/templates/)。