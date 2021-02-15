---
title: 从 ACR 任务中进行跨注册表身份验证
description: 配置 Azure 容器注册表任务（ACR 任务）以使用 Azure 资源的托管标识访问其他专用 Azure 容器注册表
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: 789d2c141f8b7c3f2eb8daa31d99090e3d028a43
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98915822"
---
# <a name="cross-registry-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>使用 Azure 托管标识在 ACR 任务中进行跨注册表的身份验证 

在 [ACR 任务](container-registry-tasks-overview.md)中，可以[为 Azure 资源启用托管标识](container-registry-tasks-authentication-managed-identity.md)。 该任务可以使用该标识来访问其他 Azure 资源，而无需提供或管理凭据。 

本文介绍如何在任务中启用托管标识，从与用于运行任务的注册表不同的其他注册表中提取映像。

为了创建 Azure 资源，本文要求运行 Azure CLI 版本 2.0.68 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli]。

## <a name="scenario-overview"></a>方案概述

此示例任务从其他 Azure 容器注册表中拉取基础映像，用于生成和推送应用程序映像。 若要拉取基础映像，应为任务配置托管标识，并向其分配适当的权限。 

此示例演示使用用户分配的托管标识或系统分配的托管标识的步骤。 你选择的标识取决于你的组织的需求。

在实际情况中，组织可能会维护一组供各开发团队用来生成应用程序的基础映像。 这些基础映像存储在企业注册表中，每个开发团队仅拥有相应的拉取权限。 

## <a name="prerequisites"></a>先决条件

在本文中，需要两个 Azure 容器注册表：

* 使用第一个注册表来创建和执行 ACR 任务。 在本文中，此注册表名为 myregistry。 
* 第二个注册表承载任务生成映像时要使用的基础映像。 在本文中，第二个注册表名为 mybaseregistry。 

请在后续步骤中将其替换为你自己的注册表名称。

如果还没有所需的 Azure 容器注册表，请参阅[快速入门：使用 Azure CLI 创建专用容器注册表](container-registry-get-started-azure-cli.md)。 还不需要将映像推送到注册表。

## <a name="prepare-base-registry"></a>准备基础注册表

出于演示目的，以一次性操作的方式，运行 [az acr import][az-acr-import]，将公共 Node.js 映像从 Docker Hub 导入到基础注册表。 在实践中，组织中的另一个团队或流程可能会维护基础注册表中的映像。

```azurecli
az acr import --name mybaseregistry \
  --source docker.io/library/node:15-alpine \
  --image baseimages/node:15-alpine 
```

## <a name="define-task-steps-in-yaml-file"></a>在 YAML 文件中定义任务步骤

此示例[多步骤任务](container-registry-tasks-multi-step.md)的步骤在 [YAML 文件](container-registry-tasks-reference-yaml.md)中进行定义。 在本地工作目录中创建名为 `helloworldtask.yaml` 的文件并粘贴以下内容。 将生成步骤中 `REGISTRY_NAME` 的值更新为基础注册表的服务器名称。

```yml
version: v1.1.0
steps:
# Replace mybaseregistry with the name of your registry containing the base image
  - build: -t $Registry/hello-world:$ID  https://github.com/Azure-Samples/acr-build-helloworld-node.git#main -f Dockerfile-app --build-arg REGISTRY_NAME=mybaseregistry.azurecr.io
  - push: ["$Registry/hello-world:$ID"]
```

