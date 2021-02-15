---
title: include 文件
description: include 文件
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 1c1d438f0322942a1e68c0af74de8d5e2d77c77a
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95553677"
---
可以通过 [Azure CLI](/cli/azure) 和 [Azure PowerShell](/powershell/azure/new-azureps-module-az) 启用和管理 Azure 磁盘加密。 为此，必须在本地安装工具并连接到 Azure 订阅。

### <a name="azure-cli"></a>Azure CLI

[Azure CLI 2.0](/cli/azure) 是用于管理 Azure 资源的命令行工具。 CLI 旨在提高数据查询灵活性、支持非阻塞进程形式的长时间操作，以及简化脚本编写。 可以按照[安装 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 中的步骤在本地安装它。

若要[使用 Azure CLI 登录 Azure 帐户](/cli/azure/authenticate-azure-cli)，请使用 [az login](/cli/azure/reference-index?view=azure-cli-latest#az-login) 命令。

```azurecli
az login
```

若要选择登录到的租户，请使用：
    
```azurecli
az login --tenant <tenant>
```

如果有多个订阅并想要指定其中的一个，请使用 [az account list](/cli/azure/account#az-account-list) 获取订阅列表，然后使用 [az account set](/cli/azure/account#az-account-set) 指定订阅。
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

有关详细信息，请参阅 [Azure CLI 2.0 入门](/cli/azure/get-started-with-azure-cli)。 

### <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell az 模块](/powershell/azure/new-azureps-module-az)提供了一组使用 [Azure 资源管理器](../articles/azure-resource-manager/management/overview.md)模型管理 Azure 资源的 cmdlet。 可以在浏览器中将它与 [Azure Cloud Shell](../articles/cloud-shell/overview.md) 结合使用，也可以按照[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)中的说明操作，将它安装在本地计算机上。 

如果已在本地安装 PowerShell，请确保使用最新版本的 Azure PowerShell SDK 来配置 Azure 磁盘加密。 下载最新版本的 [Azure PowerShell 版本](https://github.com/Azure/azure-powershell/releases)。

若要[使用 Azure PowerShell 登录 Azure 帐户](/powershell/azure/authenticate-azureps?view=azps-2.5.0)，请使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) cmdlet。

```powershell
Connect-AzAccount
```

如果有多个订阅并想要指定一个，请使用 [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) cmdlet 列出这些订阅，然后使用 [Set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) cmdlet：

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

运行 [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) cmdlet 将验证是否选择了正确的订阅。

若要确认已安装 Azure 磁盘加密 cmdlet，请使用 [Get-command](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6) cmdlet：
     
```powershell
Get-command *diskencryption*
```
有关详细信息，请参阅 [Azure PowerShell 入门](/powershell/azure/get-started-azureps)。