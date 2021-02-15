---
title: 快速入门 - 在 Azure 中按需生成容器映像
description: 使用 Azure 容器注册表命令可以在 Azure 云中按需快速生成、推送和运行 Docker 容器映像。
ms.topic: quickstart
ms.date: 09/25/2020
ms.custom: contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: c6fe1fc246d112218b492072155175b2db99c8c9
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97032943"
---
# <a name="quickstart-build-and-run-a-container-image-using-azure-container-registry-tasks"></a>快速入门：使用 Azure 容器注册表任务生成和运行容器映像

在本快速入门中，你将使用 [Azure 容器注册表任务][container-registry-tasks-overview]命令在 Azure 中以本机方式快速生成、推送和运行 Docker 容器映像，而无需本地安装 Docker。 ACR 任务是 Azure 容器注册表中的一套功能，可在整个容器生命周期内帮助管理和修改容器映像。 此示例说明如何使用本地 Dockerfile 通过按需生成将“内部循环”容器映像开发周期转移到云中。 

完成本快速入门后，请使用[教程](container-registry-tutorial-quick-task.md)探索 ACR 任务的更多高级功能。 ACR 任务可以基于代码提交或基础映像更新自动生成映像，或者同时测试多个容器，此外还支持其他一些方案。 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]
    
- 本快速入门需要 Azure CLI 2.0.58 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="create-a-resource-group"></a>创建资源组

如果还没有容器注册表，请先使用 [az group create][az-group-create] 命令创建一个资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

以下示例在“eastus”位置创建名为“myResourceGroup”的资源组。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>创建容器注册表

使用 [az acr create][az-acr-create] 命令创建容器注册表。 注册表名称在 Azure 中必须唯一，并且包含 5-50 个字母数字字符。 以下示例使用 *myContainerRegistry008*。 将其更新为唯一值。

```azurecli-interactive
az acr create --resource-group myResourceGroup \
  --name myContainerRegistry008 --sku Basic
```

此示例创建一个基本注册表，这是为了解 Azure 容器注册表的开发人员提供的成本优化选项。 有关可用服务层级的详细信息，请参阅[容器注册表服务层级][container-registry-skus]。

## <a name="build-and-push-image-from-a-dockerfile"></a>从 Dockerfile 生成和推送映像

现在，请使用 Azure 容器注册表来生成和推送映像。 首先创建一个本地工作目录，然后创建一个名为“Dockerfile”的 Dockerfile，其中只有一行内容：`FROM mcr.microsoft.com/hello-world`。 这是一个从 Microsoft 容器注册表中托管的 `hello-world` 映像生成 Linux 容器映像的简单示例。 你可以创建自己的标准 Dockerfile 并为其他平台生成映像。 如果使用 bash shell，请使用以下命令创建 Dockerfile：

```bash
echo FROM mcr.microsoft.com/hello-world > Dockerfile
```

运行 [az acr build][az-acr-build] 命令，该命令将生成映像，并在成功生成映像后将其推送到注册表。 以下示例会生成并推送 `sample/hello-world:v1` 映像。 命令末尾处的 `.` 设置 Dockerfile 的位置（在本例中为当前目录）。

```azurecli-interactive
az acr build --image sample/hello-world:v1 \
  --registry myContainerRegistry008 \
  --file Dockerfile . 
```

成功生成并推送后，输出将如下所示：

