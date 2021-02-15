---
title: Azure Maps Web SDK 中的图像模板 |Microsoft Azure 映射
description: 了解如何使用 Azure Maps Web SDK 将图像图标和图案填充多边形添加到地图中。 查看可用的图像和填充模式模板。
author: rbrundritt
ms.author: richbrun
ms.date: 8/6/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 5f455a1132a0f63e1ba3eb5d2a57a1f9bfa9a867
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895675"
---
# <a name="how-to-use-image-templates"></a>如何使用图像模板

可以在 Azure Maps web SDK 中的 HTML 标记和各种层上使用图像：

 - 符号层可使用图像图标呈现地图上的点。 还可以沿着行路径呈现符号。
 - 可以使用填充模式图像呈现多边形层。 
 - HTML 标记可以使用图像和其他 HTML 元素呈现点。

为了确保层具有良好的性能，请在呈现前将图像加载到地图图像动画处理资源。 默认情况下，SymbolLayer 的 [IconOptions](/javascript/api/azure-maps-control/atlas.iconoptions)将几个颜色的标记图像预加载到地图图像的子画面。 这些标记图像和其他标记图像作为 SVG 模板提供。 它们可用于创建具有自定义比例的图像，或用作客户主要和辅助颜色。 总共提供了42个图像模板：27个符号图标和15个多边形填充模式。

可以使用函数将图像模板添加到地图图像 sprite 资源 `map.imageSprite.createFromTemplate` 。 此函数允许传入多达五个参数;

```javascript
createFromTemplate(id: string, templateName: string, color?: string, secondaryColor?: string, scale?: number): Promise<void>
```

`id`是您创建的唯一标识符。 将 `id` 图像添加到地图图像 sprite 后，会将其分配给映像。 在层中使用此标识符来指定要呈现的图像资源。 `templateName`指定要使用的图像模板。 `color`选项设置图像的主要颜色， `secondaryColor` 选项设置图像的辅助颜色。 `scale`选项会在将图像模板应用于图像子画面之前对其进行缩放。 当图像应用于图像子画面时，它将转换为 PNG。 若要确保清晰渲染，最好在将图像模板添加到子画面之前向上向上扩展，而不是在层中进行缩放。

此函数以异步方式将图像加载到图像 sprite。 因此，它将返回可等待此函数完成的承诺。

下面的代码演示如何从一个内置模板创建一个图像，并将其用于符号层。

```javascript
map.imageSprite.createFromTemplate('myTemplatedIcon', 'marker-flat', 'teal', '#fff').then(function () {

    //Add a symbol layer that uses the custom created icon.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'myTemplatedIcon'
        }
    }));
});
```

## <a name="use-an-image-template-with-a-symbol-layer"></a>使用带有符号层的图像模板

将图像模板加载到地图图像 sprite 后，可通过在的选项中引用图像资源 ID，将其呈现为符号层中的符号 `image` `iconOptions` 。

下面的示例使用 `marker-flat` 带有蓝绿色主色和白色辅助颜色的图像模板呈现符号层。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="带有内置图标模板的符号层" src="//codepen.io/azuremaps/embed/VoQMPp/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
通过 CodePen 上的 () Azure Maps <a href='https://codepen.io/azuremaps/pen/VoQMPp/'>，查看带有内置图标模板的笔符号层</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> 。 <a href='https://codepen.io'>CodePen</a>
</iframe>

## <a name="use-an-image-template-along-a-lines-path"></a>使用沿行路径的图像模板

一旦将图像模板加载到地图图像下边缘，就可以通过将 LineString 添加到数据源，并使用带选项的符号层， `lineSpacing` 并通过在选项中引用图像资源的 ID，将其沿行的路径进行呈现 `image` `iconOptions` 。 

下面的示例在地图上呈现粉红色的线条，并使用 `car` 带有宝蓝蓝原色和白色辅助颜色的图像模板的符号层。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="带有内置图标模板的线条层" src="//codepen.io/azuremaps/embed/KOQvJe/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
通过 CodePen 上的 () Azure Maps <a href='https://codepen.io/azuremaps/pen/KOQvJe/'>，查看带有内置图标模板</a>的 "笔线层" <a href='https://codepen.io/azuremaps'>@azuremaps</a> 。 <a href='https://codepen.io'>CodePen</a>
</iframe>

> [!TIP]
> 如果图像模板指向上方，则将 `rotation` 符号层的图标选项设置为90（如果你想要将其指向与直线相同的方向。

