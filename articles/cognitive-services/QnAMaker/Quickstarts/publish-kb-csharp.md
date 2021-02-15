---
title: '快速入门：发布知识库，REST，c # QnA Maker'
description: 此基于 C# REST 的快速入门发布你的知识库，并创建可在应用程序或聊天机器人中调用的终结点。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-csharp
ms.topic: how-to
ms.openlocfilehash: abaccfb1a1f36e1f7f47d3f5acefb08e2091cb39
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351174"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-c"></a>快速入门：在 QnA Maker 中通过 C# 发布知识库

此基于 REST 的快速入门将指导你完成以编程方式发布知识库 (KB) 的过程。 发布操作会将知识库的最新版本推送到一个专用 Azure 认知搜索引擎，并创建一个可以在应用程序或聊天机器人中调用的终结点。

本快速入门调用了 QnA Maker API：
* [发布](/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) - 此 API 不需要请求正文中的任何信息。

## <a name="prerequisites"></a>先决条件

* 最新的 [**Visual Studio Community Edition**](https://www.visualstudio.com/downloads/)。
* 您必须具有 [QnA Maker 服务](../How-To/set-up-qnamaker-service-azure.md)。 若要检索密钥和终结点（包括资源名称），请在 Azure 门户中为资源选择“快速入门”。
* 在 `kbid` 查询字符串参数的 URL 中找到的 QnA Maker 知识库 (KB) ID，如下所示。

    ![QnA Maker 知识库 ID](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    如果还没有知识库，可以创建一个用于此快速入门的示例知识库：[创建新的知识库](create-new-kb-csharp.md)。

> [!NOTE]
> 完整的解决方案文件可从 [Azure-Samples/cognitive-services-qnamaker-csharp GitHub 存储库](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/quickstarts/publish-knowledge-base)获得。

## <a name="create-knowledge-base-project"></a>创建知识库项目

1. 打开 Visual Studio 2019 Community Edition。
1. 创建一个新的“控制台应用(.NET Core)”项目并将其命名为 `QnaMakerQuickstart`。 接受其余设置的默认值。

## <a name="add-required-dependencies"></a>添加必需的依赖项

在 Program.cs 的顶部，将单个 using 语句替换为以下行，以向项目添加必要的依赖项：

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/publish-kb.cs" id="dependencies":::

## <a name="add-required-constants"></a>添加必需的常量

在 **Program** 类中，添加访问 QnA Maker 所需的常量。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/publish-kb.cs" id="constants":::

## <a name="add-the-main-method-to-publish-the-knowledge-base"></a>添加 Main 方法以发布知识库

在所需常量后添加以下代码，以便向 QnA Maker API 发出 HTTPS 请求，目的是发布知识库并接收响应：

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/publish-kb.cs" id="post":::

对于成功的发布，该 API 调用会返回一个 204 状态，并且响应正文中没有任何内容。

## <a name="build-and-run-the-program"></a>生成并运行程序

生成并运行程序。 它将自动向 QnA Maker API 发送发布知识库的请求，然后，响应将输出到控制台窗口。

发布知识库后，可以使用客户端应用程序或聊天机器人从终结点对其进行查询。

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>后续步骤

在发布知识库后，需要[生成答案所需的终结点 URL](./get-answer-from-knowledge-base-csharp.md)。

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API 参考](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)