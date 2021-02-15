---
title: 深度学习和人工智能框架
titleSuffix: Azure Data Science Virtual Machine
description: Azure Data Science Virtual Machine 上可用的深度学习框架和工具。
keywords: 数据科学工具, 数据科学虚拟机, 数据科学工具, Linux 数据科学
services: machine-learning
ms.service: data-science-vm
ms.custom: devx-track-python
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 3c720e2b033d07397f3b13c37e1b4e15251b0a17
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519484"
---
# <a name="deep-learning-and-ai-frameworks-for-the-azure-data-science-vm"></a>适用于 Azure Data Science VM 的深度学习和人工智能框架
下面列出了 DSVM 上的深度学习框架。

## <a name="caffe"></a>[Caffe](https://github.com/BVLC/caffe)

| 类别 | 值 |
| ------------- | ------------- |
| 支持的版本 | |
| 支持的 DSVM 版本      | Ubuntu 16.04    |
| 如何在 DSVM 上配置/安装它？  | Caffe 在 `/opt/caffe` 中安装。   示例位于 `/opt/caffe/examples` 中。|
| 运行方式      | 使用 X2Go 登录 VM，然后启动新终端并输入以下内容：<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>系统将打开显示示例笔记本的新浏览器窗口。 二进制文件安装在 /opt/caffe/build/install/bin 中。<br/><br/>已安装的 Caffe 版本需要使用 Python 2.7，不会使用默认激活的 Python 3.5。 要切换到 Python 2.7，请运行 `source activate root` 以切换到 Anaconda 环境。|    

## <a name="caffe2"></a>[Caffe2](https://github.com/caffe2/caffe2)

