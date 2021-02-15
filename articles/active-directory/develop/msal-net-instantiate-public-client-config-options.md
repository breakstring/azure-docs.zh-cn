---
title: " (MSAL.NET) 实例化公用客户端应用 |Microsoft"
titleSuffix: Microsoft identity platform
description: 了解如何通过适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET) 使用配置选项实例化公共客户端应用程序。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 3e2ffebf0b414d4b59178fe04fb109530365786b
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2021
ms.locfileid: "98064702"
---
# <a name="instantiate-a-public-client-application-with-configuration-options-using-msalnet"></a>通过 MSAL.NET 使用配置选项实例化公共客户端应用程序

本文介绍如何使用适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET) 实例化 [公用客户端应用程序](msal-client-applications.md) 。  应用程序使用设置文件中定义的配置选项进行实例化。

在初始化应用程序之前，首先需要[注册](quickstart-register-app.md)它，以便应用可以与 Microsoft 标识平台集成。 注册后，可能需要以下信息（可在 Azure 门户中找到）：

- 客户端 ID（表示 GUID 的字符串）
- 标识提供者 URL（为实例命名）和应用程序的登录受众。 这两个参数统称为颁发机构。
- 如果你仅在为组织编写业务线应用程序（也称为单租户应用程序），则为租户 ID。
- 对于 Web 应用，有时对于公共客户端应用（特别是当你的应用需要使用中转站时），还将需要设置 redirectUri，标识提供者将在其中使用安全令牌联系你的应用程序。


.NET Core 控制台应用程序可以具有以下 *appsettings.json* 配置文件：

```json
{
  "Authentication": {
    "AzureCloudInstance": "AzurePublic",
    "AadAuthorityAudience": "AzureAdMultipleOrgs",
    "ClientId": "ebe2ab4d-12b3-4446-8480-5c3828d04c50"
  },

  "WebAPI": {
    "MicrosoftGraphBaseEndpoint": "https://graph.microsoft.com"
  }
}
```

以下代码使用 .NET 配置框架读取此文件：

```csharp
public class SampleConfiguration
{
    /// <summary>
    /// Authentication options
    /// </summary>
    public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

    /// <summary>
    /// Base URL for Microsoft Graph (it varies depending on whether the application is ran
    /// in Microsoft Azure public clouds or national / sovereign clouds
    /// </summary>
    public string MicrosoftGraphBaseEndpoint { get; set; }

    /// <summary>
    /// Reads the configuration from a json file
    /// </summary>
    /// <param name="path">Path to the configuration json file</param>
    /// <returns>SampleConfiguration as read from the json file</returns>
    public static SampleConfiguration ReadFromJsonFile(string path)
    {
        // .NET configuration
        IConfigurationRoot Configuration;
        var builder = new ConfigurationBuilder()
          .SetBasePath(Directory.GetCurrentDirectory())
        .AddJsonFile(path);
        Configuration = builder.Build();

        // Read the auth and graph endpoint config
        SampleConfiguration config = new SampleConfiguration()
        {
            PublicClientApplicationOptions = new PublicClientApplicationOptions()
        };
        Configuration.Bind("Authentication", config.PublicClientApplicationOptions);
        config.MicrosoftGraphBaseEndpoint = Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
        return config;
    }
}
```

以下代码使用设置文件中的配置创建应用程序：

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```

