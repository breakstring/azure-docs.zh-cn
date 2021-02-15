---
title: 教程：训练你的第一个机器学习模型 - Python
titleSuffix: Azure Machine Learning
description: Azure 机器学习入门系列的第 3 部分介绍了如何训练机器学习模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: b1fa4d3e6c017232922e500352558e34726b90cc
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98183075"
---
# <a name="tutorial-train-your-first-machine-learning-model-part-3-of-4"></a>教程：训练你的第一个机器学习模型（第 3 部分，共 4 部分）

本教程介绍如何在 Azure 机器学习中训练机器学习模型。

本教程是由四部分组成的系列教程的第 3 部分。你可以在其中了解 Azure 机器学习基础知识，并在 Azure 中完成基于作业的机器学习任务。 本教程的基础是你在本系列的[第 1 部分：设置](tutorial-1st-experiment-sdk-setup-local.md)和[第 2 部分：运行“Hello world!”](tutorial-1st-experiment-hello-world.md) 中完成的工作。

在本教程中，你通过提交用于训练机器学习模型的脚本来执行下一步。 此示例将有助于你了解 Azure 机器学习如何在本地调试和远程运行之间轻松实现一致的行为。

本教程介绍以下操作：

> [!div class="checklist"]
> * 创建训练脚本。
> * 使用 Conda 定义 Azure 机器学习环境。
> * 创建控制脚本。
> * 了解 Azure 机器学习类（`Environment`、`Run`、`Metrics`）。
> * 提交并运行训练脚本。
> * 在云中查看代码输出。
> * 将指标记录到 Azure 机器学习。
> * 在云中查看指标。

## <a name="prerequisites"></a>先决条件

