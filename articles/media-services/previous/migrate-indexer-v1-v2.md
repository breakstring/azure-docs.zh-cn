---
title: 从索引器 v1 和 v2 迁移到 Azure 媒体服务视频索引器 |Microsoft Docs
description: 本主题讨论如何从 Azure Media Indexer v1 和 v2 迁移到 Azure 媒体服务视频索引器。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2020
ms.author: inhenkel
ms.openlocfilehash: 330bffebb870635fd473e88a8eadb300eed40b9b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012129"
---
# <a name="migrate-from-media-indexer-and-media-indexer-2-to-video-indexer"></a>从媒体索引器和媒体索引器2迁移到视频索引器

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!IMPORTANT]
> 建议客户从索引器 v1 和索引器 v2 迁移到使用 [媒体服务 V3 AudioAnalyzerPreset Basic 模式](../latest/analyzing-video-audio-files-concept.md)。 [Azure Media Indexer](media-services-index-content.md)媒体处理器和[Azure Media Indexer 2 预览版](./legacy-components.md)媒体处理器即将停用。 有关停用日期，请参阅此[旧组件](legacy-components.md)主题。

Azure 媒体服务视频索引器基于 Azure 媒体分析、Azure 认知搜索、认知服务 (，例如人脸 API、Microsoft Translator、计算机视觉 API 和自定义语音服务) 。 有了视频索引器，就可以使用视频索引器视频和音频模型从视频中提取见解。 若要查看视频索引器可用于哪些方案、它所提供的功能以及如何开始使用，请参阅 [视频索引器视频和音频型号](../video-indexer/video-indexer-overview.md)。 

可以通过使用 [Azure 媒体服务 v3 分析器预设](../latest/analyzing-video-audio-files-concept.md) 或直接使用 [视频索引器 api](https://api-portal.videoindexer.ai/)从视频和音频文件中提取见解。 目前，视频索引器 Api 提供的功能与媒体服务 v3 Api 提供的功能重叠。

> [!NOTE]
> 若要了解视频索引器与媒体服务分析器预设值之间的差异，请查看 [比较文档](../video-indexer/compare-video-indexer-with-media-services-presets.md)。

本文介绍了从 Azure Media Indexer 和 Azure Media Indexer 2 迁移到 Azure 媒体服务视频索引器的步骤。  

## <a name="migration-options"></a>迁移选项

|如果需要  |然后在受影响的域控制器上，运行 |
|---|---|
|一种为隐藏式字幕文件格式的媒体文件格式提供语音到文本脚本的解决方案： VTT、SRT 或 TTML<br/>以及其他音频见解，如关键字、主题推断、声音事件、发言人 diarization、实体提取和转换| 更新应用程序，以通过视频索引器 v2 REST API 或 Azure 媒体服务 v3 音频分析器预设使用 Azure 视频索引器功能。|
|语音到文本功能| 直接使用认知服务语音 API。|  

## <a name="getting-started-with-video-indexer"></a>视频索引器入门

以下部分指向相关链接： [如何开始视频索引器？](../video-indexer/video-indexer-overview.md#how-can-i-get-started-with-video-indexer) 

## <a name="getting-started-with-media-services-v3-apis"></a>媒体服务 v3 Api 入门

通过 azure 媒体服务 v3 API，你可以通过 [Azure 媒体服务 v3 分析器预设](../latest/analyzing-video-audio-files-concept.md)从视频和音频文件中提取见解。

凭借 AudioAnalyzerPreset 能够从音频或视频文件中提取多个音频见解。 输出包括音频稿本的 VTT 或 TTML 文件，以及 (包含所有附加音频见解) 的 JSON 文件。 音频见解包括关键字、扬声器索引和语音情绪分析。 AudioAnalyzerPreset 还支持特定语言的语言检测。 有关详细信息，请参阅 [转换](/rest/api/media/transforms/createorupdate#audioanalyzerpreset)。

### <a name="get-started"></a>入门

若要开始操作，请参阅：

* [教程](../latest/analyze-videos-tutorial-with-api.md)
* AudioAnalyzerPreset 示例： [JAVA sdk](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer) 或 [.net sdk](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer)
* VideoAnalyzerPreset 示例： [JAVA sdk](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer) 或 [.net sdk](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/VideoAnalytics/VideoAnalyzer)

## <a name="getting-started-with-cognitive-services-speech-services"></a>认知服务语音服务入门

[Azure 认知服务](../../cognitive-services/index.yml) 提供了一种语音到文本服务，可让你的应用程序、工具或设备可以使用或显示实时实时传输到文本的转录。 您可以使用语音到文本 [自定义自己的声音模型、语言模型或发音模型](../../cognitive-services/speech-service/how-to-custom-speech-train-model.md)。 有关详细信息，请参阅 [认知服务语音到文本](../../cognitive-services/speech-service/speech-to-text.md)。 

> [!NOTE] 
> 语音到文本服务不采用视频文件格式，只采用 [某些音频格式](../../cognitive-services/speech-service/rest-speech-to-text.md#audio-formats)。 

有关文本到语音转换服务以及如何入门的详细信息，请参阅 [什么是语音到文本？](../../cognitive-services/speech-service/speech-to-text.md)

## <a name="known-differences-from-deprecated-services"></a>与弃用的服务的已知差异

你会发现，视频索引器、Azure 媒体服务 v3 AudioAnalyzerPreset 和认知服务语音服务服务的可靠性更高，并产生比已淘汰 Azure Media Indexer 1 和 Azure Media Indexer 2 处理器更好的输出质量。  

一些已知的区别包括：

* 认知服务语音服务不支持关键字提取。 不过，视频索引器和媒体服务 v3 AudioAnalyzerPreset 都提供一组更可靠的一组 JSON 文件格式的关键字。

## <a name="support"></a>支持

可以通过导航到[新建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)来开具支持票证

## <a name="next-steps"></a>后续步骤

* [旧组件](legacy-components.md)
* [定价页面](https://azure.microsoft.com/pricing/details/media-services/#encoding)