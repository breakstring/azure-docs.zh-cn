---
title: 转换为数据集：模块参考
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习设计器中的 "转换为数据集" 模块将数据输入转换为内部数据集格式。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 671a8f7c6fa4c20ef4fc88f57d4a946a84614389
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420863"
---
# <a name="convert-to-dataset"></a>转换为数据集

本文介绍如何使用 Azure 机器学习设计器中的“转换为数据集”模块将管道的任何数据转换为设计器的内部格式。
  
大多数情况下，不一定非要转换。 在对数据执行任何操作时，Azure 机器学习都会将数据隐式转换为其本机数据集格式。 

如果你对一组数据执行了某种标准化或清理，并且你希望确保在其他管道中使用这些更改，我们建议将数据保存为数据集格式。  
  
> [!NOTE]
> “转换为数据集”只会更改数据的格式。 它不会在工作区中保存数据的新副本。 若要保存数据集，请双击输出端口，选择“另存为数据集”，然后输入新名称。  
  
## <a name="how-to-use-convert-to-dataset"></a>如何使用“转换为数据集”  

在使用“转换为数据集”之前，建议使用[编辑元数据](edit-metadata.md)模块来准备数据集。 你可以添加或更改列名，调整数据类型，以及根据需要进行其他更改。

1.  将“转换为数据集”模块添加到管道。 可以在设计器中的 **数据转换** 类别中找到此模块。 

2. 将它连接到可以输出数据集的任何模块。   

    只要数据是[表格](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py)，就可以将其转换为数据集。 这包括通过[导入数据](import-data.md)加载的数据、通过[手动输入数据](enter-data-manually.md)创建的数据，或通过[应用转换](apply-transformation.md)转换的数据集。

3.  在“操作”下拉列表中，指示是否要在保存数据集之前对数据执行任何清理操作：  
  
    - **无** ：按原样使用数据。  
  
    - **SetMissingValue** ：将特定值设置为数据集中的缺失值。 默认占位符是问号字符 (?)，但你可以使用“自定义缺失值”选项来输入其他值。 例如，如果你为“自定义缺失值”输入了 **Taxi** ，则数据集中的所有 **Taxi** 实例都将更改为缺失值。
  
    - **ReplaceValues** ：使用此选项可以指定要替换为任何其他确切值的单个确切值。 可以通过设置“替换”方法来替换缺失值或自定义值：

      - **缺失** ：选择此选项可以替换输入数据集中的缺失值。 对于“新值”，请输入要用来替换缺失值的值。
      - **自定义** ：选择此选项可以替换输入数据集中的自定义值。 对于“自定义值”，请输入要查找的值。 例如，如果数据包含用作缺失值占位符的字符串 `obs`，则输入 `obs`。 对于“新值”，请输入用来替换原始字符串的新值。
  
    请注意， **ReplaceValues** 操作仅应用于完全匹配项。 例如，这些字符串不会受影响：`obs.`、`obsolete`。  
 
  
5.  提交管道。  

## <a name="results"></a>结果

+  要使用新名称保存生成的数据集，请在模块右面板的“输出”选项卡下选择“注册数据集”图标 。  
  
## <a name="technical-notes"></a>技术说明  

-   接受数据集作为输入的任何模块还可以使用 CSV 文件或 TSV 文件中的数据。 在运行任何模块代码之前，将对输入进行预处理。 预处理等效于对输入运行“转换为数据集”模块。  
  
-   无法从 SVMLight 格式转换为数据集。  
  
-   当指定自定义替换操作时，搜索和替换操作将应用于完整值。 不允许部分匹配。 例如，可以将 3 替换为 -1 或 33，但不能替换两位数（例如 35）中的 3。  
  
-   对于自定义替换操作，如果你使用不符合列的当前数据类型的任何字符进行替换，则替换将失败且不会提示。  

  
## <a name="next-steps"></a>后续步骤

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。