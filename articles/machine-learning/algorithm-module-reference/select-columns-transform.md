---
title: 选择列转换：模块参考
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习设计器中的 "选择列" 转换模块来执行 select 转换。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2020
ms.openlocfilehash: f03840e55366d7f105ca4b57bd60061c82833e72
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420710"
---
# <a name="select-columns-transform"></a>选择列转换

本文介绍如何使用 Azure 机器学习设计器中的“选择列转换”模块。 “选择列转换”模块的用途是确保在下游机器学习操作中使用可预测、一致的列集。

此模块对于需要特定列的任务（例如评分）很有帮助。 可用列中的更改可能会破坏管道或改变结果。

你将使用“选择列转换”来创建并保存一个列集。 然后，使用[应用转换](apply-transformation.md)模块来将那些选择应用于新数据。

## <a name="how-to-use-select-columns-transform"></a>如何使用“选择列转换”

此方案假设你要使用特征选择来生成将用于训练模型的一个动态列集。 为确保列选择对评分过程是相同的，你将使用“选择列转换”模块来捕获列选择并在管道中的其他位置应用它们。

1. 在设计器中将输入数据集添加到管道。

2. 添加[基于筛选器的特征选择](filter-based-feature-selection.md)的一个实例。

3. 连接模块并配置特征选择模块，以便在输入数据集中自动查找许多最佳特征。

4. 添加[训练模型](train-model.md)的一个实例，并使用[基于筛选器的特征选择](filter-based-feature-selection.md)的输出作为用于训练的输入。

    > [!IMPORTANT]
    > 由于特征重要性基于列中的值，因此你无法提前知道哪些列可能可用于[训练模型](train-model.md)的输入。  
5. 附加“选择列转换”模块的一个实例。 

    此步骤会将列选择生成为一个转换，可以保存该转换并将其应用于其他数据集。 此步骤可确保保存在特征选择中标识的列，以便在其他模块中重复使用。

6. 添加[评分模型](score-model.md)模块。 

   *请勿连接输入数据集。* 相反，请添加[应用转换](apply-transformation.md)模块，并连接特征选择转换的输出。

   管道结构应如下所示：

   > [!div class="mx-imgBorder"]
   > ![示例管道](media/module/filter-based-feature-selection-score.png)

   > [!IMPORTANT]
   > 不能期望将[基于筛选器的特征选择](filter-based-feature-selection.md)应用于评分数据集并获得相同的结果。 由于特征选择基于值，因此它可能会选择一个不同的列集，这将导致评分操作失败。
    
7. 提交管道。

保存然后应用列选择的这一过程可以确保使用相同的数据架构进行训练和评分。


## <a name="next-steps"></a>后续步骤

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。 
