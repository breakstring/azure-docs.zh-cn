---
title: 我的 Azure AD 登录页面是否接受 Microsoft 帐户 | Microsoft Docs
description: 在登录过程中屏幕消息传送如何反映用户名查找
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: overview
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c3b1c8d35c24d78abb8a519ddc8790649eb5f2b
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96547859"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Azure Active Directory 中 Microsoft 帐户的登录选项

Azure Active Directory (Azure AD) 的 Microsoft 365 登录页面支持工作或学校帐户以及 Microsoft 帐户，但根据用户的具体情况，它可能支持其中一种帐户，也可能两种帐户都支持。 例如，Azure AD 登录页面支持：

* 接受两种类型帐户登录的应用
* 接受来宾的组织

## <a name="identification"></a>识别
可以查看用户名字段中的提示文本，判断组织使用的登录页面是否支持 Microsoft 帐户。 如果提示文本显示“电子邮件、电话或 Skype”，则登录页支持 Microsoft 帐户。

![帐户登录页面之间的差异](./media/signin-account-support/ui-prompt.png)

[其他登录选项仅适用于个人 Microsoft 帐户](https://azure.microsoft.com/updates/microsoft-account-signin-options/ )，不能使用这些选项登录到工作或学校帐户资源。

## <a name="next-steps"></a>后续步骤

[自定义登录品牌](../fundamentals/add-custom-domain.md)