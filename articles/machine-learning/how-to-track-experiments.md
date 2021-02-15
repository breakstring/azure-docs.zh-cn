---
title: 记录 ML 试验和指标
titleSuffix: Azure Machine Learning
description: 在 ML 训练运行上启用日志记录，以便监视实时运行指标并帮助诊断错误和警告。
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 9e5f64d9ef61a272da488ad70e690db4c07ddccc
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625071"
---
# <a name="enable-logging-in-ml-training-runs"></a>在 ML 训练运行中启用日志记录


Azure 机器学习 Python SDK 允许使用默认的 Python 日志记录包和特定于 SDK 的功能记录实时信息。 你可以在本地进行记录，并将日志发送到门户中的工作区。

日志可帮助你诊断错误和警告，或跟踪参数和模型性能等性能指标。 本文介绍如何在以下场景中启用日志记录功能：

> [!div class="checklist"]
> * 交互式训练会话
> * 使用 ScriptRunConfig 提交训练作业
> * Python 的原生 `logging` 设置
> * 来自其他源的日志记录


> [!TIP]
> 本文说明如何监视模型训练过程。 如果你希望监视 Azure 机器学习的资源使用情况和事件，例如配额、已完成的训练运行或已完成的模型部署，请参阅[监视 Azure 机器学习](monitor-azure-machine-learning.md)。

## <a name="data-types"></a>数据类型

可以记录多个数据类型，包括标量值、列表、表、图像、目录等。 有关不同数据类型的详细信息和 Python 代码示例，请查看 [Run 类参考页](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py)。

## <a name="interactive-logging-session"></a>交互式日志记录会话

交互式日志记录会话通常用在笔记本环境中。 方法 [Experiment.start_logging()](/python/api/azureml-core/azureml.core.experiment%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truestart-logging--args----kwargs-) 启动交互式日志记录会话。 试验中会话期间记录的任何指标都会添加到运行记录中。 方法 [run.complete()](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truecomplete--set-status-true-) 结束会话并将运行标记为已完成。

## <a name="scriptrun-logs"></a>ScriptRun 日志

本部分介绍使用了 ScriptRunConfig 进行配置时，如何在创建的各次运行之内添加记录代码。 可以使用 [**ScriptRunConfig**](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) 类来封装用于可重复运行的脚本和环境。 还可以使用此选项来显示一个用于监视的 Jupyter Notebooks 视觉小组件。

此示例使用 [run.log()](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truelog-name--value--description----) 方法对 alpha 值执行参数扫描并捕获结果。

1. 创建包含日志记录逻辑的训练脚本 `train.py`。

   [!code-python[](~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]


1. 提交要在用户管理的环境中运行的 ```train.py``` 脚本。 整个脚本文件夹都要提交，以便进行训练。

   [！笔记本-python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb？ name = src) ]


   [！笔记本-python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb？名称 = 运行) ]

    `show_output` 参数会启用详细日志记录，让你可以查看训练过程的详细信息，以及有关任何远程资源或计算目标的信息。 请使用以下代码在提交试验时启用详细日志记录。

```python
run = exp.submit(src, show_output=True)
```

还可以在生成的运行上的 `wait_for_completion` 函数中使用相同的参数。

```python
run.wait_for_completion(show_output=True)
```

## <a name="native-python-logging"></a>原生 Python 日志记录

SDK 中的某些日志可能包含一个错误，指示你将日志记录级别设置为“调试”。 若要设置日志记录级别，请在脚本中添加以下代码。

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="additional-logging-sources"></a>其他日志记录源

Azure 机器学习还可以在训练期间记录其他来源的信息，例如自动化机器学习运行或运行作业的 Docker 容器。 这些日志未进行记录，但如果你遇到问题并联系了 Microsoft 支持部门，他们可以在排除故障时使用这些日志。

有关在 Azure 机器学习设计器中记录指标的信息，请参阅[如何在设计器中记录指标](how-to-track-designer-experiments.md)

## <a name="example-notebooks"></a>示例笔记本

下面的笔记本展示了本文中的概念：
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>后续步骤

请参阅以下文章，详细了解如何使用 Azure 机器学习：

* 了解如何[在 Azure 机器学习设计器中记录指标](how-to-track-designer-experiments.md)。

* 查看教程[使用 Azure 机器学习训练图像分类模型](tutorial-train-models-with-aml.md)中的示例，了解如何注册和部署最佳模型。
