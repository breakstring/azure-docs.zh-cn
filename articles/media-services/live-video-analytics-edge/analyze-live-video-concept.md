---
title: 在不录制的情况下分析实时视频 - Azure
description: 使用媒体图，可以仅从实时视频流中提取分析，而不在边缘或云中进行录制。 本文讨论了这一概念。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 25a7cadc47603b726542fa391d441e1fbca78908
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2020
ms.locfileid: "97398954"
---
# <a name="analyzing-live-video-without-any-recording"></a>在不录制的情况下分析实时视频

## <a name="suggested-pre-reading"></a>建议的读前准备 

* [媒体图概念](media-graph-concept.md)
* [基于事件的视频录制](event-based-video-recording-concept.md)

## <a name="overview"></a>概述  

可以使用媒体图来分析实时视频，而不将视频的任何部分录制到文件或资产。 下方显示的媒体图与[基于事件的视频录制](event-based-video-recording-concept.md)一文中的关系图类似，但不包含资产接收器节点或文件接收器节点。

### <a name="motion-detection"></a>动作检测

下方显示的媒体图包含一个 [RTSP 源](media-graph-concept.md#rtsp-source)节点、一个[动作检测处理器](media-graph-concept.md#motion-detection-processor)节点和一个[IoT 中心消息接收器](media-graph-concept.md#iot-hub-message-sink)节点。 可在[此处](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/motion-detection/topology.json)找到此类媒体图的图形拓扑的 JSON 表示形式。 此图让你可以检测传入实时视频流中的运动，并通过 IoT 中心消息接收器节点将动作事件中继到其他应用和服务。 外部应用或服务可以触发警报，或向相应人员发送通知。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/motion-detection.svg" alt-text="基于运动检测的实时视频分析":::

### <a name="analyzing-video-using-a-custom-vision-model"></a>使用自定义视觉模型分析视频 

下方显示的媒体图让你可以使用打包在不同模块中的自定义视觉模型来分析实时视频流。 可在[此处](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/topology.json)找到此类媒体图的图形拓扑的 JSON 表示形式。 可在[此处](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis)查看一些示例，了解如何将模型包装到作为推理服务运行的 IoT Edge 模块中。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/motion-detected-frames.svg" alt-text="基于外部推理模块的实时视频分析":::

在此媒体图中，RTSP 源的视频输入将发送到 [HTTP 扩展处理器](media-graph-concept.md#http-extension-processor) 节点，该节点将以 JPEG、BMP 或 PNG 格式 (的图像帧发送) 到外部推理服务而不是 REST。 来自外部推理服务的结果由 HTTP 扩展节点检索，并通过 IoT 中心消息接收器节点中继到 IoT Edge 中心。 这种类型的媒体图可用于为各种场景构建解决方案，例如了解路口车辆的时序分布、了解零售商店中的消费者流量模式等。
>[!TIP]
> 你可以使用字段在 HTTP 扩展处理器节点内管理帧速率， `samplingOptions` 然后再将其发送到下游。

此示例的增强功能是在 HTTP 扩展处理器节点之前使用运动检测程序处理器。 这将减少推理服务上的负载，因为仅在视频中有运动活动时使用它。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/custom-model.svg" alt-text="基于通过外部推理模块实现的运动检测帧的实时视频分析":::

## <a name="next-steps"></a>后续步骤

[连续视频录制](continuous-video-recording-concept.md)
