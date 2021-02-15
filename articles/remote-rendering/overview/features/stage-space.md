---
title: 暂存空间
description: 介绍阶段空间设置和用例
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 4a739907ce0a3b0b6dfcb8791b51d0ea5e7e76e7
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594004"
---
# <a name="stage-space"></a>暂存空间

在提供打印头跟踪数据（如 HoloLens 2）的设备上运行 ARR 时，会将 head 姿势发送到用户应用程序和服务器。 在中定义头转换的空间称为 " *舞台空间*"。

若要对齐本地和远程内容，假定在客户端和服务器上的阶段空间和世界空间都相同。 如果用户决定在照相机顶部添加其他转换，则需要将其发送到服务器并正确对齐本地和远程内容。

移动阶段空间的典型原因是 [世界锁定工具](https://microsoft.github.io/MixedReality-WorldLockingTools-Unity/README.html) 或其他真实的定位技术，以及在 VR 中移动虚拟字符。

## <a name="stage-space-settings"></a>阶段空间设置

> [!CAUTION]
> 试验：此功能是实验性的，会随时间而改变。 因此，更新到较新的客户端 SDK 版本可能需要额外的工作来升级代码。 更改舞台空间原点时，当前实现将中断本地/远程内容对齐。
因此，它目前仅用于全球锁定目的，例如仅显示一段时间内非常小的更改的定位标记。

若要通知服务器会将额外的转换应用于舞台空间，则需要通过将其置于某个位置定义的源，并在世界空间内进行旋转。 可以通过 *阶段空间设置* 访问此设置。

> [!IMPORTANT]
> 在 [桌面模拟](../../concepts/graphics-bindings.md) 中，照相机的世界空间位置由用户应用程序提供。 在这种情况下，必须跳过设置阶段空间源，因为它已与相机变换进行相乘。

```cs
void ChangeStageSpace(RenderingSession session)
{
    StageSpaceSettings settings = session.Connection.StageSpaceSettings;

    // Set position and rotation to the world-space transform of the stage space.
    settings.Position = new Double3(0, 0, 0);
    settings.Rotation = new Quaternion(0, 0, 0, 1);
}
```

```cpp
void ChangeStageSpace(ApiHandle<RenderingSession> session)
{
    ApiHandle<StageSpaceSettings> settings = session->Connection()->GetStageSpaceSettings();

    // Set position and rotation to the world-space transform of the stage space.
    settings->SetPosition({0, 0, 0});
    settings->SetRotation({0, 0, 0, 1});
}
```

## <a name="unity-stage-space-script"></a>Unity 阶段空间脚本

Unity 集成提供了一个名为 **StageSpace** 的脚本，该脚本可添加到相机的父 GameObject 中。 一旦出现，此脚本将负责设置阶段空间源。

## <a name="next-steps"></a>后续步骤

* [图形绑定](../../concepts/graphics-bindings.md)
* [后期阶段重新投影](late-stage-reprojection.md)
