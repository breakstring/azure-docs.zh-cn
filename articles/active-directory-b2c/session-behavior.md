---
title: 配置会话行为 - Azure Active Directory B2C | Microsoft Docs
description: 了解如何在 Azure Active Directory B2C 中配置会话行为。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/14/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: ad9bd8dec94660d94cf3a106d31dafdad06f47a8
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584504"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中配置会话行为

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

当用户在 Azure Active Directory B2C (Azure AD B2C) 中登录到应用程序时，单一登录 (SSO) 可以提高安全性和便利性。 本文介绍 Azure AD B2C 中使用的单一登录方法，并在配置策略时帮助你选择最适合的 SSO 方法。

借助单一登录，用户可以使用单个帐户登录一次，然后即可访问多个应用程序。 应用程序可以是 Web、移动或单页应用程序，不管它们的平台或域名如何。

当用户最初登录到应用程序时，Azure AD B2C 会保留一个基于 Cookie 的会话。 收到后续的身份验证请求后，Azure AD B2C 会读取并验证该基于 Cookie 的会话，然后颁发访问令牌，且不提示用户重新登录。 如果基于 Cookie 的会话过期或失效，则系统会提示用户重新登录。  

## <a name="prerequisites"></a>先决条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="azure-ad-b2c-session-overview"></a>Azure AD B2C 会话概述

与 Azure AD B2C 的集成涉及到三种类型的 SSO 会话：

- **Azure AD B2C** - 由 Azure AD B2C 管理的会话
- **联合标识提供程序** -由标识提供程序管理的会话，例如 Facebook、Salesforce 或 Microsoft 帐户
- **应用程序** - 由 Web、移动或单页应用程序管理的会话

![SSO 会话](media/session-behavior/sso-session-types.png)

### <a name="azure-ad-b2c-session"></a>Azure AD B2C 会话 

当用户使用本地帐户或社交帐户成功完成身份验证时，Azure AD B2C 会在用户的浏览器中存储一个基于 Cookie 的会话。 Cookie 存储在 Azure AD B2C 租户域名（例如 `https://contoso.b2clogin.com`）下。

如果用户最初使用联合帐户登录，然后在会话时间窗口（生存时间，简称 TTL）内登录到相同或不同的应用，则 Azure AD B2C 会尝试从联合标识提供者获取新的访问令牌。 如果联合标识提供者会话已过期或失效，则联合标识提供者会提示用户输入其凭据。 如果会话仍处于活动状态（或者用户已使用本地帐户而不是联合帐户登录），则 Azure AD B2C 将为用户授权并消除进一步的提示。

可以配置会话行为，包括会话 TTL，以及 Azure AD B2C 如何在策略和应用程序之间共享会话。

### <a name="federated-identity-provider-session"></a>联合标识提供者会话

社交或企业标识提供者需管理其自己的会话。 Cookie 存储在标识提供者的域名（例如 `https://login.salesforce.com`）下。 Azure AD B2C 不会控制联合标识提供者会话。 会话行为由联合标识提供者确定。 

假设出现了下面这种情景：

1. 用户登录 Facebook 以检查其源。
2. 稍后，用户将打开你的应用程序并启动登录过程。 应用程序将用户重定向到 Azure AD B2C 以完成登录过程。
3. 在 Azure AD B2C 注册或登录页上，用户选择通过其 Facebook 帐户进行登录。 用户被重定向到 Facebook。 如果 Facebook 上存在活动会话，则不会提示用户提供其凭据，并立即重定向到使用 Facebook 令牌 Azure AD B2C。

### <a name="application-session"></a>应用程序会话

可以通过 OAuth 访问、ID 令牌或 SAML 令牌来保护 Web、移动或单页应用程序。 当用户尝试访问应用中某个受保护的资源时，应用会检查应用程序端是否存在活动的会话。 如果不存在应用会话或者会话已过期，则应用会将用户转到 Azure AD B2C 登录页。

