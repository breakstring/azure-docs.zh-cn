---
title: 视频索引器的动画字符检测如何
titleSuffix: Azure Media Services
description: 这说明了如何通过视频索引器使用动画字符检测。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.custom: references_regions
ms.topic: how-to
ms.date: 12/07/2020
ms.author: juliako
ms.openlocfilehash: 29dbdb896d1a6063fec277afa33327c84cb2f0cd
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880921"
---
# <a name="use-the-animated-character-detection-preview-with-portal-and-api"></a>使用门户和 API (预览) 使用动画字符检测 

Azure 媒体服务视频索引器支持对动画内容中的字符进行检测、分组和识别，此功能可通过 Azure 门户和 API 获得。 查看 [此概述](animated-characters-recognition.md) 主题。

本文演示如何通过 Azure 门户和视频索引器 API 使用动画字符检测。

## <a name="use-the-animated-character-detection-with-portal"></a>使用门户中的动画字符检测 

在试用帐户中，通过视频索引器管理自定义视觉集成，你可以开始创建和使用 "动画字符" 模型。 如果使用试用帐户，则可以跳过以下 ( "连接自定义视觉帐户" ) 部分。

### <a name="connect-your-custom-vision-account-paid-accounts-only"></a>仅 (付费帐户连接自定义视觉帐户) 

如果你拥有视频索引器付费帐户，则需要先连接自定义视觉帐户。 如果还没有自定义视觉帐户，请创建一个。 有关详细信息，请参阅 [自定义视觉](../../cognitive-services/custom-vision-service/overview.md)。

> [!NOTE]
> 这两个帐户必须位于同一区域。 日本地区目前不支持自定义视觉集成。

有权访问其自定义视觉帐户的付费帐户可在此处查看模型和标记图像。 详细了解如何 [在自定义视觉中改进分类器](../../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md)。 

请注意，只应通过视频索引器而不是通过自定义视觉网站来完成模型的定型。 

#### <a name="connect-a-custom-vision-account-with-api"></a>使用 API 连接自定义视觉帐户 

按照以下步骤将自定义视觉帐户连接到视频索引器，或更改当前连接到视频索引器的自定义视觉帐户：

