---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 01/04/2021
ms.author: trbye
ms.openlocfilehash: 4ec9c847158c9b8a1160cb6f4510912ed83a9fae
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947641"
---
首先，使用静态函数加载关键字模型文件 `FromFile()` ，该函数将返回 `KeywordRecognitionModel` 。 使用 `.table` 从 Speech Studio 下载的文件的路径。 此外，你可以 `AudioConfig` 使用默认麦克风创建，然后使用音频配置实例化新的 `KeywordRecognizer` 。

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var keywordModel = KeywordRecognitionModel.FromFile("your/path/to/Activate_device.table");
using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using var keywordRecognizer = new KeywordRecognizer(audioConfig);
```

接下来，通过一次调用来运行关键字识别， `RecognizeOnceAsync()` 方法是传递模型对象。 这会启动一个关键字识别会话，此会话将一直持续到识别关键字为止。 因此，通常在多线程应用程序中使用此设计模式，或在可能会无限期等待唤醒字词的使用情况下使用此设计模式。

```csharp
KeywordRecognitionResult result = await keywordRecognizer.RecognizeOnceAsync(keywordModel);
```

> [!NOTE]
> 此处显示的示例使用本地关键字识别，因为它不需要 `SpeechConfig` 用于身份验证上下文的对象，也不会与后端联系。 但是，可以 [使用直接后端连接](../../../tutorial-voice-enable-your-bot-speech-sdk.md#view-the-source-code-that-enables-keyword)来运行关键字识别和验证。

### <a name="continuous-recognition"></a>连续识别

语音 SDK 中的其他类支持通过关键字识别) 语音和意向识别的持续识别 (。 这允许您使用通常用于连续识别的相同代码，并能够引用 `.table` 关键字模型的文件。

对于语音到文本，请遵循 [快速入门](../../../get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=script%2cbrowser%2cwindowsinstall#continuous-recognition) 中所示的相同设计模式来设置持续识别。 然后，将对的调用替换为 `recognizer.StartContinuousRecognitionAsync()` `recognizer.StartKeywordRecognitionAsync(KeywordRecognitionModel)` ，并传递您的 `KeywordRecognitionModel` 对象。 若要使用关键字发现停止连续识别，请使用 `recognizer.StopKeywordRecognitionAsync()` 而不是 `recognizer.StopContinuousRecognitionAsync()` 。

意向识别对和函数使用相同的 [`StartKeywordRecognitionAsync`](/dotnet/api/microsoft.cognitiveservices.speech.intent.intentrecognizer.startkeywordrecognitionasync#Microsoft_CognitiveServices_Speech_Intent_IntentRecognizer_StartKeywordRecognitionAsync_Microsoft_CognitiveServices_Speech_KeywordRecognitionModel_) 模式 [`StopKeywordRecognitionAsync`](/dotnet/api/microsoft.cognitiveservices.speech.intent.intentrecognizer.stopkeywordrecognitionasync#Microsoft_CognitiveServices_Speech_Intent_IntentRecognizer_StopKeywordRecognitionAsync) 。
