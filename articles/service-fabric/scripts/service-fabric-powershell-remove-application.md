---
title: 从 Powershell 中的群集删除应用程序
description: Azure PowerShell 脚本示例 - 从 Service Fabric 群集中删除应用程序。
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 686afa791df88382e3e5e1b2d233317c36bf1dd6
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791300"
---
# <a name="remove-an-application-from-a-service-fabric-cluster-using-powershell"></a>使用 Powershell 从 Service Fabric 群集中删除应用程序

此示例脚本删除了正在运行的 Service Fabric 应用程序实例，并从群集中取消注册了应用程序类型和版本。  删除应用程序实例时也会删除与该应用程序关联的所有正在运行的服务实例。 根据需要自定义参数。 

必要时，使用 [Service Fabric SDK](../service-fabric-get-started.md) 安装 Service Fabric PowerShell 模块。 

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/service-fabric/remove-application/remove-application.ps1 "Remove an application from a cluster")]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication) | 从群集中删除正在运行的 Service Fabric 应用程序实例。  |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype) | 从群集中注销 Service Fabric 应用程序类型和版本。 |

## <a name="next-steps"></a>后续步骤

有关 Service Fabric PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/service-fabric/overview)。

可以在 [Azure PowerShell 示例](../service-fabric-powershell-samples.md)中找到 Azure Service Fabric 的其他 Powershell 示例。
