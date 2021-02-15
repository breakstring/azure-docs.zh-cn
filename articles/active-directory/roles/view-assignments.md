---
title: 在 Azure Active Directory 门户中查看自定义角色分配 | Microsoft Docs
description: 现在，你可以在 Azure Active Directory 管理中心查看和管理 Azure Active Directory 管理员角色的成员。
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c13167706c2794253108b04eaa2dcc46e0e6c09a
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98740680"
---
# <a name="view-custom-role-assignments-using-azure-active-directory"></a>使用 Azure Active Directory 查看自定义角色分配

本文介绍如何在 Azure Active Directory (Azure AD) 中查看已分配的自定义角色。 在 Azure Active Directory (Azure AD) 中，角色可以在组织范围内分配，也可以在单应用程序范围内分配。

- 组织范围的角色分配会添加到单应用程序角色分配列表中，并可在其中查看它们。
- 单应用程序范围的角色分配不会添加到组织范围的分配列表中，也不能在其中查看它们。

## <a name="view-role-assignments-in-the-azure-portal"></a>在 Azure 门户中查看角色分配

此过程介绍如何查看组织范围的角色分配。

1. 使用 Azure AD 组织中的特权角色管理员或全局管理员权限登录到 [Azure AD 管理中心](https://aad.portal.azure.com)。
1. 依次选择“Azure Active Directory”、“角色和管理员”，然后选择一个角色，将其打开并查看其属性。
1. 选择“分配”，查看角色的分配。

    ![从列表中打开一个角色时，查看角色分配和权限](./media/view-assignments/role-assignments.png)

## <a name="view-role-assignments-using-azure-ad-powershell"></a>使用 Azure AD PowerShell 查看角色分配

此部分介绍如何查看组织范围的角色分配。 本文使用 [Azure Active Directory PowerShell 版本 2](/powershell/module/azuread/#directory_roles) 模块。 若要使用 PowerShell 查看单应用程序范围的分配，可以使用[通过 PowerShell 分配自定义角色](custom-assign-powershell.md)中的 cmdlet。

### <a name="prepare-powershell"></a>准备 PowerShell

首先，必须[下载 Azure AD 预览版 PowerShell 模块](https://www.powershellgallery.com/packages/AzureAD/)。

若要安装 Azure AD PowerShell 模块，请使用以下命令：

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

若要验证模块是否可供使用，请运行下面的命令：

``` PowerShell
get-module azuread
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

### <a name="view-the-assignments-of-a-role"></a>查看角色的分配

示例：查看角色分配。

``` PowerShell
# Fetch list of all directory roles with object ID
Get-AzureADDirectoryRole

# Fetch a specific directory role by ID
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"

# Fetch role membership for a role
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
```

## <a name="view-role-assignments-using-microsoft-graph-api"></a>使用 Microsoft Graph API 查看角色分配

此部分介绍如何查看组织范围的角色分配。  若要使用 Graph API 查看单应用程序范围的分配，可以使用[通过 Graph API 分配自定义角色](custom-assign-graph.md)中的操作。

用于为给定角色定义获取角色分配的 HTTP 请求。

GET

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments&$filter=roleDefinitionId eq ‘<object-id-or-template-id-of-role-definition>’
```

响应

``` HTTP
HTTP/1.1 200 OK
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":["/"]
}
```

## <a name="view-assignments-of-single-application-scope"></a>查看单应用程序范围的分配

此部分介绍如何查看单应用程序范围的角色分配。 此功能目前处于公开预览状态。

1. 使用 Azure AD 组织中的特权角色管理员或全局管理员权限登录到 [Azure AD 管理中心](https://aad.portal.azure.com)。
1. 选择“应用注册”，然后选择要查看其属性的应用注册。 可能必须选择“所有应用程序”，以便在 Azure AD 组织中查看应用注册的完整列表。

    ![在“应用注册”页中创建或编辑应用注册](./media/view-assignments/app-reg-all-apps.png)

1. 在应用注册中，选择“角色和管理员”，然后选择一个角色，以查看其属性。

    ![在“应用注册”页中查看应用注册角色分配](./media/view-assignments/app-reg-assignments.png)

1. 选择“分配”，查看角色的分配。 在应用注册中打开分配视图会显示局限于此 Azure AD 资源的分配。

    ![在应用注册的属性中查看应用注册角色分配](./media/view-assignments/app-reg-assignments-2.png)

## <a name="next-steps"></a>后续步骤

* 欢迎在 [Azure AD 管理角色论坛](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)上与我们分享知识。
* 有关角色以及管理员角色分配的详细信息，请参阅[分配管理员角色](permissions-reference.md)。
* 有关默认用户权限，请参阅[默认来宾和成员用户权限的比较](../fundamentals/users-default-permissions.md)。
