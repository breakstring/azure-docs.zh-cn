---
title: 自助注册经电子邮件验证的用户 - Azure AD | Microsoft Docs
description: 在 Azure Active Directory (Azure AD) 组织中使用自助注册
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: overview
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95625886ed11256a40e5993540d7e545134d6dd6
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2020
ms.locfileid: "96860858"
---
# <a name="what-is-self-service-sign-up-for-azure-active-directory"></a>什么是 Azure Active Directory 的自助注册？

本文介绍如何使用自助注册在 Azure Active Directory (Azure AD) 中填充组织。 若要从非托管 Azure AD 组织处接管域名，请参阅[以管理员身份接管非托管目录](domains-admin-takeover.md)。

## <a name="why-use-self-service-sign-up"></a>为何使用自助注册？

* 让客户更快获得所需的服务
* 为服务创建基于电子邮件的促销
* 创建基于电子邮件的注册流程，让用户使用易记的工作电子邮件别名快速创建标识
* 通过自助服务创建的 Azure AD 目录可转变为托管目录，以供其他服务使用

## <a name="terms-and-definitions"></a>术语和定义

* **自助注册**：这是用户注册云服务并让系统根据其电子邮件域在 Azure AD 中自动为其创建标识的方法。
* **非托管 Azure AD 目录**：在其中创建标识的目录。 非托管目录是没有全局管理员的目录。
* **电子邮件验证的用户**：Azure AD 中的一种用户帐户类型。 在注册自助服务产品后自动创建标识的用户称为电子邮件验证的用户。 电子邮件验证的用户是目录的常规成员，带有 creationmethod=EmailVerified 标记。

## <a name="how-do-i-control-self-service-settings"></a>如何控制自助服务设置？

目前，管理员有两种自助服务控制方式。 他们可以控制：

* 用户是否可以通过电子邮件加入目录
* 用户是否可以对自身授权以获取应用程序和服务

### <a name="how-can-i-control-these-capabilities"></a>如何控制这些功能？

管理员可以使用以下 Azure AD cmdlet Set-MsolCompanySettings 参数配置这些功能：

* **AllowEmailVerifiedUsers** 控制用户是否可以创建或加入目录。 如果将该参数设置为 $false，则电子邮件验证的用户不可以加入目录。
* **AllowAdHocSubscriptions** 控制用户执行自助注册的能力。 如果将该参数设置为 $false，则用户不可以执行自助注册。
  
AllowEmailVerifiedUsers 和 AllowAdHocSubscriptions 是可应用于托管或非托管目录的目录范围的设置。 此处有一个示例，其中：

* 你管理具有已验证域（例如 contoso.com）的目录
* 你使用另一个目录中的 B2B 协作邀请 contoso.com 主目录中尚不存在的用户 (userdoesnotexist@contoso.com)
* 主目录已开启 AllowEmailVerifiedUsers

如果满足上述条件，那么将在主目录中创建成员用户，并在邀请目录中创建 B2B 来宾用户。

有关 Flow 和 PowerApps 试用版注册的详细信息，请参阅以下文章：

* [如何禁止现有用户开始使用 Power BI？](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
* [组织中 Flow 问答](/flow/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>这些控制方式如何配合工作？
可以结合使用这两个参数，以更准确地定义如何控制自助注册。 例如，以下命令允许用户执行自助注册，但前提是这些用户已在 Azure AD 中拥有一个帐户（换言之，需要首先创建电子邮件验证帐户的用户无法执行自助注册）：

```powershell
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
```

以下流程图解释了这些参数的不同组合，以及目录和自助注册的最终状态。

![自助注册控件的流程图](./media/directory-self-service-signup/SelfServiceSignUpControls.png)

可以通过以下 powershell cmdlet Get-MsolCompanyInformation 检索此设置的详细信息。 有关详细信息，请参阅 [Get-MsolCompanyInformation](/powershell/module/msonline/get-msolcompanyinformation)。

```powershell
    Get-MsolCompanyInformation | Select AllowEmailVerifiedUsers, AllowAdHocSubscriptions
```

有关示例和如何使用这些参数的详细信息，请参阅 [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings)。

## <a name="next-steps"></a>后续步骤

* [向 Azure AD 添加自定义域名](../fundamentals/add-custom-domain.md)
* [如何安装和配置 Azure PowerShell](/powershell/azure/)
* [Azure PowerShell](/powershell/azure/)
* [Azure Cmdlet 参考](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings)
* [关闭非托管目录中的工作或学校帐户](users-close-account.md)
