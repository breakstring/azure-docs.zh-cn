---
title: 快速入门：创建、训练和发布知识库 - QnA Maker
description: 可以根据自己的内容（例如常见问题解答或产品手册）创建一个 QnA Maker 知识库 (KB)。 本文包含一个从简单的 FAQ 网页创建 QnA Maker 知识库以回答 QnA Maker 相关问题的示例。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 11/09/2020
ms.openlocfilehash: c59529db0981a1071b76714c48aacaf675e4b17a
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2021
ms.locfileid: "99987907"
---
# <a name="quickstart-create-train-and-publish-your-qna-maker-knowledge-base"></a>快速入门：创建、训练和发布 QnA Maker 知识库

可以根据自己的内容（例如常见问题解答或产品手册）创建一个 QnA Maker 知识库 (KB)。 本文包含一个从简单的 FAQ 网页创建 QnA Maker 知识库以回答 QnA Maker 相关问题的示例。

## <a name="prerequisites"></a>先决条件

> [!div class="checklist"]
> * 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/cognitive-services/)。
> * 在 Azure 门户中创建的 QnA Maker [资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)。 请记住你在创建资源时选择的 Azure Active Directory ID、订阅、QnA 资源名称。

## <a name="create-your-first-qna-maker-knowledge-base"></a>创建第一个 QnA Maker 知识库

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

1. 使用 Azure 凭据登录到 [QnAMaker.ai](https://QnAMaker.ai) 门户。

2. 在 QnA Maker 门户中，选择“创建知识库”。

3. 在“创建”页上，如果你已经有 QnA Maker 资源，请跳过“步骤 1”。

    如果尚未创建此资源，请选择“创建 QnA 服务”。 此时会将你定向到 [Azure 门户](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)，让你在订阅中设置 QnA Maker 服务。 请记住你在创建资源时选择的 Azure Active Directory ID、订阅、QnA 资源名称。

    在 Azure 门户中创建资源后，请返回到 QnA Maker 门户，刷新浏览器页，并继续完成“步骤 2”。

4. 在“步骤 2”中，为在服务中创建的所有知识库选择 Active Directory、订阅、服务（资源）和语言。

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/qnaservice-selection.png" alt-text="选择 QnA Maker 服务知识库的屏幕截图":::

5. 在“步骤 3”中，将知识库命名为“我的示例 QnA KB”。

6. 在 **步骤 4** 中，按照下表配置设置：

    |设置|值|
    |--|--|
    |**允许从 URL、.pdf 或 .docx 文件进行多轮提取。**|已选中|
    |**多回合默认文本**| 选择和选项|
    |**+ 添加 URL**|`https://www.microsoft.com/en-us/software-download/faq`|
    |**聊天内容**|选择“专业”|

7. 在“步骤 5”中，选择“创建 KB” 。

    提取过程需要一些时间来读取文档并确定问题和解答。

    QnA Maker 成功创建知识库后，“知识库”页会打开。 可在此页面上编辑知识库的内容。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/v2)

1. 使用 Azure 凭据登录到 [QnAMaker.ai](https://QnAMaker.ai) 门户。

2. 在 QnA Maker 门户中，选择“创建知识库”。

3. 在“创建”页上，如果你已经有 QnA Maker 资源，请跳过“步骤 1”。

    如果尚未创建此资源，请选择“创建 QnA 服务”。 此时会将你定向到 [Azure 门户](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)，让你在订阅中设置 QnA Maker 服务。 请记住你在创建资源时选择的 Azure Active Directory ID、订阅、QnA 资源名称。

    在 Azure 门户中创建资源后，请返回到 QnA Maker 门户，刷新浏览器页，并继续完成“步骤 2”。

4. 在“步骤 2”中，为在服务中创建的所有知识库选择 Active Directory、订阅、服务（资源）和语言。

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/connect-your-knowledge-base.png" alt-text="选择 QnA Maker 服务知识库托管预览版的屏幕截图":::

5. 在“步骤 2”中，如果你要为服务创建第一个知识库，你将可以选择让语言设置特定于每个知识库。 为第一个知识库定义语言设置后，不允许在以后为服务修改设置。

6. 在“步骤 3”中，将知识库命名为“我的示例 QnA KB” ****  **** 。 

7. 在 **步骤 4** 中，按照下表配置设置：

    |设置|值|
    |--|--|
    |**允许从 URL、.pdf 或 .docx 文件进行多轮提取。**|已选中|
    |**多回合默认文本**| 选择和选项|
    |**+ 添加文件**| 从“https://download.microsoft.com/download/7/B/1/7B10C82E-F520-4080-8516-5CF0D803EEE0/surface-book-user-guide-EN.pdf”下载 Surface 笔记本电脑手册 
    |**聊天内容**|选择“专业”|

8. 在“步骤 5”中，选择“创建 KB” 。

    提取过程需要一些时间来读取文档并确定问题和解答。

    QnA Maker 成功创建知识库后，“知识库”页会打开。 可在此页面上编辑知识库的内容。

---

## <a name="add-a-new-question-and-answer-set"></a>添加新的问答集

1. 在 QnA Maker 门户的“编辑”页上，从上下文工具栏中选择“+ 添加 QnA 对”。
1. 添加以下问题：

    `How many Azure services are used by a knowledge base?`

1. 添加 _markdown_ 格式的答案：

    ` * Azure QnA Maker service\n* Azure Cognitive Search\n* Azure web app\n* Azure app plan`

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/add-question-and-answer.png" alt-text="添加文本格式的问题和 markdown 格式的答案。":::

    markdown 符号 `*` 用于项目符号点。 `\n` 用于新行。

    “编辑”页显示 markdown。 稍后使用“测试”面板时，会看到 markdown 显示正确。

## <a name="save-and-train"></a>保存并训练

在右上角选择“保存并训练”，以便保存所做的编辑并训练 QnA Maker。 如果不保存，编辑的内容不会保留。

## <a name="test-the-knowledge-base"></a>测试知识库

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

1. 在 QnA Maker 门户的右上角，选择“测试”以测试所做的更改是否已生效。
2. 在文本框中输入示例用户查询。

    `I want to know the difference between 32 bit and 64 bit Windows`

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/query-dialogue.png" alt-text="在文本框中输入示例用户查询。":::

3. 选择“检查”，更详细地检查响应。 测试窗口用于测试对知识库所做的更改，然后会发布知识库。

4. 再次选择“测试”，关闭“测试”面板。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/v2)

