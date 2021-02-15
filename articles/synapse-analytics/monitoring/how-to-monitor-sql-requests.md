---
title: 如何在 Synapse Studio 中监视 SQL 请求
description: 了解如何使用 Synapse Studio 监视 SQL 请求。
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 7296fec91decaf1bd80829f9f3a743a518fbb7a7
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466112"
---
# <a name="use-synapse-studio-to-monitor-your-sql-requests"></a>使用 Synapse Studio 监视你的 SQL 请求

通过 Synapse Studio，可以在工作区中的 SQL 池上运行 SQL 脚本。

本文介绍如何监视 SQL 请求，使你能够关注正在运行的请求的状态，并发现历史请求的详细信息。

## <a name="access-sql-requests-list"></a>访问 SQL 请求列表

若要查看工作区中的 SQL 请求列表，请先 [打开 Synapse Studio](https://web.azuresynapse.net/) ，然后选择工作区。

![登录到工作区](./media/common/login-workspace.png)

打开工作区后，请选择左侧的 " **监视** " 部分。

![选择监视中心](./media/common/left-nav.png)

选择 " **sql 请求** " 以查看 sql 请求的列表。

 ![选择 SQL 请求](./media/how-to-monitor-sql-requests/monitor-hub-nav-sql-requests.png)

## <a name="select-a-sql-pool"></a>选择 SQL 池

默认情况下，在此视图中显示内置无服务器 SQL 池的 SQL 请求历史记录。 你可以选择一个专用 SQL 池来查看该池的 SQL 请求历史记录。

![选择 SQL 池](./media/how-to-monitor-sql-requests/select-pool.png)

## <a name="filter-your-sql-requests"></a>筛选 SQL 请求

你可以将 SQL 请求列表筛选为你感兴趣的请求。 使用屏幕顶部的筛选器，可以指定要筛选的字段。

例如，你可以筛选视图以仅查看由提交的 SQL 请求 `maria@contoso.com` ：

![示例筛选器](./media/how-to-monitor-sql-requests/filter-example.png)

## <a name="view-details-about-a-specific-sql-request"></a>查看有关特定 SQL 请求的详细信息

若要查看有关某个 SQL 请求的详细信息，请打开 SQL 请求以导航到详细信息视图。 对于在专用 SQL 池上运行的复杂请求，可以监视进度。

## <a name="next-steps"></a>后续步骤

有关监视管道运行的详细信息，请参阅 [在 Synapse Studio 中运行的监视管道](how-to-monitor-pipeline-runs.md) 一文。 

有关监视 Apache Spark 应用程序的详细信息，请参阅在 [Synapse Studio 中监视 Apache Spark 应用程序](how-to-monitor-spark-applications.md) 一文。