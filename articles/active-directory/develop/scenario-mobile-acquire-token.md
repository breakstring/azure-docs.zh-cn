---
title: 获取用于调用 Web API 的令牌（移动应用）| Azure
titleSuffix: Microsoft identity platform
description: 了解如何构建用于调用 Web API 的移动应用。 （获取应用的令牌。）
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 6e6eda3d711710ea7450165ab02d7a260067bfcb
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582547"
---
# <a name="get-a-token-for-a-mobile-app-that-calls-web-apis"></a>获取调用 Web API 的移动应用的令牌

应用需要先获得访问令牌才能调用受保护的 Web API。 本文指导你完成使用 Microsoft 身份验证库 (MSAL) 获取令牌的过程。

## <a name="define-a-scope"></a>定义范围

请求令牌时，请定义一个作用域。 作用域决定了应用能够访问哪些数据。

定义范围的最简单方法是将所需 Web API 的 `App ID URI` 与范围 `.default` 组合在一起。 此定义告知 Microsoft 标识平台，你的应用程序需要门户中设置的所有作用域。

### <a name="android"></a>Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

### <a name="ios"></a>iOS
```swift
let scopes = ["https://graph.microsoft.com/.default"]
```

### <a name="xamarin"></a>Xamarin
```csharp
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="get-tokens"></a>获取令牌

### <a name="acquire-tokens-via-msal"></a>通过 MSAL 获取令牌

MSAL 允许应用以无提示方式和交互方式获取令牌。 调用 `AcquireTokenSilent()` 或 `AcquireTokenInteractive()` 时，MSAL 将返回所请求范围的访问令牌。 正确的模式是发出无提示请求，然后回退到交互式请求。

#### <a name="android"></a>Android

```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);

// Check if there are any accounts we can sign in silently.
// Result is in the silent callback (success or error).
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {

        if (accounts.isEmpty() && accounts.size() == 1) {
            // TODO: Create a silent callback to catch successful or failed request.
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts or > 1 account. */
        }
    }
});

[...]

// No accounts found. Interactively request a token.
// TODO: Create an interactive callback to catch successful or failed requests.
sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());
```

#### <a name="ios"></a>iOS

首先尝试以无提示方式获取令牌：

```objc

NSArray *scopes = @[@"https://graph.microsoft.com/.default"];
NSString *accountIdentifier = @"my.account.id";

MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];

MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {

    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;

        // Access token to call the web API
        NSString *accessToken = result.accessToken;
    }

    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
        return;
    }
}];
```

```swift

let scopes = ["https://graph.microsoft.com/.default"]
let accountIdentifier = "my.account.id"

