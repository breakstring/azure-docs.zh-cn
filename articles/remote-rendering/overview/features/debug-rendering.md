---
title: 调试渲染
description: 服务器端调试渲染效果的概述
author: jumeder
ms.author: jumeder
ms.date: 06/15/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: f6d79f41843069fe6cafe1fa1358ac6c1aab12e6
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99591729"
---
# <a name="debug-rendering"></a>调试渲染

调试渲染 API 提供一系列全局选项，用于以不同的调试效果更改服务器端渲染。

## <a name="available-debug-rendering-effects"></a>可用的调试渲染效果

|设置                          | 效果                               |
|---------------------------------|:-------------------------------------|
|帧计数器                    | 在帧的左上角渲染文本叠加层。 文本将显示当前的服务器端帧 ID，在渲染过程中，此 ID 不断递增。 |
|多边形计数                    | 在帧的左上角渲染文本叠加层。 文本将显示当前渲染的多边形数量，该数量与[服务器端性能查询](performance-queries.md)的查询结果值相同| 
|线框                        | 如果已启用，则会以线框模式渲染服务器上加载的所有对象几何 在此模式下，只会光栅化多边形的边缘。 |

以下代码启用了这些调试效果：

```cs
void EnableDebugRenderingEffects(RenderingSession session, bool highlight)
{
    DebugRenderingSettings settings = session.Connection.DebugRenderingSettings;

    // Enable frame counter text overlay on the server side rendering
    settings.RenderFrameCount = true;

    // Enable polygon count text overlay on the server side rendering
    settings.RenderPolygonCount = true;

    // Enable wireframe rendering of object geometry on the server
    settings.RenderWireframe = true;
}
```

```cpp
void EnableDebugRenderingEffects(ApiHandle<RenderingSession> session, bool highlight)
{
    ApiHandle<DebugRenderingSettings> settings = session->Connection()->GetDebugRenderingSettings();

    // Enable frame counter text overlay on the server side rendering
    settings->SetRenderFrameCount(true);

    // Enable polygon count text overlay on the server side rendering
    settings->SetRenderPolygonCount(true);

    // Enable wireframe rendering of object geometry on the server
    settings->SetRenderWireframe(true);
}
```

![调试渲染](./media/debug-rendering.png)

> [!NOTE]
> 所有调试渲染效果都是影响整个帧的全局设置。

## <a name="use-cases"></a>用例

调试渲染 API 用于简单的调试任务，例如，验证服务连接是否确实已正常启动并运行。 文本渲染选项直接影响下游的视频帧。 启用这些选项可验证是否收到了新帧并正确对其进行了视频解码。

但是，提供的效果不会对服务运行状况提供任何详细自检。 对于这种用例，建议使用[服务器端性能查询](performance-queries.md)。

## <a name="performance-considerations"></a>性能注意事项

* 启用文本叠加只会产生极少的性能开销，甚至不会产生任何开销。
* 启用线框模式确实会产生相当高的性能开销，不过，具体的开销因场景而异。 对于复杂场景，此模式可能导致帧速率下降到 60-Hz 目标以下。

## <a name="api-documentation"></a>API 文档

* [C + + RenderingConnection：:D ebugRenderingSettings ( # B1 ](/cpp/api/remote-rendering/renderingconnection#debugrenderingsettings)

## <a name="next-steps"></a>后续步骤

* [渲染模式](../../concepts/rendering-modes.md)
* [服务器端性能查询](performance-queries.md)