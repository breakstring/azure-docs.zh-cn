---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: e3eac0311b8b24c8c6e1fbf4d8cae55c360a7352
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376763"
---
## <a name="prerequisites"></a>先决条件

在开始之前，请务必：

> [!div class="checklist"]
> * [创建 Azure 语音资源](../../../../overview.md#try-the-speech-service-for-free)
> * [设置开发环境并创建空项目](../../../../quickstarts/setup-platform.md?tabs=windows&pivots=programming-language-cpp)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="add-sample-code"></a>添加示例代码

1. 打开源文件 **helloworld.cpp**。

1. 将所有代码替换为以下片段：
   
   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-file/helloworld/helloworld.cpp#code)]

1. 在同一文件中，将字符串 `YourSubscriptionKey` 替换为你的订阅密钥。

1. 将字符串 `YourServiceRegion` 替换为与订阅关联的[区域](https://aka.ms/speech/sdkregion)中的“区域标识符”。

1. 将 `whatstheweatherlike.wav` 字符串替换为你自己的文件名。

1. 在菜单栏中，选择“文件” > “全部保存”。

> [!NOTE]
> 语音 SDK 将默认使用 en-us 作为语言进行识别。若要了解如何选择源语言，请参阅[指定语音转文本的源语言](../../../../how-to-specify-source-language.md)。

## <a name="build-and-run-the-application"></a>生成并运行应用程序

1. 从菜单栏中，选择“构建” > “构建解决方案”以构建应用程序。 现在，编译代码时应不会提示错误。

1. 选择“调试” > “开始调试”（或按 F5）以启动 helloworld 应用程序。

1. 你的音频文件将传输到语音服务，文件中的第一个言语将转录为文本，该文本将显示在同一窗口中。

   ```text
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>后续步骤

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]