- [Anaconda](https://www.anaconda.com/download/) 或 [Miniconda](https://www.anaconda.com/download/)，用于管理 Python 虚拟环境并安装包。
- 完成本系列的[第 1 部分](tutorial-1st-experiment-sdk-setup-local.md)和[第 2 部分](tutorial-1st-experiment-hello-world.md)。

## <a name="create-training-scripts"></a>创建训练脚本

首先，在 `model.py` 文件中定义神经网络体系结构。 所有训练代码（包括 `model.py`）都将进入 `src` 子目录。

下面的代码来自 PyTorch 中的[介绍性示例](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html)。 请注意，Azure 机器学习概念适用于任何机器学习代码，而不只是 PyTorch。

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/src/model.py":::

接下来，定义训练脚本。 此脚本通过使用 PyTorch `torchvision.dataset` API 来下载 CIFAR10 数据集，设置 `model.py` 中定义的网络，并通过使用标准 SGD 和互熵损失对该数据集进行两个时期的训练。

在 `src` 子目录中创建 `train.py` 脚本：

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/src/train.py":::

你现在具有以下目录结构：

```txt
tutorial
└──.azureml
|  └──config.json
└──src
|  └──hello.py
|  └──model.py
|  └──train.py
└──01-create-workspace.py
└──02-create-compute.py
└──03-run-hello.py
```

> [!div class="nextstepaction"]
> [我创建了训练脚本](?success=create-scripts#environment) [我遇到了一个问题](https://www.research.net/r/7CTJQQN?issue=create-scripts)

## <a name="create-a-new-python-environment"></a><a name="environment"></a> 创建新的 Python 环境

在 `.azureml` 隐藏目录中创建名为 `pytorch-env.yml` 的文件：

:::code language="yml" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/environments/pytorch-env.yml":::

此环境具有你的模型和训练脚本所需的所有依赖项。 请注意，适用于 Python 的 Azure 机器学习 SDK 没有依赖项。

> [!div class="nextstepaction"]
> [我创建了环境文件](?success=create-env-file#test-local) [我遇到了一个问题](https://www.research.net/r/7CTJQQN?issue=create-env-file)

## <a name="test-locally"></a><a name="test-local"></a> 本地测试

在终端或 Anaconda 提示符窗口中，使用以下代码在新环境中本地测试脚本。  

```bash
conda deactivate                                # If you are still using the tutorial environment, exit it
conda env create -f .azureml/pytorch-env.yml    # create the new Conda environment
conda activate pytorch-env                      # activate new Conda environment
python src/train.py                             # train model
```

运行此脚本后，你将会看到下载到名为 `tutorial/data` 的目录中的数据。

> [!div class="nextstepaction"]
> [我在本地运行代码](?success=test-local#create-local) [我遇到了一个问题](https://www.research.net/r/7CTJQQN?issue=test-local)

## <a name="create-the-control-script"></a><a name="create-local"></a> 创建控制脚本

下面的控制脚本和用于提交“Hello world!”的脚本之间的差异 在于你额外添加了几行来设置环境。

在 `tutorial` 目录中创建名为 `04-run-pytorch.py` 的新 Python 文件：

```python
# 04-run-pytorch.py
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import ScriptRunConfig

if __name__ == "__main__":
    ws = Workspace.from_config()
    experiment = Experiment(workspace=ws, name='day1-experiment-train')
    config = ScriptRunConfig(source_directory='./src',
                             script='train.py',
                             compute_target='cpu-cluster')

    # set up pytorch environment
    env = Environment.from_conda_specification(
        name='pytorch-env',
        file_path='./.azureml/pytorch-env.yml'
    )
    config.run_config.environment = env

    run = experiment.submit(config)

    aml_url = run.get_portal_url()
    print(aml_url)
```    
    
### <a name="understand-the-code-changes"></a>了解代码更改

:::row:::
   :::column span="":::
      `env = ...`
   :::column-end:::
   :::column span="2":::
      Azure 机器学习提供了[环境](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py)概念来表示一个可重现的、进行了版本控制的 Python 环境，以便用来运行试验。 从本地 Conda 或 pip 环境创建一个环境很简单。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config.run_config.environment = env`
   :::column-end:::
   :::column span="2":::
      将该环境添加到 [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py)。
   :::column-end:::
:::row-end:::

> [!div class="nextstepaction"]
> [我创建了控制脚本](?success=control-script#submit) [我遇到了一个问题](https://www.research.net/r/7CTJQQN?issue=control-script)


## <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit"></a> 将该运行提交到 Azure 机器学习

切换回教程环境，该环境安装了适用于 Python 的 Azure 机器学习 SDK。 由于训练代码未在计算机上运行，因此无需安装 PyTorch。  但你确实需要教程环境中的 `azureml-sdk`。

```bash
conda deactivate
conda activate tutorial
python 04-run-pytorch.py
```

>[!NOTE] 
> 首次运行此脚本时，Azure 机器学习会从 PyTorch 环境生成新的 Docker 映像。 完成整个运行可能需要 5 到 10 分钟。 
>
> 可以在 Azure 机器学习工作室中查看 Docker 生成日志。 单击指路向工作室的链接，选择“输出 + 日志”选项卡，然后选择 `20_image_build_log.txt`。
>
> 在将来的运行中将会重复使用此映像，以加快脚本的运行速度。

生成映像之后，请选择 `70_driver_log.txt`，以查看训练脚本的输出。

```txt
Downloading https://www.cs.toronto.edu/~kriz/cifar-10-python.tar.gz to ./data/cifar-10-python.tar.gz
...
Files already downloaded and verified
epoch=1, batch= 2000: loss 2.19
epoch=1, batch= 4000: loss 1.82
epoch=1, batch= 6000: loss 1.66
epoch=1, batch= 8000: loss 1.58
epoch=1, batch=10000: loss 1.52
epoch=1, batch=12000: loss 1.47
epoch=2, batch= 2000: loss 1.39
epoch=2, batch= 4000: loss 1.38
epoch=2, batch= 6000: loss 1.37
epoch=2, batch= 8000: loss 1.33
epoch=2, batch=10000: loss 1.31
epoch=2, batch=12000: loss 1.27
Finished Training
```

> [!WARNING]
> 如果看到错误“`Your total snapshot size exceeds the limit`”，则表明 `data` 目录位于 `ScriptRunConfig` 中使用的 `source_directory` 值中。
>
> 请将 `data` 移到 `src` 之外。

可以使用 `env.register(ws)` 将环境注册到工作区。 然后，可以轻松地对这些环境进行共享、重复使用和版本控制。 利用环境，可以轻松地重现以前的结果并与团队协作。

Azure 机器学习还维护特选环境的集合。 这些环境涵盖了常见的机器学习方案，受缓存的 Docker 映像的支持。 缓存的 Docker 映像使第一个远程运行速度更快。

简而言之，使用注册的环境可以节省时间！ 有关详细信息，请阅读[如何使用环境](./how-to-use-environments.md)。

> [!div class="nextstepaction"]
> [我提交了运行](?success=test-w-environment#log) [我遇到了一个问题](https://www.research.net/r/7CTJQQN?issue=test-w-environment)

## <a name="log-training-metrics"></a><a name="log"></a> 记录训练指标

你已在 Azure 机器学习中进行了模型训练，可以开始跟踪一些性能指标了。

当前训练脚本将指标输出到终端。 Azure 机器学习提供了一种机制，用于记录具有更多功能的指标。 通过添加几行代码，你可以在工作室中可视化指标并在多个运行之间比较指标。

### <a name="modify-trainpy-to-include-logging"></a>修改 `train.py`，使之包括日志记录

修改 `train.py` 脚本，以包含另外两行代码：

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/code/pytorch-cifar10-train-with-logging/train.py":::


#### <a name="understand-the-additional-two-lines-of-code"></a>了解这两行额外添加的代码

在 `train.py` 中，你通过使用 `Run.get_context()` 方法从训练脚本本身中访问 run 对象，并使用该对象来记录指标：

```python
# in train.py
run = Run.get_context()

...

run.log('loss', loss)
```

Azure 机器学习中的指标具有以下特点：

- 按试验和运行进行组织，因此可以轻松地跟踪和比较指标。
- 配备了一个 UI，使你能够在工作室中可视化训练性能。
- 设计用于进行扩展，因此即使在运行数百个试验的情况下，你也始终有这些优势。

> [!div class="nextstepaction"]
> [我修改了 train.py](?success=modify-train#log) [我遇到了一个问题](https://www.research.net/r/7CTJQQN?issue=modify-train)

### <a name="update-the-conda-environment-file"></a>更新 Conda 环境文件

`train.py` 脚本只有一个依赖于 `azureml.core` 的新依赖项。 更新 `pytorch-env.yml` 以反映此更改：

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/configuration/pytorch-aml-env.yml":::

> [!div class="nextstepaction"]
> [我更新了环境文件](?success=update-environment#submit-again) [我遇到了一个问题](https://www.research.net/r/7CTJQQN?issue=update-environment)

### <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit-again"></a> 将该运行提交到 Azure 机器学习
再次提交此脚本：

```bash
python 04-run-pytorch.py
```

这一次，当你访问工作室时，请转到“指标”选项卡，此时可以看到有关模型训练损失的实时更新！

:::image type="content" source="media/tutorial-1st-experiment-sdk-train/logging-metrics.png" alt-text="“指标”选项卡中的训练损失图。":::

> [!div class="nextstepaction"]
> [我重新提交了运行](?success=resubmit-with-logging#next-steps) [我遇到了一个问题](https://www.research.net/r/7CTJQQN?issue=resubmit-with-logging)

## <a name="next-steps"></a>后续步骤

在本课程中，你从基本的“Hello world!” 脚本升级成了更逼真的训练脚本，该脚本要求运行特定的 Python 环境。 你了解了如何使用 Azure 机器学习环境将本地 Conda 环境带入云中。 最后，你看到了如何通过几行代码将指标记录到 Azure 机器学习。

创建 Azure 机器学习环境还有其他方法，包括[从 pip 的 requirements.txt](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py#from-pip-requirements-name--file-path-) 文件创建，或者[从现有的本地 Conda 环境](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-)创建。

在下一课程中，你将了解如何通过将 CIFAR10 数据集上传到 Azure 来处理 Azure 机器学习中的数据。

> [!div class="nextstepaction"]
> [教程：自带数据](tutorial-1st-experiment-bring-data.md)

>[!NOTE] 
> 如果你想就此完成本教程系列，不再继续进行下一步，请记得[清理你的资源](tutorial-1st-experiment-bring-data.md#clean-up-resources)。
