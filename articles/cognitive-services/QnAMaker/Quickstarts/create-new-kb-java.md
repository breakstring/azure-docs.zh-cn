---
title: 快速入门：创建知识库 - REST、Java - QnA Maker
description: 此基于 Java REST 的快速入门介绍如何以编程方式创建将在你的认知服务 API 帐户的 Azure 仪表板中显示的示例 QnA Maker 知识库。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-java
ms.topic: how-to
ms.openlocfilehash: 50433ee1e5a575ab671d562bfc3fe549b26fe00c
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352297"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-java"></a>快速入门：通过 Java 在 QnA Maker 中创建知识库

本快速入门将指导你完成以编程方式创建示例 QnA Maker 知识库的过程。 QnA Maker 自动从[数据源](../index.yml)中从半结构化内容（例如常见问题解答）中自动提取问题和解答。 用于知识库的模型是在 API 请求的正文中发送的 JSON 中定义的。

本快速入门调用了 QnA Maker API：
* [创建知识库](/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [获取操作详细信息](/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[参考文档](/rest/api/cognitiveservices/qnamaker/knowledgebase)  | [Java 示例](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/blob/master/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>先决条件

* [Go 1.10.1](https://golang.org/dl/)
* 您必须具有 [QnA Maker 服务](../How-To/set-up-qnamaker-service-azure.md)。 若要检索密钥和终结点（包括资源名称），请在 Azure 门户中为资源选择“快速入门”。

[示例代码](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/blob/master/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java)可在使用 Java 的 QnA Maker 的 GitHub 存储库上获得。

## <a name="create-a-knowledge-base-file"></a>创建知识库文件

创建名为 `CreateKB.java` 的文件

## <a name="add-the-required-dependencies"></a>添加必需的依赖项

在 `CreateKB.java` 的顶部，添加以下行来向项目添加必需的依赖项：

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="dependencies":::

## <a name="add-the-required-constants"></a>添加必需的常量
在上述必需的依赖项后，向 `CreateKB` 类添加访问 QnA Maker 所必需的常量。

您必须具有 [QnA Maker 服务](../How-To/set-up-qnamaker-service-azure.md)。 若要检索密钥和资源名称，请在 Azure 门户中为 QNA Maker 资源选择 **快速入门**。

设置以下值：

* `<your-qna-maker-subscription-key>` - **密钥** 是一个 32 字符的字符串，可以在“快速入门”页的 QnA Maker 资源上的 Azure 门户中使用。 此密钥与预测终结点密钥不同。
* `<your-resource-name>` - **资源名称** 用于构造用于创作的创作终结点 URL，其格式为 `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`。 此资源名称与用于查询预测终结点的名称不同。

不需要添加最后的大括号来结束该类；它位于本快速入门末尾的最终代码片段中。

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="constants":::


## <a name="add-the-kb-model-definition-classes"></a>添加知识库模型定义类
在常量之后，在 `CreateKB` 类中添加以下类和函数，以将模型定义对象序列化为 JSON。

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="model":::

## <a name="add-supporting-functions"></a>添加支持函数

接下来，在 `CreateKB` 类中添加以下支持函数。

1. 添加以下函数，用于以可读格式输出 JSON：

    :::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="pretty":::

2. 添加以下类来管理 HTTP 响应：

    :::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="response":::

3. 添加以下方法，以向 QnA Maker API 发出 POST 请求。 `Ocp-Apim-Subscription-Key` 是用于身份验证的 QnA Maker 服务密钥。

    :::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="post":::

4. 添加以下方法，以向 QnA Maker API 发出 GET 请求。

    :::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="get":::

## <a name="add-a-method-to-create-the-kb"></a>添加方法以创建知识库
添加以下方法，以通过调用 Post 方法创建知识库。

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="create_kb":::

此 API 调用将返回包括操作 ID 的 JSON 响应。 使用操作 ID 来确定知识库是否已成功创建。

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-a-method-to-get-status"></a>添加方法以获取状态
添加以下方法以检查创建状态。

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="get_status":::

重复调用，直到成功或失败：

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-a-main-method"></a>添加 main 方法
main 方法创建知识库，然后轮询状态。 操作 ID 在 POST 响应标头字段 **Location** 中返回，然后在 GET 请求中用作路由的一部分。 `while` 循环重试获取状态（如果该操作未完成）。

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="main":::

## <a name="compile-and-run-the-program"></a>编译并运行程序

1. 确保 gson 库位于 `./libs` 目录中。 在命令行中，编译以下文件 `CreateKB.java` ：

    ```bash
    javac -cp ".;libs/*" CreateKB.java
    ```

2. 在命令行中输入以下命令以运行该程序。 它向 QnA Maker API 发送创建知识库的请求，然后每隔 30 秒轮询一次结果。 每个响应都将输出到控制台窗口中。

    ```bash
    java -cp ",;libs/*" CreateKB
    ```

创建知识库以后，即可在 QnA Maker 门户 - [My knowledge bases](https://www.qnamaker.ai/Home/MyServices)（我的知识库）页中查看它。

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API 参考](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)