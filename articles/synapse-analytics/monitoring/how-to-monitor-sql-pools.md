---
title: 如何在 Synapse Studio 中监视 SQL 池
description: 了解如何使用 Synapse Studio 监视 SQL 池。
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 63b63526333435387c3ff5b5c9d5599ec851c1a8
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466115"
---
# <a name="use-synapse-studio-to-monitor-your-sql-pools"></a>使用 Synapse Studio 监视 SQL 池

通过 Synapse Studio，可以在工作区中的 SQL 池上运行 SQL 脚本。

本文介绍如何监视 SQL 池，使你能够关注池的状态和活动。

## <a name="access-sql-pools-list"></a>访问 SQL 池列表

若要查看工作区中的 SQL 池列表，请先 [打开 Synapse Studio](https://web.azuresynapse.net/) ，然后选择工作区。

![登录到工作区](./media/common/login-workspace.png)

打开工作区后，请选择左侧的 " **监视** " 部分。

![选择监视中心](./media/common/left-nav.png)

选择 **sql 池** 以查看 sql 池列表。

 ![选择 SQL 池](./media/how-to-monitor-sql-pools/monitor-hub-nav-sql-pools.png)

## <a name="filter-your-sql-pools"></a>筛选 SQL 池

你可以将 SQL 池的列表筛选为你感兴趣的池。 使用屏幕顶部的筛选器，可以指定要筛选的字段。

例如，你可以筛选视图以便仅查看包含名称 "salesrecords" 的 SQL 池：

![示例筛选器](./media/how-to-monitor-sql-pools/filter-example.png)

## <a name="view-details-about-a-specific-sql-pool"></a>查看有关特定 SQL 池的详细信息

若要查看有关某个 SQL 池的详细信息，请选择 SQL 池以查看详细信息。

![SQL 池详细信息](./media/how-to-monitor-sql-pools/sql-pool-details.png)

## <a name="next-steps"></a>后续步骤

有关监视管道运行的详细信息，请参阅 [在 Synapse Studio 中运行的监视管道](how-to-monitor-pipeline-runs.md) 一文。 

有关监视 SQL 请求的详细信息，请参阅在 [Synapse Studio 中监视 sql 请求](how-to-monitor-sql-requests.md) 一文。