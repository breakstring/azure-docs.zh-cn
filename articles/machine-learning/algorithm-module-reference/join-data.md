---
title: 联接数据：模块参考
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习设计器中的联接数据模块将两个数据集合并在一起。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: c23dca40d50c5837bd9ff45bc3c3d7fb2581685b
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420744"
---
# <a name="join-data"></a>联接数据

本文介绍了如何通过 Azure 机器学习设计器中的“联接数据”模块使用数据库样式的联接操作来合并两个数据集。  

## <a name="how-to-configure-join-data"></a>如何配置“联接数据”

若要对两个数据集执行联接，它们应通过键列进行关联。 还支持使用多个列的组合键。 

1. 添加要合并的数据集，然后将 **联接数据** 模块拖动到管道中。 

    可以在“数据转换”类别下的“操作”中找到此模块。

1. 将数据集连接到 **联接数据** 模块。 
 
1. 选择“启动列选择器”来选择键列。 请记得同时为左侧输入和右侧输入选择列。

    对于单一键：

    为两个输入选择单个键列。
    
    对于组合键：

    按相同顺序从左输入和右输入中选择所有键列。 当所有键列都匹配时， **联接数据** 模块将联接这些表。 如果列顺序与原始表不同，请选中选项“允许重复项并保留选定内容中的列顺序”。 

    ![列选择器](media/module/join-data-column-selector.png)


1. 如果要在文本列联接上保留区分大小写，请选择“匹配大小写”选项。 
   
1. 使用“联接类型”下拉列表指定应当如何组合数据集。  
  
    * **内部联接** ：“内部联接”是最常见的联接操作。 仅当键列的值匹配时，它才会返回组合的行。  
  
    * **左外部联接** ：“左外部联接”为左表中的所有行返回联接的行。 如果左表中的某行在右表中没有匹配的行，则对于来自右表的所有列，返回的行将包含缺失值。 你还可以为缺失值指定替换值。  
  
    * **完全外部联接** ：“完全外部联接”返回来自左表 ( **table1** ) 和来自右表 ( **table2** ) 的所有行。  
  
         对于任一表中在另一表中没有匹配行的每个行，结果中将包括含缺失值的一个行。  
  
    * **左半联接** ：当键列的值匹配时，“左半联接”只返回左表中的值。  

1. 对于选项“在联接的表中保留右侧的键列”：

    * 选择此选项可以查看两个输入表中的键。
    * 取消选择此选项将只返回左侧输入中的键列。

1. 提交管道。

1. 若要查看结果，请右键单击“联接数据”，然后选择“可视化”。

## <a name="next-steps"></a>后续步骤

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。 