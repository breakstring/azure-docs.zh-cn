---
title: 设置使用 Twitter 帐户的注册和登录
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C，为 应用程序中的客户提供通过 Twitter 帐户注册与登录的功能。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/27/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 65d3badc02efbb02df50189885c28a8abe851415
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2021
ms.locfileid: "99050441"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置通过 Twitter 帐户注册与登录

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]
::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>先决条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-application"></a>创建应用程序

若要在 Azure AD B2C 中为使用 Twitter 帐户的用户登录，需要创建 Twitter 应用程序。 如果还没有 Twitter 帐户，可以在注册 [https://twitter.com/signup](https://twitter.com/signup) 。 还需要 [申请开发人员帐户](https://developer.twitter.com/en/apply/user.html)。 有关详细信息，请参阅 [应用访问权限](https://developer.twitter.com/en/apply-for-access)。

1. 用你的 Twitter 帐户凭据登录到 [Twitter 开发人员门户](https://developer.twitter.com/portal/projects-and-apps) 。
1. 在 " **独立应用**" 下，选择 " **+ 创建应用**"。
1. 输入 **应用名称**，然后选择 " **完成**"。
1. 复制 " **应用程序密钥**" 和 " **API 密钥** 密钥" 的值。  同时使用这两种方法将 Twitter 配置为租户中的标识提供者。 
1. 在 " **设置应用**" 下，选择 " **应用设置**"。
1. 在 "**身份验证设置**" 下，选择 "**编辑**"
    1. 选择 " **启用腿 OAuth** " 复选框。
    1. 选中 " **从用户请求电子邮件地址** " 复选框。
    1. 对于 **回调 url**，请输入 `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp` 。 将 `your-tenant` 替换为你的租户的名称，并将 `your-user-flow-Id` 替换为你的用户流的标识符。 例如，`b2c_1a_signup_signin_twitter`。 输入租户名称和用户流 id 时，请使用所有小写字母，即使它们是在 Azure AD B2C 中使用大写字母定义的也是如此。
    1. 对于 " **网站 URL**"，请输入 `https://your-tenant.b2clogin.com` 。 将 `your-tenant` 替换为租户的名称。 例如，`https://contosob2c.b2clogin.com`。
    1. 输入 **服务条款** 的 URL，例如 `http://www.contoso.com/tos` 。 策略 URL 是你维护的一个页面，用于为你的应用程序提供条款和条件。
    1. 输入 **隐私策略** 的 URL，例如 `http://www.contoso.com/privacy` 。 策略 URL 是继续提供应用程序的隐私信息的页面。
    1. 选择“保存”。

::: zone pivot="b2c-user-flow"

## <a name="configure-twitter-as-an-identity-provider"></a>将 Twitter 配置为标识提供程序

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用包含 Azure AD B2C 租户的目录，方法是选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。
1. 选择 " **标识提供者**"，然后选择 " **Twitter**"。
1. 输入“名称”。 例如， *Twitter*。
1. 对于 " **客户端 ID**"，请输入之前创建的 Twitter 应用程序的 *API 密钥* 。
1. 对于 **客户端密码**，请输入你记录的 *API 密钥机密* 。
1. 选择“保存”。

## <a name="add-twitter-identity-provider-to-a-user-flow"></a>将 Twitter 标识提供者添加到用户流 

1. 在 Azure AD B2C 租户中，选择“用户流”  。
1. 选择要添加 Twitter 标识提供者的用户流。
1. 在 **社交标识提供者** 下，选择 " **Twitter**"。
1. 选择“保存”。
1. 若要测试策略，请选择 " **运行用户流**"。
1. 对于 " **应用程序**"，请选择前面注册的名为 *testapp1-template.json* 的 web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 单击 "**运行用户流**"

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>创建策略密钥

你需要存储前面在 Azure AD B2C 租户中记录的机密密钥。

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 请确保使用的是包含 Azure AD B2C 租户的目录。 选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录。
3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
4. 在“概述”页上选择“标识体验框架”。
5. 选择“策略密钥”，然后选择“添加”。
6. 对于“选项”，请选择 `Manual`。
7. 输入策略密钥的 **名称**。 例如，`TwitterSecret`。 前缀 `B2C_1A_` 会自动添加到密钥名称。
8. 在“机密”中，输入前面记录的应用程序机密。
9. 在“密钥用法”处选择 `Encryption`。
10. 单击“创建”。

## <a name="configure-twitter-as-an-identity-provider"></a>将 Twitter 配置为标识提供程序

若要允许用户使用 Twitter 帐户登录，需要将该帐户定义为声明提供程序，该提供程序 Azure AD B2C 可以通过终结点进行通信。 该终结点将提供一组声明，Azure AD B2C 使用这些声明来验证特定的用户是否已完成身份验证。

可以通过在策略的扩展文件中将 Twitter 帐户添加到 **ClaimsProvider** 元素，将该帐户定义为声明提供程序。

1. 打开 *TrustFrameworkExtensions.xml*。
2. 找到 **ClaimsProviders** 元素。 如果该元素不存在，请在根元素下添加它。
3. 如下所示添加新的 **ClaimsProvider**：

    ```xml
    <ClaimsProvider>
      <Domain>twitter.com</Domain>
      <DisplayName>Twitter</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Twitter-OAuth1">
          <DisplayName>Twitter</DisplayName>
          <Protocol Name="OAuth1" />
          <Metadata>
            <Item Key="ProviderName">Twitter</Item>
            <Item Key="authorization_endpoint">https://api.twitter.com/oauth/authenticate</Item>
            <Item Key="access_token_endpoint">https://api.twitter.com/oauth/access_token</Item>
            <Item Key="request_token_endpoint">https://api.twitter.com/oauth/request_token</Item>
            <Item Key="ClaimsEndpoint">https://api.twitter.com/1.1/account/verify_credentials.json?include_email=true</Item>
            <Item Key="ClaimsResponseFormat">json</Item>
            <Item Key="client_id">Your Twitter application API key</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_TwitterSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. 将 **client_id** 的值替换为前面记录的 *API 密钥机密* 。
5. 保存文件。

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAuth1" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

[!INCLUDE [active-directory-b2c-test-relying-party-policy](../../includes/active-directory-b2c-test-relying-party-policy-user-journey.md)]


::: zone-end
