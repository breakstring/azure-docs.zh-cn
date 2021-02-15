---
title: 将 MSAL.js 与 Azure AD B2C 配合使用
titleSuffix: Microsoft identity platform
description: '适用于 JavaScript 的 Microsoft 身份验证库 ( # A0) 使应用程序能够与 Azure AD B2C 和获取令牌，以调用受保护的 web Api。 这些 Web API 可以是 Microsoft Graph、其他 Microsoft API、其他来源的 Web API 或你自己的 Web API。'
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/05/2020
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev devx-track-js
ms.openlocfilehash: 53294a30b38bed7ab7516443277cac24e4fef4c1
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2021
ms.locfileid: "98063716"
---
# <a name="use-the-microsoft-authentication-library-for-javascript-to-work-with-azure-ad-b2c"></a>使用适用于 JavaScript 的 Microsoft 身份验证库来处理 Azure AD B2C

[适用于 javascript 的 Microsoft 身份验证库 ( # A0) ](https://github.com/AzureAD/microsoft-authentication-library-for-js)使 JavaScript 开发人员能够使用[Azure Active Directory B2C](../../active-directory-b2c/overview.md) (Azure AD B2C) 来对具有社交和本地标识的用户进行身份验证。

使用作为标识管理服务的 Azure AD B2C，可以自定义和控制客户的注册和登录方式，并在他们使用你的应用程序时管理其配置文件。 使用 Azure AD B2C，还可以为应用程序在身份验证过程中显示的 UI 提供品牌标志并进行自定义。

以下部分演示如何：

- 保护 Node.js Web API
- 支持在单页应用程序 (SPA) 中登录，并调用受保护的 Web API
- 启用密码重置支持

## <a name="prerequisites"></a>先决条件

如果尚未创建 [Azure AD B2C 租户](../../active-directory-b2c/tutorial-create-tenant.md)，请创建一个。

## <a name="nodejs-web-api"></a>Node.js Web API

以下步骤演示 Web API 如何使用 Azure AD B2C 来保护自身，并将所选范围公开给客户端应用程序。

适用于 Node 的 MSAL.js 目前正在开发中。 有关详细信息，请参阅 GitHub 上的[路线图](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)。 我们目前建议使用 [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad)，它是由 Microsoft 开发并提供支持的适用于 Node.js 的身份验证库。

### <a name="step-1-register-your-application"></a>步骤 1：注册应用程序

若要使用 Azure AD B2C 保护 Web API，首先需要注册该 Web API。 请遵循[注册应用程序](../../active-directory-b2c/add-web-api-application.md)来执行详细步骤。

### <a name="step-2-download-the-sample-application"></a>步骤 2：下载示例应用程序

下载示例作为 zip 文件，或从 GitHub 克隆：

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="step-3-configure-authentication"></a>步骤 3：配置身份验证

1. 打开示例中的 config.json 文件。

2. 注册应用程序时，请使用前面获取的应用程序凭据配置示例。 更改以下代码行：将值替换为你的租户名称、客户端 ID 和策略名称。

    ```json
         "credentials": {
             "tenantName": "<your-tenant-name>",
             "clientID": "<your-webapi-application-ID>"
         },
         "policies": {
             "policyName": "B2C_1_signupsignin1"
         },
         "resource": {
             "scope": ["demo.read"] 
         },
    ```

有关详细信息，请查看此 [Node.js B2C Web API 示例](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)。

## <a name="javascript-spa"></a>JavaScript SPA

以下步骤演示单页应用程序如何使用 Azure AD B2C 进行注册和登录，并调用受保护的 Web API。

### <a name="step-1-register-your-application"></a>步骤 1：注册应用程序

若要实现身份验证，首先需要注册你的应用程序。 请遵循[注册应用程序](../../active-directory-b2c/tutorial-register-applications.md)来执行详细步骤。

### <a name="step-2-download-the-sample-application"></a>步骤 2：下载示例应用程序

下载代码示例的 [.ZIP 存档](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip)或克隆 GitHub 存储库：

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>步骤 3：配置身份验证

配置应用程序时有两个关注点：

- 配置 API 终结点和公开的范围
- 配置身份验证参数和令牌范围

1. 打开示例中的 apiConfig.js 文件。

2. 注册 Web API 时，请使用前面获取的参数配置示例。 更改以下代码行：将值替换为你的 Web API 的地址和公开的范围。

   ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"], //API scopes you exposed during api registration
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
    };
   ```

1. 打开示例中的 authConfig.js 文件。

1. 注册单页应用程序时，请使用前面获取的参数配置示例。 更改以下代码行：将值替换为你的 ClientId、颁发机构元数据和令牌请求范围。

   ```javascript
    // Config object to be passed to Msal on creation.
    const msalConfig = {
        auth: {
            clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902",
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/B2C_1_signupsignin1",
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage", // This configures where your cache will be stored
            storeAuthStateInCookie: false // Set this to "true" to save cache in cookies
        }
    };

    // Add here scopes for id token to be used at the MS Identity Platform endpoint
    const loginRequest = {
        scopes: ["openid", "profile"],
    };
   ```

有关详细信息，请查看此 [JavaScript B2C 单页应用程序示例](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)。

## <a name="support-password-reset"></a>支持密码重置

在本部分中，你将扩展单页应用程序以使用 Azure AD B2C 密码重置用户流。 尽管 MSAL 目前不原生支持多个用户流或自定义策略，但你可以使用库来处理常见的用例，例如密码重置。

以下步骤假定你已经按照前面的 [JavaScript SPA](#javascript-spa) 部分中的步骤进行操作。

### <a name="step-1-define-the-authority-string-for-password-reset-user-flow"></a>步骤 1：定义密码重置用户流的授权字符串

1. 首先，创建一个对象，用于存储颁发机构 URI：

    ```javascript
        const b2cPolicies = {
            names: {
                signUpSignIn: "b2c_1_susi",
                forgotPassword: "b2c_1_reset"
            },
            authorities: {
                signUpSignIn: {
                    authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi",
                },
                forgotPassword: {
                    authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_reset",
                },
            },
        }
    ```

1. 接下来，默认使用 `signInSignUp` 策略初始化 MSAL 对象（请参阅前面的代码片段）。 当用户尝试登录时，将显示以下屏幕：

    :::image type="content" source="media/msal-b2c-overview/user-journey-01-signin.png" alt-text="Azure AD B2C 显示的登录屏幕":::

### <a name="step-2-catch-and-handle-authentication-errors-in-your-login-method"></a>步骤 2：捕获和处理登录方法中的身份验证错误

当某个用户选择“忘记密码”时，应用程序会引发一个错误，你的代码中应会捕获该错误，然后通过提供相应的用户流来处理此情况。 在本例中，为 `b2c_1_reset` 密码重置流。

1. 按如下方式扩展登录方法：

    ```javascript
    function signIn() {
      myMSALObj.loginPopup(loginRequest)
        .then(loginResponse => {
            console.log("id_token acquired at: " + new Date().toString());

            if (myMSALObj.getAccount()) {
              updateUI();
            }

        }).catch(function (error) {
          console.log(error);

          // error handling
          if (error.errorMessage) {
            // check for forgot password error
            if (error.errorMessage.indexOf("AADB2C90118") > -1) {

              //call login method again with the password reset user flow
              myMSALObj.loginPopup(b2cPolicies.authorities.forgotPassword)
                .then(loginResponse => {
                  console.log(loginResponse);
                  window.alert("Password has been reset successfully. \nPlease sign-in with your new password.");
                })
            }
          }
        });
    }
    ```

1. 上面的代码片段介绍了在代码 `AADB2C90118` 中捕获到错误后如何显示密码重置屏幕。

    重置密码后，用户返回应用程序并重新登录。

    :::image type="content" source="media/msal-b2c-overview/user-journey-02-password-reset.png" alt-text="Azure AD B2C 显示的密码重置流屏幕" border="false":::

    有关错误代码和异常处理的详细信息，请参阅 [MSAL 错误和异常代码](msal-error-handling-js.md)。

## <a name="next-steps"></a>后续步骤

详细了解 Azure AD B2C 概念：

- [用户流](../../active-directory-b2c/tutorial-create-user-flows.md)
- [自定义策略](../../active-directory-b2c/custom-policy-get-started.md)
- [UX 自定义](../../active-directory-b2c/configure-user-input.md)
