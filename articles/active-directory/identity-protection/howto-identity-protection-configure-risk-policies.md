---
title: 风险策略-Azure Active Directory Identity Protection
description: 在 Azure Active Directory Identity Protection 中启用并配置风险策略
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 366d68be1a7f115980973015e363da6095876754
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997624"
---
# <a name="how-to-configure-and-enable-risk-policies"></a>如何：配置和启用风险策略

如前一篇文章中所述， [Identity Protection 策略](concept-identity-protection-policies.md) 有两个可在目录中启用的风险策略。 

- 登录风险策略
- 用户风险策略

![启用用户和登录风险策略的 "安全概述" 页](./media/howto-identity-protection-configure-risk-policies/identity-protection-security-overview.png)

这两个策略都可用于自动响应环境中的风险检测，并允许用户在检测到风险时自行修正。 

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]

## <a name="prerequisites"></a>先决条件 

如果你的组织想要允许用户在检测到风险时自行修正，则用户必须注册自助服务密码重置和 Azure AD 多重身份验证。 为了获得最佳体验，我们建议 [启用组合的安全信息注册体验](../authentication/howto-registration-mfa-sspr-combined.md) 。 允许用户在无需管理员干预的情况下更快速地重新获得工作效率。 在事实后，管理员仍然可以看到这些事件并进行调查。 

## <a name="choosing-acceptable-risk-levels"></a>选择可接受的风险级别

组织必须确定他们愿意接受平衡用户体验和安全状况的风险级别。 

Microsoft 建议将 "用户风险策略阈值" 设置为 " **高** "，并将 "登录风险策略" 设置为 " **中" 和 "上**"。

选择“高”阈值可减少触发策略的次数，最大程度地降低对用户的影响。 但是，它不包括策略中的 **低** 和 **中** 风险检测，这可能不会阻止攻击者利用已泄露的标识。 选择 **较低** 的阈值会引入其他用户中断，但会增加安全状况。

## <a name="exclusions"></a>排除项

所有策略都允许排除部分用户（如[应急访问或紧急情况管理员帐户](../roles/security-emergency-access.md)）。 组织可能会根据帐户的使用方式确定他们是否需要从特定策略中排除其他帐户。 应定期检查所有排除项，以查看它们是否仍适用。

在某些风险检测中，标识保护使用已配置的受信任 [网络位置](../conditional-access/location-condition.md) 来减少误报。

## <a name="enable-policies"></a>启用策略

若要启用用户风险和登录风险策略，请完成以下步骤。

1. 导航到 [Azure 门户](https://portal.azure.com)。
1. 浏览到 **Azure Active Directory**  >  **安全**  >  **标识保护**  >  **概述**。
1. 选择 " **用户风险策略**"。
   1. 在 **分配** 下
      1. **用户** -选择 " **所有用户** " **，或选择 "个人和组** " （如果限制你的部署）。
         1. 还可以选择从策略中排除用户。
      1. **条件**  - **用户风险** Microsoft 建议将此选项设置为 "**高**"。
   1. **控件** 下面
      1. **访问** -Microsoft 的建议是 **允许访问** 并 **需要更改密码**。
   1. **强制执行策略**  - **开启**
   1. **保存** -此操作将返回到 " **概述** " 页。
1. 选择 **"登录风险策略"**。
   1. 在 **分配** 下
      1. **用户** -选择 " **所有用户** " **，或选择 "个人和组** " （如果限制你的部署）。
         1. 还可以选择从策略中排除用户。
      1. **条件**  - **登录风险** Microsoft 建议将此选项设置为 "**中" 和 "上**"。
   1. **控件** 下面
      1. **访问** -Microsoft 的建议是 **允许访问** 并 **需要多重身份验证**。
   1. **强制执行策略**  - **开启**
   1. **保存**

## <a name="next-steps"></a>后续步骤

- [启用 Azure AD 多重身份验证注册策略](howto-identity-protection-configure-mfa-policy.md)

- [什么是风险](concept-identity-protection-risks.md)

- [调查风险检测](howto-identity-protection-investigate-risk.md)

- [模拟风险检测](howto-identity-protection-simulate-risk.md)
