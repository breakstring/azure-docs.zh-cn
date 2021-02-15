---
title: 在 Azure AD 的用户体验中隐藏企业应用程序
description: 如何在 Azure Active Directory 访问面板或 Microsoft 365 启动器中隐藏企业应用程序的用户体验。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 03/25/2020
ms.author: kenwith
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8469b48b92f3f9a645a0c05441e6c1943b02e16f
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258874"
---
# <a name="hide-enterprise-applications-from-end-users-in-azure-active-directory"></a>在 Azure Active Directory 中隐藏最终用户的企业应用程序

有关如何从最终用户的 MyApps 面板或 Microsoft 365 启动器中隐藏应用程序的说明。 当应用程序隐藏后，用户仍然拥有对应用程序的权限。 

## <a name="prerequisites"></a>必备条件

需要使用应用程序管理员权限才能通过 MyApps 面板和 Microsoft 365 启动器隐藏应用程序。

隐藏所有 Microsoft 365 应用程序需要全局管理员权限。


## <a name="hide-an-application-from-the-end-user"></a>向最终用户隐藏应用程序
使用以下步骤在 MyApps 面板中隐藏应用程序，并 Microsoft 365 应用程序启动器。

1.  以目录的全局管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2.  选择“Azure Active Directory” 。
3.  选择“企业应用程序”。 “企业应用程序 - 所有应用程序”边栏选项卡随即打开。
4.  在“应用程序类型”下，选择“企业应用程序”（如果尚未选择）。
5.  搜索要隐藏的应用程序，然后单击该应用程序。  此时会打开应用程序的概述。
6.  单击 **“属性”**。 
7.  对于“对用户可见?”问题，单击“否”。
8.  单击“保存”。

> [!NOTE]
> 这些说明仅适用于企业应用程序。

## <a name="use-azure-ad-powershell-to-hide-an-application"></a>使用 Azure AD PowerShell 隐藏应用程序

若要从 MyApps 面板中隐藏应用程序，可以手动将 HideApp 标记添加到应用程序的服务主体。 运行以下 [AzureAD PowerShell](/powershell/module/azuread/#service_principals) 命令，将应用程序的 **"对用户可见"** 属性设置为 " **否**"。 

```PowerShell
Connect-AzureAD

$objectId = "<objectId>"
$servicePrincipal = Get-AzureADServicePrincipal -ObjectId $objectId
$tags = $servicePrincipal.tags
$tags += "HideApp"
Set-AzureADServicePrincipal -ObjectId $objectId -Tags $tags
```

## <a name="hide-microsoft-365-applications-from-the-myapps-panel"></a>在 MyApps 面板中隐藏 Microsoft 365 应用程序

使用以下步骤可从 "MyApps" 面板中隐藏所有 Microsoft 365 应用程序。 应用程序在 Office 365 门户中仍然可见。

1.  以目录的全局管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2.  选择“Azure Active Directory” 。
3.  选择“用户”。
4.  选择 " **用户设置**"。
5.  在“企业应用程序”下，单击“管理最终用户启动和查看其应用程序的方式”。
6.  对于“用户只能在 Office 365 门户中查看 Office 365 应用”，单击“是”。
7.  单击“ **保存**”。

## <a name="next-steps"></a>后续步骤
* [查看所有组](../fundamentals/active-directory-groups-view-azure-portal.md)
* [向企业应用分配用户或组](assign-user-or-group-access-portal.md)
* [删除企业应用的用户或组分配](./assign-user-or-group-access-portal.md)
* [更改企业应用的名称或徽标](./add-application-portal-configure.md)
