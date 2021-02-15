---
title: 媒体服务 v2 到 v3 的迁移设置
description: 本文将帮助你设置用于从 Azure 媒体服务 v2 迁移到 v3 的环境。
services: media-services
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: azure 媒体服务，迁移，流，广播，实时，SDK
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 1/14/2021
ms.author: inhenkel
ms.openlocfilehash: 1f9e0816a3533008a01f26ca7c0e712abfa23e8e
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946239"
---
# <a name="step-3---set-up-to-migrate-to-the-v3-rest-api-or-client-sdk"></a>步骤 3-设置以迁移到 V3 REST API 或客户端 SDK

![迁移指南徽标](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![迁移步骤2](./media/migration-guide/steps-3.svg)

下面描述了将环境设置为使用媒体服务 V3 API 需要执行的步骤。

## <a name="sdk-model"></a>SDK 模型

在 V2 API 中，有两个不同的客户端 Sdk，一个用于管理 API，它允许以编程方式创建帐户，另一个用于资源管理。

以前，开发人员将使用 Azure 服务主体客户端 ID 和客户端机密，以及其 AMS 帐户的特定 V2 REST API 终结点。

V3 API 是基于 (ARM) 的 Azure 资源管理。 它使用 Azure Active Directory (Azure AD) 服务主体 Id 和密钥来连接到 API。 开发人员需要创建服务主体或托管标识以连接到 API。 在 V3 API 中，API 使用标准 ARM 终结点，对其他所有 Azure 服务使用类似且一致的模型。

以前使用2015-10-01 版本的 ARM 管理 API 来管理其 V2 帐户的客户应使用支持 V3 API 访问的2020-05-01 版本的 ARM 管理 API。

## <a name="create-a-new-media-services-account-for-testing"></a>创建新的媒体服务帐户进行测试

按照使用 Azure 门户 [设置环境](how-to-set-azure-subscription.md?tabs=portal) 的快速入门步骤进行操作。 选择 "API 访问" 和 "服务主体身份验证"，生成与此测试帐户一起使用的新 Azure AD 应用程序 ID 和机密。

[创建媒体服务帐户](create-account-howto.md?tabs=portal)。
[获取访问媒体服务 API](access-api-howto.md?tabs=portal)所用的凭据。

## <a name="download-client-sdk-of-your-choice-and-set-up-your-environment"></a>下载所选的客户端 SDK 并设置你的环境

- Sdk 适用于 [.net](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet&preserve-view=true)、.net Core、 [Node.js](https://docs.microsoft.com/javascript/api/overview/azure/mediaservices/management?view=azure-node-latest&preserve-view=true)、 [Python](https://docs.microsoft.com/python/api/overview/azure/mediaservices/management?view=azure-python&preserve-view=true)、 [Java](https://docs.microsoft.com/java/api/overview/azure/mediaservices/management?view=azure-java-stable&preserve-view=true)、 [中转](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/mediaservices/mgmt/2018-07-01/media)和 [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)。
- [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest&preserve-view=true)  用于简单脚本支持的集成。

> [!NOTE]
> 用于 V3 上的 Azure 媒体服务的社区 PHP SDK 不再可用。 如果在 V2 上使用 PHP，应直接在代码中迁移到 REST API。

## <a name="open-api-specifications"></a>打开 API 规范

- V3 基于统一的 API 图面，它公开了在 Azure 资源管理器上构建的管理和操作功能。 Azure 资源管理器模板可用于创建和部署转换、流式处理终结点、实时事件等。

- [OpenAPI 规范](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01) (以前称为 Swagger) 文档说明了所有服务组件的架构。

- 所有客户端 Sdk 都是从 GitHub 上发布的开放 API 规范派生和生成的。 在本文发布时，最新的开放 API 规范将在 GitHub 中公开维护。 2020-05-01 版本是 [最新的稳定版本](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01)。

## <a name="rest"></a>[REST](#tab/rest)

将 [Postman](https://docs.microsoft.com/azure/media-services/latest/media-rest-apis-with-postman) 用于媒体服务 v3 REST API 调用。
阅读 [REST API 引用页面](https://docs.microsoft.com/rest/api/media/)。

应在 Postman 集合中使用2020-05-01 版本字符串。

## <a name="net"></a>[.NET](#tab/net)

若要设置环境，请阅读 [通过 .Net 连接到媒体服务 V3 API](configure-connect-dotnet-howto.md) 一文。

如果只是想要使用 PackageManager 安装最新的 SDK，请使用以下命令：

```Install-Package Microsoft.Azure.Management.Media```

或者，若要使用 .NET CLI 安装最新的 SDK，请使用以下命令：

```dotnet add package Microsoft.Azure.Management.Media```

此外， [Azure 示例/媒体服务-v3-dotnet](https://github.com/Azure-Samples/media-services-v3-dotnet) 中提供了适用于各种方案的完整 .net 示例。 此存储库中的项目演示如何使用 v3 版本实现不同的 Azure 媒体服务方案。

### <a name="get-started-adjusting-your-code"></a>开始调整代码

在基本代码中搜索使用实例， `CloudMediaContext` 以开始升级到 V3 API。

下面的代码演示之前如何使用 v2 .NET SDK 访问 v2 API。 开发人员首先创建一个 `CloudMediaContext` 实例，并使用对象创建一个实例 `AzureAdTokenCredentials` 。

```dotnet

class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
        try
        {
            AzureAdTokenCredentials tokenCredentials = 
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

```

## <a name="java"></a>[Java](#tab/java)

若要设置环境，请阅读 [通过 Java 连接到媒体服务 V3 API](configure-connect-java-howto.md) 一文。

## <a name="python"></a>[Python](#tab/python)

若要设置环境，请参阅 [连接到 Azure 媒体服务 V3 API-Python](configure-connect-python-howto.md) 。

## <a name="nodejs"></a>[Node.js](#tab/nodejs)

若要设置环境，请参阅 [连接到 Azure 媒体服务 V3 API Node.js](configure-connect-nodejs-howto.md) 一文。

## <a name="ruby"></a>[Ruby](#tab/ruby)

- 获取 GitHub 上的 [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md) SDK。

## <a name="go"></a>[Go](#tab/go)

下载 " [前往](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/mediaservices/mgmt/2018-07-01/media) SDK"。

---

## <a name="next-steps"></a>后续步骤

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]