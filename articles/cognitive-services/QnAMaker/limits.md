---
title: 限制和边界 - QnA Maker
description: QnA Maker 对部分知识库和服务具有元限制。 为了测试和发布，请务必将知识库保留在这些限制内。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2020
ms.openlocfilehash: 1e57ae537c271e61f0b2d37f5320cb177b04802b
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/13/2021
ms.locfileid: "98164866"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>QnA Maker 知识库限制和边界

下面提供 QnA Maker 限制是 [Azure 认知搜索定价层限制](../../search/search-limits-quotas-capacity.md) 和 [QnA Maker 定价层限制](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)的组合。 您需要了解这两组限制，以了解您可以为每个资源创建多少知识库以及每个知识库的增长大小。

## <a name="knowledge-bases"></a>知识库

最大知识库数基于 [Azure 认知搜索层限制](../../search/search-limits-quotas-capacity.md)。

|**Azure 认知搜索层** | 免费 | **基本** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|已发布知识库的最大允许数量|2|14|49|199|199|2,999|

 例如，如果你的层有 15 个允许的索引，则可以发布 14 个知识库（每个已发布的知识库有 1 个索引）。 第 15 个索引 `testkb` 用于所有知识库以进行创作和测试。

## <a name="extraction-limits"></a>提取限制

### <a name="file-naming-constraints"></a>文件命名约束

文件名不能包含以下字符：

|不使用字符|
|--|
|单引号 `'`|
|双引号 `"`|

### <a name="maximum-file-size"></a>文件大小上限

|格式|最大文件大小 (MB) |
|--|--|
|`.docx`|10|
|`.pdf`|25|
|`.tsv`|10|
|`.txt`|10|
|`.xlsx`|3|

### <a name="maximum-number-of-files"></a>最大文件数

可以提取的最大文件数和最大文件大小取决于您 QnA Maker 的 **[定价层限制](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)**。

> [!NOTE]
> QnA Maker 托管 (预览版) 是一种免费服务，对可以添加的源数量没有限制。 对于管理 Api 和预测 Api，吞吐量当前上限为10个事务/秒。

### <a name="maximum-number-of-deep-links-from-url"></a>URL 中的最大深层链接数

可爬网的最大深度链接数为从 URL 页中提取 Qna 的最大数目为 **20**。

## <a name="metadata-limits"></a>元数据限制

元数据显示为基于文本的键：值对，如 `product:windows 10` 。 它以小写形式存储和比较。

### <a name="by-azure-cognitive-search-pricing-tier"></a>由 Azure 认知搜索定价层

每个知识库的元数据字段的最大数目取决于 **[Azure 认知搜索层限制](../../search/search-limits-quotas-capacity.md)**。

|**Azure 认知搜索层** | 免费 | **基本** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|每个 QnA Maker 服务的元数据字段的最大数量（包括所有知识库）|1,000|100*|1,000|1,000|1,000|1,000|

### <a name="by-name-and-value"></a>按名称和值

下表列出了元数据名称和值可接受的长度和可接受的字符。

|项|允许的字符|Regex 模式匹配|最大字符数|
|--|--|--|--|
|名称 (密钥) |可用<br>字母数字 (字母和数字) <br>`_` (下划线) <br> 不得包含空格。|`^[a-zA-Z0-9_]+$`|100|
|Value|允许除以外的所有内容<br>`:` (冒号) <br>`|` (垂直管道) <br>仅允许一个值。|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>知识库内容限制
知识库中内容的总体限制：
* 应答文本的长度：25000个字符
* 问题文本的长度：1000个字符
* 元数据密钥文本的长度：100个字符
* Metadata 值文本的长度：500个字符
* 支持的元数据名称字符：字母、数字和 `_`
* 元数据值支持的字符数：除和之外的所有字符 `:``|`
* 文件名长度：200
* 支持的文件格式:：“.tsv”、“.pdf”、“.txt”、“.docx”、“.xlsx”。
* 最大替代问题数：300
* 问题答案对的最大数目：取决于所选的 **[Azure 认知搜索层](../../search/search-limits-quotas-capacity.md#document-limits)** 。 问题和答案对映射到 Azure 认知搜索索引上的文档。
* URL/HTML 页面：1000000个字符

## <a name="create-knowledge-base-call-limits"></a>创建知识库调用限制：
表示每个创建知识库操作的限制；即，单击“创建知识库”或调用 CreateKnowledgeBase API。
* 建议的每个回答的最大替代问题数：300
* 最大 URL 数：10
* 最大文件数：10
* 每个调用允许的最大 Qna 数：1000

## <a name="update-knowledge-base-call-limits"></a>更新知识库调用限制
表示每个更新操作的限制；即，单击“保存并培训”或调用 UpdateKnowledgeBase API。
* 每个源名称的长度：300
* 建议添加或删除的最大替代问题数：300
* 添加或删除的元数据字段的最大数量：10
* 可以刷新的 URL 的最大数量：5
* 每个调用允许的最大 Qna 数：1000

## <a name="next-steps"></a>后续步骤

了解何时以及如何更改 [服务定价层](How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku)。
