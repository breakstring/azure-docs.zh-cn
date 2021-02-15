---
title: Predicates 和 PredicateValidations
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C 中的自定义策略防止将格式不正确的数据添加到 Azure AD B2C 租户。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 46f04c55b40d4f1bdbbf5fd55eb648d1d3294056
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97108410"
---
# <a name="predicates-and-predicatevalidations"></a>Predicates 和 PredicateValidations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Predicates 和 PredicateValidations 元素可用于执行验证过程，以确保向 Azure Active Directory B2C (Azure AD B2C) 租户仅输入正确格式的数据。

以下图示显示了元素之间的关系：

![显示谓词和谓词验证关系的示意图](./media/predicates/predicates.png)

## <a name="predicates"></a>谓词

Predicate 元素定义基本验证，用以检查声明类型的值并返回 `true` 或 `false`。 可通过使用指定的 Method 元素和一组与该方法相关的 Parameter 元素来完成验证。 例如，谓词可以检查字符串声明值的长度是否在指定的最小和最大参数范围内，或者字符串声明值是否包含字符集。 如果检查失败，UserHelpText 元素将为用户提供一条错误消息。 UserHelpText 元素的值可以使用[语言自定义](localization.md)进行本地化。

**Predicates** 元素必须紧跟在 [BuildingBlocks](buildingblocks.md) 元素中的 **ClaimsSchema** 元素之后。

Predicates 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| Predicate | 1:n | 谓词列表。 |

Predicate 元素包含以下属性：

