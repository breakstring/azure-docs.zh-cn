---
title: B2B 协作的自助注册门户 - Azure AD
description: 了解如何根据你组织的需求为 Azure Active Directory B2B 用户自定义载入工作流。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a5b800e78448afcc970010535ba12b543d3cc74
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2020
ms.locfileid: "96860501"
---
# <a name="self-service-for-azure-ad-b2b-collaboration-sign-up"></a>用于 Azure AD B2B 协作注册的自助服务

客户可借助通过 [Azure 门户](https://portal.azure.com)和面向最终用户的[应用程序访问面板](https://myapps.microsoft.com)公开的内置功能执行许多操作。 但你可能需要为 B2B 用户自定义载入工作流，以适应你组织的需求。

## <a name="azure-ad-entitlement-management-for-b2b-guest-user-sign-up"></a>用于 B2B 来宾用户注册的 Azure AD 权利管理

作为邀请方组织，你可能事先不知道需要访问你的资源的各个外部协作者是谁。 需要为合作伙伴公司的用户提供一种方式，让他们根据你所控制的策略自行注册。 若要使其他组织的用户能够请求访问权限，并在批准后为他们预配来宾帐户并将其分配到组、应用和 SharePoint Online 站点，可以使用 [Azure AD 权利管理](../governance/entitlement-management-overview.md)来配置用于[管理外部用户的访问权限](../governance/entitlement-management-external-users.md#how-access-works-for-external-users)的策略。

## <a name="azure-active-directory-b2b-invitation-api"></a>Azure Active Directory B2B 邀请 API

组织可以使用 [Microsoft Graph 邀请管理器 API](/graph/api/resources/invitation) 为 B2B 来宾用户构建自己的加入体验。 若要为 B2B 来宾用户提供自助服务注册，我们建议使用 [Azure AD 权利管理](../governance/entitlement-management-overview.md)。 但是，若要构建自己的体验，可以使用类似于[创建邀请 API](/graph/api/invitation-post?tabs=http) 的 API 自动将自定义的邀请电子邮件直接发送给 B2B 用户。 或者，应用可以使用创建响应中返回的 inviteRedeemUrl，将你自己的邀请（通过所选的通信机制）发送给受邀用户。

## <a name="next-steps"></a>后续步骤

* [什么是 Azure AD B2B 协作？](what-is-b2b.md)
* [外部标识定价](external-identities-pricing.md)
* [Azure Active Directory B2B 协作常见问题 (FAQ)](faq.md)
