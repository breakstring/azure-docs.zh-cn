---
title: 语音助手常见问题
titleSuffix: Azure Cognitive Services
description: 获取有关使用自定义命令或直接连线语音通道的语音助手最常见的问题的解答。
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: 4b0bbb982ed48dc052b1a15514ad36b1d69b62b5
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98599419"
---
# <a name="voice-assistants-frequently-asked-questions"></a>语音助手常见问题

如果在本文档中找不到问题的答案，请查看 [其他支持选项](../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext%253fcontext%253d%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)。

## <a name="general"></a>常规

**问：什么是语音助手？**

**答：** 与 Cortana 一样，语音助手是一种解决方案，可侦听用户的口头最谈话，分析这些最谈话的内容，以确定其含义，执行一个或多个操作来响应查询文本的意图，并为用户提供经常包含口述组件的响应。 这是与系统交互的 "语音" 的 "语音" 体验。 语音助手作者使用语音 SDK 中的来创建设备上应用程序 `DialogServiceConnector` ，以便与使用 [自定义命令](custom-commands.md) 或机器人框架的 [直接线路语音](direct-line-speech.md) 通道创建的助手通信。 这些助手可以使用自定义关键字、自定义语音和自定义语音，为你的品牌或产品提供定制体验。

**问：我是否应使用自定义命令或直接语音语音？区别是什么？**

**答：** [自定义命令](custom-commands.md) 是一组较低复杂性的工具，可以轻松创建和承载非常适合于任务完成方案的助手。 [直接线路语音](direct-line-speech.md) 提供功能更丰富且更复杂的功能，可实现强大的对话方案。 有关详细信息，请参阅 [助手解决方案的比较](voice-assistants.md#choosing-an-assistant-solution) 。

**问：如何开始使用？**

**答：** 开始创建自定义命令 (预览) 应用程序或基本机器人框架机器人的最佳方式。

- [) 应用程序 (预览创建自定义命令](./quickstart-custom-commands-application.md)
- [创建基本机器人框架机器人](/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
- [将机器人连接到直接连线语音通道](/azure/bot-service/bot-service-channel-connect-directlinespeech)

## <a name="debugging"></a>调试

**问：我的频道机密在哪里？**

**答：** 如果你使用了直接行语音的预览版本或正在阅读相关文档，则可能需要在 "语音通道注册" 页上找到密钥。 `DialogServiceConfig`语音 SDK 中的版本1.7 工厂方法 `FromBotSecret` 还需要此值。

最新版本的直接连线语音简化了从设备联系机器人的过程。 在 "通道注册" 页上，顶部的下拉箭头会将你的直接线路语音频道注册与语音资源相关联。 关联后，v2.0 Speech SDK 包含 `BotFrameworkConfig::FromSubscription` 工厂方法，该方法会将配置 `DialogServiceConnector` 为与你的订阅关联的机器人联系。

如果仍要将客户端应用程序从版本1.7 升级到5v， `DialogServiceConfig::FromBotSecret` 可以继续使用其通道机密参数的非空、非 null 值（例如，使用的上一个机密）。 使用与较新的频道注册相关联的语音订阅时，将直接忽略此功能。 请注意，该值 _必须_ 为非 null 和非空，因为在服务端关联之前，将在设备上检查这些值。

有关更详细的指南，请参阅 [教程部分](tutorial-voice-enable-your-bot-speech-sdk.md#register-the-direct-line-speech-channel) ，该部分介绍了渠道注册。

**问：在连接时收到401错误，无任何工作。我知道我的语音订阅密钥有效。这是怎么回事？**

**答：** 在 Azure 门户上管理你的订阅时，请确保使用的是 **语音** 资源 (CognitiveServicesSpeechServices "语音" ) ，而 _不_ 是 CognitiveServicesAllInOne "所有认知服务" ) 的 **认知服务** (资源。 另外，请查看 [语音助手的语音服务区域支持](regions.md#voice-assistants)。

![正确订阅直接行语音](media/voice-assistants/faq-supported-subscription.png "兼容的语音订阅示例")

**问：我从我的中取回了识别文本 `DialogServiceConnector` ，但我看到了 "1011" 错误，而不是我的机器人。为什么?**

**答：** 此错误表示助手和语音助手服务之间存在通信问题。

- 对于自定义命令，请确保已发布自定义命令应用程序
- 对于直接线路语音，请确保已将 [机器人连接到直接线路语音频道](/azure/bot-service/bot-service-channel-connect-directlinespeech)，并向机器人 ([添加了流式处理协议支持](/azure/bot-service/directline-speech-bot)) ，并检查机器人是否响应来自通道的传入请求。

**问：此代码仍不起作用，并且/或者在使用时，我收到了不同的错误 `DialogServiceConnector` 。我该怎么办？**

**答：** 基于文件的日志记录提供了更详细的信息，可帮助加速支持请求。 若要启用此功能，请参阅 [如何使用文件日志记录](how-to-use-logging.md)。

## <a name="next-steps"></a>后续步骤

- [故障排除](troubleshooting.md)
- [发行说明](releasenotes.md)