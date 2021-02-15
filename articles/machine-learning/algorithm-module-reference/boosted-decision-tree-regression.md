---
title: 提升决策树回归：模块参考
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习中的“提升决策树回归”模块，通过提升来创建回归树的系综。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 08/24/2020
ms.openlocfilehash: 664943fc5535883b3df77b2795383e5c0586a71c
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2020
ms.locfileid: "94375323"
---
# <a name="boosted-decision-tree-regression-module"></a>“提升决策树回归”模块

本文介绍 Azure 机器学习设计器中的一个模块。

使用此模块可以通过提升来创建回归树的系综。 “提升”表示每个树依赖于前面的树。 该算法通过拟合每个树前面剩余的树来进行学习。 因此，决策树系综中的提升往往会提高准确度，但在某种程度上也会带来降低覆盖面的小风险。  

此模块基于 LightGBM 算法。
  
此回归方法是一种监督式学习方法，因此需要带标签的数据集。 标签列必须包含数字值。  

> [!NOTE]
> 此模块只能与使用数字变量的数据集配合使用。  

定义模型后，使用[训练模型](./train-model.md)来训练该模型。

  
## <a name="more-about-boosted-regression-trees"></a>有关提升回归树的详细信息  

提升是用于创建系综模型以及装袋、随机林等的多种经典方法中的一种。  在 Azure 机器学习中，提升决策树使用 MART 梯度提升算法的有效实现。 梯度提升是解决回归问题的一种机器学习技术。 它使用预定义的损失函数测量每个步骤中的误差并在下一个步骤中纠正误差，以步进的方式生成每个回归树。 因此，预测模型实际上是较弱预测模型的系综。  
  
在回归问题中，提升可采用步进方式生成一系列树，然后选择使用任意可微分损失函数的最佳树。  
  
有关更多信息，请参阅以下文章：  
  
+ [https://wikipedia.org/wiki/Gradient_boosting#Gradient_tree_boosting](https://wikipedia.org/wiki/Gradient_boosting)

    这篇有关梯度提升的维基百科文章提供了一些有关提升树的背景知识。 
  
-  [https://research.microsoft.com/apps/pubs/default.aspx?id=132652](https://research.microsoft.com/apps/pubs/default.aspx?id=132652)  

    Microsoft Research：从 RankNet 到 LambdaRank 再到 LambdaMART：概述。 作者为 J.C. Burges。

此外，梯度提升法可以使用适当的损失函数将分类问题化简为回归来解决此类问题。 有关分类任务的提升树实现的详细信息，请参阅[双类提升决策树](./two-class-boosted-decision-tree.md)。  

## <a name="how-to-configure-boosted-decision-tree-regression"></a>如何配置“提升决策树回归”

1.  将“提升决策树”模块添加到管道。 可以在“机器学习”、“初始化”中的“回归”类别下找到此模块。   
  
2.  通过设置“创建训练器模式”选项来指定如何训练模型。  
  
    -   **单个参数** ：如果知道自己想要如何配置模型，请选择此选项并提供一组特定的值作为参数。 
     
    -   **参数范围** ：如果不确定最佳参数并想要运行参数整理，请选择此选项。 选择要循环访问的值范围，[优化模型超参数](tune-model-hyperparameters.md)将循环访问所提供设置的所有可能组合，以确定产生最佳结果的超参数。    
   
  
3. **每个树的最大叶数** ：指示可在任何树中创建的终端节点（叶）的最大数目。  

    如果增大此值，则可能会增加树的大小并获得更好的精度，但风险是过度拟合和更长的训练时间。  

4. **每个叶节点的最小样本数** ：指示在树中创建任何终端节点（叶）所需的最小案例数。

    通过增加此值，可以增加创建新规则的阈值。 例如，使用默认值 1 时，即使是单个案例也可以导致创建新规则。 如果将值增加到 5，则训练数据将必须包含至少 5 个满足相同条件的案例。

5. **学习速率** ：键入一个介于 0 和 1 之间的数字，用以定义学习时的步幅。 学习速率决定了学习器收敛于最优解的速度。 如果步幅太大，则可能会越过最优解。 如果步幅太小，则训练将花费更长的时间来收敛于最优解。

6. **构造的树数** ：指示要在系综中创建的决策树总数。 通过创建更多决策树，你可能会获得更好的覆盖范围，但训练时间将会增加。

    如果将值设置为1，则为;但是，在树 (仅生成一个树，该树具有初始参数集) 并且不执行进一步的迭代。

7. **随机数种子** ：键入用作随机种子值的可选非负整数。 指定种子可以确保具有相同数据和参数的运行之间的可再现性。

    随机种子默认设置为 0，这意味着将从系统时钟获取初始种子值。
  

9. 训练模型：

    + 如果将“创建训练程序模式”设置为“单个参数”，请连接带标记的数据集和[训练模型](train-model.md)模块 。  
  
    + 如果将“创建训练程序模式”设置为“参数范围”，请连接带标记的数据集并使用[优化模型超参数](tune-model-hyperparameters.md)来训练模型 。  
  
    > [!NOTE]
    > 
    > 如果将参数范围传递给[训练模型](train-model.md)，则它只使用单个参数列表中的默认值。  
    > 
    > 如果将一组参数值传递给[优化模型超参数](tune-model-hyperparameters.md)模块，则当它期望每个参数有一系列设置时，它会忽略这些值，并为学习器使用默认值。  
    > 
    > 如果选择“参数范围”选项并为任何参数输入单个值，则整个整理过程中都会使用你指定的单个值，即使其他参数的值发生一系列更改。
    

10. 提交管道。  
  
## <a name="results"></a>结果

在训练完成后：

+ 若要使用模型进行评分，请将[训练模型](train-model.md)连接到[评分模型](./score-model.md)，以预测新输入示例的值。

+ 若要保存已训练模型的快照，请在 **已训练模型** 的右侧面板中选择“输出”选项卡，然后单击“注册数据集”图标。 已训练模型的副本将作为模块保存在模块树中，并且不会在后续运行管道时进行更新。

## <a name="next-steps"></a>后续步骤

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。 
