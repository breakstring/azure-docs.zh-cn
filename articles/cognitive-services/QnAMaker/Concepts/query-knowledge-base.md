---
title: 查询知识库-QnA Maker
description: 必须发布知识库。 发布后，将使用 generateAnswer API 在运行时预测终结点上查询知识库。
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: deefc53fdc94851da0e9e255962fbf85692d1393
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820375"
---
# <a name="query-the-knowledge-base-for-answers"></a>查询知识库以获取答案

必须发布知识库。 发布后，将使用 generateAnswer API 在运行时预测终结点上查询知识库。 查询包含问题文本和其他设置，以帮助 QnA Maker 选择最佳匹配项。

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>QnA Maker 如何处理用户查询以选择最佳答案

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

训练和 [发布](../quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) 的 QnA Maker 知识库通过 [GenerateAnswer API](../how-to/metadata-generateanswer-usage.md)从机器人或其他客户端应用程序接收用户查询。 下图说明了收到用户查询时的过程。

![用户查询的排名模型过程](../media/qnamaker-concepts-knowledgebase/ranker-v1.png)

### <a name="ranker-process"></a>Ranker 进程

下表对此过程进行了说明。

|步骤|目的|
|--|--|
|1|客户端应用程序将用户查询发送到 [GENERATEANSWER API](../how-to/metadata-generateanswer-usage.md)。|
|2|QnA Maker 通过语言检测、spellers 和断字符来预处理用户查询。|
|3|此预处理用于更改最佳搜索结果的用户查询。|
|4|此更改的查询将发送到 Azure 认知搜索索引，该索引将接收 `top` 结果数。 如果这些结果中没有正确答案，请将值略微增大 `top` 。 通常， `top` 90% 的查询中的值为10。 Azure 搜索筛选器会在此步骤中 [停止字词](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/STOPWORDS.md) 。|
|5|QnA Maker 使用语法和语义特征化来确定用户查询与提取的 QnA 结果之间的相似性。|
|6|机器学习的 ranker 模型使用步骤5中的不同功能来确定置信度和新的排名顺序。|
|7|新结果将按顺序返回到客户端应用程序。|
|||

使用的功能包括但不限于 word 级语义、语料库中的术语级别重要性，以及深入了解的语义模型，以确定两个文本字符串之间的相似性和相关性。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/v2)

训练和 [发布](../quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) 的 QnA Maker 知识库通过 [GenerateAnswer API](../how-to/metadata-generateanswer-usage.md)从机器人或其他客户端应用程序接收用户查询。 下图说明了收到用户查询时的过程。

![用户查询预览的排名模型流程](../media/qnamaker-concepts-knowledgebase/ranker-v2.png)

### <a name="ranker-process"></a>Ranker 进程

下表对此过程进行了说明。

|步骤|目的|
|--|--|
|1|客户端应用程序将用户查询发送到 [GENERATEANSWER API](../how-to/metadata-generateanswer-usage.md)。|
|2|QnA Maker 通过语言检测、spellers 和断字符来预处理用户查询。|
|3|此预处理用于更改最佳搜索结果的用户查询。|
|4|此更改的查询将发送到 Azure 认知搜索索引，该索引将接收 `top` 结果数。 如果这些结果中没有正确答案，请将值略微增大 `top` 。 通常， `top` 90% 的查询中的值为10。 Azure 搜索筛选器会在此步骤中 [停止字词](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/STOPWORDS.md) 。|
|5|QnA Maker 使用基于先进转换器的模型来确定用户查询与从 Azure 认知搜索提取的候选 QnA 结果之间的相似性。 基于转换器的模型是一个深度学习多语言模型，它在水平方向上适用于所有语言，以确定置信度和新的排名顺序。|
|6|新结果将按顺序返回到客户端应用程序。|
|||

Ranker 使用所有其他问题和答案，查找用户查询的最佳匹配 QnA 对。 用户可以灵活地将 ranker 配置为仅 ranker 的问题。 

---

## <a name="http-request-and-response-with-endpoint"></a>带有终结点的 HTTP 请求和响应
发布知识库时，该服务会创建一个基于 REST 的 HTTP 终结点，该终结点可以集成到应用程序中，通常是一个聊天机器人。

### <a name="the-user-query-request-to-generate-an-answer"></a>用于生成答案的用户查询请求

用户查询是最终用户要求使用知识库的问题，例如 `How do I add a collaborator to my app?` 。 查询通常采用自然语言格式或几个表示问题的关键字，如 `help with collaborators` 。 查询将从客户端应用程序中的 HTTP 请求发送到知识库。

```json
{
    "question": "How do I add a collaborator to my app?",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "QuestionType",
        "value": "Support"
    }],
    "userId": "sd53lsY="
}
```

可以通过设置 [scoreThreshold](./confidence-score.md#choose-a-score-threshold)、 [top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)和 [strictFilters](../how-to/metadata-generateanswer-usage.md#filter-results-with-strictfilters-for-metadata-tags)等属性来控制响应。

使用 [会话上下文](../how-to/metadata-generateanswer-usage.md#use-question-and-answer-results-to-keep-conversation-context) 和 [多轮功能](../how-to/multiturn-conversation.md) ，使对话进入优化问题和答案，找到正确且最终的答案。

### <a name="the-response-from-a-call-to-generate-an-answer"></a>来自调用的响应生成答案

HTTP 响应是基于给定用户查询的最佳匹配项从知识库中检索到的答案。 响应包括答案和预测分数。 如果您要求使用属性的多个顶级答案， `top` 则会获得多个顶级答案，其中每个都有一个分数。

```json
{
    "answers": [
        {
            "questions": [
                "How do I add a collaborator to my app?",
                "What access control is provided for the app?",
                "How do I find user management and security?"
            ],
            "answer": "Use the Azure portal to add a collaborator using Access Control (IAM)",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "QuestionType",
                    "value": "Support"
                },
                {
                    "name": "ToolDependency",
                    "value": "Azure Portal"
                }
            ]
        }
    ]
}
```


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [置信度分数](./confidence-score.md)
