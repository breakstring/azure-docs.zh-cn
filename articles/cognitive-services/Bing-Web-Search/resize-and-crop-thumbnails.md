---
title: 重设图像缩略图大小并进行剪裁 - 必应 Web 搜索 API
titleSuffix: Azure Cognitive Services
description: 必应搜索 API 中的某些解答包括 Bing 提供的缩略图图像的 Url，可以调整大小和进行裁剪，还可以包含查询参数。
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 05A08B01-89FF-4781-AFE7-08DA92F25047
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: aahi
ms.openlocfilehash: a85c5b2333418367742678a529b69c95164eda53
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350477"
---
# <a name="resize-and-crop-thumbnail-images"></a>调整和裁剪缩略图图像

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 2020 年 10 月 30 日开始，必应搜索的任何新实例都需按照[此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程进行预配。
> 使用认知服务进行预配的必应搜索 API 将在未来三年或在企业协议结束前（以先发生者为准）得到支持。
> 有关迁移说明，请参阅[必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

必应搜索 API 中的某些解答包括 Bing 提供的缩略图图像的 Url，可以调整大小和进行裁剪，还可以包含查询参数。 例如：

`https://<host>/th?id=AMMS_92772df988...&w=110&h=73&rs=1&qlt=80&cdv=1&pid=16.1`

如果显示这些缩略图的一个子集，请提供一个选项以查看剩余图像。

> [!NOTE]
> 请确保裁剪和调整缩略图的大小会根据必应搜索 API [使用和显示要求](use-display-requirements.md)所要求的那样，提供遵循第三方权限的搜索方案。

## <a name="resize-a-thumbnail"></a>调整缩略图的大小 

若要调整缩略图的大小，必应建议仅在 `w` `h` 缩略图的 URL 中指定 (宽度) 或 (高度) 查询参数。 仅指定高度或宽度后，必应保持图像的原始方位。 以像素为单位指定宽和高。 

例如，如果原始缩略图为480x620：

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=480&h=620`

要减小其大小，请将 `w` 参数设置为新值 (例如 `336`) ，并删除 `h`  参数：

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=336`

如果仅指定缩略图的高度或宽度，则将保留图像的原始纵横比。 如果同时指定了这两个参数，并且不保留纵横比，必应将空白边距添加到图像的边框。

例如，如果在不进行裁剪的情况下将480x359 图像调整为200x200 大小，则完整宽度将包含图像，但高度将包含25个像素的空白，位于图像的顶部和底部。 如果图像是359x480，则左边框和右边框将包含白色填充。 如果裁剪图像，不会添加白色填充内容。  

下图显示缩略图的原始大小 (480x300)。  
  
![原始风景图](./media/resize-crop/bing-resize-crop-landscape.png)  
  
下图显示调整为 200x200 大小后的图像。 保持纵横比，并将上边框和下边框填充为白色 (其中包含的黑色边框用于显示边距) 。  
  
![调整大小后的风景图](./media/resize-crop/bing-resize-crop-landscape-resized.png)  

如果指定的尺寸大于图像的原始宽度和高度，必应将空白边距添加到左边框和上边框。  

## <a name="request-different-thumbnail-sizes"></a>请求不同的缩略图大小

若要请求其他缩略图大小，请从缩略图的 URL 中删除所有查询参数（ `id` 和 `pid` 参数除外）。 然后 `&w` ，将 (width) 或 `&h` (高度) 查询参数添加所需的图像大小（以像素为单位），但不能同时添加两者。 必应将保持图像的原始纵横比。 

若要将上述 URL 指定的图像宽度增加到165像素，请使用以下 URL：

`https://<host>/th?id=AMMS_92772df988...&w=165&pid=16.1`

如果请求的图像大于图像的原始大小，必应根据需要在图像周围添加白色空白。 例如，如果图像的原始大小为474x316，并将设置 `&w` 为500，必应会返回500x333 图像。 此图像沿上边缘和下边缘均为8.5 像素的白色填充，左侧边缘和右边缘有13像素的空白。

若要防止必应在所请求的大小大于图像的原始大小时添加白色填充，请将 `&p` 查询参数设置为0。 例如，如果在 `&p=0` 上述 URL 中包含参数，必应会返回474x316 图像而不是500x333 图像：

`https://<host>/th?id=AMMS_92772df988...&w=500&p=0&pid=16.1`

如果同时指定 `&w` 和 `&h` 查询参数，必应会保持图像的纵横比，并根据需要添加空白。 例如，如果图像的原始大小为474x316，并将宽度和高度参数设置为200x200 大小 (`&w=200&h=200`) ，必应返回在顶部和底部包含33像素的空白的图像。 如果包括 `&p` 查询参数，必应返回200x134 图像。

## <a name="crop-a-thumbnail"></a>裁剪缩略图 

若要裁剪图像，请包含 `c` (剪裁) query 参数。 可以使用下列值：
  
- `4`&mdash;盲比  
- `7`&mdash;智能比率  

### <a name="smart-ratio-cropping"></a>智能比率裁剪

如果通过将参数设置为 ") 请求智能比率裁剪 (`c` `7` ，必应会从其感兴趣的区域中心开始裁剪图像，同时保持图像的纵横比。 感兴趣区域是图像中被必应确定为包含最重要内容的区域。 下图是感兴趣区域的示例。  
  
![感兴趣区域](./media/resize-crop/bing-resize-crop-regionofinterest.png)

如果调整图像大小并请求智能比率裁剪，必应在保持纵横比的同时，将图像缩小到所请求的大小。 然后，必应根据调整了大小的尺寸裁剪该图像。 例如，如果调整了大小的宽度小于或等于高度，必应会将图像裁剪到感兴趣的区域的左侧和右侧。 否则，必应会将其裁剪到感兴趣的区域的顶部和底部。  
  
 
下图显示使用“智能比例”裁剪将图像缩成 200x200 大小的情形。 由于必应从左上角测量图像，因此会裁剪图像的下半部分。 
  
![裁剪成 200x200 大小的风景图](./media/resize-crop/bing-resize-crop-landscape200x200c7.png) 
  
下图显示使用“智能比例”裁剪将图像缩成 200x100 大小的情形。 由于必应从左上角测量图像，因此会裁剪图像的下半部分。 
   
![裁剪成 200x100 大小的风景图](./media/resize-crop/bing-resize-crop-landscape200x100c7.png)
  
下图显示使用“智能比例”裁剪将图像缩成 100x200 大小的情形。 因为必应从中心测量图像，所以图像的左右部分会被裁剪。
  
![裁剪成 100x200 大小的风景图](./media/resize-crop/bing-resize-crop-landscape100x200c7.png) 

如果 Bing 无法确定图像的感兴趣区域，则该服务将使用盲率裁剪。  

### <a name="blind-ratio-cropping"></a>盲比值裁剪

如果通过将参数设置为 ") 请求 (盲比值裁剪 `c` `4` ，必应使用以下规则来裁剪该图像。  
  
- 如果为 `(Original Image Width / Original Image Height) < (Requested Image Width / Requested Image Height)` ，则从左上角测量图像，并在底部进行裁剪。  
- 如果为 `(Original Image Width / Original Image Height) > (Requested Image Width / Requested Image Height)` ，则图像从中心开始测量，并向左和向右裁剪。  

下面是 225x300 大小的纵向图。  
  
![原始向日葵图](./media/resize-crop/bing-resize-crop-sunflower.png)
  
下图显示使用“盲比例”裁剪将图像缩成 200x200 大小的情形。 从左上角测量图像，导致裁剪图像底部的部分。  
  
![裁剪成 200x200 大小的向日葵图](./media/resize-crop/bing-resize-crop-sunflower200x200c4.png)
  
下图显示使用“盲比例”裁剪将图像缩成 200x100 大小的情形。 从左上角测量图像，导致裁剪图像底部的部分。  
  
![裁剪成 200x100 大小的向日葵图](./media/resize-crop/bing-resize-crop-sunflower200x100c4.png)
  
下图显示使用“盲比例”裁剪将图像缩成 100x200 大小的情形。 图像从中心进行测量，因此会裁剪其左侧和右侧。  
  
![裁剪成 100x200 大小的向日葵图](./media/resize-crop/bing-resize-crop-sunflower100x200c4.png)

## <a name="next-steps"></a>后续步骤

* [什么是必应搜索 API？](bing-api-comparison.md)
* [必应搜索 API 使用和显示要求](use-display-requirements.md)