guard let account = try? application.account(forIdentifier: accountIdentifier) else { return }
let silentParameters = MSALSilentTokenParameters(scopes: scopes, account: account)
application.acquireTokenSilent(with: silentParameters) { (result, error) in

    guard let authResult = result, error == nil else {

    let nsError = error! as NSError

    if (nsError.domain == MSALErrorDomain &&
        nsError.code == MSALError.interactionRequired.rawValue) {

            // Interactive auth will be required, call acquireToken()
            return
         }
         return
     }

    // You'll want to get the account identifier to retrieve and reuse the account
    // for later acquireToken calls
    let accountIdentifier = authResult.account.identifier

    // Access token to call the web API
    let accessToken = authResult.accessToken
}
```

如果 MSAL 返回 `MSALErrorInteractionRequired`，请尝试以交互方式获取令牌：

```objc
UIViewController *viewController = ...; // Pass a reference to the view controller that should be used when getting a token interactively
MSALWebviewParameters *webParameters = [[MSALWebviewParameters alloc] initWithAuthPresentationViewController:viewController];
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:webParameters];
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) {
    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;

        NSString *accessToken = result.accessToken;
    }
}];
```

```swift
let viewController = ... // Pass a reference to the view controller that should be used when getting a token interactively
let webviewParameters = MSALWebviewParameters(authPresentationViewController: viewController)
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: webviewParameters)
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in

    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }

    // Get access token from result
    let accessToken = authResult.accessToken
})
```

适用于 iOS 和 macOS 的 MSAL 在以交互方式或无提示方式获取令牌时支持各种修饰符：
* [用于获取令牌的通用参数](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALTokenParameters.html#/Configuration%20parameters)
* [用于获取交互式令牌的参数](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALInteractiveTokenParameters.html#/Configuring%20MSALInteractiveTokenParameters)
* [用于获取无提示令牌的参数](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALSilentTokenParameters.html)

#### <a name="xamarin"></a>Xamarin

以下示例演示了以交互方式获取令牌所需的最少量代码。 该示例使用 Microsoft Graph 读取用户的配置文件。

```csharp
string[] scopes = new string[] {"user.read"};
var app = PublicClientApplicationBuilder.Create(clientId).Build();
var accounts = await app.GetAccountsAsync();
AuthenticationResult result;
try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
             .ExecuteAsync();
}
catch(MsalUiRequiredException)
{
 result = await app.AcquireTokenInteractive(scopes)
             .ExecuteAsync();
}
```

#### <a name="mandatory-parameters-in-msalnet"></a>MSAL.NET 中的必需参数

`AcquireTokenInteractive` 只有一个必需的参数：`scopes`。 `scopes` 参数枚举用于定义所需令牌范围的字符串。 如果令牌用于 Microsoft Graph，可以在每个 Microsoft Graph API 的 API 参考文档中找到所需的范围。 参阅参考文档中的“权限”部分。

例如，若要[列出用户的联系人](/graph/api/user-list-contacts)，请使用范围“User.Read”、“Contacts.Read”。 有关详细信息，请参阅 [Microsoft Graph 权限参考](/graph/permissions-reference)。

在 Android 上，可以在使用 `PublicClientApplicationBuilder` 创建应用时指定父活动。 如果当时未指定父活动，以后可以使用 `.WithParentActivityOrWindow` 来指定，如以下部分中所述。 如果指定了父活动，交互后，令牌将交回给该父活动。 如果未指定父活动，`.ExecuteAsync()` 调用将引发异常。

#### <a name="specific-optional-parameters-in-msalnet"></a>MSAL.NET 中特定的可选参数

以下部分介绍 MSAL.NET 中的可选参数。

##### <a name="withprompt"></a>WithPrompt

`WithPrompt()` 参数通过指定提示来控制与用户的交互。

![显示“提示”结构中的字段的图像。 这些常量值通过定义由 WithPrompt() 参数显示的提示的类型来控制与用户的交互。](https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png)

该类定义以下常量：

- `SelectAccount` 强制安全令牌服务 (STS) 显示帐户选择对话框。 该对话框包含用户与其建立了会话的帐户。 若要让用户在不同的标识之间进行选择，可以使用此选项。 此选项会驱动 MSAL 向标识提供者发送 `prompt=select_account`。

    `SelectAccount` 常量是默认值，它根据可用信息有效提供可能最佳的体验。 可用信息可能包括帐户、用户会话存在性等。 除非有合理的理由，否则请不要更改此默认值。
- `Consent` 用于提示用户授予许可，即使以前曾经授予过许可。 在这种情况下，MSAL 会将 `prompt=consent` 发送到标识提供者。

    你可能希望在注重安全的应用程序中使用 `Consent` 常量。在此类应用程序中，组织监管政策要求用户在每次使用该应用程序时都会看到许可对话框。
- `ForceLogin` 使服务能够提示用户提供凭据，即使并不需要提示。

    如果令牌获取失败，而你想要让用户重新登录，则此选项会很有用。 在这种情况下，MSAL 会将 `prompt=login` 发送到标识提供者。 你可能希望在注重安全的应用程序中使用此选项。在此类应用程序中，组织监管政策要求用户在每次访问该应用程序的特定部分时都要登录。
- `Never` 仅适用于 .NET 4.5 和 Windows 运行时 (WinRT)。 此常量不会提示用户，而是尝试使用隐藏的嵌入式 Web 视图中存储的 Cookie。 有关详细信息，请参阅[在 MSAL.NET 中使用 Web 浏览器](./msal-net-web-browsers.md)。

    如果此选项失败，则 `AcquireTokenInteractive` 会引发异常，告知需要进行 UI 交互。 然后使用另一个 `Prompt` 参数。
- `NoPrompt` 不会向标识提供者发送提示。

    此选项仅适用于 Azure Active Directory B2C 中的编辑配置文件策略。 有关详细信息，请参阅 [B2C 细节](https://aka.ms/msal-net-b2c-specificities)。

##### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

在你希望用户提前许可多个资源的高级方案中使用 `WithExtraScopeToConsent` 修饰符。 如果你不希望使用通常与 MSAL.NET 或 Microsoft 标识平台一起使用的增量许可，则可以使用此修饰符。 有关详细信息，请参阅[让用户提前许可多个资源](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources)。

下面是代码示例：

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

##### <a name="other-optional-parameters"></a>其他可选参数

若要了解 `AcquireTokenInteractive` 的其他可选参数，请参阅 [AcquireTokenInteractiveParameterBuilder 的参考文档](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder#methods)。

### <a name="acquire-tokens-via-the-protocol"></a>通过协议获取令牌

建议不要直接使用协议来获取令牌。 如果这样做，应用将不支持某些涉及单一登录 (SSO)、设备管理和条件访问的方案。

使用协议获取移动应用的令牌时，请发出两个请求：

* 获取授权代码
* 用该代码交换令牌。

#### <a name="get-an-authorization-code"></a>获取授权代码

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-the-token"></a>获取访问权限并刷新令牌

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=<CLIENT_ID>
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=<ENCODED_REDIRECT_URI>
&grant_type=authorization_code
```

## <a name="next-steps"></a>后续步骤

转到此方案中的下一篇文章：[调用 Web API](scenario-mobile-call-api.md)。