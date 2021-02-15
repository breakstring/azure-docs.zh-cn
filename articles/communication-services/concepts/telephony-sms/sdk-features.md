---
title: Azure 通信服短信客户端库概述
titleSuffix: An Azure Communication Services concept document
description: 提供短信客户端库及其产品/服务的概述。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 6f0b5325ff95d2c2f34572239967151986621bcb
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96937179"
---
# <a name="sms-client-library-overview"></a>短信客户端概述

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]
[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Azure 通信服务短信客户端库可用于向应用程序添加短信。

## <a name="sms-client-library-capabilities"></a>短信客户端库功能

下表列出了客户端库中当前可用的一组功能。

| 功能组 | 功能                                                                            | JS  | Java | .NET | Python |
| ----------------- | ------------------------------------------------------------------------------------- | --- | ---- | ---- | ------ |
| 核心功能 | 发送和接收短信 </br> 支持 Unicode 表情符号                        | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | 接收发送的消息的传送报告                                            | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | 所有字符集（语言/unicode 支持）                                         | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | 支持长消息（最多 2048 个字符）                                           | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | 长消息的自动串联                                                   | ✔️   | ✔️    | ✔️    | ✔️      |
| 事件            | 使用事件网格配置 Webhook 以接收入站消息和传送报告 | ✔️   | ✔️    | ✔️    | ✔️      |
| 电话号码      | 免费号码                                                                     | ✔️   | ✔️    | ✔️    | ✔️      |
| 监管        | 选择退出处理                                                                      | ✔️   | ✔️    | ✔️    | ✔️      |
| 监视        | 监视发送和接收的消息的使用情况                                          | ✔️   | ✔️    | ✔️    | ✔️      |
| PSTN 呼叫      | 向启用短信的免费电话号码添加 PSTN 呼叫功能                    | ✔️   | ✔️    | ✔️    | ✔️      |

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [开始发送短信](../../quickstarts/telephony-sms/send.md)

你可能会对下列文档感兴趣：

- 熟悉常规[短信概念](../telephony-sms/concepts.md)
- 获取支持短信的[电话号码](../../quickstarts/telephony-sms/get-phone-number.md)
- [规划短信解决方案](../telephony-sms/plan-solution.md)
