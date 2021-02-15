---
title: 用于文件和 ACL 的 Azure Data Lake Storage Gen2 PowerShell
description: 使用 PowerShell cmdlet 在启用了分层命名空间 (HNS) 的存储帐户中管理目录和文件以及目录访问控制列表 (ACL)。
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 01/06/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fb715840ec3b3b1d5e65f17d4c18eb719e6acf80
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98043568"
---
# <a name="use-powershell-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>使用 PowerShell 管理 Azure Data Lake Storage Gen2 中的目录、文件和 ACL

本文介绍如何使用 PowerShell 在启用了分层命名空间 (HNS) 的存储帐户中创建和管理目录、文件与权限。 

[参考](/powershell/module/Az.Storage/)  | [Gen1 到 Gen2 的映射](#gen1-gen2-map)  | [提供反馈](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>先决条件

> [!div class="checklist"]
> * Azure 订阅。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
> * 一个已启用分层命名空间 (HNS) 的存储帐户。 按[这些](../common/storage-account-create.md)说明创建一个。
> * 已安装 .NET Framework 4.7.2 或更高版本。 请参阅[下载 .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework)。
> * PowerShell `5.1` 或更高版本。

## <a name="install-the-powershell-module"></a>安装 PowerShell 模块

1. 使用以下命令验证安装的 PowerShell 版本是否为 `5.1` 或以上。    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   若要升级 PowerShell 版本，请参阅[升级现有的 Windows PowerShell](/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell)
    
2. 安装 Az.Storage 模块。

   ```powershell
   Install-Module Az.Storage -Repository PSGallery -Force  
   ```

   有关如何安装 PowerShell 模块的详细信息，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)

## <a name="connect-to-the-account"></a>连接到帐户

选择希望命令如何获取对存储帐户的授权。 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>选项 1：使用 Azure Active Directory (AD) 获取授权

如果使用此方法，系统可确保用户帐户具有适当的 Azure 基于角色的访问控制 (Azure RBAC) 分配和 ACL 权限。

1. 打开 Windows PowerShell 命令窗口，使用 `Connect-AzAccount` 命令登录到 Azure 订阅，然后按照屏幕上的指示进行操作。

   ```powershell
   Connect-AzAccount
   ```

2. 如果你的标识已关联到多个订阅，请将活动订阅设置为要在其中创建和管理目录的存储帐户的订阅。 在此示例中，请将 `<subscription-id>` 占位符值替换为你的订阅 ID。

   ```powershell
   Select-AzSubscription -SubscriptionId <subscription-id>
   ``` 

3. 获取存储帐户上下文。

   ```powershell
   $ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
   ```

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>选项 2：使用存储帐户密钥获取授权

如果使用此方法，系统不会检查 Azure RBAC 或 ACL 权限。 使用帐户密钥获取存储帐户上下文。

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -StorageAccountKey '<storage-account-key>'
```

## <a name="create-a-container"></a>创建容器

容器充当文件的文件系统。 可以使用 `New-AzStorageContainer` cmdlet 创建一个文件系统。 

此示例创建一个名为 `my-file-system` 的容器。

```powershell
$filesystemName = "my-file-system"
New-AzStorageContainer -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>创建目录

使用 `New-AzDataLakeGen2Item` cmdlet 创建目录引用。 

此示例将名为 `my-directory` 的目录添加到容器中。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory
```

此示例添加相同的目录，但同时还会设置权限、umask、属性值和元数据值。 

```powershell
$dir = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory -Permission rwxrwxrwx -Umask ---rwx---  -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
```

## <a name="show-directory-properties"></a>显示目录属性

此示例使用 `Get-AzDataLakeGen2Item` cmdlet 获取目录，然后将属性值输出到控制台。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir =  Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
$dir.Permissions
$dir.Group
$dir.Owner
$dir.Properties
$dir.Properties.Metadata
```
> [!NOTE]
> 若要获取容器的根目录，请省略 `-Path` 参数。

## <a name="rename-or-move-a-directory"></a>重命名或移动目录

使用 `Move-AzDataLakeGen2Item` cmdlet 重命名或移动目录。

此示例将目录的名称 `my-directory` 重命名为 `my-new-directory`。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

> [!NOTE]
> 如果要直接覆盖而不触发系统提示，请使用 `-Force` 参数。

此示例将名为 `my-directory` 的目录移到名为 `my-subdirectory` 的 `my-directory-2` 子目录。 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname1 -DestFileSystem $filesystemName -DestPath $dirname2
```

## <a name="delete-a-directory"></a>删除目录

使用 `Remove-AzDataLakeGen2Item` cmdlet 删除目录。

此示例删除名为 `my-directory` 的目录。 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname 
```

