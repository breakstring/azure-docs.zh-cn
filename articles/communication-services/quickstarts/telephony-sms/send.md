---
title: 快速入门 - 如何发送短信
titleSuffix: An Azure Communication Services quickstart
description: 了解如何使用 Azure 通信服务发送短信。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.custom: tracking-python, devx-track-js
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: 60c51de4e4549649c681c961c6ddc1acdb12e698
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659686"
---
# <a name="quickstart-send-an-sms-message"></a>快速入门：发送短信

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

> [!IMPORTANT]
> 可以将短信发送到美国电话号码，也可以从美国电话号码接收短信。 通信服务短信尚不支持其他地理位置的电话号码。
> 有关详细信息，请参阅[规划电话服务和短信解决方案](../../concepts/telephony-sms/plan-solution.md)。

::: zone pivot="programming-language-csharp"
[!INCLUDE [Send SMS with .NET client library](./includes/send-sms-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Send SMS with JavaScript client library](./includes/send-sms-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Send SMS with Python client library](./includes/send-sms-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Send SMS with Java client library](./includes/send-sms-java.md)]
::: zone-end

## <a name="troubleshooting"></a>疑难解答

若要对与短信发送相关的问题进行故障排除，可以[使用事件网格启用短信发送报告](./handle-sms-events.md)，捕获有关发送的详细信息。

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除通信服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。 了解有关[清理资源](../create-communication-resource.md#clean-up-resources)的详细信息。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你了解了如何使用 Azure 通信服务发送短信。

> [!div class="nextstepaction"]
> [订阅短信事件](./handle-sms-events.md)

> [!div class="nextstepaction"]
> [规划 PSTN 解决方案](../../concepts/telephony-sms/plan-solution.md)

> [!div class="nextstepaction"]
> [了解有关短信的详细信息](../../concepts/telephony-sms/concepts.md)