## <a name="use-an-image-template-with-a-polygon-layer"></a>使用带有多边形层的图像模板

将图像模板加载到地图图像下边缘后，可通过在该图层的选项中引用图像资源 ID，将其呈现为多边形层中的填充模式 `fillPattern` 。

下面的示例使用 `dot` 带有红色主颜色和透明辅助颜色的图像模板呈现多边形层。  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="用内置图标模板填充多边形" src="//codepen.io/azuremaps/embed/WVMEmz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
请参阅 CodePen 上的 "<a href='https://codepen.io/azuremaps/pen/WVMEmz/'>使用内置图标模板的笔填充多边形</a>" Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 。 <a href='https://codepen.io'>CodePen</a>
</iframe>

> [!TIP]
> 通过设置填充模式的辅助颜色，可以更轻松地看到基础地图仍将提供主模式。 

## <a name="use-an-image-template-with-an-html-marker"></a>使用带有 HTML 标记的图像模板

可以使用函数检索图像模板，并将其用作 `altas.getImageTemplate` HTML 标记的内容。 模板可传递到 `htmlContent` 标记的选项，然后使用 `color` 、 `secondaryColor` 和选项自定义 `text` 。

下面的示例使用 `marker-arrow` 具有红色主色、粉红色辅助颜色和文本值 "00" 的模板。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="带有内置图标模板的 HTML 标记" src="//codepen.io/azuremaps/embed/EqQvzq/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
通过 CodePen 上的 () Azure Maps，查看<a href='https://codepen.io/azuremaps/pen/EqQvzq/'>带有内置图标模板</a>的 "笔 HTML 标记" <a href='https://codepen.io/azuremaps'>@azuremaps</a> 。 <a href='https://codepen.io'>CodePen</a>
</iframe>


> [!TIP]
> 还可以在地图外使用图像模板。 GetImageTemplate 函数返回具有占位符的 SVG 字符串; `{color}`, `{secondaryColor}`, `{scale}`, `{text}`. 替换这些占位符值以创建有效的 SVG 字符串。 然后，可以将 SVG 字符串直接添加到 HTML DOM，或将其转换为数据 URI，然后将其插入到 image 标记中。 例如：
> ```JavaScript
> //Retrieve an SVG template and replace the placeholder values.
> var svg = atlas.getImageTemplate('marker').replace(/{color}/, 'red').replace(/{secondaryColor}/, 'white').replace(/{text}/, '').replace(/{scale}/, 1);
>
> //Convert to data URI for use in image tags.
> var dataUri = 'data:image/svg+xml;base64,' + btoa(svg);
> ```

## <a name="create-custom-reusable-templates"></a>创建自定义的可重用模板

如果你的应用程序使用不同图标的相同图标，或者如果你要创建添加其他图像模板的模块，则可以从 Azure Maps web SDK 轻松添加和检索这些图标。 在命名空间上使用以下静态函数 `atlas` 。

| 名称 | 返回类型 | 说明 | 
|-|-|-|
| `addImageTemplate(templateName: string, template: string, override: boolean)` | | 向塔命名空间添加自定义 SVG 图像模板。 |
|  `getImageTemplate(templateName: string, scale?: number)`| 字符串 | 按名称检索 SVG 模板。 |
| `getAllImageTemplateNames()` | string[] |  按名称检索 SVG 模板。 |

SVG 图像模板支持以下占位符值：

| 占位符 | 说明 |
|-|-|
| `{color}` | 主要颜色。 | 
| `{secondaryColor}` | 辅助颜色。 | 
| `{scale}` | 将 SVG 图像添加到地图图像动画层后，会将其转换为 png 图像。 此占位符可用于在转换模板之前对其进行缩放，确保它清晰呈现。 | 
| `{text}` | 与 HTML 标记一起使用时呈现文本的位置。 |

下面的示例演示如何获取 SVG 模板，并将其作为可重用的图标模板添加到 Azure Maps web SDK。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="将自定义图标模板添加到阿特拉斯命名空间" src="//codepen.io/azuremaps/embed/NQyvEX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
请参阅 CodePen 上的 () ，<a href='https://codepen.io/azuremaps/pen/NQyvEX/'>以将自定义图标模板添加到阿特拉斯命名 Azure Maps 空间</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> 。 <a href='https://codepen.io'>CodePen</a>
</iframe>

## <a name="list-of-image-templates"></a>映像模板列表

下表列出了 Azure Maps web SDK 中当前可用的所有映像模板。 模板名称位于每个图像上方。 默认情况下，主要颜色为蓝色，辅助颜色为白色。 为了使辅助颜色在白色背景上更易于查看，以下图像将辅助颜色设置为黑色。

