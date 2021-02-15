---
title: include 文件
titleSuffix: Azure
description: include 文件
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e139954e6550e33edb75d01ab9dbec0bba543ea6
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/26/2020
ms.locfileid: "92548040"
---
成功部署对 **等互连** 资源后，可以按照以下步骤进行查看。

1. 中转到 " **资源组** "，并选择在创建对 **等互连** 资源时选择的资源组。 如果资源组太多，请使用 " **筛选器** " 框。

    > [!div class="mx-imgBorder"]
    > ![资源组](../media/setup-direct-get-resourcegroup.png)

1. 选择创建的对 **等互连** 资源。

    > [!div class="mx-imgBorder"]
    > ![显示你创建的对等互连资源的屏幕截图。](../media/setup-direct-get-open.png)

1. **概述** 页显示了高级信息，如下所示。

    > [!div class="mx-imgBorder"]
    > ![对等互连资源概述窗格](../media/setup-exchange-get-overview.png)

1. 在左侧，选择 " **ASN 信息** "，查看在创建 PeerAsn 时提交的信息。

    > [!div class="mx-imgBorder"]
    > ![对等互连资源 ASN 信息](../media/setup-direct-get-asninfo.png)

1. 在左侧，选择 " **连接** "。 在屏幕顶部，可以看到你的 ASN 和 Microsoft 之间的对等互连连接的摘要，以及大都市中不同设施间的连接。 还可以通过在中间窗格中选择 " **连接** "，从 " **概述** " 页访问 "连接摘要"，如下所示。

    > [!div class="mx-imgBorder"]
    > ![对等互连资源连接](../media/setup-exchange-get-connectionssummary.png)

    * **连接状态** 对应于对等互连连接设置的状态。 此字段中显示的状态遵循 [Exchange 对等演练](../walkthrough-exchange-all.md)中所示的状态图。
    * **Ipv4 会话状态** 和 **ipv6 会话状态** 分别对应于 ipv4 和 ipv6 BGP 会话状态。  
    * 选择屏幕顶部的行时，底部的 " **连接** " 部分会显示每个连接的详细信息。 选择箭头以展开 " **配置** "、" **IPv4 地址** " 和 " **IPv6 地址** "。

    > [!div class="mx-imgBorder"]
    > ![突出显示一个展开节的箭头的屏幕截图。](../media/setup-exchange-get-connectionsipv4.png)
