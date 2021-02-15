---
title: 什么是用于 VM 的 Azure Monitor？
description: 概述用于 VM 的 Azure Monitor，它监视 Azure Vm 的运行状况和性能，并自动发现和映射应用程序组件及其依赖项。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/22/2020
ms.openlocfilehash: e5eaf2d7075ca09aeb3cfaa2dfea81fd0f8d65ad
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2020
ms.locfileid: "94685301"
---
# <a name="overview-of-azure-monitor-for-vms"></a>用于 VM 的 Azure Monitor 概述

用于 VM 的 Azure Monitor 监视虚拟机和虚拟机规模集的性能和运行状况，包括其正在运行的进程和其他资源的依赖关系。 它可以通过识别性能瓶颈和网络问题来帮助提供重要应用程序的可预测性能和可用性，还可以帮助你了解问题是否与其他依赖关系相关。

用于 VM 的 Azure Monitor 支持 Windows 和 Linux 操作系统，请执行以下操作：

- Azure 虚拟机
- Azure 虚拟机规模集
- 与 Azure Arc 连接的混合虚拟机
- 本地虚拟机
- 托管在其他云环境中的虚拟机
  

用于 VM 的 Azure Monitor 将其数据存储在 Azure Monitor 日志中，这使得它能够提供强大的聚合和筛选功能，并在一段时间内分析数据趋势。 可以直接从虚拟机的单个 VM 查看此数据，也可以使用 Azure Monitor 提供多个 Vm 的聚合视图。

![Azure 门户中的虚拟机见解透视图](media/vminsights-overview/vminsights-azmon-directvm.png)


## <a name="pricing"></a>定价
用于 VM 的 Azure Monitor 不会直接收费，但你需要为 Log Analytics 工作区中的活动付费。 根据在 [Azure Monitor 定价页](https://azure.microsoft.com/pricing/details/monitor/)上发布的定价，将针对以下内容对用于 VM 的 Azure Monitor 进行计费：

- 从代理引入数据，并将其存储在工作区中。
- 从来宾运行状况收集的运行状况状态 (预览) 
- 基于日志和运行状况数据的警报规则。
- 从警报规则发送的通知。

日志大小因性能计数器的字符串长度而异，并且可能会随分配给 VM 的逻辑磁盘和网络适配器的数量而增大。 如果已在使用服务映射，则将看到的唯一更改是发送到 Azure Monitor 数据类型的其他性能数据 `InsightsMetrics` 。


## <a name="configuring-azure-monitor-for-vms"></a>配置用于 VM 的 Azure Monitor
配置用于 VM 的 Azure Monitor 的步骤如下所示。 请访问每个链接，获取有关每个步骤的详细指导：

- [创建 Log Analytics 工作区。](vminsights-configure-workspace.md#create-log-analytics-workspace)
- [将 VMInsights 解决方案添加到工作区。](vminsights-configure-workspace.md#add-vminsights-solution-to-workspace)
- [在要监视的虚拟机和虚拟机规模集上安装代理。](vminsights-enable-overview.md)



## <a name="next-steps"></a>后续步骤

- 有关为虚拟机启用监视的要求和方法，请参阅 [部署用于 VM 的 Azure Monitor](vminsights-enable-overview.md) 。