**符号图标模板**

:::row:::
   :::column span="":::
      marker
   :::column-end:::
   :::column span="":::
      标记-厚
   :::column-end:::
   :::column span="":::
      标记-圆圈
   :::column-end:::
   :::column span="":::
      标记-平
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![标记图标](./media/image-templates/marker.png)
   :::column-end:::
   :::column span="":::
      ![标记-厚图标](./media/image-templates/marker-thick.png)
   :::column-end:::
   :::column span="":::
      ![标记-圆圈图标](./media/image-templates/marker-circle.png)
   :::column-end:::
   :::column span="":::
      ![标记-平面图标](./media/image-templates/marker-flat.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      标记-正方形
   :::column-end:::
   :::column span="":::
      标记-方形-分类
   :::column-end:::
   :::column span="":::
      标记-箭头
   :::column-end:::
   :::column span="":::
      标记球-pin
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![标记-正方形图标](./media/image-templates/marker-square.png)
   :::column-end:::
   :::column span="":::
      ![标记-方形-群集图标](./media/image-templates/marker-square-cluster.png)
   :::column-end:::
   :::column span="":::
      ![标记-箭头图标](./media/image-templates/marker-arrow.png)
   :::column-end:::
   :::column span="":::
      ![标记球图标](./media/image-templates/marker-ball-pin.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      标记-圆形
   :::column-end:::
   :::column span="":::
      标记-方形舍入-分类
   :::column-end:::
   :::column span="":::
      flag
   :::column-end:::
   :::column span="":::
      标记-三角形
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![标记-圆形图标](./media/image-templates/marker-square-rounded.png)
   :::column-end:::
   :::column span="":::
      ![标记-方形舍入-分类图标](./media/image-templates/marker-square-rounded-cluster.png)
   :::column-end:::
   :::column span="":::
      ![标志图标](./media/image-templates/flag.png)
   :::column-end:::
   :::column span="":::
      ![标记-三角形图标](./media/image-templates/flag-triangle.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      三角形
   :::column-end:::
   :::column span="":::
      三角形-粗
   :::column-end:::
   :::column span="":::
      三角形-向上箭头
   :::column-end:::
   :::column span="":::
      三角形-向左箭头
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![三角形图标](./media/image-templates/triangle.png)
   :::column-end:::
   :::column span="":::
      ![三角形-厚图标](./media/image-templates/triangle-thick.png)
   :::column-end:::
   :::column span="":::
      ![三角形-向上箭头图标](./media/image-templates/triangle-arrow-up.png)
   :::column-end:::
   :::column span="":::
      ![三角形-向左箭头图标](./media/image-templates/triangle-arrow-left.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      六边形
   :::column-end:::
   :::column span="":::
      六边形-粗
   :::column-end:::
   :::column span="":::
      六边形-圆形
   :::column-end:::
   :::column span="":::
      六边形-粗
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![六边形图标](./media/image-templates/hexagon.png)
   :::column-end:::
   :::column span="":::
      ![六边形-厚图标](./media/image-templates/hexagon-thick.png)
   :::column-end:::
   :::column span="":::
      ![六边形圆形图标](./media/image-templates/hexagon-rounded.png)
   :::column-end:::
   :::column span="":::
      ![六边形-粗图标](./media/image-templates/hexagon-rounded-thick.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      固定
   :::column-end:::
   :::column span="":::
      固定
   :::column-end:::
   :::column span="":::
      圆角正方形
   :::column-end:::
   :::column span="":::
      圆方形-粗
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![“固定”图标](./media/image-templates/pin.png)
   :::column-end:::
   :::column span="":::
      ![固定线图标](./media/image-templates/pin-round.png)
   :::column-end:::
   :::column span="":::
      ![圆角图标](./media/image-templates/rounded-square.png)
   :::column-end:::
   :::column span="":::
      ![圆角方形-粗图标](./media/image-templates/rounded-square-thick.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      向上箭头
   :::column-end:::
   :::column span="":::
      向上箭头-细
   :::column-end:::
   :::column span="":::
      car
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![向上箭头图标](./media/image-templates/arrow-up.png)
   :::column-end:::
   :::column span="":::
      ![向上箭头-细图标](./media/image-templates/arrow-up-thin.png)
   :::column-end:::
   :::column span="":::
      ![汽车图标](./media/image-templates/car.png)
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::


**多边形填充图案模板**

:::row:::
   :::column span="":::
      检查
   :::column-end:::
   :::column span="":::
      检查器-旋转
   :::column-end:::
   :::column span="":::
      圆形
   :::column-end:::
   :::column span="":::
      圆圈-间距
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![检查图标](./media/image-templates/checker.png)
   :::column-end:::
   :::column span="":::
      ![检查器-旋转图标](./media/image-templates/checker-rotated.png)
   :::column-end:::
   :::column span="":::
      ![圆圈图标](./media/image-templates/circles.png)
   :::column-end:::
   :::column span="":::
      ![圆圈-间距图标](./media/image-templates/circles-spaced.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      斜上框线
   :::column-end:::
   :::column span="":::
      对角线-向下移动
   :::column-end:::
   :::column span="":::
      对角-条向上
   :::column-end:::
   :::column span="":::
      对角线-向下移动
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![斜上框线图标](./media/image-templates/diagonal-lines-up.png)
   :::column-end:::
   :::column span="":::
      ![斜向下箭头图标](./media/image-templates/diagonal-lines-down.png)
   :::column-end:::
   :::column span="":::
      ![对角-条带图标](./media/image-templates/diagonal-stripes-up.png)
   :::column-end:::
   :::column span="":::
      ![对角线-向下移动图标](./media/image-templates/diagonal-stripes-down.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      网格线
   :::column-end:::
   :::column span="":::
      旋转网格线
   :::column-end:::
   :::column span="":::
      旋转网格-条纹
   :::column-end:::
   :::column span="":::
      x 填充
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![网格线图标](./media/image-templates/grid-lines.png)
   :::column-end:::
   :::column span="":::
      ![旋转网格线图标](./media/image-templates/rotated-grid-lines.png)
   :::column-end:::
   :::column span="":::
      ![旋转网格线图标](./media/image-templates/rotated-grid-stripes.png)
   :::column-end:::
   :::column span="":::
      ![x 填充图标](./media/image-templates/x-fill.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      之-字形
   :::column-end:::
   :::column span="":::
      之-字形-垂直
   :::column-end:::
   :::column span="":::
      点数
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![之-字形图标](./media/image-templates/zig-zag.png)
   :::column-end:::
   :::column span="":::
      ![之-字形图标](./media/image-templates/zig-zag-vertical.png)
   :::column-end:::
   :::column span="":::
      ![点图标](./media/image-templates/dots.png)
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::
<br>

**预加载的图像图标**

该映射使用 `marker` 、和模板将一组图标预加载到地图图像 sprite `pin` `pin-round` 。 下表列出了这些图标名称及其颜色值。

| 图标名称 | color | secondaryColor |
|-----------|-------|----------------|
| `marker-black` | `#231f20` | `#ffffff` |
| `marker-blue` | `#1a73aa` | `#ffffff` |
| `marker-darkblue` | `#003963` | `#ffffff` |
| `marker-red` | `#ef4c4c` | `#ffffff` |
| `marker-yellow` | `#f2c851` | `#ffffff` |
| `pin-blue` | `#2072b8` | `#ffffff` |
| `pin-darkblue` | `#003963` | `#ffffff` |
| `pin-red` | `#ef4c4c` | `#ffffff` |
| `pin-round-blue` | `#2072b8` | `#ffffff` |
| `pin-round-darkblue` | `#003963` | `#ffffff` |
| `pin-round-red` | `#ef4c4c` | `#ffffff` |


## <a name="try-it-now-tool"></a>立即试用工具

通过以下工具，你可以通过多种方式呈现不同的内置图像模板，并自定义主要和次要的颜色和缩放。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="图标模板选项" src="//codepen.io/azuremaps/embed/NQyaaO/?height=500&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
查看 CodePen 上的 () ，查看笔<a href='https://codepen.io/azuremaps/pen/NQyaaO/'>图标模板 Azure Maps 选项</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> 。 <a href='https://codepen.io'>CodePen</a>
</iframe>

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [ImageSpriteManager](/javascript/api/azure-maps-control/atlas.imagespritemanager)

> [!div class="nextstepaction"]
> [阿特拉斯命名空间](/javascript/api/azure-maps-control/atlas#functions
)

请参阅以下文章，了解可以使用图像模板的更多代码示例：

> [!div class="nextstepaction"]
> [添加符号层](map-add-pin.md)

> [!div class="nextstepaction"]
> [添加线条层](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [添加多边形层](map-add-shape.md)

> [!div class="nextstepaction"]
> [添加 HTML 标记](map-add-bubble-layer.md)