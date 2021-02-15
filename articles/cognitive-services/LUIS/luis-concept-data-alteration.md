---
title: 数据更改 - LUIS
description: 了解如何在语言理解 (LUIS) 得出预测之前更改数据
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/06/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 621a41f743b751a8c24bf6f6ad8497fb5c79775c
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026004"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>在预测之前或预测期间更改话语数据
LUIS 提供在预测之前或预测期间操作陈述的方法。 其中包括 [修复拼写](luis-tutorial-bing-spellcheck.md)和修复预生成的 [datetimeV2](luis-reference-prebuilt-datetimev2.md)的时区问题。

## <a name="correct-spelling-errors-in-utterance"></a>更正陈述中的拼写错误


### <a name="v3-runtime"></a>V3 运行时

在将查询文本发送到 LUIS 之前，预处理文本的拼写更正。 使用带有正确拼写的示例最谈话，以确保获得正确的预测。

将文本发送到 LUIS 之前，请使用 [必应拼写检查](../bing-spell-check/overview.md) 更正文本。

### <a name="prior-to-v3-runtime"></a>在 V3 运行时之前

LUIS 使用[必应拼写检查 API V7](../Bing-Spell-Check/overview.md) 来更正陈述中的拼写错误。 LUIS 需要与该服务关联的密钥。 创建密钥，然后将密钥添加为[终结点](https://go.microsoft.com/fwlink/?linkid=2092356)的 querystring 参数。

终结点需要两个参数以进行拼写更正：

|Param|值|
|--|--|
|`spellCheck`|boolean|
|`bing-spell-check-subscription-key`|[必应拼写检查 API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) 终结点密钥|

[必应拼写检查 API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) 检测到错误时，将一并从终结点返回原始陈述、已更正陈述和预测。

#### <a name="v2-prediction-endpoint-response"></a>[V2 预测终结点响应](#tab/V2)

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 预测终结点响应](#tab/V3)

```JSON
{
    "query": "Book a flite to London?",
    "prediction": {
        "normalizedQuery": "book a flight to london?",
        "topIntent": "BookFlight",
        "intents": {
            "BookFlight": {
                "score": 0.780123
            }
        },
        "entities": {},
    }
}
```

* * *

### <a name="list-of-allowed-words"></a>允许的字词列表
LUIS 中使用的必应拼写检查 API 不支持在拼写检查更改期间要忽略的单词列表。 如果需要允许字词或首字母缩写词的列表，请在将话语发送到 LUIS 进行意向预测之前在客户端应用程序中处理话语。

## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>更改预生成 datetimeV2 实体的时区
LUIS 应用使用预生成的 [datetimeV2](luis-reference-prebuilt-datetimev2.md) 实体时，可以在预测响应中返回日期/时间值。 请求的时区用于确定要返回的正确日期/时间。 如果请求在到达 LUIS 之前来自机器人或另一个集中式应用程序，则更正 LUIS 使用的时区。

### <a name="v3-prediction-api-to-alter-timezone"></a>用于更改时区的 V3 预测 API

在 V3 中，`datetimeReference` 确定时区偏移量。 请详细了解 [V3 预测](luis-migration-api-v3.md#v3-post-body)。

### <a name="v2-prediction-api-to-alter-timezone"></a>用于更改时区的 V2 预测 API
可以通过以下方式更正时区：根据 API 版本，使用 `timezoneOffset` 参数将用户的时区添加到终结点。 要更改时间，此参数值应为正数或负数（以分钟为单位）。

#### <a name="v2-prediction-daylight-savings-example"></a>V2 预测夏令时示例
如果需要返回的预生成 datetimeV2 来调整夏令时，则应对该[终结点](https://go.microsoft.com/fwlink/?linkid=2092356)查询使用值为正数/负数（以分钟为单位）的 querystring 参数。

增加 60 分钟：

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?timezoneOffset=60&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

减去 60 分钟：

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?timezoneOffset=-60&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

#### <a name="v2-prediction-c-code-determines-correct-value-of-parameter"></a>V2 预测 C# 代码确定正确的参数值

下面的 C# 代码使用 [TimeZoneInfo](/dotnet/api/system.timezoneinfo) 类的 [FindSystemTimeZoneById](/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples) 方法基于系统时间来确定正确的偏移值：

```csharp
// Get CST zone id
TimeZoneInfo targetZone = TimeZoneInfo.FindSystemTimeZoneById("Central Standard Time");

// Get local machine's value of Now
DateTime utcDatetime = DateTime.UtcNow;

// Get Central Standard Time value of Now
DateTime cstDatetime = TimeZoneInfo.ConvertTimeFromUtc(utcDatetime, targetZone);

// Find timezoneOffset/datetimeReference
int offset = (int)((cstDatetime - utcDatetime).TotalMinutes);
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [通过本教程更正拼写错误](luis-tutorial-bing-spellcheck.md)