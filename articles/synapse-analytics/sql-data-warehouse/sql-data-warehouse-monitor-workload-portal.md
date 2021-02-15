---
title: 监视工作负荷 - Azure 门户
description: 使用 Azure 门户监视 Synapse SQL
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 3334c5ca300b6dbeb0ad055c7dbd2c15e1c5481a
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98677115"
---
# <a name="monitor-workload---azure-portal"></a>监视工作负荷 - Azure 门户

本文介绍如何使用 Azure 门户监视工作负荷。 这包括设置 Azure Monitor 日志，以使用适用于 [Synapse SQL](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/) 的日志分析来调查查询执行和工作负荷趋势。

## <a name="prerequisites"></a>先决条件

- Azure 订阅：如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
- SQL 池：我们将收集 SQL 池的日志。 如果尚未预配 SQL 池，请参阅[创建 SQL 池](./load-data-from-azure-blob-storage-using-copy.md)中的说明。

## <a name="create-a-log-analytics-workspace"></a>创建 Log Analytics 工作区

导航到 Log Analytics 工作区的浏览边栏选项卡并创建工作区

![Log Analytics 工作区](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspaces.png)

![屏幕截图显示 Log Analytics 工作区，可在其中选择“添加”。](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace.png)

![屏幕截图显示 Log Analytics 工作区，可在其中输入值。](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace_2.png)

有关工作区的更多详细信息，请访问以下[文档](../../azure-monitor/learn/quick-create-workspace.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsond#create-a-workspace)。

## <a name="turn-on-resource-logs"></a>启用资源日志

配置诊断设置，以便从 SQL 池发出日志。 日志包含与最常用的性能故障排除 DMV 等效的遥测视图。 目前支持以下视图：

- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

![启用资源日志](./media/sql-data-warehouse-monitor-workload-portal/enable_diagnostic_logs.png)

可将日志发送到 Azure 存储、流分析或 Log Analytics。 本教程选择了“Log Analytics”。

![指定日志](./media/sql-data-warehouse-monitor-workload-portal/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>针对 Log Analytics 运行查询

导航到 Log Analytics 工作区，在其中可执行以下操作：

- 使用日志查询分析日志，并保存查询以便重复使用
- 保存查询以便重复使用
- 创建日志警报
- 将查询结果固定到仪表板

有关日志查询功能的详细信息，请访问以下[文档](/azure/data-explorer/kusto/query/?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json)。

![Log Analytics 工作区编辑器](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_editor.png)

![Log Analytics 工作区查询](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_queries.png)

## <a name="sample-log-queries"></a>示例日志查询

```Kusto
//List all queries
AzureDiagnostics
| where Category contains "ExecRequests"
| project TimeGenerated, StartTime_t, EndTime_t, Status_s, Command_s, ResourceClass_s, duration=datetime_diff('millisecond',EndTime_t, StartTime_t)
```

```Kusto
//Chart the most active resource classes
AzureDiagnostics
| where Category contains "ExecRequests"
| where Status_s == "Completed"
| summarize totalQueries = dcount(RequestId_s) by ResourceClass_s
| render barchart
```

```Kusto
//Count of all queued queries
AzureDiagnostics
| where Category contains "waits"
| where Type_s == "UserConcurrencyResourceType"
| summarize totalQueuedQueries = dcount(RequestId_s)
```

## <a name="next-steps"></a>后续步骤

设置并配置 Azure Monitor 日志后，请[自定义 Azure 仪表板](../../azure-portal/azure-portal-dashboards.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)以便在整个团队中共享。