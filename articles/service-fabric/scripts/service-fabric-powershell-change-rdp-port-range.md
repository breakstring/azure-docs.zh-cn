---
title: Azure PowerShell 脚本示例 - 更改 RDP 端口范围 | Microsoft Docs
description: Azure PowerShell 脚本示例 - 更改已部署群集的 RDP 端口范围。
services: service-fabric
tags: azure-service-management
author: athinanthny
ms.author: atsenthi
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 03/19/2018
ms.openlocfilehash: 750a2100d34e02cac7c613cc6b95160fc348b535
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576360"
---
# <a name="update-the-rdp-port-range-values"></a>更新 RDP 端口范围值

部署群集后，此示例脚本可更改群集节点 VM 上的 RDP 端口范围值。  使用了 Azure PowerShell，使底层 VM 不会重启。  该脚本会获取群集资源组中的 `Microsoft.Network/loadBalancers` 资源，并更新 `inboundNatPools.frontendPortRangeStart` 和 `inboundNatPools.frontendPortRangeEnd` 值。 根据需要自定义参数。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

必要时，请使用 [Azure PowerShell 指南](/powershell/azure/)中的说明安装 Azure PowerShell。

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-port-range/change-rdp-port-range.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | 获取 `Microsoft.Network/loadBalancers` 资源。 |
|[Set-AzResource](/powershell/module/az.resources/set-azresource)|更新 `Microsoft.Network/loadBalancers` 资源。|

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/)。

可以在 [Azure PowerShell 示例](../service-fabric-powershell-samples.md)中找到 Azure Service Fabric 的其他 Azure Powershell 示例。
