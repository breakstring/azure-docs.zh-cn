---
title: 绘图工具模块 |Microsoft Azure 映射
description: 本文介绍如何使用 Microsoft Azure Map Web SDK 设置绘图选项数据
author: anastasia-ms
ms.author: v-stharr
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 95a04d763fa5982181cc1c797bce969d9857ae4b
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2020
ms.locfileid: "92890626"
---
# <a name="use-the-drawing-tools-module"></a>使用绘图工具模块

Azure Maps Web SDK 提供了一个 " *绘图工具" 模块* 。 使用此模块，可以轻松地使用输入设备（如鼠标或触摸屏）绘制和编辑地图上的形状。 此模块的核心类是 [绘图管理器](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager#setoptions-drawingmanageroptions-)。 绘图管理器提供了在地图上绘制和编辑形状所需的所有功能。 它可直接使用，并与自定义工具栏 UI 集成。 还可以使用内置的 "绘图" [工具栏](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar) 类。 

## <a name="loading-the-drawing-tools-module-in-a-webpage"></a>在网页中加载绘图工具模块

1. 创建一个新的 HTML 文件，并 [照常实现该映射](./how-to-use-map-control.md)。
2. 加载 Azure Maps 绘图工具模块。 可以通过以下两种方式之一加载它：
    - 使用 Azure Maps services 模块的全球托管的 Azure 内容分发网络版本。 在文件的元素中添加对 JavaScript 和 CSS 样式表的引用 `<head>` ：

        ```html
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.js"></script>
        ```

    - 或者，你可以使用 npm 包在本地加载 Azure Maps Web SDK 源代码的 "绘图工具" 模块， [然后将其](https://www.npmjs.com/package/azure-maps-drawing-tools) 托管到你的应用程序。 此程序包还包括了 TypeScript 定义。 使用此命令：
    
        > **npm 安装 azure 地图-绘图工具**
    
        然后，在该文件的元素中添加对 JavaScript 和 CSS 样式表的引用 `<head>` ：

         ```html
        <link rel="stylesheet" href="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.css" type="text/css" />
        <script src="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.js"></script>
         ```

## <a name="use-the-drawing-manager-directly"></a>直接使用绘图管理器

在应用程序中加载 "绘图工具" 模块后，可以使用 " [绘图管理器](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager#setoptions-drawingmanageroptions-)" 启用绘图和编辑功能。 可以在对其进行实例化或使用函数时，为绘图管理器指定选项 `drawingManager.setOptions()` 。

### <a name="set-the-drawing-mode"></a>设置绘制模式

下面的代码创建一个绘图管理器实例，并设置 "绘制 **模式** " 选项。 

```javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon"
});
```

下面的代码是一个完整运行的示例，演示如何设置绘图管理器的绘图模式。 单击地图，开始绘制多边形。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="绘制多边形" src="//codepen.io/azuremaps/embed/YzKVKRa/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅在 CodePen 上通过 Azure Maps () <a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>绘制多边形</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> 。 <a href='https://codepen.io'>CodePen</a>
</iframe>


### <a name="set-the-interaction-type"></a>设置交互类型

绘图管理器支持三种不同的方法来与地图进行交互以绘制形状。

* `click` -在单击鼠标或触摸时添加坐标。
* `freehand ` -在将鼠标或触摸拖动到地图上时添加坐标。 
* `hybrid` -在单击或拖动鼠标或触摸时添加坐标。

下面的代码启用多边形绘制模式并设置绘图管理器应遵循的绘图交互的类型 `freehand` 。 

```javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

 此代码示例实现了在地图上绘制多边形的功能。 只需按住鼠标左键并随意拖动即可。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="自由手写绘图" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Azure Maps (<a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>Free-hand drawing</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> 在<a href='https://codepen.io'>CodePen</a>上) ，请参阅绘图笔自由。
</iframe>


### <a name="customizing-drawing-options"></a>自定义绘图选项

前面的示例演示了如何在实例化绘图管理器时自定义绘图选项。 还可以使用函数设置 "绘图管理器" 选项 `drawingManager.setOptions()` 。 下面是一个工具，用于测试使用 setOptions 函数对绘图管理器的所有选项的自定义。

<br/>

<iframe height="685" title="自定义绘图管理器" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>请参阅 CodePen 上的 "通过 Azure Maps () <a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>获取形状数据</a>" <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>。
</iframe>


## <a name="next-steps"></a>后续步骤

了解如何使用绘图工具模块的其他功能：

> [!div class="nextstepaction"]
> [添加绘图工具栏](map-add-drawing-toolbar.md)

> [!div class="nextstepaction"]
> [获取形状数据](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [对绘图事件做出反应](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [交互类型和键盘快捷键](drawing-tools-interactions-keyboard-shortcuts.md)

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [Map](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [绘图管理器](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)

> [!div class="nextstepaction"]
> [绘图工具栏](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)