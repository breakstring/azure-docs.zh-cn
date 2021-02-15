---
title: 受保护的 Web API 应用注册 | Azure
titleSuffix: Microsoft identity platform
description: 了解构建受保护 Web API 的方法以及注册应用所需的信息。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5d93df0b6d59e013c22e138942ab4651784421ae
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584374"
---
# <a name="protected-web-api-app-registration"></a>受保护的 Web API：应用注册

本文介绍适用于受保护 Web API 的应用注册详细信息。

有关注册应用程序的常用步骤，请参阅[快速入门：将应用程序注册到 Microsoft 标识平台](quickstart-register-app.md)。

## <a name="accepted-token-version"></a>接受的令牌版本

Microsoft 标识平台可以颁发1.0 版令牌和 v2.0 令牌。 有关这些令牌的详细信息，请参阅[访问令牌](access-tokens.md)。

在 Azure 门户中创建 Web API 应用程序注册时，API 可以接受的令牌版本取决于你选择的“支持的帐户类型”。

- 如果 **支持的帐户类型** 的值是 **任何组织目录中的帐户和个人 Microsoft 帐户 (例如 Skype、Xbox、Outlook.com)**，则接受的令牌版本必须为 v2.0。
- 否则，已接受的令牌版本可以为 v1.0。

创建应用程序后，可以按以下步骤确定或更改接受的令牌版本：

1. 在 Azure 门户中选择你的应用，然后选择“清单”。
1. 在清单中找到 **accessTokenAcceptedVersion** 属性。
1. 该值向 Azure Active Directory (Azure AD) 指定 Web API 接受哪个令牌版本。
    - 如果值为 2，则 Web API 接受 v2.0 令牌。
    - 如果值为 **null**，则 Web API 接受 v1.0 令牌。
1. 如果更改了令牌版本，请选择“保存”。

> [!NOTE]
> Web API 会指定它所接受的令牌版本。 当客户端从 Microsoft 标识平台为 web API 请求令牌时，客户端将获取一个令牌，指示 web API 接受的令牌版本。

## <a name="no-redirect-uri"></a>无重定向 URI

Web API 不需注册重定向 URI，因为没有任何用户以交互方式登录。

## <a name="exposed-api"></a>公开的 API

特定于 Web API 的其他设置是公开的 API 和公开的范围或应用角色。

### <a name="application-id-uri-and-scopes"></a>应用程序 ID URI 和范围

范围通常采用 `resourceURI/scopeName` 格式。 对于 Microsoft Graph，范围具有快捷方式。 例如，`User.Read` 是 `https://graph.microsoft.com/user.read` 的快捷方式。

在应用注册过程中，定义以下参数：

- 资源 URI
- 一个或多个范围
- 一个或多个应用角色

默认情况下，应用程序注册门户建议使用资源 URI `api://{clientId}` 。 此 URI 是唯一的，但用户无法识别它。 如果更改 URI，请确保新值是唯一的。 应用程序注册门户将确保使用 [已配置的发布服务器域](howto-configure-publisher-domain.md)。

对于客户端应用程序，范围将显示为委托的权限，应用角色将显示为 Web API 的应用程序权限。 

范围还会出现在向应用用户显示的许可窗口中。 因此，请提供描述范围的相应字符串：

- 用户看到的内容。
- 可授予管理员许可的租户管理员看到的内容。

用户不能同意应用角色（因为它们由代表自己调用 Web API 的应用程序使用）。 租户管理员将需要同意你的 Web API 的客户端应用程序公开应用角色。 有关详细信息，请参阅[管理员同意](v2-admin-consent.md)

### <a name="exposing-delegated-permissions-scopes"></a>公开委托的权限（范围）

