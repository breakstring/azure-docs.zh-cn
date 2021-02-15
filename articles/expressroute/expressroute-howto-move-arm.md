---
title: Azure ExpressRoute：将经典线路移到资源管理器
description: 本页面介绍如何使用 PowerShell 将经典线路移动到 Resource Manager 部署模型。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/25/2019
ms.author: duau
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 460ea446fc6dfc43e81a1a57bbba032a61f3a72d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90532539"
---
# <a name="move-expressroute-circuits-from-classic-to-resource-manager-deployment-model-using-powershell"></a>使用 PowerShell 将 ExpressRoute 线路从经典部署模型转移到资源管理器部署模型

要将 ExpressRoute 线路同时用于经典部署模型和 Resource Manager 部署模型，必须将该线路移动到 Resource Manager 部署模型中。 以下部分可帮助使用 PowerShell 转移线路。

## <a name="before-you-begin"></a>准备阶段

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

* 验证是否已在本地计算机上安装经典模块和 Az Azure PowerShell 模块。 有关详细信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/)。
* 在开始配置之前，请务必查看[先决条件](expressroute-prerequisites.md)、[路由要求](expressroute-routing.md)和[工作流](expressroute-workflows.md)。
* 查看[将 ExpressRoute 线路从经典部署模型转移到资源管理器部署模型](expressroute-move.md)中提供的信息。 请确保对限制和局限性有全面的了解。
* 验证线路在经典部署模型中可完全正常运行。
* 确保拥有一个在 Resource Manager 部署模型中创建的资源组。

## <a name="move-an-expressroute-circuit"></a>移动 ExpressRoute 线路

### <a name="step-1-gather-circuit-details-from-the-classic-deployment-model"></a>步骤 1：从经典部署模型收集线路详细信息

登录 Azure 经典环境并收集服务密钥。

1. 登录到 Azure 帐户。

   ```powershell
   Add-AzureAccount
   ```

2. 选择相应的 Azure 订阅。

   ```powershell
   Select-AzureSubscription "<Enter Subscription Name here>"
   ```

3. 为 Azure 和 ExpressRoute 导入 PowerShell 模块。

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
   ```

4. 使用下面的 cmdlet 获取所有 ExpressRoute 线路的服务密钥。 检索密钥后，请复制要移动到 Resource Manager 部署模型的线路的 **服务密钥** 。

   ```powershell
   Get-AzureDedicatedCircuit
   ```

### <a name="step-2-sign-in-and-create-a-resource-group"></a>步骤 2：登录并创建资源组

登录 Resource Manager 环境并创建新的资源组。

1. 登录 Azure Resource Manager 环境。

   ```powershell
   Connect-AzAccount
   ```

2. 选择相应的 Azure 订阅。

   ```powershell
   Get-AzSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzSubscription
   ```

3. 如果还没有资源组，请修改下面的片段，创建新的资源组。

   ```powershell
   New-AzResourceGroup -Name "DemoRG" -Location "West US"
   ```

### <a name="step-3-move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>步骤 3：将 ExpressRoute 线路转移到 Resource Manager 部署模型

现在，可以将 ExpressRoute 线路从经典部署模型移动到 Resource Manager 部署模型。 在继续下之前，请先查看[将 ExpressRoute 线路从经典部署模型移动到 Resource Manager 部署模型](expressroute-move.md)中提供的信息。

若要移动线路，请修改并运行以下代码片段：

```powershell
Move-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"
```

在经典模式下，ExpressRoute 线路没有绑定到区域的概念。 但是，在资源管理器中，每个资源都需要映射到 Azure 区域。 从技术上来讲，Move-AzExpressRouteCircuit cmdlet 中指定的区域可以是任何区域。 对组织来说，建议选择一个最能代表对等位置的区域。

> [!NOTE]
> * 将经典 ExpressRoute 线路转移到资源管理器部署模型之后，默认情况下，它将有权访问经典模型和资源管理器部署模型。
> * 上一个 cmdlet 中列出的新名称将用于对资源进行寻址。 线路实质上已重命名。

## <a name="modify-circuit-access"></a>修改线路访问权限

### <a name="to-enable-expressroute-circuit-access-for-both-deployment-models"></a>为两种部署模型启用 ExpressRoute 线路访问权限

你可以为在资源管理器部署模型中创建的 ExpressRoute 线路启用对经典部署模型的访问。 运行以下 cmdlet 启用对两种部署模型的访问权限：

1. 获取线路详细信息。

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. 将“允许经典操作”设置为 TRUE。

   ```powershell
   $ckt.AllowClassicOperations = $true
   ```

3. 更新线路。 成功完成此操作后，可以在经典部署模型中查看线路。

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

4. 运行以下 cmdlet 获取 ExpressRoute 线路的详细信息。 服务密钥必须已列出。

   ```powershell
   get-azurededicatedcircuit
   ```

5. 现在，可以使用适用于经典 VNet 的经典部署模型命令以及适用于 Resource Manager VNet 的 Resource Manager 命令来管理到 ExpressRoute 线路的链接。 以下文章可帮助管理 ExpressRoute 线路的链接：

    * [在 Resource Manager 部署模型中将虚拟网络链接到 ExpressRoute 线路](expressroute-howto-linkvnet-arm.md)
    * [在经典部署模型中将虚拟网络链接到 ExpressRoute 线路](expressroute-howto-linkvnet-classic.md)

### <a name="to-disable-expressroute-circuit-access-to-the-classic-deployment-model"></a>禁止 ExpressRoute 线路访问经典部署模型

运行以下 cmdlet 可禁止访问经典部署模型。

1. 获取 ExpressRoute 线路的详细信息。

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. 将“允许经典操作”设置为 FALSE。

   ```powershell
   $ckt.AllowClassicOperations = $false
   ```

3. 更新线路。 成功完成此操作后，将无法在经典部署模型中查看线路。

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

## <a name="next-steps"></a>后续步骤

* [创建和修改 ExpressRoute 线路的路由](expressroute-howto-routing-arm.md)
* [将虚拟网络链接到 ExpressRoute 线路](expressroute-howto-linkvnet-arm.md)
