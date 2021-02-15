---
title: 快速入门：从知识库获取答案 - REST、Java - QnA Maker
description: 此基于 Java REST 的快速入门详细介绍如何以编程方式从知识库获取答案。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-java
ms.topic: how-to
ms.openlocfilehash: 8075371bddfc92d7477b3b036575836aa65f1cd0
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96346025"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-java"></a>快速入门：通过 Java 获取知识库中问题的答案

本快速入门详细介绍如何以编程方式从已发布的 QnA Maker 知识库获取答案。 知识库包含来自[数据源](../index.yml)的问题和答案，例如常见问题解答。 [问题](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration)将发送到 QnA Maker 服务。 [响应](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties)包含最常见的预测答案。

[参考文档](/rest/api/cognitiveservices/qnamaker4.0/Runtime) | [示例](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/blob/master/documentation-samples/quickstarts/get-answer/GetAnswer.java)

## <a name="prerequisites"></a>先决条件

* [JDK SE](/azure/developer/java/fundamentals/java-jdk-long-term-support)（Java 开发工具包，标准版）
* 此示例使用 HTTP 组件中的 Apache [HTTP 客户端](https://hc.apache.org/httpcomponents-client-ga/)。 需向项目添加以下 Apache HTTP 客户端库：
    * httpclient-4.5.3.jar
    * httpcore-4.4.6.jar
    * commons-logging-1.2.jar
* [Visual Studio Code](https://code.visualstudio.com/)
* 您必须具有 [QnA Maker 服务](../How-To/set-up-qnamaker-service-azure.md)。 若要检索密钥，请在适用于 QnA Maker 资源的 Azure 仪表板的“资源管理”下选择“密钥”。
* “发布”页设置。 如果没有已发布的知识库，请创建一个空的知识库，接着“设置”页上导入一个知识库，然后进行发布。 可以下载并使用[这个基本的知识库](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv)。

    发布页设置包括 POST 路由值、Host 值和 EndpointKey 值。

    ![发布设置](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-java-file"></a>创建 Java 文件

打开 VSCode，创建名为 `GetAnswer.java` 的新文件，然后添加以下类：

```Java
public class GetAnswer {

    public static void main(String[] args)
    {

    }
}
```

## <a name="add-the-required-dependencies"></a>添加必需的依赖项

本快速入门使用适合 HTTP 请求的 Apache 类。 在 GetAnswer 类上方，`GetAnswer.java` 文件顶部，向项目添加必需的依赖项：

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/QueryKB.java" id="dependencies":::

## <a name="add-the-required-constants"></a>添加必需的常量

在 `GetAnswer.java` 类的顶部，添加必需的常量来访问 QnA Maker。 发布知识库后，这些值会出现在“发布”页上。

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/QueryKB.java" id="constants":::

## <a name="add-a-post-request-to-send-question"></a>添加一个 POST 请求来发送问题

以下代码向 QnA Maker API 发出 HTTPS 请求，以便向知识库发送问题并接收响应：

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/QueryKB.java" id="post":::

`Authorization` 标头的值包括字符串 `EndpointKey`。

详细了解[请求](../how-to/metadata-generateanswer-usage.md#generateanswer-request)和[响应](../how-to/metadata-generateanswer-usage.md#generateanswer-response)。

## <a name="build-and-run-the-program"></a>生成并运行程序

通过命令行生成并运行程序。 它会自动向 QnA Maker API 发送请求，然后输出到控制台窗口。

1. 生成文件：

    ```bash
    javac -cp "lib/*" GetAnswer.java
    ```

1. 运行文件：

    ```bash
    java -cp ".;lib/*" GetAnswer
    ```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]


[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API 参考](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)