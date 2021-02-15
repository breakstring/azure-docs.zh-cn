---
title: 教程：运行“Hello world!” Python 脚本
titleSuffix: Azure Machine Learning
description: Azure 机器学习入门系列的第 2 部分介绍了如何将简单的“Hello world!” Python 脚本提交到云中。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: 43a483f49a9e9004a4f487e82195198f2600a919
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2021
ms.locfileid: "98071147"
---
# <a name="tutorial-run-a-hello-world-python-script-part-2-of-4"></a>教程：运行“Hello world!” Python 脚本（第 2 部分，共 4 部分）

在本教程中，你将了解如何使用适用于 Python 的 Azure 机器学习 SDK 提交并运行 Python“Hello world!” 脚本。

本教程是由四部分组成的系列教程的第 2 部分，你可以在其中了解 Azure 机器学习基础知识，并在 Azure 中完成基于作业的机器学习任务。 本教程的基础是你在[第 1 部分：为 Azure 机器学习设置本地计算机](tutorial-1st-experiment-sdk-setup-local.md)中完成的工作。

在本教程中，将：

> [!div class="checklist"]
> * 在本地创建并运行“Hello world!” Python 脚本。
> * 创建一个 Python 控制脚本，以将“Hello world!”提交 到 Azure 机器学习。
> * 了解控制脚本中的 Azure 机器学习概念。
> * 提交并运行“Hello world!” 脚本。
> * 在云中查看代码输出。

## <a name="prerequisites"></a>先决条件

- 如果你还没有 Azure 机器学习工作区，请先完成[第 1 部分](tutorial-1st-experiment-sdk-setup-local.md)。

## <a name="create-and-run-a-python-script-locally"></a>在本地创建并运行一个 Python 脚本

在 `tutorial` 目录下创建一个新的名为 `src` 的子目录，用于存储要在 Azure 机器学习计算群集上运行的代码。 在 `src` 子目录中，创建 `hello.py` Python 脚本：

```python
# src/hello.py
print("Hello world!")
```

你的项目目录结构现在将如下所示：

```Bash
tutorial
└──.azureml
|  └──config.json
└──src
|  └──hello.py
└──01-create-workspace.py
└──02-create-compute.py
```


### <a name="test-your-script-locally"></a><a name="test"></a>在本地测试你的脚本

你可以通过使用你常用的 IDE 或终端在本地运行代码。 在本地运行代码具有可对代码进行交互式调试的好处。  在已激活 tutorial1 conda 环境的窗口中，运行 Python 文件：

```bash
cd <path/to/tutorial>
python ./src/hello.py
```

