---
title: 从 Azure Active Directory 的组中删除角色分配 | Microsoft Docs
description: 预览用于委托标识管理的自定义 Azure AD 角色。 在 Azure 门户、PowerShell 或图形 API 中管理 Azure 角色。
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89fa3bb94f72ab04c2ea68641b8d1dff7695aa53
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98741020"
---
# <a name="remove-role-assignments-from-a-group-in-azure-active-directory"></a>从 Azure Active Directory 的组中删除角色分配

本文介绍 IT 管理员如何删除分配给组的 Azure AD 角色。 在 Azure 门户中，现在可以删除对用户的直接和间接角色分配。 如果按组成员身份向用户分配了角色，则从组中删除该用户以删除角色分配。

## <a name="using-azure-admin-center"></a>使用 Azure 管理中心

1. 使用 Azure AD 组织中的特权角色管理员或全局管理员权限登录到 [Azure AD 管理中心](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)。

1. 选择“角色和管理员”>“角色名称”。

1. 选择要从中删除角色分配的组，然后选择“删除分配”。

   ![从所选组中删除角色分配。](./media/groups-remove-assignment/remove-assignment.png)

1. 当系统要求你确认操作时，请选择“是”。

## <a name="using-powershell"></a>使用 PowerShell

### <a name="create-a-group-that-can-be-assigned-to-role"></a>创建可分配给角色的组

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true
```

### <a name="get-the-role-definition-you-want-to-assign-the-group-to"></a>获取要向其分配组的角色定义

```powershell
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Helpdesk Administrator'"
```

### <a name="create-a-role-assignment"></a>创建角色分配

```powershell
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.objectId
```

### <a name="remove-the-role-assignment"></a>删除角色分配

```powershell
Remove-AzureAdMSRoleAssignment -Id $roleAssignment.Id 
```

## <a name="using-microsoft-graph-api"></a>使用 Microsoft Graph API

### <a name="create-a-group-that-can-be-assigned-an-azure-ad-role"></a>创建可被分配 Azure AD 角色的组

```powershell
POST https://graph.microsoft.com/beta/groups

{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD",
"displayName": "Contoso_Helpdesk_Administrators",
"groupTypes": [
"Unified"
],
"mailEnabled": true,
"securityEnabled": true
"mailNickname": "contosohelpdeskadministrators",
"isAssignableToRole": true,
}
```

### <a name="get-the-role-definition"></a>获取角色定义

```powershell
GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions?$filter = displayName eq ‘Helpdesk Administrator’
```

### <a name="create-the-role-assignment"></a>创建角色分配

```powershell
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
{
"principalId":"<Object Id of Group>",
"roleDefinitionId":"<Id of role definition>",
"directoryScopeId":"/"
}
```

### <a name="delete-role-assignment"></a>删除角色分配

```powershell
DELETE https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/<Id of role assignment>
```

## <a name="next-steps"></a>后续步骤

- [使用云组来管理角色分配](groups-concept.md)
- [分配给云组的角色疑难解答](groups-faq-troubleshooting.md)
