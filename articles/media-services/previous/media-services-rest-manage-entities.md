---
title: 使用 REST 管理媒体服务实体 | Microsoft Docs
description: 本文演示如何使用 REST API 管理媒体服务实体。
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 95262a32-0f2a-4286-b9e2-1a1ca6399b5b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 95124f101b1f14a70dabedd7d44077c5c1b6e99c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89264209"
---
# <a name="managing-media-services-entities-with-rest"></a>使用 REST 管理媒体服务实体

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [REST](media-services-rest-manage-entities.md)
> * [.NET](media-services-dotnet-manage-entities.md)
> 
> 

Microsoft Azure 媒体服务是一项以 OData v3 为基础的基于 REST 的服务。 你可以像在任何其他 OData 服务上一样添加、查询、更新和删除实体。 适用时，将标注例外情况。 有关 OData 的详细信息，请参阅[开放数据协议文档](https://www.odata.org/documentation/)。

本主题演示如何使用 REST 管理 Azure 媒体服务实体。

>[!NOTE]
> 自 2017 年 4 月 1 日起，即使记录总数低于最大配额，也会自动删除帐户中所有超过 90 天的作业记录，及其相关的任务记录。 例如，将于 2017 年 4 月 1 日自动删除帐户中 2016 年 12 月 31 日前的所有作业记录。 在需要时，可使用本主题中所述的代码存档作业/任务信息。

## <a name="considerations"></a>注意事项  

访问媒体服务中的实体时，必须在 HTTP 请求中设置特定标头字段和值。 有关详细信息，请参阅[媒体服务 REST API 开发的设置](media-services-rest-how-to-use.md)。

## <a name="connect-to-media-services"></a>连接到媒体服务

若要了解如何连接到 AMS API，请参阅[通过 Azure AD 身份验证访问 Azure 媒体服务 API](media-services-use-aad-auth-to-access-ams-api.md)。 

## <a name="adding-entities"></a>添加实体
媒体服务中的每个实体都会通过 POST HTTP 请求添加到实体集（如资产）中。

以下示例说明了如何创建 AccessPolicy。

```console
POST https://media.windows.net/API/AccessPolicies HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net
Content-Length: 74
Expect: 100-continue

{"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}
```

## <a name="querying-entities"></a>查询实体
查询和列出实体非常简单，仅涉及 GET HTTP 请求和可选的 OData 操作。
下面的示例会检索一个包含所有 MediaProcessor 实体的列表。

```console
GET https://media.windows.net/API/MediaProcessors HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net
```

也可以检索特定实体或与特定实体关联的所有实体集，如下列示例所示：

```console
GET https://media.windows.net/API/JobTemplates('nb:jtid:UUID:e81192f5-576f-b247-b781-70a790c20e7c') HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net

GET https://media.windows.net/API/JobTemplates('nb:jtid:UUID:e81192f5-576f-b247-b781-70a790c20e7c')/TaskTemplates HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net
```

下面的示例仅返回所有作业的 State 属性。

```console
GET https://media.windows.net/API/Jobs?$select=State HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net
```

以下示例返回名为“SampleTemplate”的所有 JobTemplate。

```console
GET https://media.windows.net/API/JobTemplates?$filter=startswith(Name,%20'SampleTemplate') HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net
```

> [!NOTE]
> 媒体服务不支持 $expand 操作以及“LINQ 注意事项（WCF 数据服务）”中所述的不受支持的 LINQ 方法。
> 
> 

## <a name="enumerating-through-large-collections-of-entities"></a>枚举大型实体集合
查询实体时，一次返回的实体数限制为 1000 个，因为公共 REST v2 将查询结果数限制为 1000 个。 使用 **skip** 和 **top** 枚举大型实体集合。 

以下示例说明如何使用 **skip** 和 **top** 来跳过前 2000 个作业并获取后 1000 个作业。  

```console
GET https://media.windows.net/api/Jobs()?$skip=2000&$top=1000 HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: media.windows.net
```

## <a name="updating-entities"></a>更新实体
根据实体类型及其所处的状态，可以通过 PATCH、PUT 或 MERGE HTTP 请求更新该实体上的属性。 有关这些操作的详细信息，请参阅 [PATCH/PUT/MERGE](/openspecs/windows_protocols/ms-odata/59d5abd3-7b12-490a-a0e2-9d9324b91893)。

下面的代码示例演示如何更新资产实体上的 Name 属性。

```console
MERGE https://media.windows.net/API/Assets('nb:cid:UUID:80782407-3f87-4e60-a43e-5e4454232f60') HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: media.windows.net
Content-Length: 21
Expect: 100-continue

{"Name" : "NewName" }
```

## <a name="deleting-entities"></a>删除实体
可以使用 DELETE HTTP 请求在媒体服务中删除实体。 删除实体的顺序可能很重要，具体视实体而定。 例如，资产等实体要求先撤消（或删除）引用该特定资产的所有定位符，再删除资产。

下面的示例演示如何删除用于将文件上传到 blob 存储的定位符。

```console
DELETE https://media.windows.net/API/Locators('nb:lid:UUID:76dcc8e8-4230-463d-97b0-ce25c41b5c8d') HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: media.windows.net
Content-Length: 0
```

## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
