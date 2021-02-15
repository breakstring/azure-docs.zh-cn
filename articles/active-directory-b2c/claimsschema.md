---
title: ClaimsSchema - Azure Active Directory B2C | Microsoft Docs
description: 在 Azure Active Directory B2C 中指定自定义策略的 ClaimsSchema 元素。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2ff43408cfa6d95dbd5a235a950269c47d57a416
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2020
ms.locfileid: "97654024"
---
# <a name="claimsschema"></a>ClaimsSchema

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

ClaimsSchema  元素定义了可以引用为策略的一部分的声明类型。 声明架构是发出声明的位置。 声明可以是名字、姓氏、显示名称、电话号码等。 ClaimsSchema 元素包含 ClaimType  元素的列表。 ClaimType  元素包含 Id  属性，它是声明名称。

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="Id">
      <DisplayName>Surname</DisplayName>
      <DataType>string</DataType>
      <DefaultPartnerClaimTypes>
        <Protocol Name="OAuth2" PartnerClaimType="family_name" />
        <Protocol Name="OpenIdConnect" PartnerClaimType="family_name" />
        <Protocol Name="SAML2" PartnerClaimType="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" />
      </DefaultPartnerClaimTypes>
      <UserHelpText>Your surname (also known as family name or last name).</UserHelpText>
      <UserInputType>TextBox</UserInputType>
```

## <a name="claimtype"></a>ClaimType

ClaimType  元素包含以下属性：

| 属性 | 必须 | 说明 |
| --------- | -------- | ----------- |
| ID | 是 | 用于声明类型的标识符。 其他元素可以在策略中使用此标识符。 |

ClaimType  元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| DisplayName | 1:1 | 在各种屏幕上向用户显示的标题。 可将值[本地化](localization.md)。 |
| 数据类型 | 1:1 | 声明类型。 |
| DefaultPartnerClaimTypes | 0:1 | 用于指定协议的合作伙伴默认声明类型。 可以覆盖 InputClaim  或 OutputClaim  元素中指定的 PartnerClaimType  中的值。 将此元素用于指定协议的默认名称。  |
| Mask | 0:1 | 显示声明时可以应用的掩码字符的可选字符串。 例如，电话号码 324-232-4343 可以屏蔽为 XXX-XXX-4343。 |
| UserHelpText | 0:1 | 可帮助用户了解其用途的声明类型的说明。 可将值[本地化](localization.md)。 |
| UserInputType | 0:1 | 应在手动输入声明类型的声明数据时可供用户使用的输入控制的类型。 请参阅稍后在此页中定义的用户输入类型。 |
| AdminHelpText | 0:1 | 声明类型的说明，有助于管理员了解其用途。 |
| 限制 | 0:1 | 此声明的值限制，如正则表达式 (Regex) 或可接受值的列表。 可将值[本地化](localization.md)。 |
PredicateValidationReference| 0:1 | 对 **PredicateValidationsInput** 元素的引用。 **PredicateValidationReference** 元素可用于执行验证过程，以确保仅输入格式正确的数据。 有关详细信息，请参阅 [Predicates](predicates.md)。 |



### <a name="datatype"></a>数据类型

**DataType** 元素支持以下值：

| 类型 | 说明 |
| ------- | ----------- |
|boolean|表示一个布尔（`true` 或 `false`）值。|
|date| 表示某个时刻，通常表示为某天的日期。 日期的值遵循 ISO 8601 约定。|
|dateTime|表示某个时刻，通常以日期和当天的时间表示。 日期的值遵循 ISO 8601 约定。|
|duration|表示以年、月、日、小时、分钟和秒为单位的时间间隔。 其格式为 `PnYnMnDTnHnMnS`，其中 `P` 表示正值，`N` 表示负值。 `nY` 是后跟文本 `Y` 的年数。 `nMo` 是后跟文本 `Mo` 的月数。 `nD` 是后跟文本 `D` 的天数。 示例：`P21Y` 表示 21 年。 `P1Y2Mo` 表示一年零两个月。 `P1Y2Mo5D` 表示一年零两个月五天。  `P1Y2M5DT8H5M620S` 表示一年零两个月五天八小时五分钟二十秒。  |
|phoneNumber|表示电话号码。 |
|int| 表示 -2,147,483,648 到 2,147,483,647 之间的数字|
|long| 表示 -9,223,372,036,854,775,808 到 9,223,372,036,854,775,807 之间的数字 |
|string| 将文本表示为 UTF-16 代码单元的序列。|
|stringCollection|表示 `string` 的集合。|
|userIdentity| 表示用户标识。|
|userIdentityCollection|表示 `userIdentity` 的集合。|

### <a name="defaultpartnerclaimtypes"></a>DefaultPartnerClaimTypes

DefaultPartnerClaimTypes  可能包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| 协议 | 1:n | 具有其默认合作伙伴声明类型名称的协议的列表。 |

Protocol  元素包含以下属性：

| 属性 | 必须 | 说明 |
| --------- | -------- | ----------- |
| 名称 | 是 | Azure AD B2C 支持的有效协议的名称。 可能的值包括：OAuth1、OAuth2、SAML2、OpenIdConnect。 |
| PartnerClaimType | 是 | 要使用的声明类型名称。 |

在以下示例中，当标识体验框架与 SAML2 标识提供者或信赖方应用交互时，surname  声明将映射到 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`，如果使用 OpenIdConnect 和 OAuth2，该声明将映射到 `family_name`。

