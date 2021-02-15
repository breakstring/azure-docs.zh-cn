---
title: 将应用程序和 Api 迁移到 b2clogin.com
titleSuffix: Azure AD B2C
description: 了解在 Azure Active Directory B2C 的重定向 URL 中使用 b2clogin.com。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 20df5fc3a4d7c392be62df2b7778854d1e2e1cba
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109056"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>将 Azure Active Directory B2C 的重定向 URL 设置为 b2clogin.com

在 Azure Active Directory B2C (Azure AD B2C) 应用程序中设置用于注册和登录的标识提供者时，需要指定一个重定向 URL。 你不应在应用程序和 Api 中引用 *login.microsoftonline.com* ，以便通过 Azure AD B2C 对用户进行身份验证。 相反，请将 *b2clogin.com* 用于所有新的应用程序，并将现有应用程序从 *login.microsoftonline.com* 迁移到 *b2clogin.com*。

## <a name="deprecation-of-loginmicrosoftonlinecom"></a>弃用 login.microsoftonline.com

**2020 年 10 月更新：** 我们将为无法在最初宣布的弃用日期 2020 年 12 月 4 日实现弃用的租户延长宽限期。 现在，停用 login.microsoftonline.com 的时间不会早于 **14 月 14 2021 日。**

**背景**：2004年12月2019日，我们最初 [宣布](https://azure.microsoft.com/updates/b2c-deprecate-msol/) 在 2020 12 月12日 Azure AD B2C 的计划停用 login.microsoftonline.com 支持。 这提供了现有租户，一 (1) 年迁移到 b2clogin.com。 2019年12月之后创建的新租户将不接受来自 login.microsoftonline.com 的请求。 所有功能在 b2clogin.com 终结点上保持不变。

弃用 login.microsoftonline.com 不会影响 Azure Active Directory 租户。 该变更仅影响 Azure Active Directory B2C 租户。

## <a name="what-endpoints-does-this-apply-to"></a>所涉及的终结点
过渡到 b2clogin.com 仅适用于使用 Azure AD B2C 策略 (用户流或自定义策略的身份验证终结点，) 用于对用户进行身份验证。 这些终结点的 `<policy-name>` 参数指定 Azure AD B2C 应使用的策略。 [详细了解 Azure AD B2C 策略](technical-overview.md#identity-experiences-user-flows-or-custom-policies)。 

这些终结点可能如下所示：
- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/<b>\<policy-name\></b>/oauth2/v2.0/authorize</code>

- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/<b>\<policy-name\></b>/oauth2/v2.0/token</code>

另外，可将 `<policy-name>` 作为查询参数传递：
- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/oauth2/v2.0/authorize?<b>p=\<policy-name\></b></code>
- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/oauth2/v2.0/token?<b>p=\<policy-name\></b></code>

> [!IMPORTANT]
> 必须更新使用“策略”参数的终结点，以及[标识提供者重定向 URL](#change-identity-provider-redirect-urls)。

部分 Azure AD B2C 客户使用 Azure AD 企业租户的共享功能，例如 OAuth 2.0 客户端凭据授予流。 使用 Azure AD 的 login.microsoftonline.com 终结点（ *不包含策略参数*）可以访问这些功能。 __这些终结点不受影响__。

## <a name="benefits-of-b2clogincom"></a>B2clogin.com 的优点

使用 *b2clogin.com* 作为重定向 URL 时：

* Microsoft 服务在 cookie 标头中使用的空间就会减少。
* 重定向 URL 不再需要包含对 Microsoft 的引用。
* 自定义页面支持 JavaScript 客户端代码（目前为[预览版](javascript-and-page-layout.md)）。 由于安全限制，如果你使用 *login.microsoftonline.com*，则将从自定义页中删除 JavaScript 代码和 HTML 窗体元素。

## <a name="overview-of-required-changes"></a>所需的更改概述

若要将应用程序迁移到 *b2clogin.com*，可能需要进行一些修改：

* 将标识提供程序的应用程序中的重定向 URL 更改为引用 *b2clogin.com*。
* 更新 Azure AD B2C 应用程序，以便在其用户流和令牌终结点引用中使用 *b2clogin.com* 。 这可能包括更新对诸如 Microsoft 身份验证库 (MSAL) 之类的身份验证库的使用。
* 更新在 CORS 设置中为 [用户界面自定义](customize-ui-with-html.md)定义的任何 **允许的来源**。

旧版终结点可能如下所示：
- <b><code>https://login.microsoft.com/</b>\<tenant-name\>.onmicrosoft.com/\<policy-name\>/oauth2/v2.0/authorize</code>

更新后的相应终结点如下所示：
- <code><b>https://\<tenant-name\>.b2clogin.com/</b>\<tenant-name\>.onmicrosoft.com/\<policy-name\>/oauth2/v2.0/authorize</code>


## <a name="change-identity-provider-redirect-urls"></a>更改标识提供者重定向 URL

在已创建应用程序的每个标识提供者的网站上，更改所有受信任的 Url 以重定向到 `your-tenant-name.b2clogin.com` 而不是 *login.microsoftonline.com*。

你可以将两种格式用于 b2clogin.com 重定向 Url。 第一个选项使用租户 ID (GUID) 来替代租户域名，其优点是无需在 URL 中的任何位置显示“Microsoft”：

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-id}/oauth2/authresp
```

第二个选项使用 `your-tenant-name.onmicrosoft.com` 格式的租户域名。 例如：

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp
```

对于这两种格式：

* 将 `{your-tenant-name}` 替换为 Azure AD B2C 租户的名称。
* 删除 `/te`（如果 URL 中存在此参数）。

## <a name="update-your-applications-and-apis"></a>更新应用程序和 API

已启用 Azure AD B2C 的应用程序和 API 中的代码可能在多个位置引用了 `login.microsoftonline.com`。 例如，代码可能引用了用户流和令牌终结点。 请更新以下代码，以改为引用 `your-tenant-name.b2clogin.com`：

* 授权终结点
* 令牌终结点
* 令牌颁发者

例如，Contoso 注册/登录策略的机构终结点现在为：

```
https://contosob2c.b2clogin.com/00000000-0000-0000-0000-000000000000/B2C_1_signupsignin1
```

有关将基于 OWIN 的 web 应用程序迁移到 b2clogin.com 的详细信息，请参阅 [将基于 OWIN 的 WEB API 迁移到 b2clogin.com](multiple-token-endpoints.md)。

有关迁移受 Azure AD B2C 保护的 Azure API 管理 Api，请参阅[使用 Azure AD B2C 保护 AZURE Api 管理 api](secure-api-management.md)的[迁移到 b2clogin.com](secure-api-management.md#migrate-to-b2clogincom)部分。

## <a name="microsoft-authentication-library-msal"></a>Microsoft 身份验证库 (MSAL)

### <a name="msalnet-validateauthority-property"></a>MSAL.NET ValidateAuthority 属性

如果使用的是 [MSAL.NET][msal-dotnet] v2 或更早版本，请在客户端实例化上将 **ValidateAuthority** 属性设置为， `false` 以允许重定向到 *b2clogin.com*。 在 MSAL.NET v3 和更高版本中不需要将此值设置为 `false`。

```csharp
ConfidentialClientApplication client = new ConfidentialClientApplication(...); // Can also be PublicClientApplication
client.ValidateAuthority = false; // MSAL.NET v2 and earlier **ONLY**
```

### <a name="msal-for-javascript-validateauthority-property"></a>MSAL for JavaScript validateAuthority 属性

如果你使用的是 [MSAL for JavaScript][msal-js] v 1.2.2 或更早版本，请将“validateAuthority”属性设置为 `false`。

```JavaScript
// MSAL.js v1.2.2 and earlier
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false // Required in MSAL.js v1.2.2 and earlier **ONLY**
  }
);
```

如果在 MSAL.js 1.3.0+（默认值）中设置 `validateAuthority: true`，则还需要使用 `knownAuthorities` 指定有效的令牌颁发者：

```JavaScript
// MSAL.js v1.3.0+
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: true, // Supported in MSAL.js v1.3.0+
    knownAuthorities: ['tenant-name.b2clogin.com'] // Required if validateAuthority: true
  }
);
```

## <a name="next-steps"></a>后续步骤

有关将基于 OWIN 的 web 应用程序迁移到 b2clogin.com 的详细信息，请参阅 [将基于 OWIN 的 WEB API 迁移到 b2clogin.com](multiple-token-endpoints.md)。

有关迁移受 Azure AD B2C 保护的 Azure API 管理 Api，请参阅[使用 Azure AD B2C 保护 AZURE Api 管理 api](secure-api-management.md)的[迁移到 b2clogin.com](secure-api-management.md#migrate-to-b2clogincom)部分。

<!-- LINKS - External -->
[msal-dotnet]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[msal-dotnet-b2c]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics
[msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
[msal-js-b2c]: ../active-directory/develop/msal-b2c-overview.md
