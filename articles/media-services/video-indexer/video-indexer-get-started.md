---
title: 注册视频索引器并上传第一个视频 - Azure
titleSuffix: Azure Media Services
description: 了解如何使用视频索引器门户注册并上传第一个视频。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: quickstart
ms.date: 01/25/2021
ms.author: juliako
ms.openlocfilehash: 5b38c731db141052e6700472020cd60b6a4d13a5
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "98797807"
---
# <a name="quickstart-how-to-sign-up-and-upload-your-first-video"></a>快速入门：如何注册并上传第一个视频

本快速入门介绍如何登录到视频索引器网站，以及如何上传第一个视频。

创建视频索引器帐户时，可以选择免费试用帐户（提供特定分钟数的免费索引时间）或付费选项（不受配额的限制）。 使用免费试用版时，视频索引器为网站用户提供最多 600 分钟的免费索引，为 API 用户提供最多 2400 分钟的免费索引。 使用付费选项时，可以[创建连接到 Azure 订阅和 Azure 媒体服务帐户的视频索引器帐户](connect-to-azure.md)。 你需要为索引时间付费，有关详细信息，请参阅[媒体服务定价](https://azure.microsoft.com/pricing/details/media-services/)。 

## <a name="sign-up-for-video-indexer"></a>注册视频索引器

若要开始使用视频索引器进行开发，请浏览到[视频索引器](https://www.videoindexer.ai/)网站并注册。

开始使用视频索引器后，所有存储的数据和上传的内容都将使用 Microsoft 托管密钥进行静态加密。

> [!NOTE]
> 请查看[计划的视频索引器网站身份验证更改](release-notes.md#planned-video-indexer-website-authenticatication-changes)。

## <a name="upload-a-video-using-the-video-indexer-website"></a>使用视频索引器网站上传视频

### <a name="supported-file-formats-for-video-indexer"></a>视频索引器支持的文件格式

有关可用于视频索引器的文件格式列表，请参阅[输入容器/文件格式](../latest/media-encoder-standard-formats.md#input-containerfile-formats)一文。

### <a name="upload-a-video"></a>上传视频

1. 登录到[视频索引器](https://www.videoindexer.ai/)网站。
1. 若要上传视频，请按“上传”按钮或链接。 

    > [!NOTE]
    > 视频的名称长度不得超过 80 个字符。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/video-indexer-upload.png" alt-text="上载":::
1. 视频上传以后，视频索引器就会开始对视频进行索引编制和分析。 你会看到进度。 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/progress.png" alt-text="上传进度":::
1. 当视频索引器分析完成后，你会获得一封电子邮件，其中包含视频链接以及对视频中找到的内容的简短说明。 例如：人员、口语和书面语、主题和命名实体。
1. 稍后可以在库列表中找到视频并执行不同的操作。 例如：搜索、重新索引、编辑。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/uploaded.png" alt-text="已上传内容":::

## <a name="supported-browsers"></a>支持的浏览器

有关详细信息，请参阅[支持的浏览器](video-indexer-overview.md#supported-browsers)。

## <a name="see-also"></a>另请参阅

有关更多详细信息，请参阅[上传视频并为其编制索引](upload-index-videos.md)。

上传视频并为其编制索引后，可以开始使用[视频索引器网站](video-indexer-view-edit.md)或[视频索引器开发人员门户](video-indexer-use-apis.md)查看视频见解。 

[开始使用 API](video-indexer-use-apis.md)

## <a name="next-steps"></a>后续步骤

有关详细介绍，请访问我们的[简介实验室](https://github.com/Azure-Samples/media-services-video-indexer/blob/master/IntroToVideoIndexer.md)。 

在研讨会结束时，你将对可以从视频和音频内容中提取的信息类型有一个很好的了解，你将做好更充分的准备来识别与内容智能相关的机会、在 Azure 上推介视频 AI，并在视频索引器上演示几个场景。

