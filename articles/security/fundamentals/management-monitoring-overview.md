---
title: 管理和监视安全功能-Microsoft Azure |Microsoft Docs
description: 本文概述了 Azure 提供的安全功能和服务，以帮助管理和监视 Azure 云服务和虚拟机。
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 5cf2827b-6cd3-434d-9100-d7411f7ed424
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: terrylan
ms.openlocfilehash: d85b1fdd433c372bb41adec6e3d33013f19363f0
ms.sourcegitcommit: 3c8964a946e3b2343eaf8aba54dee41b89acc123
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2021
ms.locfileid: "98747167"
---
# <a name="azure-security-management-and-monitoring-overview"></a>Azure 安全管理和监视概述
本文概述了 Azure 提供的安全功能和服务，以帮助管理和监视 Azure 云服务和虚拟机。

## <a name="azure-role-based-access-control"></a>Azure 基于角色的访问控制

Azure 基于角色的访问控制 (Azure RBAC) 为 Azure 资源提供详细的访问管理。 使用 Azure RBAC，可以仅授予用户执行其作业所需的访问权限。 Azure RBAC 还有助于确保用户离开组织后无法访问云中的资源。

了解详细信息：

* [有关 Azure RBAC 的 Active Directory 团队博客](https://cloudblogs.microsoft.com/enterprisemobility/?product=azure-active-directory)
* [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md)

## <a name="antimalware"></a>反恶意软件

通过 Azure，可使用主要安全厂商（例如 Microsoft、Symantec、Trend Micro、McAfee 和 Kaspersky）的反恶意软件。 此软件可帮助保护虚拟机免受恶意文件、广告程序和其他威胁的侵害。

适用于 Azure 云服务和虚拟机的 Microsoft 反恶意软件提供了为 PaaS 角色和虚拟机安装反恶意软件代理的能力。 基于 System Center Endpoint Protection，此功能将经验证的本地安全技术引入到了云。

我们还为 Azure 平台中趋势的 [深度安全](https://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/) 和 [SecureCloud](https://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/) 产品提供了深度集成。 DeepSecurity 是一个防病毒解决方案，SecureCloud 是一个加密解决方案。 DeepSecurity 通过扩展模型部署在 VM 内部。 通过 Azure 门户 UI 和 PowerShell，用户可以选择使用即将启动的新 VM 或已部署的现有 VM 内部的 DeepSecurity。

在 Azure 上也支持 Symantec Endpoint Protection (SEP)。 通过门户集成，你能够表明想要在 VM 内使用 SEP。 SEP 可以通过 Azure 门户安装在新的 VM 上，也可以通过 PowerShell 安装在现有 VM 上。

了解详细信息：

* [在 Azure 虚拟机上部署反恶意软件解决方案](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [适用于 Azure 云服务和虚拟机的 Microsoft 反恶意软件](antimalware.md)
* [如何在 Windows VM 上安装和配置 Trend Micro Deep Security 即服务](../../virtual-machines/extensions/trend.md)
* [如何在 Windows VM 上安装和配置 Symantec Endpoint Protection](../../virtual-machines/extensions/symantec.md)
* [New Antimalware Options for Protecting Azure Virtual Machines](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)（用于保护 Azure 虚拟机的新反恶意软件选项）

## <a name="multi-factor-authentication"></a>多重身份验证

Azure AD 多重身份验证是一种需要使用多种验证方法的身份验证方法。 它为用户登录和事务添加了关键的附加安全层。

多重身份验证可帮助保护对数据和应用程序的访问，同时可以满足用户对简单登录过程的需求。 它通过各种验证选项（例如电话、短信、移动应用通知或验证码）和第三方 OATH 令牌来提供强大的身份验证。

了解详细信息：

* [多重身份验证](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [什么是 Azure AD 多重身份验证？](../../active-directory/authentication/concept-mfa-howitworks.md)
* [Azure AD 多重身份验证的工作原理](../../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="expressroute"></a>ExpressRoute

可使用 Azure ExpressRoute 通过连接服务提供商所提供的专用连接，将本地网络扩展到 Microsoft 云。 使用 ExpressRoute 可与 Azure、Microsoft 365 和 CRM Online 等 Microsoft 云服务建立连接。 连接可以来自：

* 任意位置之间的 (IP VPN) 网络。
* 点到点以太网。
* 通过位于归置设施的连接服务提供商提供的虚拟交叉连接。

ExpressRoute 连接不经过公共 Internet。 它们可提供可靠性、速度、延迟和安全性这几个方面均比基于 Internet 的典型连接更胜一筹的专用连接。

了解详细信息：

* [ExpressRoute 技术概述](../../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>虚拟网络网关

VPN 网关（也称为 Azure 虚拟网络网关）用于在虚拟网络和本地位置之间发送流量。 VPN 网关还用于在 Azure 内的多个虚拟网络之间发送流量（网络到网络）。 VPN 网关提供 Azure 和基础结构间的安全跨界连接。

了解详细信息：

* [关于 VPN 网关](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Azure 网络安全概述](network-overview.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management

用户有时候需要在 Azure 资源或者其他 SaaS 应用程序中执行特权操作。 这通常意味着，组织授予他们永久的 Azure Active Directory (Azure AD) 访问特权。

这会给云托管的资源不断增大安全风险，因为组织无法充分监视这些用户正在使用特权访问执行哪些操作。 此外，如果有访问特权的用户帐户被泄露，此安全漏洞可能会影响组织的总体云安全性。 Azure AD Privileged Identity Management 可通过降低特权的暴露时间和加强对使用情况的了解来帮助解决此风险。  

Privileged Identity Management 为角色或“及时”管理员访问引入了临时管理员的概念。 这种类型的管理员是需要为该分配的角色完成激活过程的用户。 激活过程会在指定的时段内将 Azure AD 中的用户角色分配从非活动更改为活动。

了解详细信息：

* [Azure AD Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)
* [Azure AD Privileged Identity Management 入门](../../active-directory/privileged-identity-management/pim-getting-started.md)

## <a name="identity-protection"></a>标识保护

Azure AD 标识保护提供了可疑登录活动和潜在漏洞的统一视图来帮助保护企业。 “标识保护”根据以下信号检测用户和特权（管理员）标识的可疑活动：

* 暴力攻击。
* 凭据泄漏。
* 从不熟悉的位置和易感染病毒的设备登录。

通过提供通知和建议的补救措施，标识保护有助于实时降低风险。 它会计算用户风险严重性。 可配置基于风险的策略，自动保护应用程序访问免受将来的威胁侵害。

了解详细信息：

* [Azure Active Directory 标识保护](../../active-directory/identity-protection/overview-identity-protection.md)
* [第 9 频道：Azure AD 和标识展示：“标识保护”预览](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>安全中心

Azure 安全中心可帮助防范、检测和应对威胁。 利用安全中心，你可以更深入地了解 Azure 资源以及混合云环境中的资源的安全性。 

安全中心对已连接的资源执行持续的安全评估，并将其配置和部署与 [Azure 安全基准](../benchmarks/introduction.md) 进行比较，以提供针对你的环境定制的详细安全建议。

安全中心通过以下方式帮助优化和监视 Azure 资源的安全：

- 你可根据以下内容为 Azure 订阅资源定义策略：
    - 组织的安全需求。
    - 应用程序的类型或每个订阅中数据的敏感度。
    - 适用于你的订阅的任何行业或法规标准或基准。 
- 监视 Azure 虚拟机、网络和应用程序的状态。
- 提供按优先级排列的安全警报列表，包括集成的合作伙伴解决方案中的警报。 它还提供了快速调查攻击所需的信息以及如何修复攻击的建议。

了解详细信息：

* [Azure 安全中心简介](../../security-center/security-center-introduction.md)
* [提高 Azure 安全中心中的安全评分](../../security-center/secure-score-security-controls.md)

## <a name="intelligent-security-graph"></a>Intelligent Security Graph

Intelligent Security Graph 在 Microsoft 产品和服务中提供实时威胁防护。 它使用链接大量威胁情报和安全数据的高级分析，以提供可加强组织安全性的见解。 Microsoft 使用高级分析，每月处理超过 4,500 亿次身份验证、扫描 4,000 亿封电子邮件是否存在恶意软件和钓鱼，并更新 10 亿台设备，以提供更丰富的见解。 这些见解可帮助你的组织快速检测并响应攻击。

* [Intelligent Security Graph](https://www.microsoft.com/security/intelligence)

## <a name="next-steps"></a>后续步骤
了解[共担责任模型](shared-responsibility.md)、由 Microsoft 处理的安全任务，以及由你处理的任务。

有关安全管理的详细信息，请参阅 [Azure 中的安全管理](management.md)。