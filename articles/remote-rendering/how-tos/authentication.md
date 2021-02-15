---
title: 身份验证
description: 说明身份验证的工作原理
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: how-to
ms.openlocfilehash: 35fd78a9d55dc684045fdb4b83691c1613801421
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724863"
---
# <a name="configure-authentication"></a>配置身份验证

Azure 远程渲染使用与 [Azure 空间定位点 (ASA)](../../spatial-anchors/concepts/authentication.md?tabs=csharp) 相同的身份验证机制。 客户端需要设置以下 *项之一* 才能成功调用 REST api：

* **AccountKey**：可在 Azure 门户上的远程呈现帐户的 "密钥" 选项卡中获取。 帐户密钥仅建议用于开发/原型开发。
    ![帐户 ID](./media/azure-account-primary-key.png)

* **AccountDomain**：可在 Azure 门户上的远程呈现帐户的 "概述" 选项卡中获取。
    ![帐户域](./media/azure-account-domain.png)

* **AuthenticationToken**：是 Azure AD 标记，可使用 [MSAL 库](../../active-directory/develop/msal-overview.md)获取。 有多个不同的流可用于接受用户凭据并使用这些凭据获取访问令牌。

* **MRAccessToken**：是 MR 令牌，可从 Azure Mixed Reality 安全令牌服务获取 (STS) 。 `https://sts.<accountDomain>`使用 REST 调用从终结点检索，如下所示：

    ```rest
    GET https://sts.southcentralus.mixedreality.azure.com/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
    Host: sts.southcentralus.mixedreality.azure.com
    Connection: Keep-Alive

    HTTP/1.1 200 OK
    Date: Tue, 24 Mar 2020 09:09:00 GMT
    Content-Type: application/json; charset=utf-8
    Content-Length: 1153
    Accept: application/json
    MS-CV: 05JLqWeKFkWpbdY944yl7A.0
    {"AccessToken":"eyJhbGciOiJSUzI1<truncated>uLkO2FvA"}
    ```

    其中，授权标头的格式如下： `Bearer <Azure_AD_token>` 或 `Bearer <accoundId>:<accountKey>` 。 前者适用于安全性。 从此 REST 调用返回的令牌为 MR 访问令牌。

## <a name="authentication-for-deployed-applications"></a>已部署应用程序的身份验证

建议仅在开发期间使用帐户密钥进行快速原型设计。 建议不要使用嵌入的帐户密钥将应用程序交付到生产。 推荐的方法是使用基于用户或基于服务的 Azure AD 身份验证方法。

 Azure AD 身份验证在 [Azure 空间定位点 (ASA)](../../spatial-anchors/index.yml) 服务的 [Azure AD 用户身份验证](../../spatial-anchors/concepts/authentication.md?tabs=csharp#azure-ad-user-authentication)部分中进行了说明。

 有关详细信息，请参阅 [教程：保护 Azure 远程呈现和模型存储 Azure Active Directory 身份验证](../tutorials/unity/security/security.md#azure-active-directory-azure-ad-authentication)

## <a name="azure-role-based-access-control"></a>Azure 基于角色的访问控制

若要帮助控制向你的服务授予的访问级别，请在授予基于角色的访问权限时使用以下角色：

* **远程渲染管理员**：为用户提供 Azure 远程渲染的转换、管理会话、渲染和诊断功能。
* **远程渲染客户端**：为用户提供 Azure 远程渲染的管理会话、渲染和诊断功能。

## <a name="next-steps"></a>后续步骤

* [创建帐户](create-an-account.md)
* [使用 Azure 前端 API 进行身份验证](frontend-apis.md)
* [PowerShell 脚本示例](../samples/powershell-example-scripts.md)