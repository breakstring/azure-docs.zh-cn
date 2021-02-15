---
title: 多类逻辑回归：模块参考
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习设计器中的 "多类逻辑回归" 模块来预测多个值。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: e2bbc28735bcbfd952c4941453956acd0568ea67
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420727"
---
# <a name="multiclass-logistic-regression-module"></a>多类逻辑回归模块

本文介绍 Azure 机器学习设计器中的一个模块。

使用此模块创建可用于预测多个值的逻辑回归模型。

使用逻辑回归的分类方法是一种监督式学习方法，因此需要经过标记的数据集。 可以通过提供模型和带标记的数据集作为模块（如[训练模型](./train-model.md)）的输入，来对模型进行训练。 然后即可使用训练的模型来预测新输入示例的值。

Azure 机器学习还提供了[两类逻辑回归](./two-class-logistic-regression.md)模块，该模块适用于对二进制或二分变量进行分类。

## <a name="about-multiclass-logistic-regression"></a>关于多类逻辑回归

逻辑回归是统计学中著名的用于预测结果概率的方法，是分类任务的常用方法。 该算法通过将数据拟合到逻辑函数来预测事件发生的概率。 

在多类逻辑回归中，分类器可用于预测多个结果。

## <a name="configure-a-multiclass-logistic-regression"></a>配置多类逻辑回归

1. 将“多类逻辑回归”模块添加到管道。

2. 通过设置“创建训练程序模式”选项，指定所希望的模型训练方式。

    + **单个参数** ：如果知道自己想要如何配置模型，请使用此选项并提供一组特定的值作为参数。

    + **参数范围** ：如果无法确定最佳参数并想要运行参数扫描，请选择此选项。 选择要循环访问的值范围，[优化模型超参数](tune-model-hyperparameters.md)模块将循环访问所提供的设置的所有可能组合，以确定产生最佳结果的超参数。  

3. 优化容差，指定优化器收敛的阈值。 如果迭代间的改进小于阈值，则算法将停止并返回当前模型。

4. **L1 正则化权重** ， **L2 正则化权重** ：键入要用于正则化参数 L1 和 L2 的值。 对于这两个值，建议使用非零值。

    正则化是一种通过处罚具有极端系数值的模型来防止过度拟合的方法。 正则化的工作原理是将与系数值相关联的处罚添加到假设的错误。 具有极端系数值的准确模型受到的处罚相较而言更大，而值更保守的不准确的模型受到的处罚相较而言更小。

     L1 和 L2 正则化具有不同的效果和用途。 L1 可用于稀疏模型，这在处理高维数据时非常有用。 与此相反，L2 正则化更适合用于非稀疏数据。  此算法支持 L1 和 L2 正则化值的线性组合：也就是说，如果 `x = L1` 且 `y = L2`，则 `ax + by = c` 定义正则化术语的线性跨度。

     已为逻辑回归模型设计了 L1 和 L2 术语的不同线性组合，例如[弹性网络正则化](https://wikipedia.org/wiki/Elastic_net_regularization)。

6. **随机数种子** ：如果希望结果在运行期间是可重复的，请键入一个整数值作为算法的种子。 否则，将使用系统时钟值作为种子，这可能会在同一管道的运行中产生略微不同的结果。

8. 连接标记的数据集，并训练模型：

    + 如果将“创建训练程序模式”设置为“单个参数”，请连接带标记的数据集和[训练模型](train-model.md)模块 。  
  
    + 如果将“创建训练程序模式”设置为“参数范围”，则连接标记的数据集并使用[优化模型超参数](tune-model-hyperparameters.md)模块来训练模型 。  
  
    > [!NOTE]
    > 
    > 如果将参数范围传递给[训练模型](train-model.md)模块，则它只使用单个参数列表中的默认值。  
    > 
    > 如果将一组参数值传递给[优化模型超参数](tune-model-hyperparameters.md)模块，则当它期望每个参数有一系列设置时，它会忽略这些值，并为学习器使用默认值。  
    > 
    > 如果选择“参数范围”选项并为任何参数输入单个值，则整个扫描过程中都会使用你指定的单个值，即使其他参数的一系列值发生了更改。

9. 提交管道。



## <a name="next-steps"></a>后续步骤

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。 