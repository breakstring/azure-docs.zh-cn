---
title: 快速入门：创建首个必应自定义搜索实例
titleSuffix: Azure Cognitive Services
description: 参考本快速入门创建一个可以搜索所定义的域和网页的自定义必应实例。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 726a60d611abc392bc1a4629c5088ca4c6b703d9
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96338327"
---
# <a name="quickstart-create-your-first-bing-custom-search-instance"></a>快速入门：创建首个必应自定义搜索实例

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 2020 年 10 月 30 日开始，必应搜索的任何新实例都需按照[此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程进行预配。
> 使用认知服务进行预配的必应搜索 API 将在未来三年或在企业协议结束前（以先发生者为准）得到支持。
> 有关迁移说明，请参阅[必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

若要使用必应自定义搜索，需创建自定义搜索实例来定义 Web 视图或切片。 此实例包含你要搜索的公共域、网站和网页，以及你要做出的任何排名调整。 

若要创建实例，请使用[必应自定义搜索门户](https://customsearch.ai)。 

![必应自定义搜索门户的图片](media/blockedCustomSrch.png)

## <a name="prerequisites"></a>先决条件

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-a-custom-search-instance"></a>创建自定义搜索实例

要创建必应自定义搜索实例：

1. 在[必应自定义搜索门户](https://customsearch.ai)网页上单击“开始”，然后使用 Microsoft 帐户登录。

2. 单击“新建实例”并输入描述性的名称。 随时可以更改实例的名称。
 
3. 在“搜索体验”下的“活动”选项卡上，输入要纳入搜索范围的一个或多个网站的 URL。 

    > [!NOTE]
    > 必应自定义搜索实例只会返回已由必应编制索引的公共域和网页的结果。

4. 可以在必应自定义搜索门户的右侧输入查询和检查搜索实例返回的搜索结果。 如果未返回任何结果，请尝试输入不同的 URL。  

5. 单击“发布”将更改发布到生产环境，并更新实例的终结点。

6.  单击“终结点”下的“生产”选项卡，并复制 **自定义配置 ID**。 调用自定义搜索 API 时，需将此 ID 追加到调用中的 `customconfig=` 查询参数。


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [快速入门：调用必应自定义搜索终结点](./call-endpoint-csharp.md)