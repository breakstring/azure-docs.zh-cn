---
title: 连接到 Azure 媒体服务 v3 API - Node.js
description: 本文演示如何使用 Node.js 连接到媒体服务 v3 API。
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
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: c6ea238edd68413646dda59b22d1c0dc2557d57e
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2020
ms.locfileid: "94916825"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>连接到媒体服务 v3 API - Node.js

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

本文介绍如何使用服务主体登录方法连接到 Azure 媒体服务 v3 node.js SDK。

## <a name="prerequisites"></a>必备条件

- 安装 [Node.js](https://nodejs.org/en/download/)。
- [创建媒体服务帐户](./create-account-howto.md)。 请务必记住资源组名称和媒体服务帐户名称。

> [!IMPORTANT]
> 查看[命名约定](media-services-apis-overview.md#naming-conventions)。

## <a name="create-packagejson"></a>创建 package.json

1. 使用偏好的编辑器创建一个 package.json 文件。
1. 打开该文件并粘贴以下代码：

   请确保获取最新版本的 [适用于 JavaScript 的 AZUREMEDIASERVICES SDK](https://www.npmjs.com/package/@azure/arm-mediaservices)。

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.js",
  "dependencies": {
    "azure-arm-mediaservices": "^8.0.0",
    "azure-storage": "^2.8.0",
    "ms-rest": "^2.3.3",
    "ms-rest-azure": "^2.5.5"
  }
}
```

应指定以下包：

|程序包|说明|
|---|---|
|`azure-arm-mediaservices`|Azure 媒体服务 SDK。 <br/>为确保使用的是最新的 Azure 媒体服务包，请选中 [NPM install azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/)。|
|`azure-storage`|存储 SDK。 将文件上传到资产时使用。|
|`ms-rest-azure`| 用于登录。|

可以运行以下命令以确保使用的是最新的包：

```
npm install azure-arm-mediaservices
```

## <a name="connect-to-nodejs-client"></a>连接到 Node.js 客户端

1. 使用偏好的编辑器创建一个 .js 文件。
1. 打开该文件并粘贴以下代码。
1. 将“终结点配置”部分中的值设置为从[访问 API](./access-api-howto.md) 获得的值。

```js
'use strict';

const MediaServices = require('azure-arm-mediaservices');
const msRestAzure = require('ms-rest-azure');
const msRest = require('ms-rest');
const azureStorage = require('azure-storage');

// endpoint config
// make sure your URL values end with '/'
const armAadAudience = "";
const aadEndpoint = "";
const armEndpoint = "";
const subscriptionId = "";
const accountName = "";
const region = "";
const aadClientId = "";
const aadSecret = "";
const aadTenantId = "";
const resourceGroup = "";

let azureMediaServicesClient;

///////////////////////////////////////////
//     Entrypoint for sample script      //
///////////////////////////////////////////

msRestAzure.loginWithServicePrincipalSecret(aadClientId, aadSecret, aadTenantId, {
  environment: {
    activeDirectoryResourceId: armAadAudience,
    resourceManagerEndpointUrl: armEndpoint,
    activeDirectoryEndpointUrl: aadEndpoint
  }
}, async function(err, credentials, subscriptions) {
    if (err) return console.log(err);
    azureMediaServicesClient = new MediaServices(credentials, subscriptionId, armEndpoint, { noRetryPolicy: true });
    
    console.log("connected");

});
```

## <a name="run-your-app"></a>运行应用程序

打开命令提示符。 浏览到示例的目录，然后执行以下命令：

```
npm install 
node index.js
```

## <a name="see-also"></a>另请参阅

- [媒体服务概念](concepts-overview.md)
- [NPM install azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/)

## <a name="next-steps"></a>后续步骤

浏览媒体服务 [Node.js 参考](/javascript/api/overview/azure/mediaservices/management)文档并查看[示例](https://github.com/Azure-Samples/media-services-v3-node-tutorials)，了解如何将媒体服务 API 与 Node.js 配合使用。
