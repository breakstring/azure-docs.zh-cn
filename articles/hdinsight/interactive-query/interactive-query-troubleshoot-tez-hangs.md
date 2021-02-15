---
title: Apache Tez 应用程序在 Azure HDInsight 中挂起
description: Apache Tez 应用程序在 Azure HDInsight 中挂起
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/09/2019
ms.openlocfilehash: 56c68c26ae953034283031e2427b7a4afadee94e
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98935929"
---
# <a name="scenario-apache-tez-application-hangs-in-azure-hdinsight"></a>方案：Apache Tez 应用程序在 Azure HDInsight 中挂起

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

提交 Apache Hive 作业后，"Tez" 视图中的作业状态为 "正在运行"，但未显示任何进度

## <a name="cause"></a>原因

提交的作业太多；长 Yarn 队列。

## <a name="resolution"></a>解决方法

纵向扩展群集，或者只是等到 Yarn 队列排空。

默认情况下，`yarn.scheduler.capacity.maximum-applications` 控制正在运行或挂起的应用程序的最大数量，默认为 `10000`。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 联系 [@AzureSupport](https://twitter.com/azuresupport)，这是用于改进客户体验的官方 Microsoft Azure 帐户。 它可以将 Azure 社区成员连接到适当的资源，为他们提供解答、支持和专家建议。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”  ，或打开“帮助 + 支持”  中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](../../azure-portal/supportability/how-to-create-azure-support-request.md)。 Microsoft Azure 订阅中带有对订阅管理和计费支持的访问权限，技术支持通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供。