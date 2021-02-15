---
title: 使用 Azure Media Indexer 为媒体文件编制索引
description: 使用 Azure Media Indexer，可以使媒体文件内容可供搜索，并为隐藏的字幕和关键字生成全文本脚本。 本主题说明如何使用 Media Indexer。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 827a56b2-58a5-4044-8d5c-3e5356488271
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/21/2020
ms.author: inhenkel
ms.reviewer: johndeu
ms.custom: devx-track-csharp
ms.openlocfilehash: 12a4099a6a3f0d06dfbb40171970c7386c3e4d22
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518834"
---
# <a name="indexing-media-files-with-azure-media-indexer"></a>使用 Azure Media Indexer 为媒体文件编制索引

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!IMPORTANT]
> 建议客户从索引器 v1 和索引器 v2 迁移到使用 [媒体服务 V3 AudioAnalyzerPreset Basic 模式](../latest/analyzing-video-audio-files-concept.md)。 [Azure Media Indexer](media-services-index-content.md)媒体处理器和[Azure Media Indexer 2 预览版](./legacy-components.md)媒体处理器即将停用。 有关停用日期，请参阅此[旧组件](legacy-components.md)主题。

使用 Azure Media Indexer，可以使媒体文件内容可供搜索，并为隐藏的字幕和关键字生成全文本脚本。 可以只处理一个媒体文件，也可以一次处理多个媒体文件。  

在编制内容的索引时，请确保使用语音清晰的媒体文件（没有背景音乐、噪音、特效音或麦克风电流嘶嘶声）。 适当内容的某些示例包括：录制的会议、讲座或演示内容。 以下内容可能不适合用于编制索引：电影、电视剧、混合了音频和声音特效的任何内容、带有背景噪音（电流嘶嘶声）的不当录制内容。

索引作业可生成以下输出：

* 采用以下格式的隐藏式字幕文件： **TTML**和 **WebVTT**。
  
    隐藏字幕文件包含名为 Recognizability 的标记，该标记可以根据源视频中的语音辨别度对索引作业评分。  用户可以使用 Recognizability 的值筛选可用的输出文件。 如果分数较低，则表示索引结果由于音频质量问题而不佳。
* 关键字文件 (XML)。

本文介绍如何创建索引作业，以便**为资产编制索引**以及**为多个文件编制索引**。

## <a name="using-configuration-and-manifest-files-for-indexing-tasks"></a>使用配置和清单文件执行索引编制任务
可以使用任务配置为索引编制任务指定更多详细信息。 例如，可以指定用于媒体文件的元数据。 此元数据可供语言引擎用于扩充其词汇，并大幅提高语音识别的准确性。  还可以指定所需的输出文件。

还可以使用清单文件一次处理多个媒体文件。

有关详细信息，请参阅 [Azure 媒体索引器的任务预设](./legacy-components.md)。

## <a name="index-an-asset"></a>为资产编制索引
以下方法将媒体文件上传为资产，并创建为资产编制索引的作业。

如果未指定配置文件，则将使用所有默认设置为媒体文件编制索引。

```csharp
    static bool RunIndexingJob(string inputMediaFilePath, string outputFolder, string configurationFile = "")
    {
        // Create an asset and upload the input media file to storage.
        IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
            "My Indexing Input Asset",
            AssetCreationOptions.None);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration from file if specified.
        string configuration = string.IsNullOrEmpty(configurationFile) ? "" : File.ReadAllText(configurationFile);

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);

        // Use the following event handler to check job progress.  
        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

        // Launch the job.
        job.Submit();

        // Check job execution and wait for job to finish.
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling
        // method for job progress should log errors.  Here we check
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
    }

    static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
    {
        IAsset asset = _context.Assets.Create(assetName, options);

        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
        assetFile.Upload(filePath);

        return asset;
    }

    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors
        .Where(p => p.Name == mediaProcessorName)
        .ToList()
        .OrderBy(p => new Version(p.Version))
        .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }  
```

<!-- __ -->
### <a name="output-files"></a><a id="output_files"></a>输出文件
默认情况下，索引作业生成以下输出文件。 这些文件将存储在第一个输出资产中。

如果有多个输入媒体文件，索引器会为名为“JobResult.txt”的作业输出生成清单文件。 对于每个输入媒体文件，生成的 TTML、WebVTT 和关键字文件将依序编号，并使用“"Alias.”来命名