应用程序会话可以是存储在应用程序域名（例如 `https://contoso.com`）下的基于 Cookie 的会话。 移动应用程序可能会通过一种不同的方式（但使用类似的方法）存储会话。

## <a name="configure-azure-ad-b2c-session-behavior"></a>配置 Azure AD B2C 会话行为

你可以配置 Azure AD B2C 会话行为，包括：

- **Web 应用会话生存期 (分钟)** -身份验证成功后将 Azure AD B2C 会话 cookie 存储在用户浏览器上的时间量。 可将会话生存时间设置为 15 到 720 分钟的值。

- **Web 应用会话超时** -指示会话如何通过会话生命时间设置或 "使我保持登录" 设置进行扩展。
  - **滚动** - 指示每当用户执行基于 Cookie 的身份验证时都延长会话（默认值）。
  - **绝对** - 指示在指定的时间段后强制用户重新进行身份验证。

- **单一登录配置** -Azure AD B2C 会话可以配置为以下作用域：
  - **租户** - 这是默认设置。 使用此设置允许 B2C 租户中的多个应用和用户流共享相同的用户会话。 例如，一旦用户登录到某个应用程序，就还可以在访问该应用程序时无缝登录到另一个应用程序。
  - **应用程序** - 此设置允许为某个应用程序维持独占式用户会话（独立于其他应用程序）。 例如，如果你希望无论用户是否已登录到 Contoso Groceries，他们都能够登录到 Contoso Pharmacy，则可以使用此设置。
  - **策略** - 此设置为某个用户流维持独占式用户会话（独立于使用它的应用程序）。 例如，如果用户已登录并完成多重身份验证 (MFA) 步骤，那么只要绑定到用户流的会话未过期，该用户就可以访问多个应用程序的具有较高安全性的部分。
  - **已禁用** - 此设置强制用户在每次执行策略时都要运行完整的用户流。
::: zone pivot="b2c-custom-policy"
- **使我保持登录** -通过使用永久性 cookie 延长会话生存期。 在用户关闭并重新打开浏览器后，会话将保持活动状态。 仅当用户注销时，才会撤销会话。“使我保持登录状态”功能仅适用于使用本地帐户进行的登录。 “使我保持登录状态”功能优先于会话生存时间的设置。 如果启用了“使我保持登录状态”功能，并且用户选择了此功能，则此功能将决定会话何时会过期。 
::: zone-end

::: zone pivot="b2c-user-flow"

若要配置会话行为：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 请确保使用包含 Azure AD B2C 租户的目录，方法是选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含 Azure AD B2C 租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
1. 选择“用户流”。
1. 打开之前创建的用户流。
1. 选择“属性”。
1. 根据需要配置 **Web 应用会话生存期（分钟）** 、**Web 应用会话超时**、**单一登录配置** 和 **在注销请求中需要 ID 令牌**。
1. 单击“ **保存**”。

::: zone-end

::: zone pivot="b2c-custom-policy"

若要更改会话行为和 SSO 配置，需要在 [RelyingParty](relyingparty.md) 元素内添加 **UserJourneyBehaviors** 元素。  **UserJourneyBehaviors** 元素必须紧跟在 **DefaultUserJourney** 之后。 UserJourneyBehavors 元素应当如以下示例所示：

```xml
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```

## <a name="enable-keep-me-signed-in-kmsi"></a>启用 "使我保持登录 (KMSI) 

你可以为 web 和本机应用程序的用户启用 "使我保持登录功能"，这些用户在你的 Azure Active Directory B2C (Azure AD B2C) 目录中具有本地帐户。 此功能会向返回到应用程序的用户授予访问权限，而不会提示他们重新输入用户名和密码。 当用户注销时，会撤销此访问权限。

![显示“使我保持登录状态”复选框的示例注册登录页](./media/session-behavior/keep-me-signed-in.png)

用户不应在公用计算机上启用此选项。

### <a name="configure-the-page-identifier"></a>配置页面标识符

