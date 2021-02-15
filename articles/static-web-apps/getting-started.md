---
title: 快速入门：使用 Azure Static Web Apps 生成第一个静态站点
description: 了解如何将静态站点部署到 Azure Static Web Apps。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: eb2356451c349f894c9ca74b1359f6a02d0e002a
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97562508"
---
# <a name="quickstart-building-your-first-static-site-with-azure-static-web-apps"></a>快速入门：使用 Azure Static Web Apps 生成第一个静态站点

Azure Static Web Apps 通过从 GitHub 存储库生成应用来将网站发布到生产环境。 在此快速入门中，你可以使用 Visual Studio Code 扩展将 Web 应用部署到 Azure Static Web Apps。

如果没有 Azure 订阅，[请创建一个免费的试用帐户](https://azure.microsoft.com/free)。

## <a name="prerequisites"></a>先决条件

- [GitHub](https://github.com) 帐户
- [Azure](https://portal.azure.com) 帐户
- [Visual Studio Code](https://code.visualstudio.com)
- [Visual Studio Code 的 Azure Static Web Apps 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

接下来，打开 Visual Studio Code 并转到“文件”>“打开文件夹”以在编辑器中打开刚刚克隆到计算机上的存储库。

## <a name="create-a-static-web-app"></a>创建静态 Web 应用

1. 在 Visual Studio Code 中，选择活动栏中的 Azure 徽标以打开 Azure 扩展窗口。

    :::image type="content" source="media/getting-started/extension-azure-logo.png" alt-text="Azure 徽标":::

    > [!NOTE]
    > 需要登录 Azure 和 GitHub。 如果尚未从 Visual Studio Code 登录到 Azure 和 GitHub，该扩展将在创建过程中提示你登录到这两个站点。

1. 将鼠标置于“Static Web Apps”标签上，然后选择加号。

    :::image type="content" source="media/getting-started/extension-create-button.png" alt-text="应用程序名称":::

1. 命令面板将在编辑器顶部打开，并提示你命名应用程序。

    输入“my-first-static-web-app”，然后按 Enter 。

    :::image type="content" source="media/getting-started/extension-create-app.png" alt-text="创建静态 Web 应用":::

1. 选择主分支，然后按 Enter。

    :::image type="content" source="media/getting-started/extension-branch.png" alt-text="分支名称":::

1. 选择“/”作为应用程序代码的位置，然后按 Enter 。

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="应用程序代码位置":::

1. 扩展正在查找应用程序中 API 的位置。 本文不实现 API。

    选择“暂时跳过”，然后按 Enter 。

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="API 位置":::

1. 选择在应用中生成用于生产的文件的位置。

    # <a name="no-framework"></a>[无框架](#tab/vanilla-javascript)

    清除框并按 Enter。

    :::image type="content" source="media/getting-started/extension-artifact-no-framework.png" alt-text="应用文件路径":::

    # <a name="angular"></a>[Angular](#tab/angular)

    输入“dist/angular-basic”，然后按 Enter 。

    :::image type="content" source="media/getting-started/extension-artifact-angular.png" alt-text="Angular 应用文件路径":::

    # <a name="react"></a>[React](#tab/react)

    键入“生成”，然后按 Enter 。

    :::image type="content" source="media/getting-started/extension-artifact-react.png" alt-text="React 应用文件路径":::

    # <a name="vue"></a>[Vue](#tab/vue)

    键入“dist”，然后按 Enter 。

    :::image type="content" source="media/getting-started/extension-artifact-vue.png" alt-text="Vue 应用文件路径":::

    ---

1. 选择离你最近的位置，然后按 Enter。

    :::image type="content" source="media/getting-started/extension-location.png" alt-text="资源位置":::

1. 创建应用后，将在 Visual Studio Code 中显示确认通知。

    :::image type="content" source="media/getting-started/extension-confirmation.png" alt-text="已创建确认":::

1. 在 Visual Studio Code“资源管理器”窗口中，导航到具有订阅名称的节点并将其展开。 请注意，完成部署可能需要几分钟时间。 然后返回到“Static Web Apps”部分，选择应用的名称，再右键单击 my-first-static-web-app，选择“在门户中打开”，以便在 Azure 门户中查看应用。

    :::image type="content" source="media/getting-started/extension-open-in-portal.png" alt-text="打开门户":::

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此应用程序，可通过该扩展删除 Azure Static Web Apps 实例。

在 Visual Studio Code 资源浏览器窗口中，返回到“Static Web Apps”部分，右键单击“my-first-static-web-app”，然后选择“删除” 。

:::image type="content" source="media/getting-started/extension-delete.png" alt-text="删除应用":::

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [添加 API](add-api.md)
