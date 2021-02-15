---
title: 快速入门：发布知识库、REST、Java QnA Maker
description: 此基于 Java REST 的快速入门发布你的知识库，并创建可在应用程序或聊天机器人中调用的终结点。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-java
ms.topic: how-to
ms.openlocfilehash: 8e2e902e0563e0f4ae8c0c3d0dc795a8260c62db
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351157"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-java"></a>快速入门：在 QnA Maker 中通过 Java 发布知识库

此基于 REST 的快速入门将指导你完成以编程方式发布知识库 (KB) 的过程。 发布操作会将知识库的最新版本推送到一个专用 Azure 认知搜索引擎，并创建一个可以在应用程序或聊天机器人中调用的终结点。

本快速入门调用了 QnA Maker API：
* [发布](/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) - 此 API 不需要请求正文中的任何信息。

## <a name="prerequisites"></a>先决条件

* [JDK SE](/azure/developer/java/fundamentals/java-jdk-long-term-support)（Java 开发工具包，标准版）
* 此示例使用 HTTP 组件中的 Apache [HTTP 客户端](https://hc.apache.org/httpcomponents-client-ga/)。 需向项目添加以下 Apache HTTP 客户端库：
    * httpclient-4.5.3.jar
    * httpcore-4.4.6.jar
    * commons-logging-1.2.jar
* [Visual Studio Code](https://code.visualstudio.com/)
* 您必须具有 [QnA Maker 服务](../How-To/set-up-qnamaker-service-azure.md)。 若要检索密钥和终结点（包括资源名称），请在 Azure 门户中为资源选择“快速入门”。
* 在 `kbid` 查询字符串参数的 URL 中找到的 QnA Maker 知识库 (KB) ID，如下所示。

    ![QnA Maker 知识库 ID](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    如果还没有知识库，可以创建一个用于此快速入门的示例知识库：[创建新的知识库](create-new-kb-csharp.md)。

> [!NOTE]
> 完整的解决方案文件 () 可从 [ **Azure 示例/认知-qnamaker** GitHub 存储库](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/tree/master/documentation-samples/quickstarts/publish-knowledge-base)获取。

## <a name="create-a-java-file"></a>创建 Java 文件

打开 VSCode 并创建名为 `PublishKB.java` 的新文件。

## <a name="add-the-required-dependencies"></a>添加必需的依赖项

在 `PublishKB.java` 的顶部，类的上方，添加以下行来向项目添加必需的依赖项：

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/PublishKB.java" id="dependencies":::

## <a name="create-publishkb-class-with-main-method"></a>使用 main 方法创建 PublishKB 类

在依赖项之后添加以下类：

```Go
public class PublishKB {

    public static void main(String[] args)
    {
    }
}
```

## <a name="add-required-constants"></a>添加必需的常量

在 **main** 方法中，添加所需的常量以访问 QnA Maker。 将值替换成自己的值。

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/PublishKB.java" id="constants":::

## <a name="add-post-request-to-publish-knowledge-base"></a>添加 POST 请求来发布知识库

在所需常量后添加以下代码，以便向 QnA Maker API 发出 HTTPS 请求，目的是发布知识库并接收响应：

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/PublishKB.java" id="post":::

对于成功的发布，该 API 调用会返回一个 204 状态，并且响应正文中没有任何内容。 该代码为 204 响应添加了内容。

对于任何其他响应，该响应将原样返回，不做任何更改。

## <a name="build-and-run-the-program"></a>生成并运行程序

通过命令行生成并运行程序。 它会自动向 QnA Maker API 发送请求，然后输出到控制台窗口。

1. 生成文件：

    ```bash
    javac -cp "lib/*" PublishKB.java
    ```

1. 运行文件：

    ```bash
    java -cp ".;lib/*" PublishKB
    ```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>后续步骤

在发布知识库后，需要[生成答案所需的终结点 URL](./get-answer-from-knowledge-base-java.md)。

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API 参考](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)