生成步骤使用 [Azure-Samples/acr-build-helloworld-node](https://github.com/Azure-Samples/acr-build-helloworld-node.git) 存储库中的 `Dockerfile-app` 文件来构建映像。 `--build-arg` 引用基础注册表来拉取基础映像。 成功生成映像后，会将映像推送到用于运行任务的注册表。

## <a name="option-1-create-task-with-user-assigned-identity"></a>选项 1：创建使用用户分配的标识的任务

本部分中的步骤将创建一个任务并启用用户分配的标识。 若要改为启用系统分配的标识，请参阅[选项 2：创建使用系统分配的标识的任务](#option-2-create-task-with-system-assigned-identity)。 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>创建任务

通过执行以下 [az acr task create][az-acr-task-create] 命令，创建任务 helloworldtask。 该任务无需源代码上下文即可运行，该命令将引用工作目录中的 `helloworldtask.yaml` 文件。 `--assign-identity` 参数传递用户分配的标识的资源 ID。 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>为标识授予对基础注册表的提取权限

在本部分中，向托管标识授予从基础注册表 mybaseregistry 中进行拉取的权限。

使用 [az acr show][az-acr-show] 命令获取基础注册表的资源 ID 并将其存储在变量中：

```azurecli
baseregID=$(az acr show --name mybaseregistry --query id --output tsv)
```

使用 [az role assignment create][az-role-assignment-create] 命令向标识分配用于访问基础注册表的 `acrpull` 角色。 此角色仅具有从注册表拉取映像的权限。

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

执行下一步，[将目标注册表凭据添加到任务](#add-target-registry-credentials-to-task)。

## <a name="option-2-create-task-with-system-assigned-identity"></a>选项 2：创建具有系统分配的标识的任务

本部分中的步骤创建一个任务并启用系统分配的标识。 如果要改为启用用户分配的标识，请参阅[选项 1：创建具有用户分配的标识的任务](#option-1-create-task-with-user-assigned-identity)。 

### <a name="create-task"></a>创建任务

通过执行以下 [az acr task create][az-acr-task-create] 命令，创建任务 helloworldtask。 该任务无需源代码上下文即可运行，该命令将引用工作目录中的 `helloworldtask.yaml` 文件。 不带任何值的 `--assign-identity` 参数将在任务中启用系统分配的标识。 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity 
```
[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>为标识授予对基础注册表的提取权限

在本部分中，向托管标识授予从基础注册表 mybaseregistry 中进行拉取的权限。

使用 [az acr show][az-acr-show] 命令获取基础注册表的资源 ID 并将其存储在变量中：

```azurecli
baseregID=$(az acr show --name mybaseregistry --query id --output tsv)
```

使用 [az role assignment create][az-role-assignment-create] 命令向标识分配用于访问基础注册表的 `acrpull` 角色。 此角色仅具有从注册表拉取映像的权限。

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

## <a name="add-target-registry-credentials-to-task"></a>向任务添加目标注册表凭据

现在，使用 [az acr task credential add][az-acr-task-credential-add] 命令，使任务能够使用标识的凭据进行基本注册表的身份验证。 运行与任务中启用的托管标识类型对应的命令。 如果启用了用户分配的标识，传递带有标识的客户端 ID 的 `--use-identity`。 如果启用了系统分配的标识，则传递 `--use-identity [system]`。

```azurecli
# Add credentials for user-assigned identity to the task
az acr task credential add \
  --name helloworldtask \
  --registry myregistry \
  --login-server mybaseregistry.azurecr.io \
  --use-identity $clientID

# Add credentials for system-assigned identity to the task
az acr task credential add \
  --name helloworldtask \
  --registry myregistry \
  --login-server mybaseregistry.azurecr.io \
  --use-identity [system]
```

## <a name="manually-run-the-task"></a>手动运行任务

若要验证启用了托管标识的任务是否成功运行，请使用 [az acr task run][az-acr-task-run] 命令手动触发该任务。 

```azurecli
az acr task run \
  --name helloworldtask \
  --registry myregistry
```

如果任务成功运行，输出会类似于：

```
Queued a run with ID: cf10
Waiting for an agent...
2019/06/14 22:47:32 Using acb_vol_dbfbe232-fd76-4ca3-bd4a-687e84cb4ce2 as the home volume
2019/06/14 22:47:39 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/14 22:47:40 Successfully set up Docker network: acb_default_network
2019/06/14 22:47:40 Setting up Docker configuration...
2019/06/14 22:47:41 Successfully set up Docker configuration
2019/06/14 22:47:41 Logging in to registry: myregistry.azurecr.io
2019/06/14 22:47:42 Successfully logged into myregistry.azurecr.io
2019/06/14 22:47:42 Logging in to registry: mybaseregistry.azurecr.io
2019/06/14 22:47:43 Successfully logged into mybaseregistry.azurecr.io
2019/06/14 22:47:43 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/14 22:47:43 Scanning for dependencies...
2019/06/14 22:47:45 Successfully scanned dependencies
2019/06/14 22:47:45 Launching container with name: acb_step_0
Sending build context to Docker daemon   25.6kB
Step 1/6 : ARG REGISTRY_NAME
Step 2/6 : FROM ${REGISTRY_NAME}/baseimages/node:15-alpine
15-alpine: Pulling from baseimages/node
[...]
Successfully built 41b49a112663
Successfully tagged myregistry.azurecr.io/hello-world:cf10
2019/06/14 22:47:56 Successfully executed container: acb_step_0
2019/06/14 22:47:56 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/14 22:47:56 Pushing image: myregistry.azurecr.io/hello-world:cf10, attempt 1
The push refers to repository [myregistry.azurecr.io/hello-world]
[...]
2019/06/14 22:48:00 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.517011)
2019/06/14 22:48:00 The following dependencies were found:
2019/06/14 22:48:00
- image:
    registry: myregistry.azurecr.io
    repository: hello-world
    tag: cf10
    digest: sha256:611cf6e3ae3cb99b23fadcd89fa144e18aa1b1c9171ad4a0da4b62b31b4e38d1
  runtime-dependency:
    registry: mybaseregistry.azurecr.io
    repository: baseimages/node
    tag: 15-alpine
    digest: sha256:e8e92cffd464fce3be9a3eefd1b65dc9cbe2484da31c11e813a4effc6105c00f
  git:
    git-head-revision: 0f988779c97fe0bfc7f2f74b88531617f4421643

Run ID: cf10 was successful after 32s
```

运行 [az acr repository show-tags][az-acr-repository-show-tags] 命令，验证映像已构建并成功推送到 myregistry：

```azurecli
az acr repository show-tags --name myregistry --repository hello-world --output tsv
```

示例输出：

```console
cf10
```

## <a name="next-steps"></a>后续步骤

* 详细了解如何[在 ACR 任务中启用托管标识](container-registry-tasks-authentication-managed-identity.md)。
* 请参阅 [ACR 任务 YAML 引用](container-registry-tasks-reference-yaml.md)

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
