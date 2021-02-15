---
ms.openlocfilehash: 426c735dfd0d015cdc1a734edde9d336fb88cfbc
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2020
ms.locfileid: "97486729"
---
作为本快速入门的先决条件的一部分，请将示例代码下载到一个文件夹中。 按照以下步骤检查并编辑示例代码。

1. 在 Visual Studio Code 中，转到 src/edge。 你可看到 .env 文件以及一些部署模板文件。

    部署模板是指边缘设备的部署清单，其中使用变量指定某些属性。 该 .env 文件包含这些变量的值。
1. 转到 src/cloud-to-device-console-app 文件夹。 你可在此处看到 appsettings.json 文件和一些其他文件：
    * ***c2d-console-app.csproj** _ - Visual Studio Code 的项目文件。
    **operations.json** - 希望程序运行的操作的列表。
    _ ***Program.cs** _ - 示例程序代码。 此代码：

        _ 加载应用设置。
        * 调用 IoT Edge 模块上的实时视频分析公开的直接方法。 可以通过调用模块的[直接方法](../../../direct-methods.md)来使用该模块分析实时视频流。 
        * 暂停以检查“终端”窗口中程序的输出，并检查“输出”窗口中模块生成的事件 。
        * 调用直接方法以清理资源。

1. 编辑 operations.json 文件：
    * 将链接更改为图拓扑：

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-motion-files/2.0/topology.json"`
    * 在 `GraphInstanceSet` 下，编辑图拓扑的名称，使其与上一个链接中的值匹配：
    
      `"topologyName" : "EVRToFilesOnMotionDetection"`
    * 编辑 RTSP URL，使其指向视频文件：

        `"value": "rtsp://rtspsim:554/media/lots_015.mkv"`
    * 在 `GraphTopologyDelete` 下，编辑名称：

        `"name": "EVRToFilesOnMotionDetection"`
