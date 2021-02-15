---
title: 使用 Visual Studio 监视和管理 Azure 流分析
description: 本文介绍如何使用 Visual Studio 监视和管理 Azure 流分析作业。
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: e0db101e47a5ec8eb88d3b999058e7c8521f0ff9
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020275"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-visual-studio"></a>使用 Visual Studio 监视和管理流分析作业

本文演示如何在 Visual Studio 中监视流分析作业。 针对 Visual Studio 的 Azure 流分析工具可提供类似于 Azure 门户的监视体验，而无需离开 IDE。 将作业从 Script.asaql 提交到 Azure 后即可立即开始监视作业，或者可以监视现有作业，无需考虑它们的创建方式 。 

## <a name="job-summary"></a>作业摘要

“作业摘要”和“作业指标”提供作业快照 。 可一目了然地了解作业状态和事件信息。]

<img src="./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-job-summary-metrics.png" alt="Stream Analytics job summary and job metrics" width="300px"/> 


## <a name="job-metrics"></a>作业指标

可以折叠“作业摘要”并单击“作业指标”选项卡，查看含有重要指标的关系图 。 选中和取消选中指标类型，将其添加到关系图或从图中删除。

![Visual Studio 中的流分析指标](./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-vs-metrics.png)


## <a name="error-monitoring"></a>错误监视

此外，还可以通过单击“错误”选项卡监视错误。

![Visual Studio 中的流分析错误](./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-vs-errors.png)


## <a name="get-support"></a>获取支持
如需进一步的帮助，请参阅[ Azure 流分析的 Microsoft 问答问题页面](/answers/topics/azure-stream-analytics.html)。 

## <a name="next-steps"></a>后续步骤
* [Azure 流分析简介](stream-analytics-introduction.md)
* [使用 Visual Studio 创建 Azure 流分析作业](stream-analytics-quick-create-vs.md)
* [安装针对 Visual Studio 的 Azure 流分析工具](stream-analytics-tools-for-visual-studio-install.md)