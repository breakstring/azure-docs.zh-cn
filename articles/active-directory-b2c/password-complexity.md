---
title: 配置密码复杂性要求
titleSuffix: Azure AD B2C
description: 如何配置由 Azure Active Directory B2C 中的使用者提供的密码复杂性要求。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: f9748d0d278375029fc9875f5b36674d19ad871a
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2021
ms.locfileid: "98058967"
---
# <a name="configure-complexity-requirements-for-passwords-in-azure-active-directory-b2c"></a>配置 Azure Active Directory B2C 中的密码复杂性要求

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) 支持更改由最终用户在创建帐户时提供的密码复杂性要求。 默认情况下，Azure AD B2C 使用 **强** 密码。 此外，Azure AD B2C 还支持用于控制客户可以使用的密码复杂性的配置选项。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="password-rule-enforcement"></a>密码规则强制实施

在注册或密码重置期间，最终用户必须提供符合复杂性规则的密码。 根据用户流要求，需强制实施密码复杂性规则。 可能有一个用户流在注册期间需要一个四位数的 pin，而另一个用户流在注册期间需要一个八字符的字符串。 例如，可以使用针对成人（而非儿童）的不同密码复杂性的用户流。

在登录期间绝不会强制实施密码复杂性。 登录时不会提示用户更改密码，因为它不符合当前的复杂性要求。

可在以下类型的用户流中配置密码复杂性：

- 注册或登录用户流
- 密码重置用户流

如果使用自定义策略，可以（[在自定义策略中配置密码复杂性](password-complexity.md)）。

## <a name="configure-password-complexity"></a>配置密码复杂性

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在门户工具栏中选择“目录 + 订阅”图标，然后选择包含 Azure AD B2C 租户的目录。
3. 在 Azure 门户中，搜索并选择“Azure AD B2C”  。
4. 选择“用户流”。
2. 选择一个用户流，然后单击“属性”  。
3. 在“密码复杂性”  下，将此用户流的密码复杂性更改为“简单”  、“强”  或“自定义”  。

### <a name="comparison-chart"></a>比较图表

| 复杂性 | 说明 |
| --- | --- |
| 简单 | 为至少 8 到 64 个字符的密码。 |
| 强 | 为至少 8 到 64 个字符的密码。 它需要 4 个小写字母、大写字母、数字或符号中的 3 个。 |
| “自定义” | 此选项提供了对密码复杂性规则的最大控制。  可以配置自定义长度。  还可以接受仅为数字的密码 (pin)。 |

## <a name="custom-options"></a>自定义选项

### <a name="character-set"></a>字符集

允许你接受仅为数字 (pin) 或完整的字符集。

- “仅数字”  仅限输入数字 (0-9) 密码。
- “所有”  则允许任何字母、数字或符号。

### <a name="length"></a>Length

允许你控制密码的长度要求。

- 最小长度  必须至少为 4。
- **最大长度** 必须大于或等于最小长度，最大长度为256个字符。

### <a name="character-classes"></a>字符类

允许你控制密码中使用的不同字符类型。

