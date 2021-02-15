---
title: 配置高可用性-超大规模 (Citus) -Azure Database for PostgreSQL
description: 如何启用或禁用高可用性
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: 46b842994cbcf7efe66d5992c79246d77626e268
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90907398"
---
# <a name="configure-hyperscale-citus-high-availability"></a>配置超大规模 (Citus) 高可用性

Azure Database for PostgreSQL-超大规模 (Citus) 提供高可用性 (HA) 以避免数据库停机。 启用 HA 后，服务器组中的每个节点都将获得备用。 如果原始节点变得不正常，则会升级其备用节点来替换它。

> [!IMPORTANT]
> 由于 HA 增加了组中服务器的数目，因此它也会加倍。

在创建服务器组的过程中，或之后在服务器组的 " **计算 + 存储** " 选项卡中，启用 HA 是可能的 Azure 门户。 在这两种情况下，用户界面的外观类似。 从 "无" 拖动滑块以实现 **高可用性** ：

:::image type="content" source="./media/howto-hyperscale-high-availability/01-ha-slider.png" alt-text="ha 滑块&quot;:::

单击 &quot; **保存** &quot; 按钮以应用您的选择。 启用 HA 可能需要一些时间，因为服务器组预配待机并流式传输数据。

服务器组的 &quot; **概览** &quot; 选项卡将列出所有节点及其待机，以及一个 &quot; **高可用性** " 列，指示是否为每个节点成功启用了 HA。

:::image type="content" source="./media/howto-hyperscale-high-availability/02-ha-column.png" alt-text="ha 滑块&quot;:::

单击 &quot; **保存** &quot; 按钮以应用您的选择。 启用 HA 可能需要一些时间，因为服务器组预配待机并流式传输数据。

服务器组的 &quot; **概览** &quot; 选项卡将列出所有节点及其待机，以及一个 &quot; **高可用性** ":::

### <a name="next-steps"></a>后续步骤

了解有关 [高可用性](concepts-hyperscale-high-availability.md)的详细信息。
