---
title: 教程：使用 MongoDB 的 Node.js 应用
description: 了解如何在 Azure 中运行 Node.js 应用，同时使其连接到 Azure 中的 MongoDB 数据库 (Cosmos DB)。 本教程使用 MEAN.js。
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/16/2020
ms.custom: mvc, cli-validate, seodec18, devx-track-js, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 5e76c87da1dc9ab7d4adeb0e964ae5a3248b8431
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97347653"
---
# <a name="tutorial-build-a-nodejs-and-mongodb-app-in-azure"></a>教程：在 Azure 中生成 Node.js 和 MongoDB 应用

::: zone pivot="platform-windows"  

[Azure 应用服务](overview.md)提供高度可缩放、自修复的 Web 托管服务。 本教程演示如何在 Windows 上的应用服务中创建 Node.js 应用，并将其连接至 MongoDB 数据库。 完成操作后，将拥有一个在 [Azure 应用服务](overview.md)上运行的 MEAN 应用程序（MongoDB、Express、AngularJS 和 Node.js）。 为简单起见，示例应用程序使用了 [MEAN.js Web 框架](https://meanjs.org/)。

::: zone-end

::: zone pivot="platform-linux"


[Azure 应用服务](overview.md)使用 Linux 操作系统，提供高度可缩放的自修补 Web 托管服务。 本教程演示如何在 Linux 上的应用服务中创建 Node.js 应用，在本地将其连接到 MongoDB 数据库，然后将其部署到 Azure Cosmos DB 用于 MongoDB 的 API 中的数据库。 完成操作后，将拥有一个在 Linux 应用服务中运行的 MEAN 应用程序（MongoDB、Express、AngularJS 和 Node.js）。 为简单起见，示例应用程序使用了 [MEAN.js Web 框架](https://meanjs.org/)。

::: zone-end

![在 Azure 应用服务中运行的 MEAN.js 应用](./media/tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

学习内容：

> [!div class="checklist"]
> * 在 Azure 中创建 MongoDB 数据库
> * 将 Node.js 应用连接到 MongoDB
> * 将应用部署到 Azure
> * 更新数据模型并重新部署应用
> * 从 Azure 流式传输诊断日志
> * 在 Azure 门户中管理应用

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

完成本教程：

- [安装 Git](https://git-scm.com/)
- [安装 Node.js 和 NPM](https://nodejs.org/)
- [安装 Bower](https://bower.io/)（[MEAN.js](https://meanjs.org/docs/0.5.x/#getting-started) 必需的）
- [安装 Gulp.js](https://gulpjs.com/)（[MEAN.js](https://meanjs.org/docs/0.5.x/#getting-started) 必需的）
- [安装并运行 MongoDB 社区版](https://docs.mongodb.com/manual/administration/install-community/)
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)] 

## <a name="test-local-mongodb"></a>测试本地 MongoDB

将终端窗口和 `cd` 打开到 MongoDB 安装的 `bin` 目录。 可使用此终端窗口运行本教程中的所有命令。

在终端运行 `mongo` 以连接到本地 MongoDB 服务器。

```bash
mongo
```

如果连接成功，那么 MongoDB 数据库已经开始运行。 如果连接不成功，确保按[安装 MongoDB 社区版](https://docs.mongodb.com/manual/administration/install-community/)中的步骤来启动本地 MongoDB 数据库。 通常，MongoDB 已安装，但你仍需要通过运行 `mongod` 来启动它。 

完成 MongoDB 数据库测试后，请在终端键入 `Ctrl+C`。 

## <a name="create-local-nodejs-app"></a>创建本地 Node.js 应用

在此步骤中，将设置本地 Node.js 项目。

### <a name="clone-the-sample-application"></a>克隆示例应用程序

在终端窗口中，通过 `cd` 转到工作目录。  

运行下列命令以克隆示例存储库。 

```bash
git clone https://github.com/Azure-Samples/meanjs.git
```

此示例存储库包含 [MEAN.js 存储库](https://github.com/meanjs/mean)的副本。 对它进行了修改，以便在应用服务上运行（有关详细信息，请参阅 MEAN.js 存储库[自述文件](https://github.com/Azure-Samples/meanjs/blob/master/README.md)）。

### <a name="run-the-application"></a>运行应用程序

运行以下命令，安装所需的包并启动应用程序。

```bash
cd meanjs
npm install
npm start
```

忽略 config.domain 警告。 当应用完全加载后，将看到以下类似消息：

<pre>
--
MEAN.JS - 开发环境

环境：开发服务器：        http://0.0.0.0:3000 数据库：mongodb://localhost/mean-dev 应用版本：   0.5.0 MEAN.JS 版本：0.5.0 --
</pre>

在浏览器中导航到 `http://localhost:3000`。 单击菜单顶部的“注册”，并创建测试用户。 

MEAN.js 示例应用程序将用户数据存储在数据库中。 如果创建用户和登录成功，应用向本地 MongoDB 数据库写入数据。

![MEAN.js 成功连接至 MongoDB](./media/tutorial-nodejs-mongodb-app/mongodb-connect-success.png)

选择“管理员”>“管理文章”，添加一些文章。

在终端按 `Ctrl+C`，随时停止 Node.js。 

## <a name="create-production-mongodb"></a>创建生产 MongoDB

在此步骤中，会在 Azure 中创建 MongoDB 数据库。 应用部署到 Azure 后，它将使用该云数据库。

对于 MongoDB，本教程使用了 [Azure Cosmos DB](/azure/documentdb/)。 Cosmos DB 支持 MongoDB 客户端连接。

### <a name="create-a-resource-group"></a>创建资源组

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-cosmos-db-account"></a>创建 Cosmos DB 帐户

> [!NOTE]
> 在本教程中，在你自己的 Azure 订阅中创建 Azure Cosmos DB 数据库需付费。 若要使用七天免费的 Azure Cosmos DB 帐户，可以使用[免费试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) 体验。 直接单击 MongoDB 磁贴中的“创建”按钮即可在 Azure 上创建免费的 MongoDB 数据库。 创建数据库以后，请在门户中导航到“连接字符串”，然后检索 Azure Cosmos DB 连接字符串，以便在本教程的后面使用。
>

在 Cloud Shell 中，使用 [`az cosmosdb create`](/cli/azure/cosmosdb#az_cosmosdb_create) 命令创建 Cosmos DB 帐户。

在下面的命令中，用唯一 Cosmos DB 名称替换 \<cosmosdb-name> 占位符。 此名称用作 Cosmos DB 终结点 `https://<cosmosdb-name>.documents.azure.com/` 的一部分，因此需要在 Azure 中的所有 Cosmos DB 帐户中具有唯一性。 它只能包含小写字母、数字及连字符(-)，长度必须为 3 到 50 个字符。

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

--kind MongoDB 参数启用 MongoDB 客户端连接。

创建 Cosmos DB 帐户后，Azure CLI 会显示类似于以下示例的信息：

<pre>
{
  "consistencyPolicy":
  {
    "defaultConsistencyLevel": "Session",
    "maxIntervalInSeconds": 5,
    "maxStalenessPrefix": 100
  },
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://&lt;cosmosdb-name&gt;.documents.azure.com:443/",
  "failoverPolicies": 
  ...
  &lt; Output truncated for readability &gt;
}
</pre>

## <a name="connect-app-to-production-mongodb"></a>将应用连接到生产 MongoDB

在此步骤中，将使用 MongoDB 连接字符串将 MEAN.js 示例应用程序连接至刚创建的 Cosmos DB 数据库。 

### <a name="retrieve-the-database-key"></a>检索数据库键

要连接至到 Cosmos DB 数据库，需要数据库键。 在 Cloud Shell 中，使用 [`az cosmosdb list-keys`](/cli/azure/cosmosdb#az-cosmosdb-list-keys) 命令检索主键。

```azurecli-interactive
az cosmosdb list-keys --name <cosmosdb-name> --resource-group myResourceGroup
```

Azure CLI 显示类似于以下示例的信息：

<pre>
{
  "primaryMasterKey": "RS4CmUwzGRASJPMoc0kiEvdnKmxyRILC9BWisAYh3Hq4zBYKr0XQiSE4pqx3UchBeO4QRCzUt1i7w0rOkitoJw==",
  "primaryReadonlyMasterKey": "HvitsjIYz8TwRmIuPEUAALRwqgKOzJUjW22wPL2U8zoMVhGvregBkBk9LdMTxqBgDETSq7obbwZtdeFY7hElTg==",
  "secondaryMasterKey": "Lu9aeZTiXU4PjuuyGBbvS1N9IRG3oegIrIh95U6VOstf9bJiiIpw3IfwSUgQWSEYM3VeEyrhHJ4rn3Ci0vuFqA==",
  "secondaryReadonlyMasterKey": "LpsCicpVZqHRy7qbMgrzbRKjbYCwCKPQRl0QpgReAOxMcggTvxJFA94fTi0oQ7xtxpftTJcXkjTirQ0pT7QFrQ=="
}
</pre>

复制 `primaryMasterKey` 的值。 下一步需要用到此信息。

<a name="devconfig"></a>
### <a name="configure-the-connection-string-in-your-nodejs-application"></a>在 Node.js 应用程序中配置连接字符串

在本地 MEAN.js 存储库的 _config/env/_ 文件夹中，创建名为 _local-production.js_ 的文件。 已配置 .gitignore，以确保此文件位于存储库之外。 

将以下代码复制到该文件中。 请确保将两个 \<cosmosdb-name> 占位符替换为 Cosmos DB 数据库名称，将 \<primary-master-key> 占位符替换为在先前步骤中复制的键。

```javascript
module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb-name>:<primary-master-key>@<cosmosdb-name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false'
  }
};
```

`ssl=true` 选项是必需的，因为 [Cosmos DB 需要 SSL](../cosmos-db/connect-mongodb-account.md#connection-string-requirements)。 

保存所做更改。

### <a name="test-the-application-in-production-mode"></a>在生产模式下测试应用程序 

在本地终端窗口中运行以下命令，为生产环境缩小和捆绑脚本。 这一过程将生成生产环境所需的文件。

```bash
gulp prod
```

在本地终端窗口中运行下列命令，以使用在 _config/env/local-production.js_ 中配置的连接字符串。 忽略证书错误和 config.domain 警告。

```bash
# Bash
NODE_ENV=production node server.js

# Windows PowerShell
$env:NODE_ENV = "production" 
node server.js
```

`NODE_ENV=production` 设置环境变量，该变量指示 Node.js 在生产环境中运行。  `node server.js` 使用存储库根路径中的 `server.js` 启动 Node.js 服务器。 这就是 Node.js 应用程序在 Azure 中加载的方式。 

在加载应用时请进行检查，确保它在生产环境中运行：

<pre>
--
MEAN.JS

环境：生产服务器：        http://0.0.0.0:8443 数据库：mongodb://&lt; cosmosdb-name&gt;:&lt; primary-master-key&gt;@&lt; cosmosdb-name&gt;.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false 应用版本：   0.5.0 MEAN.JS 版本：0.5.0
</pre>

在浏览器中导航到 `http://localhost:8443`。 单击菜单顶部的“注册”，并创建测试用户。 如果创建用户并登录成功，则应用会将数据写入 Azure 中的 Cosmos DB 数据库。 

在终端中，通过键入 `Ctrl+C` 停止 Node.js。 

## <a name="deploy-app-to-azure"></a>将应用部署到 Azure

在此步骤中，将连接了 MongoDB 的 Node.js 应用程序部署至 Azure 应用服务。

### <a name="configure-a-deployment-user"></a>配置部署用户

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>创建应用服务计划

::: zone pivot="platform-windows"  

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

::: zone-end

<a name="create"></a>
### <a name="create-a-web-app"></a>创建 Web 应用

::: zone pivot="platform-windows"  

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-nodejs-no-h.md)] 

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-nodejs-linux-no-h.md)] 

::: zone-end

### <a name="configure-an-environment-variable"></a>配置环境变量

默认情况下，MEAN.js 项目会在 Git 存储库外部保留 _config/env/local-production.js_。 因此对于 Azure 应用，请使用应用设置来定义 MongoDB 连接字符串。

若要设置应用设置，请在 Cloud Shell 中使用 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) 命令。 

以下示例在 Azure 应用中配置 `MONGODB_URI` 应用设置。 替换占位符 \<app-name>、\<cosmosdb-name> 和 \<primary-master-key>。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings MONGODB_URI="mongodb://<cosmosdb-name>:<primary-master-key>@<cosmosdb-name>.documents.azure.com:10250/mean?ssl=true"
```

在 Node.js 代码中，使用 `process.env.MONGODB_URI`[访问此应用设置](configure-language-nodejs.md#access-environment-variables)，如同访问任何环境变量那样。 

在本地 MEAN.js 存储库中，打开具有特定于生产环境的配置的 _config/env/production.js_（而不是 _config/env/local-production.js_）。 默认 MEAN.js 应用已配置为使用你所创建的 `MONGODB_URI` 环境变量。

```javascript
db: {
  uri: ... || process.env.MONGODB_URI || ...,
  ...
},
```

### <a name="push-to-azure-from-git"></a>从 Git 推送到 Azure

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (5/5), 489 bytes | 0 bytes/s, done.
Total 5 (delta 3), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6c7c716eee'.
remote: Running custom deployment command...
remote: Running deployment command...
remote: Handling node.js deployment.
.
.
.
remote: Deployment successful.
To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
 * [new branch]      master -> master
</pre>

你可能会注意到，部署进程将在运行 `npm install` 之后运行 [Gulp](https://gulpjs.com/)。 应用服务在部署期间不会运行 Gulp 或 Grunt 任务，因此该示例存储库的根目录中有两个额外文件用于启用它： 

- _.deployment_ - 此文件告知应用服务将 `bash deploy.sh` 作为自定义部署脚本运行。
- _deploy.sh_ - 自定义部署脚本。 如果查看该文件，则将看到它在 `npm install` 和 `bower install` 之后运行 `gulp prod`。 

可以通过此方法向基于 Git 的部署添加任意步骤。 如果重启 Azure 应用（无论何时），应用服务都不会重新运行这些自动化任务。 有关详细信息，请参阅[运行 Grunt/Bower/Gulp](configure-language-nodejs.md#run-gruntbowergulp)。

### <a name="browse-to-the-azure-app"></a>转到 Azure 应用 

使用 Web 浏览器转到已部署的应用。 

```bash 
http://<app-name>.azurewebsites.net 
``` 

单击菜单顶部的“注册”，创建虚拟用户。 

如果操作成功，且应用自动登录到已创建的用户，则 Azure 中的 MEAN.js 应用已连接至 MongoDB (Cosmos DB) 数据库。 

![在 Azure 应用服务中运行的 MEAN.js 应用](./media/tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

选择“管理员”>“管理文章”，添加一些文章。 

祝贺你！ 正在 Azure 应用服务中运行数据驱动的 Node.js 应用。

## <a name="update-data-model-and-redeploy"></a>更新数据模型和重新部署

在此步骤中，将对 `article` 数据模型进行一些更改，并将更改发布至 Azure。

### <a name="update-the-data-model"></a>更新数据模型

在本地 MEAN.js 存储库中，打开 _modules/articles/server/models/article.server.model.js_。

在 `ArticleSchema` 中，添加名为 `comment` 的 `String` 类型。 完成后，架构代码应该如下所示：

```javascript
const ArticleSchema = new Schema({
  ...,
  user: {
    type: Schema.ObjectId,
    ref: 'User'
  },
  comment: {
    type: String,
    default: '',
    trim: true
  }
});
```

### <a name="update-the-articles-code"></a>更新文章代码

更新剩余 `articles` 代码以使用 `comment`。

需修改的文件共计五个：服务器控制器以及四个客户端视图。 

打开 modules/articles/server/controllers/articles.server.controller.js。

在 `update` 函数中，添加 `article.comment` 的赋值。 以下代码显示完整的 `update` 函数：

```javascript
exports.update = function (req, res) {
  let article = req.article;

  article.title = req.body.title;
  article.content = req.body.content;
  article.comment = req.body.comment;

  ...
};
```

打开 modules/articles/client/views/view-article.client.view.html。

在 `</section>` 结尾标记正上方，添加下列行以显示 `comment` 和其余文章数据：

```html
<p class="lead" ng-bind="vm.article.comment"></p>
```

打开 modules/articles/client/views/list-articles.client.view.html。

在 `</a>` 结尾标记正上方，添加下列行以显示 `comment` 和其余文章数据：

```html
<p class="list-group-item-text" ng-bind="article.comment"></p>
```

打开 modules/articles/client/views/admin/list-articles.client.view.html。

在 `<div class="list-group">` 元素内，以及 `</a>` 结尾标记正上方，添加下列行以显示 `comment` 和其余文章数据：

```html
<p class="list-group-item-text" data-ng-bind="article.comment"></p>
```

打开 modules/articles/client/views/admin/form-article.client.view.html。

查找包含提交按钮的 `<div class="form-group">` 元素，如下所示：

```html
<div class="form-group">
  <button type="submit" class="btn btn-default">{{vm.article._id ? 'Update' : 'Create'}}</button>
</div>
```

在此标记的正上方，添加另一个 `<div class="form-group">` 元素，它允许人们编辑 `comment` 字段。 新元素应如下所示：

```html
<div class="form-group">
  <label class="control-label" for="comment">Comment</label>
  <textarea name="comment" data-ng-model="vm.article.comment" id="comment" class="form-control" cols="30" rows="10" placeholder="Comment"></textarea>
</div>
```

### <a name="test-your-changes-locally"></a>在本地测试更改

保存所有更改。

在本地终端窗口中，在生产模式下再次测试所做的更改。

```bash
# Bash
gulp prod
NODE_ENV=production node server.js

# Windows PowerShell
gulp prod
$env:NODE_ENV = "production" 
node server.js
```

在浏览器中导航至 `http://localhost:8443`，并确保已登录。

选择“管理员”>“管理文章”，然后选择 **+** 按钮添加文章。

现在你将看到新 `Comment` 文本框。

![向文章添加注释字段](./media/tutorial-nodejs-mongodb-app/added-comment-field.png)

在终端中，通过键入 `Ctrl+C` 停止 Node.js。 

### <a name="publish-changes-to-azure"></a>发布对 Azure 所做的更改

在本地终端窗口中，提交在 Git 中所做的更改，然后将代码更改推送到 Azure。

```bash
git commit -am "added article comment"
git push azure master
```

`git push` 完成后，请导航到 Azure 应用，并试用新功能。

![发布到 Azure 的模型和数据库更改](media/tutorial-nodejs-mongodb-app/added-comment-field-published.png)

如果先前添加过任何文章，现在仍能看到它们。 Cosmos DB 中的现有数据没有丢失。 同时，对数据架构的更新和现有数据都将保持不变。

## <a name="stream-diagnostic-logs"></a>流式传输诊断日志 

::: zone pivot="platform-windows"  

当 Node.js 应用程序在 Azure 应用服务中运行时，可以将控制台日志通过管道传输到终端。 如此，可以获得相同的诊断消息，以便调试应用程序错误。

若要启动日志流式处理，请在 Cloud Shell 中使用 [`az webapp log tail`](/cli/azure/webapp/log#az-webapp-log-tail) 命令。

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
``` 

启动日志流式处理后，请在浏览器中刷新 Azure 应用，以获取一些 Web 流量。 现在将看到通过管道传送到终端的控制台日志。

通过键入 `Ctrl+C`，随时停止日志流式处理。 

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

## <a name="manage-your-azure-app"></a>管理 Azure 应用

转到 [Azure 门户](https://portal.azure.com)查看创建的应用。

在左侧菜单中单击“应用服务”，然后单击 Azure 应用的名称。

![在门户中导航到 Azure 应用](./media/tutorial-nodejs-mongodb-app/access-portal.png)

默认情况下，门户将显示应用的“概述”页。 在此页中可以查看应用的运行状况。 在此处还可以执行基本的管理任务，例如浏览、停止、启动、重新启动和删除。 该页左侧的选项卡显示可以打开的不同配置页。

![Azure 门户中的应用服务页](./media/tutorial-nodejs-mongodb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>后续步骤

你已了解：

> [!div class="checklist"]
> * 在 Azure 中创建 MongoDB 数据库
> * 将 Node.js 应用连接到 MongoDB
> * 将应用部署到 Azure
> * 更新数据模型并重新部署应用
> * 将日志从 Azure 流式传输到终端
> * 在 Azure 门户中管理应用

转到下一教程，了解如何向应用映射自定义 DNS 名称。

> [!div class="nextstepaction"] 
> [将现有的自定义 DNS 名称映射到 Azure 应用服务](app-service-web-tutorial-custom-domain.md)

或者，查看其他资源：

> [!div class="nextstepaction"]
> [配置 Node.js 应用](configure-language-nodejs.md)
