---
title: 管理读取副本-Azure 门户 Azure Database for MySQL-灵活服务器
description: 了解如何使用 Azure 门户在 Azure Database for MySQL 灵活的服务器中设置和管理读取副本。
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 10/26/2020
ms.openlocfilehash: d33734dc7404e49aed94dffae8644b2bc4386925
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492822"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-flexible-server-using-the-azure-portal"></a>如何使用 Azure 门户在 Azure Database for MySQL 灵活的服务器中创建和管理读取副本

> [!IMPORTANT]
> 读取 Azure Database for MySQL-灵活服务器中的副本处于预览阶段。

在本文中，你将学习如何使用 Azure 门户在 Azure Database for MySQL 灵活的服务器上创建和管理读取副本。

> [!Note]
> 启用了高可用性的服务器上不支持副本。 

## <a name="prerequisites"></a>先决条件

- 将用作源服务器的 [Azure Database for MySQL 服务器灵活服务器](quickstart-create-server-portal.md) 。

## <a name="create-a-read-replica"></a>创建只读副本

> [!IMPORTANT]
> 如果为没有现有副本的源服务器创建副本，源服务器将首先重启，以便为复制做好准备。 请考虑这一点并在非高峰期执行这些操作。

可以使用以下步骤创建只读副本服务器：

1. 登录到 [Azure 门户](https://portal.azure.com/)。

2. 选择要用作源的现有 Azure Database for MySQL 灵活服务器。 此操作将打开“概述”页。

3. 从菜单中的“设置”下，选择“复制”。

4. 选择“添加副本”。

   :::image type="content" source="./media/how-to-read-replica-portal/add-replica.png" alt-text="Azure Database for MySQL - 复制":::

5. 输入副本服务器的名称。

    :::image type="content" source="./media/how-to-read-replica-portal/replica-name.png" alt-text="Azure Database for MySQL - 副本名称":::

6. 选择“确定”以确认创建该副本。

> [!NOTE]
> 将用与源相同的服务器配置创建读取副本。 副本服务器配置在创建后可以更改。 副本服务器始终在与源服务器相同的资源组、相同的位置和订阅中创建。 如果要将副本服务器创建到不同的资源组或不同的订阅，可以在创建后[移动副本服务器](../../azure-resource-manager/management/move-resource-group-and-subscription.md)。 建议副本服务器的配置应保留为等于或大于源的值，以确保副本能够与源保持同步。

一旦创建副本服务器，可以从“复制”边栏选项卡中进行查看。

   [:::image type="content" source="./media/how-to-read-replica-portal/list-replica.png" alt-text="Azure Database for MySQL - 列出副本":::](./media/how-to-read-replica-portal/list-replica.png#lightbox)

## <a name="stop-replication-to-a-replica-server"></a>停止复制到副本服务器

> [!IMPORTANT]
> 停止复制到服务器操作不可逆。 一旦源服务器和副本服务器之间的复制停止，将无法撤消它。 然后，副本服务器将成为独立服务器，并且现在支持读取和写入。 此服务器不能再次成为副本服务器。

若要从 Azure 门户停止源服务器和副本服务器之间的复制，请使用以下步骤：

1. 在 Azure 门户中，选择你的源 Azure Database for MySQL 灵活的服务器。 

2. 从菜单中的“设置”下，选择“复制”。

3. 选择要停止复制的副本服务器。

   [:::image type="content" source="./media/how-to-read-replica-portal/stop-replication-select.png" alt-text="Azure Database for MySQL - 停止复制选择服务器":::](./media/how-to-read-replica-portal/stop-replication-select.png#lightbox)

4. 选择“停止复制”。

   [:::image type="content" source="./media/how-to-read-replica-portal/stop-replication.png" alt-text="Azure Database for MySQL - 停止复制":::](./media/how-to-read-replica-portal/stop-replication.png#lightbox)

5. 通过单击“确定”，确认要停止复制。

   [:::image type="content" source="./media/how-to-read-replica-portal/stop-replication-confirm.png" alt-text="Azure Database for MySQL - 停止复制确认":::](./media/how-to-read-replica-portal/stop-replication-confirm.png#lightbox)

## <a name="delete-a-replica-server"></a>删除副本服务器

若要从 Azure 门户删除只读副本服务器，请使用以下步骤：

1. 在 Azure 门户中，选择你的源 Azure Database for MySQL 灵活的服务器。

2. 从菜单中的“设置”下，选择“复制”。

3. 选择要删除的副本服务器。

   [:::image type="content" source="./media/how-to-read-replica-portal/delete-replica-select.png" alt-text="Azure Database for MySQL - 删除副本选择服务器":::](./media/how-to-read-replica-portal/delete-replica-select.png#lightbox)

4. 选择“删除副本”

   :::image type="content" source="./media/how-to-read-replica-portal/delete-replica.png" alt-text="Azure Database for MySQL - 删除副本":::

5. 键入副本的名称，然后单击“删除”以确认删除副本。  

   :::image type="content" source="./media/how-to-read-replica-portal/delete-replica-confirm.png" alt-text="Azure Database for MySQL - 删除副本确认":::

## <a name="delete-a-source-server"></a>删除源服务器

> [!IMPORTANT]
> 删除源服务器会停止复制到所有副本服务器，并删除源服务器本身。 副本服务器成为现在支持读取和写入的独立服务器。

若要从 Azure 门户删除源服务器，请使用以下步骤：

1. 在 Azure 门户中，选择你的源 Azure Database for MySQL 灵活的服务器。

2. 从“概览”中，选择“删除”。

   [:::image type="content" source="./media/how-to-read-replica-portal/delete-master-overview.png" alt-text="Azure Database for MySQL-删除源":::](./media/how-to-read-replica-portal/delete-master-overview.png#lightbox)

3. 键入源服务器的名称，然后单击“删除”以确认删除源服务器。  

   :::image type="content" source="./media/how-to-read-replica-portal/delete-master-confirm.png" alt-text="Azure Database for MySQL-删除源确认":::

## <a name="monitor-replication"></a>监视复制

1. 在 [Azure 门户](https://portal.azure.com/)中，选择要监视的 Azure Database for MySQL 灵活服务器的副本。

2. 在边栏的“监视”部分，选择“指标”：

3. 从可用指标的下拉列表中选择“复制延迟(秒)”。

   [:::image type="content" source="./media/how-to-read-replica-portal/monitor-select-replication-lag.png" alt-text="选择复制延迟时间":::](./media/how-to-read-replica-portal/monitor-select-replication-lag.png#lightbox)

4. 选择要查看的时间范围。 下图选择 30 分钟的时间范围。

   [:::image type="content" source="./media/how-to-read-replica-portal/monitor-replication-lag-time-range.png" alt-text="选择时间范围":::](./media/how-to-read-replica-portal/monitor-replication-lag-time-range.png#lightbox)

5. 查看所选时间范围的复制延迟时间。 下图显示过去的 30 分钟。

   [:::image type="content" source="./media/how-to-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png" alt-text="选择时间范围“30 分钟”":::](./media/how-to-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png#lightbox)

## <a name="next-steps"></a>后续步骤

- 详细了解[只读副本](concepts-read-replicas.md)