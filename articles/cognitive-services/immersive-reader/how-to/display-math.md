---
title: 在沉浸式阅读器中显示公式
titleSuffix: Azure Cognitive Services
description: 本文介绍如何在沉浸式阅读器中显示数学。
author: nitinme
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: nitinme
ms.custom: devx-track-js
ms.openlocfilehash: 75b748604e8faa502970ac71e3fec4fae2774c19
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91334508"
---
# <a name="how-to-display-math-in-the-immersive-reader"></a>如何在沉浸式阅读器中显示数学

沉浸式读者可以在数学标记语言 ([MathML](https://developer.mozilla.org/docs/Web/MathML)) 的形式提供时显示数学标记。
MIME 类型可通过沉浸式读取器 [区块](../reference.md#chunk)进行设置。 有关详细信息，请参阅 [支持的 MIME 类型](../reference.md#supported-mime-types) 。

## <a name="send-math-to-the-immersive-reader"></a>向沉浸式读者发送数学
为了向沉浸式读者发送数学，请提供包含 MathML 的区块，并将 MIME 类型设置为 ```application/mathml+xml``` ;

例如，如果内容为以下内容：

```html
<div id='ir-content'>
    <math xmlns='http://www.w3.org/1998/Math/MathML'>
        <mfrac>
            <mrow>
                <msup>
                    <mi>x</mi>
                    <mn>2</mn>
                </msup>
                <mo>+</mo>
                <mn>3</mn>
                <mi>x</mi>
                <mo>+</mo>
                <mn>2</mn>
            </mrow>
            <mrow>
                <mi>x</mi>
                <mo>−</mo>
                <mn>3</mn>
            </mrow>
        </mfrac>
        <mo>=</mo>
        <mn>4</mn>
    </math>
</div>
```

然后，你可以使用以下 JavaScript 显示你的内容。

```javascript
const data = {
    title: 'My Math',
    chunks: [{
        content: document.getElementById('ir-content').innerHTML.trim(),
        mimeType: 'application/mathml+xml'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

启动沉浸式读者时，应会看到：

![沉浸式阅读器中的数学](../media/how-tos/1-math.png)

## <a name="next-steps"></a>后续步骤

* 浏览[沉浸式阅读器 SDK ](https://github.com/microsoft/immersive-reader-sdk)和[沉浸式阅读器 SDK 参考](../reference.md)