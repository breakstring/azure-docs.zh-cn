---
title: 查看 Azure Maps API 使用情况指标 |Microsoft Azure 映射
description: 了解如何查看 Azure Maps API 使用情况指标，例如请求总数、错误总数和可用性。 请参阅如何筛选数据和拆分结果。
author: anastasia-ms
ms.author: v-stharr
ms.date: 08/06/2018
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: c9b732bd25e7ef8aa084c98d5b059d422f86a4b0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96003508"
---
# <a name="view-azure-maps-api-usage-metrics"></a>查看 Azure Maps API 使用情况指标

本文介绍如何在 [Azure 门户](https://portal.azure.com)中查看 Azure Maps 帐户的 API 使用情况指标。 指标以便捷的图形形式显示，可自定义时间段。

## <a name="view-metric-snapshot"></a>查看指标快照

可在 Maps 帐户的“概述”页中查看部分常见指标。 当前，它可显示所选时间段的“请求总数”、“错误总数”和“可用性”。

![Azure Maps 使用情况指标概述](media/how-to-view-api-usage/portal-overview.png)

如果需要针对特定分析自定义这些图，请继续了解下一部分内容。

## <a name="view-detailed-metrics"></a>查看详细指标

1. 在[门户](https://portal.azure.com)中登录到 Azure 订阅。

2. 单击左侧的“所有资源”菜单项，然后导航到“Azure Maps 帐户”。

3. 打开 Maps 帐户后，单击左侧的“指标”菜单。

4. 在 " **度量值** " 窗格上，选择下列选项之一：

   1. 可用性 - 显示一段时间内 API 可用性的平均值。
   2. 使用情况 - 显示帐户的使用量计算情况。

      ![Azure Maps 使用情况指标 "窗格](media/how-to-view-api-usage/portal-metrics.png)

5. 接下来，可通过单击“过去 24 小时(自动)”来选择“时间范围”。 默认情况下，时间范围设置为 24 小时。 单击后，您将看到所有可选择的时间范围。 可选择“时间粒度”，然后在同一下拉菜单中选择显示“本地”或“GMT”时间。 单击“应用”  。

    ![Azure Maps 度量值时间范围](media/how-to-view-api-usage/time-range.png)

6. 添加度量值后，可以从与该度量值相关的属性中 **添加筛选器** 。 然后，选择要在关系图上反射的属性的值。

    ![Azure Maps 使用情况指标筛选器](media/how-to-view-api-usage/filter.png)

7. 此外，还可以根据所选的指标属性，对指标“应用拆分”。 它允许将关系图拆分为该属性的每个值的多个关系图。 在下图中，每个图的颜色对应图底部所示的属性值。

    ![Azure Maps 使用情况指标拆分](media/how-to-view-api-usage/splitting.png)

8. 此外，还可以在同一图中观察多个指标，只需单击顶部的“添加指标”按钮即可。

## <a name="next-steps"></a>后续步骤

了解要跟踪其使用情况的 Azure Maps API 的详细信息：
> [!div class="nextstepaction"] 
> [Azure Maps Web SDK 操作方法](how-to-use-map-control.md)

> [!div class="nextstepaction"] 
> [Azure Maps Android SDK 操作方法](how-to-use-android-map-control-library.md)

> [!div class="nextstepaction"]
> [Azure Maps REST API 文档](/rest/api/maps)