---
title: 快速入门：创建静态 HTML Web 应用
description: 在数分钟内将第一个 HTML Hello World 部署到 Azure 应用服务。 我们使用 Git 进行部署，这是部署到应用服务时使用的多种方法中的一种。
author: msangapu-msft
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: mvc, cli-validate, seodec18, devx-track-azurecli
ms.openlocfilehash: 9b85f04ca507b5d40c091b52507d0fad2cd3e798
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98185693"
---
# <a name="create-a-static-html-web-app-in-azure"></a>在 Azure 中创建静态 HTML Web 应用

[Azure 应用服务](overview.md)提供高度可缩放、自修复的 Web 托管服务。 本快速入门演示如何将基本 HTML+CSS 站点部署到 Azure 应用服务。 你将在 [Cloud Shell](../cloud-shell/overview.md) 中完成本快速入门，但是也可以使用 [Azure CLI](/cli/azure/install-azure-cli) 在本地运行这些命令。

![示例应用主页](media/quickstart-html/hello-world-in-browser-az.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>下载示例

在 Cloud Shell 中，创建一个 quickstart 目录，然后切换到该目录。

```bash
mkdir quickstart

cd $HOME/quickstart
```

接下来请运行以下命令，将示例应用存储库克隆到快速入门目录。

```bash
git clone https://github.com/Azure-Samples/html-docs-hello-world.git
```

## <a name="create-a-web-app"></a>创建 Web 应用

切换到包含示例代码的目录并运行 `az webapp up` 命令。 在以下示例中，请将 <app_name> 替换为一个唯一的应用名称。 静态内容由 `--html` 标志予以指示。

```bash
cd html-docs-hello-world

az webapp up --location westeurope --name <app_name> --html
```

`az webapp up` 命令执行以下操作：

- 创建一个默认的资源组。

- 创建一个默认的应用服务计划。

- 创建一个采用指定名称的应用。

- [使用 Zip](./deploy-zip.md) 将文件从当前工作目录部署到 Web 应用。

此命令可能需要花费几分钟时间运行。 运行时，该命令会显示类似于以下示例的信息：

```output
{
  "app_url": "https://&lt;app_name&gt;.azurewebsites.net",
  "location": "westeurope",
  "name": "&lt;app_name&gt;",
  "os": "Windows",
  "resourcegroup": "appsvc_rg_Windows_westeurope",
  "serverfarm": "appsvc_asp_Windows_westeurope",
  "sku": "FREE",
  "src_path": "/home/&lt;username&gt;/quickstart/html-docs-hello-world ",
  &lt; JSON data removed for brevity. &gt;
}
```

记下 `resourceGroup` 值。 需要在[清理资源](#clean-up-resources)部分使用它。

## <a name="browse-to-the-app"></a>浏览到应用

在浏览器中转到应用 URL：`http://<app_name>.azurewebsites.net`。

该页作为 Azure 应用服务 Web 应用运行。

![示例应用主页](media/quickstart-html/hello-world-in-browser-az.png)

祝贺你！  现已将第一个 HTML 应用部署到应用服务。

## <a name="update-and-redeploy-the-app"></a>更新并重新部署应用

在 Cloud Shell 中，键入 `nano index.html` 以打开 nano 文本编辑器。 在 `<h1>` 标题标记中，将“Azure 应用服务 - 示例静态 HTML 站点”更改为“Azure 应用服务”，如下所示。

![Nano index.html](media/quickstart-html/nano-index-html.png)

保存更改并退出 nano。 使用命令 `^O` 来保存，使用 `^X` 来退出。

现在，需使用同一 `az webapp up` 命令重新部署应用。

```bash
az webapp up --location westeurope --name <app_name> --html
```

完成部署后，切换回 **浏览到应用** 步骤中打开的浏览器窗口，然后刷新页面。

![已更新的示例应用主页](media/quickstart-html/hello-azure-in-browser-az.png)

## <a name="manage-your-new-azure-app"></a>管理新的 Azure 应用

若要管理所创建的 Web 应用，请在 [Azure 门户](https://portal.azure.com)中，搜索并选择“应用服务”"。 

![在 Azure 门户中选择应用服务](./media/quickstart-html/portal0.png)

在“应用服务”页上，选择 Azure 应用的名称。

![在门户中导航到 Azure 应用](./media/quickstart-html/portal1.png)

这里我们可以看到 Web 应用的概述页。 并可以执行基本的管理任务，例如浏览、停止、启动、重新启动和删除。

![Azure 门户中的“应用服务”边栏选项卡](./media/quickstart-html/portal2.png)

左侧菜单提供了用于配置应用的不同页面。

## <a name="clean-up-resources"></a>清理资源

在前面的步骤中，你在资源组中创建了 Azure 资源。 如果认为将来不需要这些资源，请在 Cloud Shell 中运行以下命令，以便删除资源组。 请记住，资源组名称已在[创建 Web 应用](#create-a-web-app)步骤中自动为你生成。

```bash
az group delete --name appsvc_rg_Windows_westeurope
```

此命令可能需要花费一点时间运行。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [映射自定义域](app-service-web-tutorial-custom-domain.md)
