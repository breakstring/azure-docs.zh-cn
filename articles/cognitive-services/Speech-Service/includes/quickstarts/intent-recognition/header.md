---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: trbye
ms.openlocfilehash: 4725a1a9cf2cb74655a37ac27a0a86f10d7f4bb9
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98052888"
---
在此快速入门中，你将使用[语音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)和语言理解 (LUIS) 服务来识别从麦克风获取的音频数据中的意向。 具体来说，你将使用语音 SDK 来捕获语音，并使用 LUIS 中的预构建域来识别主自动化的意向，比如打开和关闭电灯。 

满足几个先决条件后，通过麦克风识别语音和确定意向只需几个步骤：

> [!div class="checklist"]
>
> * 通过订阅密钥和区域创建 `SpeechConfig` 对象。
> * 使用以上的 `SpeechConfig` 对象创建 `IntentRecognizer` 对象。
> * 使用 `IntentRecognizer` 对象，开始单个言语的识别过程。
> * 检查返回的 `IntentRecognitionResult`。

