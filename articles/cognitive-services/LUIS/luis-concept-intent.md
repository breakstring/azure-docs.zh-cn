---
title: 意向和实体 - LUIS
titleSuffix: Azure Cognitive Services
description: 单个意向表示用户想执行的任务或操作。 它是用户话语中表达的目的或目标。 定义一组意向，对应于用户希望在应用程序中执行的操作。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: bf5ab7361c84fb787366c7c361829e52362fe427
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025902"
---
# <a name="intents-in-your-luis-app"></a>LUIS 应用中的意向

意向表示用户想执行的任务或操作。 这是用户的[陈述](luis-concept-utterance.md)中所表示的目的或目标。

定义一组意向，对应于用户希望在应用程序中执行的操作。 例如，旅行应用定义了几个意向：

旅行应用意向   |   示例陈述   |
------|------|
 BookFlight     |   “帮我预订下周去里约的航班” <br/> “24 号飞里约” <br/> “我需要下周日去里约热内卢的机票”    |
 Greeting     |   “嗨” <br/>“你好” <br/>“早上好”  |
 CheckWeather | “波士顿的天气怎样？” <br/> “显示本周末的天气预报” |
 无         | “给我一份饼干食谱”<br>“湖人赢了吗？” |

所有应用程序均附带了预定义意向“[None](#none-intent)”，这是一个回退意向。

## <a name="prebuilt-domains-provide-intents"></a>预生成域提供意向
除了定义的意向外，还可以使用其中一个[预生成域](./howto-add-prebuilt-models.md)中的预生成意向。

## <a name="return-all-intents-scores"></a>返回所有意向的得分
将陈述分配给单个意向。 当 LUIS 在终结点上收到话语时，默认情况下，它将返回该话语的最高意向。

如果想要话语的所有意向的得分，可以在预测 API 的查询字符串上提供一个标志。

|预测 API 版本|标志|
|--|--|
|V2|`verbose=true`|
|V3|`show-all-intents=true`|

## <a name="intent-compared-to-entity"></a>意向与实体
意向表示应用程序应为用户采取的操作，并且基于整个话语。 陈述仅可具有一个得分最高的意向，但可具有多个实体。

<a name="how-do-intents-relate-to-entities"></a>

 当用户的意向将在客户端应用程序中触发操作时（例如，调用 checkweather() 函数），请创建意图。 然后创建实体以表示执行操作所需的参数。

|Intent   | 实体 | 示例陈述   |
|------------------|------------------------------|------------------------------|
| CheckWeather | { "type": "location", "entity": "seattle" }<br>{ "type": "builtin.datetimeV2.date","entity": "tomorrow","resolution":"2018-05-23" } | What's the weather like in `Seattle` `tomorrow`?（西雅图明天天气怎样？） |
| CheckWeather | { "type": "date_range", "entity": "this weekend" } | Show me the forecast for `this weekend`（显示本周末的天气预报） |
||||

## <a name="prebuilt-domain-intents"></a>预生成域意向

[预生成域](./howto-add-prebuilt-models.md)通过言语提供意向。

## <a name="none-intent"></a>None 意向

已创建 None 意向但有意留空。 None 意向是必需的意向，不能删除或重命名。 使用域外的陈述对其进行填充。

**None** 意向是回退意向，在每个应用中都很重要，应占话语总数的 10%。 它用于训练应用域（主题区域）中不重要的 LUIS 陈述。 如果不向 None 意向添加任何陈述，LUIS 会强制域外的陈述进入其中一个域意向。 这将因对 LUIS 进行了错误的陈述意向训练而扭曲预测评分。

当话语预测为 None 意向时，客户端应用程序可以询问更多问题或提供菜单以指导用户进行有效选择。

## <a name="negative-intentions"></a>反面意图
如果希望确定正面和反面意向，例如“我想要一辆车”和“我不想要一辆车”，则可以创建两个意图（一个正面意向和一个反面意向），并为每个意向添加适当的陈述。 或者，可以创建单个意向，并将两个不同的正面和反面术语标记为实体。

## <a name="intents-and-patterns"></a>意向和模式

如果你有可部分或全部定义为正则表达式的示例话语，请考虑使用与[模式](luis-concept-patterns.md)配对的[正则表达式实体](luis-concept-entity-types.md#regular-expression-entity)。

使用正则表达式实体可以确保数据提取，以便匹配模式。 模式匹配可确保返回确切的意向。

## <a name="intent-balance"></a>意向平衡
应用域意向应让每个意向的陈述数保持平衡。 请勿出现一个意向具有 10 个陈述，而另一个意向具有 500 个陈述的情况。 这样不平衡。 如果遇到这种情况，请查看具有 500 个陈述的意向，了解是否可将其中许多意向重新组织为[模式](luis-concept-patterns.md)。

平衡中不包含 None 意向。 该意向应包含应用中总陈述数的 10%。

## <a name="intent-limits"></a>意向限制
查看[限制](luis-limits.md#model-boundaries)以了解可添加到模型中的意向数。

### <a name="if-you-need-more-than-the-maximum-number-of-intents"></a>如果所需意向数超过了最大意向数
首先，请考虑系统是否使用了过多意向。

### <a name="can-multiple-intents-be-combined-into-single-intent-with-entities"></a>能否将多个意向合并为具有实体的单个意向
如果意向过于类似，则会增加 LUIS 区分它们的难度。 意向应具有足够的差异，以捕获用户要求的主要任务，但无需捕获代码所采用的每条路径。 例如，在旅行应用中，BookFlight 和 FlightCustomerService 可能是单独的意向，但 BookInternationalFlight 和 BookDomesticFlight 太过于相似。 如果系统需要将其区分开来，请使用实体或其他逻辑，而不是意向。

### <a name="dispatcher-model"></a>调度程序模型
深入了解有关将 LUIS 和 QnA maker 应用与[调度模型](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps)相结合的信息。

### <a name="request-help-for-apps-with-significant-number-of-intents"></a>请求具有大量意向的应用的帮助
如果减少意向数或将意向划分给多个应用不起作用，请与支持部门联系。 如果所持 Azure 订阅包含支持服务，请与 [Azure 技术支持](https://azure.microsoft.com/support/options/)联系。

## <a name="next-steps"></a>后续步骤

* 详细了解[实体](luis-concept-entity-types.md)，这是与意向相关的重要字词
* 了解如何在 LUIS 应用中[添加和管理意向](luis-how-to-add-intents.md)。
* 查看意向[最佳做法](luis-concept-best-practices.md)