```xml
<ClaimType Id="surname">
  <DisplayName>Surname</DisplayName>
  <DataType>string</DataType>
  <DefaultPartnerClaimTypes>
    <Protocol Name="OAuth2" PartnerClaimType="family_name" />
    <Protocol Name="OpenIdConnect" PartnerClaimType="family_name" />
    <Protocol Name="SAML2" PartnerClaimType="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" />
  </DefaultPartnerClaimTypes>
</ClaimType>
```

因此，Azure AD B2C 发布的 JWT 令牌会忽略 `family_name`，而不是 ClaimType 名称 surname  。

```json
{
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  "auth_time": 1535013501,
  "given_name": "David",
  "family_name": "Williams",
  "name": "David Williams",
}
```

### <a name="mask"></a>Mask

Mask  元素包含以下属性：

| 属性 | 必须 | 说明 |
| --------- | -------- | ----------- |
| `Type` | 是 | 声明掩码的类型。 可能的值：`Simple` 或 `Regex`。 `Simple` 值表示简单的文本掩码应用于字符串声明的前导部分。 `Regex` 值指示正则表达式总体上应用于字符串声明。  如果指定了 `Regex` 值，则还必须通过要使用的正则表达式定义可选属性。 |
| `Regex` | 否 | 如果将 **`Type`** 设置为 `Regex`，请指定要使用的正则表达式。

以下示例将配置具有 `Simple` 掩码的 PhoneNumber  声明：

```xml
<ClaimType Id="PhoneNumber">
  <DisplayName>Phone Number</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Simple">XXX-XXX-</Mask>
  <UserHelpText>Your telephone number.</UserHelpText>
</ClaimType>
```

标识体验框架呈现电话号码的同时隐藏了前六个数字：

![浏览器中显示的电话号码声明，其中前六位数字由 X 掩码](./media/claimsschema/mask.png)

以下示例将配置具有 `Regex` 掩码的 AlternateEmail  声明：

```xml
<ClaimType Id="AlternateEmail">
  <DisplayName>Please verify the secondary email linked to your account</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Regex" Regex="(?&lt;=.).(?=.*@)">*</Mask>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```

标识体验框架仅呈现电子邮件地址和电子邮件域名的第一个字母：

![浏览器中显示的电子邮件声明，其中字符由星号掩码](./media/claimsschema/mask-regex.png)


### <a name="restriction"></a>限制

Restriction  元素可能包含以下属性：

| 属性 | 必须 | 说明 |
| --------- | -------- | ----------- |
| MergeBehavior | 否 | 用于将枚举值与具有相同标识符的父策略中的 ClaimType 合并的方法。 覆盖基本策略中指定的声明时，请使用此属性。 可能的值：`Append`、`Prepend` 或 `ReplaceAll`。 `Append` 值是应追加到父策略中指定的集合的末尾的数据集合。 `Prepend` 值是应在父策略中指定的集合之前添加的数据集合。 `ReplaceAll` 值是应忽略的父策略中指定的数据集合。 |

