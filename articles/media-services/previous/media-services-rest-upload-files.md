---
title: 使用 REST 将文件上传到 Azure 媒体服务帐户 | Microsoft Docs
description: 了解如何通过使用 REST 创建和上传资产将媒体内容加入媒体服务。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 49863bec4cbd367b6b309ef5a79e7287cb53ee5b
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93042974"
---
# <a name="upload-files-into-a-media-services-account-using-rest"></a>使用 REST 将文件上传到媒体服务帐户

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> * [Portal](media-services-portal-upload-files.md)
> 

在媒体服务中，可以将数字文件上传到资产中。 [资产](/rest/api/media/operations/asset)实体可以包含视频、音频、图像、缩略图集合、文本轨道和隐藏式字幕文件（以及这些文件的相关元数据。）将文件上传到资产后，相关内容即安全地存储在云中供后续处理和流式处理。 

本教程介绍如何上传文件及其关联的其他操作：

> [!div class="checklist"]
> * 为所有上传操作设置 Postman
> * 连接到媒体服务 
> * 创建具有写入权限的访问策略。
> * 创建资产
> * 创建 SAS 定位符并创建上传 URL
> * 使用上传 URL 将文件上传到 Blob 存储
> * 在资产中为上传的媒体文件创建元数据

## <a name="prerequisites"></a>先决条件

- 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [使用 Azure 门户创建 Azure 媒体服务帐户](media-services-portal-create-account.md)。
- 查看[通过 ADD 身份验证访问 Azure 媒体服务 API 概述](media-services-use-aad-auth-to-access-ams-api.md)一文。
- 另外，有关详细信息，请参阅[通过 Azure AD 身份验证使用 REST 访问媒体服务 API](./media-services-rest-connect-with-aad.md) 一文。
- 根据 [为媒体服务 REST API 调用配置 Postman](media-rest-apis-with-postman.md) 中所述配置 **Postman** 。

## <a name="considerations"></a>注意事项

使用媒体服务 REST API 时，需注意以下事项：
 