1. 浏览到 [www.customvision.ai](https://www.customvision.ai) 并登录。
1. 复制定型资源和预测资源的密钥：

    > [!NOTE]
    > 若要提供所有键，你需要在自定义视觉中使用两个单独的资源，一个用于定型，一个用于预测。
1. 提供其他信息：

    * 终结点 
    * 预测资源 ID
1. 浏览并登录到 [视频索引器](https://vi.microsoft.com/)。
1. 单击页面右上角的问号，并选择 " **API 引用**"。
1. 请确保已通过单击 " **产品** " 选项卡订阅了 API 管理。如果已连接 API，则可继续执行下一步，否则请订阅。 
1. 在开发人员门户中，单击 " **完整 API 参考** " 并浏览到 " **操作**"。  
1. 选择 " **连接自定义视觉帐户" (预览 ")** 并单击" **试用**"。
1. 填写必填字段以及访问令牌，然后单击 " **发送**"。 

    有关如何获取视频索引器访问令牌的详细信息，请转到 [开发人员门户](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Account-Access-Token?)，查看 [相关文档](video-indexer-use-apis.md#obtain-access-token-using-the-authorization-api)。  
1. 调用返回 200 OK 响应后，你的帐户已连接。
1. 若要验证连接，请浏览到 [视频索引器](https://vi.microsoft.com/)) 门户：
1. 单击右上角的 " **内容模型自定义** " 按钮。
1. 中转到 " **动画字符** " 选项卡。
1. 单击自定义视觉中的 "管理模型" 后，将传输到刚刚连接的自定义视觉帐户。

> [!NOTE]
> 目前仅支持通过视频索引器创建的模型。 通过自定义视觉创建的模型将不可用。 此外，最佳做法是仅通过视频索引器平台编辑通过视频索引器创建的模型，因为通过自定义视觉所做的更改可能会导致意外的结果。

### <a name="create-an-animated-characters-model"></a>创建动画字符模型

1. 浏览到[视频索引器](https://vi.microsoft.com/)网站并登录。
1. 若要在帐户中自定义模型，请在页面左侧选择 " **内容模型自定义** " 按钮。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/content-model-customization/content-model-customization.png" alt-text="自定义视频索引器中的内容模型":::
1. 中转到 "模型自定义" 部分中的 " **动画字符** " 选项卡。
1. 单击 " **添加模型**"。
1. 为模型命名并单击 enter 以保存名称。

> [!NOTE]
> 最佳做法是为每个动画序列提供一个自定义远景模型。 

### <a name="index-a-video-with-an-animated-model"></a>使用动画模型为视频编制索引

对于初始培训，请至少上传两个视频。 每个都应比预期更好的识别模式。 如果你的剧集较短，则建议在训练之前上传至少30分钟的视频内容。 这将允许你合并属于不同场景和背景的相同字符的组，因此会增加在你编制索引的以下剧集中检测字符的机会。 若要在多个视频 (剧集上定型模型) 需要使用同一动画模型对它们进行索引。 

1. 单击 "上 **传** " 按钮。
1. 选择要从文件或 URL)  (上传的视频。
1. 单击 " **高级选项**"。
1. 在 " **人物/动画字符** " 下，选择 **动画模型**。
1. 如果有一个模型，则会自动选择该模型，如果有多个模型，则可以从下拉菜单中选择相关的模型。
1. 单击 "上传"。
1. 为视频编制索引后，会在 "**见解**" 窗格的 "**动画字符**" 部分看到检测到的字符。

在对模型进行标记和定型之前，所有的动画字符都将命名为 "Unknown #X"。 训练模型后，还会识别它们。

### <a name="customize-the-animated-characters-models"></a>自定义动画字符模型

1. 在视频索引器中命名字符。

    1. 创建模型后，建议在自定义视觉中查看这些组。 
    1. 若要对视频中的动画字符进行标记，请参阅 " **见解**"   选项卡，并单击窗口右上角的 " **编辑**"   按钮。 
    1. 在 " **见解**"   窗格中，单击任何检测到的动画字符，并将其名称从 "Unknown #X" 更改为临时名称 (或之前分配给该字符) 的名称。 
    1. 键入新名称后，单击新名称旁边的复选图标。 这会将新名称保存在视频索引器的模型中。 
1. 仅付费帐户：在自定义视觉中查看组 

    > [!NOTE]
    > 有权访问其自定义视觉帐户的付费帐户可在此处查看模型和标记图像。 详细了解如何 [在自定义视觉中改进分类器](../../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md)。 请务必注意，只应通过视频索引器来完成模型的定型 (如此 topid) 中所述，而不是通过自定义视觉网站进行。 

    1. 在视频索引器中转到 " **自定义模型** " 页，然后选择 " **动画字符** " 选项卡。 
    1. 单击正在处理的模型的 "编辑" 按钮，在自定义视觉中对其进行管理。 
    1. 检查每个字符组： 

        * 如果组中包含不相关的映像，则建议在自定义视觉网站中将其删除。 
        * 如果有属于不同字符的图像，请单击图像，再添加相应的标记，然后删除错误的标记，从而更改这些特定图像上的标记。 
        * 如果组不正确（也就是说，它包含多个字符中的非字符图像或图像），可以在自定义视觉网站或视频索引器见解中删除。 
        * 分组算法有时会将字符拆分为不同的组。 因此，建议在视频索引器 Insights) 中为属于同一字符的所有组指定相同的名称 (，这会立即导致所有这些组在自定义视觉网站中显示为 on。 
    1. 优化组后，请确保用标记的初始名称反映组中的字符。 
1. 定型模型 

    1. 完成编辑所需的所有名称后，需要为模型定型。 
    1. 一旦将字符定型到模型中后，就会将其识别为使用该模型建立索引的下一个视频。 
    1. 打开 "自定义" 页，单击 " **动画字符**   " 选项卡，然后单击 " **训练**" 按钮训练模型。 为了保持视频之间的连接 
    
索引器和模型，请不要在自定义视觉网站中训练模型 (付费帐户是否有权访问自定义视觉网站) ，只需在视频索引器中访问。 训练后，任何将通过该模型建立索引或重新编制索引的视频都将识别定型字符。 

## <a name="delete-an-animated-character-and-the-model"></a>删除动画字符和模型

1. 删除动画字符。

    1. 若要删除视频见解中的动画字符，请在 " **见解** " 选项卡上，单击窗口右上角的 " **编辑** " 按钮。
    1. 选择动画字符，然后单击其名称下面的 " **删除** " 按钮。

    > [!NOTE]
    > 这将删除此视频的见解，但不会影响模型。
1. 删除模型。

    1. 单击顶部菜单上的 " **内容模型自定义** " 按钮，然后选择 " **动画字符** " 选项卡。
    1. 单击要删除的模型右侧的省略号图标，然后在 "删除" 按钮上单击。
    
    * 付费帐户：模型将与视频索引器断开连接，并且你将无法重新连接它。
    * 试用帐户：模型也将从海关远景中删除。 
    
        > [!NOTE]
        > 在试用帐户中，只有一个可以使用的模型。 删除后，将无法对其他模型定型。

## <a name="use-the-animated-character-detection-with-api"></a>使用 API 的动画字符检测 

1. 连接自定义视觉帐户。

    如果你拥有视频索引器付费帐户，则需要先连接自定义视觉帐户。 <br/>
    如果还没有自定义视觉帐户，请创建一个。 有关详细信息，请参阅 [自定义视觉](../../cognitive-services/custom-vision-service/overview.md)。

    [使用 API 连接自定义视觉帐户](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Connect-Custom-Vision-Account?tags=&pattern=&groupBy=tag)。
1. 创建 "动画字符" 模型。

    使用 [创建动画模型](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Animation-Model?&groupBy=tag) API。
1. 为视频编制索引或重新编制索引。

    使用 [重新索引](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) API。 
1. 自定义动画字符模型。

    使用 [训练动画模型](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Train-Animation-Model?&groupBy=tag) API。

### <a name="view-the-output"></a>查看输出

查看生成的 JSON 文件中的动画字符。

```json
"animatedCharacters": [
    {
    "videoId": "e867214582",
    "confidence": 0,
    "thumbnailId": "00000000-0000-0000-0000-000000000000",
    "seenDuration": 201.5,
    "seenDurationRatio": 0.3175,
    "isKnownCharacter": true,
    "id": 4,
    "name": "Bunny",
    "appearances": [
        {
            "startTime": "0:00:52.333",
            "endTime": "0:02:02.6",
            "startSeconds": 52.3,
            "endSeconds": 122.6
        },
        {
            "startTime": "0:02:40.633",
            "endTime": "0:03:16.6",
            "startSeconds": 160.6,
            "endSeconds": 196.6
        },
    ]
    },
]
```

## <a name="limitations"></a>限制

* 目前 East-Asia 区域中不支持 "动画标识" 功能。
* 如果视频质量较差，可能无法正确识别在视频中显示较小或更多的字符。
* 建议使用每组动画字符集的模型， (例如每个动画系列) 。

## <a name="next-steps"></a>后续步骤

[视频索引器概述](video-indexer-overview.md)