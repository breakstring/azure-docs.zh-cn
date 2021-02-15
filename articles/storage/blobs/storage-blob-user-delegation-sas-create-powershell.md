---
title: 使用 PowerShell 为容器或 blob 创建用户委托 SAS
titleSuffix: Azure Storage
description: 了解如何使用 PowerShell 创建具有 Azure Active Directory 凭据的用户委托 SAS。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 875b2a9f35562dd8f0d5df3c631e5ade1e3fbf75
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91714528"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-powershell"></a>使用 PowerShell 为容器或 blob 创建用户委托 SAS

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

本文介绍如何使用 Azure Active Directory (Azure AD) 凭据为包含 Azure PowerShell 的容器或 blob 创建用户委托 SAS。

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-powershell-module"></a>安装 PowerShell 模块

若要使用 PowerShell 创建用户委托 SAS，请安装1.10.0 模块的版本或更高版本。 按照以下步骤安装最新版本的模块：

1. 卸载以前安装的所有 Azure PowerShell：

    - 使用“设置”**** 下的“应用和功能”**** 设置从 Windows 中删除以前安装的所有 Azure PowerShell。
    - 从中删除所有 **Azure** 模块 `%Program Files%\WindowsPowerShell\Modules` 。

1. 确保已安装 PowerShellGet 最新版本。 打开 Windows PowerShell 窗口，然后运行以下命令以安装最新版本：

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. 安装 PowerShellGet 后关闭并重新打开 PowerShell 窗口。

1. 安装最新版本的 Azure PowerShell：

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. 请确保已安装 Azure PowerShell 版本3.2.0 或更高版本。 运行以下命令以安装最新版本的 Azure 存储 PowerShell 模块：

    ```powershell
    Install-Module -Name Az.Storage -Repository PSGallery -Force
    ```

1. 关闭并重新打开 PowerShell 窗口。

若要查看安装的 Az 模块的版本，请运行以下命令：

```powershell
Get-Module -ListAvailable -Name Az.Storage -Refresh
```

有关如何安装 Azure PowerShell 的详细信息，请参阅[使用 PowerShellGet 安装 Azure PowerShell](/powershell/azure/install-az-ps)。

## <a name="sign-in-to-azure-powershell-with-azure-ad"></a>通过 Azure AD 登录到 Azure PowerShell

调用 [AzAccount](/powershell/module/az.accounts/connect-azaccount) 命令以通过 Azure AD 帐户登录：

```powershell
Connect-AzAccount
```

有关如何在 PowerShell 中登录的详细信息，请参阅 [登录 Azure PowerShell](/powershell/azure/authenticate-azureps)。

## <a name="assign-permissions-with-azure-rbac"></a>向 Azure RBAC 分配权限

若要从 Azure PowerShell 创建用户委托 SAS，必须为用于登录 PowerShell 的 Azure AD 帐户分配包含 storageAccounts **//blobServices/generateUserDelegationKey** 操作的角色。 此权限允许 Azure AD 帐户请求 *用户委托密钥*。 用户委托密钥用于对用户委托 SAS 进行签名。 提供 storageAccounts/ **blobServices/generateUserDelegationKey** 操作的角色必须在存储帐户、资源组或订阅的级别上进行分配。 有关创建用户委托 SAS 的 Azure RBAC 权限的详细信息，请参阅[创建用户委托 sas](/rest/api/storageservices/create-user-delegation-sas)中的**使用 azure rbac 分配权限**部分。

如果你没有足够的权限将 Azure 角色分配到 Azure AD 安全主体，你可能需要要求帐户所有者或管理员分配必要的权限。

下面的示例分配 **存储 Blob 数据参与者** 角色，其中包括 storageAccounts **//blobServices/generateUserDelegationKey** 操作。 角色的作用域为存储帐户的级别。

请务必将尖括号中的占位符值替换为你自己的值：

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

