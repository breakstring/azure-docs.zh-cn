---
title: 重置失败的线路-ExpressRoute： PowerShell： Azure |Microsoft Docs
description: 本文帮助你重置处于故障状态的 ExpressRoute 线路。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/07/2021
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 84c8275b7a7257530a735e8612047ef42e2f8a7c
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98011333"
---
# <a name="reset-a-failed-expressroute-circuit"></a>重置有故障的 ExpressRoute 线路

当 ExpressRoute 线路上的操作未成功完成时，线路可能进入 "失败" 状态。 本文将帮助你重置失败的 Azure ExpressRoute 线路。

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="reset-a-circuit"></a>重置线路

1. 安装最新版本的 Azure 资源管理器 PowerShell cmdlet。 有关详细信息，请参阅[安装和配置 Azure PowerShell](/powershell/azure/install-az-ps)。

2. 使用提升的权限打开 PowerShell 控制台，并连接到帐户。 使用下面的示例来帮助连接：

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
3. 如果有多个 Azure 订阅，请查看该帐户的订阅。

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
4. 指定要使用的订阅。

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```
5. 运行以下命令可重置处于故障状态的线路：

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

现在，该线路应已恢复正常。 如果线路仍处于故障状态，请向 [Microsoft 支持部门](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)开具支持票证。

## <a name="next-steps"></a>后续步骤

如果仍遇到问题，请与 [Microsoft 支持](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 部门建立支持票证。