| 文件名 | 说明 |
| --- | --- |
| **InputFileName.ttml**<br/>**InputFileName.vtt** |采用 TTML 和 WebVTT 格式的隐藏式字幕 (CC) 文件。<br/><br/>这些文件可用于使听力障碍用户能够访问音频和视频文件。<br/><br/>隐藏式字幕文件包含名为 <b>Recognizability</b> 的标记，该标记可以根据源视频中的语音辨别度对索引作业评分。  可以使用 <b>Recognizability</b> 的值筛选可用的输出文件。 如果分数较低，则表示索引结果由于音频质量问题而不佳。 |
| **InputFileName.kw.xml<br/>InputFileName.info** |关键字和信息文件。 <br/><br/>关键字文件是 XML 文件，其中包含从语音内容中提取的关键字，以及频率和偏移量信息。 <br/><br/>信息文件是一种纯文本文件，其中包含有关每个已识别术语的详细信息。 第一行很特别，包含 Recognizability 评分。 后续每一行是使用制表符分隔的以下数据的列表：开始时间、结束时间、单词/短语、置信度。 时间以秒为单位，置信度为数字 0-1。 <br/><br/>示例行：“1.20    1.45    word    0.67” <br/><br/>这些文件可用于各种目的，如执行语音分析，公开给必应、Google 或 Microsoft SharePoint 等搜索引擎以使媒体文件更容易被发现，甚至用于传送更具相关性的广告。 |
| **JobResult.txt** |输出清单（仅在为多个文件编制索引时存在），包含以下信息：<br/><br/><table border="1"><tr><th>InputFile</th><th>Alias</th><th>MediaLength</th><th>错误</th></tr><tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr><tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3000</td></tr><tr><td>c.mp4</td><td>Media_3</td><td>600</td><td>0</td></tr></table><br/> |

