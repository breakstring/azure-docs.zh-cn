---
title: Microsoft 365 政府版中 Azure AD 有哪些新功能？ | Microsoft Docs
description: 了解 Azure Active Directory (Azure AD) 在 Microsoft 365 政府云实例中进行一些更改，这可能会影响你。
services: active-directory
author: eross-msft
manager: daveba
ms.author: lizross
ms.reviewer: sumitp
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0efc4bc8f89b0fbefbba171d80a3f8a1ed5e7f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89318924"
---
# <a name="whats-new-for-azure-active-directory-in-microsoft-365-government"></a>Microsoft 365 政府版中 Azure Active Directory 的新增功能

我们对 Microsoft 365 政府云实例中的 Azure Active Directory (Azure AD) 进行了一些更改，该实例适用于使用以下服务的客户：

- Microsoft Azure 政府

- Microsoft 365 政府– GCC 高

- Microsoft 365 政府– DoD

本文不适用于 Microsoft 365 政府– GCC 客户。

## <a name="changes-to-the-initial-domain-name"></a>对初始域名的更改

在组织初始注册 Microsoft 365 政府在线服务期间，系统会要求你选择组织的域名 `<your-domain-name>.onmicrosoft.com` 。 如果已经有一个具有 .com 后缀的域名，则不会更改任何内容。

但是，如果要注册新的 Microsoft 365 政府版服务，系统会要求你使用后缀选择域名 `.us` 。 那么，它将是 `<your-domain-name>.onmicrosoft.us` 。

>[!Note]
>此更改不适用于云服务提供商 (Csp) 管理的任何客户。

## <a name="changes-to-portal-access"></a>门户访问更改

我们已更新 Microsoft Azure 政府、Microsoft 365 政府版 Microsoft 365 政府– DoD 的门户终结点，如 [终结点映射表](#endpoint-mapping)中所示。

之前的客户可以使用全球 Azure (portal.azure.com) 和 Office 365 (portal.office.com) 门户登录。 通过此更新，客户现在必须使用特定的 Microsoft Azure 政府、Microsoft 365 政府-GCC 高 Microsoft 365 政府 DoD 门户登录。

## <a name="endpoint-mapping"></a>终结点映射

下表显示了所有客户的终结点：

| 名称 | 终结点详细信息 |
|------|------------------|
| 门户 |Microsoft Azure 政府： https://portal.azure.us<p>Microsoft 365 政府– GCC 高： https://portal.office365.us<p>Microsoft 365 政府– DoD： https://portal.apps.mil |
| Azure Active Directory 机构终结点 | https://login.microsoftonline.us |
| Microsoft 365 政府-GCC 高版的 Microsoft Graph API | https://graph.microsoft.us |
| Microsoft 365 政府-DoD 的 Microsoft Graph API | https://dod-graph.microsoft.us |
| Azure 政府服务终结点 | 有关详细信息，请参阅 [Azure 政府开发人员指南](../../azure-government/documentation-government-developer-guide.md) |
| Microsoft 365 政府-GCC 高终结点 | 有关详细信息，请参阅 [Office 365 美国政府版 GCC 高终结点](/office365/enterprise/office-365-u-s-government-gcc-high-endpoints) |
| Microsoft 365 政府-DoD | 有关详细信息，请参阅 [Office 365 美国政府 DoD 终结点](/office365/enterprise/office-365-u-s-government-dod-endpoints) |

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章：

- [什么是 Azure 政府？](../../azure-government/documentation-government-welcome.md)

- [Azure 政府 AAD 颁发机构终结点更新](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)

- [在美国政府云中 Microsoft Graph 终结点](https://developer.microsoft.com/graph/blogs/new-microsoft-graph-endpoints-in-us-government-cloud/)

- [Office 365 美国政府版和 DoD](/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)