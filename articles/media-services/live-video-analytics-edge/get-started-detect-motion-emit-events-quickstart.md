---
title: IoT Edge 上的实时视频分析入门 - Azure
description: 本快速入门演示如何开始使用 IoT Edge 上的实时视频分析。 了解如何检测实时视频流中的运动。
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: d9f2637166c3807e36f4304e9394bf2ecf91d88c
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99573800"
---
# <a name="quickstart-get-started---live-video-analytics-on-iot-edge"></a>快速入门：入门 - IoT Edge 上的实时视频分析

本快速入门将引导你完成开始使用 IoT Edge 上的实时视频分析的步骤。 它将 Azure VM 用作 IoT Edge 设备。 它还使用模拟的实时视频流。 

完成设置步骤后，你将能通过媒体图运行模拟实时视频流，该媒体图可检测和报告该流中的任何运动。 下图显示了该媒体图的图形表示形式。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/motion-detection.svg" alt-text="基于运动检测的实时视频分析":::

可以查看以下视频，其中包含有关如何开始使用 IoT Edge 上的实时视频分析的详细步骤：

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Hcax]

## <a name="prerequisites"></a>先决条件

* 具有活动订阅的 Azure 帐户。 如果没有帐户，可[免费创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

  > [!NOTE]
  > 你将需要一个具有服务主体创建权限（所有者角色提供此权限）的 Azure 订阅。 如果你没有正确的权限，请联系帐户管理员，让其授予你适当的权限。  

* 开发计算机上的 [Visual Studio Code](https://code.visualstudio.com/)。 请确保具有 [Azure IoT Tools 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)。
* 确保开发计算机连接到的网络允许出站流量采用高级消息队列协议 (AMQP) 流经端口 5671。 此设置使 Azure IoT Tools 可以与 Azure IoT 中心通信。

> [!TIP]
> 安装 Azure IoT Tools 扩展时，系统可能会提示你安装 Docker。 可以忽略此提示。

## <a name="set-up-azure-resources"></a>设置 Azure 资源

本教程需要以下 Azure 资源：

* IoT 中心
* 存储帐户
* Azure 媒体服务帐户
* Azure 中的 Linux VM，已安装 [IoT Edge 运行时](../../iot-edge/how-to-install-iot-edge.md)

在本快速入门中，我们建议你使用[实时视频分析资源设置脚本](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)在 Azure 订阅中部署所需资源。 为此，请执行下列步骤：

1. 转到 [Azure 门户](https://portal.azure.com)并选择 Cloud Shell 图标。
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstarts/cloud-shell.png" alt-text="Cloud Shell":::
1. 如果你是第一次使用 Cloud Shell，系统将提示你选择一个订阅以创建存储帐户和 Microsoft Azure 文件存储共享。 选择“创建存储”，创建用于存储 Cloud Shell 会话信息的存储帐户。 此存储帐户不同于脚本将要创建的与 Azure 媒体服务帐户配合使用的帐户。
1. 在 Cloud Shell 窗口左侧的下拉菜单中，选择“Bash”作为环境。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstarts/env-selector.png" alt-text="环境选择器":::
1. 运行以下命令。

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
    在脚本成功完成后，你应可在订阅中看到所有所需资源。 脚本将设置总共 12 种资源：
    1. **流式处理终结点** - 这将有助于播放记录的 AMS 资产。
    1. **虚拟机** - 这是将充当边缘设备的虚拟机。
    1. **磁盘** - 这是连接到虚拟机来存储媒体和项目的存储磁盘。
    1. **网络安全组** - 这用于筛选 Azure 虚拟网络中出入 Azure 资源的网络流量。
    1. **网络接口** - Azure 虚拟机可通过它与 Internet、Azure 和其他资源进行通信。
    1. **堡垒连接** - 你可通过它使用浏览器和 Azure 门户连接到虚拟机。
    1. **公共 IP 地址** - Azure 资源可通过它与 Internet 和面向公众的 Azure 服务通信
    1. **虚拟网络** - 通过它，许多类型的 Azure 资源（例如虚拟机）可以安全方式彼此通信、与 Internet 通信，以及与本地网络通信。 详细了解[虚拟网络](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)。
    1. **IoT 中心** - 这充当消息中心，用于在 IoT 应用程序、IoT Edge 模块以及它管理的设备之间进行双向通信。
    1. **媒体服务帐户** - 这有助于在 Azure 中管理和流式传输媒体内容。
    1. **存储帐户** - 你必须具有一个主存储帐户，而且可拥有任意数量与媒体服务帐户关联的辅助存储帐户。 有关详细信息，请参阅 [Azure 存储帐户与 Azure 媒体服务帐户](https://docs.microsoft.com/azure/media-services/latest/storage-account-concept)。
    1. **容器注册表** - 这有助于存储和管理专用 Docker 容器映像及相关项目。

在脚本输出中，资源表会列出 IoT 中心名称。 查找资源类型 `Microsoft.Devices/IotHubs`，并记下名称。 下一步骤需要用到此名称。  

> [!NOTE]
> 该脚本还会在 ~/clouddrive/lva-sample/ 目录中生成一些配置文件。 稍后在快速入门中需要用到这些文件。

> [!TIP]
> 如果在创建 Azure 资源时遇到问题，请查看[故障排除指南](troubleshoot-how-to.md#common-error-resolutions)来解决一些常见问题。

## <a name="deploy-modules-on-your-edge-device"></a>在边缘设备上部署模块

在 Cloud Shell 中运行以下命令。

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content ~/clouddrive/lva-sample/edge-deployment/deployment.amd64.json
```

此命令会将以下模块部署到边缘设备（在此例中为 Linux VM）。

* IoT Edge 上的实时视频分析（模块名称为 `lvaEdge`）
* 实时流式处理协议 (RTSP) 模拟器（模块名称为 `rtspsim`）

RTSP 模拟器模块使用视频文件模拟实时视频流，该文件已在运行[实时视频分析资源设置脚本](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)时复制到边缘设备。 

现在已部署模块，但没有媒体图处于活动状态。

## <a name="configure-the-azure-iot-tools-extension"></a>配置 Azure IoT Tools 扩展

按照以下说明使用 Azure IoT Tools 扩展连接到 IoT 中心。

1. 在 Visual Studio Code 中，打开“扩展”选项卡（或按 Ctrl+Shift+X），然后搜索“Azure IoT 中心”。
1. 右键单击并选择“扩展设置”。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="扩展设置":::
1. 搜索并启用“显示详细消息”。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="显示详细消息":::
1. 选择”查看” > ”资源管理器”， 或选择 Ctrl+Shift+E。
1. 在“资源管理器”选项卡的左下角，选择“Azure IoT 中心”。
1. 选择“更多选项”图标以查看上下文菜单。 然后选择“设置 IoT 中心连接字符串”。
1. 输入框出现时，在其中输入 IoT 中心连接字符串。 在 Cloud Shell 中，可以从 ~/clouddrive/lva-sample/appsettings.json 获取连接字符串。

> [!NOTE]
> 系统可能会要求你提供 IoT 中心的内置终结点信息。 若要获取此信息，请在 Azure 门户中导航到 IoT 中心，然后在左侧导航窗格中查找“内置终结点”选项。 单击此处，在“与事件中心兼容的终结点”部分下查找“与事件中心兼容的终结点” 。 复制并使用框中的文本。 终结点将如下所示：  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

如果连接成功，边缘设备列表随即显示。 应该会看到至少一个设备，名为 lva-sample-device。 现在你可以管理 IoT Edge 设备，并通过上下文菜单与 Azure IoT 中心进行交互。 若有查看部署在边缘设备上的模块，请在“lva-sample-device”下，展开“模块”节点。

![lva-sample-device 节点](./media/quickstarts/lva-sample-device-node.png)

> [!TIP]
> 如果你自己已在边缘设备（例如 ARM64 设备）上[手动部署了 IoT Edge 上的实时视频分析](deploy-iot-edge-device.md)，则你会在 Azure IoT 中心看到模块显示在该设备下。 你可以选择该模块，然后执行下面的剩余步骤。

## <a name="use-direct-method-calls"></a>使用直接方法调用

可以通过调用直接方法来使用该模块分析实时视频流。 有关详细信息，请参阅 [IoT Edge上的实时视频分析的直接方法](direct-methods.md)。 

### <a name="invoke-graphtopologylist"></a>调用 GraphTopologyList

若有枚举模块中的所有[图形拓扑](media-graph-concept.md#media-graph-topologies-and-instances)：

1. 在 Visual Studio Code 中，右键单击“lvaEdge”模块，然后选择“调用模块直接方法”。
1. 在出现的框中，输入 GraphTopologyList。
1. 复制以下 JSON 有效负载，然后粘贴到框中。 然后选择 Enter 键。

    ```
    {
        "@apiVersion" : "2.0"
    }
    ```

    几秒钟内，“输出”窗口会显示以下响应。

    ```
    [DirectMethod] Invoking Direct Method [GraphTopologyList] to [lva-sample-device/lvaEdge] ...
    [DirectMethod] Response from [lva-sample-device/lvaEdge]:
    {
      "status": 200,
      "payload": {
        "value": []
      }
    }
    ```
    
    由于没有创建图形拓扑，所以预期会出现此响应。
    

### <a name="invoke-graphtopologyset"></a>调用 GraphTopologySet

与之前一样，现可调用 `GraphTopologySet` 来设置[图形拓扑](media-graph-concept.md#media-graph-topologies-and-instances)。 使用以下 JSON 作为有效负载。

```
{
    "@apiVersion": "2.0",
    "name": "MotionDetection",
    "properties": {
        "description": "Analyzing live video to detect motion and emit events",
        "parameters": [
            {
                "name": "rtspUserName",
                "type": "String",
                "description": "rtsp source user name.",
                "default": "dummyUserName"
            },
            {
                "name": "rtspPassword",
                "type": "String",
                "description": "rtsp source password.",
                "default": "dummyPassword"
            },
            {
                "name": "rtspUrl",
                "type": "String",
                "description": "rtsp Url"
            }
        ],
        "sources": [
            {
                "@type": "#Microsoft.Media.MediaGraphRtspSource",
                "name": "rtspSource",
                "endpoint": {
                    "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
                    "url": "${rtspUrl}",
                    "credentials": {
                        "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
                        "username": "${rtspUserName}",
                        "password": "${rtspPassword}"
                    }
                }
            }
        ],
        "processors": [
            {
                "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
                "name": "motionDetection",
                "sensitivity": "medium",
                "inputs": [
                    {
                        "nodeName": "rtspSource"
                    }
                ]
            }
        ],
        "sinks": [
            {
                "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
                "name": "hubSink",
                "hubOutputName": "inferenceOutput",
                "inputs": [
                    {
                        "nodeName": "motionDetection"
                    }
                ]
            }
        ]
    }
}

```

此 JSON 有效负载创建定义三个参数的图形拓扑。 其中两个参数具有默认值。 拓扑具有一个源（RTSP 源）节点、一个处理器（运动检测处理器）节点和一个接收器（IoT 中心接收器）节点。

几秒钟内，“输出”窗口中显示以下响应。

```
[DirectMethod] Invoking Direct Method [GraphTopologySet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-19T07:41:34.507Z",
      "lastModifiedAt": "2020-05-19T07:41:34.507Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "medium",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

返回的状态为 201。 此状态指示已创建了新拓扑。 

请尝试以下后续步骤：

1. 再次调用 `GraphTopologySet`。 返回的状态代码为 200。 此代码指示已成功更新现有拓扑。
1. 再次调用 `GraphTopologySet`，但更改描述字符串。 返回的状态代码为 200，且描述已更新为新值。
1. 调用 `GraphTopologyList`，如前一部分所述。 现在，你可以在返回的有效负载中查看 `MotionDetection` 拓扑。

### <a name="invoke-graphtopologyget"></a>调用 GraphTopologyGet

使用以下有效负载调用 `GraphTopologyGet`。

```
{
    "@apiVersion" : "2.0",
    "name" : "MotionDetection"
}
```

几秒钟内，“输出”窗口中显示以下响应：

```
[DirectMethod] Invoking Direct Method [GraphTopologyGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-19T07:41:34.507Z",
      "lastModifiedAt": "2020-05-19T07:41:34.507Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "medium",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

在响应有效负载中，请注意以下详细信息：

* 状态代码为 200，表示成功。
* 有效负载包含 `created` 时间戳和 `lastModified` 时间戳。

### <a name="invoke-graphinstanceset"></a>调用 GraphInstanceSet

创建引用上述图形拓扑的图形实例。 图形实例允许你使用相同图形拓扑分析来自许多照相机的实时视频流。 有关详细信息，请参阅[媒体图拓扑和实例](media-graph-concept.md#media-graph-topologies-and-instances)。

使用以下有效负载调用直接方法 `GraphInstanceSet`。

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-1",
    "properties" : {
        "topologyName" : "MotionDetection",
        "description" : "Sample graph description",
        "parameters" : [
            { "name" : "rtspUrl", "value" : "rtsp://rtspsim:554/media/camera-300s.mkv" }
        ]
    }
}
```

请注意，此有效负载：

* 指定需要为其创建实例的拓扑名称 (`MotionDetection`)。
* 包含 `rtspUrl`（它在图形拓扑有效负载中没有默认值）的参数值。 此值是指向以下示例视频的链接：
    > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LTY4]
几秒钟内，“输出”窗口中显示以下响应：

```
[DirectMethod] Invoking Direct Method [GraphInstanceSet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "name": "Sample-Graph-1",
    "properties": {
      "created": "2020-05-19T07:44:33.868Z",
      "lastModified": "2020-05-19T07:44:33.868Z",
      "state": "Inactive",
      "description": "Sample graph description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        }
      ]
    }
  }
}
```

在响应有效负载中，请注意：

* 状态代码为 201，指示已创建新实例。
* 状态为 `Inactive`，指示图形实例已创建但未激活。 有关详细信息，请参阅[媒体图状态](media-graph-concept.md)。

请尝试以下后续步骤：

1. 使用相同有效负载再次调用 `GraphInstanceSet`。 请注意，返回的状态代码为 200。
1. 再次调用 `GraphInstanceSet`，但使用不同描述。 请注意响应有效负载中更新的描述，指示图形实例已成功更新。
1. 调用 `GraphInstanceSet`，但将名称更改为 `Sample-Graph-2`。 在响应有效负载中，请注意新创建的图形实例（即状态代码 201）。

### <a name="invoke-graphinstanceactivate"></a>调用 GraphInstanceActivate

现在激活图形实例，以通过模块启动实时视频流。 使用以下有效负载调用直接方法 `GraphInstanceActivate`。

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-1"
}
```

几秒钟内，“输出”窗口中显示以下响应。

```
[DirectMethod] Invoking Direct Method [GraphInstanceActivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

状态代码 200 指示图形实例已成功激活。

### <a name="invoke-graphinstanceget"></a>调用 GraphInstanceGet

现在使用以下有效负载调用直接方法 `GraphInstanceGet`。

```
 {
     "@apiVersion" : "2.0",
     "name" : "Sample-Graph-1"
 }
 ```

几秒钟内，“输出”窗口中显示以下响应。

```
[DirectMethod] Invoking Direct Method [GraphInstanceGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "name": "Sample-Graph-1",
    "properties": {
      "created": "2020-05-19T07:44:33.868Z",
      "lastModified": "2020-05-19T07:44:33.868Z",
      "state": "Active",
      "description": "graph description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        }
      ]
    }
  }
}
```

在响应有效负载中，请注意以下详细信息：

* 状态代码为 200，表示成功。
* 状态为 `Active`，表示图形实例现在处于活动状态。

## <a name="observe-results"></a>观察结果

创建和激活的图形实例使用运动检测处理器节点来检测传入实时视频流中的运动。 它会将事件发送到 IoT 中心接收器节点。 这些事件会中继到 IoT Edge 中心。 

若要观察结果，请遵循以下步骤。

1. 在 Visual Studio Code 中，打开“资源管理器”窗格。 在左下角，查找“Azure IoT 中心”。
2. 展开“设备”节点。
3. 右键单击“lva-sample-device”，然后选择“开始监视内置事件监视” 。

    ![开始监视 IoT 中心事件](./media/quickstarts/start-monitoring-iothub-events.png)

    > [!NOTE]
    > 系统可能会要求你提供 IoT 中心的内置终结点信息。 若要获取此信息，请在 Azure 门户中导航到 IoT 中心，然后在左侧导航窗格中查找“内置终结点”选项。 单击此处，在“与事件中心兼容的终结点”部分下查找“与事件中心兼容的终结点” 。 复制并使用框中的文本。 终结点将如下所示：  
        ```
        Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
        ```
    
“输出”窗口显示以下消息：

```
[IoTHubMonitor] [7:44:33 AM] Message received from [lva-sample-device/lvaEdge]:
{
    "body": {
    "timestamp": 143005362606360,
    "inferences": [
        {
        "type": "motion",
        "motion": {
            "box": {
            "l": 0.828452,
            "t": 0.455224,
            "w": 0.1,
            "h": 0.088889
            }
        }
        },
        {
        "type": "motion",
        "motion": {
            "box": {
            "l": 0.661088,
            "t": 0.597015,
            "w": 0.0625,
            "h": 0.051852
            }
        }
        }
    ]
    }
}
```

请注意以下详细信息：

* 该消息包含 `body` 部分和 `applicationProperties` 部分。 有关详细信息，请参阅[创建和读取 IoT 中心消息](../../iot-hub/iot-hub-devguide-messages-construct.md)。
* 在 `applicationProperties` 中，`subject` 引用生成消息的 `MediaGraph` 中的节点。 在本例中，该消息来自运动检测处理器。
* `applicationProperties` 中的 `eventType` 指示此事件是分析事件。
* `eventTime` 值为事件发生的时间。
* `body` 部分包含有关分析事件的数据。 在本例中，该事件是推理事件，因此正文包含 `timestamp` 和 `inferences` 数据。
* `inferences` 部分指示 `type` 为 `motion`。 它提供有关 `motion` 事件的附加数据。

如果让媒体图运行一段时间，你会在“输出”窗口中看到以下消息。

```
[IoTHubMonitor] [7:47:45 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1588948185746703 1 IN IP4 172.xx.xx.xx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-19T07:47:45.747Z"
  }
}
```

在此消息中，请注意以下详细信息：

* `applicationProperties` 中的 `subject` 指示消息是从媒体图中的 RTSP 源节点生成的。
* `applicationProperties` 中的 `eventType` 指示此事件是诊断事件。
* `body` 包含有关诊断事件的数据。 在本例中，消息包含正文，因为事件是 `MediaSessionEstablished`。

## <a name="invoke-additional-direct-methods-to-clean-up"></a>调用其他直接方法进行清理

调用直接方法以便先停用图形实例，然后删除它。

### <a name="invoke-graphinstancedeactivate"></a>调用 GraphInstanceDeactivate

使用以下有效负载调用直接方法 `GraphInstanceDeactivate`。

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-1"
}
```

几秒钟内，“输出”窗口中显示以下响应：

```
[DirectMethod] Invoking Direct Method [GraphInstanceDeactivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

状态代码 200 指示图形实例已成功停用。

接下来，尝试按本文前面所示来调用 `GraphInstanceGet`。 观察 `state` 值。

### <a name="invoke-graphinstancedelete"></a>调用 GraphInstanceDelete

使用以下有效负载调用直接方法 `GraphInstanceDelete`。

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-1"
}
```

几秒钟内，“输出”窗口中显示以下响应：

```
[DirectMethod] Invoking Direct Method [GraphInstanceDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

状态代码 200 指示图形实例已成功删除。

### <a name="invoke-graphtopologydelete"></a>调用 GraphTopologyDelete

使用以下有效负载调用直接方法 `GraphTopologyDelete`。

```
{
    "@apiVersion" : "2.0",
    "name" : "MotionDetection"
}
```

几秒钟内，“输出”窗口中显示以下响应。

```
[DirectMethod] Invoking Direct Method [GraphTopologyDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

状态代码 200 指示图形拓扑已成功删除。

请尝试以下后续步骤：

1. 调用 `GraphTopologyList`，并观察到模块不包含图形拓扑。
1. 使用与 `GraphTopologyList` 相同的有效负载调用 `GraphInstanceList`。 观察到未枚举任何图形实例。

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此应用程序，请删除本快速入门中创建的资源。

## <a name="next-steps"></a>后续步骤

* 了解如何[使用 IoT Edge 上的实时视频分析录制视频](continuous-video-recording-tutorial.md)。
* 详细了解[诊断消息](monitoring-logging.md)。