1. 在 QnA Maker 门户的右上角，选择“测试”以测试所做的更改是否已生效。
2. 在文本框中输入示例用户查询。

    `whats the size of the touchscreen`

3. 如果你为知识库启用了 MRC 功能，通过选择“显示简短答案”，除了测试窗格中的答案段落外，还会显示一个精确答案（如果有的话）。 

    ![托管的已启用的测试窗格](../media/conversational-context/test-pane-with-managed.png)
    

4. 选择“检查”，更详细地检查响应。 测试窗口用于测试对知识库所做的更改，然后会发布知识库。 
5. 再次选择“测试”，关闭“测试”面板。
---

## <a name="publish-the-knowledge-base"></a>发布知识库

发布知识库时，知识库的内容将从 `test` 索引转移到 Azure 搜索中的 `prod` 索引。

![移动知识库内容的屏幕截图](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. 在 QnA Maker 门户中，选择“发布”。 然后，若要进行确认，请在页面上选择“发布”。

    QnA Maker 服务现在已成功发布。 可以在应用程序或机器人代码中使用此终结点。

    ![成功发布的屏幕截图](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint.png)

## <a name="create-a-bot"></a>创建机器人

发布后，可以从“发布”页创建机器人：

* 可以快速创建多个机器人，它们全部可以指向不同区域中的同一个知识库，或者指向单个机器人的定价计划。
* 如果只想为知识库创建一个机器人，请使用“在 Azure 门户中查看所有机器人”链接查看当前机器人的列表。

对知识库进行更改并重新发布时，不需要对机器人采取进一步的操作。 机器人已配置为使用该知识库，并会处理将来对该知识库所做的所有更改。 每当发布某个知识库时，连接到该知识库的所有机器人都会自动更新。

1. 在 QnA Maker 门户中的“发布”页上，选择“创建机器人”。 只有在发布知识库后，才会显示此按钮。

    ![创建机器人的屏幕截图](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. 此时会打开 Azure 门户的新浏览器标签页，其中显示了 Azure 机器人服务的创建页。 配置 Azure 机器人服务。 机器人和 QnA Maker 可以共享 Web 应用服务计划，但不能共享 Web 应用。 这意味着，机器人的 **应用名称** 必须不同于 QnA Maker 服务的应用名称。

    * **建议做法**
        * 更改机器人句柄 - 如果它不独一无二。
        * 选择 SDK 语言。 创建机器人后，可以将代码下载到本地开发环境，然后继续开发过程。
    * **不要**
        * 创建机器人时，更改 Azure 门户中的以下设置。 这些值是为现有的知识库预先填充的：
           * QnA 身份验证密钥
           * 应用服务计划和位置


1. 创建机器人后，打开“机器人服务”资源。
1. 在“机器人管理”下，选择“通过网上聊天执行测试”。
1. 出现聊天提示“键入你的消息”时，输入：

    `Azure services?`

    聊天机器人使用知识库中的一个答案进行响应。

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/test-web-chat.png" alt-text="将用户查询输入测试性 Web 聊天中。":::

## <a name="what-did-you-accomplish"></a>你完成了哪些操作？

你创建了一个新的知识库、将一个公共 URL 添加到了知识库，添加了自己的 QnA 对，以及训练、测试和发布了知识库。

在发布知识库之后，你创建了一个机器人，并测试了该机器人。

这只需几分钟即可完成，无需编写代码或清理内容。

## <a name="clean-up-resources"></a>清理资源

如果你不继续学习下一个快速入门，请在 Azure 门户中删除 QnA Maker 和 Bot 框架资源。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [添加带有元数据的问题](add-question-metadata-portal.md)

更多相关信息：

* [答案中的 Markdown 格式](../reference-markdown-format.md)
* QnA Maker [数据源](../Concepts/data-sources-and-content.md)。