有关内置角色的详细信息，包括 **storageAccounts//blobServices/generateUserDelegationKey** 操作，请参阅 [Azure 内置角色](../../role-based-access-control/built-in-roles.md)。

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>使用 Azure AD 凭据来保护 SAS

使用 Azure PowerShell 创建用户委托 SAS 时，将为你隐式创建用于对 SAS 进行签名的用户委托密钥。 为 SAS 指定的开始时间和到期时间也用作用户委托密钥的开始时间和到期时间。 

由于用户委托密钥的有效最大时间间隔是从开始日期起的7天，因此，你应为开始时间在7天内的 SAS 指定到期时间。 此 SA 在用户委托密钥过期后无效，因此过期时间超过7天的 SAS 仍将仅适用于7天。

若要为带有 Azure PowerShell 的容器或 blob 创建用户委托 SAS，请首先创建一个新的 Azure 存储上下文对象，并指定 `-UseConnectedAccount` 参数。 `-UseConnectedAccount`参数指定该命令在您登录时所用的 Azure AD 帐户下创建上下文对象。

请务必将尖括号中的占位符值替换为你自己的值：

```powershell
$ctx = New-AzStorageContext -StorageAccountName <storage-account> -UseConnectedAccount
```

### <a name="create-a-user-delegation-sas-for-a-container"></a>为容器创建用户委托 SAS

若要为容器返回用户委托 SAS 令牌，请调用 [AzStorageContainerSASToken](/powershell/module/az.storage/new-azstoragecontainersastoken) 命令，并传入之前创建的 Azure 存储上下文对象。

以下示例返回容器的用户委托 SAS 令牌。 请记住，用自己的值替换括号中的占位符值：

```powershell
New-AzStorageContainerSASToken -Context $ctx `
    -Name <container> `
    -Permission racwdl `
    -ExpiryTime <date-time>
```

返回的用户委托 SAS 令牌如下所示：

```output
?sv=2018-11-09&sr=c&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-05T22%3A24%3A36Z&ske=2019-08-07T07%3A
00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=rwdl
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>为 blob 创建用户委托 SAS

若要为 blob 返回用户委托 SAS 令牌，请调用 [AzStorageBlobSASToken](/powershell/module/az.storage/new-azstorageblobsastoken) 命令，并传入之前创建的 Azure 存储上下文对象。

以下语法返回 blob 的用户委托 SAS。 该示例指定 `-FullUri` 参数，该参数返回附加 SAS 令牌的 BLOB URI。 请记住，用自己的值替换括号中的占位符值：

```powershell
New-AzStorageBlobSASToken -Context $ctx `
    -Container <container> `
    -Blob <blob> `
    -Permission racwd `
    -ExpiryTime <date-time>
    -FullUri
```

返回的用户委托 SAS URI 将类似于：

```output
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?sv=2018-11-09&sr=b&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-06T21%3A16%3A54Z&ske=2019-08-07T07%3A00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=racwd
```

> [!NOTE]
> 用户委托 SAS 不支持使用存储访问策略定义权限。

## <a name="revoke-a-user-delegation-sas"></a>撤消用户委托 SAS

若要从 Azure PowerShell 撤消用户委托 SAS，请调用 **AzStorageAccountUserDelegationKeys** 命令。 此命令吊销与指定存储帐户关联的所有用户委托密钥。 与这些密钥关联的所有共享访问签名都将失效。

请务必将尖括号中的占位符值替换为你自己的值：

```powershell
Revoke-AzStorageAccountUserDelegationKeys -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
```

> [!IMPORTANT]
> 用户委托密钥和 Azure 角色分配都是由 Azure 存储缓存的，因此，在启动吊销过程和现有用户委派 SAS 变为无效之间可能存在延迟。

## <a name="next-steps"></a>后续步骤

- [创建 (REST API 的用户委托 SAS) ](/rest/api/storageservices/create-user-delegation-sas)
- [获取用户委派密钥操作](/rest/api/storageservices/get-user-delegation-key)
