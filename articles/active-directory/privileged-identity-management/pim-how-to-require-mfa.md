---
title: MFA 或2FA 和 Privileged Identity Management Azure AD |Microsoft Docs
description: 了解 Azure AD Privileged Identity Management (PIM) 如何验证多重身份验证 (MFA)。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4344e769cc8466287dab1e98e95cc3fbe705ffbd
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2020
ms.locfileid: "94834993"
---
# <a name="multi-factor-authentication-and-privileged-identity-management"></a>多重身份验证和 Privileged Identity Management

我们建议要求所有管理员使用多重身份验证 (MFA)。 这可降低因密码泄露而受到攻击的风险。

可以要求用户在登录时完成多重身份验证质询。 还可以要求用户在 Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 中激活角色时完成多重身份验证质询。 这样一来，如果用户在登录时未完成多重身份验证质询，Privileged Identity Management 会提示他们完成此操作。

> [!IMPORTANT]
> 目前，Azure AD 多重身份验证仅适用于工作或学校帐户，而不是 Microsoft 个人帐户 (通常是用于登录 Microsoft 服务（如 Skype、Xbox 或 Outlook.com) ）的个人帐户。 因此，使用个人帐户的任何人都不是符合条件的管理员，因为他们无法使用多重身份验证激活其角色。 如果这些用户需要继续使用 Microsoft 帐户管理工作负荷，请立即将其提升到永久管理员。

## <a name="how-pim-validates-mfa"></a>PIM 如何验证 MFA

当用户激活角色时，有两个选项可用于验证多重身份验证。

最简单的选项是依赖于正在激活特权角色的用户 Azure AD 多重身份验证。 为此，请首先检查这些用户是否获得许可，如有必要，是否已注册 Azure AD 多重身份验证。 有关如何部署 Azure AD 多重身份验证的详细信息，请参阅 [部署基于云的 Azure AD 多重身份验证](../authentication/howto-mfa-getstarted.md)。 建议（但不是必需）将 Azure AD 配置为在用户登录时针对这些用户强制执行多重身份验证。 这是因为多重身份验证检查将由 Privileged Identity Management 本身进行。

或者，如果用户在本地进行身份验证，你可以让标识提供者负责多重身份验证。 例如，如果你已将 AD 联合身份验证服务配置为在访问 Azure AD 之前要求使用基于智能卡的身份验证，则 [使用 Azure AD 多重身份验证保护云资源和 AD FS](../authentication/howto-mfa-adfs.md) 包括配置 AD FS 以将声明发送到 Azure AD 的说明。 当用户尝试激活某个角色时，Privileged Identity Management 将在收到相应声明后，接受已为该用户验证的多因素身份验证。

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中配置 Azure AD 角色设置](pim-how-to-change-default-settings.md)
- [在 Privileged Identity Management 中配置 Azure 资源角色设置](pim-resource-roles-configure-role-settings.md)
