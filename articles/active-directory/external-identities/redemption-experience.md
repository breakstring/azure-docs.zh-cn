---
title: B2B 协作中的邀请兑换 - Azure AD
description: 介绍最终用户的 Azure AD B2B 协作邀请兑换体验，包括对隐私条款的同意。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08f560f076caf90c9c930cedfd6a7ba9c6c8b37d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100365440"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Azure Active Directory B2B 协作邀请兑换

本文介绍来宾用户可以访问资源以及所遇到的同意过程的方式。 如果向来宾发送邀请电子邮件，邀请会附有一个链接，来宾可以兑换该链接来访问应用或门户。 邀请电子邮件只是来宾可以访问资源的其中一种方式。 或者，可以将来宾添加到目录并为其提供指向要共享的门户或应用的直接链接。 无论他们使用哪种方法，都会指导来宾完成首次同意过程。 此过程可确保来宾同意隐私条款，并接受已设置的任何[使用条款](../conditional-access/terms-of-use.md)。

将来宾用户添加到目录时，来宾用户帐户的同意状态（可在 PowerShell 中查看）最初设置为“PendingAcceptance”。 在来宾接受邀请并同意隐私策略和使用条款之前，此设置一直保留。 之后，同意状态将更改为“已接受”，并且不再向来宾显示同意页面。

   > [!IMPORTANT]
   > - 从 2021 年 1 月 4 日开始，Google 将[弃用 WebView 登录支持](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html)。 如果要通过 Gmail 使用 Google 联合身份验证或自助服务注册，则应[测试业务线本机应用程序的兼容性](google-federation.md#deprecation-of-webview-sign-in-support)。
   > - **从2021年10月开始**，Microsoft 将不再支持通过创建非托管 Azure AD 帐户和用于 B2B 协作方案的租户来兑换邀请。 在准备期间，我们鼓励客户选择参与[电子邮件一次性密码身份验证](one-time-passcode.md)。 我们欢迎你提供有关此公共预览版功能的反馈，并且很乐意创建更多的协作方式。

## <a name="redemption-through-the-invitation-email"></a>通过邀请电子邮件兑换

[使用 Azure 门户](./b2b-quickstart-add-guest-users-portal.md)将来宾用户添加到目录时，会在该过程中向来宾发送邀请电子邮件。 你还可以选择在[使用 PowerShell](./b2b-quickstart-invite-powershell.md) 将来宾用户添加到目录时发送邀请电子邮件。 下面是来宾在兑换电子邮件中的链接时的体验说明。

1. 来宾收到从 Microsoft 邀请发送的[邀请电子邮件](./invitation-email-elements.md)。
2. 来宾选择电子邮件中的“接受邀请”。
3. 来宾将使用其自己的凭据登录到目录。 如果来宾没有可联合到目录的帐户，且未启用[电子邮件一次性密码 (OTP)](./one-time-passcode.md) 功能；系统将提示来宾创建个人 [MSA](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) 或 [Azure AD 自助服务帐户](../enterprise-users/directory-self-service-signup.md)。 有关详细信息，请参阅[邀请兑换流](#invitation-redemption-flow)。
4. 将指导来宾完成下面所述的[同意体验](#consent-experience-for-the-guest)。

## <a name="redemption-through-a-direct-link"></a>通过直接链接兑换

作为邀请电子邮件的替代方法，你可以为来宾提供指向应用或门户的直接链接。 首先需要通过 [Azure 门户](./b2b-quickstart-add-guest-users-portal.md)或 [PowerShell](./b2b-quickstart-invite-powershell.md) 将来宾用户添加到目录。 然后，可以使用[将应用程序部署到用户的可自定义方式](../manage-apps/end-user-experiences.md)（包括直接登录链接）。 当来宾使用直接链接而不是邀请电子邮件时，仍将指导他们完成首次同意体验。

> [!IMPORTANT]
> 直接链接必须特定于租户。 换句话说，它必须包含租户 ID 或已验证的域，以便可以在共享应用所在的租户中对来宾进行身份验证。 常见的 URL（如 https://myapps.microsoft.com ）对来宾不起作用，因为它将重定向到其主租户进行身份验证。 下面是包含租户上下文的直接链接的一些示例：
 > - 应用访问面板：`https://myapps.microsoft.com/?tenantid=<tenant id>`
 > - 已验证域的应用访问面板：`https://myapps.microsoft.com/<;verified domain>`
 > - Azure 门户：`https://portal.azure.com/<tenant id>`
 > - 单个应用：了解如何使用[直接登录链接](../manage-apps/end-user-experiences.md#direct-sign-on-links)

在某些情况下，建议通过直接链接发送邀请电子邮件。 如果这些特殊情况对贵组织而言很重要，我们建议使用仍会发送邀请电子邮件的方法来邀请用户：
 - 用户在联合组织中没有 Azure AD 帐户、MSA 或电子邮件帐户。 除非你使用一次性密码功能，否则来宾需要兑换邀请电子邮件才能指导完成创建 MSA 的步骤。
 - 有时，由于与联系人对象（例如，Outlook 联系人对象）存在冲突，受邀用户对象可能会没有电子邮件地址。 在这种情况下，用户必须单击邀请电子邮件中的兑换 URL。
 - 用户可使用受邀电子邮件地址的别名登录。 （别名指与电子邮件帐户关联的其他电子邮件地址。）在这种情况下，用户必须单击邀请电子邮件中的兑换 URL。

## <a name="invitation-redemption-flow"></a>邀请兑换流

当用户单击[邀请电子邮件](invitation-email-elements.md)中的“接受邀请”链接时，Azure AD 会根据兑换流自动兑换邀请，如下所示：

![显示兑换流关系图的屏幕截图](media/redemption-experience/invitation-redemption-flow.png)

如果用户的用户主体名称 (UPN) 与现有 Azure AD 和个人 MSA 帐户相匹配，系统会提示用户选择兑换所用的帐户。*

1. Azure AD 执行基于用户的发现，以确定用户是否存在于[现有 Azure AD 租户](./what-is-b2b.md#easily-invite-guest-users-from-the-azure-ad-portal)中。

2. 如果管理员已启用[直接联合](./direct-federation.md)，Azure AD 会检查用户的域后缀是否与已配置的 SAML/WS-Fed 标识提供程序的域相匹配，并将用户重定向到预配置的标识提供程序。

3. 如果管理员已启用 [Google 联合](./google-federation.md)，Azure AD 会检查用户的域后缀是 gmail.com 还是 googlemail.com，并将用户重定向到 Google。

4. 兑换过程会检查用户是否具有现有个人 [Microsoft 帐户 (MSA)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create)。

5. 标识用户的主目录后，用户将发送到相应的标识提供程序进行登录。  

6. 如果步骤 1 到 4 找不到受邀用户的主目录，Azure AD 会确定邀请租户是否已为来宾启用[电子邮件一次性密码 (OTP)](./one-time-passcode.md) 功能。

7. 如果 [已为来宾启用电子邮件一次性密码](./one-time-passcode.md#when-does-a-guest-user-get-a-one-time-passcode)，密码将通过邀请的电子邮件发送到用户。 用户将在 Azure AD 登录页面中检索并输入此密码。

8. 如果已为来宾禁用电子邮件一次性密码，Azure AD 会检查域后缀，以确定它是否属于使用者帐户。 如果是这样，系统会提示用户创建个人 [Microsoft 帐户](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create)。 否则，系统会提示用户创建 [Azure AD 自助服务帐户](../enterprise-users/directory-self-service-signup.md)。

9. Azure AD 尝试通过验证对电子邮件的访问权限来创建 [Azure AD 自助服务帐户](../enterprise-users/directory-self-service-signup.md)。 通过向电子邮件发送代码，让用户检索该代码并将其提交到 Azure AD 来验证帐户。 但是，如果受邀用户的租户是联合的，或者如果在受邀用户的租户中将 AllowEmailVerifiedUsers 字段设置为 false，则用户无法完成兑换，并且流会导致错误。 有关详细信息，请参阅 [Azure Active Directory B2B 协作疑难解答](./troubleshoot.md#the-user-that-i-invited-is-receiving-an-error-during-redemption)。

10. 系统会提示用户创建个人 [Microsoft 帐户 (MSA)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create)。

11. 对正确的标识提供程序进行身份验证后，用户将重定向到 Azure AD 以完成[同意体验](#consent-experience-for-the-guest)。  

对于实时 (JIT) 兑换，通过租户应用程序链接兑换时，步骤 8 到步骤 10 不可用。 如果用户到达步骤 6 且未启用电子邮件一次性密码功能，则用户会收到一条错误消息，并且无法兑换邀请。 若要防止此错误，管理员应[启用电子邮件一次性密码](./one-time-passcode.md#when-does-a-guest-user-get-a-one-time-passcode)或确保用户单击邀请链接。

## <a name="consent-experience-for-the-guest"></a>来宾的同意体验

来宾在首次登录以访问合作伙伴组织中的资源后，会指导他们完成以下页面。 

1. 来宾查看描述邀请组织的隐私声明的“查看权限”页。 用户必须“接受”根据邀请组织的隐私策略使用其信息才能继续。

   ![显示“查看权限”页的屏幕截图](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > 有关如何以租户管理员身份链接到组织隐私声明的信息，请参阅[如何：在 Azure Active Directory 中添加组织的隐私信息](../fundamentals/active-directory-properties-area.md)。

2. 如果已配置使用条款，来宾将打开并查看使用条款，然后选择“接受”。 

   ![显示新使用条款的屏幕截图](media/redemption-experience/terms-of-use-accept.png) 

   可以在“外部标识” > “使用条款”中配置[使用条款](../conditional-access/terms-of-use.md)。

3. 除非另行指定，否则来宾将重定向到应用访问面板，其中列出了来宾可以访问的应用程序。

   ![显示应用访问面板的屏幕截图](media/redemption-experience/myapps.png) 

在目录中，来宾的“已接受邀请”值更改为“是” 。 如果创建了 MSA，则来宾的“源”将显示“Microsoft 帐户”。 有关来宾用户帐户属性的详细信息，请参阅 [Azure AD B2B 协作用户的属性](user-properties.md)。 

## <a name="next-steps"></a>后续步骤

- [什么是 Azure AD B2B 协作？](what-is-b2b.md)
- [在 Azure 门户中添加 Azure Active Directory B2B 协作用户](add-users-administrator.md)
- [信息工作者如何将 B2B 协作用户添加到 Azure Active Directory？](add-users-information-worker.md)
- [使用 PowerShell 添加 Azure Active Directory B2B 协作用户](customize-invitation-api.md#powershell)
- [以来宾用户身份离开组织](leave-the-organization.md)