```console
Packing source code into tar to upload...
Uploading archived source code from '/tmp/build_archive_b0bc1e5d361b44f0833xxxx41b78c24e.tar.gz'...
Sending context (1.856 KiB) to registry: mycontainerregistry008...
Queued a build with ID: ca8
Waiting for agent...
2019/03/18 21:56:57 Using acb_vol_4c7ffa31-c862-4be3-xxxx-ab8e615c55c4 as the home volume
2019/03/18 21:56:57 Setting up Docker configuration...
2019/03/18 21:56:58 Successfully set up Docker configuration
2019/03/18 21:56:58 Logging in to registry: mycontainerregistry008.azurecr.io
2019/03/18 21:56:59 Successfully logged into mycontainerregistry008.azurecr.io
2019/03/18 21:56:59 Executing step ID: build. Working directory: '', Network: ''
2019/03/18 21:56:59 Obtaining source code and scanning for dependencies...
2019/03/18 21:57:00 Successfully obtained source code and scanned for dependencies
2019/03/18 21:57:00 Launching container with name: build
Sending build context to Docker daemon  13.82kB
Step 1/1 : FROM mcr.microsoft.com/hello-world
latest: Pulling from hello-world
Digest: sha256:2557e3c07ed1e38f26e389462d03ed943586fxxxx21577a99efb77324b0fe535
Successfully built fce289e99eb9
Successfully tagged mycontainerregistry008.azurecr.io/sample/hello-world:v1
2019/03/18 21:57:01 Successfully executed container: build
2019/03/18 21:57:01 Executing step ID: push. Working directory: '', Network: ''
2019/03/18 21:57:01 Pushing image: mycontainerregistry008.azurecr.io/sample/hello-world:v1, attempt 1
The push refers to repository [mycontainerregistry008.azurecr.io/sample/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Layer already exists
v1: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/03/18 21:57:03 Successfully pushed image: mycontainerregistry008.azurecr.io/sample/hello-world:v1
2019/03/18 21:57:03 Step ID: build marked as successful (elapsed time in seconds: 2.543040)
2019/03/18 21:57:03 Populating digests for step ID: build...
2019/03/18 21:57:05 Successfully populated digests for step ID: build
2019/03/18 21:57:05 Step ID: push marked as successful (elapsed time in seconds: 1.473581)
2019/03/18 21:57:05 The following dependencies were found:
2019/03/18 21:57:05
- image:
    registry: mycontainerregistry008.azurecr.io
    repository: sample/hello-world
    tag: v1
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: v1
    digest: sha256:2557e3c07ed1e38f26e389462d03ed943586f744621577a99efb77324b0fe535
  git: {}

Run ID: ca8 was successful after 10s
```

## <a name="run-the-image"></a>运行映像

现在，请快速运行已生成并推送到注册表的映像。 此处使用 [az acr run][az-acr-run] 运行容器命令。 在容器开发工作流中，这可能是部署映像之前的验证步骤，或者你可以将该命令包含在[多步骤 YAML 文件][container-registry-tasks-multi-step]中。 

以下示例使用 `$Registry` 指定运行命令的注册表：

```azurecli-interactive
az acr run --registry myContainerRegistry008 \
  --cmd '$Registry/sample/hello-world:v1' /dev/null
```

此示例中的 `cmd` 参数以其默认配置运行容器，但 `cmd` 支持附加 `docker run` 参数甚至其他 `docker` 命令。

输出与下面类似：

```console
Packing source code into tar to upload...
Uploading archived source code from '/tmp/run_archive_ebf74da7fcb04683867b129e2ccad5e1.tar.gz'...
Sending context (1.855 KiB) to registry: mycontainerre...
Queued a run with ID: cab
Waiting for an agent...
2019/03/19 19:01:53 Using acb_vol_60e9a538-b466-475f-9565-80c5b93eaa15 as the home volume
2019/03/19 19:01:53 Creating Docker network: acb_default_network, driver: 'bridge'
2019/03/19 19:01:53 Successfully set up Docker network: acb_default_network
2019/03/19 19:01:53 Setting up Docker configuration...
2019/03/19 19:01:54 Successfully set up Docker configuration
2019/03/19 19:01:54 Logging in to registry: mycontainerregistry008.azurecr.io
2019/03/19 19:01:55 Successfully logged into mycontainerregistry008.azurecr.io
2019/03/19 19:01:55 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/03/19 19:01:55 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

2019/03/19 19:01:56 Successfully executed container: acb_step_0
2019/03/19 19:01:56 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 0.843801)

Run ID: cab was successful after 6s
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要存储在该处的资源组、容器注册表和容器映像，可以使用 [az group delete][az-group-delete] 命令将其删除。

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你使用了 Azure 任务功能在 Azure 中以本机方式快速构建、推送和运行 Docker 容器映像，而未本地安装 Docker。 请继续学习 Azure 容器注册表任务教程，了解如何使用 Azure 任务来自动生成和更新映像。

> [!div class="nextstepaction"]
> [Azure 容器注册表任务教程][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[azure-account]: https://azure.microsoft.com/free/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-tasks-overview]: container-registry-tasks-overview.md
[container-registry-tasks-multi-step]: container-registry-tasks-multi-step.md
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[azure-cli-install]: /cli/azure/install-azure-cli
