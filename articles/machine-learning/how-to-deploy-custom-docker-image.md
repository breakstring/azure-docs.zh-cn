---
title: 使用自定义 Docker 映像部署模型
titleSuffix: Azure Machine Learning
description: 了解如何使用自定义 Docker 基本映像部署 Azure 机器学习模型。 虽然 Azure 机器学习提供了默认的基础映像，但你也可以使用自己的基础映像。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.reviewer: larryfr
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy, devx-track-azurecli
ms.openlocfilehash: 1ff4d7693a7e493ccb736ab9363fd26c93017c79
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2020
ms.locfileid: "94695344"
---
# <a name="deploy-a-model-using-a-custom-docker-base-image"></a>使用自定义 Docker 基础映像部署模型

了解如何在使用 Azure 机器学习部署已训练的模型时使用自定义 Docker 基础映像。

如果未指定 Docker 基础映像，Azure 机器学习将使用默认的 Docker 基础映像。 可以找到与 `azureml.core.runconfig.DEFAULT_CPU_IMAGE` 一起使用的特定 Docker 映像。 你还可以使用 Azure 机器学习环境来选择特定的基础映像，或者使用自己的基础映像。

为部署创建映像时，可以从基础映像着手。 基础映像提供基本的操作系统和组件。 然后，部署过程会将其他组件（如模型、Conda 环境和其他资产）添加到该映像中。

通常情况下，如果希望使用 Docker 来管理依赖项、对组件版本进行更严格的控制或节省部署时间，则可以创建自定义基础映像。 你可能还希望安装模型所需的软件，而安装过程需要很长时间。 如果在创建基础映像时安装软件，你就不必为每个部署安装它。

> [!IMPORTANT]
> 部署模型时，不能覆盖核心组件，如 Web 服务器或 IoT Edge 组件。 这些组件提供已知的工作环境并由 Microsoft 进行测试和支持。

> [!WARNING]
> Microsoft 可能无法帮助解决由自定义映像引起的问题。 如果遇到问题，请使用默认映像或 Microsoft 提供的映像之一，了解该问题是否特定于你的映像。

本文档分为两个部分：

* 创建自定义基础映像：为管理员和 DevOps 提供有关使用 Azure CLI 和机器学习 CLI 创建自定义映像和配置 Azure 容器注册表的身份验证的信息。
* 使用自定义基础映像部署模型：向数据科学家和 DevOps/ML 工程师提供有关在从 Python SDK 或 ML CLI 部署定型模型时使用自定义映像的信息。

## <a name="prerequisites"></a>先决条件

* Azure 机器学习工作区。 有关详细信息，请参阅[创建工作区](how-to-manage-workspace.md)一文。
* [Azure 机器学习 SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)。 
* [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)。
* [用于 Azure 机器学习的 CLI 扩展](reference-azure-machine-learning-cli.md)。
* 可在 Internet 上访问的 [Azure 容器注册表](../container-registry/index.yml)或其他 Docker 注册表。
* 本文档中的步骤假设你熟悉如何创建和使用“推理配置”对象作为模型部署的一部分。 有关详细信息，请参阅[部署位置及方式](how-to-deploy-and-where.md)。

## <a name="create-a-custom-base-image"></a>创建自定义基础映像

本部分中的信息假设你正在使用 Azure 容器注册表存储 Docker 映像。 计划为 Azure 机器学习创建自定义映像时，请使用以下清单：

* 你将使用为 Azure 机器学习工作区创建的 Azure 容器注册表，还是使用独立的 Azure 容器注册表？

    使用存储在工作区的容器注册表中的映像时，不需要对注册表进行身份验证。 身份验证由工作区处理。

    > [!WARNING]
    > 首次使用工作区训练或部署模型时，将创建工作区的 Azure 容器注册表。 如果你创建了一个新的工作区，但没有训练或创建模型，则该工作区将不存在 Azure 容器注册表。

    使用存储在“独立容器注册表”中的映像时，需要配置至少具有读取访问权限的服务主体。 然后向使用注册表中的映像的任何人提供服务主体 ID（用户名）和密码。 但你使容器注册表可公开访问的情况例外。

    有关如何创建专用 Azure 容器注册表的信息，请参阅[创建专用容器注册表](../container-registry/container-registry-get-started-azure-cli.md)。

    有关在 Azure 容器注册表中使用服务主体的信息，请参阅[使用服务主体的 Azure 容器注册表身份验证](../container-registry/container-registry-auth-service-principal.md)。