可以使用 `-Force` 参数删除文件，而不会看到提示。

## <a name="download-from-a-directory"></a>从目录下载

使用 `Get-AzDataLakeGen2ItemContent` cmdlet 从目录中下载文件。

此示例从名为 `my-directory` 的目录中下载名为 `upload.txt` 的文件。 

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>列出目录内容

使用 `Get-AzDataLakeGen2ChildItem` cmdlet 列出目录的内容。 可以使用可选参数 `-OutputUserPrincipalName` 来获取用户的名称（而不是对象 ID）。

此示例列出名为 `my-directory` 的目录的内容。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -OutputUserPrincipalName
```

下面的示例列出目录中每个项的 `ACL`、`Permissions`、`Group` 和 `Owner` 属性。 获取 `ACL` 属性的值需要 `-FetchProperty` 参数。 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$properties = Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchProperty
$properties.ACL
$properties.Permissions
$properties.Group
$properties.Owner
```

> [!NOTE]
> 若要列出容器的根目录的内容，请省略 `-Path` 参数。

## <a name="upload-a-file-to-a-directory"></a>将文件上传到目录

使用 `New-AzDataLakeGen2Item` cmdlet 将文件上传到目录。

此示例将名为 `upload.txt` 的文件上传到名为 `my-directory` 的目录。 

```powershell
$localSrcFile =  "upload.txt"
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$destPath = $dirname + (Get-Item $localSrcFile).Name
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Force 
```

此示例上传同一文件，但随后会设置目标文件的权限、umask、属性值和元数据值。 此示例还会将这些值输出到控制台。

```powershell
$file = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Permission rwxrwxrwx -Umask ---rwx--- -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
$file1
$file1.Properties
$file1.Properties.Metadata

```

> [!NOTE]
> 若要将文件上传到容器的根目录，请省略 `-Path` 参数。

## <a name="show-file-properties"></a>显示文件属性

此示例使用 `Get-AzDataLakeGen2Item` cmdlet 获取文件，然后将属性值输出到控制台。

```powershell
$filepath =  "my-directory/upload.txt"
$filesystemName = "my-file-system"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filepath
$file
$file.ACL
$file.Permissions
$file.Group
$file.Owner
$file.Properties
$file.Properties.Metadata
```

## <a name="delete-a-file"></a>删除文件

使用 `Remove-AzDataLakeGen2Item` cmdlet 删除文件。

此示例删除名为 `upload.txt` 的文件。 

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath 
```

可以使用 `-Force` 参数删除文件，而不会看到提示。

## <a name="manage-access-control-lists-acls"></a>管理访问控制列表 (ACL)

可以获取、设置和更新目录与文件的访问权限。

> [!NOTE]
> 如果使用 Azure Active Directory (Azure AD) 来授权命令，请确保已为安全主体分配了[存储 Blob 数据所有者角色](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)。 若要详细了解如何应用 ACL 权限以及更改它们所带来的影响，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](./data-lake-storage-access-control.md)。

### <a name="get-an-acl"></a>获取 ACL

使用 `Get-AzDataLakeGen2Item` cmdlet 获取目录或文件的 ACL。

此示例获取某个容器根目录的 ACL，然后将该 ACL 输出到控制台。

```powershell
$filesystemName = "my-file-system"
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

此示例获取某个 **目录** 的 ACL，然后将 ACL 输出到控制台。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

此示例获取某个 **文件** 的 ACL，然后将 ACL 输出到控制台。

```powershell
$filePath = "my-directory/upload.txt"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

下图显示了获取目录 ACL 后的输出。

![获取目录的 ACL 输出](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

在本示例中，负责人用户具有读取、写入和执行权限。 负责人组仅具有读取和执行权限。 有关访问控制列表的详细信息，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](data-lake-storage-access-control.md)。

### <a name="set-an-acl"></a>设置 ACL

使用 `set-AzDataLakeGen2ItemAclObject` cmdlet 为所有者用户、所有者组或其他用户创建 ACL。 然后使用 `Update-AzDataLakeGen2Item` cmdlet 提交 ACL。

此示例针对所有者用户、所有者组或其他用户的容器根目录设置 ACL，然后将 ACL 输出到控制台。

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Acl $acl
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

此示例针对所有者用户、所有者组或其他用户的 **目录** 设置 ACL，然后将 ACL 输出到控制台。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

> [!NOTE]
> 如果要设置 **默认** ACL 条目，请在运行 **Set-AzDataLakeGen2ItemAclObject** 命令时使用 **-DefaultScope** 参数。 例如：`$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`。

此示例针对所有者用户、所有者组或其他用户的 **文件** 设置 ACL，然后将 ACL 输出到控制台。

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath -Acl $acl
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```
> [!NOTE]
> 如果要设置 **默认** ACL 条目，请在运行 **Set-AzDataLakeGen2ItemAclObject** 命令时使用 **-DefaultScope** 参数。 例如：`$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`。

