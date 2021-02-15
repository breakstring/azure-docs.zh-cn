---
title: “从文本中提取 N 元语法特征”模块参考
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习设计器中的 "提取 N 元语法" 模块来对文本数据进行特征。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 12/08/2019
ms.openlocfilehash: 37a10d90fa0e277fbe45d9f1377e365cb3d42996
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861439"
---
# <a name="extract-n-gram-features-from-text-module-reference"></a>“从文本中提取 N 元语法特征”模块参考

本文介绍 Azure 机器学习设计器中的一个模块。 使用“从文本中提取 N 元语法特征”模块抽取非结构化文本数据的特征。 

## <a name="configuration-of-the-extract-n-gram-features-from-text-module"></a>配置“从文本中提取 N 元语法特征”模块

该模块支持以下使用 N 元语法字典的方案：

* 从一列自由文本[创建新的 N 元语法字典](#create-a-new-n-gram-dictionary)。

* [使用一组现有文本特征](#use-an-existing-n-gram-dictionary)抽取自由文本列的特征。

* [评分或部署](#build-inference-pipeline-that-uses-n-grams-to-deploy-a-real-time-endpoint) 使用 n 元语法的模型。

### <a name="create-a-new-n-gram-dictionary"></a>创建新的 N 元语法字典

1.  将“从文本中提取 N 元语法特征”模块添加到管道，并连接包含要处理的文本的数据集。

1.  使用“文本列”选择包含要提取的文本的字符串类型列。 由于结果冗长，一次只能处理一个列。

1. 将“词汇模式”设置为“创建”，以指示你正在创建 N 元语法特征的新列表 。 

1. 设置“N 元语法大小”，以指示要提取和存储的 N 元语法的最大大小。 

    例如，如果输入 3，则将创建一元语法、双元语法和三元语法。

1. 加权函数指定如何构建文档特征向量以及如何从文档中提取词汇。

    * **二进制权重**：将二进制状态值分配给提取的 N 元语法。 如果文档中存在 N 元语法，则其值为 1；否则为 0。

    * **TF 权重**：将词频 (TF) 分数分配给提取的 N 元语法。 每个 N 元语法的值是其在文档中的出现频率。

    * **IDF 权重**：将反转文件频率 (IDF) 分数分配给提取的 N 元语法。 每个 N 元语法的值是语料库大小的对数除以其在整个语料库中的出现频率。
    
      `IDF = log of corpus_size / document_frequency`
 
    *  **TF-IDF 权重**：将词频/反转文件频率 (TF/IDF) 分数分配给提取的 N 元语法。 每个 N 元语法的值是其 TF 分数乘以其 IDF 分数。

1. 将“最小字长”设置为可以在 N 元语法的任意单字中使用的最小字符数。

1. 使用“最大字长”设置可在 N 元语法的任意单字中使用的最大字符数。

    默认情况下，每个字或令牌最多可包含 25 个字符。

1. 使用“最小 N 元语法文件绝对频率”来设置 N 元语法字典中包含的任何 N 元语法所需的最小出现次数。 

    例如，如果使用默认值 5，则任何 N 元语法都必须在语料库中至少出现 5 次，才会被包含在 N 元语法字典中。 

1.  将“最大 N 元语法文件比率”设置为包含一种特定 N 元语法的行数占语料库中总行数的最大比率。

    例如，比率为 1，则表示即使每行中都有特定的 N 元语法，也可以将 N 元语法添加到 N 元语法字典中。 通常，将每一行中都出现的字视为干扰词，需将其删除。 若要筛选掉领域相关干扰词，请尝试降低此比率。

    > [!IMPORTANT]
    > 特定字的出现率不一致。 它因文件而异。 例如，如果要分析有关特定产品的客户评论，则产品名的频率可能会非常高且接近干扰词，但它在其他上下文中是一个重要的术语。

1. 选择选项“标准化 N 元语法特征向量”以标准化特征向量。 如果启用此选项，则每个 N 元语法特征向量除以其 L2 范数。

1. 提交管道。

### <a name="use-an-existing-n-gram-dictionary"></a>使用现有的 N 元语法字典

1.  将“从文本中提取 N 元语法特征”模块添加到管道，并将包含要处理的文本的数据集连接到“数据集”端口。

1.  使用“文本列”选择包含要抽取特征的文本的文本列。 默认情况下，该模块选择“字符串”类型的所有列。 为了获得最佳结果，请一次处理一个列。

1. 添加已保存的数据集，其中包含之前生成的 N 元语法字典，并将其连接到“输入词汇”端口。 还可连接“从文本中提取 N 元语法特征”模块的上游实例的“结果词汇”输出。

1. 对于“词汇模式”，请从下拉列表中选择“只读”更新选项 。

   “只读”选项表示输入词汇的输入语料库。 按原样应用输入词汇中的 N 元语法权重，而不是从新文本数据集（左侧输入）计算词频。

   > [!TIP]
   > 如果要对文本分类器进行评分，请使用此选项。

1.  对于所有其他选项，请参阅[上一节](#create-a-new-n-gram-dictionary)中的属性说明。

1.  提交管道。

### <a name="build-inference-pipeline-that-uses-n-grams-to-deploy-a-real-time-endpoint"></a>使用 n 元语法部署实时终结点的生成推理管道

一个训练管道，其中包含从文本和 **评分模型** 中 **提取 N 元语法功能** 以对测试数据集进行预测，采用以下结构：

:::image type="content" source="./media/module/extract-n-gram-training-pipeline-score-model.png" alt-text="提取 N 语法定型管道示例" border="true":::

带圆圈的 "**从文本中提取 N 元语法" 功能** 的 **词汇模式** 是 "**创建**"，连接到 **评分模型** 模块的模块的 **词汇模式** 为 **ReadOnly**。

成功提交上述定型管道后，可以将带圆圈的模块的输出注册为数据集。

:::image type="content" source="./media/module/extract-n-gram-output-voc-register-dataset.png" alt-text="注册数据集" border="true":::

然后，你可以创建实时推理管道。 创建推理管道后，需要手动调整推理管道，如下所示：

:::image type="content" source="./media/module/extract-n-gram-inference-pipeline.png" alt-text="推理管道" border="true":::

然后，提交推理管道并部署实时终结点。

## <a name="results"></a>结果

“从文本中提取 N 元语法特征”模块会创建两种类型的输出： 

* **结果数据集**：此输出总结了与提取的 N 元语法结合的经分析文本。 未在“文本列”选项中选择的列选项将传递到输出。 对于分析的每个文本列，该模块将生成以下列：

  * **N 元语法出现次数的矩阵**：该模块为总语料库中找到的每个 N 元语法生成一个列，并在每个列中添加一个分数，以指示该行的 N 元语法的权重。 

* **结果词汇**：词汇包含实际的 N 元语法字典，以及在分析过程中生成的词频分数。 可保存数据集，以便重用于另一组输入，或用于稍后更新。 还可以重用词汇进行建模和评分。

### <a name="result-vocabulary"></a>结果词汇

词汇包含 N 元语法字典，还包含在分析过程中生成的词频分数。 无论其他选项如何，都将生成 DF 和 IDF 分数。

+ **ID**：为每个唯一 N 元语法生成的标识符。
+ **nGram**：N 元语法。 空格或其他断字符将替换为下划线字符。
+ **DF**：原始语料库中 N 元语法的词频分数。
+ **IDF**：原始语料库中 N 元语法的反转文件频率分数。

可以手动更新此数据集，但可能会引入错误。 例如：

* 如果模块发现输入词汇中包含具有相同键的重复行，则会引发错误。 请确保词汇中任意两行都不具有相同的字。
* 词汇数据集的输入架构必须完全匹配，包括列名和列类型。 
* ID 列和 DF 列必须是整数类型 。 
* IDF 列必须是浮点类型。

> [!Note]
> 不要直接将数据输出连接到“训练模型”模块。 应删除自由文本列，以免它们被馈送到训练模型。 否则，自由文本列将被视为分类特征。

## <a name="next-steps"></a>后续步骤

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。
