---
title: 自定义策略的布尔型声明转换示例
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C 的 Identity Experience Framework (IEF) 架构的布尔型声明转换示例。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/06/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7c292f939339add06168c55236f8666651e4aace
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85201270"
---
# <a name="boolean-claims-transformations"></a>布尔型声明转换

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文提供了在 Azure Active Directory B2C (Azure AD B2C) 中使用 Identity Experience Framework 架构的布尔型声明转换的示例。 有关详细信息，请参阅 [ClaimsTransformations](claimstransformations.md)。

## <a name="andclaims"></a>AndClaims

执行两个布尔型 inputClaims 的 And 运算并使用运算结果设置 outputClaim。

| 项目  | TransformationClaimType  | 数据类型  | 注释 |
|-------| ------------------------ | ---------- | ----- |
| InputClaim | inputClaim1 | boolean | 第一个要评估的 ClaimType。 |
| InputClaim | inputClaim2  | boolean | 第二个要评估的 ClaimType。 |
|OutputClaim | outputClaim | boolean | 调用此声明转换后将生成的 ClaimTypes（true 或 false）。 |

以下声明转换演示如何执行两个布尔型 ClaimTypes 的 And 运算：`isEmailNotExist` 和 `isSocialAccount`。 如果这两个输入声明的值为 `true`，则输出声明 `presentEmailSelfAsserted` 设置为 `true`。 在业务流程步骤中，只有在社交帐户电子邮件为空的情况下，才可以使用前置条件来预设自断言页。

```xml
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="AndClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isEmailNotExist" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isSocialAccount" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentEmailSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example-of-andclaims"></a>AndClaims 示例

- 输入声明：
    - **inputClaim1**: true
    - **inputClaim2**: false
- 输出声明：
    - **outputClaim**: false


## <a name="assertbooleanclaimisequaltovalue"></a>AssertBooleanClaimIsEqualToValue

检查两个声明的布尔值是否相等，如果不等，将引发异常。

| 项目 | TransformationClaimType  | 数据类型  | 注释 |
| ---- | ------------------------ | ---------- | ----- |
| inputClaim | inputClaim | boolean | 要断言的 ClaimType。 |
| InputParameter |valueToCompareTo | boolean | 要比较的值（true 或 false）。 |

AssertBooleanClaimIsEqualToValue 声明转换始终从[验证技术配置文件](validation-technical-profile.md)执行，该文件由[自断言技术配置文件](self-asserted-technical-profile.md)调用。 UserMessageIfClaimsTransformationBooleanValueIsNotEqual 自断言技术配置文件元数据控制向用户显示的技术配置文件。 可以将错误消息[本地化](localization-string-ids.md#claims-transformations-error-messages)。

![AssertStringClaimsAreEqual 执行](./media/boolean-transformations/assert-execution.png)

以下声明转换演示如何检查带 `true` 值的布尔型 ClaimType 的值。 如果 `accountEnabled` ClaimType 的值为 false，则会引发错误消息。

```xml
<ClaimsTransformation Id="AssertAccountEnabledIsTrue" TransformationMethod="AssertBooleanClaimIsEqualToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="accountEnabled" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="valueToCompareTo" DataType="boolean" Value="true" />
  </InputParameters>
</ClaimsTransformation>
```


`login-NonInteractive` 验证技术配置文件会调用 `AssertAccountEnabledIsTrue` 声明转换。

```xml
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

自断言技术配置文件调用验证 login-NonInteractive 技术配置文件。

```xml
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Custom error message if account is disabled.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example-of-assertbooleanclaimisequaltovalue"></a>AssertBooleanClaimIsEqualToValue 示例

- 输入声明：
    - **inputClaim**: false
    - **valueToCompareTo**: true
- 结果：引发错误

## <a name="comparebooleanclaimtovalue"></a>CompareBooleanClaimToValue

检查声明的布尔值是等于 `true` 还是 `false`，并返回压缩结果。

| 项目 | TransformationClaimType  | 数据类型  | 注释 |
| ---- | ------------------------ | ---------- | ----- |
| InputClaim | inputClaim | boolean | 要断言的 ClaimType。 |
| InputParameter |valueToCompareTo | boolean | 要比较的值（true 或 false）。 |
| OutputClaim | compareResult | boolean | 调用此 ClaimsTransformation 后生成的 ClaimType。 |

以下声明转换演示如何检查带 `true` 值的布尔型 ClaimType 的值。 如果 `IsAgeOver21Years` ClaimType 的值等于 `true`，则声明转换返回 `true`，否则返回 `false`。

```xml
<ClaimsTransformation Id="AssertAccountEnabled" TransformationMethod="CompareBooleanClaimToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="IsAgeOver21Years" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="valueToCompareTo" DataType="boolean" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim  ClaimTypeReferenceId="accountEnabled" TransformationClaimType="compareResult"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example-of-comparebooleanclaimtovalue"></a>CompareBooleanClaimToValue 示例

- 输入声明：
    - **inputClaim**: false
- 输入参数：
    - **valueToCompareTo**: true
- 输出声明：
    - **compareResult**: false

## <a name="notclaims"></a>NotClaims

执行布尔型 inputClaim 的 Not 运算并使用运算结果设置 outputClaim。

| 项目 | TransformationClaimType | 数据类型 | 注释 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | boolean | 要运算的声明。 |
| OutputClaim | outputClaim | boolean | 调用此 ClaimsTransformation 后生成的 ClaimType（true 或 false）。 |

使用此声明转换对声明执行逻辑非运算。

```xml
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="NotClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example-of-notclaims"></a>NotClaims 示例

- 输入声明：
    - **inputClaim**: false
- 输出声明：
    - **outputClaim**: true

## <a name="orclaims"></a>OrClaims

执行两个布尔型 inputClaims 的 Or 运算并使用运算结果设置 outputClaim。

| 项目 | TransformationClaimType | 数据类型 | 注释 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | boolean | 第一个要评估的 ClaimType。 |
| InputClaim | inputClaim2 | boolean | 第二个要评估的 ClaimType。 |
| OutputClaim | outputClaim | boolean | 调用此 ClaimsTransformation 后将生成的 ClaimTypes（true 或 false）。 |

以下声明转换演示如何执行两个布尔型 ClaimTypes 的 `Or` 运算。 在业务流程步骤中，如果其中一个声明的值为 `true`，则可以使用前置条件来预设自断言页。

```xml
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="OrClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedNotExists" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentTOSSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example-of-orclaims"></a>OrClaims 示例

- 输入声明：
    - **inputClaim1**: true
    - **inputClaim2**: false
- 输出声明：
    - **outputClaim**: true
