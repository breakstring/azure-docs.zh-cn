---
title: 如何委派用户注册和产品订阅
description: 了解如何在 Azure API 管理中将用户注册和产品订阅委派给第三方。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.assetid: 8b7ad5ee-a873-4966-a400-7e508bbbe158
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/15/2020
ms.author: apimpm
ms.openlocfilehash: 54193c9333c75fd8b973ebe33470fca3617e2f2d
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341835"
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>如何委派用户注册和产品订阅

可以通过委派使用现有网站处理开发人员的登录/注册和产品订阅事项，不需使用开发人员门户中的内置功能。 这样就可以让网站拥有用户数据，并通过自定义方式对这些步骤进行验证。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="delegating-developer-sign-in-and-sign-up"></a><a name="delegate-signin-up"> </a>委派开发人员登录和注册

若要将开发人员登录和注册委派给现有网站，需在站点上创建一个特殊的委派终结点。 它需要充当从 API 管理开发人员门户发起的任何此类请求的入口点。

最终工作流将如下所示：

1. 开发人员单击 API 管理开发人员门户中的登录或注册链接
2. 浏览器重定向到委派终结点
3. 委派终结点反过来会重定向到 UI 或呈现 UI，要求用户登录或注册
4. 成功后，用户会重定向回一开始使用的 API 管理开发人员门户页

一开始需先将 API 管理设置为通过委派终结点来路由请求。 在 Azure 门户中，在 API 管理资源中搜索“安全性”，然后单击“委派”项 。 单击用于启用“委派登录和注册”的复选框。

![“委派”页][api-management-delegation-signin-up]

* 确定特殊委派终结点的 URL，将其输入到“委派终结点 URL”字段中。 
* 在”委派身份验证密钥”字段中输入一个密钥，该密钥用于计算提供给用户进行验证的签名，确保请求确实来自 Azure API 管理。 可以单击“生成”按钮让 API 管理随机生成一个密钥。

现在需创建“委派终结点”。 该终结点需执行多项操作：

1. 接收以下形式的请求：
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl={源页的 URL}&salt={字符串}&sig={字符串}*
   
    登录/注册示例的查询参数：
   
   * **operation** ：确定委派请求的类型，在此示例中只能为 **SignIn**
   * **returnUrl** ：用户已单击了其中的登录或注册链接的页的 URL
   * **salt** ：用于计算安全哈希的特殊 salt 字符串
   * **sig** ：计算的安全哈希，用于与用户自行计算的哈希进行比较
2. 验证请求是否来自 Azure API 管理（可选，但强烈推荐执行以确保安全）
   
   * 根据 **returnUrl** 和 **salt** 查询参数计算字符串的 HMAC-SHA512 哈希（ [下文提供了示例代码]）：
     
     > HMAC( **salt** + '\n' + **returnUrl** )

   * 将上面计算的哈希与 **sig** 查询参数的值进行比较。 如果两个哈希匹配，则转到下一步，否则拒绝该请求。
3. 验证收到的是否为登录/注册请求：需将 **operation** 查询参数设置为“ **SignIn** ”。
4. 向用户提供登录或注册 UI
5. 如果用户要注册，则需在 API 管理中为其创建相应的帐户。 请使用 API 管理 REST API [创建用户]。 这样做时，请确保将用户 ID 设置为与用户存储中的用户 ID 相同的值，或设置为可跟踪的 ID。
6. 在成功对用户进行身份验证后：
   
   * 通过 API 管理[请求共享访问令牌]REST API
   * 将 returnUrl 查询参数追加到你从上述 API 调用接收的 SSO URL：
     
     > 例如，`https://<developer portal domain, for example: contoso.developer.azure-api.net>/signin-sso?token=<URL-encoded token>&returnUrl=<URL-encoded URL, for example: %2Freturn%2Furl>` 
     
   * 将用户重定向到上述生成的 URL

除了 **SignIn** 操作，还可以执行帐户管理，只需按上述步骤使用以下某个操作即可：

* **ChangePassword**
* **ChangeProfile**
* **CloseAccount**
* **SignOut**

若要执行帐户管理操作，必须传递以下查询参数。

