---
title: 快速入门： Node.js 的 REST Api QnA Maker
description: 本快速入门介绍如何开始使用适用于 Node.js 的 QnA Maker REST API。 请按照以下步骤安装程序包并试用基本任务的示例代码。  使用 QnA Maker，可以根据常见问题解答文档、URL 和产品手册等半结构化内容打造一项问题与解答服务。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-js
ms.topic: how-to
ms.openlocfilehash: 645f9ffb1d907eb576f5414b593ef57679d26daa
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351140"
---
# <a name="quickstart-qna-maker-rest-apis-for-nodejs"></a>快速入门： Node.js 的 QnA Maker REST Api

适用于 Node.js 的 QnA Maker REST API 入门。 遵循以下步骤尝试学习基本任务的示例代码。  使用 QnA Maker，可以根据常见问题解答文档、URL 和产品手册等半结构化内容打造一项问题与解答服务。

使用适用于 Node.js 的 QnA Maker REST API 可以：

* 创建知识库
* 替换知识库
* 发布知识库
* 删除知识库
* 下载知识库
* 获取操作的状态

[参考文档](/rest/api/cognitiveservices/qnamaker/knowledgebase)  | [Node.js 示例](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/tree/master/documentation-samples/quickstarts/rest-api)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services)
* 最新版本的 [Node.js](https://nodejs.org)。
* 您必须具有 [QnA Maker 服务](../How-To/set-up-qnamaker-service-azure.md)。 若要检索密钥和终结点（包括资源名称），请在 Azure 门户中为资源选择“快速入门”。

## <a name="setting-up"></a>设置

### <a name="create-a-qna-maker-azure-resource"></a>创建 QnA Maker Azure 资源

Azure 认知服务由你订阅的 Azure 资源表示。 使用 [Azure 门户](../../cognitive-services-apis-create-account.md)或 [Azure CLI](../../cognitive-services-apis-create-account-cli.md) 在本地计算机上创建用于 QnA Maker 的资源。

从资源获取密钥后，为资源[创建环境变量](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication)（名为 `QNAMAKER_RESOURCE_KEY` 和 `QNAMAKER_AUTHORING_ENDPOINT`）。 使用在 Azure 门户中资源的“快速入门”页中找到的密钥和终结点值。

### <a name="create-a-new-nodejs-application"></a>创建新的 Node.js 应用程序

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，为应用创建一个新目录并导航到该目录。

```console
mkdir myapp && cd myapp
```

运行 `npm init -y` 命令以创建 `package.json` node 文件。

```console
npm init -y
```

添加 `reqeuestretry` 和 `request` NPM 包：

```console
npm install requestretry request --save
```

## <a name="code-examples"></a>代码示例

这些代码片段演示如何使用适用于 Node.js 的 QnA Maker REST API 执行以下操作：

* [创建知识库](#create-a-knowledge-base)
* [替换知识库](#replace-a-knowledge-base)
* [发布知识库](#publish-a-knowledge-base)
* [删除知识库](#delete-a-knowledge-base)
* [下载知识库](#download-the-knowledge-base)
* [获取操作的状态](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>添加依赖项

创建一个名为的文件 `rest-apis.js` 并添加以下依赖项。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="dependencies":::

## <a name="add-utility-functions"></a>添加实用函数

添加以下实用工具函数。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="utility":::

## <a name="add-azure-resource-information"></a>添加 Azure 资源信息

为资源的 Azure 终结点和密钥创建变量。 如果在启动应用程序后创建了环境变量，则需要关闭再重新打开运行该应用程序的编辑器、IDE 或 shell 才能访问该变量。

设置以下环境值：

* `QNAMAKER_RESOURCE_KEY` - **密钥** 为32字符串，在 "Azure 门户" 的 "QnA Maker 资源" 的 " **快速启动** " 页上提供。 这与预测终结点密钥不同。
* `QNAMAKER_AUTHORING_ENDPOINT` - 创作终结点的格式为 `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`，其中包括 **资源名称**。 这与用于查询预测终结点的 URL 不同。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="authorization":::

## <a name="create-a-knowledge-base"></a>创建知识库

知识库将存储基于以下内容的 JSON 对象创建的问题和回答对：

* **编辑内容**。
* **文件** - 不需要任何权限的本地文件。
* **URL** - 公用的 URL。

[使用 REST API 创建知识库](/rest/api/cognitiveservices/qnamaker/knowledgebase/create)。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="createKb":::

## <a name="replace-a-knowledge-base"></a>替换知识库

[使用 REST API 替换知识库](/rest/api/cognitiveservices/qnamaker/knowledgebase/replace)。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="replaceKb":::

## <a name="publish-a-knowledge-base"></a>发布知识库

发布知识库。 此进程使得知识库可从 HTTP 查询预测终结点使用。

[使用 REST API 发布知识库](/rest/api/cognitiveservices/qnamaker/knowledgebase/publish)。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="publishKb":::

## <a name="download-the-knowledge-base"></a>下载知识库

[使用 REST API 下载知识库](/rest/api/cognitiveservices/qnamaker/knowledgebase/download)。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="downloadKb":::

## <a name="delete-a-knowledge-base"></a>删除知识库

用完知识库后，请将其删除。

[使用 REST API 删除知识库](/rest/api/cognitiveservices/qnamaker/knowledgebase/delete)。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="deleteKb":::

## <a name="get-status-of-an-operation"></a>获取操作的状态

长时间运行的进程（例如创建进程）将返回一个操作 ID，需要使用单独的 REST API 调用来检查该 ID。 此函数采用 create 响应的正文。 重要的键是 `operationState`，它确定是否需要继续轮询。

[使用 REST API 监视对知识库执行的操作](/rest/api/cognitiveservices/qnamaker/operations/getdetails)。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="operationDetails":::

## <a name="add-main-method"></a>添加 main 方法

添加以下 `main` 方法。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="main":::

## <a name="run-the-application"></a>运行应用程序

从应用程序目录使用 `node rest-apis.js` 命令运行应用程序。

```console
node rest-apis.js
```

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [门户](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
>[教程：创建和解答知识库](./create-publish-knowledge-base.md)

* [什么是 QnA Maker API？](../Overview/overview.md)
* [编辑知识库](../how-to/edit-knowledge-base.md)
* [获取使用情况分析](../how-to/get-analytics-knowledge-base.md)
* 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/rest-api/rest-api.js) 上找到此示例的源代码。