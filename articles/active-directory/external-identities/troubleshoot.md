---
title: B2B 协作故障排除 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B 协作的常见问题的补救措施
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: troubleshooting
ms.date: 02/12/2021
tags: active-directory
ms.author: mimart
author: msmimart
ms.reviewer: mal
ms.custom:
- it-pro
- seo-update-azuread-jan"
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60cd944ecb144a30e872259f6e959a11c3ea6319
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100365423"
---
# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Azure Active Directory B2B 协作故障排除

以下是 Azure Active Directory (Azure AD) B2B 协作的常见问题的一些补救措施。

   > [!IMPORTANT]
   > - 从 2021 年 1 月 4 日开始，Google 将[弃用 WebView 登录支持](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html)。 如果要通过 Gmail 使用 Google 联合身份验证或自助服务注册，则应[测试业务线本机应用程序的兼容性](google-federation.md#deprecation-of-webview-sign-in-support)。
   > - **从2021年10月开始**，Microsoft 将不再支持通过创建非托管 Azure AD 帐户和用于 B2B 协作方案的租户来兑换邀请。 在准备期间，我们鼓励客户选择参与[电子邮件一次性密码身份验证](one-time-passcode.md)。 我们欢迎你提供有关此公共预览版功能的反馈，并且很乐意创建更多的协作方式。

## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>我已添加外部用户，但在全局通讯簿或人员选取器中看不到这些用户

在外部用户未填充到列表中的情况下，可能需要几分钟复制对象。

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>B2B 来宾用户没有显示在 SharePoint Online/OneDrive 人员选取器中

默认情况下，搜索现有来宾用户的功能在 SharePoint Online (SPO) 人员选取器中处于“关闭”状态以匹配旧行为。

可使用“ShowPeoplePickerSuggestionsForGuestUsers”设置在租户和网站集级别启用此功能。 可使用 Set-SPOTenant 和 Set-SPOSite cmdlet 设置此功能，这将允许用户搜索目录中的所有现有来宾用户。 租户范围中的更改不会影响已经预配的 SPO 站点。

## <a name="invitations-have-been-disabled-for-directory"></a>已对目录禁用邀请

如果收到无权邀请用户的通知，请在“Azure Active Directory”>“用户设置”>“外部用户”>“管理外部协作设置”下验证你的用户帐户是否有权邀请外部用户：

![显示了”外部用户”设置的屏幕截图](media/troubleshoot/external-user-settings.png)

如果最近修改了这些设置或为用户分配了“来宾邀请者”角色，可能有 15-60 分钟的延迟更改才生效。

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>我邀请的用户在兑换过程中收到错误

常见错误包括：

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>当发生以下情况时，被邀请者的管理员禁止在其租户中创建电子邮件验证的用户

受邀用户所在组织正在使用 Azure Active Directory，但其中不存在特定用户帐户（例如，用户不存在于 AAD contoso.comAzure AD contoso.com 中）。 contoso.com 的管理员可能会设置一个策略以阻止创建用户。 用户必须向其管理员进行核实以确定是否允许外部用户。 外部用户的管理员可能需要在其域中允许电子邮件验证的用户（请参阅有关允许电子邮件验证的用户的此[文章](/powershell/module/msonline/set-msolcompanysettings)）。

![错误，指出租户不允许经电子邮件验证的用户](media/troubleshoot/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>外部用户尚未存在于联合域中

如果使用联合身份验证，并且用户在 Azure Active Directory 中不存在，则无法邀请该用户。

要解决此问题，外部用户的管理员必须将该用户的帐户同步到 Azure Active Directory。

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>“\#”（这通常不是有效字符）如何与 Azure AD 进行同步？

由于受邀帐户 user@contoso.com 变为 user_contoso.com#EXT#@fabrikam.onmicrosoft.com，“\#”是 Azure AD B2B 协作或外部用户的 UPN 中的保留字符。 因此，不允许来自本地的 UPN 中的 \# 登录到 Azure 门户。 

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>我将外部用户添加到同步组时，收到错误

外部用户只能添加到“已分配”或“安全”组，而不能分配到在本地控制的组。

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>我的外部用户未收到用于兑换的电子邮件

被邀请者应该向其 ISP 或垃圾邮件筛选器查询，以确保允许以下地址：Invites@microsoft.com

## <a name="i-notice-that-the-custom-message-does-not-get-included-with-invitation-messages-at-times"></a>我发现邀请消息有时不包含自定义消息

为遵守隐私法规，在以下情况下，API 不会在电子邮件邀请中包含自定义邮件：

- 邀请方在邀请租户中没有电子邮件地址
- 应用服务主体发送邀请

如果此方案非常重要，则可取消 API 邀请电子邮件，并通过所选的电子邮件机制发送邮件。 请咨询所属组织的法律顾问，确保通过这种方式发送的任何电子邮件均符合隐私法规。

## <a name="you-receive-an-aadsts65005-error-when-you-try-to-log-in-to-an-azure-resource"></a>尝试登录到 Azure 资源时收到“AADSTS65005”错误

具有来宾帐户的某个用户无法登录，并收到以下错误消息：

```plaintext
    AADSTS65005: Using application 'AppName' is currently not supported for your organization contoso.com because it is in an unmanaged state. An administrator needs to claim ownership of the company by DNS validation of contoso.com before the application AppName can be provisioned.
```

该用户具有 Azure 用户帐户，是已被放弃的或非托管的病毒性租户。 此外，租户中没有全局管理员。

若要解决此问题，你必须接管被放弃的租户。 请参阅  [在 Azure Active Directory 中以管理员身份接管非托管目录](../enterprise-users/domains-admin-takeover.md)。 你还必须访问相关域后缀的面向 Internet 的 DNS，以便提供直接证据来证明你控制着该命名空间。 在租户返回到托管状态后，请与客户讨论保留用户和经验证的域名是否是其组织的最佳选择。

## <a name="a-guest-user-with-a-just-in-time-or-viral-tenant-is-unable-to-reset-their-password"></a>具有实时或“病毒性”租户的来宾用户无法重置其密码

如果标识租户是实时 (JIT) 或病毒性租户（独立的不受管 Azure 租户），则只有来宾用户可以重置其密码。 有时，组织会接管在员工使用其工作电子邮件地址注册服务时创建 [的病毒租户的管理](../enterprise-users/domains-admin-takeover.md) 。 组织接管病毒性租户后，只有该组织中的管理员可以重置用户密码或启用 SSPR。 如果需要，作为邀请方组织，你可以从目录中删除来宾用户帐户并重新发送邀请。

## <a name="a-guest-user-is-unable-to-use-the-azuread-powershell-v1-module"></a>来宾用户无法使用 AzureAD PowerShell V1 模块

从 2019 年 11 月 18 日开始，你的目录中的来宾用户（所定义的其 userType  属性为 Guest  的用户帐户）被系统阻止使用 AzureAD PowerShell V1 模块。 之后，用户需要成为成员用户（userType  为 Member  ）或使用 AzureAD PowerShell V2 模块。

## <a name="in-an-azure-us-government-tenant-i-cant-invite-a-b2b-collaboration-guest-user"></a>在 Azure 美国政府租户中，无法邀请 B2B 协作来宾用户

在 Azure 美国政府版云中，当前仅支持在 Azure 美国政府云中的租户之间进行 B2B 协作，并且两者都支持 B2B 协作。 如果你邀请不属于 Azure 美国政府云的租户中的用户，或者不支持 B2B 协作，则会出现错误。 有关详细信息和限制，请参阅 [Azure Active Directory Premium P1 和 P2 变体](../../azure-government/compare-azure-government-global-azure.md#azure-active-directory-premium-p1-and-p2)。

## <a name="i-receive-the-error-that-azure-ad-cannot-find-the-aad-extensions-app-in-my-tenant"></a>我收到了一个错误，Azure AD 在我的租户中找不到 aad 扩展应用

使用自助服务注册功能（如自定义用户属性或用户流）时， `aad-extensions-app. Do not modify. Used by AAD for storing user data.` 会自动创建一个名为的应用。 Azure AD 外部标识使用它来存储有关注册和收集的自定义属性的用户的信息。

如果将 `aad-extensions-app` 意外删除，则你有 30 天的时间可以恢复它。 你可以使用 Azure AD PowerShell 模块还原应用。

1. 启动 Azure AD PowerShell 模块并运行 `Connect-AzureAD` 。
1. 以全局管理员身份登录要恢复已删除应用的 Azure AD 租户。
1. 运行 PowerShell 命令 `Get-AzureADDeletedApplication` 。
1. 在列表中查找显示名称以开头的应用程序 `aad-extensions-app` ，并复制其 `ObjectId` 属性值。
1. 运行 PowerShell 命令 `Restore-AzureADDeletedApplication -ObjectId {id}` 。 将 `{id}` 命令的部分替换为 `ObjectId` 上一步中的。

现在，应会在 Azure 门户中看到已还原的应用程序。

## <a name="next-steps"></a>后续步骤

[获取对 B2B 协作的支持](../fundamentals/active-directory-troubleshooting-support-howto.md)
