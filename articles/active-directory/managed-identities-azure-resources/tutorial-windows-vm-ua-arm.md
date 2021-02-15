---
title: 教程`:`使用托管标识访问 Azure 资源管理器 - Windows - Azure AD
description: 本教程介绍了使用 Windows VM 上用户分配的托管标识访问 Azure 资源管理器的过程。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9757a322922524f181b1fa3f48850efbb7a18dd
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96546771"
---
# <a name="tutorial-use-a-user-assigned-managed-identity-on-a-windows-vm-to-access-azure-resource-manager"></a>教程：使用 Windows VM 上用户分配的托管标识访问 Azure 资源管理器

本教程介绍了如何创建用户分配的标识、将其分配给 Windows 虚拟机 (VM)，然后再使用此标识访问 Azure 资源管理器 API。 托管服务标识由 Azure 自动管理。 此标识可用于向支持 Azure AD 身份验证的服务进行身份验证，这样就无需在代码中嵌入凭据了。 

学习如何：

> [!div class="checklist"]
> * 创建用户分配的托管标识
> * 将用户分配的标识分配给 Windows VM
> * 向用户分配的标识授予对 Azure 资源管理器中资源组的访问权限 
> * 使用用户分配的标识获取访问令牌，并使用它调用 Azure 资源管理器 
> * 读取资源组属性

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>先决条件

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