Restriction  元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| 枚举 | 1:n | 可让用户在用户界面中为声明选择的可用选项，例如下拉列表中的值。 |
| 模式 | 1:1 | 要使用的正则表达式。 |

#### <a name="enumeration"></a>枚举

**Enumeration** 元素定义了可供用户在用户界面中选择声明的可用选项，例如 `CheckboxMultiSelect`、`DropdownSingleSelect` 或 `RadioSingleSelect` 中的值。 另外，可以使用 [LocalizedCollections](localization.md#localizedcollections) 元素定义和本地化可用选项。 若要从声明 **Enumeration** 集合中查找项目，请使用 [GetMappedValueFromLocalizedCollection](string-transformations.md#getmappedvaluefromlocalizedcollection) 声明转换。

Enumeration  元素包含以下属性：

| 属性 | 必须 | 说明 |
| --------- | -------- | ----------- |
| 文本 | 是 | 在用户界面中向用户显示的此选项的显示字符串。 |
|Value | 是 | 与此选项关联的声明值。 |
| SelectByDefault | 否 | 指示默认情况下是否应在 UI 中选择此选项。 可能的值：True 或 False。 |

以下示例将配置默认值设置为 `New York` 的  “城市”下拉列表声明：

```xml
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="New York" Value="new-york" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

默认值设置为纽约的城市下拉列表：

![在浏览器中呈现并显示默认值的下拉控件](./media/claimsschema/dropdownsingleselect.png)

### <a name="pattern"></a>模式

Pattern  元素可以包含以下属性：

| 属性 | 必须 | 说明 |
| --------- | -------- | ----------- |
| RegularExpression | 是 | 此类型的声明必须匹配才能有效的正则表达式。 |
| HelpText | 否 | 正则表达式检查失败时向用户显示的错误消息。 |

以下示例将配置具有正则表达式输入验证和帮助文本的 email  声明：

```xml
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <DefaultPartnerClaimTypes>
  <Protocol Name="OpenIdConnect" PartnerClaimType="email" />
  </DefaultPartnerClaimTypes>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
  <Restriction>
    <Pattern RegularExpression="^[a-zA-Z0-9.+!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$" HelpText="Please enter a valid email address." />
  </Restriction>
</ClaimType>
```

标识体验框架呈现具有电子邮件格式输入验证的电子邮件地址声明：

![显示由正则表达式限制触发的错误消息的 TextBox](./media/claimsschema/pattern.png)

### <a name="userinputtype"></a>UserInputType

Azure AD B2C 支持各种用户输入类型，例如在手动输入声明类型的声明数据时可以使用的文本框、密码和下拉列表。 使用 [自断言技术配置文件](self-asserted-technical-profile.md)和 [显示控件](display-controls.md)从用户收集信息时，必须指定 **UserInputType**。

**UserInputType** 元素可用的用户输入类型：

| UserInputType | 支持的 ClaimType | 说明 |
| --------- | -------- | ----------- |
|CheckboxMultiSelect| `string` |多选下拉框。 声明值表示为所选值的逗号分隔字符串。 |
|DateTimeDropdown | `date`, `dateTime` |用于选择日、月和年的下拉框。 |
|DropdownSingleSelect |`string` |单选下拉框。 声明值为所选值。|
|EmailBox | `string` |电子邮件输入字段。 |
|Paragraph | `boolean`、`date`、`dateTime`、`duration`、`int`、`long`、`string`|一个仅显示段落标记中文本的字段。 |
|密码 | `string` |密码文本框。|
|RadioSingleSelect |`string` | 单选按钮的集合。 声明值为所选值。|
|Readonly | `boolean`、`date`、`dateTime`、`duration`、`int`、`long`、`string`| 只读文本框。 |
|TextBox |`boolean`、`int`、`string` |单行文本框。 |


#### <a name="textbox"></a>TextBox

TextBox  用户输入类型用于提供单行文本框。

![显示在声明类型中指定的属性的 TextBox](./media/claimsschema/textbox.png)

```xml
<ClaimType Id="displayName">
  <DisplayName>Display Name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your display name.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

#### <a name="emailbox"></a>EmailBox

EmailBox  用户输入类型用于提供基本电子邮件输入字段。

![显示在声明类型中指定的属性的 EmailBox](./media/claimsschema/emailbox.png)

```xml
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>EmailBox</UserInputType>
  <Restriction>
    <Pattern RegularExpression="^[a-zA-Z0-9.+!#$%&amp;'+^_`{}~-]+(?:\.[a-zA-Z0-9!#$%&amp;'+^_`{}~-]+)*@(?:[a-zA-Z0-9](?:[a-zA-Z0-9-]*[a-zA-Z0-9])?\.)+[a-zA-Z0-9](?:[a-zA-Z0-9-]*[a-zA-Z0-9])?$" HelpText="Please enter a valid email address." />
  </Restriction>
</ClaimType>
```

#### <a name="password"></a>密码

Password  用户输入类型用于记录用户输入的密码。

![使用具有 password 的声明类型](./media/claimsschema/password.png)

```xml
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
</ClaimType>
```

#### <a name="datetimedropdown"></a>DateTimeDropdown

DateTimeDropdown  用户输入类型用于提供一组用来选择日、月和年的下拉列表。 可以使用 Predicates 和 PredicateValidations 元素来控制最小日期值和最大日期值。 有关详细信息，请参阅 [Predicates 和 PredicateValidations](predicates.md) 的  “配置日期范围”部分。

![使用具有 datetimedropdown 的声明类型](./media/claimsschema/datetimedropdown.png)

```xml
<ClaimType Id="dateOfBirth">
  <DisplayName>Date Of Birth</DisplayName>
  <DataType>date</DataType>
  <UserHelpText>The date on which you were born.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
</ClaimType>
```

#### <a name="radiosingleselect"></a>RadioSingleSelect

RadioSingleSelect  用户输入类型用于提供允许用户选择一个选项的单选按钮集合。

![使用具有 radiodsingleselect 的声明类型](./media/claimsschema/radiosingleselect.png)

```xml
<ClaimType Id="color">
  <DisplayName>Preferred color</DisplayName>
  <DataType>string</DataType>
  <UserInputType>RadioSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Blue" Value="Blue" SelectByDefault="false" />
    <Enumeration Text="Green " Value="Green" SelectByDefault="false" />
    <Enumeration Text="Orange" Value="Orange" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

#### <a name="dropdownsingleselect"></a>DropdownSingleSelect

DropdownSingleSelect  用户输入类型用于提供允许用户选择一个选项的下拉框。

![使用具有 dropdownsingleselect 的声明类型](./media/claimsschema/dropdownsingleselect.png)

```xml
<ClaimType Id="city">
  <DisplayName>City where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="New York" Value="new-york" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

#### <a name="checkboxmultiselect"></a>CheckboxMultiSelect

CheckboxMultiSelect  用户输入类型用于提供允许用户选择多个选项的复选框集合。

![使用具有 checkboxmultiselect 的声明类型](./media/claimsschema/checkboxmultiselect.png)

```xml
<ClaimType Id="languages">
  <DisplayName>Languages you speak</DisplayName>
  <DataType>string</DataType>
  <UserInputType>CheckboxMultiSelect</UserInputType>
  <Restriction>
    <Enumeration Text="English" Value="English" SelectByDefault="true" />
    <Enumeration Text="France " Value="France" SelectByDefault="false" />
    <Enumeration Text="Spanish" Value="Spanish" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

#### <a name="readonly"></a>Readonly

Readonly  用户输入类型用于提供要显示声明和值的只读字段。

![使用具有 readonly 的声明类型](./media/claimsschema/readonly.png)

```xml
<ClaimType Id="membershipNumber">
  <DisplayName>Membership number</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your membership number (read only)</UserHelpText>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```


#### <a name="paragraph"></a>Paragraph

Paragraph  用户输入类型用于提供仅在段落标记中显示文本的字段。  例如，&lt;p&gt;文本&lt;/p&gt;。 自断言技术配置文件的 **Paragraph** 用户输入类型 `OutputClaim` 必须将 `Required` 属性设置为 `false`（默认值）。

![使用具有 paragraph 的声明类型](./media/claimsschema/paragraph.png)

```xml
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>A claim responsible for holding response messages to send to the relying party</AdminHelpText>
  <UserHelpText>A claim responsible for holding response messages to send to the relying party</UserHelpText>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cannot sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```
