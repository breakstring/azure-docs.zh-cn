---
title: 在管理单元中添加、删除和列出用户 - Azure Active Directory | Microsoft Docs
description: 在 Azure Active Directory 的管理单元中管理用户及其角色权限
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3dc7b37c96d2d82ae42d9bce32a97beab2d91e9
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98740510"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>在 Azure Active Directory 的管理单元中添加和管理用户

在 Azure Active Directory (Azure AD) 中，你可以向管理单元添加用户，以便更精细地控制管理范围。

若要准备将 PowerShell 和 Microsoft Graph 用于管理单元的管理，请参阅[入门](admin-units-manage.md#get-started)。

## <a name="add-users-to-an-administrative-unit"></a>向管理单元添加用户

### <a name="use-the-azure-portal"></a>使用 Azure 门户

你可以逐个地或以批量操作的方式将用户分配到管理单元。

- 从用户配置文件分配单个用户：

   1. 使用特权角色管理员权限登录到 [Azure AD 管理中心](https://portal.azure.com)。

   1. 选择“用户”，然后打开用户配置文件，选择要分配给管理单元的用户。
   
   1. 选择“管理单元”。 
   
   1. 若要将用户分配给一个或多个管理单元，请选择“分配给管理单元”，然后在右窗格中选择要为其分配用户的管理单元。

       ![“管理单元”窗格的屏幕截图，用于将用户分配给管理单元。](./media/admin-units-add-manage-users/assign-users-individually.png)

- 从管理单元分配单个用户：

   1. 使用特权角色管理员权限登录到 [Azure AD 管理中心](https://portal.azure.com)。
   1. 选择“管理单元”，然后选择要为其分配用户的管理单元。
   1. 选择“所有用户”，然后选择“添加成员”，然后在“添加成员”窗格上，选择要分配给管理单元的一个或多个用户  。

        ![管理单元“用户”窗格的屏幕截图，用于将用户分配给管理单元。](./media/admin-units-add-manage-users/assign-to-admin-unit.png)

- 批量分配用户：

   1. 使用特权角色管理员权限登录到 [Azure AD 管理中心](https://portal.azure.com)。

   1. 选择“管理单元”。

   1. 选择要向其中添加用户的管理单元。

   1. 选择“用户” > “批量活动” > “批量添加成员”  。 然后可以下载逗号分隔值 (CSV) 模板并编辑该文件。 格式很简单，需要在每一行中添加一个用户主体名称。 在该文件准备就绪后，请将其保存在适当的位置，然后作为本步骤的一部分上传。

      ![“用户”窗格的屏幕截图，用于将用户批量分配给管理单元。](./media/admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="use-powershell"></a>使用 PowerShell

在 PowerShell 中，使用以下示例中的 `Add-AzureADAdministrativeUnitMember` cmdlet 将用户添加到管理单元。 要向其中添加用户的管理单元的对象 ID 和要添加的用户的对象 ID 用作参数。 根据特定环境的需要更改突出显示的部分。

```powershell
$administrativeunitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$UserObj = Get-AzureADUser -Filter "UserPrincipalName eq 'billjohn@fabidentity.onmicrosoft.com'"
Add-AzureADMSAdministrativeUnitMember -Id $administrativeunitObj.ObjectId -RefObjectId $UserObj.ObjectId
```


### <a name="use-microsoft-graph"></a>使用 Microsoft Graph

用测试信息替换占位符并运行以下命令：

```http
Http request
POST /administrativeUnits/{Admin Unit id}/members/$ref
Request body
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/{id}"
}
```

例如：

```http
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/johndoe@fabidentity.com"
}
```

## <a name="view-a-list-of-administrative-units-for-a-user"></a>查看用户的管理单元列表

### <a name="use-the-azure-portal"></a>使用 Azure 门户

在 Azure 门户中可以通过以下方式打开用户配置文件：

1. 转到“Azure AD”并选择“用户” 。

1. 选择要查看其配置文件的用户。

1. 选择“管理单元”，显示已向其分配用户的管理单元列表。

   ![已向其分配用户的管理单元的屏幕截图。](./media/admin-units-add-manage-users/list-user-admin-units.png)

### <a name="use-powershell"></a>使用 PowerShell

运行以下命令：

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -Id $_.ObjectId | where {$_.RefObjectId -eq $userObjId} }
```
> [!NOTE]
> 默认情况下，`Get-AzureADAdministrativeUnitMember` 只返回管理单元的 100 个成员。 若要检索更多成员，可以添加 `"-All $true"`。

### <a name="use-microsoft-graph"></a>使用 Microsoft Graph

用测试信息替换占位符并运行以下命令：

```http
https://graph.microsoft.com/v1.0/users/{id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-single-user-from-an-administrative-unit"></a>从管理单元中删除单个用户

### <a name="use-the-azure-portal"></a>使用 Azure 门户

可以通过以下两种方式之一从管理单元中删除用户： 

* 在 Azure 门户中，转到“Azure AD”，然后选择“用户” 。 
  1. 选择该用户以打开该用户的配置文件。 
  1. 选择要从中删除用户的管理单元，然后选择“从管理单元中删除”。

     ![显示如何从用户配置文件窗格中删除管理单元中的用户的屏幕截图。](./media/admin-units-add-manage-users/user-remove-admin-units.png)

* 在 Azure 门户中，转到“Azure AD”，然后选择“管理单元” 。
  1. 选择要从中删除用户的管理单元。 
  1. 选择用户，然后选择“删除成员”。
  
     ![显示如何在管理单元级别删除用户的屏幕截图。](./media/admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="use-powershell"></a>使用 PowerShell

运行以下命令：

```powershell
Remove-AzureADMSAdministrativeUnitMember -Id $auId -MemberId $memberUserObjId
```

### <a name="use-microsoft-graph"></a>使用 Microsoft Graph

用测试信息替换占位符并运行以下命令：

`https://graph.microsoft.com/v1.0/directory/administrativeUnits/{adminunit-id}/members/{user-id}/$ref`

## <a name="remove-multiple-users-as-a-bulk-operation"></a>批量删除多个用户

若要从管理单元中删除多个用户，请执行以下操作：

1. 在 Azure 门户中，转到“Azure AD”。

1. 选择“管理单元”，然后选择要从中删除用户的管理单元。 

1. 选择“批量删除成员”，然后下载将用于列出要删除的用户的 CSV 模板。

   ![显示“用户”窗格上的“批量删除成员”链接的屏幕截图。](./media/admin-units-add-manage-users/bulk-user-remove.png)

1. 编辑带有相关用户条目的已下载 CSV 模板。 请勿删除模板的前两行。 在每行中添加一个用户主体名称 (UPN)。

   ![用于从管理单元批量删除用户的已编辑 CSV 文件的屏幕截图。](./media/admin-units-add-manage-users/bulk-user-entries.png)

1. 保存更改、上传文件、然后选择“提交”。

## <a name="next-steps"></a>后续步骤

- [向管理单元分配角色](admin-units-assign-roles.md)
- [向管理单元添加组](admin-units-add-manage-groups.md)
