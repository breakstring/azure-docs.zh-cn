---
title: 方案：VNet 的自定义隔离
titleSuffix: Azure Virtual WAN
description: 路由方案 - 阻止所选 VNet 相互联系
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: ca1ee8418bc08d70a031d81a15dc1b4ace2f1a3a
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2020
ms.locfileid: "92461815"
---
# <a name="scenario-custom-isolation-for-vnets"></a>方案：VNet 的自定义隔离

使用虚拟 WAN 虚拟中心路由时，有很多可用方案。 在 VNet 的自定义隔离方案中，目标是防止特定的 VNet 集能够访问其他特定的 VNet 集。 但是，这些 VNet 需要与所有分支（VPN/ER/用户 VPN）通信。 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。

## <a name="design"></a><a name="design"></a>设计

为了确定将会需要多少个路由表，可以构建一个连接矩阵。 对于此方案，它将如下所示，其中每个单元格表示源（行）是否可以与目标（列）通信：

| 源 | 到:| *蓝色 VNet* | *红色 VNet* | *分支*|
|---|---|---|---|---|
| **蓝色 VNet** |   &#8594;|   直接     |           |  直接 |
| **红色 VNet**  |   &#8594;|              |   直接  |  直接 |
| **分支**   |   &#8594;|   直接     |   直接  |  直接 |

上表中的每个单元都说明了虚拟 WAN 连接 (流的 "From" 端，行标题) 与流的 "To" 端 (目标通信，) 中的列标题。 在这种情况下，不存在防火墙或网络虚拟设备，因此通信直接通过虚拟 WAN (，因此) 的表中 "Direct" 一词。

不同行模式的数量将是我们在此方案中需要的路由表的数量。 在本例中，我们将调用三个路由表，**RT_BLUE** 和 **RT_RED** 用于虚拟网络，**Default** 用于分支。 请记住，分支必须始终与 Default 路由表相关联。

分支需要了解来自红色和蓝色 VNet 的前缀，因此，所有 VNet 都需要传播到 Default（除了 **RT_BLUE** 或 **RT_RED** 之外）。 蓝色和红色 VNet 将需要了解分支前缀，因此分支还将同时传播到路由表 **RT_BLUE** 和 **RT_RED**。 这样，最终设计如下：

* 蓝色虚拟网络：
  * 关联的路由表：**RT_BLUE**
  * 传播到路由表：**RT_BLUE** 和 **Default**
* 红色虚拟网络：
  * 关联的路由表：**RT_RED**
  * 传播到路由表：**RT_RED** 和 **Default**
* 分支：
  * 关联的路由表：**默认**
  * 传播到路由表：**RT_BLUE**、**RT_RED** 和 **Default**

> [!NOTE]
> 由于所有分支都需要关联到 Default 路由表，并且要传播到同一组路由表，因此所有分支都将具有相同的连接性配置文件。 换句话说，VNet 的红色/蓝色概念不能应用于分支。

> [!NOTE]
> 如果将虚拟 WAN 部署在多个区域，则需要在每个中心创建 **RT_BLUE** 和 **RT_RED** 路由表，并且需要使用传播标签将每个 VNet 连接中的路由传播到每个虚拟中心的路由表。

有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。

## <a name="workflow"></a><a name="architecture"></a>工作流

**图 1** 中有蓝色和红色 VNet 连接。

* 蓝色连接的 VNet 可以相互联系，还可以访问所有分支 (VPN/ER/P2S) 连接。
* 红色 VNet 可以相互联系，还可以访问所有分支 (VPN/ER/P2S) 连接。

设置路由时，请考虑以下步骤。

1. 在 Azure 门户中创建两个自定义路由表：**RT_BLUE** 和 **RT_RED**。
2. 对于路由表 **RT_BLUE**，对于以下设置：
   * **Association**：选择所有蓝色 VNet。
   * **传播**：对于“分支”，请选择适合分支的选项，这意味着分支 (VPN/ER/P2S) 连接会将路由传播到此路由表。
3. 对于 **RT_RED** 路由表，请针对红色 VNet 和分支 (VPN/ER/P2S) 重复相同的步骤。

这将导致路由配置更改，如下图所示

**图 1**

:::image type="content" source="./media/routing-scenarios/custom-isolation/custom.png" alt-text="图 1":::

## <a name="next-steps"></a>后续步骤

* 有关虚拟 WAN 的详细信息，请参阅[常见问题解答](virtual-wan-faq.md)。
* 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。
