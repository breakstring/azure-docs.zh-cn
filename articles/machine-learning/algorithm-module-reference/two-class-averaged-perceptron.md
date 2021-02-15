---
title: 双类平均感知器：模块参考
titleSuffix: Azure Machine Learning
description: 了解如何在设计器中使用 Two-Class 平均感知器模块来创建二进制分类器。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: f1161dc99b84026bcc830c08b63b95d0bf3b0994
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421135"
---
# <a name="two-class-averaged-perceptron-module"></a>双类平均感知器模块

本文介绍 Azure 机器学习设计器中的一个模块。

使用此模块，可以根据平均感知器算法创建机器学习模型。  
  
此分类算法是一种监督式学习方法，需要一个标记的数据集（其中包含标签列）。 可以对模型进行训练，只需提供模型和标记的数据集作为[训练模型](./train-model.md)的输入， 然后即可使用训练的模型来预测新输入示例的值。  

### <a name="about-averaged-perceptron-models"></a>关于平均感知器模型

平均感知器方法是神经网络的早期简单版本。 在此方法中，我们根据一个线性函数将输入分类为多个可能的输出，然后将其与一组派生自特征矢量的权重相结合，这也是“感知器”这一名称的由来。

较简单的感知器模型适用于学习线性可分模式，而神经网络（尤其是深度神经网络）则可对较复杂的类边界建模。 但是，感知器速度更快，并且由于它们是按顺序处理案例的，因此可以将它们用于连续训练。

## <a name="how-to-configure-two-class-averaged-perceptron"></a>如何配置双类平均感知器

1.  向管道添加“双类平均感知器”模块。  

2.  通过设置“创建训练程序模式”选项，指定所希望的模型训练方式。  
  
    -   **单个参数** ：如果知道自己想要如何配置模型，请提供一组特定的值作为参数。

    -   **参数范围** ：如果不确定最佳参数并想要运行参数整理，请选择此选项。 选择要循环访问的值范围，[优化模型超参数](tune-model-hyperparameters.md)将循环访问所提供设置的所有可能组合，以确定产生最佳结果的超参数。  
  
3.  对于“学习速率”，请指定一个值作为“学习速率”。 学习速率值控制每次对模型进行测试和纠正时用于随机梯度下降法的梯度的大小。
  
     降低该速率可以加快模型测试频率，但风险是可能会在局部出现拟合效果改进缓慢的情况。 加大梯度可以加快聚合速度，但风险是可能会错过真正的最小值。
  
4.  对于“最大迭代次数”，请键入你希望算法检查训练数据的次数。  
  
     早停止通常提供更好的通用化。 提高迭代次数可以改进拟合情况，风险是过度拟合。
  
5.  对于“随机数种子”，可以选择键入一个整数值，将其用作种子。 如果需要跨运行确保管道的可再现性，建议使用种子。  
  
1.  连接训练数据集，然后训练模型：

    + 如果将“创建训练程序模式”设置为“单个参数”，请连接带标记的数据集和[训练模型](train-model.md)模块 。  
  
    + 如果将“创建训练程序模式”设置为“参数范围”，请连接带标记的数据集并使用[优化模型超参数](tune-model-hyperparameters.md)来训练模型 。  
  
    > [!NOTE]
    > 
    > 如果将参数范围传递给[训练模型](train-model.md)，则它只使用单个参数列表中的默认值。  
    > 
    > 如果将一组参数值传递给[优化模型超参数](tune-model-hyperparameters.md)模块，则当它期望每个参数有一系列设置时，它会忽略这些值，并为学习器使用默认值。  
    > 
    > 如果选择“参数范围”选项并为任何参数输入单个值，则整个整理过程中都会使用你指定的单个值，即使其他参数的值发生一系列更改。




## <a name="next-steps"></a>后续步骤

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。 