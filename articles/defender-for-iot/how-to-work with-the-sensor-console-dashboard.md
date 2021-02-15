---
title: 使用传感器控制台仪表板
description: 可以通过仪表板快速查看网络的安全状态。 它在时间线上提供对整个系统的威胁的高级概述以及相关设备的相关信息。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 11/03/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: c9afc22cd123a782c9ee0247952c78c72ee916c0
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509063"
---
# <a name="the-dashboard"></a>仪表板

可以通过仪表板快速查看网络的安全状态。 它在时间线上提供对整个系统的威胁的高级概述以及相关设备的相关信息，包括：

- 不同严重性级别的警报：

- 严重

- 主要

- 次要

- 警告

- 页面中心中的两个指示器显示 (PPS) 的每秒数据包数，以及 (UA) 的未确认警报。 **PPS** 是系统每秒确认的数据包数。 **UA** 是尚未确认的警报数。

- 未确认的警报的列表及其说明。

- 带有警报说明的时间线。

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/dashboard-alert-screen-v2.png" alt-text="仪表板":::

## <a name="viewing-the-latest-alerts"></a>查看最新警报

在页面中心 (UA) 仪表的未确认警报指示此类警报的数目。 若要查看警报列表，请在 "仪表板" 页底部选择 " **更多警报** "，或者在侧菜单中选择 " **警报** "。

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unhandled-alerts-list.png" alt-text="未确认警报":::

## <a name="status-boxes"></a>状态框

本部分介绍了每个状态框。

| 状态框和仪表 | 说明 |
| -------------- | -------------- |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/critical-alert-status-box-v2.png" alt-text="严重警报"::: | **关键警报** -页面顶部中间的框指示关键警报的数目。 选择此框以显示有关时间线上的警报以及仪表下的列表的说明（如果有）。                              |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/major-alert-status-box-v2.png" alt-text="主要警报"::: | **主要警报** -页面右上角的框指示了主要警报的数目。 选择此框以显示有关时间线上的警报以及仪表下的列表的说明（如果有）。                                     |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/minor-alert-status-box-v2.png" alt-text="次要警报"::: | **次警报** -页面左下方的框指示次要警报的数目。 选择此框以显示有关时间线上的警报以及仪表下的列表的说明（如果有）。                                   |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/warnings-alert-status-box-v2.png" alt-text="警告警报"::: | **警告性警报** -页面底部中间的框指示警告警报的数目。 选择此框以显示有关时间线上的警报以及仪表下的列表的说明（如果有）。                             |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/all-alert-status-box-v2.png" alt-text="所有警报"::: | **所有警报** -该页右下角的框指示关键、主要、次要和警告警报的总数。 选择此框以显示有关时间线上的警报以及仪表下的列表的说明（如果有）。 |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/packets-per-second-gauge-v2.png" alt-text="每秒数据包数"::: | **每秒数据包数 (pps)** -PPS 指标是网络性能的指标。 |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unacknowledged-events-gauge-v2.png" alt-text=" (UA) 未确认的事件 "::: | 未 **确认事件**-此指标指示未确认的事件数。

## <a name="using-the-timeline"></a>使用时间线

这些警报沿垂直时间线显示，其中包括日期和时间信息。

时间线以图形方式显示：

- 严重警报

- 主要警报

- 次要警报

- 警告警报

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/timeline-of-events.png" alt-text="时间线图":::

## <a name="viewing-alerts"></a>查看警报

选择警报框底部的向下箭头 **V** 以显示警报条目和设备信息。

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/extended-alert-screen.png" alt-text="警报条目和设备信息":::

- 选择设备以显示 "物理" 模式图。 会突出显示这些设备。

- 单击警报框中的任意位置，以显示有关警报的其他详细信息。 将显示如下所示的弹出窗口

- 选择 " :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/excel-icon.png" alt-text="Excel"::: " 以导出有关警报的 CSV 文件。

- 仅限管理员和安全分析人员-选择 " :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/approve-all-icon.png" alt-text="全部确认"::: " 以 **确认所有** 关联的警报。

- 选择 " :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/pdf-icon.png" alt-text="pdf":::"，将警报报告下载为 PDF 文件。

- 选择 " :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/pin-icon.png" alt-text="固定":::"，固定或取消固定警报。 选择 "锁定" 会将其添加到 "**警报**" 屏幕上的 "**固定警报**" 窗口。

- 选择 " :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/download-icon.png" alt-text="下载"::: "，通过下载包含网络协议分析的相关 PCAP 文件来调查警报。

- 选择 " :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/cloud-download-icon.png" alt-text="云"::: " 以下载相关的已筛选 PCAP 文件，该文件仅包含与警报相关的数据包，从而减少输出文件大小并允许进行更集中的分析。 可以使用 [Wireshark](https://www.wireshark.org/)查看它。

- 选择 " :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/navigate-icon.png" alt-text="导航"::: " 可导航到请求警报时的事件时间线。 这允许你评估特定警报可能发生的其他事件。

- 仅限管理员和安全分析师-将警报的状态从 "未确认" 更改为 "已确认"。 选择 "了解" 以批准检测到的活动。

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unauthorized-internet-connectivity-detection-v3.png" alt-text="检测到未经授权的 Internet 连接":::

## <a name="see-also"></a>另请参阅

[处理传感器的警报](how-to-work-with-alerts-on-your-sensor.md)