* **operation** ：确定委派请求的类型（ChangePassword、ChangeProfile 或 CloseAccount）
* **userId** ：要管理的帐户的用户 ID
* **salt** ：用于计算安全哈希的特殊 salt 字符串
* **sig** ：计算的安全哈希，用于与用户自行计算的哈希进行比较

## <a name="delegating-product-subscription"></a><a name="delegate-product-subscription"> </a>委派产品订阅

委派产品订阅的工作方式与委派用户登录/注册类似。 最终工作流将如下所示：

1. 开发人员在 API 管理开发人员门户中选择一个产品，并单击“订阅”按钮。
2. 浏览器将重定向到委托终结点。
3. 委托终结点执行所需的产品订阅步骤。 具体的步骤由你设计。 步骤可以包括重定向到另一个用于请求计费信息的页面、提出更多提问，或者只是存储信息而不要求执行任何用户操作

若要启用此功能，请在“委派”页上单击“委派产品订阅”。 

接下来，确保委托终结点执行以下操作：

1. 接收以下形式的请求：
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation={操作}&productId={要订阅的产品}&userId={提出请求的用户}&salt={字符串}&sig={字符串}*
   >
   
    产品订阅示例的查询参数：
   
   * **operation** ：标识委派请求的类型。 对于产品订阅请求，有效选项包括：
     * “Subscribe”：请求为用户订阅具有所提供的 ID 的给定产品（参见下文）
     * “Unsubscribe”：请求为用户取消订阅某个产品
     * “Renew”：请求续订某个订阅（例如即将到期的订阅）
   * **productId** （“Subscribe”中）- 用户请求订阅的产品的 ID
   * **subscriptionId** （ *Unsubscribe* 和 *Renew* ）中 - 产品订阅的 ID
   * **userId** （“Subscribe”中）- 请求所针对的用户的 ID
   * **salt** ：用于计算安全哈希的特殊 salt 字符串
   * **sig** ：计算的安全哈希，用于与用户自行计算的哈希进行比较

2. 验证请求是否来自 Azure API 管理（可选，但强烈推荐执行以确保安全）
   
   * 根据 **productId** 、 **userId** 和 **salt** 查询参数计算字符串的 HMAC-SHA512：
     
     > HMAC( **salt** + '\n' + **productId** + '\n' + **userId** )
     > 
     > 
   * 将上面计算的哈希与 **sig** 查询参数的值进行比较。 如果两个哈希匹配，则转到下一步，否则拒绝该请求。
3. 根据在 operation 中请求的操作类型（例如请求计费信息、提问更多问题，等等）处理产品订阅。
4. 在这一端成功为用户订阅产品以后，即可[调用订阅 REST API] 为用户订阅 API 管理产品。

## <a name="example-code"></a><a name="delegate-example-code"> </a>示例代码

这些代码示例演示如何：

* 提取发布者门户的“委托”屏幕中设置的委托验证密钥
* 创建 HMAC，随后它将用于验证签名，以证实所传递的 returnUrl 的有效性。

同样的代码也适用于 productId 和 userId，只需进行轻微修改。

**用于生成 returnUrl 哈希的 C# 代码**

```csharp
using System.Security.Cryptography;

string key = "delegation validation key";
string returnUrl = "returnUrl query parameter";
string salt = "salt query parameter";
string signature;
using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
{
    signature = Convert.ToBase64String(encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl)));
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
}
```

**用于生成 returnUrl 哈希的 NodeJS 代码**

```
var crypto = require('crypto');

var key = 'delegation validation key'; 
var returnUrl = 'returnUrl query parameter';
var salt = 'salt query parameter';

var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
var digest = hmac.update(salt + '\n' + returnUrl).digest();
// change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
// compare signature to sig query parameter

var signature = digest.toString('base64');
```

> [!IMPORTANT]
> 需要[重新发布开发人员门户](api-management-howto-developer-portal-customize.md#publish)才能使委托更改生效。

## <a name="next-steps"></a>后续步骤
有关委派的详细信息，请观看以下视频：

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delegating developer sign in and sign up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[请求共享访问令牌]: /rest/api/apimanagement/2019-12-01/user/getsharedaccesstoken
[创建用户]: /rest/api/apimanagement/2019-12-01/user/createorupdate
[调用订阅 REST API]: /rest/api/apimanagement/2019-12-01/subscription/createorupdate
[Next steps]: #next-steps
[下文提供了示例代码]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png
