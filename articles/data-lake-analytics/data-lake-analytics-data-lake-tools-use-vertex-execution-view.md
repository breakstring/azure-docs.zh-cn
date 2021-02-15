---
title: Visual Studio Data Lake 工具中的顶点执行视图
description: 本文介绍如何使用顶点执行视图测试 Data Lake Analytics 作业。
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 10/13/2016
ms.openlocfilehash: 3fba7bdaa5db1d812fbcd479e5f1eab50c8d1032
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/20/2020
ms.locfileid: "92215852"
---
# <a name="use-the-vertex-execution-view-in-data-lake-tools-for-visual-studio"></a>在用于 Visual Studio 的 Data Lake 工具中使用顶点执行视图
了解如何使用顶点执行视图测试 Data Lake Analytics 作业。


## <a name="open-the-vertex-execution-view"></a>打开顶点执行视图
在用于 Visual Studio 的 Data Lake 工具中打开 U-SQL 作业。 在左下角单击“顶点执行视图”****。 系统可能提示先加载配置文件，此操作可能需要一些时间，具体取决于网络连接状况。

![显示 Data Lake Analytics 工具顶点执行视图的屏幕截图](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-open-vertex-execution-view.png)

## <a name="understand-vertex-execution-view"></a>了解顶点执行视图
顶点执行视图由三个部分组成：

![显示顶点执行视图的屏幕截图，其中突出显示了 "顶点选择器" 和中心顶部和底部。](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view.png)

可在左边的“顶点选择器”**** 按功能选择顶点（例如：前 10 个数据读取，或按阶段选择）。 其中一个最常用筛选器是查看“关键路径上的顶点”****。 “关键路径”**** 是 U-SQL 作业顶点中最长的链。 了解关键路径有助于通过检查哪个顶点耗时最长来优化作业。
  
![屏幕截图，显示 "顶点执行视图" 顶部中间窗格，其中显示 "所有顶点的运行状态"。](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane2.png)

顶部中心窗格显示“所有顶点的运行状态”****。
  
![屏幕截图，显示 "顶点执行视图" 底端中间窗格，其中显示了有关每个顶点的信息。](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane3.png)

底部中心窗格显示每个顶点的信息：
* 进程名称：顶点实例的名称。 它由 StageName|VertexName|VertexRunInstance 中的不同部分构成。 例如，SV7_Split[62].v1 顶点表示阶段 SV7_Split 中顶点数 62 的第二个运行实例（.v1，从 0 开始索引）。
* 读取/写入的总数据：此顶点读取/写入的数据。
* 状态/退出状态：顶点结束时的最终状态。
* 退出代码/失败类型：顶点失败时出现的错误。
* 创建原因：为何创建顶点。
* 资源延迟/进程延迟/PN 队列延迟：顶点等待资源、处理数据和在队列中等待所需的时间。
* 进程/创建者 GUID：当前运行顶点或其创建者的 GUID。
* 版本：运行中顶点的第 N 个实例（系统可能因故障转移、计算冗余等多种原因安排顶点的新实例）
* 版本创建时间。
* 进程创建开始时间/进程排队时间/进程开始时间/进程完成时间：顶点进程开始创建的时间；顶点进程开始排队的时间；特定顶点进程开始时间；特定顶点完成时间。

## <a name="next-steps"></a>后续步骤
* 若要记录诊断信息，请参阅[访问 Azure Data Lake Analytics 的诊断日志](data-lake-analytics-diagnostic-logs.md)
* 若要查看更复杂的查询，请参阅 [使用 Azure Data Lake Analytics 分析网站日志](data-lake-analytics-analyze-weblogs.md)。
* 若要查看作业详细信息，请参阅[对 Azure Data Lake Analytics 作业使用作业浏览器和作业视图](data-lake-analytics-data-lake-tools-view-jobs.md)
