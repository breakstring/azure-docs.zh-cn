---
title: 内容审查器 Python 客户端库快速入门
titleSuffix: Azure Cognitive Services
description: 本快速入门介绍如何开始使用适用于 Python 的 Azure 内容审查器客户端库。 在应用中内置内容筛选软件，以符合法规或维护用户的预期环境。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 09/15/2020
ms.custom: cog-serv-seo-aug-2020
ms.author: pafarley
ms.openlocfilehash: 1058b93a304dbf6f08d9a3e8f995e123a6dc6631
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947416"
---
适用于 Python 的 Azure 内容审查器客户端库入门。 请按照以下步骤安装 PiPy 包并试用基本任务的示例代码。 

内容审查器是一种 AI 服务，可用于处理可能的冒犯性、危险或不可取内容。 使用 AI 支持型内容审核服务扫描文本、图像和视频，并自动应用内容标志。 然后，将应用与审阅工具进行集成，为人工审阅者团队提供一个联机审查环境。 在应用中内置内容筛选软件，以符合法规或维护用户的预期环境。

使用适用于 Python 的内容审查器客户端库可以：

* 审查文本
* 使用自定义字词列表
* 审查图像
* 使用自定义图像列表
* 创建评审

[参考文档](/python/api/overview/azure/cognitiveservices/contentmoderator) | [库源代码](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-contentmoderator) | [包 (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-contentmoderator/) | [示例](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
* 拥有 Azure 订阅后，在 Azure 门户中<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title="创建内容审查器资源"  target="_blank">创建内容审查器资源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以获取密钥和终结点。 等待其部署并单击“转到资源”按钮。
    * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到内容审查器。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
    * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。


## <a name="setting-up"></a>设置

### <a name="install-the-client-library"></a>安装客户端库

安装 Python 后，可使用以下命令安装内容审查器客户端库：

```console
pip install --upgrade azure-cognitiveservices-vision-contentmoderator
```

### <a name="create-a-new-python-application"></a>创建新的 Python 应用程序

创建新的 Python 脚本，并在喜好的编辑器或 IDE 中打开它。 将以下 `import` 语句添加到该文件的顶部。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imports)]

> [!TIP]
> 想要立即查看整个快速入门代码文件？ 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ContentModerator/ContentModeratorQuickstart.py) 上找到它，其中包含此快速入门中的代码示例。

接下来，为资源的终结点位置和密钥创建变量。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_vars)]

> [!IMPORTANT]
> 转到 Azure 门户。 如果在“先决条件”部分中创建的内容审查器资源已成功部署，请单击“后续步骤”下的“转到资源”按钮  。 在资源的“密钥和终结点”页的“资源管理”下可以找到密钥和终结点 。 
>
> 完成后，请记住将密钥从代码中删除，并且永远不要公开发布该密钥。 对于生产环境，请考虑使用安全的方法来存储和访问凭据。 例如，[Azure 密钥保管库](../../../../key-vault/general/overview.md)。

## <a name="object-model"></a>对象模型

以下类将处理内容审查器 Python 客户端库的某些主要功能。

|名称|说明|
|---|---|
|[ContentModeratorClient](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient)|所有内容审查器功能都需要此类。 请使用你的订阅信息实例化此类，然后使用它来生成其他类的实例。|
|[ImageModerationOperations](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations)|此类提供用于分析成人内容、个人信息或人脸的功能。|
|[TextModerationOperations](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.textmoderationoperations)|此类提供用于在文本中分析语言、猥亵内容、错误和个人信息的功能。|
[ReviewsOperations](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations)|此类提供评审 API 的功能，包括用于创建作业、自定义工作流和人工评审的方法。|

## <a name="code-examples"></a>代码示例

这些代码片段演示如何使用适用于 Python 的内容审查器客户端库执行以下任务：