* Azure 容器注册表和映像信息：请为需要使用映像的任何人提供映像名。 例如，使用名为 `myimage` 的映像（存储在名为 `myregistry` 的注册表中）进行模型部署时，该映像将被引用为 `myregistry.azurecr.io/myimage`

### <a name="image-requirements"></a>图像要求

Azure 机器学习仅支持提供以下软件的 Docker 映像：
* Ubuntu 16.04 或更高版本。
* Conda 4.5.# 或更高版本。
* Python 3.5+。

若要使用数据集，请安装 libfuse-dev 包。 另外，请确保安装你可能需要的所有用户空间包。

Azure ML 会维护一组发布到 Microsoft 容器注册表的 CPU 和 GPU 基础映像，你可以选择利用（或引用）这些映像，而不是创建自己的自定义映像。 若要查看这些映像的 Dockerfile，请参考 [Azure/AzureML-Container](https://github.com/Azure/AzureML-Containers) GitHub 存储库。

对于 GPU 映像，Azure ML 目前同时提供了 cuda9 和 cuda10 基础映像。 这些基础映像中安装的主要依赖项包括：

| 依赖项 | IntelMPI CPU | OpenMPI CPU | IntelMPI GPU | OpenMPI GPU |
| --- | --- | --- | --- | --- |
| miniconda | ==4.5.11 | ==4.5.11 | ==4.5.11 | ==4.5.11 |
| mpi | intelmpi==2018.3.222 |openmpi==3.1.2 |intelmpi==2018.3.222| openmpi==3.1.2 |
| cuda | - | - | 9.0/10.0 | 9.0/10.0/10.1 |
| cudnn | - | - | 7.4/7.5 | 7.4/7.5 |
| nccl | - | - | 2.4 | 2.4 |
| git | 2.7.4 | 2.7.4 | 2.7.4 | 2.7.4 |

CPU 映像从 ubuntu16.04 生成。 cuda9 的 GPU 映像从 nvidia/cuda:9.0-cudnn7-devel-ubuntu16.04 生成。 cuda10 的 GPU 映像从 nvidia/cuda:10.0-cudnn7-devel-ubuntu16.04 生成。
<a id="getname"></a>

> [!IMPORTANT]
> 使用自定义 Docker 映像时，建议固定包版本，以便更好地确保可再现性。

### <a name="get-container-registry-information"></a>获取容器注册表信息

本部分介绍如何获取 Azure 机器学习工作区的 Azure 容器注册表的名称。

> [!WARNING]
> 首次使用工作区训练或部署模型时，将创建工作区的 Azure 容器注册表。 如果你创建了一个新的工作区，但没有训练或创建模型，则该工作区将不存在 Azure 容器注册表。

如果已使用 Azure 机器学习训练或部署了模型，则会为你的工作区创建容器注册表。 若要查找此容器注册表的名称，请使用以下步骤：

1. 打开新的 shell 或命令提示符，并使用以下命令对 Azure 订阅进行身份验证：

    ```azurecli-interactive
    az login
    ```

    按照提示对订阅进行身份验证。

    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

2. 使用以下命令列出工作区的容器注册表。 将 `<myworkspace>` 替换为 Azure 机器学习工作区名称。 将 `<resourcegroup>` 替换为包含工作区的 Azure 资源组：

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

    返回的信息类似于下文：

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    `<registry_name>` 值是工作区的 Azure 容器注册表的名称。

### <a name="build-a-custom-base-image"></a>生成自定义基础映像

本部分中的步骤将介绍如何在 Azure 容器注册表中创建自定义 Docker 映像。 有关示例 dockerfile，请参阅 [Azure/AzureML-Containers](https://github.com/Azure/AzureML-Containers) GitHub 存储库。

1. 创建名为 `Dockerfile` 的新文本文件，并将以下文本用作内容：

    ```text
    FROM ubuntu:16.04

    ARG CONDA_VERSION=4.7.12
    ARG PYTHON_VERSION=3.7
    ARG AZUREML_SDK_VERSION=1.13.0
    ARG INFERENCE_SCHEMA_VERSION=1.1.0

    ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
    ENV PATH /opt/miniconda/bin:$PATH
    ENV DEBIAN_FRONTEND=noninteractive

    RUN apt-get update --fix-missing && \
        apt-get install -y wget bzip2 && \
        apt-get install -y fuse && \
        apt-get clean -y && \
        rm -rf /var/lib/apt/lists/*

    RUN useradd --create-home dockeruser
    WORKDIR /home/dockeruser
    USER dockeruser

    RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-x86_64.sh -O ~/miniconda.sh && \
        /bin/bash ~/miniconda.sh -b -p ~/miniconda && \
        rm ~/miniconda.sh && \
        ~/miniconda/bin/conda clean -tipsy
    ENV PATH="/home/dockeruser/miniconda/bin/:${PATH}"

    RUN conda install -y conda=${CONDA_VERSION} python=${PYTHON_VERSION} && \
        pip install azureml-defaults==${AZUREML_SDK_VERSION} inference-schema==${INFERENCE_SCHEMA_VERSION} &&\
        conda clean -aqy && \
        rm -rf ~/miniconda/pkgs && \
        find ~/miniconda/ -type d -name __pycache__ -prune -exec rm -rf {} \;
    ```

2. 在 shell 或命令提示符下，使用以下命令对 Azure 容器注册表进行身份验证。 将 `<registry_name>` 替换为要将映像存储在其中的容器注册表的名称：

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. 若要上传 Dockerfile 并生成它，请使用以下命令。 将 `<registry_name>` 替换为要将映像存储在其中的容器注册表的名称：

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    > [!TIP]
    > 在此示例中，标记 `:v1` 将应用于映像。 如果未提供标记，则应用标记 `:latest`。

    在生成过程中，信息将被流式传输回命令行。 如果生成成功，你将收到类似于以下文本的消息：

    ```text
    Run ID: cda was successful after 2m56s
    ```

如需深入了解如何使用 Azure 容器注册表生成映像，请参阅[使用 Azure 容器注册表任务](../container-registry/container-registry-quickstart-task-cli.md)生成和运行容器映像

如需深入了解如何将现有映像上传到 Azure 容器注册表，请参阅[将首个映像推送到专用 Docker 容器注册表](../container-registry/container-registry-get-started-docker-cli.md)。

## <a name="use-a-custom-base-image"></a>使用自定义基础映像

若要使用自定义映像，需要以下信息：

* 映像名称。 例如，`mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest` 是 Microsoft 提供的简单 Docker 映像的路径。

    > [!IMPORTANT]
    > 对于已创建的自定义映像，请确保包含用于该映像的任何标记。 例如，如果映像是使用特定标记（如 `:v1`）创建的。 如果创建映像时未使用特定标记，则应用标记 `:latest`。

* 如果映像位于“专用存储库”中，则需要以下信息：

    * 注册表地址。 例如，`myregistry.azureecr.io`。
    * 具有注册表读取权限的服务主体“用户名”和“密码” 。

    如果没有此信息，请与管理员联系，获取包含映像的 Azure 容器注册表。

### <a name="publicly-available-base-images"></a>公开发布的基础映像

Microsoft 在可公开访问的存储库中提供了多个 docker 映像，可按照本节中的步骤使用这些映像：

| 映像 | 说明 |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Azure 机器学习的核心映像 |
| `mcr.microsoft.com/azureml/onnxruntime:latest` | 包含用于 CPU 推理的 ONNX 运行时 |
| `mcr.microsoft.com/azureml/onnxruntime:latest-cuda` | 包含用于 GPU 的 ONNX 运行时和 CUDA |
| `mcr.microsoft.com/azureml/onnxruntime:latest-tensorrt` | 包含用于 GPU 的 ONNX 运行时和 TensorRT |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-vadm` | 包含用于基于 Movidius<sup>TM</sup> MyriadX VPU 的 Intel<sup></sup> Vision Accelerator Design 的 ONNX 运行时和 OpenVINO |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-myriad` | 包含用于 Intel<sup></sup> Movidius<sup>TM</sup> U 盘的 ONNX 运行时和 OpenVINO |

有关 ONNX 运行时基础映像的更多信息，请参阅 GitHub 存储库中的 [ONNX 运行时 dockerfile 部分](https://github.com/microsoft/onnxruntime/blob/master/dockerfiles/README.md)。

> [!TIP]
> 由于这些映像是公开可用的，因此在使用它们时不需要提供地址、用户名或密码。

有关详细信息，请参阅 GitHub 上的 [Azure 机器学习容器](https://github.com/Azure/AzureML-Containers)存储库。

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>将映像与 Azure 机器学习 SDK 结合使用

若要使用存储在工作区的 Azure 容器注册表中的映像，或使用存储在可公开访问的容器注册表中的映像，请设置以下[环境](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py)属性 ：

+ `docker.enabled=True`
+ `docker.base_image`：设置为注册表和映像的路径。

```python
from azureml.core.environment import Environment
# Create the environment
myenv = Environment(name="myenv")
# Enable Docker and reference an image
myenv.docker.enabled = True
myenv.docker.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest"
```

若要使用不在工作区中的专用容器注册表中的映像，必须使用 `docker.base_image_registry` 指定存储库的地址以及用户名和密码：

```python
# Set the container registry information
myenv.docker.base_image_registry.address = "myregistry.azurecr.io"
myenv.docker.base_image_registry.username = "username"
myenv.docker.base_image_registry.password = "password"

myenv.inferencing_stack_version = "latest"  # This will install the inference specific apt packages.

# Define the packages needed by the model and scripts
from azureml.core.conda_dependencies import CondaDependencies
conda_dep = CondaDependencies()
# you must list azureml-defaults as a pip dependency
conda_dep.add_pip_package("azureml-defaults")
myenv.python.conda_dependencies=conda_dep
```

必须添加版本 >= 1.0.45 的 azureml-defaults 作为 pip 依赖项。 此包包含将模型作为 Web 服务托管时所需的功能。 还必须将环境的 inferencing_stack_version 属性设置为“latest”，这将安装 Web 服务所需的特定 apt 包。 

定义环境后，将其与 [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig?preserve-view=true&view=azure-ml-py) 对象一起使用，以定义模型和 Web 服务将在其中运行的推理环境。

```python
from azureml.core.model import InferenceConfig
# Use environment in InferenceConfig
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

此时，你可以继续进行部署。 例如，以下代码片段将使用推理配置和自定义映像在本地部署 Web 服务：

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

有关部署的详细信息，请参阅[使用 Azure 机器学习部署模型](how-to-deploy-and-where.md)。

有关自定义 Python 环境的详细信息，请参阅[创建和管理用于训练和部署的环境](how-to-use-environments.md)。 

### <a name="use-an-image-with-the-machine-learning-cli"></a>将映像与机器学习 CLI 结合使用

> [!IMPORTANT]
> 目前，机器学习 CLI 可以使用来自工作区的 Azure 容器注册表或可公开访问的存储库的映像。 而不能使用来自独立的专用注册表的映像。

使用机器学习 CLI 部署模型之前，请创建一个使用自定义映像的[环境](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py)。 然后创建引用该环境的推理配置文件。 也可以在推理配置文件中直接定义环境。 下面的 JSON 文档演示了如何在公共容器注册表中引用映像。 在本示例中，以内联方式定义环境：

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest",
            "enabled": false,
            "sharedVolumes": true,
            "shmSize": null
        },
        "environmentVariables": {
            "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
        },
        "name": "my-deploy-env",
        "python": {
            "baseCondaEnvironment": null,
            "condaDependencies": {
                "channels": [
                    "conda-forge"
                ],
                "dependencies": [
                    "python=3.6.2",
                    {
                        "pip": [
                            "azureml-defaults",
                            "azureml-telemetry",
                            "scikit-learn",
                            "inference-schema[numpy-support]"
                        ]
                    }
                ],
                "name": "project_environment"
            },
            "condaDependenciesFile": null,
            "interpreterPath": "python",
            "userManagedDependencies": false
        },
        "version": "1"
    }
}
```

此文件与 `az ml model deploy` 命令一起使用。 `--ic` 参数用于指定推理配置文件。

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

有关使用 ML CLI 部署模型的详细信息，请参阅 [Azure 机器学习的 CLI 扩展](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment)一文的“模型注册、分析和部署”部分。

## <a name="next-steps"></a>后续步骤

* 详细了解[部署位置和方式](how-to-deploy-and-where.md)。
* 了解如何[使用 Azure Pipelines 训练和部署机器学习模型](/azure/devops/pipelines/targets/azure-machine-learning?view=azure-devops&preserve-view=true)。