1. 在应用程序注册中选择“公开 API”。
1. 选择“添加范围”。 
1. 出现提示时，请选择“保存并继续”，接受建议的应用程序 ID URI (`api://{clientId}`)。
1. 指定以下值：
    - 选择“范围名称”并输入 **access_as_user**。
    - 选择“谁能许可”，并确保选择“管理员和用户”。 
    - 选择“管理员许可显示名称”，并输入“以用户身份访问 TodoListService”。 
    - 选择“管理员同意说明”，并输入“以用户身份访问 TodoListService Web API”。 
    - 选择“用户许可显示名称”，并输入“以用户身份访问 TodoListService”。 
    - 选择“用户同意说明”，并输入“以用户身份访问 TodoListService Web API”。 
    - 将“状态”值保留设置为“已启用”。 
 1. 选择“添加作用域”。

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>如果 Web API 由守护程序应用调用

本部分介绍如何注册受保护的 Web API，使守护程序应用可以安全地调用它。

- 由于守护程序应用不与用户交互，因此请仅声明并公开应用程序权限。 委托的权限没有作用。
- 租户管理员可以要求 Azure AD 仅将 Web API 令牌颁发给已注册的、可访问某个 API 的应用程序权限的应用程序。

#### <a name="exposing-application-permissions-app-roles"></a>公开应用程序权限（应用角色）

若要公开应用程序权限，请编辑清单。

1. 在应用程序的应用程序注册中选择“清单”。
1. 若要编辑清单，请找到 `appRoles` 设置并添加应用程序角色。 以下示例 JSON 块中提供了角色定义。
1. 仅将 `allowedMemberTypes` 保留设置为 `"Application"`。
1. 确保 `id` 是唯一的 GUID。
1. 确保 `displayName` 和 `value` 不包含空格。
1. 保存清单。

以下示例演示了 `appRoles` 的内容，其中 `id` 的值可以是任何唯一的 GUID。

```json
"appRoles": [
    {
    "allowedMemberTypes": [ "Application" ],
    "description": "Accesses the TodoListService-Cert as an application.",
    "displayName": "access_as_application",
    "id": "ccf784a6-fd0c-45f2-9c08-2f9d162a0628",
    "isEnabled": true,
    "lang": null,
    "origin": "Application",
    "value": "access_as_application"
    }
],
```

#### <a name="ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients"></a>确保 Azure AD 仅向允许的客户端颁发 Web API 的令牌

Web API 将检查应用角色。 此角色是软件开发人员公开应用程序权限的方式。 还可以将 Azure AD 配置为仅向租户管理员批准的、可访问 API 的应用颁发 API 令牌。

若要添加此增强的安全性：

1. 转到应用注册的应用“概述”页。
1. 在“本地目录中的托管应用程序”下，选择包含应用名称的链接。 此选项的标题可能已截断。 例如，你可能会看到“...中的托管应用程序”。

   > [!NOTE]
   >
   > 选择此链接后，会转到“企业应用程序概述”页。 此页与创建应用程序的租户中该应用程序的服务主体相关联。 可以使用浏览器中的“后退”按钮转到应用注册页。

1. 在“企业应用程序”页的“管理”部分选择“属性”页。 
1. 如果希望 Azure AD 仅允许从特定的客户端访问你的 Web API，请将“需要用户分配?”设置为“是”。 

   > [!IMPORTANT]
   >
   > 如果将“需要用户分配?”设置为“是”，当客户端请求 Web API 访问令牌时，Azure AD 会检查这些客户端的应用角色分配。  如果客户端未分配到任何应用角色，Azure AD 将返回错误消息“invalid_client:AADSTS501051:应用程序 \<application name\> 未分配给 \<web API\> 的角色”。
   >
   > 如果将“需要用户分配?”设置为“否”，当客户端请求 Web API 的访问令牌时，Azure AD 不会检查应用角色分配。  任何守护程序客户端（即，任何使用客户端凭据流的客户端）只需指定其受众，即可获取 API 的访问令牌。 任何应用程序都可以访问 API，而无需请求其权限。
   >
   > 但是，如上一部分中所述，Web API 始终可以验证应用程序是否具有租户管理员授权的适当角色。API 的验证方式是验证访问令牌是否包含角色声明，以及此声明的值是否正确。 在上述 JSON 示例中，值为 `access_as_application`。

1. 选择“保存” 。

## <a name="next-steps"></a>后续步骤

转到此方案中的下一篇文章：[应用代码配置](scenario-protected-web-api-app-configuration.md)。
