---
title: Azure Active Directory 身份验证管理操作参考指南
description: 此操作参考指南介绍了安全身份验证管理应采取的检查和操作
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 90e215ea445c8c700e351149e9c7a91d9a595252
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2020
ms.locfileid: "96859515"
---
# <a name="azure-active-directory-authentication-management-operations-reference-guide"></a>Azure Active Directory 身份验证管理操作参考指南

本部分的 [Azure AD 操作参考指南](active-directory-ops-guide-intro.md) 介绍了在保护和管理凭据时应该采取的检查和操作、定义身份验证体验、委派分配、衡量使用量，以及基于企业安全状况定义访问策略。

> [!NOTE]
> 这些建议是发布日期之后的最新建议，但会随时间变化。 组织应持续评估其身份实践，因为 Microsoft 产品和服务随时间推移而发展。

## <a name="key-operational-processes"></a>关键操作过程

### <a name="assign-owners-to-key-tasks"></a>将所有者分配到关键任务

管理 Azure Active Directory 需要持续执行关键操作任务和进程，这可能不是一个部署项目的一部分。 设置这些任务以优化环境仍非常重要。 关键任务及其建议所有者包括：

| 任务 | 所有者 |
| :- | :- |
| 管理 Azure AD 中 (SSO) 配置的单一登录生命周期 | IAM 操作团队 |
| 为 Azure AD 应用程序设计条件性访问策略 | InfoSec 体系结构团队 |
| 存档 SIEM 系统中的登录活动 | InfoSec 运营团队 |
| 存档 SIEM 系统中的风险事件 | InfoSec 运营团队 |
| 会审和调查安全报告 | InfoSec 运营团队 |
| 会审和调查风险事件 | InfoSec 运营团队 |
| 会审和调查标记为有风险的用户和漏洞报告 Azure AD Identity Protection | InfoSec 运营团队 |