下图显示了设置文件 ACL 后的输出。

![获取文件的 ACL 输出](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

在本示例中，负责人用户和负责人组只有读取和写入权限。 所有其他用户都具有写入和执行权限。 有关访问控制列表的详细信息，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](data-lake-storage-access-control.md)。

### <a name="add-or-update-an-acl-entry"></a>添加或更新 ACL 条目

首先，获取 ACL。 然后，使用 `set-AzDataLakeGen2ItemAclObject` cmdlet 添加或更新 ACL 条目。 使用 `Update-AzDataLakeGen2Item` cmdlet 提交 ACL。

此示例创建或更新用户目录的 ACL。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname).ACL
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityID xxxxxxxx-xxxx-xxxxxxxxxxx -Permission r-x -InputObject $acl 
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
```

> [!NOTE]
> 如果要更新 **默认** ACL 条目，请在运行 **Set-AzDataLakeGen2ItemAclObject** 命令时使用 **-DefaultScope** 参数。 例如：`$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityID xxxxxxxx-xxxx-xxxxxxxxxxx -Permission r-x -DefaultScope`。

### <a name="remove-an-acl-entry"></a>删除 ACL 条目

此示例从现有 ACL 中删除条目。

```powershell
$id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

# Create the new ACL object.
[Collections.Generic.List[System.Object]]$aclnew =$acl

foreach ($a in $aclnew)
{
    if ($a.AccessControlType -eq "User"-and $a.DefaultScope -eq $false -and $a.EntityId -eq $id)
    {
        $aclnew.Remove($a);
        break;
    }
}
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $aclnew
```

### <a name="set-an-acl-recursively"></a>以递归方式设置 ACL

你可以为父目录的现有子项以递归方式添加、更新和删除 ACL，而不必为每个子项单独进行这些更改。 有关详细信息，请参阅[以递归方式为 Azure Data Lake Storage Gen2 设置访问控制列表 (ACL)](recursive-access-control-lists.md)。

<a id="gen1-gen2-map"></a>

## <a name="gen1-to-gen2-mapping"></a>Gen1 到 Gen2 的映射

下表显示了用于 Data Lake Storage Gen1 映射到 Data Lake Storage Gen2 的 cmdlet 的 cmdlet。

|Data Lake Storage Gen1 cmdlet| Data Lake Storage Gen2 cmdlet| 说明 |
|--------|---------|-----|
|Get-AzDataLakeStoreChildItem|Get-AzDataLakeGen2ChildItem|默认情况下，Get-AzDataLakeGen2ChildItem cmdlet 仅列出第一级子项。 -递归参数以递归方式列出子项目。 |
|Get-AzDataLakeStoreItem<br>Get-AzDataLakeStoreItemAclEntry<br>Get-AzDataLakeStoreItemOwner<br>Get-AzDataLakeStoreItemPermission|Get-AzDataLakeGen2Item|Get-AzDataLakeGen2Item cmdlet 的输出项具有以下属性： Acl、所有者、组和权限。|
|Get-AzDataLakeStoreItemContent|Get-AzDataLakeGen2FileContent|Get-AzDataLakeGen2FileContent cmdlet 将文件内容下载到本地文件。|
|Move-AzDataLakeStoreItem|Move-AzDataLakeGen2Item||
|New-AzDataLakeStoreItem|New-AzDataLakeGen2Item|此 cmdlet 将从本地文件上传新文件内容。|
|Remove-AzDataLakeStoreItem|Remove-AzDataLakeGen2Item||
|Set-AzDataLakeStoreItemOwner<br>Set-AzDataLakeStoreItemPermission<br>Set-AzDataLakeStoreItemAcl|Update-AzDataLakeGen2Item|Update-AzDataLakeGen2Item cmdlet 仅更新单个项，而不是以递归方式更新。 如果要以递归方式进行更新，请使用 Get-AzDataLakeStoreChildItem cmdlet 将项列出，然后将管道序列到 Update-AzDataLakeGen2Item cmdlet。|
|Test-AzDataLakeStoreItem|Get-AzDataLakeGen2Item|如果项不存在，则 Get-AzDataLakeGen2Item cmdlet 将报告错误。|

## <a name="see-also"></a>另请参阅

* [已知问题](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [存储 PowerShell cmdlet](/powershell/module/az.storage)
