---
title: 部署 Azure 文件同步 | Microsoft Docs
description: 了解如何使用 Azure 门户、PowerShell 或 Azure CLI 从开始到完成部署 Azure 文件同步。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 11/05/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 4814a12a870d5317ad91c3514327ba0daad7ed69
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2021
ms.locfileid: "99225364"
---
# <a name="deploy-azure-file-sync"></a>部署 Azure 文件同步
使用 Azure 文件同步，即可将组织的文件共享集中在 Azure 文件中，同时又不失本地文件服务器的灵活性、性能和兼容性。 Azure 文件同步可将 Windows Server 转换为 Azure 文件共享的快速缓存。 可以使用 Windows Server 上可用的任意协议本地访问数据，包括 SMB、NFS 和 FTPS。 并且可以根据需要在世界各地具有多个缓存。

强烈建议先阅读[规划 Azure 文件部署](storage-files-planning.md)和[规划 Azure 文件同步部署](storage-sync-files-planning.md)，再按照本文中的步骤进行操作。

## <a name="prerequisites"></a>先决条件

# <a name="portal"></a>[门户](#tab/azure-portal)

1. 要部署 Azure 文件同步的同一区域中的 Azure 文件共享。有关详细信息，请参阅：
    - Azure 文件同步的[适用地区](storage-sync-files-planning.md#azure-file-sync-region-availability)。
    - [创建文件共享](storage-how-to-create-file-share.md)，了解创建文件共享的分步说明。
1. 至少有一个受支持的 Windows Server 或 Windows Server 群集实例与 Azure 文件同步同步。有关支持的 Windows Server 版本和推荐的系统资源的详细信息，请参阅 [windows file Server 注意事项](storage-sync-files-planning.md#windows-file-server-considerations)。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 要部署 Azure 文件同步的同一区域中的 Azure 文件共享。有关详细信息，请参阅：
    - Azure 文件同步的[适用地区](storage-sync-files-planning.md#azure-file-sync-region-availability)。
    - [创建文件共享](storage-how-to-create-file-share.md)，了解创建文件共享的分步说明。
1. 至少有一个受支持的 Windows Server 或 Windows Server 群集实例与 Azure 文件同步同步。有关支持的 Windows Server 版本和推荐的系统资源的详细信息，请参阅 [windows file Server 注意事项](storage-sync-files-planning.md#windows-file-server-considerations)。

1. Az PowerShell 模块可与 PowerShell 5.1 或 PowerShell 6 + 一起使用。 你可以在任何支持的系统（包括非 Windows 系统）上使用 Az PowerShell module for Azure 文件同步，但必须始终在要注册的 Windows Server 实例上运行服务器注册 cmdlet (可以直接或通过 PowerShell 远程处理) 执行此操作。 在 Windows Server 2012 R2 上，可以验证是否至少运行了 PowerShell 5.1。 \*通过查看 **$PSVersionTable** 对象的 **PSVersion** 属性的值：

    ```powershell
    $PSVersionTable.PSVersion
    ```

    如果 **PSVersion** 值小于5.1， \* 则与大多数全新安装的 Windows Server 2012 R2 一样，你可以通过下载并安装 [WINDOWS Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616)来轻松升级。 为 Windows Server 2012 R2 下载和安装的合适包是 **win 8.1 andw2k12r2-KB \* \* \* \* \* \* \* -x64**。 

    PowerShell 6 + 可用于任何受支持的系统，并且可以通过其 [GitHub 页面](https://github.com/PowerShell/PowerShell#get-powershell)下载。 

    > [!Important]  
    > 如果你计划使用服务器注册 UI，而不是直接从 PowerShell 注册，则必须使用 PowerShell 5.1。

1. 如果已选择使用 PowerShell 5.1，请确保至少安装了 .NET 4.7.2。 详细了解系统上的 [.NET Framework 版本和依赖关系](/dotnet/framework/migration-guide/versions-and-dependencies) 。

    > [!Important]  
    > 如果在 Windows Server Core 上安装 .NET 4.7.2 +，则必须用 `quiet` 和标志安装， `norestart` 否则安装将失败。 例如，如果安装 .NET 4.8，则命令将如下所示：
    > ```PowerShell
    > Start-Process -FilePath "ndp48-x86-x64-allos-enu.exe" -ArgumentList "/q /norestart" -Wait
    > ```

1. Az PowerShell 模块，可按照此处的说明进行安装： [安装和配置 Azure PowerShell](/powershell/azure/install-Az-ps)。
     
    > [!Note]  
    > 安装 Az PowerShell 模块后，将自动安装 Az Storagesync.sys 模块。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 要部署 Azure 文件同步的同一区域中的 Azure 文件共享。有关详细信息，请参阅：
    - Azure 文件同步的[适用地区](storage-sync-files-planning.md#azure-file-sync-region-availability)。
    - [创建文件共享](storage-how-to-create-file-share.md)，了解创建文件共享的分步说明。
1. 至少有一个受支持的 Windows Server 或 Windows Server 群集实例与 Azure 文件同步同步。有关支持的 Windows Server 版本和推荐的系统资源的详细信息，请参阅 [windows file Server 注意事项](storage-sync-files-planning.md#windows-file-server-considerations)。

1. [安装 Azure CLI](/cli/azure/install-azure-cli)

   如果愿意，还可以使用 Azure Cloud Shell 来完成本教程中的步骤。  Azure Cloud Shell 是一种可以通过浏览器使用的交互式 shell 环境。  使用下列方法之一开始使用 Cloud Shell：

   - 选择代码块右上角的“试用”。 **尝试它** 将打开 Azure Cloud Shell，但不会自动将代码复制到 Cloud Shell。

   - 通过转到 [https://shell.azure.com](https://shell.azure.com) 打开 Cloud Shell

   - 选择 [Azure 门户](https://portal.azure.com)右上角菜单栏上的 Cloud Shell 按钮

1. 登录。

   如果使用的是 CLI 的本地安装，请使用 [az login](/cli/azure/reference-index#az-login) 命令登录。

   ```azurecli
   az login
   ```

    遵循终端中显示的步骤完成身份验证过程。

1. 安装 [az filesync](/cli/azure/ext/storagesync/storagesync) Azure CLI 扩展。

   ```azurecli
   az extension add --name storagesync
   ```

   安装 **storagesync.sys** 扩展引用后，您将收到以下警告。

   ```output
   The installed extension 'storagesync' is experimental and not covered by customer support. Please use with discretion.
   ```

---

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>准备 Windows Server，用于 Azure 文件同步
对于要与 Azure 文件同步配合使用的每个服务器（包括故障转移群集中的服务器节点），请禁用“Internet Explorer 增强的安全性配置”。 只需在最初注册服务器时禁用。 可在注册服务器后重新启用。

# <a name="portal"></a>[门户](#tab/azure-portal)
> [!Note]  
> 如果要在 Windows Server Core 上部署 Azure 文件同步，则可以跳过此步骤。

1. 打开服务器管理器。
2. 单击“本地服务器”：  
    ![服务器管理器 UI 左侧的“本地服务器”](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
3. 在“属性”子窗格上，选择“IE 增强的安全性配置”的链接。  
    ![服务器管理器 UI 中的“IE 增强的安全性配置”窗格](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
4. 在 " **Internet Explorer 增强的安全配置**" 对话框中，为 "**管理员**" 和 "**用户**" 选择 "**关闭**"：  
    ![选定“关”的“Internet Explorer 增强的安全性配置”弹出窗口](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
若要禁用“Internet Explorer 增强的安全性配置”，请在权限提升的 PowerShell 会话中执行以下命令：

```powershell
$installType = (Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\").InstallationType

# This step is not required for Server Core
if ($installType -ne "Server Core") {
    # Disable Internet Explorer Enhanced Security Configuration 
    # for Administrators
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force
    
    # Disable Internet Explorer Enhanced Security Configuration 
    # for Users
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A8-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force
    
    # Force Internet Explorer closed, if open. This is required to fully apply the setting.
    # Save any work you have open in the IE browser. This will not affect other browsers,
    # including Microsoft Edge.
    Stop-Process -Name iexplore -ErrorAction SilentlyContinue
}
``` 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

按照 Azure 门户或 PowerShell 的说明进行操作。

---

## <a name="deploy-the-storage-sync-service"></a>部署存储同步服务 
Azure 文件同步的部署过程首先会将一个“存储同步服务”资源放入所选订阅的资源组中。 我们建议尽量少预配这些资源。 将在服务器与此资源之间创建信任关系，一个服务器只能注册到一个存储同步服务。 因此，我们建议根据需要部署尽量多的存储同步服务，以隔离服务器组。 请记住，不同存储同步服务中的服务器不能彼此同步。

> [!Note]
> 存储同步服务将从它已部署到的订阅和资源组继承访问权限。 我们建议仔细检查谁有权访问该服务。 具有写访问权限的实体可以开始从已注册到此存储同步服务的服务器同步新的文件集，使数据流向这些实体可以访问的 Azure 存储。

# <a name="portal"></a>[门户](#tab/azure-portal)
若要部署存储同步服务，请单击 " [Azure 门户](https://portal.azure.com/)"，再单击 " *创建资源* "，然后搜索 Azure 文件同步。在搜索结果中，选择 " **Azure 文件同步**"，然后选择 " **创建** " 以打开 " **部署存储同步** " 选项卡。

在打开的窗格中，输入以下信息：

- **名称**：存储同步服务每个区域)  (唯一名称。
- 订阅：需要在其中创建存储同步服务的订阅。 根据组织的配置策略，可能有权访问一个或多个订阅。 Azure 订阅是对每项云服务（如 Azure 文件）计费的最基本容器。
- **资源组**：资源组是 Azure 资源（如存储帐户或存储同步服务）的逻辑组。 你可以创建新的资源组或使用现有资源组进行 Azure 文件同步。 (建议使用资源组作为容器以逻辑方式将资源隔离到你的组织，例如对 HR 资源或特定项目的资源进行分组。 ) 
- **位置**：要在其中部署 Azure 文件同步的区域。此列表中仅提供受支持的区域。

完成后，选择“创建”部署存储同步服务。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
`<Az_Region>` `<RG_Name>` 使用你自己的值替换、和， `<my_storage_sync_service>` 然后使用以下命令创建并部署存储同步服务：

```powershell
$hostType = (Get-Host).Name

if ($installType -eq "Server Core" -or $hostType -eq "ServerRemoteHost") {
    Connect-AzAccount -UseDeviceAuthentication
}
else {
    Connect-AzAccount
}

# this variable holds the Azure region you want to deploy 
# Azure File Sync into
$region = '<Az_Region>'

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = @()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the selected Azure Region or the region is mistyped.")
}

# the resource group to deploy the Storage Sync Service into
$resourceGroup = '<RG_Name>'

# Check to ensure resource group exists and create it if doesn't
$resourceGroups = @()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    New-AzResourceGroup -Name $resourceGroup -Location $region
}

$storageSyncName = "<my_storage_sync_service>"
$storageSync = New-AzStorageSyncService -ResourceGroupName $resourceGroup -Name $storageSyncName -Location $region
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

按照 Azure 门户或 PowerShell 的说明进行操作。

---

## <a name="install-the-azure-file-sync-agent"></a>安装 Azure 文件同步代理
Azure 文件同步代理是一个可下载包，可实现 Windows 服务器与 Azure 文件共享的同步。 

# <a name="portal"></a>[门户](#tab/azure-portal)
可从 [Microsoft 下载中心](https://go.microsoft.com/fwlink/?linkid=858257)下载代理。 下载完成后，双击 MSI 包，开始安装 Azure 文件同步代理。

> [!Important]  
> 如果要对故障转移群集使用 Azure 文件同步，则 必须在群集中的每个节点上安装 Azure 文件同步代理。 群集中的每个节点都必须注册才能使用 Azure 文件同步。

建议执行以下操作：
- 保留默认安装路径(C:\Program Files\Azure\StorageSyncAgent)，以简化故障排除和服务器维护。
- 启用 Microsoft 更新，使 Azure 文件同步保持最新。 Azure 文件同步代理的所有更新（包括功能更新和修补程序）都可从 Microsoft 更新进行。 建议将最新更新安装到 Azure 文件同步。有关详细信息，请参阅 [Azure 文件同步更新策略](storage-sync-files-planning.md#azure-file-sync-agent-update-policy)。

Azure 文件同步代理安装完成后，服务器注册 UI 自动打开。 在注册之前，必须创建存储同步服务；请参阅下一部分了解如何创建存储同步服务。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
执行以下 PowerShell 代码，以下载适用于所用 OS 的 Azure 文件同步代理版本，并将其安装在系统上。

> [!Important]  
> 如果要对故障转移群集使用 Azure 文件同步，则 必须在群集中的每个节点上安装 Azure 文件同步代理。 必须注册群集中的每个节点才能使用 Azure 文件同步。

```powershell
# Gather the OS version
$osver = [System.Environment]::OSVersion.Version

# Download the appropriate version of the Azure File Sync agent for your OS.
if ($osver.Equals([System.Version]::new(10, 0, 17763, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2019 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(10, 0, 14393, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2016 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(6, 3, 9600, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2012R2 `
        -OutFile "StorageSyncAgent.msi" 
} else {
    throw [System.PlatformNotSupportedException]::new("Azure File Sync is only supported on Windows Server 2012 R2, Windows Server 2016, and Windows Server 2019")
}

# Install the MSI. Start-Process is used to PowerShell blocks until the operation is complete.
# Note that the installer currently forces all PowerShell sessions closed - this is a known issue.
Start-Process -FilePath "StorageSyncAgent.msi" -ArgumentList "/quiet" -Wait

# Note that this cmdlet will need to be run in a new session based on the above comment.
# You may remove the temp folder containing the MSI and the EXE installer
Remove-Item -Path ".\StorageSyncAgent.msi" -Recurse -Force
```
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

按照 Azure 门户或 PowerShell 的说明进行操作。

---

## <a name="register-windows-server-with-storage-sync-service"></a>向存储同步服务注册 Windows Server
向存储同步服务注册 Windows Server 可在服务器（或群集）与存储同步服务之间建立信任关系。 一个服务器只能注册到一个存储同步服务，并可与同一存储同步服务关联的其他服务器和 Azure 文件共享同步。

> [!Note]
> 服务器注册使用你的 Azure 凭据在存储同步服务与 Windows Server 之间创建信任关系，但是，服务器随后会创建并使用自身有效的标识，前提是该服务器保持已注册状态，并且当前的共享访问签名令牌（存储 SAS）有效。 取消注册服务器后，无法将新的 SAS 令牌颁发给服务器，因此，服务器无法访问 Azure 文件共享，并停止任何同步。

注册服务器的管理员必须是给定的存储同步服务的管理角色 **所有者** 或 **参与者** 的成员。 这可以在存储同步服务的 Azure 门户中的 " **访问控制" (IAM)** 进行配置。

此外，还可以区分管理员将服务器注册到允许的服务器，也可以在存储同步服务中配置同步。 为此，您需要创建一个自定义角色，其中列出了只允许注册服务器的管理员，并为您的自定义角色提供下列权限：

* "Storagesync.sys/storageSyncServices/registeredServers/write"
* "Storagesync.sys/storageSyncServices/read"
* "Storagesync.sys/storageSyncServices/工作流/读取"
* "Storagesync.sys/storageSyncServices/工作流/操作/读取"

# <a name="portal"></a>[门户](#tab/azure-portal)
服务器注册 UI 应在 Azure 文件同步代理安装后自动打开。 如果没有打开，可以手动从其文件位置 C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe 打开。 服务器注册 UI 打开时，请选择“登录”开始操作。

登录后，系统会提示输入以下信息：

![服务器注册 UI 的屏幕快照](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- Azure 订阅：包含存储同步服务的订阅（请参阅[部署存储同步服务](#deploy-the-storage-sync-service)）。 
- 资源组：包含存储同步服务的资源组。
- 存储同步服务：想要向其注册的存储同步服务的名称。

选择相应的信息之后，选择“注册”完成服务器注册。 在注册过程中，系统会提示进行其他登录。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$registeredServer = Register-AzStorageSyncServer -ParentObject $storageSync
```
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

按照 Azure 门户或 PowerShell 的说明进行操作。

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>创建同步组和云终结点
同步组定义一组文件的同步拓扑。 同步组中的终结点保持彼此同步。 同步组中必须包含一个表示 Azure 文件共享的云终结点，以及一个或多个服务器终结点。 服务器终结点表示已注册服务器上的路径。 服务器可以包含多个同步组中的服务器终结点。 可以创建任意数量的同步组，以适当地描述所需的同步拓扑。

云终结点是指向 Azure 文件共享的指针。 所有服务器终结点将与某个云终结点同步，使该云终结点成为中心。 Azure 文件共享的存储帐户必须位于存储同步服务所在的同一个区域。 将同步整个 Azure 文件共享，但存在一种例外情况：将预配一个特殊的文件夹，它相当于 NTFS 卷上的“System Volume Information”隐藏文件夹。 此目录名为“.SystemShareInformation”。 其中包含不会同步到其他终结点的重要同步元数据。 请不要使用或删除它！

> [!Important]  
> 可对同步组中的任何云终结点或服务器终结点进行更改，并将文件同步到同步组中的其他终结点。 如果直接对云终结点（Azure 文件分享）进行更改，首先需要通过 Azure 文件同步更改检测作业来发现更改。 每 24 小时仅针对云终结点启动一次更改检测作业。 有关详细信息，请参阅 [Azure 文件常见问题解答](storage-files-faq.md#afs-change-detection)。

创建云终结点的管理员必须是包含云终结点指向的 Azure 文件共享的存储帐户的管理角色 **所有者** 的成员。 这可以在 " **访问控制" (IAM)** 在存储帐户的 Azure 门户中进行配置。

# <a name="portal"></a>[门户](#tab/azure-portal)
若要创建同步组，请在 [Azure 门户](https://portal.azure.com/)中，请前往你的存储同步服务，然后选择 " **+ 同步组**"：

![在 Azure 门户中创建新的同步组](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

在打开的窗格中输入以下信息，创建具有云终结点的同步组：

- **同步组名称**：要创建的同步组的名称。 此名称在存储同步服务内必须是唯一的，但可以是符合逻辑的任何名称。
- 订阅：在[部署存储同步服务](#deploy-the-storage-sync-service)中用于部署存储同步服务的订阅。
- 存储帐户：如果选择“选择存储账户”，另一个窗格随即出现，可在其中选择包含要同步的 Azure 文件共享的存储帐户。
- **Azure 文件共享**：要与其同步的 Azure 文件共享的名称。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
若要创建同步组，请执行以下 PowerShell。 请记得将 `<my-sync-group>` 替换为同步组的所需名称。

```powershell
$syncGroupName = "<my-sync-group>"
$syncGroup = New-AzStorageSyncGroup -ParentObject $storageSync -Name $syncGroupName
```

成功创建同步组后，可以创建云终结点。 请务必将 `<my-storage-account>` 和 `<my-file-share>` 替换为预期的值。

```powershell
# Get or create a storage account with desired name
$storageAccountName = "<my-storage-account>"
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup | Where-Object {
    $_.StorageAccountName -eq $storageAccountName
}

if ($storageAccount -eq $null) {
    $storageAccount = New-AzStorageAccount `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup `
        -Location $region `
        -SkuName Standard_LRS `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly:$true
}

# Get or create an Azure file share within the desired storage account
$fileShareName = "<my-file-share>"
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    $fileShare = New-AzStorageShare -Context $storageAccount.Context -Name $fileShareName
}

# Create the cloud endpoint
New-AzStorageSyncCloudEndpoint `
    -Name $fileShare.Name `
    -ParentObject $syncGroup `
    -StorageAccountResourceId $storageAccount.Id `
    -AzureFileShareName $fileShare.Name
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 [az storagesync.sys](/cli/azure/ext/storagesync/storagesync/sync-group#ext-storagesync-az-storagesync-sync-group-create) 命令创建新的同步组。  若要为所有 CLI 命令默认设置资源组，请使用 [az configure](/cli/azure/reference-index#az-configure)。

```azurecli
az storagesync sync-group create --resource-group myResourceGroupName \
                                 --name myNewSyncGroupName \
                                 --storage-sync-service myStorageSyncServiceName \
```

使用 [az storagesync.sys](/cli/azure/ext/storagesync/storagesync/sync-group/cloud-endpoint#ext-storagesync-az-storagesync-sync-group-cloud-endpoint-create) 命令创建新的云终结点。

```azurecli
az storagesync sync-group cloud-endpoint create --resource-group myResourceGroup \
                                                --storage-sync-service myStorageSyncServiceName \
                                                --sync-group-name mySyncGroupName \
                                                --name myNewCloudEndpointName \
                                                --storage-account mystorageaccountname \
                                                --azure-file-share-name azure-file-share-name
```

---

## <a name="create-a-server-endpoint"></a>创建服务器终结点
服务器终结点代表已注册服务器上的特定位置，例如服务器卷中的文件夹。 服务器终结点必须是已注册的服务器（而不是装载的共享）上的路径；若要使用云分层，该路径必须在非系统卷上。 不支持网络附加存储 (NAS)。

> [!NOTE]
> 不支持在卷上创建服务器终结点后更改路径或驱动器号。 请确保在已注册的服务器上使用最终路径。

# <a name="portal"></a>[门户](#tab/azure-portal)
若要添加服务器终结点，请前往新创建的同步组，然后选择 " **添加服务器终结点**"。

![在“同步组”窗格中添加一个新的服务器终结点](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

在 " **添加服务器终结点** " 窗格中，输入以下信息以创建服务器终结点：

- **已注册服务器**：要在其中创建服务器终结点的服务器或群集的名称。
- **路径**：要作为同步组的一部分进行同步的 Windows Server 路径。
- 云分层：启用或禁用云分层的开关。 通过云分层可以将不常使用或访问的文件分层到 Azure 文件。
- **卷可用** 空间：要在服务器终结点所在的卷上保留的可用空间量。 例如，如果有一个服务器终结点的卷上的卷可用空间设置为 50%，则约有一半数据会分层为 Azure 文件。 不管是否启用云分层，Azure 文件共享在同步组中始终具有完整的数据副本。
- **初始下载模式**：这是可选选择，从代理版本11开始，在 Azure 文件共享中存在文件但服务器上没有文件时，这会很有用。 例如，如果您创建一个服务器终结点来向同步组添加另一个分支机构服务器，或者在灾难恢复失败的服务器时，则可能存在这种情况。 如果启用了云分层，则默认情况下将仅重新调用命名空间，最初不会有任何文件内容。 如果你认为用户访问请求应决定将哪些文件内容撤回到服务器，这会很有用。 如果已禁用云分层，则默认情况下将先下载命名空间，然后将根据上次修改的时间戳撤回文件，直到达到本地容量。 但是，可以将初始下载模式改为仅命名空间。 如果为此服务器终结点禁用了云分层，则只能使用第三种模式。 此模式避免首先撤回命名空间。 如果文件有机会完全下载，则文件将仅出现在本地服务器上。 此模式在以下情况下非常有用：应用程序要求存在完整文件，并且无法容忍其命名空间中的分层文件。

若要添加服务器终结点，请选择 " **创建**"。 现在，文件在 Azure 文件共享和 Windows Server 之间保持保存。 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
执行以下 PowerShell 命令来创建服务器终结点，并确保将和替换为 `<your-server-endpoint-path>` `<your-volume-free-space>` 所需的值，并检查可选的可选设置以获取可选的初始下载策略。

```powershell
$serverEndpointPath = "<your-server-endpoint-path>"
$cloudTieringDesired = $true
$volumeFreeSpacePercentage = <your-volume-free-space>
# Optional property. Choose from: [NamespaceOnly] default when cloud tiering is enabled. [NamespaceThenModifiedFiles] default when cloud tiering is disabled. [AvoidTieredFiles] only available when cloud tiering is disabled.
$initialDownloadPolicy = NamespaceOnly

if ($cloudTieringDesired) {
    # Ensure endpoint path is not the system volume
    $directoryRoot = [System.IO.Directory]::GetDirectoryRoot($serverEndpointPath)
    $osVolume = "$($env:SystemDrive)\"
    if ($directoryRoot -eq $osVolume) {
        throw [System.Exception]::new("Cloud tiering cannot be enabled on the system volume")
    }

    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -CloudTiering `
        -VolumeFreeSpacePercent $volumeFreeSpacePercentage `
        -InitialDownloadPolicy $initialDownloadPolicy
} else {
    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -InitialDownloadPolicy $initialDownloadPolicy
}
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 [az storagesync.sys-endpoint](/cli/azure/ext/storagesync/storagesync/sync-group/server-endpoint#ext-storagesync-az-storagesync-sync-group-server-endpoint-create) 命令创建新的服务器终结点。

```azurecli
# Create a new sync group server endpoint 
az storagesync sync-group server-endpoint create --resource-group myResourceGroupName \
                                                 --name myNewServerEndpointName
                                                 --registered-server-id 91beed22-7e9e-4bda-9313-fec96c286e0
                                                 --server-local-path d:\myPath
                                                 --storage-sync-service myStorageSyncServiceNAme
                                                 --sync-group-name mySyncGroupName

# Create a new sync group server endpoint with additional optional parameters
az storagesync sync-group server-endpoint create --resource-group myResourceGroupName \
                                                 --storage-sync-service myStorageSyncServiceName \
                                                 --sync-group-name mySyncGroupName \
                                                 --name myNewServerEndpointName \
                                                 --registered-server-id 91beed22-7e9e-4bda-9313-fec96c286e0 \
                                                 --server-local-path d:\myPath \
                                                 --cloud-tiering on \
                                                 --volume-free-space-percent 85 \
                                                 --tier-files-older-than-days 15 \
                                                 --initial-download-policy NamespaceOnly [OR] NamespaceThenModifiedFiles [OR] AvoidTieredFiles
                                                 --offline-data-transfer on \
                                                 --offline-data-transfer-share-name myfilesharename \

```

---

## <a name="configure-firewall-and-virtual-network-settings"></a>配置防火墙和虚拟网络设置

### <a name="portal"></a>门户
如果要将 Azure 文件同步配置为使用防火墙和虚拟网络设置，请执行以下操作：

1. 在 Azure 门户中，导航到要保护的存储帐户。
1. 选择左侧菜单中的 " **防火墙和虚拟网络** " 按钮。
1. 在 "**允许访问**" 下选择 **所选网络**。
1. 确保你的服务器 IP 或虚拟网络列在相应的部分下。
1. 请确保选中 " **允许受信任的 Microsoft 服务访问此存储帐户** "。
1. 选择“保存”以保存设置。

![配置防火墙和虚拟网络设置以使用 Azure 文件同步](media/storage-sync-files-deployment-guide/firewall-and-vnet.png)

## <a name="onboarding-with-azure-file-sync"></a>使用 Azure 文件同步进行载入
第一次在 Azure 文件同步上载入的建议步骤为零停机时间，同时保留完全文件保真和访问控制列表 (ACL) 如下所示：
 
1. 部署存储同步服务。
1. 创建一个同步组。
1. 在包含完整数据集的服务器上安装 Azure 文件同步代理。
1. 注册该服务器并在共享中创建一个服务器终结点。 
1. 让同步服务执行到 Azure 文件共享（云终结点）的完整上传。  
1. 在初始上传完成后，在剩余的每台服务器上安装 Azure 文件同步代理。
1. 在剩余的每台服务器上创建新的文件共享。
1. 使用云分层策略在新的文件共享中创建服务器终结点（如果需要）。 （此步骤要求有额外的存储可供初始设置使用。）
1. 让 Azure 文件同步代理可以快速还原完整的命名空间，而无需实际进行数据传输。 在完成完整的命名空间同步后，同步引擎将根据服务器终结点的云分层策略填充本地磁盘空间。 
1. 确保同步完成，并根据需要测试拓扑。 
1. 将用户和应用程序重定向到此新共享。
1. 还可以选择删除服务器上任何重复的共享。
 
如果没有可用于初始载入的额外存储空间，并且希望附加到现有的共享，则可以在 Azure 文件共享中预先播种数据。 当且仅当可以接受停机并且绝对可以保证在初始载入过程中服务器共享上不会发生数据更改时，才建议使用此方法。 
 
1. 确保任何服务器上的数据在载入过程中都不会更改。
1. 使用任何数据传输工具通过 SMB 对 Azure 文件共享进行预 seed。 例如，Robocopy。 还可以使用 AzCopy 而不是 REST。 请确保将 AzCopy 与适当的开关一起使用，以保留 Acl 时间戳和属性。
1. 使用所需的指向现有共享的服务器终结点创建 Azure 文件同步拓扑。
1. 让同步服务在所有终结点上完成对帐过程。 
1. 在对帐完成后，你可以打开共享进行更改。
 
目前，预播种方法有一些限制- 
- 在同步拓扑完全启动并运行之前更改服务器上的数据可能会导致各个服务器终结点上发生冲突。  
- 创建云终结点后，在开始初始同步之前，Azure 文件同步会运行一个进程来检测云中的文件。完成此过程所花的时间取决于各种因素，如网络速度、可用带宽以及文件和文件夹的数目。 对于预览版，粗略估计，检测流程以大约每秒 10 个文件的速度运行。因此，当在云中预先播种数据时，即使预先播种运行速度很快，获得完全运行的系统所需的总体时间也会更长。

## <a name="self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service"></a>通过早期版本和 VSS (卷影复制服务) 进行自助还原

> [!IMPORTANT]
> 以下信息仅可用于版本 9 (或更高版本的存储同步代理) 。 低于9的版本将不具有 StorageSyncSelfService cmdlet。

以前的版本是一项 Windows 功能，可用于利用卷的服务器端 VSS 快照将文件的可恢复版本提供给 SMB 客户端。
这将为信息工作者（而不是从 IT 管理员的还原）直接为信息工作者提供一种功能强大的方案（通常称为自助还原）。

VSS 快照和先前版本的工作方式与 Azure 文件同步无关。但是，必须将云分层设置为兼容模式。 许多 Azure 文件同步服务器终结点可存在于同一个卷上。 你必须为每个卷进行以下 PowerShell 调用，此 PowerShell 调用甚至有一个服务器终结点，你打算或使用云分层。

```powershell
Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'
Enable-StorageSyncSelfServiceRestore [-DriveLetter] <string> [[-Force]] 
```

VSS 快照是整个卷。 默认情况下，给定卷最多可以有64个快照，因为有足够的空间来存储快照。 VSS 会自动处理这种情况。 默认快照计划从星期一到星期五每天拍摄两个快照。 可以通过 Windows 计划任务来配置该计划。 上述 PowerShell cmdlet 执行两项操作：
1. 它将 Azure 文件配置为在指定的卷上同步云分层，以与以前的版本兼容，并确保可以从以前的版本中还原文件，即使已将该文件分层到服务器上的云中。 
1. 它启用默认 VSS 计划。 然后，你可以决定稍后修改它。 

> [!Note]  
> 此处需要注意两个要点：
>- 如果使用-Force 参数，且当前启用了 VSS，则它将覆盖当前 VSS 快照计划，并将其替换为默认计划。 确保在运行 cmdlet 之前保存自定义配置。
> - 如果在群集节点上使用此 cmdlet，则还必须在群集中的所有其他节点上运行该 cmdlet！ 

若要查看是否已启用自助恢复兼容性，你可以运行以下 cmdlet。

```powershell
Get-StorageSyncSelfServiceRestore [[-Driveletter] <string>]
```

它将列出服务器上的所有卷，以及每个卷的云分层兼容日期数。 此数字是基于每个卷的最大可能快照数和默认快照计划自动计算的。 因此，默认情况下，提供给信息工作者的所有先前版本都可用于从还原。 如果更改默认计划以拍摄更多快照，则情况也是如此。
但是，如果您更改计划的方式将导致卷上的可用快照超过兼容日期值，则用户将无法使用旧版本的快照 (早期版本) 从还原。

> [!Note]
> 启用自助服务还原可能会影响 Azure 存储消耗和计费。 此影响仅限于服务器上当前分层的文件。 启用此功能可确保云中提供的文件版本可通过早期版本 (VSS 快照) 条目进行引用。
>
> 如果禁用该功能，则 Azure 存储消耗将慢慢降低，直到达到 "兼容日期" 窗口。 没有办法加速。 

每个卷的默认最大 VSS 快照数 (64) 以及用于执行该操作的默认计划，将导致信息工作者可以从其还原的以前版本中的最大值为45天，具体取决于你可以在卷上存储多少个 VSS 快照。

如果每个卷的最大 64 VSS 快照不是正确的设置，则可以 [通过注册表项更改](/windows/win32/backup/registry-keys-for-backup-and-restore#maxshadowcopies)该值。
要使新的限制生效，你需要重新运行 cmdlet，以便在以前启用它的每个卷上启用以前版本的兼容性，并使用-Force 标志将新的每个卷的最大 VSS 快照数考虑在内。 这将导致新计算的兼容天数。 请注意，此更改只会对新的分层文件生效，并覆盖你可能已执行的 VSS 计划中的任何自定义。

<a id="proactive-recall"></a>
## <a name="proactively-recall-new-and-changed-files-from-an-azure-file-share"></a>主动从 Azure 文件共享撤回新文件和更改的文件

对于代理版本11，新模式在服务器终结点上可用。 即使在本地用户访问任何文件之前，此模式也允许全球分布的公司预先填充的远程区域中的服务器缓存。 在服务器终结点上启用时，此模式将导致此服务器撤回在 Azure 文件共享中已创建或更改的文件。

### <a name="scenario"></a>方案

全球分布式公司在美国和印度有分支机构。 早上 (我们的时间) 信息工作者为全新的项目创建一个新文件夹和新文件，并在其上工作一整天。 Azure 文件同步会将文件夹和文件同步到 (云终结点) 的 Azure 文件共享。 印度的信息工作者将继续在其时区中处理该项目。 在上午，印度的本地 Azure 文件同步启用的服务器需要在本地提供这些新文件，使印度团队能够有效地使用本地缓存。 启用此模式可防止初始文件访问速度较慢，因为需要重新调用，并使服务器能够在 Azure 文件共享中更改或创建文件后主动重新调用文件。

> [!IMPORTANT]
> 必须认识到，跟踪服务器上的 Azure 文件共享中的更改可能会增加传出流量并从 Azure 计费。 如果在本地不需要回调到服务器的文件，则对服务器的不必要的撤回可能会产生负面影响。 如果你知道在云中最近更改的服务器上预填充缓存，则使用此模式将对使用该服务器上的文件的用户或应用程序产生积极影响。

### <a name="enable-a-server-endpoint-to-proactively-recall-what-changed-in-an-azure-file-share"></a>启用服务器终结点以主动撤回 Azure 文件共享中的更改

# <a name="portal"></a>[门户](#tab/proactive-portal)

1. 在 [Azure 门户](https://portal.azure.com/)中，请切换到存储同步服务，选择正确的同步组，然后在 Azure 文件共享 (云终结点) 中，确定要密切跟踪其更改的服务器终结点。
1. 在 "云分层" 部分中，找到 "Azure 文件共享下载" 主题。 你将看到当前所选模式，可以将其更改为跟踪 Azure 文件共享更改，并主动地将其重新与服务器撤回。

:::image type="content" source="media/storage-sync-files-deployment-guide/proactive-download.png" alt-text="显示当前生效的服务器终结点的 Azure 文件共享下载行为的图像，以及用于打开允许更改该服务的菜单的按钮。":::

# <a name="powershell"></a>[PowerShell](#tab/proactive-powershell)

可以通过 [AzStorageSyncServerEndpoint](/powershell/module/az.storagesync/set-azstoragesyncserverendpoint) Cmdlet 修改 PowerShell 中的服务器终结点属性。

```powershell
# Optional parameter. Default: "UpdateLocallyCachedFiles", alternative behavior: "DownloadNewAndModifiedFiles"
$recallBehavior = "DownloadNewAndModifiedFiles"

Set-AzStorageSyncServerEndpoint -InputObject <PSServerEndpoint> -LocalCacheMode $recallBehavior
```

---

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>将 DFS 复制 (DFS-R) 部署迁移至 Azure 文件同步
若要将 DFS-R 部署迁移至 Azure 文件同步，请执行以下操作：

1. 创建一个同步组以表示要替换的 DFS-R 拓扑。
2. 从 DFS-R 拓扑具有完整数据集的服务器开始迁移。 在该服务器上安装 Azure 文件同步。
3. 注册该服务器，并为要迁移的第一个服务器创建服务器终结点。 请勿启用云分层。
4. 让所有数据同步至你的 Azure 文件共享（云终结点）。
5. 在剩余的每个 DFS-R 服务器上安装并注册 Azure 文件同步代理。
6. 禁用 DFS-R。 
7. 在每个 DFS-R 服务器上创建服务器终结点。 请勿启用云分层。
8. 确保同步完成，并根据需要测试拓扑。
9. 注销 DFS-R。
10. 现在应该可以根据需要在任何一个服务器终结点上启用云分层。

有关详细信息，请参阅 [Azure 文件同步与分布式文件系统 (DFS) 的互操作](storage-sync-files-planning.md#distributed-file-system-dfs)。

## <a name="next-steps"></a>后续步骤
- [添加或删除 Azure 文件同步服务器终结点](storage-sync-files-server-endpoint.md)
- [向 Azure 文件同步注册或注销服务器](storage-sync-files-server-registration.md)
- [监视 Azure 文件同步](storage-sync-files-monitoring.md)
