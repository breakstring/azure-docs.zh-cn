---
title: TrustFrameworkPolicy - Azure Active Directory B2C | Microsoft Docs
description: 在 Azure Active Directory B2C 中指定自定义策略的 TrustFrameworkPolicy 元素。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/31/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a87b4c6b845006a9f9f3cf82815277c67c09bef0
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/13/2021
ms.locfileid: "98178834"
---
# <a name="trustframeworkpolicy"></a>TrustFrameworkPolicy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

自定义策略以一个或多个采用 XML 格式的文件表示，这些文件在分层链中相互引用。 XML 元素定义策略的元素，例如声明架构、声明转换、内容定义、声明提供程序、技术配置文件、用户旅程和业务流程步骤。 每个策略文件在策略文件的顶级 **TrustFrameworkPolicy** 元素中定义。

```xml
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="mytenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
  ...
```


**TrustFrameworkPolicy** 元素包含以下属性：

| Attribute | 必选 | 说明 |
|---------- | -------- | ----------- |
| PolicySchemaVersion | 是 | 用于执行策略的架构版本。 值必须是 `0.3.0.0` |
| TenantObjectId | 否 | Azure Active Directory B2C (Azure AD B2C) 租户的唯一对象标识符。 |
| TenantId | 是 | 此策略所属的租户的唯一标识符。 |
| PolicyId | 是 | 策略的唯一标识符。 此标识符必须带有 *B2C_1A_* 前缀 |
| PublicPolicyUri | 是 | 策略的 URI，它是租户 ID 和策略 ID 的组合。 |
| DeploymentMode | 否 | 可能的值：`Production` 或 `Development`。 `Production` 为默认值。 使用此属性来调试策略。 有关详细信息，请参阅 [收集日志](troubleshoot-with-application-insights.md)。 |
| UserJourneyRecorderEndpoint | 否 | 用于日志记录的终结点。 如果该属性存在，则该值必须设置为 `urn:journeyrecorder:applicationinsights` 。 有关详细信息，请参阅 [收集日志](troubleshoot-with-application-insights.md)。 |


以下示例演示如何指定 **TrustFrameworkPolicy** 元素：

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="mytenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkBase"
   PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

**TrustFrameworkPolicy** 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| BasePolicy| 0:1| 基本策略的标识符。 |
| [BuildingBlocks](buildingblocks.md) | 0:1 | 策略的构建基块。 |
| [ClaimsProviders](claimsproviders.md) | 0:1 | 声明提供程序的集合。 |
| [UserJourneys](userjourneys.md) | 0:1 | 用户旅程的集合。 |
| [RelyingParty](relyingparty.md) | 0:1 | 信赖方策略的定义。 |

若要从另一个策略继承某个策略，必须在策略文件的 **TrustFrameworkPolicy** 元素下声明 **BasePolicy** 元素。 **BasePolicy** 元素是对从中派生此策略的基本策略的引用。

**BasePolicy** 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | --------|
| TenantId | 1:1 | Azure AD B2C 租户的标识符。 |
| `PolicyId` | 1:1 | 父策略的标识符。 |


以下示例演示如何指定基本策略。 此 **B2C_1A_TrustFrameworkExtensions** 策略派生自 **B2C_1A_TrustFrameworkBase** 策略。

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="mytenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkExtensions"
   PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkExtensions">

  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkBase</PolicyId>
  </BasePolicy>
  ...
</TrustFrameworkPolicy>
```

