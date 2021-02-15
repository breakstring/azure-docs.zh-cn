---
title: 快速入门：创建多容器应用
description: 部署第一个多容器应用即可开始使用 Azure 应用服务上的多容器应用。
keywords: azure 应用服务, web 应用, linux, docker compose, 多容器, 用于容器的 web 应用, 多个容器, 容器, wordpress, azure db for mysql, 包含容器的生产数据库
author: msangapu-msft
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 2ba42e5e800ae607631e00aee50954bf2638ae43
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007121"
---
# <a name="create-a-multi-container-preview-app-using-a-docker-compose-configuration"></a>使用 Docker Compose 配置创建多容器（预览版）应用

> [!NOTE]
> 多容器处于预览状态。

在[用于容器的 Web 应用](overview.md#app-service-on-linux)中可以灵活使用 Docker 映像。 本快速入门介绍如何在 [Cloud Shell](../cloud-shell/overview.md) 中使用 Docker Compose 配置将多容器应用（预览）部署到用于容器的 Web 应用。

![用于容器的 Web 应用中的示例多容器应用][1]

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

本文需要 Azure CLI 版本 2.0.32 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="download-the-sample"></a>下载示例

本快速入门使用 [Docker](https://docs.docker.com/compose/wordpress/#define-the-project) 中的 compose 文件。 可在 [Azure 示例](https://github.com/Azure-Samples/multicontainerwordpress)中找到该配置文件。

[!code-yml[Main](../../azure-app-service-multi-container/docker-compose-wordpress.yml)]

在 Cloud Shell 中，创建一个 quickstart 目录，然后切换到该目录。

```bash
mkdir quickstart

cd $HOME/quickstart
```

接下来请运行以下命令，将示例应用存储库克隆到快速入门目录。 然后切换到 `multicontainerwordpress` 目录。

```bash
git clone https://github.com/Azure-Samples/multicontainerwordpress

cd multicontainerwordpress
```

## <a name="create-a-resource-group"></a>创建资源组

[!INCLUDE [resource group intro text](../../includes/resource-group.md)]

在 Cloud Shell 中，使用 [`az group create`](/cli/azure/group#az-group-create) 命令创建资源组。 下面的示例命令在“美国中南部”位置创建名为 *myResourceGroup* 的资源组。  若要查看 **标准** 层中 Linux 上的应用服务支持的所有位置，请运行 [`az appservice list-locations --sku S1 --linux-workers-enabled`](/cli/azure/appservice#az-appservice-list-locations) 命令。

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

通常在附近的区域中创建资源组和资源。

此命令完成后，JSON 输出会显示资源组属性。

## <a name="create-an-azure-app-service-plan"></a>创建 Azure 应用服务计划

在 Cloud Shell 中，使用 [`az appservice plan create`](/cli/azure/appservice/plan#az-appservice-plan-create) 命令在资源组中创建应用服务计划。

以下示例在 **标准** 定价层 (`--sku S1`) 和 Linux 容器 (`--is-linux`) 中创建名为 `myAppServicePlan` 的应用服务计划。

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

创建应用服务计划后，Azure CLI 会显示类似于以下示例的信息：

<pre>
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "South Central US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "linux",
  "location": "South Central US",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
</pre>

## <a name="create-a-docker-compose-app"></a>创建 Docker Compose 应用

> [!NOTE]
> 目前，Azure 应用服务上的 Docker Compose 的限制为 4,000 个字符。

在 Cloud Shell 终端中，使用 [az webapp create](/cli/azure/webapp#az-webapp-create) 命令在 `myAppServicePlan` 应用服务计划中创建一个多容器 [Web 应用](overview.md#app-service-on-linux)。 不要忘记将 \<app_name> 替换为唯一的应用名称（有效字符是 `a-z`、`0-9` 和 `-`）。

```azurecli
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --multicontainer-config-type compose --multicontainer-config-file compose-wordpress.yml
```

创建 Web 应用后，Azure CLI 会显示类似于以下示例的输出：

<pre>
{
  "additionalProperties": {},
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;app_name&gt;.azurewebsites.net",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>

### <a name="browse-to-the-app"></a>浏览到应用

浏览到所部署的应用 (`http://<app_name>.azurewebsites.net`)。 该应用可能需要几分钟时间才能完成加载。 如果收到错误，请在几分钟后刷新浏览器。

![用于容器的 Web 应用中的示例多容器应用][1]

**祝贺你**，现已在用于容器的 Web 应用中创建了多容器应用。

[!INCLUDE [Clean-up section](../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：多容器 WordPress 应用](tutorial-multi-container-app.md)

> [!div class="nextstepaction"]
> [配置自定义容器](configure-custom-container.md)

<!--Image references-->
[1]: media/tutorial-multi-container-app/azure-multi-container-wordpress-install.png
