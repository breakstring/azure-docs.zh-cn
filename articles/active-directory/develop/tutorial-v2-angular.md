---
title: 教程：创建使用 Microsoft 标识平台进行身份验证的 Angular 应用 | Azure
titleSuffix: Microsoft identity platform
description: 在本教程中，你将生成一个 Angular 单页应用 (SPA)，它使用 Microsoft 标识平台实现用户登录，并获取访问令牌以代表用户调用 Microsoft Graph API。
services: active-directory
author: hamiltonha
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 03/05/2020
ms.author: hahamil
ms.custom: aaddev, identityplatformtop40, devx-track-js
ms.openlocfilehash: 105353598a2af60c407bacf02b4527b2de84e450
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756159"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-an-angular-single-page-application"></a>教程：从 Angular 单页应用程序将用户登录并调用 Microsoft Graph API

在本教程中，你将构建一个 Angular 单页应用程序 (SPA)，它使用户登录并调用 Microsoft Graph API。

本教程的内容：

> [!div class="checklist"]
> * 使用 `npm` 创建 Angular 项目
> * 在 Azure 门户中注册应用程序
> * 添加代码以支持用户登录和注销
> * 添加代码以调用 Microsoft Graph API
> * 测试应用程序

## <a name="prerequisites"></a>先决条件

