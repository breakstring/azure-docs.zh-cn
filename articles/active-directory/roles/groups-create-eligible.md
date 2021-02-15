---
title: 在 Azure Active Directory 中创建用于分配角色的组 |Microsoft Docs
description: 了解如何在 Azure AD 中创建可分配角色的组。 在 Azure 门户、PowerShell 或图形 API 中管理 Azure 角色。
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
ms.openlocfilehash: 3f06a1e9ea83fc7ad758ad17245ffa5d7ca973f6
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742092"
---
# <a name="create-a-role-assignable-group-in-azure-active-directory"></a>在 Azure Active Directory 中创建角色可分配的组

您只能向使用设置为 True 的 "isAssignableToRole" 属性创建的组分配角色，或者在 Azure AD 门户中创建的、 **Azure AD 可以将角色分配给组** 的角色。 此组属性可将组分配到 Azure Active Directory (Azure AD) 中的角色。 本文介绍如何创建这种特殊类型的组。 **注意：** 将 isAssignableToRole 属性设置为 true 的组不能为动态成员身份类型。 有关详细信息，请参阅 [使用组管理 Azure AD 角色分配](groups-concept.md)。

## <a name="using-azure-ad-admin-center"></a>使用 Azure AD 管理中心

1. 使用 Azure AD 组织中的特权角色管理员或全局管理员权限登录到 [Azure AD 管理中心](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)。
1. 选择 "**组**" "  >  **所有组**" "  >  **新组**"。

    [![打开 Azure Active Directory 并创建一个新组。](./media/groups-create-eligible/new-group.png "打开 Azure Active Directory 并创建一个新组。")](./media/groups-create-eligible/new-group.png#<lightbox>)

1. 在 " **新建组** " 选项卡上，提供组类型、名称和说明。
1. 打开 **Azure AD 可以将角色分配给组**。 只有特权角色管理员和全局管理员才能看到此开关，因为这两个角色只是两个可设置交换机的角色。

    [![使新组符合角色分配的条件](./media/groups-create-eligible/eligible-switch.png "使新组符合角色分配的条件")](./media/groups-create-eligible/eligible-switch.png#<lightbox>)

1. 选择组的成员和所有者。 你还可以选择将角色分配给组，但此处不需要分配角色。

    [![将成员添加到可分配角色的组并分配角色。](./media/groups-create-eligible/specify-members.png "将成员添加到可分配角色的组并分配角色。")](./media/groups-create-eligible/specify-members.png#<lightbox>)

1. 指定成员和所有者后，选择 " **创建**"。

    [!["创建" 按钮位于页面的底部。](./media/groups-create-eligible/create-button.png ""创建" 按钮位于页面的底部。")](./media/groups-create-eligible/create-button.png#<lightbox>)

组是使用分配给它的任何角色创建的。

## <a name="using-powershell"></a>使用 PowerShell

### <a name="install-the-azure-ad-preview-module"></a>安装 Azure AD 预览模块

```powershell
install-module azureadpreview
import-module azureadpreview
```

若要验证该模块是否可供使用，请发出以下命令：

```powershell
get-module azureadpreview
```

### <a name="create-a-group-that-can-be-assigned-to-role"></a>创建可分配给角色的组

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true
```

对于这种类型的组， `isPublic` 将始终为 false，并且 `isSecurityEnabled` 始终为 true。

### <a name="copy-one-groups-users-and-service-principals-into-a-role-assignable-group"></a>将一个组的用户和服务主体复制到一个可分配角色的组中

```powershell
#Basic set up
install-module azureadpreview
import-module azureadpreview
get-module azureadpreview

#Connect to Azure AD. Sign in as Privileged Role Administrator or Global Administrator. Only these two roles can create a role-assignable group.
Connect-AzureAD

#Input variabled: Existing group
$idOfExistingGroup = "14044411-d170-4cb0-99db-263ca3740a0c"

#Input variables: New role-assignable group
$groupName = "Contoso_Bellevue_Admins"
$groupDescription = "This group is assigned to Helpdesk Administrator built-in role in Azure AD."
$mailNickname = "contosobellevueadmins"

#Create new security group which is a role assignable group. For creating a Microsoft 365 group, set GroupTypes="Unified" and MailEnabled=$true
$roleAssignablegroup = New-AzureADMSGroup -DisplayName $groupName -Description $groupDescription -MailEnabled $false -MailNickname $mailNickname -SecurityEnabled $true -IsAssignableToRole $true

#Get details of existing group
$existingGroup = Get-AzureADMSGroup -Id $idOfExistingGroup
$membersOfExistingGroup = Get-AzureADGroupMember -ObjectId $existingGroup.Id

#Copy users and service principals from existing group to new group
foreach($member in $membersOfExistingGroup){
if($member.ObjectType -eq 'User' -or $member.ObjectType -eq 'ServicePrincipal'){
Add-AzureADGroupMember -ObjectId $roleAssignablegroup.Id -RefObjectId $member.ObjectId
}
}
```

## <a name="using-microsoft-graph-api"></a>使用 Microsoft Graph API

### <a name="create-a-role-assignable-group-in-azure-ad"></a>在 Azure AD 中创建角色可分配的组

```powershell
POST https://graph.microsoft.com/beta/groups
{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD.",
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

对于这种类型的组， `isPublic` 将始终为 false，并且 `isSecurityEnabled` 始终为 true。

## <a name="next-steps"></a>后续步骤

- [向云组分配角色](groups-assign-role.md)
- [使用云组来管理角色分配](groups-concept.md)
- [分配给云组的角色疑难解答](groups-faq-troubleshooting.md)
