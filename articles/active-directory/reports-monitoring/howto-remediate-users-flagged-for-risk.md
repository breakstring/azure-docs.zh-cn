---
title: 在 Azure Active Directory 门户中标记为有风险的用户 |Microsoft Docs
description: 了解 Azure Active Directory 门户中“标记为风险用户”的用户的安全报告
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23bd7a26bc6215f50a2860ab4aac84f1b46301f5
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2020
ms.locfileid: "92308944"
---
# <a name="remediate-users-flagged-for-risk-in-the-azure-active-directory-portal"></a>修正 Azure Active Directory 门户中“标记为风险用户”的用户

使用 Azure Active Directory (Azure AD) 中的安全报告，你可以评估你的环境中用户帐户泄露的可能性。 已标记为存在风险的用户是指可能已泄露的用户帐户。

Microsoft 致力于保护你的环境的安全。 为此，Microsoft 持续监视那些不正常的活动或符合已知攻击模式的活动。 

如果检测到不正常的活动，表明有人对你的部分用户帐户进行了非授权的访问，则系统会向你发送通知，让你采取行动。 这并非意味着 Microsoft 自己的系统遭到侵害。

## <a name="access-the-users-flagged-for-risk-report"></a>访问标记为存在风险的用户的报告

可以在 Azure 门户中通过[“有风险的用户”](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RiskyUsers)报告查看已标记为存在风险的用户。 如果没有 Azure AD，可以免费注册 [https://aka.ms/AccessAAD](https://aka.ms/AccessAAD) 。 

从“已标记为存在风险的用户”报告中，你可以针对每个用户采取以下操作：

- 生成临时密码
- 要求用户在下次登录时安全地重置其密码
- 无需执行任何更正操作，即可消除用户风险。

有关详细信息，请参阅[“已标记为存在风险的用户”安全报告](../identity-protection/overview-identity-protection.md)。

### <a name="azure-ad-subscription-for-microsoft-365-customers"></a>Microsoft 365 客户 Azure AD 订阅

你还可以使用你的 Microsoft 365 凭据访问 **Azure 管理中心**。 激活对 Azure AD 的访问权限以后，就会重定向到 Azure AD 门户。 在基本订阅级别，报告中提供的详细信息量是受限制的。 Azure 高级订户可获取其他的数据和分析。

若要访问 Microsoft 365 管理中心中 **标记为风险** 报告的用户：

1.  在左侧导航菜单中，选择“管理中心”****。 
2.  选择 **Azure AD**。
3.  登录到 **Azure Active Directory 管理中心**。
4.  如果页面顶部显示一个横幅，要求“查看新门户”，请选择相应的链接。****
4.  在左侧导航菜单中，选择“Azure Active Directory”。**** 
5.  在导航窗格中，从“安全性”部分中选择“已标记为存在风险的用户”。********

## <a name="remediation-actions"></a>修正操作

采取以下措施修正受影响的帐户，确保环境安全：

1.  [验证](https://aka.ms/MFAValid)多重身份验证和自助密码重置的信息是否正确。 
2.  为所有用户[启用多重身份验证](/microsoft-365/admin/security-and-compliance/set-up-multi-factor-authentication)。 
3.  对每个受影响的帐户使用此[修正脚本](https://aka.ms/remediate)以自动执行以下步骤： 

    a. 重置密码以保护帐户安全并终止活动会话。

    b. 删除邮箱委托。

    c. 禁用针对外部域的邮件转发规则。

    d. 删除邮箱上的全局邮件转发属性。

    e. 在用户的帐户上启用 MFA。

    f. 将帐户的密码复杂性设置为高。

    g. 启用邮箱审核。

    h. 生成审核日志供管理员查看。

4. 调查 Microsoft 365 租户和其他 IT 基础结构，包括查看所有租户设置、用户帐户以及每用户的配置设置，以便进行可能的修改。 查看是否存在使用持久性方法的痕迹，以及是否存在入侵者利用初始据点获取 VPN 凭据或访问其他组织资源的痕迹。 

5.  在调查过程中，考虑是否应当通知政府机构，包括执法机构。

除此之外，还应采取以下措施：

- 在解决异常活动问题时，阅读并实施此[指南](/archive/blogs/office365security/how-to-fix-a-compromised-hacked-microsoft-office-365-account)。 
- [启用审核管道](/archive/blogs/office365security/using-office-365-activity-data-to-improve-your-cybersecurity-stance-and-capability)来帮助你分析在租户上进行的活动。 完成后，系统会开始在审核存储中填充活动日志。 此时，还可以利用[安全性和符合性中心的搜索和调查资源](https://aka.ms/sccsearch)。 
- 查看所有邮箱的委托权限和邮件转发规则。 可以使用此 [PowerShell 脚本](https://aka.ms/delegateforwardrules)来执行此任务。 

## <a name="next-steps"></a>后续步骤

* [Azure Active Directory 标识保护](../identity-protection/overview-identity-protection.md)
* [已标记为存在风险的用户](../identity-protection/overview-identity-protection.md)