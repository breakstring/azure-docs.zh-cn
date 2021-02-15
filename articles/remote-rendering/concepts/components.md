---
title: 组件
description: Azure 远程渲染范围内组件的定义
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 9679be03c69090a0c11d007cfc542bae70bd3cbc
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592188"
---
# <a name="components"></a>组件

Azure 远程渲染使用[实体组件系统](https://en.wikipedia.org/wiki/Entity_component_system)模式。 [实体](entities.md)代表对象的位置和层次结构组合，而组件则负责实现行为。

最常使用的组件类型是 [:::no-loc text="mesh components":::](meshes.md) ，它将网格添加到呈现管道中。 同样，[光线组件](../overview/features/lights.md)用于添加光线，而[剖切面组件](../overview/features/cut-planes.md)用于切开网格。

所有这些组件都使用它们所附着的实体的转换（位置、旋转、缩放）作为它们的参考点。

## <a name="working-with-components"></a>使用组件

可以通过编程方式轻松地添加、删除和操作组件：

```cs
// create a point light component
RenderingSession session = GetCurrentlyConnectedSession();
PointLightComponent lightComponent = session.Connection.CreateComponent(ObjectType.PointLightComponent, ownerEntity) as PointLightComponent;

lightComponent.Color = new Color4Ub(255, 150, 20, 255);
lightComponent.Intensity = 11;

// ...

// destroy the component
lightComponent.Destroy();
lightComponent = null;
```

```cpp
// create a point light component
ApiHandle<RenderingSession> session = GetCurrentlyConnectedSession();

ApiHandle<PointLightComponent> lightComponent = session->Connection()->CreateComponent(ObjectType::PointLightComponent, ownerEntity)->as<PointLightComponent>();

// ...

// destroy the component
lightComponent->Destroy();
lightComponent = nullptr;
```

组件在创建时附加到实体。 之后不能将其迁移至其他实体。 使用 `Component.Destroy()` 显式删除组件，或者在组件的所有者实体被销毁时自动显式删除组件。

一次只能向一个实体添加每个组件类型的一个实例。

## <a name="unity-specific"></a>Unity 特定

Unity 集成具有其他扩展函数，用于与组件进行交互。 请参阅 [Unity 游戏对象和组件](../how-tos/unity/objects-components.md)。

## <a name="api-documentation"></a>API 文档

* [C # ComponentBase](/dotnet/api/microsoft.azure.remoterendering.componentbase)
* [C # RenderingConnection CreateComponent ( # B1 ](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.createcomponent)
* [C # FindComponentOfType ( # B1 ](/dotnet/api/microsoft.azure.remoterendering.entity.findcomponentoftype)
* [C + + ComponentBase](/cpp/api/remote-rendering/componentbase)
* [C + + RenderingConnection：： CreateComponent ( # B1 ](/cpp/api/remote-rendering/renderingconnection#createcomponent)
* [C + + Entity：： FindComponentOfType ( # B1 ](/cpp/api/remote-rendering/entity#findcomponentoftype)

## <a name="next-steps"></a>后续步骤

* [对象边界](object-bounds.md)
* [网格](meshes.md)