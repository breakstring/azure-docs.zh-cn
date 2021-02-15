---
title: 面向开发人员的 Azure AD (v1.0) | Azure
description: 了解面向开发人员的 Azure AD (v1.0) 的身份验证基础知识，如应用模型、API、预配和最常见的身份验证方案。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 10/14/2019
ms.author: ryanwi
ms.reviewer: saeeda, sureshja, hirsin
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 36b39f3706db615e40ebfadebf36be4d8b29c33e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "80154723"
---
# <a name="what-is-authentication"></a>什么是身份验证？

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

身份验证是向访问方质询合法凭据的措施，提供创建用于标识和访问控制的安全主体的基础  。 简单来说，身份验证就是证明你是你自己的过程。 身份验证有时缩写为 AuthN。

授权是指授予经过身份验证的安全主体执行某项操作权力的措施。  它指定了可以访问的数据以及使用其可执行的操作。 授权有时缩写为 AuthZ。

面向开发人员的 Azure Active Directory (v1.0) (Azure AD) 通过提供标识即服务、支持 OAuth 2.0 和 OpenID Connect 等行业标准协议以及适用于不同平台的开源库来帮助你快速开始编码，从而简化了应用程序开发人员的身份验证。

在 Azure AD 编程模型中有两个主要用例：

* 在 OAuth 2.0 授权流程中 - 资源所有者向客户端应用程序授权时，允许客户端访问资源所有者的资源。
* 在客户端访问资源期间 - 与资源服务器实现的机制一样，使用访问令牌中提供的声明值作为依据做出访问控制决策。

## <a name="authentication-basics-in-azure-ad"></a>Azure AD 中的身份验证基础知识

思考一下需要标识的最基本方案：Web 浏览器中的用户需要通过 Web 应用程序进行身份验证。 下图显示了此方案：

![Web 应用程序登录概述](./media/v1-authentication-scenarios/auth-basics-microsoft-identity-platform.svg)

下面是你需要了解的示意图中显示的各种组件的相关信息：

