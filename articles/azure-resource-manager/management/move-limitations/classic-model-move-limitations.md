---
title: 移动 Azure 经典部署资源
description: 使用 Azure 资源管理器将经典部署资源移到新的资源组或订阅。
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 78b9769a31fa0c96c12e18d05cb9c484aa52a1d5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "75485281"
---
# <a name="move-guidance-for-classic-deployment-model-resources"></a>针对经典部署模型资源的移动指南

移动通过经典模型部署的资源时，其步骤各不相同，具体取决于是在订阅内移动资源，还是将资源移到新的订阅。

## <a name="move-in-the-same-subscription"></a>在同一订阅中移动

在同一订阅内将资源从一个资源组移到另一个资源组时存在以下限制：

* 不能移动虚拟网络（经典）。
* 虚拟机（经典）必须与云服务一起移动。
* 移动云服务时，必须移动其所有虚拟机。
* 一次只能移动一项云服务。
* 一次只能移动一个存储帐户（经典）。
* 存储帐户（经典）与虚拟机或云服务不能在同一操作中移动。

若要将经典资源移到同一订阅内的新资源组，请通过门户、Azure PowerShell、Azure CLI 或 REST API 使用[标准移动操作](../move-resource-group-and-subscription.md)。 使用的操作应与移动 Resource Manager 资源时所用的操作相同。

## <a name="move-across-subscriptions"></a>跨订阅移动

将资源移到新订阅时存在以下限制：

* 必须在同一操作中移动订阅中的所有经典资源。
* 目标订阅不得包含任何其他经典资源。
* 只能通过独立的适用于经典移动的 REST API 来请求移动。 将经典资源移到新订阅时，不能使用标准的资源管理器移动命令。

要将经典资源移动到新订阅，请使用特定于经典资源的 REST 操作。 若要使用 REST，请执行以下步骤：

1. 检查源订阅是否可以参与跨订阅移动。 使用以下操作：

   ```HTTP
   POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     在请求正文中包含以下内容：

   ```json
   {
    "role": "source"
   }
   ```

     验证操作的响应采用以下格式：

   ```json
   {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
   }
   ```

1. 检查目标订阅是否可以参与跨订阅移动。 使用以下操作：

   ```HTTP
   POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     在请求正文中包含以下内容：

   ```json
   {
    "role": "target"
   }
   ```

     响应的格式与源订阅验证相同。
1. 如果两个订阅都通过了验证，可使用以下操作将所有经典资源从一个订阅移到另一个订阅：

   ```HTTP
   POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
   ```

    在请求正文中包含以下内容：

   ```json
   {
    "target": "/subscriptions/{target-subscription-id}"
   }
   ```

运行该操作可能需要几分钟。

## <a name="next-steps"></a>后续步骤

如果移动经典资源时遇到问题，请与[支持部门](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)联系。

有关移动资源的命令，请参阅[将资源移动至新资源组或订阅](../move-resource-group-and-subscription.md)。
