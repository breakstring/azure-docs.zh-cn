---
title: 部署计划 - Azure Active Directory | Microsoft Docs
description: 有关如何部署多个 Azure Active Directory 功能的端到端指南。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4185ffd644d54c419f42c78326ca10bf100443c3
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2021
ms.locfileid: "99051424"
---
# <a name="azure-active-directory-deployment-plans"></a>Azure Active Directory 部署计划
正在寻找有关部署 Azure Active Directory (Azure AD) 功能的端到端指南吗？ Azure AD 部署计划将引导你完成成功部署常见 Azure AD 功能所需的业务价值、规划注意事项和操作过程。

从任何计划页面，使用浏览器的打印到 PDF 功能创建文档的最新脱机版本。


## <a name="deploy-authentication"></a>部署身份验证

| 功能 | 说明|
| -| -|
| [多重身份验证](../authentication/howto-mfa-getstarted.md)| Azure AD 多重身份验证 (MFA) 是 Microsoft 的双重验证解决方案。 Azure AD MFA 可使用管理员批准的身份验证方法帮助保护对数据和应用程序的访问，同时满足对简单登录过程的需求。 观看此视频， [了解如何在租户中配置和强制实施多重身份验证](https://www.youtube.com/watch?v=qNndxl7gqVM)|
| [条件访问](../conditional-access/plan-conditional-access.md)| 使用条件访问时，可以根据条件实施自动化的访问控制决策，决定谁能够访问云应用。 |
| [自助式密码重置](../authentication/howto-sspr-deployment.md)| 自助密码重置功能可帮助用户在无需管理员干预的情况下重置密码。 |
| [无密码](../authentication/howto-authentication-passwordless-deployment.md) | 使用组织中的 Microsoft Authenticator 应用或 FIDO2 安全密钥实现无密码 authentication |

## <a name="deploy-application-and-device-management"></a>部署应用程序和设备管理

| 功能 | 说明|
| -| - |
| [单一登录](../manage-apps/plan-sso-deployment.md)| 单一登录可帮助用户访问在只登录一次时需要执行业务的应用和资源。 登录后，他们可以从 Microsoft Office 到 SalesForce 到 Box，再到内部应用程序，而无需再次输入凭据。 |
| [我的应用](../manage-apps/my-apps-deployment-plan.md)| 为你的用户提供一个简单的中心来发现和访问他们的所有应用程序。 通过自助服务功能提高他们的工作效率，例如请求访问应用和组，或代表其他人管理对资源的访问。 |
| [设备](../devices/plan-device-deployment.md) | 本文可帮助你评估以下操作方法：将设备与 Azure AD 集成，选择实施计划，以及提供指向受支持的设备管理工具的关键链接。 |


## <a name="deploy-hybrid-scenarios"></a>部署混合方案

| 功能 | 说明|
| -| -|
| [使用 ADFS 进行密码哈希同步](../hybrid/plan-migrate-adfs-password-hash-sync.md)| 通过密码哈希同步，用户密码的哈希将从本地 Active Directory 同步到 Azure AD，让 Azure AD 对不与本地的交互的用户进行身份验证 Active Directory |
| [使用 ADFS 进行直通身份验证](../hybrid/plan-migrate-adfs-pass-through-authentication.md)| Azure AD 传递身份验证可帮助用户使用相同的密码登录到本地和基于云的应用程序。 此功能为用户提供更好的体验-需要记住的密码越少，并且减少了 IT 支持人员的成本，因为用户不太可能忘记登录的方式。 当人们使用 Azure AD 登录时，此功能可直接通过本地 Active Directory 验证用户的密码。 |
| [Azure AD 应用程序代理](../manage-apps/application-proxy-deployment-plan.md) |现今的员工想要随时随地都能在任何设备上高效工作。 他们需要访问云中的 SaaS 应用和本地的公司应用。 Azure AD 应用程序代理支持这种强大的访问，而无需昂贵和复杂的虚拟专用网络 (VPN) 或外围子网 (DMZ)。 |
| [无缝 SSO](../hybrid/how-to-connect-sso-quick-start.md)| Azure Active Directory 无缝单一登录（Azure AD 无缝 SSO）可使连接到企业网络的企业设备上的用户自动登录。 利用此功能，用户无需键入密码即可登录 Azure AD，通常不需要输入用户名。 此功能为授权用户提供轻松访问基于云的应用程序的权限，而无需其他任何本地组件。 |

## <a name="deploy-user-provisioning"></a>部署用户预配

| 功能 | 说明|
| -| -|
| [用户预配](../app-provisioning/plan-auto-user-provisioning.md)| 可以通过 Azure AD 自动创建、维护和删除云 (SaaS) 应用程序（如 Dropbox、Salesforce、ServiceNow 等）中的用户标识。 |
| [Cloud HR 用户预配](../app-provisioning/plan-cloud-hr-provision.md)| Cloud HR 用户预配到 Active Directory 为正在进行的标识管理创建了基础，并增强了依赖于权威标识数据的业务流程的质量。 将此功能与云 HR 产品（如 Workday 或 Successfactors）结合使用，可以通过配置规则来 (对员工和临时工作人员的标识生命周期进行无缝管理，这些规则将映射 Leaver 流程 () 如创建、启用、禁用)  |

## <a name="deploy-governance-and-reporting"></a>部署管理和报告

| 功能 | 说明|
| -| -|
| [Privileged Identity Management](../privileged-identity-management/pim-deployment-plan.md)| Azure AD Privileged Identity Management (PIM) 可帮助跨 Azure AD、Azure 资源和其他 Microsoft 联机服务管理特权管理角色。 PIM 提供实时访问、请求审批工作流和完全集成的访问评审等解决方案，让用户可以实时识别、发现并防止特权角色的恶意活动。 |
| [报告和监视](../reports-monitoring/plan-monitoring-and-reporting.md)| Azure AD 报告和监视解决方案的设计取决于你的法律、安全和运营要求以及你的现有环境和过程。 本文介绍各种设计选项，并指导您正确部署策略。 |
| [访问评审](../governance/deploy-access-reviews.md) | 访问评审是您的管理策略的重要组成部分，它使您可以了解和管理谁有权访问的人员以及他们有权访问的内容。 本文可帮助你规划和部署访问评审，以实现所需的安全和协作 postures。 |

## <a name="include-the-right-stakeholders"></a>包含正确的利益干系人

在开始规划新功能时，必须在组织中包含关键利益干系人。 建议你确定并记录满足以下各项角色的人员或人员，并与他们合作来确定他们在项目中的参与情况。  

角色可能包括以下 

|角色 |说明 |
|-|-|
|最终用户|将为其实施功能的代表用户组。 经常预览试点计划中的更改。
|IT 支持经理|IT 支持组织代表，他们可以从支持人员的角度提供此更改的可支持性的输入。  
|标识架构师或 Azure 全局管理员|标识管理团队代表定义此更改如何与组织中的核心标识管理基础结构保持一致。|
|应用程序企业所有者 |受影响的应用程序的总体业务所有者 () ，这可能包括管理访问权限。还可以提供有关用户体验的输入，并从最终用户的角度来使用此更改。
|安全所有者|安全团队的代表，可以签署计划，以满足组织的安全要求。|
|合规性管理器|组织中负责确保符合公司、行业或政府要求的人员。|

**参与级别可能包括：**

- 用于实现项目计划和结果的 **R** esponsible 

- 项目计划和结果 **的 pproval** 

- **C** ontributor 到项目计划和结果 

- **我** nformed 项目计划和结果


## <a name="best-practices-for-a-pilot"></a>试验的最佳实践
通过试验，你可以在为每个人启用一种功能之前测试一个小组。 确保在测试过程中，组织内的每个用例都经过全面测试。 最好瞄准一组特定的试验用户，然后将其整体推广到你的组织。

在第一轮中，面向 IT、可用性和其他可测试和提供反馈的适当用户。 此反馈应该用于进一步开发您向用户发送的通信和说明，并深入了解支持人员可能会看到的问题类型。 

将部署扩展到更大的用户组应通过增加)  (的组的范围来执行。 这可以通过 [动态组成员身份](../enterprise-users/groups-dynamic-membership.md)来完成，也可以通过手动将用户添加到目标组 () 。