| 属性 | 必需 | 说明 |
| --------- | -------- | ----------- |
| ID | 是 | 用于谓词的标识符。 其他元素可以在策略中使用此标识符。 |
| 方法 | 是 | 用于验证的方法类型。 可能的值：[IsLengthRange](#islengthrange)、[MatchesRegex](#matchesregex)、[IncludesCharacters](#includescharacters) 或 [IsDateRange](#isdaterange)。  |
| HelpText | 否 | 检查失败时向用户发送的错误消息。 此字符串可以使用[语言自定义](localization.md)进行本地化 |

Predicate 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| UserHelpText | 0:1 | （已弃用）检查失败时向用户显示的错误消息。 |
| parameters | 1:1 | 用于字符串验证的方法类型参数。 |

Parameters 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| 参数 | 1:n | 用于字符串验证的方法类型参数。 |

Parameter 元素包含以下属性：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| ID | 1:1 | 该参数的标识符。 |

### <a name="predicate-methods"></a>谓词方法

#### <a name="islengthrange"></a>IsLengthRange

IsLengthRange 方法检查字符串声明值的长度是否在指定的最小和最大参数的范围内。 谓词元素支持以下参数：

| 参数 | 必需 | 说明 |
| ------- | ----------- | ----------- |
| 最大值 | 是 | 可输入的最大字符数。 |
| 最小值 | 是 | 必须输入的最小字符数。 |


下面的示例说明 IsLengthRange 方法使用参数 `Minimum` 和 `Maximum` 指定字符串的长度范围：

```xml
<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange" HelpText="The password must be between 8 and 64 characters.">
  <Parameters>
    <Parameter Id="Minimum">8</Parameter>
    <Parameter Id="Maximum">64</Parameter>
  </Parameters>
</Predicate>
```

#### <a name="matchesregex"></a>MatchesRegex

MatchesRegex 方法检查字符串声明值是否与正则表达式相匹配。 谓词元素支持以下参数：

| 参数 | 必需 | 说明 |
| ------- | ----------- | ----------- |
| RegularExpression | 是 | 要匹配的正则表达式模式。 |

下面的示例说明 `MatchesRegex` 方法使用参数 `RegularExpression` 指定正则表达式：

```xml
<Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be numbers only.">
  <Parameters>
    <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
  </Parameters>
</Predicate>
```

#### <a name="includescharacters"></a>IncludesCharacters

IncludesCharacters 方法检查字符串声明值是否包含字符集。 谓词元素支持以下参数：

| 参数 | 必需 | 说明 |
| ------- | ----------- | ----------- |
| CharacterSet | 是 | 可输入的字符集。 例如，小写字符 `a-z`、大写字符 `A-Z`、数字 `0-9`，或 `@#$%^&amp;*\-_+=[]{}|\\:',?/~"();!` 等一系列符号。 |

下面的示例说明 `IncludesCharacters` 方法使用参数 `CharacterSet` 指定字符集：

```xml
<Predicate Id="Lowercase" Method="IncludesCharacters" HelpText="a lowercase letter">
  <Parameters>
    <Parameter Id="CharacterSet">a-z</Parameter>
  </Parameters>
</Predicate>
```

#### <a name="isdaterange"></a>IsDateRange

IsDateRange 方法检查日期声明值是否在指定的最小和最大参数范围内。 谓词元素支持以下参数：

| 参数 | 必需 | 说明 |
| ------- | ----------- | ----------- |
| 最大值 | 是 | 可输入的最大日期。 日期格式遵循 `yyyy-mm-dd` 约定或 `Today`。 |
| 最小值 | 是 | 可输入的最小日期。 日期格式遵循 `yyyy-mm-dd` 约定或 `Today`。|

下面的示例说明 `IsDateRange` 方法使用参数 `Minimum` 和 `Maximum` 指定格式为 `yyyy-mm-dd` 和 `Today` 的日期范围。

```xml
<Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 1970-01-01 and today.">
  <Parameters>
    <Parameter Id="Minimum">1970-01-01</Parameter>
    <Parameter Id="Maximum">Today</Parameter>
  </Parameters>
</Predicate>
```

## <a name="predicatevalidations"></a>PredicateValidations

尽管谓词定义了根据声明类型进行检查的验证，但是 PredicateValidations 会对一组谓词进行分组，以构成可应用于声明类型的用户输入验证。 每个 PredicateValidation 元素均包含一组 PredicateGroup 元素，其中包含一组指向 Predicate 的 PredicateReference 元素。 为了通过验证，声明的值应通过所有 PredicateGroup 下的任何谓词的全部测试，及其包含的一组 PredicateReference 元素。

**PredicateValidations** 元素必须紧跟在 [BuildingBlocks](buildingblocks.md) 元素中的 **Predicates** 元素之后。

```xml
<PredicateValidations>
  <PredicateValidation Id="">
    <PredicateGroups>
      <PredicateGroup Id="">
        <UserHelpText></UserHelpText>
        <PredicateReferences MatchAtLeast="">
          <PredicateReference Id="" />
          ...
        </PredicateReferences>
      </PredicateGroup>
      ...
    </PredicateGroups>
  </PredicateValidation>
...
</PredicateValidations>
```

PredicateValidations 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| PredicateValidation | 1:n | 谓词验证列表。 |

PredicateValidation 元素包含以下属性：

| 属性 | 必需 | 说明 |
| --------- | -------- | ----------- |
| ID | 是 | 用于谓词验证的标识符。 ClaimType 元素可以在策略中使用此标识符。 |

PredicateValidation 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| PredicateGroups | 1:n | 谓词组列表。 |

PredicateGroups 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| PredicateGroup | 1:n | 谓词列表。 |

PredicateGroup 元素包含以下属性：

| 属性 | 必需 | 说明 |
| --------- | -------- | ----------- |
| ID | 是 | 用于谓词组的标识符。  |

PredicateGroup 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| UserHelpText | 0:1 |  谓词的说明，可以帮助用户了解应键入哪些值。 |
| PredicateReferences | 1:n | 谓词引用的列表。 |

PredicateReferences 元素包含以下属性：

| 属性 | 必需 | 说明 |
| --------- | -------- | ----------- |
| MatchAtLeast | 否 | 指定该值针对要接受的输入必须至少匹配多个谓词定义。 如果未指定，则该值必须与所有谓词定义匹配。 |

PredicateReferences 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| PredicateReference | 1:n | 对谓词的引用。 |

PredicateReference 元素包含以下属性：

| 属性 | 必需 | 说明 |
| --------- | -------- | ----------- |
| ID | 是 | 用于谓词验证的标识符。  |


## <a name="configure-password-complexity"></a>配置密码复杂性

使用 Predicates 和 PredicateValidationsInput，可以在创建帐户时控制用户提供的密码的复杂性要求。 默认情况下，Azure AD B2C 使用强密码。 此外，Azure AD B2C 还支持用于控制客户可以使用的密码复杂性的配置选项。 你可以使用这些谓词元素来定义密码的复杂性：

- IsLengthBetween8And64 使用 `IsLengthRange` 方法，验证密码必须介于 8 到 64 个字符之间。
- Lowercase 使用 `IncludesCharacters` 方法，验证密码包含一个小写字母。
- Uppercase 使用 `IncludesCharacters` 方法，验证密码包含一个大写字母。
- Number 使用 `IncludesCharacters` 方法，验证密码包含一个数字。
- Symbol 使用 `IncludesCharacters` 方法验证密码是否包含几个符号字符之一。
- PIN 使用 `MatchesRegex` 方法，验证密码仅包含数字。
- AllowedAADCharacters 使用 `MatchesRegex` 方法，验证提供了仅限密码的无效字符。
- DisallowedWhitespace 使用 `MatchesRegex` 方法，验证密码不以空格字符开头或结尾。

```xml
<Predicates>
  <Predicate Id="IsLengthBetween8And64" Method="IsLengthRange" HelpText="The password must be between 8 and 64 characters.">
    <Parameters>
      <Parameter Id="Minimum">8</Parameter>
      <Parameter Id="Maximum">64</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Lowercase" Method="IncludesCharacters" HelpText="a lowercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">a-z</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Uppercase" Method="IncludesCharacters" HelpText="an uppercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">A-Z</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Number" Method="IncludesCharacters" HelpText="a digit">
    <Parameters>
      <Parameter Id="CharacterSet">0-9</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Symbol" Method="IncludesCharacters" HelpText="a symbol">
    <Parameters>
      <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\\:',.?/`~"();!</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be numbers only.">
    <Parameters>
      <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="AllowedAADCharacters" Method="MatchesRegex" HelpText="An invalid character was provided.">
    <Parameters>
      <Parameter Id="RegularExpression">(^([0-9A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~"();! ]|(\.(?!@)))+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="DisallowedWhitespace" Method="MatchesRegex" HelpText="The password must not begin or end with a whitespace character.">
    <Parameters>
      <Parameter Id="RegularExpression">(^\S.*\S$)|(^\S+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>
```

定义基本验证后，你可以将它们组合在一起，并创建一组可以在策略中使用的密码策略：

- SimplePassword 验证 DisallowedWhitespace、AllowedAADCharacters 和 IsLengthBetween8And64
- StrongPassword 验证 DisallowedWhitespace、AllowedAADCharacters、IsLengthBetween8And64。 最后一组 `CharacterClasses` 运行一组额外的谓词，其中 `MatchAtLeast` 设置为 3。 用户密码长度必须为 8 到 16 个字符，且必须包含下列其中的三类字符：小写、大写、数字或符号。
- CustomPassword 仅验证 DisallowedWhitespace 和 AllowedAADCharacters。 因此，只要字符有效，用户可以提供任意长度的任何密码。

```xml
<PredicateValidations>
  <PredicateValidation Id="SimplePassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences>
          <PredicateReference Id="IsLengthBetween8And64" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>

  <PredicateValidation Id="StrongPassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
       </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences>
          <PredicateReference Id="IsLengthBetween8And64" />
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

  <PredicateValidation Id="CustomPassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

在声明类型中，添加 PredicateValidationReference 元素，并将标识符指定为 SimplePassword、StrongPassword 或 CustomPassword 等谓词验证中的一个。

```xml
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>Enter password</AdminHelpText>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
  <PredicateValidationReference Id="StrongPassword" />
</ClaimType>
```

下面显示了当 Azure AD B2C 显示错误消息时元素的组织方式：

![谓词和 PredicateGroup 密码复杂性示例的示意图](./media/predicates/predicates-pass.png)

## <a name="configure-a-date-range"></a>配置日期范围

借助 Predicates 和 PredicateValidations 元素，你可以通过使用 `DateTimeDropdown` 来控制 UserInputType 的最小和最大日期值。 如下例所示，可使用 `IsDateRange` 方法创建 Predicate 并提供最小和最大参数。

```xml
<Predicates>
  <Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 01-01-1980 and today.">
    <Parameters>
      <Parameter Id="Minimum">1980-01-01</Parameter>
      <Parameter Id="Maximum">Today</Parameter>
    </Parameters>
  </Predicate>
</Predicates>
```

添加引用 `DateRange` 谓词的 PredicateValidation。

```xml
<PredicateValidations>
  <PredicateValidation Id="CustomDateRange">
    <PredicateGroups>
      <PredicateGroup Id="DateRangeGroup">
        <PredicateReferences>
          <PredicateReference Id="DateRange" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

在声明类型中，添加 PredicateValidationReference 元素，并将标识符指定为 `CustomDateRange`。

```xml
<ClaimType Id="dateOfBirth">
  <DisplayName>Date of Birth</DisplayName>
  <DataType>date</DataType>
  <AdminHelpText>The user's date of birth.</AdminHelpText>
  <UserHelpText>Your date of birth.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
  <PredicateValidationReference Id="CustomDateRange" />
</ClaimType>
 ```

## <a name="next-steps"></a>后续步骤

- 了解如何使用谓词验证 [Azure Active Directory B2C 中的自定义策略配置密码复杂性](password-complexity.md) 。