- [登录到 Azure 门户](https://portal.azure.com)

- [创建 Windows 虚拟机](../../virtual-machines/windows/quick-create-portal.md)

- 若要执行本教程中必需的资源创建和角色管理步骤，你的帐户需要在相应范围（订阅或资源组）具有“所有者”权限。 如果需要有关角色分配的帮助，请参阅[使用基于角色的访问控制管理对 Azure 订阅资源的访问权限](../../role-based-access-control/role-assignments-portal.md)。

- 若要运行示例脚本，有两个选项：
    - 使用 [Azure Cloud Shell](../../cloud-shell/overview.md)，你可使用代码块右上角的“试用”按钮打开它。
    - 如下一节所述，使用 Azure PowerShell 在本地运行脚本。

### <a name="configure-azure-powershell-locally"></a>在本地配置 Azure PowerShell

若要在本文情景中本地使用 Azure PowerShell（而不是使用 Cloud Shell），请完成以下步骤：

1. 安装[最新版本的 Azure PowerShell](/powershell/azure/install-az-ps)（如果尚未安装）。

1. 登录 Azure：

    ```azurepowershell
    Connect-AzAccount
    ```

1. 安装[最新版本的 PowerShellGet](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget)。

    ```azurepowershell
    Install-Module -Name PowerShellGet -AllowPrerelease
    ```

    在下一步运行此命令后，可能需要 `Exit` 退出当前 PowerShell 会话。

1. 安装 `Az.ManagedServiceIdentity` 模块的预发布版本，以执行本文中用户分配托管标识操作：

    ```azurepowershell
    Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease
    ```

## <a name="enable"></a>启用

对于基于用户分配标识的方案，需要执行以下步骤：

- 创建标识
- 分配新建的标识

### <a name="create-identity"></a>创建标识

本部分说明如何创建用户分配的标识。 用户分配的标识是作为独立的 Azure 资源创建的。 使用 [New-AzUserAssignedIdentity](/powershell/module/az.managedserviceidentity/get-azuserassignedidentity)，Azure 可在你的 Azure AD 租户中创建可分配给一个或多个 Azure 服务实例的标识。

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1
```

该响应包含已创建的用户分配标识的详细信息，与以下示例类似。 请记录用户分配的标识的 `Id` 和 `ClientId` 值，因为在后续步骤中将使用它们：

```azurepowershell
{
Id: /subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1
ResourceGroupName : myResourceGroupVM
Name: ID1
Location: westus
TenantId: 733a8f0e-ec41-4e69-8ad8-971fc4b533f8
PrincipalId: e591178e-b785-43c8-95d2-1397559b2fb9
ClientId: af825a31-b0e0-471f-baea-96de555632f9
ClientSecretUrl: https://control-westus.identity.azure.net/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1/credentials?tid=733a8f0e-ec41-4e69-8ad8-971fc4b533f8&oid=e591178e-b785-43c8-95d2-1397559b2fb9&aid=af825a31-b0e0-471f-baea-96de555632f9
Type: Microsoft.ManagedIdentity/userAssignedIdentities
}
```

### <a name="assign-identity"></a>分配标识

本部分介绍如何将用户分配的标识分配给 Windows VM。 用户分配的标识可以由多个 Azure 资源上的客户端使用。 使用以下命令将用户分配的标识分配给单个 VM。 将上一步返回的 `Id` 属性用于 `-IdentityID` 参数。

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVM -ResourceGroupName TestRG -VM $vm -IdentityType "UserAssigned" -IdentityID "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
```

## <a name="grant-access"></a>授予访问权限 

本部分介绍如何授予用户分配的标识访问 Azure 资源管理器中的资源组的权限。 Azure 资源的托管标识提供了相关标识，你的代码可使用这些标识来请求访问令牌，以向支持 Azure AD 身份验证的资源 API 进行身份验证。 在本教程中，你的代码将访问 Azure 资源管理器 API。 

需先向标识授予对 Azure 资源管理器中资源的访问权限，代码才能访问 API。 在此情况下，即为包含 VM 的资源组。 根据环境适当地更新 `<SUBSCRIPTION ID>` 的值。

```azurepowershell-interactive
$spID = (Get-AzUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1).principalid
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/"
```

响应包含所创建的角色分配的详细信息，与以下示例类似：

```azurepowershell
RoleAssignmentId: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM/providers/Microsoft.Authorization/roleAssignments/f9cc753d-265e-4434-ae19-0c3e2ead62ac
Scope: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM
DisplayName: ID1
SignInName:
RoleDefinitionName: Reader
RoleDefinitionId: acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId: e591178e-b785-43c8-95d2-1397559b2fb9
ObjectType: ServicePrincipal
CanDelegate: False
```

## <a name="access-data"></a>访问数据

### <a name="get-an-access-token"></a>获取访问令牌 

在本教程的剩余部分，你将从先前创建的 VM 入手。

1. 通过 [https://portal.azure.com](https://portal.azure.com) 登录到 Azure 门户

2. 在门户中，导航到“虚拟机”并转到 Windows 虚拟机，然后在“概述”中单击“连接”。

3. 输入创建 Windows VM 时使用的“用户名”和“密码”。

4. 现在，已经创建了与虚拟机的远程桌面连接，请在远程会话中打开 PowerShell。

5. 使用 Powershell 的 `Invoke-WebRequest`，向 Azure 资源终结点的本地托管标识发出请求以获取 Azure 资源管理器的访问令牌。  `client_id` 值是创建用户分配的托管标识时返回的值。

    ```azurepowershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=af825a31-b0e0-471f-baea-96de555632f9&resource=https://management.azure.com/' -Method GET -Headers @{Metadata="true"}
    $content = $response.Content | ConvertFrom-Json
    $ArmToken = $content.access_token
    ```

### <a name="read-properties"></a>读取属性

使用上一个步骤中检索到的访问令牌访问 Azure 资源管理器，并读取向用户分配的标识授予了访问权限的资源组的属性。 将 `<SUBSCRIPTION ID>` 替换为你环境的订阅 ID。

```azurepowershell
(Invoke-WebRequest -Uri https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/myResourceGroupVM?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{Authorization ="Bearer $ArmToken"}).content
```
响应包含特定资源组信息，类似于下面的示例：

```json
{"id":"/subscriptions/<SUBSCRIPTIONID>/resourceGroups/myResourceGroupVM","name":"myResourceGroupVM","location":"eastus","properties":{"provisioningState":"Succeeded"}}
```

## <a name="next-steps"></a>后续步骤

在本教程中，你已学习了如何创建用户分配的标识，并将其附加到 Azure 虚拟机以访问 Azure 资源管理器 API。  若要详细了解 Azure 资源管理器，请参阅：

> [!div class="nextstepaction"]
>[Azure 资源管理器](../../azure-resource-manager/management/overview.md)
