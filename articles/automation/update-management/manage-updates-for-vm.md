---
title: 在 Azure 自动化中管理虚拟机 (VM) 的更新和修补程序
description: 本文介绍如何使用更新管理功能来管理 Azure VM 和非 Azure VM 的更新和修补程序。
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: c86c9049bc0afc81f5dfd8553d2aa98cfd4b1a46
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98915976"
---
# <a name="manage-updates-and-patches-for-your-vms"></a>管理 VM 的更新和修补程序

Azure 自动化更新管理中的软件更新提供一组工具和资源，它们可帮助管理“跟踪软件更新并将其应用于 Azure 和混合云中的计算机”这项复杂任务。 若要维持操作效率、克服安全问题和降低日益增多的网络安全威胁风险，有效的软件更新管理过程是必需的。 但是，由于技术日新月异，并且新的安全威胁不断出现，因此需要始终如一地持续关注有效的软件更新管理。

> [!NOTE]
> 更新管理功能支持部署第一方更新并预先下载这些更新。 要实现此项支持，必须在要更新的系统上进行更改。 请参阅[为 Azure 自动化更新管理配置 Windows 更新设置](configure-wuagent.md)，了解如何在系统上配置这些设置。

尝试管理 VM 的更新之前，请确保已使用下述方法之一在 VM 上启用了更新管理功能：

* [从自动化帐户启用更新管理](enable-from-automation-account.md)
* [通过浏览 Azure 门户启用更新管理](enable-from-portal.md)
* [从 runbook 启用更新管理](enable-from-runbook.md)
* [从 Azure VM 启用更新管理](enable-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>限制部署的范围

更新管理在工作区中使用范围配置来确定要接收更新的计算机。 有关详细信息，请参阅[限制更新管理的部署范围](scope-configuration.md)。

## <a name="compliance-assessment"></a>符合性评估

在将软件更新部署到计算机之前，请查看已启用的计算机的更新符合性评估结果。 对于每个软件更新，系统都会记录其符合性状态，然后在评估完成后，进行收集并批量转发到 Azure Monitor 日志。

在 Windows 计算机上，符合性扫描默认每12小时运行一次，并在15分钟内重新启动 Windows Log Analytics 代理。 然后，将评估数据转发到工作区并刷新 **更新** 表。 在更新安装之前和之后，将执行更新符合性扫描以识别缺少的更新，但不会使用结果来更新表中的评估数据。

请务必查看有关如何 [配置 Windows 更新客户端](configure-wuagent.md) 与更新管理的建议，以避免任何阻止其正确管理的问题。

对于 Linux 计算机，符合性扫描默认情况下每个小时执行一次。 如果适用于 Linux 的 Log Analytics 代理重启，则在重启后 15 分钟内开始符合性扫描。

每台已经过评估的计算机的符合性结果将显示在更新管理中。 最长可能需要30分钟的时间，仪表板才会显示启用了管理的新计算机的更新数据。

请查看[监视软件更新](view-update-assessments.md)，了解如何查看符合性结果。

## <a name="deploy-updates"></a>部署更新

评审符合性结果后，软件更新部署阶段进入到部署软件更新的过程。 若要安装更新，请计划一个遵循你的发布时间和服务窗口的部署。 可选择在部署中包括哪种更新类型。 例如，可包括关键或安全更新，排除更新汇总。

请查看[部署软件更新](deploy-updates.md)，了解如何计划更新部署。

## <a name="review-update-deployments"></a>查看更新部署

部署完成后，请检查执行过程，按计算机或目标组确定更新部署是否成功。 请参阅[查看部署状态](deploy-updates.md#check-deployment-status)，了解如何监视部署状态。

## <a name="next-steps"></a>后续步骤

* 若要了解如何创建警报来就更新部署结果进行通知，请参阅[为更新管理创建警报](configure-alerts.md)。

* 可[查询 Azure Monitor 日志](query-logs.md)来分析更新评估、部署和其他相关管理任务。 它包含预定义的查询，可帮助你入门。