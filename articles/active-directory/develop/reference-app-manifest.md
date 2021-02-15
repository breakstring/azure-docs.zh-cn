---
title: 了解 Azure Active Directory 应用清单
titleSuffix: Microsoft identity platform
description: 详细介绍 Azure Active Directory 应用清单，该清单表示 Azure AD 租户中的应用程序标识配置，并方便实现 OAuth 授权、许可体验和其他功能。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 02/02/2021
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: sureshja
ms.openlocfilehash: 47215070893c6fefd42554fdf4514e4aa8b45047
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/10/2021
ms.locfileid: "100103339"
---
# <a name="azure-active-directory-app-manifest"></a>Azure Active Directory 应用清单

应用程序清单包含 Microsoft 标识平台中的某个应用程序对象的所有属性的定义。 它还充当用于更新应用程序对象的机制。 有关应用程序实体及其架构的详细信息，请参阅[图形 API 应用程序实体文档](/graph/api/resources/application)。

可以通过 Azure 门户或者使用 [REST API](/graph/api/resources/application) 或 [PowerShell](/powershell/module/azuread#applications) 以编程方式配置应用的属性。 但是，在某些情况下，需要编辑应用清单来配置应用的属性。 这些方案包括：

* 如果已将应用注册为 Azure AD 多租户和个人 Microsoft 帐户，则不能在 UI 中更改支持的 Microsoft 帐户。 而是必须使用应用程序清单编辑器来更改支持的帐户类型。
* 若要定义应用支持的权限和角色，必须修改应用程序清单。

## <a name="configure-the-app-manifest"></a>配置应用清单

若要配置应用程序清单，请执行以下操作：

1. 转到 <a href="https://portal.azure.com/" target="_blank">Azure 门户</a>。 搜索并选择 **Azure Active Directory** 服务。
1. 选择“应用注册” 。
1. 选择要配置的应用。
1. 在应用的“概览”页中，选择“清单”部分。  此时会打开一个基于 Web 的清单编辑器，可在其中编辑门户中的清单。 （可选）可以选择“下载”以在本地编辑清单，然后使用“上传”将清单重新应用于应用程序。 

## <a name="manifest-reference"></a>清单参考

本部分介绍在应用程序清单中找到的属性。

### <a name="id-attribute"></a>ID 属性

| 键 | 值类型 |
| :--- | :--- |
| id | String |

应用在目录中的唯一标识符。 此 ID 不是用于在任何协议事务中标识应用的标识符。 用于引用目录查询中的对象。

示例：

```json
    "id": "f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd",
```

### <a name="accesstokenacceptedversion-attribute"></a>accessTokenAcceptedVersion 属性

| 键 | 值类型 |
| :--- | :--- |
| accessTokenAcceptedVersion | 可为 Null 的 Int32 |

指定资源所需的访问令牌版本。 此参数会更改独立于用于请求访问令牌的终结点或客户端生成的 JWT 的版本和格式。

使用的端点 v1.0 或 v2.0 由客户端选择，仅影响 id_tokens 的版本。 资源需要显式配置 `accesstokenAcceptedVersion` 以指示受支持的访问令牌格式。

`accesstokenAcceptedVersion` 的可能值为 1、2 或为 null。 如果值为 null，则此参数默认为 1，这对应于 v1.0 终结点。

如果 `signInAudience` 为 `AzureADandPersonalMicrosoftAccount`，则值必须是 `2`。

示例：

```json
    "accessTokenAcceptedVersion": 2,
```

### <a name="addins-attribute"></a>addIns 属性

| 密钥 | 值类型 |
| :--- | :--- |
| addIns | 集合 |

定义自定义行为，供消耗型服务在特定上下文中调用应用。 例如，呈现文件流的应用程序可以设置其“FileHandler”功能的 `addIns` 属性。 Microsoft 365 等服务可以通过此参数在用户正在处理的文档上下文中调用该应用程序。

示例：

```json
    "addIns": [
       {
        "id": "968A844F-7A47-430C-9163-07AE7C31D407",
        "type":" FileHandler",
        "properties": [
           {
              "key": "version",
              "value": "2"
           }
        ]
       }
    ],
```

### <a name="allowpublicclient-attribute"></a>allowPublicClient 属性

| 密钥 | 值类型 |
| :--- | :--- |
| allowPublicClient | Boolean |

指定回退应用程序类型。 默认情况下，Azure AD 基于 replyUrlsWithType 推断应用程序类型。 某些情况下，Azure AD 无法确定客户端应用类型。 例如，这样的一种情况是 [ROPC](https://tools.ietf.org/html/rfc6749#section-4.3) 流，其中发生了没有 URL 重定向的 HTTP 请求。 在这种情况下，Azure AD 将基于此属性的值解释应用程序类型。 如果此值设置为 true，则回退应用程序类型设置为公共客户端，例如在移动设备上运行的已安装应用。 默认值为 false，这意味着，回退应用程序类型为机密，例如 Web 应用。

示例：

```json
    "allowPublicClient": false,
```

### <a name="appid-attribute"></a>appId 属性

| 密钥 | 值类型 |
| :--- | :--- |
| appId | String |

指定由 Azure AD 分配给应用的应用唯一标识符。

示例：

```json
    "appId": "601790de-b632-4f57-9523-ee7cb6ceba95",
```

### <a name="approles-attribute"></a>appRoles 属性

| 密钥 | 值类型 |
| :--- | :--- |
| appRoles | 集合 |

指定应用可以声明的角色集合。 可将这些角色分配给用户、组或服务主体。 有关更多示例和信息，请参阅[在应用程序中添加应用角色并在令牌中接收它们](howto-add-app-roles-in-azure-ad-apps.md)。

示例：

```json
    "appRoles": [
        {
           "allowedMemberTypes": [
               "User"
           ],
           "description": "Read-only access to device information",
           "displayName": "Read Only",
           "id": "601790de-b632-4f57-9523-ee7cb6ceba95",
           "isEnabled": true,
           "value": "ReadOnly"
        }
    ],
```

### <a name="errorurl-attribute"></a>errorUrl 属性

| 密钥 | 值类型 |
| :--- | :--- |
| errorUrl | String |

不受支持。

### <a name="groupmembershipclaims-attribute"></a>groupMembershipClaims 属性

| 键 | 值类型 |
| :--- | :--- |
|groupMembershipClaims | String |

配置应用所需的用户访问令牌或 OAuth 2.0 访问令牌中颁发的 `groups` 声明。 若要设置此属性，请使用以下有效的字符串值之一：

- `"None"`
- `"SecurityGroup"`（适用于安全组和 Azure AD 角色）
- `"ApplicationGroup"` (此选项仅包括分配给应用程序的组) 
- `"DirectoryRole"` (获取用户所属的 Azure AD 目录角色) 
- `"All"` (此项将获取已登录用户是) 成员的所有安全组、通讯组和 Azure AD 目录角色。

示例：

```json
    "groupMembershipClaims": "SecurityGroup",
```

### <a name="optionalclaims-attribute"></a>optionalClaims 属性

| 密钥 | 值类型 |
| :--- | :--- |
| optionalClaims | String |

此特定应用的安全令牌服务在令牌中返回的可选声明。

目前，同时支持个人帐户和 Azure AD（已通过应用注册门户注册）的应用无法使用可选声明。 但是，使用 v2.0 终结点仅为 Azure AD 注册的应用可以获取它们在清单中请求的可选声明。 有关详细信息，请参阅[可选声明](active-directory-optional-claims.md)。

示例：

```json
    "optionalClaims": null,
```


### <a name="identifieruris-attribute"></a>identifierUris 属性

| 密钥 | 值类型 |
| :--- | :--- |
| identifierUris | String Array |

用户定义的 URI，用于在 Web 应用的 Azure AD 租户中唯一标识该应用程序；如果应用是多租户的，则用于在已验证的自定义域中唯一标识该应用。

示例：

```json
    "identifierUris": "https://MyRegisteredApp",
```

### <a name="informationalurls-attribute"></a>informationalUrls 属性

| 密钥 | 值类型 |
| :--- | :--- |
| informationalUrls | String |

指定应用服务条款和隐私声明的链接。 服务条款和隐私声明通过用户同意体验展示给用户。 有关详细信息，请参阅[如何：为已注册的 Azure AD 应用添加服务条款和隐私声明](howto-add-terms-of-service-privacy-statement.md)。

示例：

```json
    "informationalUrls": {
        "termsOfService": "https://MyRegisteredApp/termsofservice",
        "support": "https://MyRegisteredApp/support",
        "privacy": "https://MyRegisteredApp/privacystatement",
        "marketing": "https://MyRegisteredApp/marketing"
    },
```

### <a name="keycredentials-attribute"></a>keyCredentials 属性

| 密钥 | 值类型 |
| :--- | :--- |
| keyCredentials | 集合 |

包含对应用所分配的凭据、基于字符串的共享机密和 X.509 证书的引用。 在请求访问令牌时，可使用这些凭据（如果应用充当客户端而不是资源）。

示例：

```json
    "keyCredentials": [
        {
           "customKeyIdentifier":null,
           "endDate":"2018-09-13T00:00:00Z",
           "keyId":"<guid>",
           "startDate":"2017-09-12T00:00:00Z",
           "type":"AsymmetricX509Cert",
           "usage":"Verify",
           "value":null
        }
    ],
```

### <a name="knownclientapplications-attribute"></a>knownClientApplications 属性

| 密钥 | 值类型 |
| :--- | :--- |
| knownClientApplications | String Array |

如果解决方案包含两个部分：客户端应用和自定义 Web API 应用，则该值用于捆绑许可。 如果在此值中输入客户端应用的 appID，则用户只需许可该客户端应用一次。 Azure AD 会知道许可客户端意味着隐式许可 Web API， 并会自动为客户端和 Web API 同时预配服务主体。 客户端和 Web API 应用都必须在同一个租户中注册。

示例：

```json
    "knownClientApplications": ["f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"],
```

### <a name="logourl-attribute"></a>logoUrl 属性

| 密钥 | 值类型 |
| :--- | :--- |
| logoUrl | String |

只读值，指向已在门户中上传的徽标的 CDN URL。

示例：

```json
    "logoUrl": "https://MyRegisteredAppLogo",
```

### <a name="logouturl-attribute"></a>logoutUrl 属性

| 密钥 | 值类型 |
| :--- | :--- |
| logoutUrl | String |

用于注销应用的 URL。

示例：

```json
    "logoutUrl": "https://MyRegisteredAppLogout",
```

### <a name="name-attribute"></a>name 属性

| 密钥 | 值类型 |
| :--- | :--- |
| name | String |

应用的显示名称。

示例：

```json
    "name": "MyRegisteredApp",
```

### <a name="oauth2allowimplicitflow-attribute"></a>oauth2AllowImplicitFlow 属性

| 密钥 | 值类型 |
| :--- | :--- |
| oauth2AllowImplicitFlow | Boolean |

指定此 Web 应用是否可以请求 OAuth2.0 隐式流访问令牌。 默认值为 false。 此标志用于基于浏览器的应用，例如 JavaScript 单页应用。 若要了解详细信息，请在目录中输入 `OAuth 2.0 implicit grant flow`，并查看有关隐式流的主题。

示例：

```json
    "oauth2AllowImplicitFlow": false,
```

### <a name="oauth2allowidtokenimplicitflow-attribute"></a>oauth2AllowIdTokenImplicitFlow 属性

| 密钥 | 值类型 |
| :--- | :--- |
| oauth2AllowIdTokenImplicitFlow | Boolean |

指定此 Web 应用是否可以请求 OAuth2.0 隐式流 ID 令牌。 默认值为 false。 此标志用于基于浏览器的应用，例如 JavaScript 单页应用。

示例：

```json
    "oauth2AllowIdTokenImplicitFlow": false,
```

### <a name="oauth2permissions-attribute"></a>oauth2Permissions 属性

| 密钥 | 值类型 |
| :--- | :--- |
| oauth2Permissions | 集合 |

指定 Web API（资源）应用向客户端应用公开的 OAuth 2.0 权限范围集合。 在许可期间，可将这些权限范围授予客户端应用。

示例：

```json
    "oauth2Permissions": [
       {
          "adminConsentDescription": "Allow the app to access resources on behalf of the signed-in user.",
          "adminConsentDisplayName": "Access resource1",
          "id": "<guid>",
          "isEnabled": true,
          "type": "User",
          "userConsentDescription": "Allow the app to access resource1 on your behalf.",
          "userConsentDisplayName": "Access resources",
          "value": "user_impersonation"
        }
    ],
```

### <a name="oauth2requiredpostresponse-attribute"></a>oauth2RequiredPostResponse 属性

| 密钥 | 值类型 |
| :--- | :--- |
| oauth2RequiredPostResponse | Boolean |

指定在 OAuth 2.0 令牌请求过程中，Azure AD 是否允许与 GET 请求相反的 POST 请求。 默认值为 false，即指定只允许 GET 请求。

示例：

```json
    "oauth2RequirePostResponse": false,
```

### <a name="parentalcontrolsettings-attribute"></a>parentalControlSettings 属性

| 密钥 | 值类型 |
| :--- | :--- |
| parentalControlSettings | String |

- `countriesBlockedForMinors` 指定禁止未成年人使用该应用的国家/地区。
- `legalAgeGroupRule` 指定适用于应用用户的法定年龄组规则。 可设置为 `Allow`、`RequireConsentForPrivacyServices`、`RequireConsentForMinors`、`RequireConsentForKids` 或 `BlockMinors`。

示例：

```json
    "parentalControlSettings": {
        "countriesBlockedForMinors": [],
        "legalAgeGroupRule": "Allow"
    },
```

### <a name="passwordcredentials-attribute"></a>passwordCredentials 属性

| 密钥 | 值类型 |
| :--- | :--- |
| passwordCredentials | 集合 |

请参阅 `keyCredentials` 属性的说明。

示例：

```json
    "passwordCredentials": [
      {
        "customKeyIdentifier": null,
        "endDate": "2018-10-19T17:59:59.6521653Z",
        "keyId": "<guid>",
        "startDate":"2016-10-19T17:59:59.6521653Z",
        "value":null
      }
    ],
```

### <a name="preauthorizedapplications-attribute"></a>preAuthorizedApplications 属性

| 密钥 | 值类型 |
| :--- | :--- |
| preAuthorizedApplications | 集合 |

列出了应用程序和针对隐式同意的所请求权限。 要求管理员已针对应用程序表示同意。 preAuthorizedApplications 不要求用户同意所请求的权限。 preAuthorizedApplications 中列出的权限不要求用户同意。 但是，preAuthorizedApplications 中未列出的任何其他所请求权限都要求用户同意。

示例：

```json
    "preAuthorizedApplications": [
       {
          "appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",
          "permissionIds": [
             "8748f7db-21fe-4c83-8ab5-53033933c8f1"
            ]
        }
    ],
```

### <a name="publisherdomain-attribute"></a>publisherDomain 属性

| 密钥 | 值类型 |
| :--- | :--- |
| publisherDomain | String |

应用程序的已验证发布者域。 只读。

示例：

```json
    "publisherDomain": "{tenant}.onmicrosoft.com",
```

### <a name="replyurlswithtype-attribute"></a>replyUrlsWithType 属性

| 密钥 | 值类型 |
| :--- | :--- |
| replyUrlsWithType | 集合 |

此多值属性保存 Azure AD 在返回令牌时接受用作目标的已注册 redirect_uri 值列表。 每个 URI 值都应当包含一个关联的应用类型值。 支持的类型值为：

- `Web`
- `InstalledClient`
- `Spa`

若要了解详细信息，请参阅 [replyUrl 限制和局限性](./reply-url.md)。

示例：

```json
    "replyUrlsWithType": [
       {
          "url": "https://localhost:4400/services/office365/redirectTarget.html",
          "type": "InstalledClient"
       }
    ],
```

### <a name="requiredresourceaccess-attribute"></a>requiredResourceAccess 属性

| 键 | 值类型 |
| :--- | :--- |
| requiredResourceAccess | 集合 |

`requiredResourceAccess` 可以使用动态许可来驱动管理员许可体验，并驱动使用静态许可的用户的用户许可体验。 但是，此参数无法驱动常规性的用户许可体验。

- `resourceAppId` 是应用需要访问的资源的唯一标识符。 此值应等于目标资源应用中声明的 appId。
- `resourceAccess` 是一个数组，列出应用在指定的资源中所需的 OAuth2.0 权限范围和应用角色。 包含指定的资源的 `id` 和 `type` 值。

示例：

```json
    "requiredResourceAccess": [
        {
            "resourceAppId": "00000002-0000-0000-c000-000000000000",
            "resourceAccess": [
                {
                    "id": "311a71cc-e848-46a1-bdf8-97ff7156d8e6",
                    "type": "Scope"
                }
            ]
        }
    ],
```

### <a name="samlmetadataurl-attribute"></a>samlMetadataUrl 属性

| 密钥 | 值类型 |
| :--- | :--- |
| samlMetadataUrl | String |

应用的 SAML 元数据 URL。

示例：

```json
    "samlMetadataUrl": "https://MyRegisteredAppSAMLMetadata",
```

### <a name="signinurl-attribute"></a>signInUrl 属性

| 键 | 值类型 |
| :--- | :--- |
| signInUrl | String |

指定应用主页的 URL。

示例：

```json
    "signInUrl": "https://MyRegisteredApp",
```

### <a name="signinaudience-attribute"></a>signInAudience 属性

| 密钥 | 值类型 |
| :--- | :--- |
| signInAudience | String |

指定当前应用程序支持哪些 Microsoft 帐户。 支持的值是：
- `AzureADMyOrg` - 在我组织的 Azure AD 租户（例如单租户）中具有 Microsoft 工作或学校帐户的用户
- `AzureADMultipleOrgs` - 在任何组织的 Azure AD 租户（例如多租户）中具有 Microsoft 工作或学校帐户的用户
- `AzureADandPersonalMicrosoftAccount` - 在任何组织的 Azure AD 租户中具有个人 Microsoft 帐户、工作或学校帐户的用户
- `PersonalMicrosoftAccount` - 用于登录 Xbox 和 Skype 等服务的个人帐户。

示例：

```json
    "signInAudience": "AzureADandPersonalMicrosoftAccount",
```

### <a name="tags-attribute"></a>tags 属性

| 密钥 | 值类型 |
| :--- | :--- |
| tags | String Array  |

可用来对应用程序进行分类和标识的自定义字符串。

示例：

```json
    "tags": [
       "ProductionApp"
    ],
```

## <a name="common-issues"></a>常见问题

### <a name="manifest-limits"></a>清单限制

应用程序清单包含多个称为集合的属性，例如 appRoles、keyCredentials、knownClientApplications、identifierUris、redirectUris、requiredResourceAccess 和 oauth2Permissions。 在任何应用程序的完整应用程序清单中，所有合并集合中的条目总数的上限为 1200。 如果以前在应用程序清单中指定了 100 个重定向 URI，则在构成该清单的其他所有合并集合中，只剩下 1100 个条目可供使用。

> [!NOTE]
> 如果你尝试在应用程序清单中添加超过 1200 个条目，则可能会显示错误“未能更新应用程序 xxxxxx **。错误详细信息: 清单的大小已超出其限制。请减少值数，然后重试请求。”**

### <a name="unsupported-attributes"></a>不支持的属性

应用程序清单代表 Azure AD 中底层应用程序模型的架构。 随着底层架构的不断演进，清单编辑器将不时地更新，以反映新的架构。 因此，你可能会在应用程序清单中看到新的属性。 在极少数情况下，你可能会注意到现有属性中的语法或语义发生了变化，或者你可能会发现以前存在的属性不再受支持。 例如，你会在[应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)中看到新的属性，而这些属性在应用注册（旧版）体验中使用不同的名称。

| 应用注册(旧版)| 应用注册           |
|---------------------------|-----------------------------|
| `availableToOtherTenants` | `signInAudience`            |
| `displayName`             | `name`                      |
| `errorUrl`                | -                           |
| `homepage`                | `signInUrl`                 |
| `objectId`                | `Id`                        |
| `publicClient`            | `allowPublicClient`         |
| `replyUrls`               | `replyUrlsWithType`         |

有关这些属性的说明，请参阅[清单参考](#manifest-reference)部分。

尝试上传之前下载的清单时，可能会出现以下错误之一。 此错误很可能是因为清单编辑器现在支持较新版本的架构，该架构与你尝试上传的清单不匹配。

* “无法更新 xxxxxx 应用程序。 错误详细信息：无效的对象标识符 'undefined'。 []."
* “无法更新 xxxxxx 应用程序。 错误详细信息：指定的一个或多个属性值无效。 []."
* “无法更新 xxxxxx 应用程序。 错误详细信息：不允许在此 api 版本中设置要更新的 availableToOtherTenants。 []."
* “无法更新 xxxxxx 应用程序。 错误详细信息：不允许对此应用程序的 replyUrls 属性进行更新。 请改用 'replyUrlsWithType' 属性。 []."
* “无法更新 xxxxxx 应用程序。 错误详细信息：找到了没有类型名称的值，并且没有可用的预期类型。 如果指定该模型，则有效负载中的每个值都必须具有可在有效负载中指定、显式由调用方调用或隐式从父值推断的类型。 []"

显示这些错误之一时，建议执行以下操作：

1. 在清单编辑器中逐个编辑属性，而不是上传之前下载的清单。 使用[清单参考](#manifest-reference)表来了解旧属性和新属性的语法与语义，以便能够成功编辑所需的属性。
1. 如果工作流要求在源存储库中保存清单供以后使用，我们建议使用 **应用注册** 体验中显示的清单来变基存储库中保存的清单。

## <a name="next-steps"></a>后续步骤

* 有关应用的应用程序对象与服务主体对象之间关系的详细信息，请参阅 [Azure AD 中的应用程序对象和服务主体对象](app-objects-and-service-principals.md)。
* 参阅 [Microsoft 标识平台开发人员词汇表](developer-glossary.md)，了解某些 Microsoft 标识平台开发人员核心概念的定义。

使用以下评论部分提供反馈，帮助我们改进和组织内容。

<!--article references -->
[AAD-APP-OBJECTS]:app-objects-and-service-principals.md
[AAD-DEVELOPER-GLOSSARY]:developer-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]:quickstart-v1-integrate-apps-with-azure-ad.md
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]:v1-oauth2-implicit-grant-flow.md
[INTEGRATING-APPLICATIONS-AAD]: ./quickstart-register-app.md
[O365-PERM-DETAILS]: /graph/permissions-reference
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
