---
title: 在自定义策略中为 SAML 颁发者定义技术配置文件
titleSuffix: Azure AD B2C
description: 在 Azure Active Directory B2C 的自定义策略中为安全断言标记语言 (SAML) 令牌颁发者定义技术配置文件。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b4fb7c6fb3bbf02e5f1aba25c868e4a44e8507dd
ms.sourcegitcommit: ac7029597b54419ca13238f36f48c053a4492cb6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2020
ms.locfileid: "96309624"
---
# <a name="define-a-technical-profile-for-a-saml-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>在 Azure Active Directory B2C 自定义策略中定义 SAML 令牌颁发者的技术配置文件

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) 在处理每个身份验证流时颁发多种安全令牌。 SAML 令牌颁发者的技术配置文件发出返回给信赖方应用（服务提供者）的 SAML 令牌。 通常，此技术配置文件是用户旅程中的最后一个业务流程步骤。

## <a name="protocol"></a>协议

“Protocol”元素的“Name”属性必须设置为 `SAML2`。 将 **OutputTokenFormat** 元素设置为 `SAML2`。

以下示例演示了 `Saml2AssertionIssuer` 的技术配置文件：

```xml
<TechnicalProfile Id="Saml2AssertionIssuer">
  <DisplayName>Token Issuer</DisplayName>
  <Protocol Name="SAML2"/>
  <OutputTokenFormat>SAML2</OutputTokenFormat>
  <Metadata>
    <Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_SAML</Item>
    <Item Key="TokenNotBeforeSkewInSeconds">600</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
    <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
  </CryptographicKeys>
  <InputClaims/>
  <OutputClaims/>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer"/>
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>输入、输出和保存声明

**InputClaims**、**OutputClaims** 和 **PersistClaims** 元素为空或不存在。 **InutputClaimsTransformations** 和 **OutputClaimsTransformations** 元素也不存在。

## <a name="metadata"></a>元数据

| Attribute | 必选 | 说明 |
| --------- | -------- | ----------- |
| IssuerUri | 否 | SAML 响应中出现的颁发者名称。 该值应与信赖方应用中配置的名称相同。 |
| XmlSignatureAlgorithm | 否 | Azure AD B2C 用于对 SAML 断言请求进行签名的方法。 可能的值：`Sha256`、`Sha384`、`Sha512` 或 `Sha1`。 确保在两端配置具有相同值的签名算法。 仅使用证书支持的算法。 若要配置 SAML 响应，请参阅 [信赖方 SAML 元数据](relyingparty.md#metadata)|
|TokenNotBeforeSkewInSeconds| 否| 以整数形式为标记有效期开始时间的时间戳指定倾斜。 此数字越大，有效期开始的时间相对于为信赖方发出声明的时间就越早。 例如，当 TokenNotBeforeSkewInSeconds 设置为 60 秒时，如果令牌是在 UTC 13:05:10 颁发的，则该令牌从 UTC 13:04:10 起有效。 默认值为 0。 最大值为 3600（1 小时）。 |
|TokenLifeTimeInSeconds| 否| 指定 SAML 断言的生存期。 此值以秒为单位，NotBefore 值为 refernced。默认值为300秒 (5 分钟) 。 |


## <a name="cryptographic-keys"></a>加密密钥

CryptographicKeys 元素包含以下属性：

| Attribute | 必需 | 说明 |
| --------- | -------- | ----------- |
| MetadataSigning | 是 | X509 证书（RSA 密钥集），用于对 SAML 元数据进行签名。 Azure AD B2C 使用此密钥对元数据进行签名。 |
| SamlMessageSigning| 是| 指定 X509 证书（RSA 密钥集），用于对 SAML 消息进行签名。 Azure AD B2C 使用此密钥对发送到信赖方的响应 `<samlp:Response>` 进行签名。|

## <a name="session-management"></a>会话管理

用于在信赖方应用、`UseTechnicalProfileForSessionManagement` 元素的属性之间配置 Azure AD B2C SAML 会话，请参考 [SamlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider) SSO 会话。

## <a name="next-steps"></a>后续步骤

有关使用 SAML 颁发者技术配置文件的示例，请参阅以下文章：

- [在 Azure AD B2C 中注册 SAML 应用程序](connect-with-saml-service-providers.md)