> [!NOTE]
> Azure AD Identity Protection 要求 Azure AD Premium P2 许可证。 若要根据需要查找正确的许可证，请参阅 [比较 Azure AD Free 和 Azure AD Premium 版本的一般可用功能](https://azure.microsoft.com/pricing/details/active-directory/)。

查看列表时，可能会发现需要为缺少所有者的任务分配所有者，或使用与上述建议不符的所有者来调整任务的所有权。

#### <a name="owner-recommended-reading"></a>所有者建议阅读

- [在 Azure Active Directory 中分配管理员角色](../roles/permissions-reference.md)
- [Azure 中的监管](../../governance/index.yml)

## <a name="credentials-management"></a>凭据管理

### <a name="password-policies"></a>密码策略

安全地管理密码是身份和访问管理中最重要的部分，通常是攻击的最大目标之一。 Azure AD 支持多种功能，这些功能可帮助防止攻击得逞。

使用下表查找用于缓解需要解决的问题的建议解决方案：

| 问题 | 建议 |
| :- | :- |
| 无机制来防范弱密码 | 启用 Azure AD [自助服务密码重置 (SSPR) ](../authentication/concept-sspr-howitworks.md) 和 [密码保护](../authentication/concept-password-ban-bad-on-premises.md) |
| 没有用于检测泄漏的密码的机制 | 启用 [密码哈希同步](../hybrid/how-to-connect-password-hash-synchronization.md) (PHS) 以获取见解 |
| 使用 AD FS 并且无法移动到托管身份验证 | 启用 [AD FS Extranet 智能锁定](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) 和/或 [Azure AD 智能锁定](../authentication/howto-password-smart-lockout.md) |
| 密码策略使用基于复杂性的规则，例如长度、多个字符集或过期时间 | 请考虑 [使用 Microsoft 推荐的做法](https://www.microsoft.com/research/publication/password-guidance/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F265143%2Fmicrosoft_password_guidance.pdf) ，并将方法切换到密码管理，并部署 [Azure AD 密码保护](../authentication/concept-password-ban-bad.md)。 |
| 用户未注册使用多重身份验证 (MFA)  | [注册所有用户的安全信息](../identity-protection/howto-identity-protection-configure-mfa-policy.md) ，以便将其用作验证用户标识及其密码的机制 |
| 不会根据用户风险吊销密码 | 部署 Azure AD [Identity Protection 用户风险策略](../identity-protection/howto-identity-protection-configure-risk-policies.md) ，以使用 SSPR 强制对泄露的凭据进行密码更改 |
| 没有智能锁定机制可防止恶意身份验证来自标识的 IP 地址的不良参与者 | 使用密码哈希同步或 [传递身份验证](../hybrid/how-to-connect-pta-quick-start.md) (PTA) 部署云托管的身份验证 |

#### <a name="password-policies-recommended-reading"></a>建议阅读的密码策略

- [Azure AD 和 AD FS 最佳实践：防御密码喷涂攻击-企业移动性 + 安全性](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)

### <a name="enable-self-service-password-reset-and-password-protection"></a>启用自助服务密码重置和密码保护

需要更改或重置其密码的用户是最大数量的资源和支持人员的呼叫成本。 除了成本之外，将密码更改为缓解用户风险的工具是提高组织的安全状况的基本步骤。

至少，建议部署 Azure AD [自助服务密码重置](../authentication/concept-sspr-howitworks.md) (SSPR) 和本地 [密码保护](../authentication/howto-password-ban-bad-on-premises-deploy.md) 才能完成：

- 转移咨询台呼叫。
- 替换使用临时密码。
- 替换依赖于本地解决方案的任何现有自助服务密码管理解决方案。
- 消除你的组织中的[弱密码](../authentication/concept-password-ban-bad.md)。

> [!NOTE]
> 对于具有 Azure AD Premium P2 订阅的组织，建议部署 SSPR，并将其用作 [Identity Protection 用户风险策略](../identity-protection/howto-identity-protection-configure-risk-policies.md)的一部分。

### <a name="strong-credential-management"></a>强凭据管理

密码本身不太安全，无法防止不良的执行组件获取对你的环境的访问权限。 必须至少为具有特权帐户的任何用户启用多重身份验证， (MFA) 。 理想情况下，应启用 [组合注册](../authentication/concept-registration-mfa-sspr-combined.md) ，并要求所有用户使用 [组合注册体验](../user-help/security-info-setup-signin.md)注册 MFA 和 SSPR。 最终，我们建议采用一种策略来 [提供复原能力](../authentication/concept-resilient-controls.md) ，以降低由于不可预见的情况而导致锁定的风险。

![组合用户体验流程](./media/active-directory-ops-guide/active-directory-ops-img4.png)

### <a name="on-premises-outage-authentication-resiliency"></a>本地中断身份验证复原

除了简单的优点和启用泄漏的凭据检测以外，Azure AD 密码哈希同步 (PHS) 和 Azure AD MFA 允许用户访问 SaaS 应用程序和 Microsoft 365，这是因为网络攻击（如 [NotPetya](https://www.microsoft.com/security/blog/2018/02/05/overview-of-petya-a-rapid-cyberattack/)）导致的本地中断。 同时，还可以使用联合身份验证来启用 PHS。 启用 PHS 允许在联合身份验证服务不可用时回退身份验证。

如果本地组织缺乏中断复原策略，或者有一个未与 Azure AD 集成的策略，则应该部署 Azure AD PHS 并定义包含 PHS 的灾难恢复计划。 启用 Azure AD PHS 将允许用户在本地 Active Directory 不可用时，对 Azure AD 进行身份验证。

![密码哈希同步流](./media/active-directory-ops-guide/active-directory-ops-img5.png)

若要更好地了解身份验证选项，请参阅 [为 Azure Active Directory 混合标识解决方案选择正确的身份验证方法](../hybrid/choose-ad-authn.md)。

### <a name="programmatic-usage-of-credentials"></a>凭据的编程使用情况

使用 PowerShell 或使用 Microsoft Graph API 的应用程序 Azure AD 脚本需要安全身份验证。 执行这些脚本和工具的凭据管理不当会增加凭据被盗的风险。 如果你使用的脚本或应用程序依赖于硬编码的密码或密码提示，你应该首先查看配置文件或源代码中的密码，然后替换这些依赖项并使用 Azure 托管标识、Integrated-Windows 身份验证或 [证书](../reports-monitoring/tutorial-access-api-with-certificates.md) 。 对于之前的解决方案无法实现的应用程序，请考虑使用 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)。

如果确定有具有密码凭据的服务主体，并且不确定这些密码凭据如何由脚本或应用程序保护，请与应用程序所有者联系，以便更好地了解使用模式。

如果有具有密码凭据的服务主体，Microsoft 还建议联系应用程序所有者来了解使用模式。

## <a name="authentication-experience"></a>身份验证体验

### <a name="on-premises-authentication"></a>本地身份验证

使用集成的 Windows 身份验证的联合身份验证 (IWA) 或无缝单一 Sign-On (SSO) 使用密码哈希同步或直通身份验证的托管身份验证在公司网络内部的域控制器到本地域控制器时，最好的用户体验。 它最大程度地减少了凭据提示疲劳并降低了用户遭受成为牺牲品攻击的风险。 如果已在 PHS 或 PTA 中使用云托管的身份验证，但用户在本地进行身份验证时仍需要键入密码，则应立即 [部署无缝 SSO](../hybrid/how-to-connect-sso.md)。 另一方面，如果你当前已与计划最终迁移到云托管的身份验证，则应将无缝 SSO 作为迁移项目的一部分实现。

### <a name="device-trust-access-policies"></a>设备信任访问策略

与组织中的用户一样，设备也是要保护的核心标识。 可以使用设备标识随时随地保护你的资源。 对设备和其信任类型的记帐进行身份验证可通过以下方式改善你的安全状况和可用性：

- 在设备受信任的情况下，避免通过 MFA 实现摩擦
- 阻止来自不受信任设备的访问
- 对于 Windows 10 设备， [无缝地向本地资源提供单一登录](../devices/azuread-join-sso.md)。

可以通过使用以下方法之一在 Azure AD 中引入设备标识并对其进行管理，从而执行此目标：

- 组织可以使用 [Microsoft Intune](/intune/what-is-intune) 来管理设备并强制实施符合性策略、证明设备运行状况，并根据设备是否符合来设置条件性访问策略。 Microsoft Intune 可以通过 JAMF 集成) 、Windows 桌面 (以本机方式使用适用于 Windows 10 的移动设备管理以及与 Microsoft 终结点 Configuration Manager) 和 Android 移动设备的共同管理，来管理 iOS 设备、Mac 桌面 (。
- [混合 Azure AD 联接](../devices/hybrid-azuread-join-managed-domains.md) 在具有 Active Directory 加入域的计算机设备的环境中，通过组策略或 Microsoft 终结点 Configuration Manager 提供管理。 组织可以通过无缝 SSO 的 PHS 或 PTA 部署托管环境。 通过使你的设备 Azure AD 跨云和本地资源的 SSO 提高用户工作效率，同时使你能够使用 [条件访问](../conditional-access/overview.md) 同时保护对云和本地资源的访问。

如果已加入域且未在云中注册的 Windows 设备或在云中注册但没有条件访问策略的已加入域的 Windows 设备，则应注册未注册的设备，并且在任一情况下，都应 [使用混合 Azure AD 联接作为](../conditional-access/require-managed-devices.md) 条件性访问策略中的控件。

![需要混合设备的条件访问策略中的授权的屏幕截图](./media/active-directory-ops-guide/active-directory-ops-img6.png)

如果你正在使用 MDM 或 Microsoft Intune 管理设备，但没有在条件访问策略中使用设备控制，则建议在这些策略中使用 " [要求设备标记为合规](../conditional-access/require-managed-devices.md#require-device-to-be-marked-as-compliant) " 作为控件。

![要求设备符合性的条件访问策略中的授权的屏幕截图](./media/active-directory-ops-guide/active-directory-ops-img7.png)

#### <a name="device-trust-access-policies-recommended-reading"></a>设备信任访问策略建议阅读

- [如何：规划混合 Azure Active Directory 加入的实施](../devices/hybrid-azuread-join-plan.md)
- [标识和设备访问权限配置](/microsoft-365/enterprise/microsoft-365-policies-configurations)

### <a name="windows-hello-for-business"></a>Windows Hello 企业版

在 Windows 10 中， [Windows Hello 企业版](/windows/security/identity-protection/hello-for-business/hello-identity-verification) 将密码替换为 pc 上的强双重身份验证。 Windows Hello 企业版可为用户提供更简单的 MFA 体验，并减少对密码的依赖。 如果尚未开始推出 Windows 10 设备，或仅部分部署了 Windows 10 设备，则建议升级到 Windows 10，并在所有设备上 [启用 Windows Hello For Business](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) 。

如果要了解有关无密码 authentication 的详细信息，请参阅 [没有密码的世界 Azure Active Directory](../authentication/concept-authentication-passwordless.md)。

## <a name="application-authentication-and-assignment"></a>应用程序身份验证和分配

### <a name="single-sign-on-for-apps"></a>应用的单一登录

为整个企业提供标准化的单一登录机制对于获得最佳用户体验、降低风险、报告和管理能力至关重要。 如果你使用的应用程序支持使用 SSO 的 Azure AD 但当前配置为使用本地帐户，则应重新配置这些应用程序，以将 SSO 与 Azure AD 结合使用。 同样，如果你正在使用支持 SSO 的任何应用程序 Azure AD 但使用的是其他标识提供者，则应重新配置这些应用程序，以将 SSO 与 Azure AD 结合使用。 对于不支持联合身份验证的应用程序，但支持基于窗体的身份验证，我们建议你将应用程序配置为使用 Azure AD 应用程序代理的 [密码保管](../manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md) 。

![AppProxy 基于密码的登录](./media/active-directory-ops-guide/active-directory-ops-img8.png)

> [!NOTE]
> 如果没有一种机制来发现组织中的非托管应用程序，我们建议使用云访问安全代理解决方案来实现发现过程 (CASB) 例如 [Microsoft Cloud App Security](https://www.microsoft.com/enterprise-mobility-security/cloud-app-security)。

最后，如果你有 Azure AD 应用库并使用支持 SSO 与 Azure AD 的应用程序，我们建议你 [在应用程序库中列出应用程序](../develop/v2-howto-app-gallery-listing.md)。

#### <a name="single-sign-on-recommended-reading"></a>建议的单一登录

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录](../manage-apps/what-is-single-sign-on.md)

### <a name="migration-of-ad-fs-applications-to-azure-ad"></a>将 AD FS 应用程序迁移到 Azure AD

[将应用从 AD FS 迁移到 Azure AD](../manage-apps/migrate-adfs-apps-to-azure.md) 可实现更高的安全性、更一致的可管理性和更好的协作体验。 如果在 AD FS 中配置了支持 SSO 与 Azure AD 的应用程序，则应重新配置这些应用程序以将 SSO 与 Azure AD 结合使用。 如果在 AD FS 中配置了应用程序，但 Azure AD 不支持不常见的配置，则应联系应用程序所有者了解特殊配置是否是应用程序的绝对要求。 如果不是必需的，则应重新配置应用程序以将 SSO 与 Azure AD 一起使用。

![作为主要标识提供者 Azure AD](./media/active-directory-ops-guide/active-directory-ops-img9.png)

> [!NOTE]
> 可以使用[ADFS Azure AD Connect Health](../hybrid/how-to-connect-health-adfs.md)来收集有关可能迁移到 Azure AD 的每个应用程序的配置详细信息。

### <a name="assign-users-to-applications"></a>将用户分配到应用程序

将[用户分配到应用程序](../manage-apps/assign-user-or-group-access-portal.md)最好是使用组进行映射，因为它们允许更大的灵活性和大规模管理。 使用组的优点包括 [基于属性的动态组成员身份](../enterprise-users/groups-dynamic-membership.md) 和 [应用所有者的委派](../fundamentals/active-directory-accessmanagement-managing-group-owners.md)。 因此，如果你已在使用和管理组，则建议你采取以下措施来改进大规模管理：

- 向应用程序所有者委派组管理和管理。
- 允许对应用程序进行自助访问。
- 定义动态组（如果用户属性可以一致地确定对应用程序的访问权限）。
- 使用 [Azure AD 访问评审](../governance/access-reviews-overview.md)将证明实施到用于应用程序访问的组。

另一方面，如果找到已分配给单个用户的应用程序，请务必围绕这些应用程序实施 [管理](../governance/index.yml) 。

#### <a name="assign-users-to-applications-recommended-reading"></a>将用户分配到建议阅读的应用程序

- [向 Azure Active Directory 中的应用程序分配用户和组](../manage-apps/assign-user-or-group-access-portal.md)
- [在 Azure Active Directory 中委托应用注册权限](../roles/delegate-app-roles.md)
- [Azure Active Directory 中的动态组成员资格规则](../enterprise-users/groups-dynamic-membership.md)

## <a name="access-policies"></a>访问策略

### <a name="named-locations"></a>命名位置

在 Azure AD 中的 [命名位置](../reports-monitoring/quickstart-configure-named-locations.md) ，可以标记组织中受信任的 IP 地址范围。 Azure AD 使用命名位置以：

- 防止风险事件中出现误报。 从受信任的网络位置登录会降低用户的登录风险。
- 配置[基于位置的条件访问](../reports-monitoring/quickstart-configure-named-locations.md)。

![命名位置](./media/active-directory-ops-guide/active-directory-ops-img10.png)

根据优先级，使用下表找到最符合组织需求的推荐解决方案：

| **Priority** | **方案** | 建议 |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| 1 | 如果你使用的是 PHS 或 PTA，但未定义已命名的位置 | 定义已命名位置以提高风险事件的检测 |
| 2 | 如果你是联合的并且未使用 "insideCorporateNetwork" 声明和命名位置， | 定义已命名位置以提高风险事件的检测 |
| 3 | 如果不在条件访问策略中使用命名位置，并且条件访问策略中没有任何风险或设备控制 | 配置条件访问策略以包含命名位置 |
| 4 | 如果你是联合的并且使用的是 "insideCorporateNetwork" 声明，并且尚未定义已命名的位置 | 定义已命名位置以提高风险事件的检测 |
| 5 | 如果你使用的是受信任的 IP 地址而不是命名位置，并将其标记为受信任 | 定义命名位置，并将其标记为受信任，以改善风险事件的检测 |

### <a name="risk-based-access-policies"></a>基于风险的访问策略

Azure AD 可以计算每个登录和每个用户的风险。 使用风险作为访问策略中的条件可以提供更好的用户体验（例如，较少的身份验证提示）和更好的安全性，例如，仅在需要时提示用户，并自动执行响应和修正。

![登录风险策略](./media/active-directory-ops-guide/active-directory-ops-img11.png)

如果你已拥有支持在访问策略中使用风险的 Azure AD Premium P2 许可证，但未使用这些许可证，我们强烈建议将风险添加到安全状况。

#### <a name="risk-based-access-policies-recommended-reading"></a>建议阅读基于风险的访问策略

- [如何：配置登录风险策略](../identity-protection/howto-identity-protection-configure-risk-policies.md)
- [如何：配置用户风险策略](../identity-protection/howto-identity-protection-configure-risk-policies.md)

### <a name="client-application-access-policies"></a>客户端应用程序访问策略

Microsoft Intune 应用管理 (MAM) 提供将数据保护控制（如存储加密、PIN、远程存储清理等）推送到兼容的客户端移动应用程序（例如 Outlook Mobile）的功能。 此外，还可以创建条件访问策略，以 [限制](../conditional-access/app-based-conditional-access.md) 从批准或兼容的应用访问 Exchange Online 等云服务。

如果你的员工安装支持 MAM 的应用程序（如 Office 移动应用程序）来访问公司资源（如 Exchange Online 或 SharePoint Online），并且你还支持 BYOD (自带设备) ，我们建议你在不使用 MDM 注册的情况下部署应用程序 MAM 策略以管理个人拥有的设备中的应用程序

![条件访问授权控制](./media/active-directory-ops-guide/active-directory-ops-img12.png)

如果员工在 Intune 托管设备上安装支持 MAM 的应用程序，而访问权限受到限制，则应考虑部署应用程序 MAM 策略来管理个人设备的应用程序配置，并更新条件性访问策略以仅允许从支持 MAM 的客户端进行访问。

### <a name="conditional-access-implementation"></a>条件访问实现

条件性访问是提高组织的安全状况的重要工具。 因此，请务必遵循以下最佳做法：

- 确保所有 SaaS 应用程序都至少应用了一个策略
- 避免将 " **所有应用** " 筛选器与 **block** 控件合并，以避免锁定风险
- 避免将 **所有用户** 用作筛选器，不小心添加 **来宾**
- **将所有 "旧版" 策略迁移到 Azure 门户**
- 捕获用户、设备和应用程序的所有条件
- 使用条件性访问策略来 [实现 MFA](../conditional-access/plan-conditional-access.md)，而不是使用 **基于用户的 mfa**
- 具有一小部分可应用于多个应用程序的核心策略
- 定义空的异常组，并将它们添加到策略中以获得异常策略
- 规划 [中断玻璃](../roles/security-planning.md#break-glass-what-to-do-in-an-emergency) 帐户，无 MFA 控制
- 通过对 Exchange Online 和 Sharepoint Online 等服务实现一组相同的控件，确保在 Microsoft 365 的客户端应用程序（例如团队、OneDrive、) Outlook 等）上保持一致的体验
- 应通过组而不是个体来实现策略的分配
- 定期检查策略中使用的异常组，以限制用户超出安全状态的时间。 如果你拥有 Azure AD P2，则可以使用访问评审来自动执行此过程

#### <a name="conditional-access-recommended-reading"></a>建议阅读的条件性访问

- [Azure Active Directory 中的条件性访问的最佳做法](../conditional-access/overview.md)
- [标识和设备访问权限配置](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Azure Active Directory 条件访问设置参考](../conditional-access/concept-conditional-access-conditions.md)
- [常见条件访问策略](../conditional-access/concept-conditional-access-policy-common.md)

## <a name="access-surface-area"></a>访问外围应用

### <a name="legacy-authentication"></a>旧式身份验证

强凭据（如 MFA）无法使用旧式身份验证协议保护应用，这使其成为恶意执行组件的首选攻击向量。 锁定旧身份验证对于改善访问安全状况至关重要。

旧身份验证是指应用使用的身份验证协议，例如：

- 不使用新式身份验证的旧版 Office 客户端 (例如，Office 2010 客户端) 
- 使用 IMAP/SMTP/POP 等邮件协议的客户端

攻击者强烈倾向于使用这些协议-事实上，几乎 [100% 的密码喷涂攻击](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984) 使用旧的身份验证协议！ 黑客使用旧的身份验证协议，因为它们不支持交互式登录，这是其他安全挑战（如多重身份验证和设备身份验证）所需要的。

如果你的环境中广泛使用了旧身份验证，则应计划尽快将旧客户端迁移到支持 [新式身份验证](/office365/enterprise/modern-auth-for-office-2013-and-2016) 的客户端。 在同一标记中，如果某些用户已在使用新式身份验证，但其他用户仍使用旧身份验证，则应执行以下步骤来锁定旧版身份验证客户端：

1. 使用 [登录活动报告](../reports-monitoring/concept-sign-ins.md) 来识别仍在使用旧身份验证和计划修正的用户：

   a. 升级到受影响用户的支持新式身份验证的客户端。
   
   b. 计划转换时间范围，按以下步骤进行锁定。
   
   c. 确定哪些旧应用程序对旧式身份验证有硬依赖关系。 请参阅下面的步骤3。

2. 在源 (禁用旧协议，例如 Exchange 邮箱) ，适用于不使用旧身份验证的用户，以免出现更多的风险。
3. 对于其他帐户 (理想的非人身份（如服务帐户) ），使用 [条件访问限制旧协议](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Conditional-Access-support-for-blocking-legacy-auth-is/ba-p/245417) 后的身份验证。

#### <a name="legacy-authentication-recommended-reading"></a>旧身份验证建议读取

- [启用或禁用对 Exchange Server 中的邮箱的 POP3 或 IMAP4 访问](/exchange/clients/pop3-and-imap4/configure-mailbox-access)

### <a name="consent-grants"></a>同意授权

在违法许可授权攻击中，攻击者将创建一个 Azure AD 注册的应用程序，该应用程序请求访问数据（如联系信息、电子邮件或文档）。 当登陆到恶意网站时，用户可能会通过网络钓鱼攻击向恶意应用程序授予许可。

下面列出了你可能想要通过 Microsoft 云服务进行查看的权限：

- 应用或委托应用 \* 。ReadWrite 权限
- 具有委托权限的应用可以代表用户读取、发送或管理电子邮件
- 向授予的应用使用以下权限：

| 资源 | 权限 |
| :- | :- |
| Exchange Online | EAS.AccessAsUser |
| | EWS.AccessAsUser |
| | Mail.Read |
| Microsoft Graph API | Mail.Read |
| | Mail. Read. Shared |
| | Node.js |

- 已授予已登录用户的完全用户模拟的应用。 例如：

|资源 | 权限 |
| :- | :- |
| Microsoft Graph API| Directory.AccessAsUser.All |
| Azure REST API | user_impersonation |

若要避免这种情况，应参阅 [Office 365 中的 "检测和修正违法许可授权](/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) "，以识别和修复具有违法授权的任何应用程序或具有超过所需的授权的应用程序。 接下来， [删除自助服务](../manage-apps/configure-user-consent.md) 并 [建立管理过程](../manage-apps/configure-admin-consent-workflow.md)。 最后，计划应用权限的定期审查，并在不需要时删除它们。

#### <a name="consent-grants-recommended-reading"></a>同意授予建议阅读

- [Microsoft Graph API 权限](/graph/permissions-reference)

### <a name="user-and-group-settings"></a>用户和组设置

如果没有明确的业务需求，则可以锁定以下用户和组设置：

#### <a name="user-settings"></a>用户设置

- **外部用户** -企业中可能会有机外部协作，其中包含团队、Power BI、Sharepoint Online 和 Azure 信息保护等服务。 如果你有显式约束来控制用户启动的外部协作，则建议你通过使用 [Azure AD 的权利管理](../governance/entitlement-management-overview.md) 或受控操作（如通过技术支持）来启用外部用户。 如果你不希望为服务提供随机外部协作，则可以 [阻止成员完全邀请外部用户](../external-identities/delegate-invitations.md)。 此外，也可以在外部用户邀请中 [允许或阻止特定域](../external-identities/allow-deny-list.md) 。
- **应用注册** -启用应用注册后，最终用户可以将应用程序自行加入并向其授予对数据的访问权限。 应用注册的一个典型示例是用户启用 Outlook 插件或语音助手（如 Alexa 和 Siri），以读取其电子邮件和日历或代表他们发送电子邮件。 如果客户决定关闭应用注册，则必须将 InfoSec 和 IAM 团队纳入 (根据业务需求) 需要的应用注册，因为他们需要使用管理员帐户注册应用程序，并且很可能需要设计一个过程来操作该过程。
- **管理门户** -组织可以锁定 Azure 门户中的 Azure AD 边栏选项卡，这样，非管理员就不能访问 Azure 门户中的 Azure AD 管理，因此不会造成混淆。 在 Azure AD 管理门户中转到 "用户设置" 以限制访问：

![管理门户限制访问](./media/active-directory-ops-guide/active-directory-ops-img13.png)

> [!NOTE]
> 非管理员仍可通过命令行和其他编程接口访问 Azure AD 管理接口。

#### <a name="group-settings"></a>组设置

**自助服务组管理/用户可以创建安全组/Microsoft 365 组。** 如果云中没有适用于组的当前自助服务计划，客户可以决定将其关闭，直到它们准备好使用此功能。

#### <a name="groups-recommended-reading"></a>组建议阅读

- [什么是 Azure Active Directory B2B 协作？](../external-identities/what-is-b2b.md)
- [将应用程序与 Azure Active Directory 集成](../develop/quickstart-register-app.md)
- [Azure Active Directory 中的应用、权限和许可。](../develop/quickstart-register-app.md)
- [使用组来管理对中的资源的访问 Azure Active Directory](./active-directory-manage-groups.md)
- [在 Azure Active Directory 中设置自助服务应用程序访问管理](../enterprise-users/groups-self-service-management.md)

### <a name="traffic-from-unexpected-locations"></a>来自意外位置的流量

攻击者来自世界各地的各个部分。 使用条件性访问策略和位置作为条件来管理此风险。 使用条件性访问策略的 [位置条件](../conditional-access/location-condition.md) ，你可以阻止从其登录的位置到的位置的访问。

![创建新命名位置](./media/active-directory-ops-guide/active-directory-ops-img14.png)

如果可用，请使用安全信息和事件管理 (SIEM) 解决方案来分析和查找跨区域访问的模式。 如果不使用 SIEM 产品，或者它不引入 Azure AD 中的身份验证信息，我们建议你使用 [Azure Monitor](../../azure-monitor/overview.md) 来确定跨区域的访问模式。

## <a name="access-usage"></a>访问使用情况

### <a name="azure-ad-logs-archived-and-integrated-with-incident-response-plans"></a>Azure AD 日志存档并与事件响应计划集成

有权访问登录活动、Azure AD 的审核和风险事件对于故障排除、使用情况分析和取证调查至关重要。 Azure AD 通过具有有限保留期的 REST Api 提供对这些源的访问。 安全信息和事件管理 (SIEM) 系统或等效的存档技术，是长期存储审核和可支持性的关键。 若要启用 Azure AD 日志的长期存储，则必须将其添加到现有的 SIEM 解决方案中或使用 [Azure Monitor](../reports-monitoring/concept-activity-logs-azure-monitor.md)。 存档日志，可作为事件响应计划和调查的一部分使用。

#### <a name="logs-recommended-reading"></a>日志建议阅读

- [Azure Active Directory 审核 API 参考](/graph/api/resources/directoryaudit?view=graph-rest-beta%3fview%3dgraph-rest-beta)
- [Azure Active Directory 登录活动报告 API 参考](/graph/api/resources/signin?view=graph-rest-beta%3fview%3dgraph-rest-beta)
- [使用证书通过 Azure AD 报告 API 获取数据](../reports-monitoring/tutorial-access-api-with-certificates.md)
- [Azure Active Directory Identity Protection 的 Microsoft Graph](../identity-protection/howto-identity-protection-graph-api.md)
- [Office 365 管理活动 API 参考](/office/office-365-management-api/office-365-management-activity-api-reference)
- [如何使用 Azure Active Directory Power BI 内容包](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

## <a name="summary"></a>摘要

安全标识基础结构有12个方面。 此列表将帮助你进一步保护和管理凭据、定义身份验证体验、委派分配、衡量使用情况，并根据企业安全状况定义访问策略。

- 将所有者分配给关键任务。
- 实施解决方案来检测薄弱密码或泄漏的密码，提高密码管理和保护，并进一步确保用户对资源的访问权限。
- 管理设备的标识，以便随时和从任何位置保护资源。
- 实现无密码 authentication。
- 在整个组织中提供标准化的单一登录机制。
- 将应用从 AD FS 迁移到 Azure AD，以实现更好的安全性和更一致的可管理性。
- 使用组将用户分配到应用程序，以允许更大的灵活性和大规模管理。
- 配置基于风险的访问策略。
- 锁定旧的身份验证协议。
- 检测并修正违法许可授予。
- 锁定用户和组设置。
- 支持长期存储 Azure AD 日志，以便进行故障排除、使用情况分析和取证调查。

## <a name="next-steps"></a>后续步骤

开始处理 [身份管理操作检查和操作](active-directory-ops-guide-govern.md)。
