---
title: 轮廓渲染
description: 介绍了如何执行选定项轮廓渲染
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: c04f2312926d3b6d668dff712eedb57d816c8bf3
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592001"
---
# <a name="outline-rendering"></a>轮廓渲染

可以通过[分层状态重写组件](../../overview/features/override-hierarchical-state.md)添加轮廓渲染，以可视方式突出显示选定的对象。 本章介绍了如何通过客户端 API 更改轮廓渲染的全局参数。

轮廓属性是全局设置。 使用轮廓渲染的所有对象将使用相同的设置 - 无法使用按对象的轮廓颜色。

## <a name="parameters-for-outlinesettings"></a>`OutlineSettings` 的参数

类 `OutlineSettings` 保存与全局轮廓属性相关的设置。 此类公开以下成员：

| 参数      | 类型    | 说明                                             |
|----------------|---------|---------------------------------------------------------|
| `Color`          | Color4Ub | 用于绘制轮廓的颜色。 将忽略 alpha 部分。         |
| `PulseRateHz`    | FLOAT   | 轮廓的每秒振荡频率|
| `PulseIntensity` | FLOAT   | 轮廓脉冲效果的强度。 必须介于 0.0（无脉冲）与 1.0（全脉冲）之间。 强度将轮廓的最小不透明度隐式设置为 `MinOpacity = 1.0 - PulseIntensity`。 |

![对象使用不同的轮廓参数呈现三次：将 ](./media/outlines.png) `color` 参数从黄色 (左) 改为洋红色 (中心) ， `pulseIntensity` 从0到 0.8 (右) 。

## <a name="example"></a>示例

以下代码展示了一个通过 API 设置轮廓参数的示例：

```cs
void SetOutlineParameters(RenderingSession session)
{
    OutlineSettings outlineSettings = session.Connection.OutlineSettings;
    outlineSettings.Color = new Color4Ub(255, 255, 0, 255);
    outlineSettings.PulseRateHz = 2.0f;
    outlineSettings.PulseIntensity = 0.5f;
}
```

```cpp
void SetOutlineParameters(ApiHandle<RenderingSession> session)
{
    ApiHandle<OutlineSettings> outlineSettings = session->Connection()->GetOutlineSettings();
    Color4Ub outlineColor;
    outlineColor.channels = { 255, 255, 0, 255 };
    outlineSettings->SetColor(outlineColor);
    outlineSettings->SetPulseRateHz(2.0f);
    outlineSettings->SetPulseIntensity(0.5f);
}
```

## <a name="performance"></a>性能

轮廓渲染可能会对渲染性能产生重大影响。 这种影响根据给定帧的选定对象与非选定对象之间的屏幕空间关系而异。

## <a name="api-documentation"></a>API 文档

* [C # RenderingConnection OutlineSettings 属性](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.outlinesettings)
* [C + + RenderingConnection：： OutlineSettings ( # B1 ](/cpp/api/remote-rendering/renderingconnection#outlinesettings)

## <a name="next-steps"></a>后续步骤

* [分层状态重写组件](../../overview/features/override-hierarchical-state.md)