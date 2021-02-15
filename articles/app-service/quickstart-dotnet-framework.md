---
title: 快速入门：创建 C# ASP.NET 应用
description: 了解如何通过部署 Visual Studio 提供的默认 C# ASP.NET Web 应用模板，在 Azure 应用服务中运行 Web 应用。
ms.assetid: 04a1becf-7756-4d4e-92d8-d9471c263d23
ms.topic: quickstart
ms.date: 11/20/2020
ms.custom: devx-track-csharp, mvc, devcenter, seodec18
ms.openlocfilehash: a4f7ba288bc27d6079deea9caf0ea315a55d0745
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "96004025"
---
# <a name="create-an-aspnet-framework-web-app-in-azure"></a>在 Azure 中创建 ASP.NET Framework Web 应用

[Azure 应用服务](overview.md)提供高度可缩放、自修复的 Web 托管服务。

本快速入门演示如何将第一个 ASP.NET Web 应用部署到 Azure 应用服务中。 完成后，你将拥有一个应用服务计划。 还将获得一个应用服务应用，其中包含已部署的 Web 应用程序。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

为完成此教程，请安装支持 **ASP.NET 和 Web 开发** 工作负荷的 <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a>。

如果已安装 Visual Studio 2019：

- 通过选择“帮助” > “检查更新”，在 Visual Studio 中安装最新更新。
- 通过选择“工具” > “获取工具和功能”，添加工作负荷。

## <a name="create-an-aspnet-web-app"></a>创建 ASP.NET Web 应用 <a name="create-and-publish-the-web-app"></a>

按照以下步骤创建 ASP.NET Web 应用：

1. 打开 Visual Studio，然后选择“创建新项目”。

2. 在“创建新项目”中，找到并选择“ASP.NET Web 应用程序(.NET Framework)”，然后选择“下一步”。

3. 在“配置新项目”中，将应用程序命名为“myFirstAzureWebApp”，然后选择“创建”。

   ![配置 Web 应用项目](./media/quickstart-dotnet-framework/configure-web-app-project-framework.png)

4. 可将任何类型的 ASP.NET Web 应用部署到 Azure。 对于本快速入门，请选择“MVC”模板。

5. 确保身份验证设置为“不进行身份验证”。 选择“创建”。

   ![创建 ASP.NET Web 应用程序](./media/quickstart-dotnet-framework/select-mvc-template-vs2019.png)

6. 在 Visual Studio 菜单中，选择“调试” > “开始执行(不调试)”以在本地运行 Web 应用。 

   ![在本地运行应用](./media/quickstart-dotnet-framework/local-web-app.png)

## <a name="publish-your-web-app"></a>发布 Web 应用 <a name="launch-the-publish-wizard"></a>

1. 在“解决方案资源管理器”中右键单击“myFirstAzureWebApp”项目，然后选择“发布”。

1. 在“发布”中，选择“Azure”，然后单击“下一步”  。

1. 选择“Azure 应用服务 (Windows)”，然后单击“下一步” 。

   <!-- ![Publish from project overview page](./media/quickstart-dotnet-framework/publish-app-framework-vs2019.png) -->

1. 你的选项取决于你是否已登录 Azure 以及是否有一个链接到 Azure 帐户的 Visual Studio 帐户。 选择“添加帐户”或“登录”以登录 Azure 订阅。 如果你已经登录，请选择所需的帐户。

   ![登录 Azure](./media/quickstart-dotnet-framework/sign-in-azure-framework-vs2019.png)

   [!INCLUDE [resource group intro text](../../includes/resource-group.md)]

1. 在“应用服务实例”的右侧，单击“+” 。

   ![新的应用服务应用](./media/quickstart-dotnet-framework/publish-new-app-service.png)

1. 对于“资源组”，选择“新建”。

1. 在“新资源组名称”中，输入“myResourceGroup”并选择“确定”。

   [!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

1. 对于“托管计划”，请选择“新建”。

1. 在“托管计划”对话框中，输入下表中的值，然后选择“确定” 。

   | 设置 | 建议的值 | 说明 |
   |-|-|-|
   | 托管计划| myAppServicePlan | 应用服务计划的名称。 |
   | 位置 | 西欧 | 托管 Web 应用的数据中心。 |
   | 大小 | 免费 | [定价层](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)确定托管功能。 |

   ![创建应用服务计划](./media/quickstart-dotnet-framework/app-service-plan-framework-vs2019.png)

1. 在 **名称** 中，输入仅包含有效字符 `a-z`、`A-Z`、`0-9` 和 `-` 的唯一应用名称。 可以接受自动生成的唯一名称。 Web 应用的 URL 为 `http://<app-name>.azurewebsites.net`，其中 `<app-name>` 是应用名称。

2. 选择“创建”以创建 Azure 资源。

   ![配置应用名称](./media/quickstart-dotnet-framework/web-app-name-framework-vs2019.png)

    向导完成后，便为你创建了 Azure 资源，你就可以进行发布了。

3. 选择“完成”关闭向导。

3. 在“发布”页中，单击“发布”。 Visual Studio 将生成、打包应用并将其发布到 Azure，然后在默认浏览器中启动该应用。

    ![已在 Azure 中发布的 ASP.NET Web 应用](./media/quickstart-dotnet-framework/published-azure-web-app.png)

在“创建新的应用服务”页中指定的应用名称将用作 `http://<app-name>.azurewebsites.net` 格式的 URL 前缀。

祝贺你！ 你的 ASP.NET Web 应用已在 Azure 应用服务中实时运行！

## <a name="update-the-app-and-redeploy"></a>更新应用并重新部署

1. 在 **解决方案资源管理器** 中你的项目下，打开“视图” > “主页” > “Index.cshtml”。

1. 在顶部附近找到 `<div class="jumbotron">` HTML 标记，将整个元素替换为以下代码：

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. 若要重新部署到 Azure，请在“解决方案资源管理器”中右键单击“myFirstAzureWebApp”项目，然后选择“发布”。 然后选择“发布”。

    发布完成后，Visual Studio 将启动浏览器并转到 Web 应用的 URL。

    ![已在 Azure 中更新的 ASP.NET Web 应用](./media/quickstart-dotnet-framework/updated-azure-web-app.png)

## <a name="manage-the-azure-app"></a>管理 Azure 应用

1. 若要管理 Web 应用，请转到 [Azure 门户](https://portal.azure.com)，搜索并选择“应用服务”。

   ![选择应用服务](./media/quickstart-dotnet-framework/app-services.png)

2. 在“应用服务”页上，选择 Web 应用的名称  。

   ![在门户中导航到 Azure 应用](./media/quickstart-dotnet-framework/access-portal-framework-vs2019.png)

   这里我们可以看到 Web 应用的概述页。 在这里，可以进行基本的管理，如浏览、停止、启动、重启和删除。

   ![Azure 门户中的应用服务概述](./media/quickstart-dotnet-framework/web-app-general-framework-vs2019.png)

   左侧菜单提供了用于配置应用的不同页面。

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [将 ASP.NET 与 SQL 数据库配合使用](app-service-web-tutorial-dotnet-sqldatabase.md)

> [!div class="nextstepaction"]
> [配置 ASP.NET 应用](configure-language-dotnet-framework.md)