> [!div class="nextstepaction"]
> [我在本地运行脚本](?success=run-local#control-script) [我遇到了一个问题](https://www.research.net/r/7C2NTH7?issue=run-local)

## <a name="create-a-control-script"></a><a name="control-script"></a> 创建控制脚本

使用控制脚本，你可以在云中运行 `hello.py` 脚本。 可以使用控制脚本来控制如何运行以及在何处运行机器学习代码。  

在你的教程目录中，创建名为 `03-run-hello.py` 的新 Python 文件，然后将以下代码复制并粘贴到该文件中：

```python
# tutorial/03-run-hello.py
from azureml.core import Workspace, Experiment, Environment, ScriptRunConfig

ws = Workspace.from_config()
experiment = Experiment(workspace=ws, name='day1-experiment-hello')

config = ScriptRunConfig(source_directory='./src', script='hello.py', compute_target='cpu-cluster')

run = experiment.submit(config)
aml_url = run.get_portal_url()
print(aml_url)
```

### <a name="understand-the-code"></a>了解代码

下面是有关控制脚本工作方式的说明：

:::row:::
   :::column span="":::
      `ws = Workspace.from_config()`
   :::column-end:::
   :::column span="2":::
      [工作区](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py)连接到你的 Azure 机器学习工作区，以便你可以与 Azure 机器学习资源通信。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `experiment =  Experiment( ... )`
   :::column-end:::
   :::column span="2":::
      可以通过[试验](/python/api/azureml-core/azureml.core.experiment.experiment?preserve-view=true&view=azure-ml-py)轻松地将多个运行组织到单个名称下。 稍后，你可以了解如何使用试验轻松地在数十个运行之间比较指标。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig( ... )` 
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) 包装你的 `hello.py` 代码并将其传递到你的工作区。 顾名思义，你可以使用此类来配置你希望脚本如何在 Azure 机器学习中运行。   此类还会指定该脚本将会在哪个计算目标上运行。 在此代码中，目标是在[设置教程](tutorial-1st-experiment-sdk-setup-local.md)中创建的计算群集。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `run = experiment.submit(config)`
   :::column-end:::
   :::column span="2":::
       提交脚本。 此提交被称为[运行](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py)。 运行会封装你的代码的单次执行。 可以使用运行来监视脚本进度、捕获输出、分析结果、将指标可视化，等等。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `aml_url = run.get_portal_url()` 
   :::column-end:::
   :::column span="2":::
        `run` 对象提供了一个便于执行你的代码的句柄。 可以使用从 Python 脚本输出的 URL 从 Azure 机器学习工作室中监视代码执行进度。  
   :::column-end:::
:::row-end:::

> [!div class="nextstepaction"]
> [我创建了控制脚本](?success=create-control-script#submit) [我遇到了一个问题](https://www.research.net/r/7C2NTH7?issue=create-control-script)

## <a name="submit-and-run-your-code-in-the-cloud"></a><a name="submit"></a> 在云中提交并运行代码

运行你的控制脚本，该脚本继而会在[设置教程](tutorial-1st-experiment-sdk-setup-local.md)中创建的计算群集上运行 `hello.py`。


```bash
python 03-run-hello.py
```

> [!TIP]
> 如果运行此代码返回错误“你没有访问订阅的权限”，请参阅[连接到工作区](how-to-manage-workspace.md?tab=python#connect-multi-tenant)，以获取有关身份验证选项的信息。

> [!div class="nextstepaction"]
> [我在云中提交了代码](?success=submit-to-cloud#monitor) [我遇到了一个问题](https://www.research.net/r/7C2NTH7?issue=submit-to-cloud)

## <a name="monitor-your-code-in-the-cloud-by-using-the-studio"></a><a name="monitor"></a>通过使用工作室在云中监视代码

来自脚本的输出中将包含一个指向工作室的链接，该链接类似于：`https://ml.azure.com/experiments/hello-world/runs/<run-id>?wsid=/subscriptions/<subscription-id>/resourcegroups/<resource-group>/workspaces/<workspace-name>`。

访问该链接。  首先，你会看到状态为“正在准备”。  首次运行将需要 5 - 10 分钟才能完成。 这是因为发生了以下情况：

* docker 映像在云中构建
* 计算群集的大小从 0 个节点调整到 1 个节点
* docker 映像被下载到该计算。 

由于 docker 映像已缓存在计算中，因此后续运行要快得多（约 15 秒）。 可以通过在第一次运行完成后重新提交以下代码来进行测试。

作业完成后，转到“输出 + 日志”选项卡。在那里可以看到 `70_driver_log.txt` 文件，该文件的内容类似于：

```txt
 1: [2020-08-04T22:15:44.407305] Entering context manager injector.
 2: [context_manager_injector.py] Command line Options: Namespace(inject=['ProjectPythonPath:context_managers.ProjectPythonPath', 'RunHistory:context_managers.RunHistory', 'TrackUserError:context_managers.TrackUserError', 'UserExceptions:context_managers.UserExceptions'], invocation=['hello.py'])
 3: Starting the daemon thread to refresh tokens in background for process with pid = 31263
 4: Entering Run History Context Manager.
 5: Preparing to call script [ hello.py ] with arguments: []
 6: After variable expansion, calling script [ hello.py ] with arguments: []
 7:
 8: Hello world!
 9: Starting the daemon thread to refresh tokens in background for process with pid = 31263
10:
11:
12: The experiment completed successfully. Finalizing run...
13: Logging experiment finalizing status in history service.
14: [2020-08-04T22:15:46.541334] TimeoutHandler __init__
15: [2020-08-04T22:15:46.541396] TimeoutHandler __enter__
16: Cleaning up all outstanding Run operations, waiting 300.0 seconds
17: 1 items cleaning up...
18: Cleanup took 0.1812913417816162 seconds
19: [2020-08-04T22:15:47.040203] TimeoutHandler __exit__
```

在第 8 行，你会看到“Hello world!” 输出。

`70_driver_log.txt` 文件包含来自运行的标准输出。 当你在云中调试远程运行时，此文件会很有用。

> [!div class="nextstepaction"]
> [我在工作室看到了该日志](?success=monitor-in-studio#next-steps) [我遇到了一个问题](https://www.research.net/r/7C2NTH7?issue=monitor-in-studio)

## <a name="next-steps"></a>后续步骤

在本教程中，你使用了一个简单的“hello world!” 脚本，并在 Azure 上运行了该脚本。 你了解了如何连接到 Azure 机器学习工作区、如何创建试验，以及如何将 `hello.py` 代码提交到云中。

在接下来的教程中，你将在这些知识的基础上通过运行比 `print("Hello world!")` 更有趣的脚本来进一步学习。

> [!div class="nextstepaction"]
> [教程：定型模型](tutorial-1st-experiment-sdk-train.md)

>[!NOTE] 
> 如果你想就此完成本教程系列，不再继续进行下一步，请记得[清理你的资源](tutorial-1st-experiment-bring-data.md#clean-up-resources)。