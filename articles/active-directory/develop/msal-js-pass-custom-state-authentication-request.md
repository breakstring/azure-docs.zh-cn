---
title: 在身份验证请求中传递自定义状态 (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: 了解如何使用适用于 JavaScript 的 Microsoft 身份验证库 (MSAL.js) 在身份验证请求中传递自定义状态参数值。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 01/16/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 5c1fad96ea6e3b75b3afdfd4a4d3baac43308541
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2021
ms.locfileid: "98063665"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>使用 MSAL.js 在身份验证请求中传递自定义状态

由 OAuth 2.0 定义的 state  参数包含在身份验证请求中，并在令牌响应中返回，以防止跨站点请求伪造攻击。 默认情况下，适用于 JavaScript 的 Microsoft 身份验证库 ( # A0) 在身份验证请求中传递随机生成的唯一 *状态* 参数值。

state 参数也可用于在重定向之前对应用的状态信息进行编码。 可以在应用中传递用户的状态，例如他们所在的页面或视图，作为此参数的输入。 MSAL.js 库允许你将自定义状态作为 state 参数传入 `Request` 对象：

```javascript
// Request type
export type AuthenticationParameters = {
    scopes?: Array<string>;
    extraScopesToConsent?: Array<string>;
    prompt?: string;
    extraQueryParameters?: QPDict;
    claimsRequest?: string;
    authority?: string;
    state?: string;
    correlationId?: string;
    account?: Account;
    sid?: string;
    loginHint?: string;
    forceRefresh?: boolean;
};
```

> [!Note]
> 如果要跳过缓存的令牌并转到服务器，请将布尔值 `forceRefresh` 传入到用于发出登录/令牌请求的 AuthenticationParameters 对象。
> 默认情况下不应使用 `forceRefresh`，因为对应用程序存在性能影响。
> 可以依靠缓存为用户提供更好的体验。
> 只应在知道当前缓存的数据没有最新信息的情况下，才应跳过缓存。
> 例如，管理工具将角色添加到需要使用更新角色获取新令牌的用户。

例如：

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: "page_url"
}

myMSALObj.loginPopup(loginRequest);
```

发送请求时，传入状态将追加到 MSAL.js 设置的唯一 GUID 中。 返回响应时，MSAL.js 将检查状态是否匹配，然后在 `Response` 对象中返回自定义传入状态作为 `accountState`。

```javascript
export type AuthResponse = {
    uniqueId: string;
    tenantId: string;
    tokenType: string;
    idToken: IdToken;
    accessToken: string;
    scopes: Array<string>;
    expiresOn: Date;
    account: Account;
    accountState: string;
};
```

若要了解详细信息，请阅读[使用 MSAL.js 生成单页应用程序 (SPA)](scenario-spa-overview.md)。