* [对客户端进行身份验证](#authenticate-the-client)
* [审查文本](#moderate-text)
* [使用自定义字词列表](#use-a-custom-terms-list)
* [审查图像](#moderate-images)
* [使用自定义图像列表](#use-a-custom-image-list)
* [创建评审](#create-a-review)

## <a name="authenticate-the-client"></a>验证客户端

使用终结点和密钥实例化某个客户端。 使用密钥创建 [CognitiveServicesCredentials](/python/api/msrest/msrest.authentication.cognitiveservicescredentials) 对象，然后在终结点上使用该对象创建 [ContentModeratorClient](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient) 对象。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_client)]

## <a name="moderate-text"></a>审查文本

以下代码使用内容审查器客户端分析文本的正文，并将结果输出到控制台。 首先，在项目的根目录中创建 **text_files/** 文件夹，并在其中添加 *content_moderator_text_moderation.txt* 文件。 将你自己的文本添加到此文件中，或使用以下示例文本：

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 2065550111
```

添加对新文件夹的引用。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

然后，将以下代码添加到 Python 脚本中。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textmod)]

## <a name="use-a-custom-terms-list"></a>使用自定义字词列表

以下代码演示如何管理用于审查文本的自定义字词列表。 可以使用 [ListManagementTermListsOperations](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementtermlistsoperations) 类创建字词列表、管理各个字词，并根据该列表筛选其他文本正文。

### <a name="get-sample-text"></a>获取示例文本

若要使用此示例，必须在项目的根目录中创建 **text_files/** 文件夹，并在其中添加 *content_moderator_term_list.txt* 文件。 此文件应包含要根据字词列表检查的有序文本。 可使用以下示例文本：

```
This text contains the terms "term1" and "term2".
```

添加对该文件夹的引用（如果尚未定义引用）。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

### <a name="create-a-list"></a>创建列表

将以下代码添加到 Python 脚本以创建自定义字词列表，并保存其 ID 值。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_create)]

### <a name="define-list-details"></a>定义列表详细信息

可以使用列表的 ID 来编辑其名称和说明。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_details)]

### <a name="add-a-term-to-the-list"></a>将字词添加到列表

以下代码将字词 `"term1"` 和 `"term2"` 添加到列表。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_add)]

### <a name="get-all-terms-in-the-list"></a>获取列表中的所有字词

可以使用列表 ID 返回列表中的所有字词。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_getterms)]

### <a name="refresh-the-list-index"></a>刷新列表索引

每当在列表中添加或删除字词后，都必须先刷新索引，然后才能使用更新的列表。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_refresh)]

### <a name="screen-text-against-the-list"></a>根据列表筛选文本

自定义字词列表的主要功能就是将文本正文与该列表进行比较，找出是否存在任何匹配的字词。 

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_screen)]

### <a name="remove-a-term-from-a-list"></a>从列表中删除字词

以下代码从列表中删除字词 `"term1"`。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_remove)]

### <a name="remove-all-terms-from-a-list"></a>从列表中删除所有字词

使用以下代码可清除列表中的所有字词。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_removeall)]

### <a name="delete-a-list"></a>删除列表

使用以下代码可删除自定义字词列表。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_deletelist)]

## <a name="moderate-images"></a>审查图像

以下代码使用内容审查器客户端以及 [ImageModerationOperations](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations) 对象在图像中分析成人内容和猥亵内容。

### <a name="get-sample-images"></a>获取示例图像

定义对要分析的某些图像的引用。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemodvars)]

然后添加以下代码来循环访问图像。 本部分的余下代码将进入此循环。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>检查成人/猥亵内容

以下代码在给定 URL 上的图像中检查成人内容或猥亵，然后将结果输出到控制台。 有关这些术语的含义，请参阅 [图像审查的概念](../../image-moderation-api.md) 指南。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_ar)]

### <a name="check-for-visible-text"></a>检查可见文本

以下代码在图像中检查可见的文本内容，并将结果输出到控制台。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>检查人脸

以下代码在图像中检查人脸，并将结果输出到控制台。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_face)]

## <a name="use-a-custom-image-list"></a>使用自定义图像列表

以下代码演示如何管理用于图像审查的自定义图像列表。 如果你的平台经常收到一组你要筛选出的相同图像的实例，则此功能很有用。维护这些特定图像的列表可以提高性能。 使用 [ListManagementImageListsOperations](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementimagelistsoperations) 类可以创建图像列表、管理该列表中的各个图像，并将这些图像与其他图像进行比较。

创建以下文本变量用于存储此方案中使用的图像 URL。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelistvars)]

> [!NOTE]
> 此列表本身并不适当，而是要添加到代码的 `add images` 节中的非正式图像列表。


### <a name="create-an-image-list"></a>创建图像列表

添加以下代码以创建图像列表，并保存对其 ID 的引用。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_create)]

### <a name="add-images-to-a-list"></a>将图像添加到列表。

以下代码将所有图像添加到列表。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_add)]

在脚本中的其他某个位置定义 **add_images** 帮助器函数。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_addhelper)]

### <a name="get-images-in-list"></a>获取列表中的图像

以下代码输出列表中所有图像的名称。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getimages)]

### <a name="update-list-details"></a>更新列表详细信息

可以使用列表 ID 来更新列表的名称和说明。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_updatedetails)]

### <a name="get-list-details"></a>获取列表详细信息

使用以下代码可输出列表的当前详细信息。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getdetails)]

### <a name="refresh-the-list-index"></a>刷新列表索引

添加或删除图像后，必须刷新列表索引才能使用它来筛选其他图像。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_refresh)]

### <a name="match-images-against-the-list"></a>将图像与列表进行匹配

图像列表的主要功能是比较新图像，确定是否存在任何匹配项。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_match)]

### <a name="remove-an-image-from-the-list"></a>从列表中删除图像

以下代码从列表中删除一个项。 在本例中，该项是与列表类别不匹配的某个图像。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_remove)]

### <a name="remove-all-images-from-a-list"></a>从列表中删除所有图像

使用以下代码可清除图像列表。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_removeall)]

### <a name="delete-the-image-list"></a>删除图像列表

使用以下代码可删除给定的图像列表。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_delete)]

## <a name="create-a-review"></a>创建评审

可使用内容审查器 Python 客户端库将内容馈送到[评审工具](https://contentmoderator.cognitive.microsoft.com)，使审查人员可以评审该内容。 若要详细了解评审工具，请参阅[评审工具概念指南](../../review-tool-user-guide/human-in-the-loop.md)。

以下代码使用 [ReviewsOperations](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations) 类创建评审、检索评审 ID，并在通过评审工具的 Web 门户收到人工输入后检查评审详细信息。

### <a name="get-review-credentials"></a>获取审阅凭据

首先登录到评审工具并检索团队名称。 然后将此名称分配到代码中的相应变量。 或者，可以设置一个回调终结点用于接收有关评审活动的更新。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_vars)]

### <a name="create-an-image-review"></a>创建图像评审

添加以下代码，以创建并发布给定图像 URL 的评审。 该代码将保存对审阅 ID 的引用。 
[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_create)]

### <a name="get-review-details"></a>获取评论详细信息

使用以下代码检查给定评审的详细信息。 创建评审后，可以自行转到评审工具并与内容交互。 有关如何执行此操作的信息，请参阅[评审操作方法指南](../../review-tool-user-guide/review-moderated-images.md)。 完成后，可以再次运行此代码，以检索评审过程的结果。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_getdetails)]

如果在此方案中使用了回调终结点，应会收到采用以下格式的事件：

```console
{'callback_endpoint': 'https://requestb.in/qmsakwqm',
 'content': '',
 'content_id': '3ebe16cb-31ed-4292-8b71-1dfe9b0e821f',
 'created_by': 'cspythonsdk',
 'metadata': [{'key': 'sc', 'value': 'True'}],
 'review_id': '201901i14682e2afe624fee95ebb248643139e7',
 'reviewer_result_tags': [{'key': 'a', 'value': 'True'},
                          {'key': 'r', 'value': 'True'}],
 'status': 'Complete',
 'sub_team': 'public',
 'type': 'Image'}
```

## <a name="run-the-application"></a>运行应用程序

在快速入门文件中使用 `python` 命令运行应用程序。

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [门户](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>后续步骤

本快速入门已介绍如何使用内容审查器 Python 库来执行审查任务。 接下来，请阅读概念指南来详细了解图像或其他媒体的审查。

> [!div class="nextstepaction"]
>[图像审查的概念](../../image-moderation-api.md)

* [什么是 Azure 内容审查器？](../../overview.md)
* 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ContentModerator/ContentModeratorQuickstart.py) 上找到此示例的源代码。
