---
title: 在最短的停机时间内迁移到 Azure Database for PostgreSQL（单一服务器）
description: 本文介绍如何使用 Azure 数据库迁移服务，在最短的停机时间内将 PostgreSQL 数据库迁移到 Azure Database for PostgreSQL（单一服务器）。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 27da5f1b731b2cdb0604f91f7f9e78b19ee2908b
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2020
ms.locfileid: "92489789"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql---single-server"></a>在最短的停机时间内迁移到 Azure Database for PostgreSQL（单一服务器）
[!INCLUDE[applies-to-postgres-single-flexible-server](includes/applies-to-postgres-single-flexible-server-hyperscale.md)]

可以使用为 [Azure 数据库迁移服务](https://aka.ms/get-dms) (DMS) 新引入的**连续同步功能**以最短停机时间执行 PostgreSQL 到 Azure Database for PostgreSQL 的迁移。 此功能可限制应用程序引起的停机时间量。

## <a name="overview"></a>概述
Azure DMS 将本地内容初始加载到 Azure Database for PostgreSQL，然后在应用程序仍在运行时不断地将任何新事务同步到 Azure。 在目标 Azure 端的数据已与本地端数据保持同步后，可暂停应用程序一段时间（最短停机时间），等待最后一批数据（从停止应用程序时起，直到应用程序实际不可用于接收任何新流量）同步到目标，然后更新连接字符串使其指向 Azure。 完成后，应用程序将在 Azure 上运行！

:::image type="content" source="./media/howto-migrate-online/ContinuousSync.png" alt-text="使用 Azure 数据库迁移服务进行连续同步":::

## <a name="next-steps"></a>后续步骤
- 观看视频[使用 Microsoft Azure 实现应用现代化](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102)，其中包含一个演示，演示如何将 PostgreSQL 应用迁移到 Azure Database for PostgreSQL。
- 请参阅教程[使用 DMS 以联机方式将 PostgreSQL 迁移到 Azure Database for PostgreSQL](../dms/tutorial-postgresql-azure-postgresql-online.md)。