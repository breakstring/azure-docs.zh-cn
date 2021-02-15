---
title: 连接到 Azure 媒体服务 v3 API - Python
description: 本文演示如何使用 Python 连接到媒体服务 v3 API。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/18/2020
ms.author: inhenkel
ms.custom: devx-track-python
ms.openlocfilehash: 76df8baaf170b05762b93478a496eb1e9ed802d5
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2020
ms.locfileid: "94916740"
---
# <a name="connect-to-media-services-v3-api---python"></a>连接到媒体服务 v3 API - Python

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

本文介绍如何使用服务主体登录方法连接到 Azure 媒体服务 v3 Python SDK。

## <a name="prerequisites"></a>必备条件

- 从 [python.org](https://www.python.org/downloads/) 下载 Python
- 请确保设置 `PATH` 环境变量
- [创建媒体服务帐户](./create-account-howto.md)。 请务必记住资源组名称和媒体服务帐户名称。
- 按照[访问 API](./access-api-howto.md) 主题中的步骤进行操作。 记下订阅 ID、应用程序 ID（客户端 ID）、身份验证密钥（机密）和租户 ID，以便在后面的步骤中使用。

> [!IMPORTANT]
> 查看[命名约定](media-services-apis-overview.md#naming-conventions)。

## <a name="install-the-modules"></a>安装模块

若要通过 Python 使用 Azure 媒体服务，需要安装以下模块。

* `azure-mgmt-resource` 模块，其中包括用于 Active Directory 的 Azure 模块。
* `azure-mgmt-media` 模块，其中包括媒体服务实体。

    请确保获取 [最新版本的适用于 Python 的媒体服务 SDK](https://pypi.org/project/azure-mgmt-media/)。

打开命令行工具并使用以下命令安装这些模块。

```
pip3 install azure-mgmt-resource
pip3 install azure-mgmt-media==3.0.0
```

## <a name="connect-to-the-python-client"></a>连接到 Python 客户端

1. 创建扩展名为 `.py` 的文件
1. 在偏好的编辑器中打开该文件
1. 将下面的代码添加到该文件。 此代码将导入所需的模块，并创建连接到媒体服务所需的 Active Directory 凭据对象。

      将变量的值设置为从[访问 API](./access-api-howto.md) 中获得的值

      ```
      import adal
      from msrestazure.azure_active_directory import AdalAuthentication
      from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD
      from azure.mgmt.media import AzureMediaServices
      from azure.mgmt.media.models import MediaService

      RESOURCE = 'https://management.core.windows.net/'
      # Tenant ID for your Azure Subscription
      TENANT_ID = '00000000-0000-0000-000000000000'
      # Your Service Principal App ID
      CLIENT = '00000000-0000-0000-000000000000'
      # Your Service Principal Password
      KEY = '00000000-0000-0000-000000000000'
      # Your Azure Subscription ID
      SUBSCRIPTION_ID = '00000000-0000-0000-000000000000'
      # Your Azure Media Service (AMS) Account Name
      ACCOUNT_NAME = 'amsv3account'
      # Your Resource Group Name
      RESOUCE_GROUP_NAME = 'AMSv3ResourceGroup'

      LOGIN_ENDPOINT = AZURE_PUBLIC_CLOUD.endpoints.active_directory
      RESOURCE = AZURE_PUBLIC_CLOUD.endpoints.active_directory_resource_id

      context = adal.AuthenticationContext(LOGIN_ENDPOINT + '/' + TENANT_ID)
      credentials = AdalAuthentication(
          context.acquire_token_with_client_credentials,
          RESOURCE,
          CLIENT,
          KEY
      )

      # The AMS Client
      # You can now use this object to perform different operations to your AMS account.
      client = AzureMediaServices(credentials, SUBSCRIPTION_ID)

      print("signed in")

      # Now that you are authenticated, you can manipulate the entities.
      # For example, list assets in your AMS account
      print (client.assets.list(RESOUCE_GROUP_NAME, ACCOUNT_NAME).get(0))
      ```

1. 运行该文件

## <a name="next-steps"></a>后续步骤

- 使用 [Python SDK](https://aka.ms/ams-v3-python-sdk)。
- 查看媒体服务 [Python 参考](/python/api/overview/azure/mediaservices/management)文档。
