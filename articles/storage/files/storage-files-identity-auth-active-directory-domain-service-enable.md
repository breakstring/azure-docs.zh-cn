---
title: 使用 Azure AD 域服务通过 SMB 授权访问文件数据
description: 了解如何通过 Azure Active Directory 域服务，通过服务器消息块对 Azure 文件 (SMB) 启用基于身份的身份验证。 已加入域的 Windows 虚拟机 (Vm) 然后可以使用 Azure AD 凭据访问 Azure 文件共享。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 01/03/2021
ms.author: rogarana
ms.subservice: files
ms.custom: contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: 153ffd699b22a6379758bf66b896a2b37a19fdf3
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97883276"
---
# <a name="enable-azure-active-directory-domain-services-authentication-on-azure-files"></a>启用 Azure 文件上 Azure Active Directory 域服务身份验证

[Azure 文件](storage-files-introduction.md)  通过以下两种类型的域服务，支持通过服务器消息块进行基于身份的身份验证 (SMB) ：本地 Active Directory 域服务 (AD DS) Azure Active Directory (Azure AD) 和域服务。强烈建议您查看 "[工作原理" 部分](./storage-files-active-directory-overview.md#how-it-works)，选择适当的域服务进行身份验证。 安装程序不同，具体取决于所选的域服务。 本文重点介绍如何启用和配置 Azure AD DS，以便通过 Azure 文件共享进行身份验证。

如果你不熟悉 Azure 文件共享，我们建议在阅读以下文章之前阅读我们的 [规划指南](storage-files-planning.md) 。

> [!NOTE]
> Azure 文件仅支持具有仅限 RC4-HMAC Azure AD DS 的 Kerberos 身份验证。 目前尚不支持 AES Kerberos 加密。
> Azure 文件支持对 Azure AD DS 进行身份验证，与 Azure AD 完全同步。 如果已在 Azure AD DS 中启用范围内同步，仅同步 Azure AD 的有限标识，则不支持身份验证和授权。

## <a name="prerequisites"></a>先决条件

在 Azure 文件共享的 SMB 上启用 Azure AD 之前，请确保已完成以下先决条件：

1.  **选择或创建 Azure AD 租户。**

    可以使用新的或现有的租户进行通过 SMB 的 Azure AD 身份验证。 要访问的租户和文件共享必须与同一订阅相关联。

    若要创建一个新的 Azure AD 租户，可以[添加 Azure AD 租户和 Azure AD 订阅](/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription)。 如果现有 Azure AD 租户，但想要创建新的租户用于 Azure 文件共享，请参阅 [创建 Azure Active Directory 租户](/rest/api/datacatalog/create-an-azure-active-directory-tenant)。

1.  **启用 Azure AD 租户上的 Azure AD 域服务。**

    若要支持使用 Azure AD 凭据进行身份验证，必须启用 Azure AD 租户的 Azure AD 域服务。 如果你不是 Azure AD 租户的管理员，请与管理员联系并按照分步指南操作，以[使用 Azure 门户启用 Azure Active Directory 域服务](../../active-directory-domain-services/tutorial-create-instance.md)。

    完成 Azure AD DS 部署通常需要大约15分钟。 在继续下一步之前，请验证 Azure AD DS **的运行状况状态显示为**"已启用密码哈希同步"。

1.  **使用 Azure AD DS 将 Azure VM 加入域。**

    若要通过使用 Azure AD 虚拟机中的凭据来访问文件共享，你的 VM 必须已加入域 Azure AD DS。 有关如何将 VM 加入域的详细信息，请参阅[将 Windows Server 虚拟机加入托管域](../../active-directory-domain-services/join-windows-vm.md)。

    > [!NOTE]
    > 仅支持在 Windows 7 或 Windows Server 2008 R2 以上的操作系统版本上运行的 Azure Vm 在 Azure 文件共享上 Azure AD DS 身份验证。

1.  **选择或创建 Azure 文件共享。**

    选择与 Azure AD 租户相同订阅关联的新的或现有文件共享。 有关创建新的文件共享的信息，请参阅[在 Azure 文件中创建文件共享](storage-how-to-create-file-share.md)。
    为了获得最佳性能，建议将文件共享与计划从中访问共享的 VM 位于同一区域。

1.  **通过使用存储帐户密钥装载 Azure 文件共享来验证 Azure 文件连接。**

    要验证是否已正确配置 VM 和文件共享，请尝试使用存储帐户密钥装载文件共享。 有关详细信息，请参阅[在 Windows 中装载 Azure 文件共享并对其进行访问](storage-how-to-use-files-windows.md)。

## <a name="regional-availability"></a>区域可用性

使用 Azure AD DS 的 azure 文件身份验证在 [所有 Azure 公共、Gov 和中国地区](https://azure.microsoft.com/global-infrastructure/locations/)都可用。

## <a name="overview-of-the-workflow"></a>工作流概述

在 Azure 文件共享的 SMB 上启用 Azure AD DS 身份验证之前，请验证是否正确配置了 Azure AD 和 Azure 存储环境。 建议你逐步完成 [先决条件](#prerequisites) ，确保你已完成所有必需的步骤。

接下来，执行以下操作以使用 Azure AD 凭据授予对 Azure 文件资源的访问权限：

1. 启用针对存储帐户的 SMB Azure AD DS 身份验证，以便向关联的 Azure AD DS 部署注册存储帐户。
2. 将共享的访问权限分配给 Azure AD 标识（用户、组或服务主体）。
3. 通过 SMB 为目录和文件配置 NTFS 权限。
4. 从加入域的 VM 装载 Azure 文件共享。

下图说明了用于实现 Azure 文件的通过 SMB 进行 Azure AD DS 身份验证的端到端工作流。

![显示通过 SMB 为 Azure 文件启用 Azure AD 的工作流的图表](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>为帐户启用 Azure AD DS 身份验证

若要启用对 Azure 文件的 SMB Azure AD DS 身份验证，可以通过使用 Azure 门户、Azure PowerShell 或 Azure CLI 来设置存储帐户的属性。 将此属性隐式设置为具有关联 Azure AD DS 部署的存储帐户。 然后，将对存储帐户中的所有新的和现有文件共享启用通过 SMB 进行的 Azure AD DS 身份验证。

请记住，仅当已成功将 Azure AD DS 部署到 Azure AD 租户之后，才能通过 SMB 启用 Azure AD DS 身份验证。 有关详细信息，请参阅 [先决条件](#prerequisites)。

# <a name="portal"></a>[Portal](#tab/azure-portal)

若要通过 [Azure 门户](https://portal.azure.com)启用 SMB Azure AD DS 身份验证，请执行以下步骤：

1. 在 Azure 门户中，请前往现有的存储帐户，或者 [创建一个存储帐户](../common/storage-account-create.md)。
1. 在“设置”部分选择“配置”。
1. 在 " **文件共享基于标识的访问** " 下，将 **Azure Active Directory 域服务)  (** 的切换切换到 " **已启用**"。
1. 选择“保存”。 

下图显示了如何为存储帐户启用通过 SMB 进行的 Azure AD DS 身份验证。

![启用 Azure 门户中的 SMB Azure AD DS 身份验证](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要启用 Azure PowerShell 的 SMB Azure AD DS 身份验证，请安装最新 Az 模块 (2.4 或更高版本) 或 (1.5 或更高版本的) 。 有关安装 PowerShell 的详细信息，请参阅 [在 Windows 上通过 PowerShellGet 安装 Azure PowerShell](/powershell/azure/install-Az-ps)。

若要创建新的存储帐户，请调用 [AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount?view=azps-2.5.0)，然后将 **EnableAzureActiveDirectoryDomainServicesForFile** 参数设置为 **true**。 在下面的示例中，请记住将占位符值替换为自己的值。  (如果你使用的是以前的预览模块，则启用该功能的参数为 **EnableAzureFilesAadIntegrationForSMB**。 ) 

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```

若要在现有存储帐户上启用此功能，请使用以下命令：

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要启用 Azure CLI 通过 SMB 进行 Azure AD 身份验证，请安装最新的 CLI 版本 (版本2.0.70 或更高版本) 。 有关安装 Azure CLI 的详细信息，请参阅 [安装 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。

若要创建新的存储帐户，请调用 [az storage account create](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)，并将 `--enable-files-aadds` 属性设置为 **true**。 在下面的示例中，请记住将占位符值替换为自己的值。  (如果你使用的是以前的预览模块，则功能启用参数为 **文件-aad**。 ) 

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

若要在现有存储帐户上启用此功能，请使用以下命令：

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```
---

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

你现在已成功启用通过 SMB Azure AD DS 身份验证，并分配了自定义角色，该角色提供对具有 Azure AD 标识的 Azure 文件共享的访问权限。 若要向其他用户授予对文件共享的访问权限，请按照 " [分配访问权限](#assign-access-permissions-to-an-identity) " 中的说明，使用一个标识，并在 [SMB 部分配置 NTFS 权限](#configure-ntfs-permissions-over-smb)。

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure 文件以及如何通过 SMB 使用 Azure AD，请参阅以下资源：

- [支持 SMB 访问的 Azure 文件存储基于标识的身份验证概述](storage-files-active-directory-overview.md)
- [常见问题解答](storage-files-faq.md)
