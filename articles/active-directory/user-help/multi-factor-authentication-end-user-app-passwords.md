---
title: 如何管理应用密码 - Azure Active Directory | Microsoft Docs
description: 了解应用密码以及它们在双重验证中的作用。
services: active-directory
author: curtand
manager: daveba
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 05/28/2020
ms.author: curtand
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 07303a0b0b3007ade9adb90af7397855a5014cc0
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/13/2021
ms.locfileid: "98179416"
---
# <a name="manage-app-passwords-for-two-step-verification"></a>管理双重验证的应用密码

> [!Important]
>管理员可能不允许你使用应用密码。 如果你没有看到“应用密码”作为选项列出，则它们在你的组织中不可用。

使用应用密码时，请务必记住：

- 应用密码是自动生成的，应为每个应用创建和输入一次。

- 每个用户的密码限制为 40 个。 如果在达到该限制后尝试创建密码，系统会提示删除现有的密码，然后才能创建新密码。

    >[!Note]
    >Office 2013 客户端（包括 Outlook）支持新的身份验证协议，并可用于双重验证。 此支持意味着，在启用双重验证后，不再需要对 Office 2013 客户端使用应用密码。 有关详细信息，请参阅[如何对 Office 2013 和 Office 2016 客户端应用使用新式身份验证](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517)一文。

## <a name="create-new-app-passwords"></a>创建新的应用密码

在初始双重验证注册过程中，系统会提供一个应用密码。 如果需要多个密码，则需要自行创建。 可以根据组织中设置双重验证的方式，从多个区域创建应用密码。 要详细了解如何进行注册以配合使用双重验证和工作或学校帐户，请参阅[双重验证和工作或学校帐户概述](multi-factor-authentication-end-user-first-time.md)及其相关文章。

### <a name="where-to-create-and-delete-your-app-passwords"></a>在哪里创建和删除应用密码

可以根据使用双重验证的方式创建和删除应用密码：

- 你的组织使用了双重验证和“其他安全验证”页。 如果在组织中结合使用工作或学校帐户（例如 alain@contoso.com）和双重验证，则可以在[“其他安全验证”页](https://account.activedirectory.windowsazure.com/Proofup.aspx)中管理应用密码。 有关详细说明，请参阅本文中的[使用“其他安全验证”页创建和删除应用密码](#create-and-delete-app-passwords-from-the-additional-security-verification-page)。

- 你的组织使用了双重验证和 Office 365 门户。 如果你在组织中使用工作或学校帐户 (如 alain@contoso.com) 、双因素验证和 Microsoft 365 应用，则可以从 [Office 365 门户页面](https://www.office.com)管理应用密码。 有关详细说明，请参阅本文中的[使用 Office 365 门户创建和删除应用密码](#create-and-delete-app-passwords-using-the-office-365-portal)。

- 你在使用双重验证和个人 Microsoft 帐户。 如果使用的是个人 Microsoft 帐户（例如，alain@outlook.com）和双重验证，则可以在[“安全基础”页](https://account.microsoft.com/security/)中管理应用密码。 有关详细说明，请参阅[将应用密码用于不支持双重验证的应用](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-and-two-step-verification)。

## <a name="create-and-delete-app-passwords-from-the-additional-security-verification-page"></a>在“其他安全验证”页中创建和删除应用密码

可以在工作或学校帐户的“其他安全验证”页中创建和删除应用密码。

1. 登录[“其他安全验证”页](https://account.activedirectory.windowsazure.com/Proofup.aspx)，然后选择“应用密码”。

    ![“应用密码”页，其中突出显示了”应用密码”选项卡](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

2. 选择“创建”，键入要求使用应用密码的应用的名称，然后选择“下一步” 。

    ![“创建应用密码”页，其中包含要求使用密码的应用的名称](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

3. 从“你的应用密码”页中复制密码，然后选择“关闭” 。

    ![“你的应用密码”页，其中包含指定应用的密码](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

4. 在 " **应用密码** " 页上，确保已列出你的应用。

    ![“应用密码”页，列表中显示了新应用](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

5. 打开为其创建了应用密码的应用（例如，Outlook 2010），然后在系统要求时粘贴应用密码。 只需针对每个应用执行一次此操作。

### <a name="to-delete-an-app-password-using-the-app-passwords-page"></a>使用“应用密码”页删除应用密码

1. 在 " **应用密码** " 页上，选择要删除的应用密码旁边的 " **删除** "。

   ![显示 "应用密码" 页上删除应用密码的屏幕截图](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. 选择“是”确认要删除该密码，然后选择“关闭”。 

    应用密码现已成功删除。

## <a name="create-and-delete-app-passwords-using-the-office-365-portal"></a>使用 Office 365 门户创建和删除应用密码

如果对工作或学校帐户以及 Microsoft 365 应用使用双重验证，则可以使用 Office 365 门户创建和删除应用密码。

### <a name="to-create-app-passwords-using-the-office-365-portal"></a>使用 Office 365 门户创建应用密码

1. 登录到你的工作或学校帐户，中转到 " [我的帐户" 页](https://myaccount.microsoft.com)，然后选择 " **安全信息**"。

    ![显示 "安全信息" 选项卡的 Office 门户](media/multi-factor-authentication-end-user-app-passwords/mfa-security-info.png)

2. 从下拉列表中选择 " **添加方法**"，选择 " **应用密码** "，然后单击 " **添加**"。

    !["安全信息" 页，带有 "添加方法 drowpdown" 列表](media/multi-factor-authentication-end-user-app-passwords/mfa-add-method.png)

3. 输入应用密码的名称，然后选择 " **下一步**"。

    !["创建应用密码" 页，其名称为应用密码](media/multi-factor-authentication-end-user-app-passwords/mfa-enter-app-password-name.png)

4. 复制 " **应用密码** " 页中的密码，然后选择 " **完成**"。

    !["应用密码" 页，其中包含创建的新应用密码](media/multi-factor-authentication-end-user-app-passwords/mfa-copy-app-password.png)

5. 在 " **安全信息** " 页上，确保列出了你的应用密码。

    !["安全信息" 页，其中列出了新的应用密码](media/multi-factor-authentication-end-user-app-passwords/mfa-verify-app-password.png)  

6. 打开创建应用密码 (的应用程序，例如，Outlook 2016) ，然后在要求时粘贴应用密码。 只需针对每个应用执行一次此操作。

### <a name="to-delete-app-passwords-using-the-security-info-page"></a>使用 "安全信息" 页删除应用密码

1. 在 " **安全信息** " 页上，选择要删除的应用密码旁边的 " **删除** "。

   ![显示删除 "安全信息" 页上的应用密码的屏幕截图](media/multi-factor-authentication-end-user-app-passwords/mfa-delete-app-password.png)

2. 在确认框中选择 **"确定"** 。

    应用密码现已成功删除。

## <a name="if-your-app-passwords-arent-working-properly"></a>如果应用密码不正常工作

确保正确键入密码。 如果你确认已正确输入了密码，可以尝试重新登录，并创建新的应用密码。 如果这些做法都不能解决问题，请联系组织的支持人员，请求他们删除现有的应用密码，并让你创建新的密码。

## <a name="next-steps"></a>后续步骤

- [管理双重验证设置](multi-factor-authentication-end-user-manage-settings.md)

- 试用 [Microsoft 验证器应用](user-help-auth-app-download-install.md)以验证是否可以使用应用通知（而不是通过接收短信或电话）登录。