- **4 选 2：小写字符、大写字符、数字 (0-9)、符号** 确保密码包含至少两种字符类型。 例如，数字和小写字符。
- **4 选 3：小写字符、大写字符、数字 (0-9)、符号** 确保密码包含至少三种字符类型。 例如，数字、小写字符和大写字符。
- **全部 4 个：小写字符、大写字符、数字 (0-9)、符号** 确保密码包含所有字符类型。

    > [!NOTE]
    > 要求“全部 4 个”  可能会对最终用户造成困扰。 某些研究表明此要求不会改善密码熵。 请参阅 [NIST 密码指南](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="password-predicate-validation"></a>密码谓词验证

若要配置密码复杂性，请 `newPassword` 使用对 `reenterPassword` [谓词验证](predicates.md#predicatevalidations)的引用替代和[声明类型](claimsschema.md)。 PredicateValidations 元素对一组谓词进行分组，以形成可应用于声明类型的用户输入验证。 打开策略的扩展文件， 例如，<em>`SocialAndLocalAccounts/``TrustFrameworkExtensions.xml`</em>。

1. 搜索 [BuildingBlocks](buildingblocks.md) 元素。 如果该元素不存在，请添加该元素。
1. 找到 [ClaimsSchema](claimsschema.md) 元素。 如果该元素不存在，请添加该元素。
1. 将 `newPassword` 和 `reenterPassword` 声明添加到 **ClaimsSchema** 元素。

    ```xml
    <ClaimType Id="newPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    <ClaimType Id="reenterPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    ```

1. [谓词](predicates.md) 定义用于检查声明类型的值并返回 true 或 false 的基本验证。 通过使用指定的方法元素以及与方法相关的一组参数来完成验证。 将以下谓词添加到 **BuildingBlocks** 元素中，紧跟在元素的关闭之后 `</ClaimsSchema>` ：

    ```xml
    <Predicates>
      <Predicate Id="LengthRange" Method="IsLengthRange">
        <UserHelpText>The password must be between 6 and 64 characters.</UserHelpText>
        <Parameters>
          <Parameter Id="Minimum">6</Parameter>
          <Parameter Id="Maximum">64</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Lowercase" Method="IncludesCharacters">
        <UserHelpText>a lowercase letter</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">a-z</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Uppercase" Method="IncludesCharacters">
        <UserHelpText>an uppercase letter</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">A-Z</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Number" Method="IncludesCharacters">
        <UserHelpText>a digit</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">0-9</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Symbol" Method="IncludesCharacters">
        <UserHelpText>a symbol</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\\:',.?/`~"();!</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    ```

1. 将以下谓词验证添加到 **BuildingBlocks** 元素，紧跟在元素关闭之后 `</Predicates>` ：

    ```xml
    <PredicateValidations>
      <PredicateValidation Id="CustomPassword">
        <PredicateGroups>
          <PredicateGroup Id="LengthGroup">
            <PredicateReferences MatchAtLeast="1">
              <PredicateReference Id="LengthRange" />
            </PredicateReferences>
          </PredicateGroup>
          <PredicateGroup Id="CharacterClasses">
            <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
            <PredicateReferences MatchAtLeast="3">
              <PredicateReference Id="Lowercase" />
              <PredicateReference Id="Uppercase" />
              <PredicateReference Id="Number" />
              <PredicateReference Id="Symbol" />
            </PredicateReferences>
          </PredicateGroup>
        </PredicateGroups>
      </PredicateValidation>
    </PredicateValidations>
    ```

## <a name="disable-strong-password"></a>禁用强密码 

以下技术配置文件是 [Active Directory 技术配置文件，这些配置文件](active-directory-technical-profile.md)将数据读写到 Azure Active Directory 中。 覆盖扩展文件中的这些技术配置文件。 用于 `PersistedClaims` 禁用强密码策略。 找到 **ClaimsProviders** 元素。  添加以下声明提供程序，如下所示：

```xml
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
      </PersistedClaims>
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWritePasswordUsingObjectId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
      </PersistedClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

保存策略文件。

## <a name="test-your-policy"></a>测试策略

### <a name="upload-the-files"></a>上传文件

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 请确保使用包含 Azure AD B2C 租户的目录，方法是选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录。
3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
4. 选择“标识体验框架”。
5. 在“自定义策略”页上，单击“上传策略”。
6. 选择 **"覆盖策略（如果存在**）"，然后搜索并选择 " *TrustFrameworkExtensions.xml* 文件。
7. 单击“上载” 。

### <a name="run-the-policy"></a>运行策略

1. 打开注册或登录策略。 例如，*B2C_1A_signup_signin*。
2. 对于“应用程序”  ，选择你之前注册的应用程序。 若要查看令牌，“回复 URL”应当显示 `https://jwt.ms`。
3. 单击 " **立即运行**"。
4. 选择“立即注册”，输入电子邮件地址，并输入新密码。 密码限制中会显示相关指导。 完成输入用户信息，然后单击“创建”。 应看到返回的令牌的内容。

## <a name="next-steps"></a>后续步骤

- 了解如何 [在 Azure Active Directory B2C 中配置密码更改](add-password-change-policy.md)。
- 了解有关 IEF 引用中的 [谓词](predicates.md) 和 [PredicateValidations](predicates.md#predicatevalidations) 元素的详细信息。


::: zone-end
