---
title: 将 Citrix WAF 数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何使用 Citrix WAF 数据连接器将其日志提取到 Azure Sentinel 中。 在工作簿中查看 Citrix WAF 数据，创建警报，并改进调查。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2020
ms.author: yelevin
ms.openlocfilehash: 475e04ad3dd8c7cc51d6345b4b51dd68cff5b597
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93102821"
---
# <a name="connect-your-citrix-waf-to-azure-sentinel"></a>将 Citrix WAF 连接到 Azure Sentinel

> [!IMPORTANT]
> Azure Sentinel 中的 Citrix Web 应用程序防火墙 (WAF) 数据连接器目前为公共预览版。 此功能在提供时没有服务级别协议。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文介绍如何将 Citrix Web 应用程序防火墙 (WAF) 设备连接到 Azure Sentinel。 通过 Citrix WAF 数据连接器，可以轻松地通过 Azure Sentinel 连接 Citrix WAF 日志、查看仪表板、创建自定义警报和改进调查。 通过将 Citrix WAF CEF 日志连接到 Azure Sentinel，你可以利用每个日志的搜索和相关、警报和威胁情报扩充。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="forward-citrix-waf-logs-to-the-syslog-agent"></a>将 Citrix WAF 日志转发到 Syslog 代理  

Citrix WAF 将以 CEF 格式将 Syslog 消息发送到基于 Linux 的日志转发服务器 (运行 rsyslog 或 Syslog-ng) 并在其上安装 Log Analytics 代理，这会将日志转发到 Azure Sentinel 工作区。

1. 如果没有这样的日志转发服务器，请参阅 [这些说明](connect-cef-agent.md) 以启动并运行。

1. 按照 Citrix 提供的说明 [配置 WAF](https://support.citrix.com/article/CTX234174)， [配置 CEF 日志记录](https://support.citrix.com/article/CTX136146)，并 [配置将日志发送到日志转发器](https://docs.citrix.com/en-us/citrix-adc/13/system/audit-logging/configuring-audit-logging.html)。 请确保将日志发送到日志转发器计算机的 IP 地址上的 TCP 端口514。

1. 验证连接并使用 [这些说明](connect-cef-verify.md)验证数据引入。 可能需要长达20分钟的时间，日志才会开始出现在 Log Analytics 中。

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据将显示在 " **日志** " 的 " *CommonSecurityLog* " 表中的 " **Azure Sentinel** " 部分下。

若要在 Log Analytics 中查询 Citrix WAF 日志，请 `CommonSecurityLog` 在 "查询" 窗口顶部输入。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Citrix WAF 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。