| 类别 | 值 |
| ------------- | ------------- |
| 支持的版本 | |
| 支持的 DSVM 版本      | Ubuntu 16.04     |
| 如何在 DSVM 上配置/安装它？  | Caffe2 安装在 [Python 2.7 (root) conda 环境中。 |
| 运行方式      | 终端：启动 Python，并导入 Caffe2。 <br/> * JupyterHub：[连接到 JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)，然后导航到 Caffe2 目录，以查找示例笔记本。 某些笔记本需要在 Python 代码中设置 Caffe2 根；输入 /opt/caffe2。 |

## <a name="chainer"></a>[Chainer](https://chainer.org/)

| 类别 | 值 |
| ------------- | ------------- |
| 支持的版本 | 5.2 |
| 支持的 DSVM 版本      | Ubuntu 16.04    |
| 如何在 DSVM 上配置/安装它？  | Chainer 安装在 Python 3.5 中。 |
| 运行方式      | 终端：激活 Python 3.5 环境，运行 `python`，然后运行 `import chainer`。 <br/> * JupyterHub：[连接到 JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)，然后导航到 Chainer 目录，以查找示例笔记本。| 

## <a name="cuda-cudnn-nvidia-driver"></a>[CUDA、cuDNN、NVIDIA 驱动程序](https://developer.nvidia.com/cuda-toolkit)

| 类别 | 值 |
| ------------- | ------------- |
| 支持的版本 | 10.0.130|
| 支持的 DSVM 版本      | Windows Server 2019<br>Ubuntu 18.04<br> Windows 2016<br> Ubuntu 16.04  |
| 如何在 DSVM 上配置/安装它？  |_nvidia-smi_ 在系统路径上可用。  |
| 运行方式      | 打开命令提示符（在 Windows 上）或终端（在 Linux 上），然后运行 nvidia-smi。 |


## <a name="horovod"></a>[Horovod](https://github.com/uber/horovod)

| 类别 | 值 |
| ------------- | ------------- |
| 支持的版本 | 0.16.1|
| 支持的 DSVM 版本      | Ubuntu 18.04<br> Ubuntu 16.04   |
| 如何在 DSVM 上配置/安装它？  | Horovod 安装在 Python 3.5 中 |
| 运行方式      | 在终端上激活正确的环境，然后运行 Python。 |

## <a name="keras"></a>[Keras](https://keras.io/)

| 类别 | 值 |
| ------------- | ------------- |
| 支持的版本 | 2.2.4 |
| 支持的 DSVM 版本      | Windows Server 2019<br>Ubuntu 18.04<br> Windows 2016 <br> Ubuntu 16.04   |
| 如何在 DSVM 上配置/安装它？  | Keras 在 Windows 上安装于 Python 3.6 中，在 Linux 上安装于 Python 3.5 中 |
| 运行方式      | 在终端上激活正确的环境，然后运行 Python。 |

## <a name="microsoft-cognitive-toolkit-cntk"></a>[Microsoft Cognitive Toolkit (CNTK)](/cognitive-toolkit/)

| 类别 | 值 |
| ------------- | ------------- |
| 支持的版本 | 2.5.1 |
| 支持的 DSVM 版本      | Windows 2016 <br> Ubuntu 16.04   |
| 如何在 DSVM 上配置/安装它？  | CNTK 在 [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) 上安装于 Python 3.6 中，在 [Linux](./dsvm-tools-languages.md#python-linux-edition) 上安装于 Python 3.5 中 |
| 运行方式      | 终端：激活正确的环境，然后运行 Python。 <br/>Jupyter：连接到 [Jupyter](provision-vm.md) 或 [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)，然后打开 CNTK 目录获取示例。 |

## <a name="mxnet"></a>[MXNet](https://mxnet.apache.org/)
| 类别 | 值 |
| ------------- | ------------- |
| 支持的版本 | 1.3.0 |
| 支持的 DSVM 版本      | Windows 2016 <br> Ubuntu 16.04    |
| 如何在 DSVM 上配置/安装它？  | MXNet 在 Windows 上安装于 `C:\dsvm\tools\mxnet` 中，在 Ubuntu 上安装于 `/dsvm/tools/mxnet` 中。 Python 绑定在 [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) 上安装于 Python 3.6 中，在 [Linux](./dsvm-tools-languages.md#python-linux-edition) 上安装于 Python 3.5 中。Ubuntu DSVM 中也包含 R 绑定。 |
| 运行方式      | 终端：激活正确的 conda 环境，然后运行 `import mxnet`。 <br/>Jupyter：连接到 [Jupyter](provision-vm.md#access-the-dsvm) 或 [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)，然后打开 `mxnet` 目录获取示例。 |

## <a name="mxnet-model-server"></a>[MXNet 模型服务器](https://github.com/awslabs/mxnet-model-server#quick-start)

| 类别 | 值 |
| ------------- | ------------- |
| 支持的版本 | 1.0.1 |
| 支持的 DSVM 版本      | Windows 2016 <br> Ubuntu 16.04    |
| 如何在 DSVM 上配置/安装它？  | MXNet 模型服务器在 [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) 上安装于 Python 3.6 中，在 [Linux](./dsvm-tools-languages.md#python-linux-edition) 上安装于 Python 3.5 中 |
| 运行方式      | 终端：首先，运行 `sudo systemctl stop jupyterhub` 以停止 JupyterHub 服务，因为这两者侦听相同的端口。 然后，激活正确的 conda 环境，并运行 `mxnet-model-server --start --models squeezenet=https://s3.amazonaws.com/model-server/model_archive_1.0/squeezenet_v1.1.mar` |

## <a name="nvidia-system-management-interface-nvidia-smi"></a>[NVidia System Management Interface (nvidia-smi)](https://developer.nvidia.com/nvidia-system-management-interface)

| 类别 | 值 |
| ------------- | ------------- |
| 支持的版本 |  |
| 支持的 DSVM 版本      | Windows Server 2019<br>Ubuntu 18.04<br> Windows 2016<br> Ubuntu 16.04   |
| 用途 | 用于查询 GPU 活动的 NVIDIA 工具 |
| 如何在 DSVM 上配置/安装它？  | `nvidia-smi` 位于系统路径上。 |
| 运行方式      | 在具有 GPU 的虚拟机上，打开命令提示符（在 Windows 上）或终端（在 Linux 上），然后运行 `nvidia-smi`。 |

## <a name="pytorch"></a>[PyTorch](https://pytorch.org/)

| 类别 | 值 |
| ------------- | ------------- |
| 支持的版本 | 1.2.0 (Ubuntu 16.04)、1.4.0（Ubuntu 18.04、Windows 2019） |
| 支持的 DSVM 版本      | Windows Server 2019<br>Ubuntu 18.04<br> Ubuntu 16.04 |
| 如何在 DSVM 上配置/安装它？  | 安装在 [Python 3.5](dsvm-tools-languages.md#python-linux-edition) 中。 示例 Jupyter 笔记本包含在内，并且示例位于 /dsvm/samples/pytorch 中。 |
| 运行方式      | 终端：激活正确的环境，然后运行 Python。<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)：进行连接，然后打开 PyTorch 目录获取示例。  |

## <a name="tensorflow"></a>[TensorFlow](https://www.tensorflow.org/)

| 类别 | 值 |
| ------------- | ------------- |
| 支持的版本 | 1.13 |
| 支持的 DSVM 版本      | Windows Server 2019<br>Ubuntu 18.04<br> Windows 2016 <br> Ubuntu 16.04 |
| 如何在 DSVM 上配置/安装它？  | 在 [Linux](dsvm-tools-languages.md#python-linux-edition) 上安装于 Python 3.5 中，在 [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) 上安装于 Python 3.6 中 |
| 运行方式      | 终端：激活正确的环境，然后运行 Python。 <br/> * Jupyter：连接到 [Jupyter](provision-vm.md) 或 [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)，然后打开 TensorFlow 目录获取示例。   |

## <a name="tensorflow-serving"></a>[TensorFlow Serving](https://www.tensorflow.org/serving/)

| 类别 | 值 |
| ------------- | ------------- |
| 支持的版本 | 1.12 |
| 支持的 DSVM 版本      | Ubuntu 16.04 |
| 如何在 DSVM 上配置/安装它？  | 在终端提供了 tensorflow_model_server。 |
| 运行方式      |  可以[联机](https://www.tensorflow.org/serving/)获取示例。   |


## <a name="theano"></a>[Theano](https://github.com/Theano/Theano)

| 类别 | 值 |
| ------------- | ------------- |
| 支持的版本 | 1.0.3 |
| 支持的 DSVM 版本      | Ubuntu 16.04 |
| 如何在 DSVM 上配置/安装它？  |Theano 安装在 Python 2.7 (root) 以及 Python 3.5 (py35) 环境中。 |
| 运行方式      |  终端：激活所需的 Python 版本（root 或 py35），运行 python，然后导入 Theano。<br/>* Jupyter：选择 Python 2.7 或 3.5 内核，然后导入 Theano。  <br/>要解决最近的数学内核库 (MKL) bug，首先需要设置 MKL 线程层，如下所示：<br/><br/>`export MKL_THREADING_LAYER=GNU`  |