---
title: 必应见解的示例 - 必应视觉搜索
titleSuffix: Azure Cognitive Services
description: 本文包含必应视觉搜索如何在 Bing.com 上使用和显示图像见解的示例。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: scottwhi
ms.openlocfilehash: e0a3bdc7e68301c067b7e1ca80a3e708522e0df1
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96493349"
---
# <a name="examples-of-bing-insights-usage"></a>必应见解用法示例

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 2020 年 10 月 30 日开始，必应搜索的任何新实例都需按照[此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程进行预配。
> 使用认知服务进行预配的必应搜索 API 将在未来三年或在企业协议结束前（以先发生者为准）得到支持。
> 有关迁移说明，请参阅[必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

本文包含有关必应可能如何使用以及如何在 Bing.com 上显示图像见解的示例。

## <a name="pagesincluding-insight-example"></a>PagesIncluding 见解示例

下面显示了指向第一个网页的链接，并使用户可以展开和折叠包含该图像的其他网页的列表：

![包含扩展页面](./media/pages-including.PNG)

## <a name="shoppingsources-insight-example"></a>ShoppingSources 见解示例

下面显示了必应如何显示图像中显示的产品的购物源：

![购物源](./media/shopping-sources.PNG)

## <a name="visualsearch-insight-example"></a>VisualSearch 见解示例

下面显示了必应如何显示外观相似的图像 (参阅示例) 中的 **相关图像** ：

![视觉上相似的图像](./media/similar-images.PNG)

## <a name="recipes-insight-example"></a>Recipes 见解示例

以下介绍了必应如何为图像中的食物显示食谱。 该示例允许用户了解食谱可用：

![包含食谱和页面](./media/recipes-pages-including.PNG)

 并提供在用户展开列表时指向食谱的链接：

![包含展开的食谱页面](./media/expanded-recipes-pages-including.PNG)

## <a name="relatedsearches-insight-example"></a>RelatedSearches 见解示例

以下介绍了必应如何显示他人所进行的图像相关搜索。 如果用户单击图像，将针对相关查询转到 Bing.com/images 搜索结果页面。

![图像的相关搜索](./media/bordered-related-searches.PNG)

## <a name="entity-insight-example"></a>Entity 见解示例

以下介绍了必应如何显示图像中实体（人物、位置或事物）的相关信息。 如果用户单击实体链接，则会将该用户转到实体的 Bing.com 搜索结果页：

![图像中显示的实体](./media/entity.PNG)

## <a name="displaying-other-insights-that-the-user-might-explore"></a>显示用户可能浏览的其他见解

以下介绍了必应如何显示用户可能浏览的图像的相关信息。

![浏览有关图像的其他见解](./media/apple-pie-more-tags.PNG)

## <a name="bounding-boxes-and-hot-spots"></a>边框和热点

非默认标记包括标识图像（标记应用于的图像）中感兴趣区域的边框。 如果边框未标识整个图像，使用边框在图像上创建热点。 用户可以单击热点以获取与热点（或矩形）下内容相关的信息。 例如，如果图像是一个高品质的图像，则结果可能会包含图像中显示的附件)  (和边界框，如钱包、饰品、scarfs 等。 下面的示例演示了图像中显示的太阳镜的热点矩形：

![边框和热点](./media/click-to-search.PNG)

## <a name="next-steps"></a>后续步骤

若要开始处理第一个请求，请参阅快速入门：

* [C#](quickstarts/csharp.md)

* [Java](quickstarts/java.md)

* [node.js](quickstarts/nodejs.md)

* [Python](quickstarts/python.md)