* 访问媒体服务 REST API 访问实体时，必须在 HTTP 请求中设置特定标头字段和值。 有关详细信息，请参阅[媒体服务 REST API 开发的设置](media-services-rest-how-to-use.md)。 <br/>本教程中使用的 Postman 集合负责设置所有必要的标头。
* 为流式处理 (内容生成 Url 时，媒体服务将使用 IAssetFile.Name 属性的值，例如 http：//{AMSAccount} windowsazure.mediaservices/{GUID}/{IAssetFile}/streamingParameters。 ) ，则不允许使用百分号编码。 **Name** 属性的值不能含有任何以下 [百分号编码保留字符](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)：!*'();:@&=+$,/?%#[]"。 此外，文件扩展名中只能含有一个“.”。
* 名称长度不应超过 260 个字符。
* 在媒体服务中进行处理时，系统支持的最大文件大小存在限制。 有关文件大小限制的详细信息，请参阅[此文](media-services-quotas-and-limitations.md)。

## <a name="set-up-postman"></a>设置 Postman

有关如何为本教程设置 Postman 的步骤，请参阅[配置 Postman](media-rest-apis-with-postman.md)。

## <a name="connect-to-media-services"></a>连接到媒体服务

1. 将连接值添加到环境。 

    在开始执行[集合](postman-collection.md)中定义的操作之前，需要手动设置 MediaServices [环境](postman-environment.md)中的某些变量。 

    若要获取前五个变量的值，请参阅[通过 Azure AD 身份验证访问 Azure 媒体服务 API](media-services-use-aad-auth-to-access-ams-api.md)。 

    ![屏幕截图，显示从右上方选择的 "齿轮" 图标和从 "管理环境" 选项卡中选择的前五个变量。](./media/media-services-rest-upload-files/postman-import-env.png)
2. 指定 **MediaFileName** 环境变量的值。

    指定要上传的媒体的文件名。 在此示例中，我们要上传 BigBuckBunny.mp4。 
3. 检查 **AzureMediaServices.postman_environment.json** 文件。 可以看到，集合中的几乎所有操作都执行“test”脚本。 这些脚本采用响应返回的某些值，并设置相应的环境变量。

    例如，第一个操作获取访问令牌，并在用于所有其他操作的 **AccessToken** 环境变量中设置该令牌。

    ```    
    "listen": "test",
    "script": {
        "type": "text/javascript",
        "exec": [
            "var json = JSON.parse(responseBody);",
            "postman.setEnvironmentVariable(\"AccessToken\", json.access_token);"
        ]
    }
    ```
4. 在“Postman”窗口的左侧，单击“1.    获取 AAD 身份验证令牌” -> “获取服务主体的 Azure AD 令牌”。

    URL 部分填入 AzureADSTSEndpoint  环境变量（在本教程的前面部分，设置了支持集合的环境变量的值）。

    ![显示 "1" 的屏幕截图。 获取 D 身份验证令牌-从 "Postman" 窗口中选择 "获取服务主体的 Azure A D 令牌"，并选择 "发送" 按钮。](./media/media-services-rest-upload-files/postment-get-token.png)

5. 按“发送”。

    可以看到包含“access_token”的响应。 “test”脚本采用此值，并设置 **AccessToken** 环境变量（如前所述）。 如果检查环境变量的话，将会看到，此变量现在包含剩余操作中使用的访问令牌（持有者令牌）值。 

    如果该令牌已过期，请再次执行“获取服务主体的 Azure AD 令牌”步骤。 

## <a name="create-an-access-policy-with-write-permission"></a>创建具有写入权限的访问策略。

### <a name="overview"></a>概述 

>[!NOTE]
>不同 AMS 策略的策略限制为 1,000,000 个（例如，对于定位器策略或 ContentKeyAuthorizationPolicy）。 如果始终使用相同的日期/访问权限，则应使用相同的策略 ID，例如，用于要长期就地保留的定位符的策略（非上传策略）。 有关详细信息，请参阅[本文](media-services-dotnet-manage-entities.md#limit-access-policies)。

将任何文件上传到 BLOB 存储之前，请设置用于对资产执行写入操作的访问策略权限。 为此，请向 AccessPolicy 实体集发送一个 HTTP POST 请求。 请在执行创建操作时定义 DurationInMinutes 值，否则会在响应中收到 500 内部服务器错误消息。 有关 AccessPolicies 的详细信息，请参阅 [AccessPolicy](/rest/api/media/operations/accesspolicy)。

### <a name="create-an-access-policy"></a>创建访问策略

1. 选择 " **AccessPolicy**  ->  **Create AccessPolicy for 上传** "。
2. 按“发送”。

    ![屏幕截图，显示从左侧菜单中选择 "AccessPolicy-Create AccessPolicy for 上传"，并选择 "发送" 按钮。](./media/media-services-rest-upload-files/postman-access-policy.png)

    “test”脚本将获取 AccessPolicy ID，并设置相应的环境变量。

## <a name="create-an-asset"></a>创建资产

### <a name="overview"></a>概述

[资产](/rest/api/media/operations/asset)是媒体服务中多种类型或对象集的容器，包括视频、音频、图像、缩略图集合、文本轨道和隐藏式字幕文件。 在 REST API 中，创建资产需要向媒体服务发送 POST 请求，并将任何有关资产的属性信息放入请求正文中。

在创建资产时可以添加的属性之一是 **Options** 。 可以指定以下加密选项之一： **None** （默认值，不使用加密）、 **StorageEncrypted** （适用于已使用客户端存储加密预先加密的内容）、 **CommonEncryptionProtected** 或 **EnvelopeEncryptionProtected** 。 如果资产已加密，则需要配置传送策略。 有关详细信息，请参阅 [配置资产传送策略](media-services-rest-configure-asset-delivery-policy.md)。

如果资产要使用加密，必须按以下文章中所述创建 **ContentKey** 并将其链接到资产： [如何创建 ContentKey](media-services-rest-create-contentkey.md)。 将文件上传到资产后，需要使用加密 **资产** 期间获取的值更新 **AssetFile** 实体上的加密属性。 使用 **MERGE** HTTP 请求完成此操作。 

本示例将创建一个不加密的资产。 

### <a name="create-an-asset"></a>创建资产

1. 选择 **资产** "  ->  **创建资产** "。
2. 按“发送”。

    ![屏幕截图，显示 "集合" 菜单中选择的 "资产-创建资产"，并选择 "发送" 按钮。](./media/media-services-rest-upload-files/postman-create-asset.png)

    “test”脚本将获取 Asset ID，并设置相应的环境变量。

## <a name="create-a-sas-locator-and-create-the-upload-url"></a>创建 SAS 定位符并创建上传 URL

### <a name="overview"></a>概述

设置 AccessPolicy 和定位符后，即可使用 Azure 存储 REST API 将具体的文件上传到 Azure BLOB 存储容器。 必须以块 blob 形式上传文件。 Azure 媒体服务不支持页 blob。  

有关使用 Azure 存储 Blob 的详细信息，请参阅 [Blob 服务 REST API](/rest/api/storageservices/blob-service-rest-api)。

若要检索实际上传 URL，请创建一个 SAS 定位符（参阅下文）。 定位符为希望访问资产中文件的客户端定义连接终结点的开始时间和类型。 可以为给定 AccessPolicy 和资产对创建多个定位符实体，以处理不同的客户端请求和需求。 这其中的任一定位符都可使用 AccessPolicy 的 StartTime 值和 DurationInMinutes 值来确定可以使用某 URL 的时间长度。 有关详细信息，请参阅[定位符](/rest/api/media/operations/locator)。

SAS URL 采用以下格式：

`{https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}`

### <a name="considerations"></a>注意事项

请注意以下事项：

* 一项给定的资产一次最多只能与五个唯一的定位符相关联。 有关详细信息，请参阅定位符。
* 如果需要立即上传文件，应将 StartTime 值设置为当前时间前五分钟。 这是因为客户端计算机与媒体服务之间可能存在时钟偏差。 StartTime 值还必须采用以下日期/时间格式：YYYY-MM-DDTHH:mm:ssZ（例如“2014-05-23T17:53:50Z”）。    
* 定位符从创建到可用可能会有 30-40 秒的延迟。

### <a name="create-a-sas-locator"></a>创建 SAS 定位符

1. 选择 " **定位符**  ->  **创建 SAS 定位符** "。
2. 按“发送”。

    “test”脚本将会根据指定的媒体文件名和 SAS 定位符信息创建“上传 URL”，并设置相应的环境变量。

    ![屏幕截图，显示 "集合" 菜单中选择的 "定位符-创建 s 定位符"，并选择 "发送" 按钮。](./media/media-services-rest-upload-files/postman-create-sas-locator.png)

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>使用上传 URL 将文件上传到 Blob 存储

### <a name="overview"></a>概述

创建上传 URL 后，需要直接使用 Azure Blob API 编写一些代码，用于将文件上传到 SAS 容器。 有关详细信息，请参阅下列文章：

- [使用 Azure 存储 REST API](../../storage/common/storage-rest-api-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [放置 Blob](/rest/api/storageservices/put-blob)
- [将 Blob 上传到 Blob 存储](/previous-versions/azure/storage/storage-use-azcopy#upload-blobs-to-blob-storage)

### <a name="upload-a-file-with-postman"></a>使用 Postman 上传文件

例如，我们使用 Postman 上传一个 .mp4 小文件。 通过 Postman 上传二进制文件可能存在文件大小限制。

上传请求不是 **AzureMedia** 集合的一部分。 

创建并设置新请求：
1. 按 **+** ，以创建新的请求选项卡。
2. 选择“PUT”操作并在 URL 中粘贴 
2. 将“授权”选项卡保留原样（不要将其设置为“持有者令牌”）。 
3. 在“标头”选项卡中，为“密钥”指定“x-ms-blob-type”，为“值”指定“BlockBlob”。 
2. 在“正文”选项卡中，单击“二进制”。 
4. 选择具有 **MediaFileName** 环境变量中指定的名称的文件。
5. 按“发送”。

    ![显示选定 " (UploadU R L) " 选项卡的屏幕截图。](./media/media-services-rest-upload-files/postman-upload-file.png)

##  <a name="create-a-metadata-in-the-asset"></a>在资产中创建元数据

上传文件后，需要在已上传到与资产关联的 Blob 存储的媒体文件的资产中创建元数据。

1. 选择 **AssetFiles**  ->  **CreateFileInfos** 。
2. 按“发送”。

    ![上传文件](./media/media-services-rest-upload-files/postman-create-file-info.png)

随后应会上传该文件及其元数据。

## <a name="validate"></a>验证

若要验证是否已成功上传文件，可以查询 [AssetFile](/rest/api/media/operations/assetfile)，并将 **ContentFileSize** （或其他详细信息）与希望在新资产中显示的信息进行比较。 

例如，以下 **GET** 操作将获取资产文件（在本例中为 BigBuckBunny.mp4 文件）的文件数据。 该查询使用前面设置的[环境变量](postman-environment.md)。

`{{RESTAPIEndpoint}}/Assets('{{LastAssetId}}')/Files`

响应中将会包含大小、名称和其他信息。

```console
"Id": "nb:cid:UUID:69e72ede-2886-4f2a-8d36-80a59da09913",
"Name": "BigBuckBunny.mp4",
"ContentFileSize": "3186542",
"ParentAssetId": "nb:cid:UUID:0b8f3b04-72fb-4f38-8e7b-d7dd78888938",
```
  
## <a name="next-steps"></a>后续步骤

现即可编码已上传的资产。 有关详细信息，请参阅[对资产进行编码](media-services-portal-encode.md)。

也可使用 Azure Functions 根据到达已配置容器的文件触发编码作业。 有关详细信息，请参阅[此示例](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ )。