* Azure AD 是标识提供程序。 标识提供程序负责对组织的目录中存在的用户和应用程序的标识进行验证，并在这些用户和应用程序成功通过身份验证时颁发安全令牌。
* 要将身份验证外包给 Azure AD 的应用程序必须在 Azure Active Directory (Azure AD) 中进行注册。 Azure AD 在目录中注册并唯一标识应用。
* 开发人员可以使用开源 Azure AD 身份验证库处理协议细节，方便你进行身份验证。 有关详细信息，请参阅 Microsoft 标识平台 [v2.0 身份验证库](../develop/reference-v2-libraries.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)和 [v1.0 身份验证库](active-directory-authentication-libraries.md)。
* 在用户通过身份验证后，应用程序必须对用户的安全令牌进行验证以确保身份验证是成功的。 可查找各种语言和框架的快速入门、教程和代码示例，了解应用程序必须执行的操作。
  * 要快速构建应用并添加功能（如获取令牌、刷新令牌、进行用户登录、显示某些用户信息），请参阅文档的“快速入门”部分  。
  * 要深入了解顶级身份验证开发人员任务的案例过程，例如获取访问令牌并在调用 Microsoft Graph API 和其他 API 时使用它们，使用 OpenID Connect 通过传统的基于 Web 浏览器的应用实现 Microsoft 登录等，请参阅文档的“教程”部分  。
  * 要下载代码示例，请转到 [GitHub](https://github.com/Azure-Samples?q=active-directory)。
* 身份验证过程的请求和响应流是由所使用的身份验证协议（例如 OAuth 2.0、OpenID Connect、WS 联合身份验证或 SAML 2.0）决定的。 有关协议的详细信息，请参阅文档中的“概念”>“身份验证协议”部分  。

在上面的示例方案中，你可以根据以下两个角色对应用进行分类：

* 需要安全地访问资源的应用
* 扮演资源本身角色的应用

### <a name="how-each-flow-emits-tokens-and-codes"></a>每个流如何发出令牌和代码

根据客户端的生成方式，客户端可以使用 Azure AD 支持的一种（或几种）身份验证流。 这些流可以生成各种令牌（id_tokens、刷新令牌、访问令牌）以及授权代码，并需要不同的令牌使其正常工作。 此图表提供概述：

|流向 | 需要 | id_token | 访问令牌 | 刷新令牌 | 授权代码 | 
|-----|----------|----------|--------------|---------------|--------------------|
|[授权代码流](v1-protocols-oauth-code.md) | | x | x | x | x|  
|[隐式流](v1-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[混合 OIDC 流](v1-protocols-openid-connect-code.md#get-access-tokens)| | x  | |          |            x   |
|[刷新令牌兑换](v1-protocols-oauth-code.md#refreshing-the-access-tokens) | 刷新令牌 | x | x | x| |
|[代理流](v1-oauth2-on-behalf-of-flow.md) | 访问令牌| x| x| x| |
|[客户端凭据](v1-oauth2-client-creds-grant-flow.md) | | | x（仅限应用）| | |

通过隐式模式颁发的令牌由于通过 URL（其中 `response_mode` 是 `query` 或 `fragment`）传回浏览器而具有长度限制。  有些浏览器对可以放在浏览器栏中的 URL 的大小有限制，当 URL 太长时会失败。  因此，这些令牌没有 `groups` 或 `wids` 声明。 

现在你已大致了解了基础知识，请继续阅读以了解身份应用模型和 API，如何在 Azure AD 中进行预配，以及指向 Azure AD 支持的常见方案的详细信息的链接。

## <a name="application-model"></a>应用程序模型

Azure AD 表示遵循特定模型的应用程序，该模型旨在实现两个主要功能：

* **根据应用支持的身份验证协议识别应用** - 这涉及枚举身份验证时所需的所有标识符、URL、机密和相关信息。 此处，Azure AD 可以：

    * 保存运行时支持身份验证所需的所有数据。
    * 保存所有数据，以确定应用可能需要访问的资源类型、是否应满足给定请求以及适用的条件。
    * 提供用于在应用开发人员的租户和任何其他 Azure AD 租户中实现应用配置的基础设施。

* **在令牌请求期间处理用户许可并帮助跨租户动态配置应用** - 此处，Azure AD 可以：

    * 使用户和管理员能够动态地同意或拒绝应用以他们的名义访问资源。
    * 使管理员能够最终决定允许执行哪些应用、哪些用户可以使用特定的应用，以及如何访问目录资源。

在 Azure AD 中，应用程序对象将应用程序描述为抽象实体  。 开发人员使用应用程序。 在部署时，Azure AD 使用给定的应用程序对象作为蓝图来创建服务主体，它表示目录或租户中的应用程序的具体实例  。 该服务主体用于定义应用在特定目标目录中可以实际执行的操作、使用者是谁、以及可以访问哪些资源等。 Azure AD 通过“同意”使用应用程序对象创建服务主体  。

下图显示了征得同意后经过简化的 Azure AD 预配流程。  其中存在两个租户（A 和 B），租户 A 拥有应用程序，租户 B 通过服务主体实例化应用程序。  

![征得同意后经过简化的预配流程](./media/v1-authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

在此预配流程中：

1. 来自租户 B 的某个用户尝试使用该应用登录，授权终结点请求应用程序的令牌。
1. 获取并验证用于身份验证的用户凭据
1. 系统提示用户许可该应用访问租户 B
1. Azure AD 使用租户 A 中的应用程序对象作为在租户 B 中创建服务主体的蓝图
1. 用户收到请求的令牌

你可以根据需要对其他租户（C、D 等）重复此过程。 租户 A 保留了应用（应用程序对象）的蓝图。 应用获得许可的所有其他租户中的用户和管理员通过每个租户中的相应服务主体对象保留对应用程序允许执行的操作的控制权。 有关详细信息，请参阅 [Microsoft 标识平台中的应用程序和服务主体对象](../develop/app-objects-and-service-principals.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)。

## <a name="claims-in-azure-ad-security-tokens"></a>Azure AD 安全令牌中的声明

Azure AD 颁发的安全令牌（访问令牌和 ID 令牌）包含与经过身份验证的使用者有关的信息的声明或断言。 应用程序可以使用各种任务的声明，包括：

* 验证令牌
* 标识使用者的目录租户
* 显示用户信息
* 确定使用者的授权

任何给定安全令牌中存在的声明都依赖于令牌的类型、用于验证用户身份的凭据的类型和应用程序配置。

下表提供了由 Azure AD 发出的每种声明的简要说明。 有关详细信息，请参阅 Azure AD 颁发的[访问令牌](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)和 [ID 令牌](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)。

| 声明 | 说明 |
| --- | --- |
| 应用程序 ID | 标识正在使用令牌的应用程序。 |
| 目标受众 | 标识令牌所针对的接收方资源。 |
| 应用程序身份验证上下文类引用 | 指示客户端的身份验证方式（公共客户端与机密客户端）。 |
| 身份验证即时 | 记录身份验证发生的日期和时间。 |
| 身份验证方法 | 指示令牌使用者的身份验证方式（密码、证书等）。 |
| 名字 | 按照 Azure AD 中的设置提供用户的名称。 |
| 组 | 包含用户所属 Azure AD 组的对象 ID。 |
| 标识提供者 | 记录对令牌使用者进行身份验证的标识提供者。 |
| 颁发时间 | 记录令牌的颁发时间，通常用于计算令牌新鲜度。 |
| 颁发者 | 标识发出令牌以及 Azure AD 租户的 STS。 |
| 姓氏 | 按照 Azure AD 中的设置提供用户的姓氏。 |
| 名称 | 提供一个用户可读值，用于标识令牌使用者。 |
| 对象 ID | 包含 Azure AD 中的使用者的不可变、唯一标识符。 |
| 角色 | 包含授予用户的 Azure AD 应用程序角色的友好名称。 |
| 作用域 | 指示授予客户端应用程序的权限。 |
| 使用者 | 指示令牌断言信息的主体。 |
| 租户 ID | 包含已颁发令牌的目录租户的不可变、唯一标识符。 |
| 令牌生存期 | 定义令牌保持有效状态的时间间隔。 |
| 用户主体名称 | 包含使用者的用户主体名称。 |
| 版本 | 包含令牌的版本号。 |

## <a name="next-steps"></a>后续步骤

* 了解有关 [Microsoft 标识平台支持的应用程序类型和方案](app-types.md)
