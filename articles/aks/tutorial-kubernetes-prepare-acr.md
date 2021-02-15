---
title: Azure 上的 Kubernetes 教程 - 创建容器注册表
description: 在本 Azure Kubernetes 服务 (AKS) 教程中，请创建 Azure 容器注册表实例并上传示例应用程序容器映像。
services: container-service
ms.topic: tutorial
ms.date: 01/31/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 9f6ec14cea20192aef7d3010201e6613c5d03a9e
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430958"
---
# <a name="tutorial-deploy-and-use-azure-container-registry"></a>教程：部署并使用 Azure 容器注册表

Azure 容器注册表 (ACR) 是容器映像的专用注册表。 可以通过专用容器注册表安全地生成和部署应用程序和自定义代码。 本教程的第 2 部分（共 7 部分）介绍如何部署 ACR 实例并向其推送容器映像。 学习如何：

> [!div class="checklist"]
> * 创建 Azure 容器注册表 (ACR) 实例
> * 标记 ACR 的容器映像
> * 向 ACR 上传映像
> * 查看注册表中的映像

在后面的教程中，此 ACR 实例将与 AKS 中的 Kubernetes 群集集成，而应用程序则通过映像进行部署。

## <a name="before-you-begin"></a>开始之前

在[上一教程][aks-tutorial-prepare-app]中，已经为一个 Azure Voting 应用程序示例创建了容器映像。 如果尚未创建 Azure Voting 应用映像，请返回到[教程 1：创建容器映像][aks-tutorial-prepare-app]。

此教程需要运行 Azure CLI 2.0.53 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

## <a name="create-an-azure-container-registry"></a>创建 Azure 容器注册表

若要创建 Azure 容器注册表，首先需要一个资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

使用“[az group create][az-group-create]”命令创建资源组。 以下示例在 *eastus* 区域创建名为 *myResourceGroup* 的资源组：

```azurecli
az group create --name myResourceGroup --location eastus
```

使用 [az acr create][az-acr-create] 命令创建 Azure 容器注册表实例，并提供你自己的注册表名称。 注册表名称在 Azure 中必须唯一，并且包含 5-50 个字母数字字符。 在本教程的剩余部分，请使用 `<acrName>` 作为容器注册表名称的占位符。 提供自己的唯一注册表名称。 “基本”SKU 是一个针对成本优化的入口点，适用于可以对存储和吞吐量进行均衡考虑的开发目的。

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

## <a name="log-in-to-the-container-registry"></a>登录到容器注册表

若要使用 ACR 实例，必须先登录。 使用 [az acr login][az-acr-login] 命令并提供一个唯一名称，该名称是在上一步提供给容器注册表的。

```azurecli
az acr login --name <acrName>
```

完成后，该命令会返回“登录成功”消息。

## <a name="tag-a-container-image"></a>标记容器映像

若要查看当前的本地映像的列表，请使用 [docker images][docker-images] 命令：

```console
docker images
```
上述命令输出显示当前本地图像的列表：

```output
REPOSITORY                                     TAG                 IMAGE ID            CREATED             SIZE
mcr.microsoft.com/azuredocs/azure-vote-front   v1                  84b41c268ad9        7 minutes ago       944MB
mcr.microsoft.com/oss/bitnami/redis            6.0.8               3a54a920bb6c        2 days ago          103MB
tiangolo/uwsgi-nginx-flask                     python3.6           a16ce562e863        6 weeks ago         944MB
```

若要将 *azure-vote-front* 容器映像与 ACR 配合使用，需使用注册表的登录服务器地址对映像进行标记。 在将容器映像推送到映像注册表时，使用此标记进行路由。

若要获取登录服务器地址，请使用 [az acr list][az-acr-list] 命令并查询是否存在 *loginServer*，如下所示：

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

现在，请使用容器注册表的 acrLoginServer 地址标记本地 azure-vote-front 映像。 若要指示映像版本，请将 *:v1* 添加到映像名称的末尾：

```console
docker tag mcr.microsoft.com/azuredocs/azure-vote-front:v1 <acrLoginServer>/azure-vote-front:v1
```

若要验证是否已应用标记，请再次运行 [docker images][docker-images]。

```console
docker images
```

系统会使用 ACR 实例地址和版本号对映像进行标记。

```
REPOSITORY                                      TAG                 IMAGE ID            CREATED             SIZE
mcr.microsoft.com/azuredocs/azure-vote-front    v1                  84b41c268ad9        16 minutes ago      944MB
mycontainerregistry.azurecr.io/azure-vote-front v1                  84b41c268ad9        16 minutes ago      944MB
mcr.microsoft.com/oss/bitnami/redis             6.0.8               3a54a920bb6c        2 days ago          103MB
tiangolo/uwsgi-nginx-flask                      python3.6           a16ce562e863        6 weeks ago         944MB
```

## <a name="push-images-to-registry"></a>将映像推送到注册表

生成并标记映像后，将 *azure-vote-front* 映像推送到 ACR 实例。 使用 [docker push][docker-push] 并提供自己的适用于映像名称的 *acrLoginServer* 地址，如下所示：

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

可能需要数分钟才能将映像推送到 ACR。

## <a name="list-images-in-registry"></a>列出注册表中的映像

若要返回已推送到 ACR 实例的映像列表，请使用 [az acr repository list][az-acr-repository-list] 命令。 按如下所示提供自己的 `<acrName>`：

```azurecli
az acr repository list --name <acrName> --output table
```

以下示例输出将 *azure-vote-front* 映像列为在注册表中可用：

```output
Result
----------------
azure-vote-front
```

若要查看特定映像的标记，请使用 [az acr repository show-tags][az-acr-repository-show-tags] 命令，如下所示：

```azurecli
az acr repository show-tags --name <acrName> --repository azure-vote-front --output table
```

以下示例输出显示在上一步标记的 *v1* 映像：

```output
Result
--------
v1
```

你现在有了一个容器映像，该映像存储在专用的 Azure 容器注册表实例中。 在下一教程中，此映像会从 ACR 部署到 Kubernetes 群集。

## <a name="next-steps"></a>后续步骤

在本教程中，你创建了一个 Azure 容器注册表并推送了可以在 AKS 群集中使用的映像。 你已了解如何执行以下操作：

> [!div class="checklist"]
> * 创建 Azure 容器注册表 (ACR) 实例
> * 标记 ACR 的容器映像
> * 向 ACR 上传映像
> * 查看注册表中的映像

继续学习下一篇教程，了解如何在 Azure 中部署 Kubernetes 群集。

> [!div class="nextstepaction"]
> [部署 Kubernetes 群集][aks-tutorial-deploy-cluster]

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr
[az-acr-list]: /cli/azure/acr
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-list]: /cli/azure/acr#az-acr-list
[az-acr-repository-list]: /cli/azure/acr/repository
[az-acr-repository-show-tags]: /cli/azure/acr/repository
[az-group-create]: /cli/azure/group#az-group-create
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-tutorial-deploy-cluster]: ./tutorial-kubernetes-deploy-cluster.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
