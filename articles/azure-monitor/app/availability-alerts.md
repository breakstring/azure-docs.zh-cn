---
title: 使用 Azure Application Insights 设置可用性警报 | Microsoft Docs
description: 了解如何在 Application Insights 中设置 web 测试。 当网站不可用或响应速度缓慢时接收警报。
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/19/2019
ms.reviewer: sdash
ms.openlocfilehash: a0e8540d9488e9cc3756eb09c69349ac90d20b83
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93043374"
---
# <a name="availability-alerts"></a>可用性警报

[Azure Application Insights](./app-insights-overview.md) 将来自全球各地的 Web 请求定期发送到应用程序。 如果应用程序无响应或响应太慢，它可以提醒你。

## <a name="enable-alerts"></a>启用警报

默认情况下，现在会自动启用警报，但为了完全配置警报，首先需要创建可用性测试。

![创建体验](./media/availability-alerts/create-test.png)

> [!NOTE]
>  使用[新的统一警报](../platform/alerts-overview.md)时，必须在警报体验中配置预警规则严重性和[操作组](../platform/action-groups.md)的通知首选项。 如果不执行以下步骤，则只会收到门户内通知。

1. 保存可用性测试后，在详细信息选项卡上单击你刚才所做的测试旁边的省略号。 单击“编辑警报”。

   ![屏幕截图显示从菜单选择的编辑警报。](./media/availability-alerts/edit-alert.png)

2. 设置所需的严重性级别、规则说明，最重要的是设置具有要用于此警报规则的通知首选项的操作组。

   ![屏幕截图显示 "规则管理" 页，可以在其中编辑规则。](./media/availability-alerts/set-action-group.png)

> [!NOTE]
> 通过这种体验创建的可用性警报是基于状态的。 这意味着，当满足警报条件时，如果站点被检测为不可用，会生成一个警报。 如果在下一次评估警报条件时站点仍处于关闭状态，则不会产生新警报。 因此，如果站点关闭了一个小时且已设置电子邮件警报，则你只会在该站点关闭时收到一封电子邮件，然后在该站点重新开放时再收到一封电子邮件。 你不会连续收到提醒该站点仍然不可用的警报。

### <a name="alert-on-x-out-of-y-locations-reporting-failures"></a>当 Y 个位置中有 X 个报告失败时发出警报

创建新的可用性测试时，会在[新的统一警报体验](../platform/alerts-overview.md)中默认启用“Y 个位置中的 X 个”警报规则。 可通过选择“经典”选项或选择禁用该警报规则来选择退出。

> [!NOTE]
> 通过执行上述步骤，将操作组配置为在警报触发时接收通知。 如果不执行此步骤，则在规则触发时只会收到门户内通知。
>

### <a name="alert-on-availability-metrics"></a>根据可用性指标发出警报

使用[新的统一警报](../platform/alerts-overview.md)时，可以根据分段聚合可用性发出警报以及测试持续时间指标：

1. 在指标体验中选择 Application Insights 资源，然后选择可用性指标：

    ![可用性指标选择](./media/availability-alerts/select-metric.png)

2. 从菜单中配置警报选项将转到新体验，可在其中选择特定测试或位置以将警报规则设置为打开。 还可以在此处配置此警报规则的操作组。

### <a name="alert-on-custom-analytics-queries"></a>根据自定义分析查询发出警报

使用[新的统一警报](../platform/alerts-overview.md)时，可以根据[自定义日志查询](../platform/alerts-unified-log.md)发出警报。 借助自定义查询，可以在有助于获得最可靠的可用性问题信号的任意条件下发出警报。 如果使用 TrackAvailability SDK 发送自定义可用性结果，这同样适用。

> [!Tip]
> 可用性数据的指标包括可能通过调用我们的 TrackAvailability SDK 提交的任何自定义可用性结果。 可以使用“根据指标发出警报”支持根据自定义可用性结果发出警报。
>

## <a name="automate-alerts"></a>自动发送警报

若要使用 Azure 资源管理器模板自动执行此过程，请参阅[使用资源管理器模板创建指标警报](../platform/alerts-metric-create-templates.md#template-for-an-availability-test-along-with-a-metric-alert)文档。

## <a name="troubleshooting"></a>故障排除

专用[故障排除文章](troubleshoot-availability.md)。

## <a name="next-steps"></a>后续步骤

* [多步骤 Web 测试](availability-multistep.md)
* [URL ping Web 测试](monitor-web-app-availability.md)