* 用于运行本地 Web 服务器的 [Node.js](https://nodejs.org/en/download/)。
* 用于修改项目文件的 [Visual Studio Code](https://code.visualstudio.com/download) 或其他编辑器。

## <a name="how-the-sample-app-works"></a>示例应用工作原理

![示意图，展示了本教程中生成的示例应用的工作原理](./media/tutorial-v2-angular/diagram-auth-flow-spa-angular.svg)

本教程中创建的示例应用程序使 Angular SPA 能够查询 Microsoft Graph API 或 Web API，该 API 接受 Microsoft 标识平台颁发的令牌。 它使用适用于 Angular 的 Microsoft 身份验证库 (MSAL)，这是核心 MSAL.js 库的包装器。 MSAL Angular 使 Angular 6+ 应用程序能够使用 Azure Active Directory (Azure AD) 对企业用户和具有 Microsoft 帐户和社交标识（如 Facebook、Google 和 LinkedIn）的用户进行身份验证。 使用此库，应用程序还可以获取对 Microsoft 云服务和 Microsoft Graph 的访问权限。

在此方案中，用户登录后请求了访问令牌，并通过授权标头将其添加到 HTTP 请求。 令牌获取和续订通过 MSAL 处理。

### <a name="libraries"></a>库

本教程使用以下库：

|库|说明|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|适用于 JavaScript Angular 的 Microsoft 身份验证库包装器|

可以在 GitHub 上的 [AzureAD/microsoft-authentication-library-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js) 存储库中找到 MSAL.js 库的源代码。

## <a name="create-your-project"></a>创建项目

使用以下 npm 命令生成一个新的 Angular 应用程序：

```bash
npm install -g @angular/cli@8                    # Install the Angular CLI
ng new my-application --routing=true --style=css # Generate a new Angular app
cd my-application                                # Change to the app directory
npm install @angular/material@8 @angular/cdk@8   # Install the Angular Material component library (optional, for UI)
npm install msal @azure/msal-angular             # Install MSAL and MSAL Angular in your application
ng generate component page-name                  # To add a new page (such as a home or profile page)
```

## <a name="register-your-application"></a>注册应用程序

按照说明在 Azure 门户中[注册单页应用程序](./scenario-spa-app-registration.md)。

在注册的应用“概述”页上，记下“应用程序(客户端) ID”值供稍后使用。 

注册 **http://localhost:4200/** 作为“重定向 URI”，并启用隐式授权设置。

## <a name="configure-the-application"></a>配置应用程序

1. 在 src/app 文件夹中，编辑 app.module.ts，将 `MSALModule` 添加到 `imports` 并添加 `isIE` 常量： 

    ```javascript
    const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;
    @NgModule({
      declarations: [
        AppComponent
      ],
      imports: [
        BrowserModule,
        AppRoutingModule,
        MsalModule.forRoot({
          auth: {
            clientId: 'Enter_the_Application_Id_here', // This is your client ID
            authority: 'Enter_the_Cloud_Instance_Id_Here'/'Enter_the_Tenant_Info_Here', // This is your tenant ID
            redirectUri: 'Enter_the_Redirect_Uri_Here'// This is your redirect URI
          },
          cache: {
            cacheLocation: 'localStorage',
            storeAuthStateInCookie: isIE, // Set to true for Internet Explorer 11
          },
        }, {
          popUp: !isIE,
          consentScopes: [
            'user.read',
            'openid',
            'profile',
          ],
          unprotectedResources: [],
          protectedResourceMap: [
            ['https://graph.microsoft.com/v1.0/me', ['user.read']]
          ],
          extraQueryParameters: {}
        })
      ],
      providers: [],
      bootstrap: [AppComponent]
    })
    ```

    替换以下值：

    |值名称|关于|
    |---------|---------|
    |Enter_the_Application_Id_Here|在应用程序注册的“概览”页中，这是你的“应用程序(客户端) ID”值。  |
    |Enter_the_Cloud_Instance_Id_Here|这是 Azure 云的实例。 对于主要云或全球 Azure 云，请输入 **https://login.microsoftonline.com** 。 对于国家/地区云（例如中国云），请参阅[国家/地区云](./authentication-national-cloud.md)。|
    |Enter_the_Tenant_Info_Here| 设置为以下选项之一：如果应用程序支持此组织目录中的帐户，请将此值替换为目录（租户）ID 或租户名称（例如 contoso.microsoft.com）。 如果应用程序支持“任何组织目录中的帐户”，请将此值替换为 **organizations**。 如果应用程序支持“任何组织目录中的帐户和个人 Microsoft 帐户”，请将此值替换为 **common**。 若要限制对“仅限个人 Microsoft 帐户”的支持，请将此值替换为 **consumers**。 |
    |Enter_the_Redirect_Uri_Here|替换为 **http://localhost:4200** 。|

    有关可用的可配置选项的详细信息，请阅读[初始化客户端应用程序](msal-js-initializing-client-applications.md)。

2. 在同一文件的顶部，添加以下 import 语句：

    ```javascript
    import { MsalModule, MsalInterceptor } from '@azure/msal-angular';
    ```

3. 将以下 import 语句添加到 `src/app/app.component.ts` 的顶部：

    ```javascript
    import { MsalService, BroadcastService } from '@azure/msal-angular';
    import { Component, OnInit } from '@angular/core';
    ```
## <a name="sign-in-a-user"></a>将用户登录

将以下代码添加到 `AppComponent`，以将用户登录：

```javascript
export class AppComponent implements OnInit {
    constructor(private broadcastService: BroadcastService, private authService: MsalService) { }

    ngOnInit() { }

    login() {
        const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;

        if (isIE) {
          this.authService.loginRedirect({
            extraScopesToConsent: ["user.read", "openid", "profile"]
          });
        } else {
          this.authService.loginPopup({
            extraScopesToConsent: ["user.read", "openid", "profile"]
          });
        }
    }
}
```

> [!TIP]
> 对于 Internet Explorer 用户，建议使用 `loginRedirect`。

## <a name="acquire-a-token"></a>获取令牌

### <a name="angular-interceptor"></a>Angular 侦听器

MSAL Angular 向已知的受保护资源提供一个 `Interceptor` 类，该类可自动为使用 Angular `http` 客户端的传出请求获取令牌。

首先，将 `Interceptor` 类作为提供程序包含在应用程序中：

```javascript
import { MsalInterceptor, MsalModule } from "@azure/msal-angular";
import { HTTP_INTERCEPTORS, HttpClientModule } from "@angular/common/http";

@NgModule({
    // ...
    providers: [
        {
            provide: HTTP_INTERCEPTORS,
            useClass: MsalInterceptor,
            multi: true
        }
    ]
}
```

接下来，以 `protectedResourceMap` 的形式提供受保护资源到 `MsalModule.forRoot()` 的映射，并将这些作用域包含在 `consentScopes` 中。 在 `protectedResourceMap` 集合中输入的 URL 区分大小写。

```javascript
@NgModule({
  // ...
  imports: [
    // ...
    MsalModule.forRoot({
      auth: {
        clientId: 'Enter_the_Application_Id_here', // This is your client ID
        authority: 'https://login.microsoftonline.com/Enter_the_Tenant_Info_Here', // This is your tenant info
        redirectUri: 'Enter_the_Redirect_Uri_Here' // This is your redirect URI
      },
      cache: {
        cacheLocation: 'localStorage',
        storeAuthStateInCookie: isIE, // Set to true for Internet Explorer 11
      },
    },
    {
      popUp: !isIE,
      consentScopes: [
        'user.read',
        'openid',
        'profile',
      ],
      unprotectedResources: [],
      protectedResourceMap: [
        ['https://graph.microsoft.com/v1.0/me', ['user.read']]
      ],
      extraQueryParameters: {}
    })
  ],
});
```

最后，使用 HTTP 请求检索用户的配置文件：

```JavaScript
const graphMeEndpoint = "https://graph.microsoft.com/v1.0/me";

getProfile() {
  this.http.get(graphMeEndpoint).toPromise()
    .then(profile => {
      this.profile = profile;
    });
}
```

### <a name="acquiretokensilent-acquiretokenpopup-acquiretokenredirect"></a>acquireTokenSilent、acquireTokenPopup、acquireTokenRedirect
MSAL 使用三个方法来获取令牌：`acquireTokenRedirect`、`acquireTokenPopup` 和 `acquireTokenSilent`。 但是，对于 Angular 应用，我们建议改用 `MsalInterceptor` 类，如前一部分中所示。

#### <a name="get-a-user-token-silently"></a>以无提示方式获取用户令牌

`acquireTokenSilent` 方法处理令牌获取和续订，且无需用户交互。 首次执行 `loginRedirect` 或 `loginPopup` 方法后，通常使用 `acquireTokenSilent` 获取用于在后续调用中访问受保护资源的令牌。 进行请求或续订令牌的调用时，以静默方式进行。

```javascript
const requestObj = {
    scopes: ["user.read"]
};

this.authService.acquireTokenSilent(requestObj).then(function (tokenResponse) {
    // Callback code here
    console.log(tokenResponse.accessToken);
}).catch(function (error) {
    console.log(error);
});
```

在该代码中，`scopes` 包含所请求的需要在 API 的访问令牌中返回的作用域。

例如：

* Microsoft Graph 的 `["user.read"]`
* 自定义 Web API 的 `["<Application ID URL>/scope"]`（即 `api://<Application ID>/access_as_user`）

#### <a name="get-a-user-token-interactively"></a>以交互方式获取用户令牌

有时需要让用户与 Microsoft 标识平台进行交互。 例如：

* 由于密码已过期，用户可能需要重新输入凭据。
* 应用程序正在请求访问用户需要许可的其他资源范围。
* 需要双重身份验证。

对于大多数应用程序，建议的模式是先调用 `acquireTokenSilent`，然后捕获异常，然后再调用 `acquireTokenPopup`（或 `acquireTokenRedirect`），以启动交互式请求。

调用 `acquireTokenPopup` 会弹出一个登录窗口。 另外，`acquireTokenRedirect` 会将用户重定向到 Microsoft 标识平台。 在该窗口中，用户需要确认其凭据，许可访问所需的资源，或完成双重身份验证。

```javascript
  const requestObj = {
      scopes: ["user.read"]
  };

  this.authService.acquireTokenPopup(requestObj).then(function (tokenResponse) {
      // Callback code here
      console.log(tokenResponse.accessToken);
  }).catch(function (error) {
      console.log(error);
  });
```

> [!NOTE]
> 本快速入门对 Microsoft Internet Explorer 使用 `loginRedirect` 和 `acquireTokenRedirect` 方法，因为 Internet Explorer 浏览器处理弹出窗口时会出现一个[已知问题](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues)。

## <a name="log-out"></a>注销

添加以下代码来注销用户：

```javascript
logout() {
  this.authService.logout();
}
```

## <a name="add-ui"></a>添加 UI
有关如何使用 Angular Material 组件库添加 UI 的示例，请查看[示例应用程序](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular)。

## <a name="test-your-code"></a>测试代码

1.  通过在命令行提示符下从应用程序文件夹运行以下命令，启动 Web 服务器来侦听端口：

    ```bash
    npm install
    npm start
    ```
1. 在浏览器中输入 **http://localhost:4200** 或 **http://localhost:{port}** ，其中，port 是 Web 服务器正在侦听的端口。


### <a name="provide-consent-for-application-access"></a>许可应用程序访问

首次开始登录到应用程序时，系统会提示你授予其访问你的个人资料的权限，并允许其将你登录：

![“请求的权限”窗口](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

## <a name="add-scopes-and-delegated-permissions"></a>添加范围和委托的权限

Microsoft Graph API 需要 *user.read* 作用域来读取用户的个人资料。 默认情况下，在注册门户上注册的每个应用程序中，都会自动添加此范围。 Microsoft Graph 的其他 API 以及后端服务器的自定义 API 可能需要其他作用域。 例如，Microsoft Graph API 需要使用 Calendars.Read 作用域才能列出用户的日历。

若要在应用程序上下文中访问用户的日历，请将 *Calendars.Read* 委派权限添加到应用程序注册信息。 然后，将 *Calendars.Read* 作用域添加到 `acquireTokenSilent` 调用。

>[!NOTE]
>当你增加作用域数量时，可能会提示用户另外进行许可。

如果后端 API 不需要范围（不建议），则你可以将 *clientId* 用作调用中的范围来获取令牌。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>后续步骤

在由多部分组成的文章系列中，深入了解 Microsoft 标识平台上的单页应用程序 (SPA) 开发。

> [!div class="nextstepaction"]
> [方案：单页应用程序](scenario-spa-overview.md)
