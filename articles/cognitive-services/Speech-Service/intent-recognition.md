---
title: 意向识别概述-语音服务
titleSuffix: Azure Cognitive Services
description: 意向识别允许识别预先定义的用户目标。 本文概述了意向识别服务的优势和功能。
services: cognitive-services
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: v-demjoh
keywords: 意向识别
ms.openlocfilehash: 215b25e440b8cad76e0656e47d32b184edf4ac66
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018695"
---
# <a name="what-is-intent-recognition"></a>什么是意向识别？

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

在本概述中，你将了解意向识别的优点和功能。 认知服务语音 SDK 与语言理解服务 (LUIS) 相集成，以提供意向识别。 意向是用户想要做的某件事：预订航班、查看天气预报或拨打电话。
使用意向识别，应用程序、工具和设备可以根据在 LUIS 中定义的选项来确定用户希望启动或执行的操作。

## <a name="luis-key-required"></a>需要 LUIS 密钥

* LUIS 与语音服务集成，可从语音中识别意向。 不需要语音服务订阅，只需要 LUIS。
* 语音意向识别与 SDK 集成。 可以将 LUIS 密钥用于语音服务。
* 通过语音 SDK 的意向识别 [在 LUIS 支持的区域子集提供](./regions.md#intent-recognition)。

## <a name="get-started"></a>入门

请参阅 [快速入门](quickstarts/intent-recognition.md) ，了解意向识别入门。

## <a name="sample-code"></a>代码示例

意向识别的示例代码：

* [快速入门：使用预构建的家庭自动化应用](../luis/luis-get-started-create-app.md)
* [使用适用于 C# 的语音 SDK 从语音中识别意向](./how-to-recognize-intents-from-speech-csharp.md)
* [在 C 中使用 Unity 的意向识别和其他语音服务#](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/speechrecognizer)
* [使用用于 Python 的语音 SDK 识别意向](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console)
* [在 Windows 上使用适用于 c + + 的语音 SDK 实现意向识别和其他语音服务](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/cpp/windows/console)
* [在 Windows 或 Linux 上使用适用于 Java 的语音 SDK 实现意向识别和其他语音服务](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/jre/console)
* [在 web 浏览器上使用适用于 JavaScript 的语音 SDK 实现意向识别和其他语音服务](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/js/browser)

## <a name="reference-docs"></a>参考文档

* [语音 SDK](./speech-sdk.md)

## <a name="next-steps"></a>后续步骤

* 完成意向识别 [快速入门](quickstarts/intent-recognition.md)
* [免费获取语音服务订阅密钥](overview.md#try-the-speech-service-for-free)
* [获取语音 SDK](speech-sdk.md)