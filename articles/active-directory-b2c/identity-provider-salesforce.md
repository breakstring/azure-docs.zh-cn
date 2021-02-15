---
title: 设置使用 Salesforce 帐户的注册和登录
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C 向应用程序中的 Salesforce 帐户提供注册和登录。
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
ms.openlocfilehash: 0981687b03344daf7a447cc4d9e50f0923341340
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98952285"
---
# <a name="set-up-sign-up-and-sign-in-with-a-salesforce-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置使用 Salesforce 帐户的注册和登录

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>必备条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="create-a-salesforce-application"></a>创建 Salesforce 应用程序

若要使用 Azure Active Directory B2C (Azure AD B2C) 中的 Salesforce 帐户登录用户，需要在 Salesforce [应用管理器](https://login.salesforce.com/)中创建应用程序。 有关详细信息，请参阅 [配置基本连接的应用设置](https://help.salesforce.com/articleView?id=connected_app_create_basics.htm)和 [启用适用于 API 集成的 OAuth 设置](https://help.salesforce.com/articleView?id=connected_app_create_api_integration.htm)

1. [登录到 Salesforce](https://login.salesforce.com/)。
1. 从菜单中选择 " **安装**"。
1.  展开 " **应用**"，然后选择 " **应用管理器**"。
1. 选择 "新建" " **连接的应用**"。
1. 在 " **基本信息**" 下，输入：
    1. **连接的应用名称** -连接的应用名称显示在应用管理器和其应用启动器磁贴上。 该名称在您的组织内必须是唯一的。 
    1. **API 名称** 
    1. **联系人电子邮件** -Salesforce 的联系人电子邮件
1. 在 " **API () 启用 Oauth 设置**" 下，选择 "**启用 oauth 设置**"
    1. 在 " **回调 URL**" 中，输入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` 。 将 `your-tenant-name` 替换为租户的名称。 输入租户名称时，必须全部使用小写字母，即使租户是使用大写字母在 Azure AD B2C 中定义的，也是如此。
    1. 在 " **所选 OAuth 作用域**" 中，选择 " **(id、配置文件、电子邮件、地址、电话) 访问你的基本信息**，并 **允许 (openid) 访问你的唯一标识符**。
    1. 选择 " **需要 Web 服务器流的机密**"。
1. 选择 **配置 ID 令牌** 
    1. 将 **令牌设置为 "有效** " 5 分钟。
    1. 选择 " **包括标准声明**"。
1. 单击“保存”。
1. 复制 " **使用者密钥** " 和 " **使用者机密**" 的值。 需要将 Salesforce 配置为租户中的标识提供者。 **客户端密钥** 是重要的安全凭据。

::: zone pivot="b2c-user-flow"

## <a name="configure-salesforce-as-an-identity-provider"></a>将 Salesforce 配置为标识提供者

1. 确保你正在使用包含 Azure AD B2C 租户的目录。 在顶部菜单中选择“目录 + 订阅”筛选器，然后选择包含 Azure AD B2C 租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
1. 选择“标识提供程序”，然后选择“新建 OpenID Connect 提供程序” 。
1. 输入“名称”。 例如，输入 *Salesforce*。
1. 对于 " **元数据 url**"，请输入 [Salesforce OpenID connect 配置文档](https://help.salesforce.com/articleView?id=remoteaccess_using_openid_discovery_endpoint.htm)的 url。 对于沙盒，将 login.salesforce.com 替换为 test.salesforce.com。 对于社区，login.salesforce.com 将替换为社区 URL，如 username.force.com/.well-known/openid-configuration。 URL 必须为 HTTPS。

    ```
    https://login.salesforce.com/.well-known/openid-configuration
    ```

1. 对于“客户端 ID”，输入之前记录的应用程序 ID。
1. 对于“客户端机密”，请输入之前记录的客户端机密。
1. 对于“范围”，请输入 `openid id profile email`。
1. 对于“响应类型”和“响应模式” ，请保留默认值。
1. （可选）对于“域提示”，请输入 `contoso.com`。 有关详细信息，请参阅[使用 Azure Active Directory B2C 设置直接登录](direct-signin.md#redirect-sign-in-to-a-social-provider)。
1. 在“标识提供者声明映射”下，选择以下声明：

    - **用户 ID**： *sub*
    - 显示名称：name
    - 给定名称：given_name
    - 姓氏：family_name
    - **电子邮件**： *电子邮件*

1. 选择“保存”。

## <a name="add-salesforce-identity-provider-to-a-user-flow"></a>将 Salesforce 标识提供者添加到用户流 

1. 在 Azure AD B2C 租户中，选择“用户流”  。
1. 单击要添加 Salesforce 标识提供者的用户流。
1. 在 **社交标识提供者** 下，选择 " **Salesforce**"。
1. 选择“保存”。
1. 若要测试策略，请选择 " **运行用户流**"。
1. 对于 " **应用程序**"，请选择前面注册的名为 *testapp1-template.json* 的 web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 单击 "**运行用户流**"

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>创建策略密钥

你需要存储前面在 Azure AD B2C 租户中记录的客户端机密。

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 请确保使用的是包含 Azure AD B2C 租户的目录。 选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录。
3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
4. 在“概述”页上选择“标识体验框架”。
5. 选择“策略密钥”，然后选择“添加”。
6. 对于“选项”，请选择 `Manual`。
7. 输入策略密钥的 **名称**。 例如，`SalesforceSecret`。 前缀 `B2C_1A_` 会自动添加到密钥名称。
8. 在“机密”中，输入前面记录的应用程序机密。
9. 在“密钥用法”处选择 `Signature`。
10. 单击“创建”。

## <a name="configure-salesforce-as-an-identity-provider"></a>将 Salesforce 配置为标识提供者

若要使用户能够使用 Salesforce 帐户登录，需要将该帐户定义为声明提供程序，Azure AD B2C 可通过终结点进行通信。 该终结点将提供一组声明，Azure AD B2C 使用这些声明来验证特定的用户是否已完成身份验证。

可以通过在策略的扩展文件中将 Salesforce 帐户添加到 **ClaimsProvider** 元素，将该帐户定义为声明提供程序。

1. 打开 *TrustFrameworkExtensions.xml*。
2. 找到 **ClaimsProviders** 元素。 如果该元素不存在，请在根元素下添加它。
3. 如下所示添加新的 **ClaimsProvider**：

    ```xml
    <ClaimsProvider>
      <Domain>salesforce.com</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Salesforce-OpenIdConnect">
          <DisplayName>Salesforce</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="METADATA">https://login.salesforce.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid id profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">Your Salesforce application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_SalesforceSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="salesforce.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. **元数据** 设置为 [Salesforce OpenID connect 配置文档](https://help.salesforce.com/articleView?id=remoteaccess_using_openid_discovery_endpoint.htm)的 URL。 对于沙盒，将 login.salesforce.com 替换为 test.salesforce.com。 对于社区，login.salesforce.com 将替换为社区 URL，如 username.force.com/.well-known/openid-configuration。 URL 必须为 HTTPS。
5. 将 **client_id** 设置为应用程序注册中的应用程序 ID。
6. 保存文件。

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="Salesforce-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

[!INCLUDE [active-directory-b2c-test-relying-party-policy](../../includes/active-directory-b2c-test-relying-party-policy-user-journey.md)]

::: zone-end

## <a name="next-steps"></a>后续步骤

了解如何将 [Salesforce 令牌传递给应用程序](idp-pass-through-user-flow.md)。
