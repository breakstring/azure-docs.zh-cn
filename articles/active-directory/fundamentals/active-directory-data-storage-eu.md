---
title: 欧盟客户的标识数据存储-Azure AD
description: 了解 MAzure Active Directory 在哪个位置存储其欧洲客户的标识相关数据。
services: active-directory
author: ajburnle
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/15/2020
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a8b013723707c4a3a087a90674227c3d41c5108
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2020
ms.locfileid: "94836931"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Azure Active Directory 中的欧洲客户标识数据存储
标识数据由 Azure AD 存储在地理位置，这是在订阅 Microsoft Online 服务（例如 Microsoft 365 和 Azure）时由你的组织提供的地址。 若要了解标识数据的存储位置，可以使用 Microsoft 信任中心的 " [你的数据位于何处？](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) " 部分。

对于在欧洲提供地址的客户，Azure AD 保留欧洲数据中心内的大多数标识数据。 本文档提供了有关 Azure AD 服务在欧洲之外存储的任何数据的信息。

## <a name="microsoft-azure-ad-multi-factor-authentication"></a>Microsoft Azure AD 多重身份验证

对于基于云的 Azure AD 多重身份验证，在最接近用户的数据中心完成身份验证。 北美、欧洲和亚太存在 Azure AD 多重身份验证的数据中心。

* 使用电话呼叫的多重身份验证源自美国数据中心，由全局提供商路由。
* 使用 SMS 的多重身份验证由全局提供程序进行路由。
* 使用来自欧盟数据中心的 Microsoft Authenticator 应用推送通知的多重身份验证请求在欧盟数据中心处理。
    * 特定于设备供应商的服务，如 Apple 推送通知，可以在欧洲外。
* 使用来自欧盟数据中心的 OATH 代码的多重身份验证请求在 EU 进行验证。

若要详细了解 Azure 多重身份验证服务器收集哪些用户信息 (MFA Server) 和基于云的 Azure AD MFA，请参阅 [Azure 多重身份验证用户数据收集](../authentication/howto-mfa-reporting-datacollection.md)。

## <a name="password-based-single-sign-on-for-enterprise-applications"></a>适用于企业应用程序的基于密码的单一 Sign-On
 
如果客户创建新的企业应用程序 (通过 Azure AD 库或非库) 并启用基于密码的 SSO，则应用程序登录 URL 和自定义捕获登录字段将存储在美国中。 有关此功能的详细信息，请参阅 [配置基于密码的单一登录](../manage-apps/configure-password-single-sign-on-non-gallery-applications.md)

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure Active Directory B2C (Azure AD B2C)

Azure AD B2C 策略配置数据和密钥容器存储在美国数据中心。 它们不包含任何用户个人数据。 有关策略配置的详细信息，请参阅 [Azure Active Directory B2C：内置策略](../../active-directory-b2c/user-flow-overview.md)一文。

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure Active Directory B2B (Azure AD B2B) 
    
Azure AD B2B 用兑换链接来存储邀请，并在美国数据中心重定向 URL 信息。 此外，取消订阅接收 B2B 邀请的用户的电子邮件地址也存储在美国数据中心。

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Microsoft Azure Active Directory 域服务 (Azure AD DS)

Azure AD DS 将用户数据存储在客户选择的 Azure 虚拟网络所在的同一位置。 因此，如果该网络位于欧洲外部，则会复制数据并将其存储在欧洲外部。

## <a name="federation-in-microsoft-exchange-server-2013"></a>Microsoft Exchange Server 2013 中的联合身份验证
    
- 应用程序标识符 (AppID) -Azure Active Directory authentication 系统生成的唯一编号，用于标识 Exchange 组织。
- 应用程序的已批准联合域列表
- 应用程序的令牌签名公钥 

有关 Microsoft Exchange server 中的联合的详细信息，请参阅 [联合： Exchange 2013 帮助](/exchange/federation-exchange-2013-help) 文章。


## <a name="other-considerations"></a>其他注意事项

与 Azure AD 集成的服务和应用程序可以访问标识数据。 评估每个服务和应用程序，以确定该特定服务和应用程序如何处理标识数据，以及这些服务和应用程序是否符合公司的数据存储要求。

有关 Microsoft 服务的数据存放的详细信息，请参阅 Microsoft 信任中心的[数据存储在何处？](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)部分。

## <a name="next-steps"></a>后续步骤
有关上述任何功能的详细信息，请参阅以下文章：
- [什么是多重身份验证？](../authentication/concept-mfa-howitworks.md)

- [Azure AD 自助服务密码重置](../authentication/concept-sspr-howitworks.md)

- [什么是 Azure Active Directory B2C？](../../active-directory-b2c/overview.md)

- [什么是 Azure AD B2B 协作？](../external-identities/what-is-b2b.md)

- [Azure Active Directory (AD) 域服务](../../active-directory-domain-services/overview.md)