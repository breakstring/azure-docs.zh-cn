---
title: 网格
description: Azure 远程呈现范围内网格的定义
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 33894e0d0b6f3ea50ffeac4f0c90c60f28e09f5e
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594512"
---
# <a name="meshes"></a>网格

## <a name="mesh-resource"></a>网格资源

网格是不可变的 [共享资源](../concepts/lifetime.md)，只能通过 [模型转换](../how-tos/conversion/model-conversion.md)来创建。 网格包含一个或多个 *submeshes* ，以及 [raycast 查询](../overview/features/spatial-queries.md)的物理表示形式。 每个子网格都引用一种默认情况下应呈现的 [材料](materials.md) 。 若要在三维空间中放置网格，请将 [MeshComponent](#meshcomponent) 添加到 [实体](entities.md)。

### <a name="mesh-resource-properties"></a>网格资源属性

`Mesh`类属性为：

* **材料：** 材料的数组。 每个材料由不同的子网格使用。 数组中的多个条目可能引用相同的 [材料](materials.md)。 此数据不能在运行时修改。

* **边界：** 网格顶点 (AABB) 的本地空间轴对齐的边界框。

## <a name="meshcomponent"></a>MeshComponent

`MeshComponent`类用于放置网格资源的实例。 每个 MeshComponent 都引用单个网格。 它可以重写用于呈现每个子网格的材料。

### <a name="meshcomponent-properties"></a>MeshComponent 属性

* **网格：** 此组件使用的网格资源。

* **材料：** 在网格组件上指定的材料的数组。 该数组的长度始终与网格资源上的 *材料* 数组的长度相同。 不应从网格默认值覆盖的材料在此数组中设置为 *null* 。

* **UsedMaterials：** 每个子网格的实际使用材料的数组。 对于非 null 值，将与 *材料* 数组中的数据相同。 否则，它包含来自网格实例中的 *材料* 阵列的值。

### <a name="sharing-of-meshes"></a>网格的共享

`Mesh`可以在网格组件的多个实例之间共享资源。 此外， `Mesh` 分配给网格组件的资源可以随时以编程方式进行更改。 下面的代码演示如何克隆网格：

```cs
Entity CloneEntityWithModel(RenderingConnection api, Entity sourceEntity)
{
    MeshComponent meshComp = sourceEntity.FindComponentOfType<MeshComponent>();
    if (meshComp != null)
    {
        Entity newEntity = api.CreateEntity();
        MeshComponent newMeshComp = api.CreateComponent(ObjectType.MeshComponent, newEntity) as MeshComponent;
        newMeshComp.Mesh = meshComp.Mesh; // share the mesh
        return newEntity;
    }
    return null;
}
```

```cpp
ApiHandle<Entity> CloneEntityWithModel(ApiHandle<RenderingConnection> api, ApiHandle<Entity> sourceEntity)
{
    if (ApiHandle<MeshComponent> meshComp = sourceEntity->FindComponentOfType<MeshComponent>())
    {
        ApiHandle<Entity> newEntity = *api->CreateEntity();
        ApiHandle<MeshComponent> newMeshComp = api->CreateComponent(ObjectType::MeshComponent, newEntity)->as<RemoteRendering::MeshComponent>();
        newMeshComp->SetMesh(meshComp->GetMesh()); // share the mesh
        return newEntity;
    }
    return nullptr;
}
```

## <a name="api-documentation"></a>API 文档

* [C # 网格类](/dotnet/api/microsoft.azure.remoterendering.mesh)
* [C # MeshComponent 类](/dotnet/api/microsoft.azure.remoterendering.meshcomponent)
* [C + + 网格类](/cpp/api/remote-rendering/mesh)
* [C + + MeshComponent 类](/cpp/api/remote-rendering/meshcomponent)


## <a name="next-steps"></a>后续步骤

* [材料](materials.md)