如果没有为所有输入媒体文件成功编制索引，索引编制作业会失败，错误代码为 4000。 有关详细信息，请参阅[错误代码](#error_codes)。

## <a name="index-multiple-files"></a>为多个文件编制索引
以下方法将多个媒体文件上传为资产，并创建一次性为所有这些文件编制索引的作业。

创建具有“.lst”扩展名的清单文件，并将其上传到资产。 该清单文件包含所有资产文件的列表。 有关详细信息，请参阅 [Azure 媒体索引器的任务预设](./legacy-components.md)。

```csharp
    static bool RunBatchIndexingJob(string[] inputMediaFiles, string outputFolder)
    {
        // Create an asset and upload to storage.
        IAsset asset = CreateAssetAndUploadMultipleFiles(inputMediaFiles,
            "My Indexing Input Asset - Batch Mode",
            AssetCreationOptions.None);

        // Create a manifest file that contains all the asset file names and upload to storage.
        string manifestFile = "input.lst";            
        File.WriteAllLines(manifestFile, asset.AssetFiles.Select(f => f.Name).ToArray());
        var assetFile = asset.AssetFiles.Create(Path.GetFileName(manifestFile));
        assetFile.Upload(manifestFile);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job - Batch Mode");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration.
        string configuration = File.ReadAllText("batch.config");

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task - Batch Mode",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset - Batch Mode", AssetCreationOptions.None);

        // Use the following event handler to check job progress.  
        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

        // Launch the job.
        job.Submit();

        // Check job execution and wait for job to finish.
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling
        // method for job progress should log errors.  Here we check
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
    }

    private static IAsset CreateAssetAndUploadMultipleFiles(string[] filePaths, string assetName, AssetCreationOptions options)
    {
        IAsset asset = _context.Assets.Create(assetName, options);

        foreach (string filePath in filePaths)
        {
            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
            assetFile.Upload(filePath);
        }

        return asset;
    }
```

### <a name="partially-succeeded-job"></a>部分成功的作业
如果没有为所有输入媒体文件成功编制索引，索引编制作业会失败，错误代码为 4000。 有关详细信息，请参阅[错误代码](#error_codes)。

生成相同的输出（与成功的作业一样）。 可以参考输出清单文件，以根据“错误”列的值查明哪些输入文件失败。 对于失败的输入文件，不会生成相应的 TTML、WebVTT 和关键字文件。

### <a name="task-preset-for-azure-media-indexer"></a><a id="preset"></a> Azure Media Indexer 的任务预设
可以通过连同任务一起提供可选任务预设，来自定义 Azure Media Indexer 的处理操作。  下面描述了此配置 xml 文件的格式。

| 名称 | 必需 | 说明 |
| --- | --- | --- |
| **input** |false |要编制索引的资产文件。</p><p>Azure Media Indexer 支持以下格式的媒体文件：MP4、WMV、MP3、M4A、WMA、AAC、WAV。</p><p>可以在 **input** 元素的“名称”**** 或“列表”**** 属性中指定文件名（如下所示）。如果不指定要编制索引的文件，则选择主文件。 如果未设置主资产文件，则为输入资产中的第一个文件编制索引。</p><p>若要显式指定资产文件名，请执行：<br/>`<input name="TestFile.wmv">`<br/><br/>还可以一次为多个资产文件编制索引（最多 10 个文件）。 为此，请按以下步骤操作：<br/><br/><ol class="ordered"><li><p>创建一个文本文件（清单文件），并为其指定扩展名 .lst。 </p></li><li><p>将输入资产中所有资产文件名的列表添加到此清单文件。 </p></li><li><p>将该清单文件添加（上传）到资产。  </p></li><li><p>在输入的列表属性中指定清单文件的名称。<br/>`<input list="input.lst">`</li></ol><br/><br/>请注意：如果在清单文件中添加了 10 个以上的文件，则索引作业会失败并显示 2006 错误代码。 |
| **metadata** |false |用于词汇自适应的指定资产文件的元数据。  在准备索引器以使其能够识别非标准词汇（例如专有名词）时，该元素非常有用。<br/>`<metadata key="..." value="..."/>` <br/><br/>可以提供预定义**键**的**值**。 当前支持以下键：<br/><br/>“title”和“description”- 用于词汇适应，以微调作业的语言模型及改进语音辨识准确度。  值将植入 Internet 搜索以查找上下文相关的文本文档，并在执行索引任务期间使用内容来扩大内部字典。<br/>`<metadata key="title" value="[Title of the media file]" />`<br/>`<metadata key="description" value="[Description of the media file] />"` |
| **功能** <br/><br/> 在版本 1.2 中添加。 目前，唯一支持的功能是语音识别（“ASR”）。 |false |语音识别功能具有以下设置键：<table><tr><th><p>密钥</p></th>        <th><p>描述</p></th><th><p>示例值</p></th></tr><tr><td><p>语言</p></td><td><p>要在多媒体文件中识别的自然语言。</p></td><td><p>英语、西班牙语</p></td></tr><tr><td><p>CaptionFormats</p></td><td><p>以分号分隔的所需输出字幕格式的列表（如果有）</p></td><td><p>ttml;webvtt</p></td></tr><tr><td><p></p></td><td><p> </p></td><td><p>True; False</p></td></tr><tr><td><p>GenerateKeywords</p></td><td><p>布尔标志，指定是否需要关键字 XML 文件。</p></td><td><p>True；False。 </p></td></tr><tr><td><p>ForceFullCaption</p></td><td><p>布尔型标志，指定是否强制完整字幕（不考虑可信度）。  </p><p>默认值为 false，在此情况下，将忽略最终字幕输出中可信度小于 50% 的单词和短语并将其替换为省略号（“...”）。  省略号可用于字幕质量控制和审核。</p></td><td><p>True；False。 </p></td></tr></table> |

### <a name="error-codes"></a><a id="error_codes"></a>错误代码
如果发生错误，Azure Media Indexer 应返回以下错误代码之一：

| 代码 | 名称 | 可能的原因 |
| --- | --- | --- |
| 2000 |配置无效 |配置无效 |
| 2001 |输入资产无效 |输入资产缺失或资产为空。 |
| 2002 |清单无效 |清单为空或清单包含无效项目。 |
| 2003 |无法下载媒体文件 |清单文件中的 URL 无效。 |
| 2004 |协议不受支持 |不支持媒体 URL 的协议。 |
| 2005 |文件类型不受支持 |不支持输入媒体文件类型。 |
| 2006 |输入文件太多 |输入清单中的文件超过 10 个。 |
| 3000 |无法解码媒体文件 |不受支持的媒体编解码器 <br/>或<br/> 受损的媒体文件 <br/>或<br/> 输入媒体中没有音频流。 |
| 4000 |分批编制索引部分成功 |一些输入媒体文件无法编制索引。 有关详细信息，请参阅<a href="#output_files">输出文件</a>。 |
| 其他 |内部错误 |请联系支持团队。 indexer@microsoft.com |

## <a name="supported-languages"></a><a id="supported_languages"></a>支持的语言
当前支持英语和西班牙语。  

## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>相关链接
[Azure 媒体服务分析概述](./legacy-components.md)

[使用 Azure 媒体索引器 2 预览版为媒体文件编制索引](./legacy-components.md)