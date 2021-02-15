---
title: SSPR 和 Azure AD 多重身份验证的组合注册-Azure Active Directory
description: 了解 Azure Active Directory 的组合注册体验，让用户注册 Azure AD 多重身份验证和自助服务密码重置
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 112ad0714c84cd3be08788b3277f52372f6d0373
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98938477"
---
# <a name="combined-security-information-registration-for-azure-active-directory-overview"></a>Azure Active Directory 概述的组合安全信息注册

在组合注册之前，用户会为 Azure AD 多重身份验证和自助服务密码重置 (SSPR) 单独注册身份验证方法。 人们搞糊涂，使用了类似的方法进行多重身份验证和 SSPR，但他们不得不注册这两种功能。 现在，通过组合注册，用户可以注册一次，并获得多重身份验证和 SSPR 的优势。

> [!NOTE]
> 自2020年8月15日起，将自动为所有新的 Azure AD 租户启用合并注册。 

本文概述了安全注册的组合。 若要开始结合安全注册，请参阅以下文章：

> [!div class="nextstepaction"]
> [启用组合的安全注册](howto-registration-mfa-sspr-combined.md)

![我的帐户显示用户的已注册安全信息](media/concept-registration-mfa-sspr-combined/combined-security-info-defaults-registered.png)

在启用新体验之前，请查看此以管理员为中心的文档和以用户为中心的文档，以确保你了解此功能的功能和效果。 根据 [用户文档](../user-help/security-info-setup-signin.md) 的培训，为用户准备新体验，并帮助确保成功部署。

Azure AD 组合的安全信息注册当前不适用于 Azure 德国或 Azure 中国世纪互联的国内云。 适用于 Azure 美国政府版。

