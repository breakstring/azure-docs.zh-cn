---
title: 使用 Azure 媒体服务流式传输视频文件 - Node.js
description: 按照本教程的步骤，创建新的 Azure 媒体服务帐户、编码文件并将文件流式传输到 Azure Media Player。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
keywords: Azure 媒体服务, 流式传输
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.custom: mvc, devx-track-js
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 711db5135345fd9b69da9aaea5bc4dbb929d27c9
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895130"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---nodejs"></a>教程：基于 URL 对远程文件进行编码并流式传输视频 - Node.js

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

本教程展示了使用 Azure 媒体服务在各种浏览器和设备上对视频进行编码和流式处理有多轻松。 可以使用 HTTPS、URL、SAS URL 或位于 Azure Blob 存储中的文件路径来指定输入内容。

本文中的示例对可通过 HTTPS URL 访问的内容进行编码。 请注意，目前，AMS v3 不支持基于 HTTPS URL 的块传输编码。

完成本教程后即可对视频进行流式处理。  

![播放视频](./media/stream-files-nodejs-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

- 安装 [Node.js](https://nodejs.org/en/download/)
- [创建媒体服务帐户](./create-account-howto.md)。<br/>请务必记住用于资源组名称和媒体服务帐户名称的值。
- 遵循[使用 Azure CLI 访问 Azure 媒体服务 API](./access-api-howto.md) 中的步骤并保存凭据。 需要使用这些凭据来访问 API。

## <a name="download-and-configure-the-sample"></a>下载并配置示例

使用以下命令将包含流式处理 Node.js 示例的 GitHub 存储库克隆到计算机：  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

该示例位于 [StreamFilesSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/master/AMSv3Samples/StreamFilesSample) 文件夹。

在下载的项目中打开 [index.js](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L25)。 将 `endpoint config` 值替换为从[访问 API](./access-api-howto.md) 获得的凭据。

该示例执行以下操作：

1. 创建一个 **转换**（首先，检查指定的转换是否存在）。 
2. 创建一个输出 **资产**，用作编码 **作业** 的输出。
3. 创建基于 HTTPS URL 的 **作业** 输入。
4. 使用先前创建的输入和输出提交编码 **作业**。
5. 检查作业的状态。
6. 创建 **流定位符**。
7. 生成流式处理 URL。

## <a name="run-the-sample-app"></a>运行示例应用

1. 该应用下载编码文件。 创建想要输出文件位于的文件夹，并更新 [index.js](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L39) 文件中 **outputFolder** 变量的值。
1. 打开 **命令提示符**，浏览到示例的目录，然后执行以下命令。

    ```
    npm install 
    node index.js
    ```

运行完成后，应该会看到与此类似的输出：

![命令窗口的屏幕截图，其中包含 StreamFileSample 示例应用的输出（显示下载到本地目录的三个文件的 URL）。](./media/stream-files-nodejs-quickstart/run.png)

## <a name="test-with-azure-media-player"></a>使用 Azure Media Player 进行测试

本文使用 Azure Media Player 测试流式传输。 

> [!NOTE]
> 如果播放器在 Https 站点上进行托管，请确保将 URL 更新为“https”。

1. 打开 Web 浏览器并导航到 [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/)。
2. 在“URL:”框中，粘贴运行应用程序时获取的某个流式处理 URL 值  。 
 
     可以粘贴 HLS、Dash 或 Smooth 格式的 URL，Azure Media Player将切换到适当的流协议，以便在你的设备上自动播放。
3. 按“更新播放器”  。

Azure Media Player 可用于测试，但不可在生产环境中使用。 

## <a name="clean-up-resources"></a>清理资源

如果不再需要你的资源组中的任何一个资源（包括为本教程创建的媒体服务和存储帐户），请删除该资源组。

执行以下 CLI 命令：

```azurecli
az group delete --name amsResourceGroup
```

## <a name="see-also"></a>另请参阅

[作业错误代码](/rest/api/media/jobs/get#joberrorcode)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [媒体服务概念](concepts-overview.md)
