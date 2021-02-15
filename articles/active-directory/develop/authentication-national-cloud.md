---
title: Azure AD 身份验证和国家云 | Azure
titleSuffix: Microsoft identity platform
description: 了解国家云的应用注册和身份验证终结点。
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: negoe
ms.reviewer: marsma, negoe,celested
ms.custom: aaddev,references_regions
ms.openlocfilehash: da1ea6462d22242d23629bcec192d7760314dfed
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96444814"
---
# <a name="national-clouds"></a>国家云

国家云是物理上独立的 Azure 实例。 Azure 的这些区域旨在确保数据驻留、主权和合规性要求在地理边界内得到遵从。

包括全球云，Azure Active Directory (Azure AD) 部署在以下国家云中：  

- Azure Government
- Azure 德国
- Azure 中国世纪互联

国家云是独一无二的，与 Azure 全球分开的环境。 在为这些环境开发应用程序时，了解关键差异非常重要。 差异包括注册应用程序、获取令牌和配置终结点。

## <a name="app-registration-endpoints"></a>应用注册终结点

每个国家云都有一个单独的 Azure 门户。 若要在国家云中将应用程序与 Microsoft 标识平台集成，需要在每个特定于环境的 Azure 门户中单独注册应用程序。

下表列出了用于为每个国家云注册应用程序的 Azure AD 终结点的基 URL。

| 国家云 | Azure AD 门户终结点 |
|----------------|--------------------------|
| 适用于美国政府的 Azure AD | `https://portal.azure.us` |
| Azure AD 德国 | `https://portal.microsoftazure.de` |
| 由世纪互联运营的 Azure AD 中国 | `https://portal.azure.cn` |
| Azure AD（全局服务） |`https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Azure AD 身份验证终结点

所有各国云在每个环境中分别对用户进行身份验证，并具有单独的身份验证终结点。

下表列出了用于获取每个国家云的令牌的 Azure AD 终结点的基 URL。

| 国家云 | Azure AD 身份验证终结点 |
|----------------|-------------------------|
| 适用于美国政府的 Azure AD | `https://login.microsoftonline.us` |
| Azure AD 德国| `https://login.microsoftonline.de` |
| 由世纪互联运营的 Azure AD 中国 | `https://login.chinacloudapi.cn` |
| Azure AD（全局服务）| `https://login.microsoftonline.com` |

可以使用适当的特定于区域的基 URL 来形成对 Azure AD 授权或令牌终结点的请求。 例如，对于 Azure 德国：

- 授权常用终结点为 `https://login.microsoftonline.de/common/oauth2/v2.0/authorize`。
- 令牌常用终结点为 `https://login.microsoftonline.de/common/oauth2/v2.0/token`。

对于单租户应用程序，请将先前 URL 中的“common”替换为你的租户 ID 或名称。 例如 `https://login.microsoftonline.de/contoso.com`。

## <a name="microsoft-graph-api"></a>Microsoft Graph API

若要了解如何在国家云环境中调用 Microsoft Graph API，请转到[国家云部署中的 Microsoft Graph](/graph/deployments)。

> [!IMPORTANT]
> 全球服务的特定区域中的某些服务和功能可能并非在所有国家云中都可用。 若要了解哪些服务可用，请访问[可用产品（按区域）](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast)。

若要了解如何使用 Microsoft 标识平台构建应用程序，请遵循 [Microsoft 身份验证库 (MSAL) 教程](msal-national-cloud.md)。 具体而言，此应用会将用户登录，获取用于调用 Microsoft Graph API 的访问令牌。

## <a name="next-steps"></a>后续步骤

了解如何使用 [Microsoft 身份验证库在全国云环境中 (MSAL) ](msal-national-cloud.md)。

国家/地区云文档：

- [Azure Government](../../azure-government/index.yml)
- [Azure 中国世纪互联](/azure/china/)
- [Azure 德国](../../germany/index.yml)
