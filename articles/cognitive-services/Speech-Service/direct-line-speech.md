---
title: 直拨语音语音服务
titleSuffix: Azure Cognitive Services
description: 概述使用语音助手的语音助手的功能、功能和限制 (SDK) 的语音助手。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: 501a0e954efbdbc9a0396fd94c915efaf0fde9fc
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98601118"
---
# <a name="what-is-direct-line-speech"></a>什么是 Direct Line 语音？

**直接线路语音** 是一种可靠的端到端解决方案，可用于创建灵活、可扩展的语音助手。 它由机器人框架和其直接线路语音通道提供支持，该通道经过优化，可用于与机器人的语音语音交互。

[语音助手](voice-assistants.md) 会倾听用户的响应，并在响应时执行操作，这通常是一回谈话。 他们使用 [语音到文本](speech-to-text.md) 转录用户的语音，然后对文本的自然语言理解采取措施。 此操作通常包含用 [文本到语音功能](text-to-speech.md)生成的助手的语音输出。

直接连线语音提供语音助手的最高级别的自定义和复杂程度。 它适用于使用任务完成或命令和控制使用的开放、自然或混合两种会话方案。 这种高度的灵活性带来了更大的复杂性，并且通过自然语言输入以定义完善的任务为作用域的方案，可能需要考虑使用 [自定义命令](custom-commands.md) 来简化解决方案的体验。

## <a name="getting-started-with-direct-line-speech"></a>直接行语音入门

使用 "直接线路语音" 创建语音助手的第一步是 [获取语音订阅密钥](overview.md#try-the-speech-service-for-free)，创建与该订阅关联的新机器人，并将机器人连接到直接线路语音通道。

   ![直接连线语音业务流程服务流的概念图](media/voice-assistants/overview-directlinespeech.png "语音通道流")

有关使用直行语音创建简单语音助手的完整循序渐进指南，请参阅 [使用语音 SDK 和直接线路语音通道启用机器人的语音教程](tutorial-voice-enable-your-bot-speech-sdk.md)。

我们还提供了快速入门，旨在让你快速运行代码和学习 Api。 此表包含按语言和平台组织的语音助手快速入门列表。

| 快速入门 | 平台 | API 参考 |
|------------|----------|---------------|
| C#、UWP | Windows | [“浏览”](/dotnet/api/microsoft.cognitiveservices.speech) |
| Java | Windows、macOS、Linux | [“浏览”](/java/api/com.microsoft.cognitiveservices.speech) |
| Java | Android | [“浏览”](/java/api/com.microsoft.cognitiveservices.speech) |

## <a name="sample-code"></a>示例代码

GitHub 上提供了用于创建语音助手的示例代码。 这些示例涵盖了用几种常用编程语言连接到助手的客户端应用程序。

* [语音助手示例 (SDK)](https://aka.ms/csspeech/samples/#voice-assistants-quickstarts)
* [教程：通过语音 SDK 使用语音启用助手，C#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="customization"></a>自定义

使用语音服务构建的语音助手可以使用各种自定义选项，这些选项可用于 [语音到文本](speech-to-text.md)、 [文本到语音](text-to-speech.md)和 [自定义关键字选择](./custom-keyword-basics.md)。

> [!NOTE]
> 自定义选项因语言/区域设置而异（请参阅[支持的语言](./language-support.md)）。

"语音助手" 的 "直接行语音" 及其相关功能是 " [虚拟助手解决方案和企业模板](/azure/bot-service/bot-builder-enterprise-template-overview)" 的理想补充。 尽管直接行语音可以与任何兼容机器人配合使用，但这些资源为高质量的对话体验提供了可重用的基线，并为快速入门提供了常见的支持技能和模型。

## <a name="reference-docs"></a>参考文档

* [语音 SDK](./speech-sdk.md)
* [Azure 机器人服务](/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>后续步骤

* [免费获取语音服务订阅密钥](overview.md#try-the-speech-service-for-free)
* [获取语音 SDK](speech-sdk.md)
* [创建和部署基本机器人](/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [获取虚拟助手解决方案和企业模板](https://github.com/Microsoft/AI)
