---
title: 发布应用 - LUIS
titleSuffix: Azure Cognitive Services
description: 当完成生成和测试活动 LUIS 应用时，可通过将其发布到终结点使其可用于客户端应用程序。
services: cognitive-services
author: aahill
manager: nitinme
ms.author: aahi
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 01/12/2021
ms.openlocfilehash: 8db0f5fa39c7f489db0e30e98ee2684c74eee7e8
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/13/2021
ms.locfileid: "98180024"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>将活动的已训练应用发布到过渡或生产终结点

当完成生成、训练和测试活动 LUIS 应用的操作时，可将其发布到终结点，使其可供客户端应用程序使用。

## <a name="publishing"></a>发布
1. 登录到 [LUIS 门户](https://www.luis.ai)，选择“订阅”和“创作资源”以查看分配给该创作资源的应用。
1. 在“我的应用”页上选择应用名称以打开应用。
1. 若要发布到终结点，请在右上方的面板中选择“发布”。

    ![导航栏右上角的“发布”按钮](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

1. 选择用于已发布的预测终结点的设置，然后选择“发布”。

    ![选择发布设置，然后选择“发布”按钮](./media/luis-how-to-publish-app/publish-pop-up.png)

### <a name="publishing-slots"></a>发布槽

当显示弹出窗口时选择正确的槽：

* 过渡
* 生产

使用两个发布槽，可以对已发布的终结点使用两个不同版本的应用，或者在两个不同的终结点上使用同一版本。

### <a name="publishing-regions"></a>发布区域

该应用将发布到与 LUIS 预测终结点资源关联的所有区域，这些资源在 LUIS 门户中通过“管理” -> [“Azure 资源”](luis-how-to-azure-subscription.md#assign-a-resource-to-an-app)页添加。

例如，对于在 [www.luis.ai](https://www.luis.ai)上创建的应用，如果在两个区域 **westus** 和 **eastus** 中创建 luis 资源，并将它们作为资源添加到该应用，则会在这两个区域发布该应用。 有关 LUIS 区域的详细信息，请参阅[区域](luis-reference-regions.md)。

> [!TIP]
> 有3种创作区域。 你必须在要向其发布内容的区域中创作。 如果需要将内容发布到所有区域，需要在所有 3 个创作区域中管理创作过程和生成的已定型模型。


## <a name="configuring-publish-settings"></a>配置发布设置

在选择槽后，为以下项配置发布设置：

* 情绪分析
* [拼写更正](luis-tutorial-bing-spellcheck.md)
* 语音启动

发布以后，即可在“发布设置”页的“管理”部分查看这些设置。  可以在每次发布时更改设置。 如果取消发布，则在发布过程中所做的任何更改也会被取消。

### <a name="when-your-app-is-published"></a>当应用发布后

成功发布应用后，浏览器顶部会显示一个表示成功的通知。 通知还包含指向终结点的链接。

如果需要终结点 URL，请选择该链接。 还可以通过在顶部菜单中选择“管理”，然后在左侧菜单中选择“Azure 资源”来访问终结点 URL。

## <a name="sentiment-analysis"></a>情绪分析

<a name="enable-sentiment-analysis"></a>

情绪分析使 LUIS 可以与[文本分析](https://azure.microsoft.com/services/cognitive-services/text-analytics/)集成，以便提供情绪和关键短语分析。

不需提供文本分析密钥，而且系统不会因为此服务向你的 Azure 帐户收费。

情绪数据是一个介于 0 到 1 之间的分数，指示数据的正面情绪（分数接近 1）或负面情绪（分数接近 0）。 `positive`、`neutral` 和 `negative` 的情绪标签针对每个支持的区域性。 目前，仅英语支持情绪标签。

若要详细了解使用情绪分析时的 JSON 终结点响应，请参阅[情绪分析](luis-reference-prebuilt-sentiment.md)

## <a name="spelling-correction"></a>拼写更正

V3 预测 API 现在支持必应拼写检查 API。 可以通过在请求的标头中包含必应搜索资源的键，向应用程序添加拼写检查。 如果已拥有必应资源，可以使用现有 Bing 资源，或 [创建新](https://portal.azure.com/#create/Microsoft.BingSearch) 资源来使用此功能。 

|标头键|标头值|
|--|--|
|`mkt-bing-spell-check-key`|在资源的 " **密钥" 和 "终结点** " 边栏选项卡中找到密钥|

拼写错误的查询的预测输出示例：

```json
{
  "query": "bouk me a fliht to kayro",
  "prediction": {
    "alteredQuery": "book me a flight to cairo",
    "topIntent": "book a flight",
    "intents": {
      "book a flight": {
        "score": 0.9480589
      }
      "None": {
        "score": 0.0332136229
      }
    },
    "entities": {}
  }
}
```

在 LUIS 用户话语预测之前纠正拼写错误。 可以在响应中查看对原始话语所做的任何更改，包括拼写方面的更改。

## <a name="speech-priming"></a>语音启动

语音启动是指在将文本转换为语音之前使用“将 LUIS 模型发送到语音服务”的过程。 这样一来，语音服务就可以更准确地为模型提供语音转换。 这样可以在一个调用中使用机器人语音以及 LUIS 请求和响应，方法是：进行一次语音调用，然后获取 LUIS 响应。 它的延迟综合说来较低。

## <a name="next-steps"></a>后续步骤

* 请参阅[管理密钥](./luis-how-to-azure-subscription.md)以了解如何将密钥添加到针对 LUIS 的 Azure 订阅密钥、如何设置必应拼写检查密钥以及如何将所有意向包含在结果中。
* 有关如何在测试控制台中测试已发布应用的说明，请参阅[训练和测试应用](luis-interactive-test.md)。