> [!IMPORTANT]
> 同时启用了原始预览和增强组合注册体验的用户将看到新的行为。 同时启用这两种体验的用户只会看到我的帐户体验。 *我的帐户* 与组合注册的外观一致，并为用户提供无缝体验。 用户可以通过转到查看我的帐户 [https://myaccount.microsoft.com](https://myaccount.microsoft.com) 。
>
> 尝试访问安全信息选项时可能会遇到错误消息，如 "抱歉，我们无法登录"。 确认在 web 浏览器中没有任何配置或组策略对象阻止第三方 cookie。

*我的帐户* 页面根据访问页面的计算机的语言设置进行本地化。 Microsoft 存储了浏览器缓存中使用的最新语言，因此后续访问页面的尝试将继续以最后使用的语言呈现。 如果清除缓存，则页面将重新呈现。

如果要强制使用特定语言，则可以将添加 `?lng=<language>` 到 URL 的末尾，其中 `<language>` 是要呈现的语言的代码。

![设置 SSPR 或其他安全验证方法](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>组合注册中的可用方法

组合注册支持以下身份验证方法和操作：

| 方法 | 注册 | 更改 | 删除 |
| --- | --- | --- | --- |
| Microsoft Authenticator | 是 (最多 5)  | 否 | 是 |
| 其他验证器应用 | 是 (最多 5)  | 否 | 是 |
| 硬件令牌 | 否 | 否 | 是 |
| 电话 | 是 | 是 | 是 |
| 备用号码 | 是 | 是 | 是 |
| 办公电话 | 是 | 是 | 是 |
| 电子邮件 | 是 | 是 | 是 |
| 安全性问题 | 是 | 否 | 是 |
| 应用密码 | 是 | 否 | 是 |
| FIDO2 安全密钥<br />*托管模式，仅从 " [安全信息](https://mysignins.microsoft.com/security-info) " 页*| 是 | 是 | 是 |

> [!NOTE]
> 应用密码仅适用于已强制执行多重身份验证的用户。 对于通过条件性访问策略启用多重身份验证的用户，不能使用应用密码。

用户可以将以下选项之一设置为默认多重身份验证方法：

- Microsoft Authenticator –通知。
- 验证器应用或硬件令牌-代码。
- 电话呼叫。
- 短信。

随着我们不断向 Azure AD 添加更多身份验证方法，这些方法可用于合并注册。

## <a name="combined-registration-modes"></a>组合注册模式

组合注册分为两种模式：中断和管理。

- **中断模式** 是一种类似于向导的体验，当用户在登录时注册或刷新其安全信息时，它会向用户提供。
- **管理模式** 是用户配置文件的一部分，允许用户管理其安全信息。

对于这两种模式，以前注册了可用于多重身份验证的方法的用户需要先执行多重身份验证，然后才能访问其安全信息。 用户必须确认其信息，然后才能继续使用以前注册的方法。 

### <a name="interrupt-mode"></a>中断模式

如果同时为你的租户启用了多因素身份验证和 SSPR 策略，则组合注册会考虑这两个策略。 这些策略控制在登录期间用户是否被中断以便注册，以及哪些方法可用于注册。

下面是可能提示用户注册或刷新其安全信息的示例方案：

- *通过 Identity Protection 强制实施多重身份验证注册：* 要求用户在登录时注册。 如果为用户启用了 SSPR) ，则它们将注册多重身份验证方法和 SSPR 方法 (。
- *通过每用户多重身份验证强制实施多重身份验证注册：* 要求用户在登录时注册。 如果为用户启用了 SSPR) ，则它们将注册多重身份验证方法和 SSPR 方法 (。
- *通过条件访问或其他策略强制实施多重身份验证注册：* 要求用户在使用需要多重身份验证的资源时进行注册。 如果为用户启用了 SSPR) ，则它们将注册多重身份验证方法和 SSPR 方法 (。
- *强制执行注册 SSPR：* 要求用户在登录时注册。 它们只注册 SSPR 方法。
- 已 *强制执行 SSPR 刷新：* 用户需要在管理员设置的时间间隔内检查其安全信息。用户显示其信息，并可以确认当前信息，或在需要时进行更改。

强制执行注册后，用户会看到所需的最小方法数必须符合多重身份验证和 SSPR 策略（从最高到最安全）。

请考虑以下示例场景：

- 已为 SSPR 启用用户。 SSPR 策略需要两种方法来重置和启用移动应用代码、电子邮件和手机。
- 此用户需要注册两个方法。
   - 默认情况下，用户显示为 "身份验证器应用和电话"。
   - 用户可以选择注册电子邮件，而不是验证身份验证应用或电话。

以下流程图介绍了在登录期间中断注册时向用户显示的方法：

![组合安全信息流程图](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

如果同时启用了多重身份验证和 SSPR，我们建议强制实施多重身份验证注册。

如果 SSPR 策略要求用户定期检查其安全信息，则用户会在登录期间中断，并显示其所有注册方法。 如果是最新的，他们可以确认当前信息，如果需要，也可以进行更改。 访问此页时，用户必须执行多重身份验证。

### <a name="manage-mode"></a>管理模式

用户可以访问 "管理" 模式，方法是转到 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) 或从 "我的帐户" 中选择 " **安全信息** "。 用户可以在该处添加方法、删除或更改现有方法、更改默认方法等。

## <a name="key-usage-scenarios"></a>关键使用方案

### <a name="set-up-security-info-during-sign-in"></a>在登录过程中设置安全信息

管理员已强制执行注册。

用户尚未设置所有所需的安全信息，并转到 Azure 门户。 输入用户名和密码后，系统会提示用户设置安全信息。 然后，用户按照向导中显示的步骤来设置所需的安全信息。 如果设置允许，则用户可以选择设置默认情况下显示的其他方法。 完成向导后，用户将查看他们设置的方法及其用于多重身份验证的默认方法。 若要完成安装过程，用户需要确认信息并继续 Azure 门户。

### <a name="set-up-security-info-from-my-account"></a>设置我的帐户的安全信息

管理员尚未强制执行注册。

尚未设置所有必需的安全信息的用户将进入 [https://myaccount.microsoft.com](https://myaccount.microsoft.com) 。 用户在左窗格中选择 " **安全信息** "。 用户从此处选择添加方法，选择可用的任何方法，然后按照步骤设置该方法。 完成后，用户将看到在 "安全信息" 页上设置的方法。

### <a name="delete-security-info-from-my-account"></a>从我的帐户中删除安全信息

之前设置了至少一个方法的用户将导航到 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) 。 用户选择删除以前注册的方法之一。 完成后，用户将不再在 "安全信息" 页上看到该方法。

### <a name="change-the-default-method-from-my-account"></a>更改我的帐户的默认方法

以前设置了至少一个可用于多重身份验证的方法的用户将导航到 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) 。 用户将当前默认方法更改为其他默认方法。 完成后，用户将看到 "安全信息" 页上的新默认方法。

## <a name="next-steps"></a>后续步骤

若要开始使用，请参阅 [启用自助服务密码重置](tutorial-enable-sspr.md) 和 [启用 Azure AD 多重身份验证](tutorial-enable-azure-mfa.md)的教程。

了解如何 [在租户中启用组合注册](howto-registration-mfa-sspr-combined.md) ，或 [强制用户重新注册身份验证方法](howto-mfa-userdevicesettings.md#manage-user-authentication-options)。

你还可以查看 [Azure AD 多重身份验证和 SSPR 的可用方法](concept-authentication-methods.md)。
