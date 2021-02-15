---
title: 导入文档格式准则-QnA Maker
description: 使用这些指导原则导入文档，以获得最佳内容。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 04/06/2020
ms.openlocfilehash: 15ff2ec296cedc37b086a9ca2d0825fb20b4f05a
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2021
ms.locfileid: "99549535"
---
# <a name="format-guidelines-for-imported-documents-and-urls"></a>导入的文档和 Url 的格式准则

查看这些格式设置准则，以获得最佳内容。

## <a name="formatting-considerations"></a>格式设置注意事项

导入文件或 URL 后，QnA Maker 以 [markdown 格式](https://en.wikipedia.org/wiki/Markdown)转换和存储内容。 转换过程会在文本中添加新行，例如 `\n\n` 。 Markdown 格式的知识有助于理解转换后的内容和管理知识库内容。

如果直接在知识库中添加或编辑内容，请使用 **markdown 格式** 创建丰富的文本内容，或者更改答案中已经存在的 markdown 格式内容。 QnA Maker 支持大部分 markdown 格式，以便为内容提供丰富的文本功能。 但是，客户端应用程序（如聊天机器人）可能不支持相同的一组 markdown 格式。 必须测试客户端应用程序的答案显示。

请参阅 [内容类型和示例](./concepts/data-sources-and-content.md#content-types-of-documents-you-can-add-to-a-knowledge-base)的完整列表。

## <a name="basic-document-formatting"></a>基本文档格式设置

QnA Maker 根据视觉对象线索标识文件中的部分和子节和关系，如：

* 字体大小
* 字体样式
* 方式
* 颜色

> [!NOTE]
> 当前不支持从上传的文档提取图像。

### <a name="product-manuals"></a>产品手册

手册通常是产品附带的指导材料。 它可以帮助用户设置、使用、维护产品和排除产品故障。 当 QnA Maker 处理手册时，它会将标题和副标题作为问题提取，将后续内容作为答案提取。 请参阅[此处](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf)的示例。

下面是一个手册示例，其中包含索引页和分层内容。

 ![知识库的产品手册示例](./media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> 提取最适用于具有目录和/或索引页的手册，以及具有分层标题的清晰结构。

### <a name="brochures-guidelines-papers-and-other-files"></a>小册子、指南、论文和其他文件

许多其他类型的文档也可进行处理，以便生成 QA 对，前提是它们有清晰的结构和布局。 其中包括：小册子、指导原则、报告、白皮书、科研论文、政策、书籍等。请参阅 [此处](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx)的示例。

下面是一个半结构化文档的示例，没有索引：

 ![Azure Blob 存储半结构化文档](./media/qnamaker-concepts-datasources/semi-structured-doc.png)

### <a name="structured-qna-document"></a>结构化 QnA 文档

DOC 文件中结构化问答的格式采用每行都有交替的问题和回答的形式，一个问题占据一行，后面的一行是其回答，如下所示：

```text
Question1

Answer1

Question2

Answer2
```

下面是结构化 QnA Word 文档的示例：

 ![知识库的结构化常见问题解答文档示例](./media/qnamaker-concepts-datasources/structured-qna-doc.png)

### <a name="structured-txt-tsv-and-xls-files"></a>结构化 *TXT*、*TSV* 和 *XLS* 文件

采用结构化 *.txt*、*.tsv* 或 *.xls* 文件形式的 QnA 也可上传到 QnA Maker，以便创建或增强知识库。  它们的内容可以是纯文本，也可以是 RTF 或 HTML 格式。

| 问题  | Answer  | Metadata (1 密钥：1值)  |
|-----------|---------|-------------------------|
| 问题 1 | 答案 1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| 问题 2 | 答案 2 |      `Key:Value`           |

将忽略源文件中的任何其他列。

#### <a name="example-of-structured-excel-file"></a>结构化 Excel 文件的示例

下面是结构化 QnA *.xls* 文件的示例，包含 HTML 内容：

 ![知识库的结构化常见问题解答 Excel 示例](./media/qnamaker-concepts-datasources/structured-qna-xls.png)

#### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Excel 文件中单个答案的替代问题示例

下面是结构化 *的 QnA 文件* 的一个示例，其中有几个替代问题可用于单个答案：

 ![Excel 文件中单个答案的替代问题示例](./media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

导入文件后，问题和答案对在知识库中显示，如下所示：

 ![导入到知识库中的单个答案的替代问题的屏幕截图](./media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

### <a name="structured-data-format-through-import"></a>通过导入提供的结构化数据格式

导入知识库将替换现有知识库的内容。 导入需要包含数据源信息的结构化 .tsv 文件。 这些信息可帮助 QnA Maker 将问答对分组，并将它们归因于特定数据源。

| 问题  | Answer  | 源| Metadata (1 密钥：1值)  |
|-----------|---------|----|---------------------|
| 问题 1 | 答案 1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| 问题 2 | 答案 2 | 编辑|    `Key:Value`       |

<a href="#formatting-considerations"></a>

### <a name="multi-turn-document-formatting"></a>多转换文档格式

* 使用标题和子标题来表示层次结构。 例如，可以通过 h1 表示父 QnA 和 h2，以表示应作为 prompt 出现的 QnA。 使用较小的标题尺寸来表示后续层次结构。 不要使用样式、颜色或其他某些机制来表示文档中的结构，QnA Maker 将不会提取多个提示。
* 标题的第一个字符必须大写。
* 不要以问号结束标题 `?` 。

**示例文档**：<br>[Surface Pro (.docx) ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Contoso (.docx) 权益权益 ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Contoso (pdf) 权益 ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)

## <a name="faq-urls"></a>常见问题解答 URL

QnA Maker 可以支持 3 种不同形式的常见问题解答网页：

* 纯文本常见问题解答页
* 带链接的常见问题解答页
* 主题主页的常见问题页面

### <a name="plain-faq-pages"></a>纯文本常见问题解答页

这是最常见的常见问题解答页类型，其中答案会紧跟在同一页中的问题后面。

下面是普通常见问题解答页的示例：

![知识库的纯文本常见问题解答页示例](./media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>带链接的常见问题解答页

在这种类型的常见问题解答页中，问题聚合在一起，并链接到同一页的不同部分或不同页中的答案。

下面的示例是一个常见问题解答页，其中的链接位于同一页上的不同部分：

 ![知识库的部分链接常见问题解答页示例](./media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="parent-topics-page-links-to-child-answers-pages"></a>父主题页面链接到子应答页面

这种类型的 FAQ 有一个主题页面，其中每个主题都链接到不同页面上相应的一组问题和答案。 QnA Maker 对所有链接页面进行爬网，以 & 答案来提取相应的问题。

下面是主题页面的示例，其中包含指向不同页面中的 FAQ 部分的链接。

 ![知识库的深层链接常见问题解答页示例](./media/qnamaker-concepts-datasources/topics-faq.png)

### <a name="support-urls"></a>支持 URL

QnA Maker 可以处理半结构化支持网页，例如，介绍如何执行给定任务、如何诊断和解决给定问题以及适用于给定流程的最佳做法的网文。 提取最适用于结构清晰且具有分层标题的文档。

> [!NOTE]
> 提取支持文章是一项新功能，并且处于早期阶段。 它最适用于结构良好且未包含复杂页眉/页脚的简单页面。

![QnA Maker 支持从在分层标题中提供了清晰结构的半结构化网页进行提取](./media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)

## <a name="import-and-export-knowledge-base"></a>导入和导出知识库

通过从 QnA Maker 门户的 "**设置**" 页中导入文件，只能使用从导出的知识库中导入的 **TSV 文件和 XLS 文件**。 在知识库创建期间或从 "**设置**" 页上的 " **+ 添加文件**" 或 " **+ 添加 URL** " 功能中，不能将它们用作数据源。 

通过这些 **TSV 和 XLS 文件** 导入知识库时，QnA 对将添加到 "编辑源"，而不是添加到从导出的知识库中提取 qna 的源。 


## <a name="next-steps"></a>后续步骤

查看[内容类型和示例](./concepts/data-sources-and-content.md#content-types-of-documents-you-can-add-to-a-knowledge-base)的完整列表