若要启用 KMSI，请将内容定义 `DataUri` 元素设置为 [页面标识符](contentdefinitions.md#datauri) `unifiedssp` 和 [页面版本](page-layout.md) *1.1.0* 或更高版本。

1. 打开策略的扩展文件。 例如，<em>`SocialAndLocalAccounts/``TrustFrameworkExtensions.xml`</em>。 此扩展文件是自定义策略初学者包中包含的策略文件之一，你应该已在先决条件中获取 [自定义策略入门](custom-policy-get-started.md)。
1. 搜索 **BuildingBlocks** 元素。 如果该元素不存在，请添加该元素。
1. 将 **ContentDefinitions** 元素添加到策略的 **BuildingBlocks** 元素中。

    自定义策略应类似于以下代码片段：

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

### <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>将元数据添加到自断言技术配置文件

若要将 KMSI 复选框添加到注册和登录页，请将 `setting.enableRememberMe` 元数据设置为 true。 覆盖扩展文件中的 SelfAsserted-LocalAccountSignin 技术配置文件。

1. 找到 ClaimsProviders 元素。 如果该元素不存在，请添加该元素。
1. 将以下声明提供程序添加到 ClaimsProviders 元素：

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <Metadata>
        <Item Key="setting.enableRememberMe">True</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

1. 保存扩展文件。

### <a name="configure-a-relying-party-file"></a>配置信赖方文件

更新用于启动创建的用户旅程的信赖方 (RP) 文件。

1. 打开自定义策略文件。 例如，SignUpOrSignin.xml。
1. 如果它尚不存在，请将一个 `<UserJourneyBehaviors>` 子节点添加到该 `<RelyingParty>` 节点。 它必须紧跟在之后 `<DefaultUserJourney ReferenceId="User journey Id" />` ，例如： `<DefaultUserJourney ReferenceId="SignUpOrSignIn" />` 。
1. 将以下节点添加为 `<UserJourneyBehaviors>` 元素的子级。

    ```xml
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

建议将 SessionExpiryInSeconds 的值设置为短期间 (1200 秒) ，而 KeepAliveInDays 的值可以设置为相对较长的时间段 (30 天) ，如以下示例所示：

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <UserJourneyBehaviors>
    <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
    <SessionExpiryType>Absolute</SessionExpiryType>
    <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
  </UserJourneyBehaviors>
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

::: zone-end

## <a name="sign-out"></a>注销

如果想要从应用程序中注销用户，只是清除应用程序的 Cookie 或者结束与用户的会话是不够的。 必须将用户重定向到 Azure AD B2C 进行注销。否则，用户可能可以在应用程序中重新进行身份验证，且无需再次输入其凭据。

收到注销请求后，Azure AD B2C 将会：

1. 使 Azure AD B2C 基于 Cookie 的会话失效。
::: zone pivot="b2c-user-flow"
2. 尝试从联合标识提供者注销
::: zone-end
::: zone pivot="b2c-custom-policy"
3. 尝试从联合标识提供者注销：
   - OpenId Connect - 如果标识提供者的已知配置终结点指定了 `end_session_endpoint` 位置。
   - OAuth2-如果 [标识提供程序元数据](oauth2-technical-profile.md#metadata) 包含位置，则为 `end_session_endpoint` 。
   - SAML-如果 [标识提供程序元数据](saml-identity-provider-technical-profile.md#metadata) 包含 `SingleLogoutService` 位置。
4. 选择性地从其他应用程序注销。 有关详细信息，请参阅[单一注销](#single-sign-out)部分。

> [!NOTE]
> 您可以通过将标识提供程序的技术配置文件的元数据设置为来禁用从联合标识提供程序中注销 `SingleLogoutEnabled` `false` 。
::: zone-end

注销会清除用户在 Azure AD B2C 中的单一登录状态，但可能不会将用户从其社交标识提供者会话中注销。 如果用户在后续登录期间选择相同的标识提供者，那么他们可以重新进行身份验证，且无需输入其凭据。 如果用户想要从应用程序中注销，则不一定意味着他们要注销其 Facebook 帐户。 但是，如果使用了本地帐户，则用户的会话将正常结束。

::: zone pivot="b2c-custom-policy"

### <a name="single-sign-out"></a>单一登录 

将用户重定向到 Azure AD B2C 注销终结点（适用于 OAuth2 和 SAML 协议）时，Azure AD B2C 将从浏览器中清除该用户的会话。 但是，用户可能在其他使用 Azure AD B2C 进行身份验证的应用程序中仍处于已登录状态。 要使这些应用程序能够同时注销用户，Azure AD B2C 会将 HTTP GET 请求发送到用户当前登录到的所有应用程序的已注册 `LogoutUrl`。

应用程序必须通过清除任何标识用户的会话并返回 `200` 响应来响应此请求。 若要在应用程序中支持单一注销，必须在应用程序代码中实现 `LogoutUrl`。 

若要支持单一注销，JWT 和 SAML 的令牌颁发者技术配置文件必须指定以下内容：

- 协议名称，例如 `<Protocol Name="OpenIdConnect" />`
- 对会话技术配置文件的引用，例如 `UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />`。

下面的示例演示了单一注销的 JWT 和 SAML 令牌颁发者：

```xml
<ClaimsProvider>
  <DisplayName>Local Account SignIn</DisplayName>
  <TechnicalProfiles>
    <!-- JWT Token Issuer -->
    <TechnicalProfile Id="JwtIssuer">
      <DisplayName>JWT token Issuer</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputTokenFormat>JWT</OutputTokenFormat>
      ...    
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for OIDC based tokens -->
    <TechnicalProfile Id="SM-OAuth-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.OAuthSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>

    <!--SAML token issuer-->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>SAML token issuer</DisplayName>
      <Protocol Name="SAML2" />
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      ...
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

::: zone-end

### <a name="secure-your-logout-redirect"></a>保护注销重定向

注销后，用户将重定向到 `post_logout_redirect_uri` 参数中指定的 URI，而不管为应用程序指定的回复 URL 为何。 但是，如果传递了有效的 `id_token_hint` 并启用了“注销请求中需要 ID 令牌”，则在执行重定向之前，Azure AD B2C 将验证 `post_logout_redirect_uri` 的值是否与应用程序的某个已配置重定向 URI 相匹配。 如果没有为应用程序配置匹配的回复 URL，则会显示一条错误消息，而用户不会重定向。 

::: zone pivot="b2c-user-flow"

若要在注销请求中要求使用 ID 令牌：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 请确保使用包含 Azure AD B2C 租户的目录，方法是选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含 Azure AD B2C 租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
1. 选择“用户流”。
1. 打开之前创建的用户流。
1. 选择“属性”。
1. 启用“注销请求中需要 ID 令牌”。
1. 返回到“Azure AD B2C”。
1. 选择“应用注册”，然后选择自己的应用程序。
1. 选择“身份验证”。
1. 在“注销 URL”文本框中，键入注销后重定向 URI，然后选择“保存” 。

::: zone-end

::: zone pivot="b2c-custom-policy"

若要启用要求在注销请求中提供 ID 令牌，请在 [RelyingParty](relyingparty.md) 元素中添加 **UserJourneyBehaviors** 元素。 然后，将 SingleSignOn 元素的 EnforceIdTokenHintOnLogout 设置为 `true` 。 UserJourneyBehaviors 元素应当如以下示例所示：

```xml
<UserJourneyBehaviors>
  <SingleSignOn Scope="Tenant" EnforceIdTokenHintOnLogout="true"/>
</UserJourneyBehaviors>
```

::: zone-end

配置应用程序注销 URL：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 请确保使用包含 Azure AD B2C 租户的目录，方法是选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含 Azure AD B2C 租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
1. 选择“应用注册”，然后选择自己的应用程序。
1. 选择“身份验证”。
1. 在“注销 URL”文本框中，键入注销后重定向 URI，然后选择“保存” 。

## <a name="next-steps"></a>后续步骤

- 了解如何 [在 Azure AD B2C 中配置标记](configure-tokens.md)。
