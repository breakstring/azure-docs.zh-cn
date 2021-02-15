---
title: 自定义“沉浸式阅读器”按钮
titleSuffix: Azure Cognitive Services
description: 本文将介绍如何自定义用于启动沉浸式阅读器的按钮。
services: cognitive-services
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metang
ms.openlocfilehash: b24a9dcb7a369708b1374f367186dc4d9a6ce475
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146869"
---
# <a name="how-to-customize-the-immersive-reader-button"></a>如何自定义沉浸式读者

本文演示如何自定义用于启动沉浸式阅读器的按钮，以满足应用程序的需求。

## <a name="add-the-immersive-reader-button"></a>添加沉浸式读者

沉浸式读者 SDK 为启动沉浸式阅读器的按钮提供默认样式。 使用 `immersive-reader-button` class 特性启用此样式设置。

```html
<div class='immersive-reader-button'></div>
```

## <a name="customize-the-button-style"></a>自定义按钮样式

使用 `data-button-style` 属性设置按钮的样式。 允许的值为 `icon` 、 `text` 和 `iconAndText` 。 默认值是 `icon`。

### <a name="icon-button"></a>图标按钮

```html
<div class='immersive-reader-button' data-button-style='icon'></div>
```

这会呈现以下内容：

![这是呈现的文本按钮](./media/button-icon.png)

### <a name="text-button"></a>文本按钮

```html
<div class='immersive-reader-button' data-button-style='text'></div>
```

这会呈现以下内容：

![这是呈现的沉浸式读取器按钮。](./media/button-text.png)

### <a name="icon-and-text-button"></a>图标和文本按钮

```html
<div class='immersive-reader-button' data-button-style='iconAndText'></div>
```

这会呈现以下内容：

![图标按钮](./media/button-icon-and-text.png)

## <a name="customize-the-button-text"></a>自定义按钮文本

使用特性为按钮配置语言和替换文本 `data-locale` 。 默认语言为英语。

```html
<div class='immersive-reader-button' data-locale='fr-FR'></div>
```

## <a name="customize-the-size-of-the-icon"></a>自定义图标大小

可使用属性配置沉浸式读者图标大小 `data-icon-px-size` 。 这会设置图标的大小（以像素为单位）。 默认大小为20px。

```html
<div class='immersive-reader-button' data-icon-px-size='50'></div>
```

## <a name="next-steps"></a>后续步骤

* 浏览[沉浸式阅读器 SDK 参考](./reference.md)