---
title: 颜色材料
description: 描述颜色材料类型。
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: fb26a669229ac140aba262032f8ce84ef9f37727
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593375"
---
# <a name="color-materials"></a>颜色材料

*颜色材料* 是 Azure 远程呈现中支持的 [材料类型](../../concepts/materials.md) 之一。 它们用于不应接收任何类型照明的 [网格](../../concepts/meshes.md) ，而是始终使用完全亮度。 这种情况可能适用于 "光亮" 材料，如汽车仪表板、轻型电灯泡或已合并静态照明的数据（例如通过 [photogrammetry](https://en.wikipedia.org/wiki/Photogrammetry)获取的模型）。

颜色材料比 [.pbr 材料](pbr-materials.md) 更高效，因为它的深浅底纹模型更为简单。 它们还支持不同的透明模式。

## <a name="common-material-properties"></a>常用材料属性

这些属性是所有材料共有的：

* **albedoColor：** 此颜色与其他颜色（如 *albedoMap* 或 *:::no-loc text="vertex"::: 颜色*）相乘。 如果对材料启用了 *透明度* ，则使用 alpha 通道调整不透明度， `1` 这意味着完全不透明并 `0` 表示完全透明。 默认值为白色。

  > [!NOTE]
  > 由于颜色材料不反映环境，因此完全透明的颜色材料将变得不可见。 这对于 [.pbr 材料](pbr-materials.md)是不同的。

* **albedoMap：** 每像素 albedo 值的 [2d 纹理](../../concepts/textures.md) 。

* **alphaClipEnabled** 和 **AlphaClipThreshold：** 如果 *alphaClipEnabled* 为 true，则不会绘制 albedo alpha 值小于 *alphaClipThreshold* 的所有像素。 即使不启用透明度，也可以使用 Alpha 剪辑，因而速度要快得多。 不过，Alpha 剪裁材料的呈现速度仍比完全不透明材料慢。 默认情况下，alpha 剪辑处于禁用状态。

* **textureCoordinateScale** 和 **textureCoordinateOffset：** 将刻度与 UV 纹理坐标相乘，并向其添加偏移量。 可用于拉伸和移动纹理。 默认小数位数为1，1) ，偏移量为 (0，0)  (。

* **useVertexColor：** 如果网格包含 :::no-loc text="vertex"::: 颜色且已启用此选项，则网格的 :::no-loc text="vertex"::: 颜色将被相乘到 *albedoColor* 和 *albedoMap* 中。 默认情况下， *useVertexColor* 处于禁用状态。

* **isDoubleSided：** 如果将 sidedness 设置为 true，则即使相机正在查看背景面，也会呈现带有此材料的三角形。 默认情况下，此选项处于禁用状态。 另请参阅[ :::no-loc text="Single-sided"::: 呈现](single-sided-rendering.md)。

* **TransparencyWritesDepth：** 如果对材料设置了 TransparencyWritesDepth 标志，而材料是透明的，则使用此材料的对象也会对最终的深度缓冲区产生影响。 请参阅下一节中的颜色材料属性 *transparencyMode* 。 如果用例需要更变得合理的 [延迟阶段 reprojection](late-stage-reprojection.md) 完全透明的场景，则建议启用此功能。 对于混合的不透明/透明场景，此设置可能会引入可能 reprojection 行为或 reprojection 项目。 出于此原因，一般用例的默认设置和推荐设置是禁用此标志。 书写的深度值取自离相机最近的对象的每像素深度层。

* **FresnelEffect：** 此材料标志启用对各自材料的加法 [菲涅尔衰减效果](../../overview/features/fresnel-effect.md) 。 此效果的外观由以下所述的其他菲涅尔衰减参数控制。 

* **FresnelEffectColor：** 此材料使用的菲涅尔衰减颜色。 仅当已对此材料设置了菲涅尔衰减效果位时，才 (参阅上面) 。 此属性控制菲涅尔衰减照射 (的基本颜色，请参阅 [菲涅尔衰减效果](../../overview/features/fresnel-effect.md) 以获取完整说明) 。 目前只有 rgb 通道值非常重要，并且 alpha 值将被忽略。

* **FresnelEffectExponent：** 此材料使用的菲涅尔衰减指数。 仅当已对此材料设置了菲涅尔衰减效果位时，才 (参阅上面) 。 此属性控制菲涅尔衰减的照射的传播。 最小值0.01 导致整个对象分布。 最大值10.0 限制仅显示最 gracing 边缘。

## <a name="color-material-properties"></a>颜色材料属性

以下属性特定于颜色材料：

* **vertexMix：** 与之间的此值 `0` `1` :::no-loc text="vertex"::: 用于指定 [网格](../../concepts/meshes.md) 中的颜色对最终颜色的高度。 如果默认值为1，则将 :::no-loc text="vertex"::: 颜色完全乘以 albedo 颜色。 如果值为0，则 :::no-loc text="vertex"::: 完全忽略颜色。

* **transparencyMode：** 与 [.pbr 材料](pbr-materials.md)相反，颜色材料区分不同的透明模式：

  1. 不 **透明：** 默认模式禁用透明度。 不过，即使有足够的，也仍然可以进行 Alpha 剪裁。
  
  1. **AlphaBlended：** 此模式类似于 .PBR 材料的透明模式。 它应该用于查看-通过玻璃等材料。

  1. **累加性：** 此模式是最简单且最有效的透明模式。 将材料的贡献添加到呈现的图像。 此模式可用于模拟光亮 (但仍然是透明) 对象，例如用于突出显示重要对象的标记。

## <a name="api-documentation"></a>API 文档

* [C # ColorMaterial 类](/dotnet/api/microsoft.azure.remoterendering.colormaterial)
* [C # RenderingConnection CreateMaterial ( # B1 ](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.creatematerial)
* [C + + ColorMaterial 类](/cpp/api/remote-rendering/colormaterial)
* [C + + RenderingConnection：： CreateMaterial ( # B1 ](/cpp/api/remote-rendering/renderingconnection#creatematerial)

## <a name="next-steps"></a>后续步骤

* [PBR 材料](pbr-materials.md)
* [纹理](../../concepts/textures.md)
* [网格](../../concepts/meshes.md)