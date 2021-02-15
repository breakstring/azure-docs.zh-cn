---
title: Azure 文件同步代理发行说明 | Microsoft Docs
description: 阅读 Azure 文件同步代理的发行说明，这使你可以将组织的文件共享集中在 Azure 文件中。
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 2/11/2021
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 4e1b5828e096d3d712def5420fd846d65b22e696
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100366103"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Azure 文件同步代理发行说明
借助 Azure 文件同步，既可将组织的文件共享集中在 Azure 文件中，又不失本地文件服务器的灵活性、性能和兼容性。 Windows Server 安装可转换为 Azure 文件共享的快速缓存。 可以使用 Windows Server 上提供的任意协议（包括 SMB、NFS 和 FTPS）以本地方式访问数据， 并且可以根据需要在世界各地设置多个缓存。

本文提供受支持的 Azure 文件同步代理版本的发行说明。

## <a name="supported-versions"></a>支持的版本
支持以下 Azure 文件同步代理版本：

| 里程碑 | 代理版本号 | 发布日期 | 状态 |
|----|----------------------|--------------|------------------|
| V2.0 版本- [KB4539952](https://support.microsoft.com/topic/azure-file-sync-agent-v11-2-release-february-2021-c956eaf0-cd8e-4511-98c0-e5a1f2c84048)| 11.2.0.0 | 2021年2月2日 | 支持-试验 |
| V2.0 版本- [KB4539951](https://support.microsoft.com/help/4539951)| 11.1.0.0 | 2020年11月4日 | 支持 |
| V2.0 版本- [KB4522411](https://support.microsoft.com/help/4522411)| 10.1.0.0 | 6月5日2020 | 支持 |
| 2020 年 5 月更新汇总 - [KB4522412](https://support.microsoft.com/help/4522412)| 10.0.2.0 | 2020 年 5 月 19 日 | 支持 |
| V10 版本 - [KB4522409](https://support.microsoft.com/help/4522409)| 10.0.0.0 | 2020 年 4 月 9 日 | 支持 |
| 2019 年 12 月更新汇总 - [KB4522360](https://support.microsoft.com/help/4522360)| 9.1.0.0 | 2019 年 12 月 12 日 | 支持的代理版本将于2021年2月16日过期 |
| V9 版本 - [KB4522359](https://support.microsoft.com/help/4522359)| 9.0.0.0 | 2019 年 12 月 2 日 | 支持的代理版本将于2021年2月16日过期 |

## <a name="unsupported-versions"></a>不支持的版本
以下 Azure 文件同步代理版本已过期，不再受支持：

| 里程碑 | 代理版本号 | 发布日期 | 状态 |
|----|----------------------|--------------|------------------|
| V8 版本 | 8.0.0.0 | 不可用 | 不支持-代理版本在2021年1月12日过期 |
| V7 版本 | 7.0.0.0-7.2.0。0 | 不可用 | 不支持-代理版本在2020年9月1日过期 |
| V6 版本 | 6.0.0.0 - 6.3.0.0 | 空值 | 不支持 - 代理版本已于 2020 年 4 月 21 日到期 |
| V5 版本 | 5.0.2.0 - 5.2.0.0 | 空值 | 不支持 - 代理版本已于 2020 年 3 月 18 日到期 |
| V4 版本 | 4.0.1.0 - 4.3.0.0 | 空值 | 不支持 - 代理版本已于 2019 年 11 月 6 日到期 |
| V3 版本 | 3.1.0.0 - 3.4.0.0 | 空值 | 不支持 - 代理版本已于 2019 年 8 月 19 日到期 |
| 预正式发布代理 | 1.1.0.0 - 3.0.13.0 | 空值 | 不支持 - 代理版本于 2018 年 10 月 1 日到期 |

### <a name="azure-file-sync-agent-update-policy"></a>Azure 文件同步代理更新策略
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-11200"></a>代理版本11.2.0。0
以下发行说明适用于2021年2月2日发布的 Azure 文件同步代理的版本11.2.0.0。 除了为版本11.1.0.0 列出的发行说明外，还提供了这些说明。

### <a name="improvements-and-issues-that-are-fixed"></a>改进和已解决的问题 
- 如果由于每个项的错误数过多而取消了同步会话，则当 Azure 文件同步服务确定自定义同步会话以便更正每个项的错误时，同步可能会在启动新会话时经过协调。
- 使用 Register-AzStorageSyncServer cmdlet 注册服务器可能会失败，并出现 "未经处理的异常" 错误。
- 新的 PowerShell cmdlet (StorageSyncAllowedServerEndpointPath) 在服务器上配置允许的服务器终结点路径。 如果 Azure 文件同步部署由云解决方案提供商 (CSP) 或服务提供商管理，并且客户希望在服务器上配置允许的服务器终结点路径，则此 cmdlet 非常有用。 创建服务器终结点时，如果指定的路径不在允许列表中，则服务器终结点的创建将失败。 请注意，这是一项可选功能，创建服务器终结点时，默认情况下允许所有受支持的路径。  

    
    - 若要添加允许的服务器终结点路径，请在服务器上运行以下 PowerShell 命令：

    ```powershell
    Import-Module 'C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll' -verbose
    Add-StorageSyncAllowedServerEndpointPath -Path <path>
    ```  

    - 若要获取支持的路径列表，请运行以下 PowerShell 命令：
    
    ```powershell
    Get-StorageSyncAllowedServerEndpointPath
    ```     
    - 若要删除路径，请运行以下 PowerShell 命令：
    
    ```powershell
    Remove-StorageSyncAllowedServerEndpointPath -Path <path>
    ```  
## <a name="agent-version-11100"></a>代理版本11.1.0。0
以下发行说明适用于) 2020 年11月4日发布的 Azure 文件同步代理 (版本11.1.0.0。

### <a name="improvements-and-issues-that-are-fixed"></a>改进和已解决的问题
- 新的云分层模式，可控制初始下载和主动召回
    - 初始下载模式：你现在可以选择希望文件最初下载到新服务器终结点的方式。 需要将所有文件分层或尽可能多的文件下载到服务器上按上次修改时间戳进行下载？ 您可以这样做！ 不能使用云分层？ 你现在可以选择避免系统上的分层文件。 若要了解详细信息，请参阅部署 Azure 文件同步文档中的 [创建服务器终结点](./storage-sync-files-deployment-guide.md?tabs=azure-portal%252cproactive-portal#create-a-server-endpoint) 部分。
    - 主动撤回模式：每次创建或修改文件时，都可以主动地将其撤回到在同一同步组中指定的服务器。 这使文件随时可用于指定的每个服务器。 地球上的团队是否正在处理相同的数据？ 启用主动撤回，以便在团队到达下一个早上，团队在不同时区中更新的所有文件都将下载并准备就绪！ 若要了解详细信息，请参阅部署 Azure 文件同步文档中的 [主动召回 Azure 文件共享中的新文件和更改的文件](./storage-sync-files-deployment-guide.md?tabs=azure-portal%252cproactive-portal#proactively-recall-new-and-changed-files-from-an-azure-file-share) 部分。

- 排除来自云分层的应用程序上一次访问时间跟踪你现在可以从上次访问时间跟踪中排除应用程序。 当应用程序访问文件时，会在云分层数据库中更新文件的上次访问时间。 扫描文件系统的应用程序（如防病毒软件）会导致所有文件具有相同的上次访问时间，这会影响文件的分层。

    若要从上次访问时间跟踪中排除应用程序，请将进程名称添加到 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync 下的 HeatTrackingProcessNameExclusionList 注册表设置。

    示例： reg ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync"/v HeatTrackingProcessNameExclusionList/t REG_MULTI_SZ/d "SampleApp.exe\0AnotherApp.exe"/f

    > [!Note]  
    > 重复数据删除和文件服务器资源管理器 (FSRM) 进程在默认情况下会被排除 (硬编码) 并且进程排除列表每5分钟刷新一次。

- 其他性能和可靠性提升
    - 提高了更改检测性能，以检测在 Azure 文件共享中已更改的文件。
    - 提高了同步上载性能。
    - 初始上传现在是从 VSS 快照执行的，它可减少每项错误和同步会话失败。
    - 同步某些 i/o 模式的可靠性改进。
    - 修复了一个 bug，以防止在发生故障转移时同步数据库在故障转移群集上进行时恢复。
    - 提高了访问分层文件时的召回性能。

### <a name="evaluation-tool"></a>评估工具
在部署 Azure 文件同步之前，应当使用 Azure 文件同步评估工具评估它是否与你的系统兼容。 此工具是一个 Azure PowerShell cmdlet，用于检查文件系统和数据集的潜在问题，例如不受支持的字符或不受支持的 OS 版本。 有关安装和使用情况的说明，请参阅计划指南中的[评估工具](./storage-sync-files-planning.md#evaluation-cmdlet)部分。 

### <a name="agent-installation-and-server-configuration"></a>代理安装和服务器配置
若要详细了解如何使用 Windows Server 安装和配置 Azure 文件同步代理，请参阅[规划 Azure 文件同步部署](storage-sync-files-planning.md)和[如何部署 Azure 文件同步](storage-sync-files-deployment-guide.md)。

- 代理安装包必须使用提升的（管理员）权限进行安装。
- Nano Server 部署选项不支持此代理。
- 只有 Windows Server 2019、Windows Server 2016 和 Windows Server 2012 R2 上支持此代理。
- 代理需要至少 2 GiB 的内存。 如果服务器在启用了动态内存的虚拟机中运行，则至少应当为该 VM 配置 2048 MiB 内存。 有关详细信息，请参阅[推荐使用的系统资源](./storage-sync-files-planning.md#recommended-system-resources)。
- 存储同步代理 (FileSyncSvc) 服务不支持进行了系统卷信息 (SVI) 目录压缩的卷上的服务器终结点。 此配置会导致意外结果。

### <a name="interoperability"></a>互操作性
- 防病毒应用程序、备份应用程序和其他应用程序在访问分层文件时可能会导致不需要的撤回，除非这些应用程序遵从脱机属性，在读取这些文件的内容时跳过。 有关详细信息，请参阅[对 Azure 文件同步进行故障排除](storage-sync-files-troubleshoot.md)。
- 当文件因文件屏蔽而被阻止时，文件服务器资源管理器 (FSRM) 文件屏蔽可能导致无休止的同步故障。
- 不支持在安装了 Azure 文件同步代理的服务器上运行 sysprep，此操作会导致意外结果。 应当在部署服务器映像并完成 sysprep 迷你安装后再安装 Azure 文件同步代理。

### <a name="sync-limitations"></a>同步限制
以下各项不同步，但系统其余部分仍会继续正常运行：
- 包含不受支持字符的文件。 有关不受支持的字符的列表，请参阅[故障排除指南](storage-sync-files-troubleshoot.md#handling-unsupported-characters)。
- 以句点结尾的文件或目录。
- 长度超过 2,048 个字符的路径。
- 安全描述符的系统访问控制列表 (SACL) 部分，用于审核。
- 扩展的属性。
- 备用数据流。
- 重分析点。
- 硬链接。
- 将更改从其他终结点同步到服务器文件时，不会保留压缩（如果在该文件上设置了压缩）。
- 任何使用 EFS（或其他用户模式加密方式）加密的文件，此类加密会阻止服务读取数据。

    > [!Note]  
    > Azure 文件同步始终加密传输中的数据， 而在 Azure 中，数据始终进行静态加密。
 
### <a name="server-endpoint"></a>服务器终结点
- 服务器终结点只能在 NTFS 卷上创建。 Azure 文件同步目前不支持 ReFS、FAT、FAT32 等文件系统。
- 系统卷上不支持云分层。 要在系统卷上创建服务器终结点，请在创建服务器终结点时禁用云分层。
- 故障转移群集仅适用于群集磁盘，而不适用于群集共享卷 (CSV)。
- 服务器终结点不能嵌套， 但可以与另一终结点并行共存于同一卷上。
- 请勿在服务器终结点位置中存储 OS 或应用程序分页文件。
- 如果重命名服务器，则不会更新门户中的服务器名称。

### <a name="cloud-endpoint"></a>云终结点
- Azure 文件同步支持直接对 Azure 文件共享进行更改。 但是，首先需要通过 Azure 文件同步更改检测作业来发现对 Azure 文件共享进行的更改。 每 24 小时针对云终结点启动一次更改检测作业。 若要立即同步 Azure 文件共享中已更改的文件，可使用 [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell cmdlet 手动启动 Azure 文件共享中的更改检测。 此外，通过 REST 协议对 Azure 文件共享所做的更改将不会更新 SMB 上次修改时间，亦不会被视为同步更改。
- 可以将存储同步服务和/或存储帐户移动到其他资源组、订阅或 Azure AD 租户。 移动存储同步服务或存储帐户后，需要为 Storagesync.sys 应用程序授予对存储帐户的访问权限 (参阅 [确保 Azure 文件同步有权访问存储帐户](./storage-sync-files-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)) 。

    > [!Note]  
    > 创建云终结点时，存储同步服务和存储帐户必须位于相同的 Azure AD 租户中。 创建云终结点后，可以将存储同步服务和存储帐户移到不同的 Azure AD 租户。

### <a name="cloud-tiering"></a>云分层
- 如果使用 Robocopy 将分层的文件复制到另一位置，生成的文件不会分层。 可能会对脱机属性进行设置，因为 Robocopy 会在复制操作中错误地包括该属性。
- 使用 robocopy 复制文件时，可使用 /MIR 选项保留文件时间戳。 这将确保较旧的文件比最近访问的文件更早分层。

## <a name="agent-version-10100"></a>代理版本10.1.0。0
以下发行说明适用于2020年6月5日发布的 Azure 文件同步代理的版本10.1.0.0。 除了为版本10.0.0.0 和10.0.2.0 列出的发行说明外，还提供了这些说明。

### <a name="improvements-and-issues-that-are-fixed"></a>改进和已解决的问题

- Azure 专用终结点支持
    - 现在可以将流量同步到存储同步服务的流量发送到专用终结点。 这将允许通过 ExpressRoute 或 VPN 连接建立隧道连接。 若要了解详细信息，请参阅 [配置 Azure 文件同步网络终结点](./storage-sync-files-networking-endpoints.md)。
- 现在，已同步的文件指标将在运行大型同步时显示进度，而不是在结束时显示进度。
- 代理安装、云分层、同步和遥测的其他可靠性改进

## <a name="agent-version-10020"></a>代理版本 10.0.2.0
以下发行说明适用于 2020 年 5 月 19 日发布的 Azure 文件同步代理版本 10.0.2.0。 这些说明是对针对版本 10.0.0.0 列出的发行说明的补充。

此版本中已修复的问题：  
- 存储同步代理 (FileSyncSvc) 在安装 Azure 文件同步 v10 代理后经常崩溃。

> [!Note]  
>此版本不对配置为在新版本可用时自动更新的服务器进行外部测试。 若要安装此更新，请使用 Microsoft 更新或 Microsoft 更新目录（有关安装说明，请参阅 [KB4522412](https://support.microsoft.com/help/4522412)）。

## <a name="agent-version-10000"></a>代理版本 10.0.0.0
以下发行说明适用于 Azure 文件同步代理版本 10.0.0.0（2020 年 4 月 9 日发布）。

### <a name="improvements-and-issues-that-are-fixed"></a>改进和已解决的问题

- 提高了门户中的同步进度
    - 在 V10 代理版本中，Azure 门户即将开始显示运行的同步会话的类型。 例如 初始下载、定期下载、后台召回（快速灾难恢复案例）等等。 

- 改进了云分层门户体验
    - 如果文件无法进行分层或召回，则现在可以在服务器终结点属性中查看分层错误。
    - 其他云分层信息可用于服务器终结点：
        - 本地缓存大小
        - 缓存使用效率
        - 云分层策略详细信息：卷大小、当前可用空间或本地缓存中最早文件的上次访问时间。
    - 这些更改将在 V10 代理初始发布之后不久在 Azure 门户中提供。

- 支持将存储同步服务和/或存储帐户移动到不同的 Azure Active Directory 租户
    - Azure 文件同步目前支持将存储同步服务和/或存储帐户移到其他资源组、订阅或 Azure AD 租户。
    
- 其他性能和可靠性提升
    - 如果在存储帐户上配置了虚拟网络 (VNET) 和防火墙规则，Azure 文件共享上的更改检测可能会失败。
    - 减少了与召回相关的内存消耗。 
    - 提高了使用 [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) cmdlet 时的性能。
    - 其他可靠性提升。 
    
### <a name="evaluation-tool"></a>评估工具
在部署 Azure 文件同步之前，应当使用 Azure 文件同步评估工具评估它是否与你的系统兼容。 此工具是一个 Azure PowerShell cmdlet，用于检查文件系统和数据集的潜在问题，例如不受支持的字符或不受支持的 OS 版本。 有关安装和使用情况的说明，请参阅计划指南中的[评估工具](./storage-sync-files-planning.md#evaluation-cmdlet)部分。 

### <a name="agent-installation-and-server-configuration"></a>代理安装和服务器配置
若要详细了解如何使用 Windows Server 安装和配置 Azure 文件同步代理，请参阅[规划 Azure 文件同步部署](storage-sync-files-planning.md)和[如何部署 Azure 文件同步](storage-sync-files-deployment-guide.md)。

- 代理安装包必须使用提升的（管理员）权限进行安装。
- Nano Server 部署选项不支持此代理。
- 只有 Windows Server 2019、Windows Server 2016 和 Windows Server 2012 R2 上支持此代理。
- 代理需要至少 2 GiB 的内存。 如果服务器在启用了动态内存的虚拟机中运行，则至少应当为该 VM 配置 2048 MiB 内存。
- 存储同步代理 (FileSyncSvc) 服务不支持进行了系统卷信息 (SVI) 目录压缩的卷上的服务器终结点。 此配置会导致意外结果。

### <a name="interoperability"></a>互操作性
- 防病毒应用程序、备份应用程序和其他应用程序在访问分层文件时可能会导致不需要的撤回，除非这些应用程序遵从脱机属性，在读取这些文件的内容时跳过。 有关详细信息，请参阅[对 Azure 文件同步进行故障排除](storage-sync-files-troubleshoot.md)。
- 当文件因文件屏蔽而被阻止时，文件服务器资源管理器 (FSRM) 文件屏蔽可能导致无休止的同步故障。
- 不支持在安装了 Azure 文件同步代理的服务器上运行 sysprep，此操作会导致意外结果。 应当在部署服务器映像并完成 sysprep 迷你安装后再安装 Azure 文件同步代理。

### <a name="sync-limitations"></a>同步限制
以下各项不同步，但系统其余部分仍会继续正常运行：
- 包含不受支持字符的文件。 有关不受支持的字符的列表，请参阅[故障排除指南](storage-sync-files-troubleshoot.md#handling-unsupported-characters)。
- 以句点结尾的文件或目录。
- 长度超过 2,048 个字符的路径。
- 安全描述符的系统访问控制列表 (SACL) 部分，用于审核。
- 扩展的属性。
- 备用数据流。
- 重分析点。
- 硬链接。
- 将更改从其他终结点同步到服务器文件时，不会保留压缩（如果在该文件上设置了压缩）。
- 任何使用 EFS（或其他用户模式加密方式）加密的文件，此类加密会阻止服务读取数据。

    > [!Note]  
    > Azure 文件同步始终加密传输中的数据， 而在 Azure 中，数据始终进行静态加密。
 
### <a name="server-endpoint"></a>服务器终结点
- 服务器终结点只能在 NTFS 卷上创建。 Azure 文件同步目前不支持 ReFS、FAT、FAT32 等文件系统。
- 系统卷上不支持云分层。 要在系统卷上创建服务器终结点，请在创建服务器终结点时禁用云分层。
- 故障转移群集仅适用于群集磁盘，而不适用于群集共享卷 (CSV)。
- 服务器终结点不能嵌套， 但可以与另一终结点并行共存于同一卷上。
- 请勿在服务器终结点位置中存储 OS 或应用程序分页文件。
- 如果重命名服务器，则不会更新门户中的服务器名称。

### <a name="cloud-endpoint"></a>云终结点
- Azure 文件同步支持直接对 Azure 文件共享进行更改。 但是，首先需要通过 Azure 文件同步更改检测作业来发现对 Azure 文件共享进行的更改。 每 24 小时针对云终结点启动一次更改检测作业。 若要立即同步 Azure 文件共享中已更改的文件，可使用 [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell cmdlet 手动启动 Azure 文件共享中的更改检测。 此外，通过 REST 协议对 Azure 文件共享所做的更改将不会更新 SMB 上次修改时间，亦不会被视为同步更改。
- 可以将存储同步服务和/或存储帐户移动到其他资源组、订阅或 Azure AD 租户。 移动存储同步服务或存储帐户后，需要为 Storagesync.sys 应用程序授予对存储帐户的访问权限 (参阅 [确保 Azure 文件同步有权访问存储帐户](./storage-sync-files-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)) 。

    > [!Note]  
    > 创建云终结点时，存储同步服务和存储帐户必须位于相同的 Azure AD 租户中。 创建云终结点后，可以将存储同步服务和存储帐户移到不同的 Azure AD 租户。

### <a name="cloud-tiering"></a>云分层
- 如果使用 Robocopy 将分层的文件复制到另一位置，生成的文件不会分层。 可能会对脱机属性进行设置，因为 Robocopy 会在复制操作中错误地包括该属性。
- 使用 robocopy 复制文件时，可使用 /MIR 选项保留文件时间戳。 这将确保较旧的文件比最近访问的文件更早分层。

## <a name="agent-version-9100"></a>代理版本 9.1.0.0
以下发行说明适用于 2019 年 12 月 12 日发布的 Azure 文件同步代理版本 9.1.0.0。 这些说明是对针对版本 9.0.0.0 列出的发行说明的补充。

此版本中已修复的问题：  
- 升级到 Azure 文件同步代理版本 9.0 后，同步失败，并出现以下某个错误：
    - 0x8e5e044e (JET_errWriteConflict)
    - 0x8e5e0450 (JET_errInvalidSesid)
    - 0x8e5e0442 (JET_errInstanceUnavailable)

## <a name="agent-version-9000"></a>代理版本 9.0.0.0
以下发行说明适用于 Azure 文件同步代理版本 9.0.0.0（2019 年 12 月 2 日发布）。

### <a name="improvements-and-issues-that-are-fixed"></a>改进和已解决的问题

- 自助式还原支持
    - 用户现在还可以使用以前的版本功能，从在卷上启用自助还原功能后创建的 VSS 快照还原分层文件 (以及使用以前版本功能) 。 在 v9.x 版本之前，分层文件不支持以前的版本功能。 必须为每个卷单独启用此功能，此卷上存在启用了云分层的终结点。 若要了解详细信息，请参阅  
[通过早期版本和 VSS（卷影复制服务）进行自助式还原](./storage-sync-files-deployment-guide.md#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service)。 
 
- 支持较大的文件共享大小 
    - Azure 文件同步目前在单个同步命名空间中最多支持 64TiB 和 1 亿个文件。  
 
- 服务器 2019 上的重复数据删除支持 
    -  (，无论是否在 Windows Server 2016 和 Windows Server 2019 上的卷) 上的一个或多个服务器终结点上启用或禁用云分层，现在都支持重复数据删除。 若要在服务器2019上支持云分层的卷上支持重复数据删除，必须安装 Windows update [KB4520062](https://support.microsoft.com/help/4520062) 。 
 
- 改进了要进行分层的文件的最小文件大小 
    - 目前，要进行分层的文件的最小文件大小取决于文件系统群集大小（文件系统群集大小的两倍）。 例如，默认情况下，NTFS 文件系统群集大小为 4KB，生成的要进行分层的文件的最小文件大小为 8KB。 
 
- 网络连接测试 cmdlet 
    - 作为 Azure 文件同步配置的一部分，必须联系多个服务终结点。 每个用户都有自己的 DNS 名称，服务器可以访问这些名称。 这些 URL 也特定于服务器注册到的区域。 服务器注册后，可以使用连接测试 cmdlet（PowerShell 和服务器注册实用工具）来测试与特定于此服务器的所有 URL 之间的通信。 当未完成通信阻止服务器完全使用 Azure 文件同步时，此 cmdlet 可帮助进行故障排除并且可用于微调代理和防火墙配置。  
 
        若要运行网络连接测试，请运行以下 PowerShell 命令： 
 
        Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"  
        Test-StorageSyncNetworkConnectivity
 
- 启用云分层时删除服务器终结点改进 
    - 与之前一样，删除服务器终结点不会删除 Azure 文件共享中的文件。 但是，本地服务器上重新分析点的行为已更改。 现在删除服务器终结点时会删除重新分析点（指向服务器上不是本地文件的指针）。 完全缓存的文件将保留在服务器上。 此改进是为了防止删除服务器终结点时[孤立分层文件](./storage-sync-files-troubleshoot.md?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)。 如果重新创建服务器终结点，则会在服务器上重新创建分层文件的重新分析点。  
 
- 性能和可靠性提升 
    - 减少召回失败。 召回大小现在会根据网络带宽自动调整。 
    - 提高了向同步组添加新服务器时的下载性能。 
    - 减少了因约束冲突而不同步的文件。 
    - 如果服务器终结点路径是卷装入点，则在某些情况下，文件将无法进行分层或意外召回。
    
### <a name="evaluation-tool"></a>评估工具
在部署 Azure 文件同步之前，应当使用 Azure 文件同步评估工具评估它是否与你的系统兼容。 此工具是一个 Azure PowerShell cmdlet，用于检查文件系统和数据集的潜在问题，例如不受支持的字符或不受支持的 OS 版本。 有关安装和使用情况的说明，请参阅计划指南中的[评估工具](./storage-sync-files-planning.md#evaluation-cmdlet)部分。 

### <a name="agent-installation-and-server-configuration"></a>代理安装和服务器配置
若要详细了解如何使用 Windows Server 安装和配置 Azure 文件同步代理，请参阅[规划 Azure 文件同步部署](storage-sync-files-planning.md)和[如何部署 Azure 文件同步](storage-sync-files-deployment-guide.md)。

- 代理安装包必须使用提升的（管理员）权限进行安装。
- Nano Server 部署选项不支持此代理。
- 只有 Windows Server 2019、Windows Server 2016 和 Windows Server 2012 R2 上支持此代理。
- 代理需要至少 2 GiB 的内存。 如果服务器在启用了动态内存的虚拟机中运行，则至少应当为该 VM 配置 2048 MiB 内存。
- 存储同步代理 (FileSyncSvc) 服务不支持进行了系统卷信息 (SVI) 目录压缩的卷上的服务器终结点。 此配置会导致意外结果。

### <a name="interoperability"></a>互操作性
- 防病毒应用程序、备份应用程序和其他应用程序在访问分层文件时可能会导致不需要的撤回，除非这些应用程序遵从脱机属性，在读取这些文件的内容时跳过。 有关详细信息，请参阅[对 Azure 文件同步进行故障排除](storage-sync-files-troubleshoot.md)。
- 当文件因文件屏蔽而被阻止时，文件服务器资源管理器 (FSRM) 文件屏蔽可能导致无休止的同步故障。
- 不支持在安装了 Azure 文件同步代理的服务器上运行 sysprep，此操作会导致意外结果。 应当在部署服务器映像并完成 sysprep 迷你安装后再安装 Azure 文件同步代理。

### <a name="sync-limitations"></a>同步限制
以下各项不同步，但系统其余部分仍会继续正常运行：
- 包含不受支持字符的文件。 有关不受支持的字符的列表，请参阅[故障排除指南](storage-sync-files-troubleshoot.md#handling-unsupported-characters)。
- 以句点结尾的文件或目录。
- 长度超过 2,048 个字符的路径。
- 安全描述符的自定义访问控制列表 (DACL) 部分（如果其大小大于 2 KB）。 （只有在单个项上的访问控制条目 (ACE) 大于某个数（大约为 40）的情况下，这才是一个问题。）
- 安全描述符的系统访问控制列表 (SACL) 部分，用于审核。
- 扩展的属性。
- 备用数据流。
- 重分析点。
- 硬链接。
- 将更改从其他终结点同步到服务器文件时，不会保留压缩（如果在该文件上设置了压缩）。
- 任何使用 EFS（或其他用户模式加密方式）加密的文件，此类加密会阻止服务读取数据。

    > [!Note]  
    > Azure 文件同步始终加密传输中的数据， 而在 Azure 中，数据始终进行静态加密。
 
### <a name="server-endpoint"></a>服务器终结点
- 服务器终结点只能在 NTFS 卷上创建。 Azure 文件同步目前不支持 ReFS、FAT、FAT32 等文件系统。
- 如果没有在删除服务器终结点之前撤回分层的文件，则这些文件会变得不可访问。 若要还原对文件的访问权限，请重新创建服务器终结点。 如果自删除服务器终结点后已过去了 30 天或者如果删除了云终结点，则未撤回的分层文件将不可用。 若要了解详细信息，请参阅[在删除服务器终结点后无法访问服务器上的分层文件](./storage-sync-files-troubleshoot.md?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)。
- 系统卷上不支持云分层。 要在系统卷上创建服务器终结点，请在创建服务器终结点时禁用云分层。
- 故障转移群集仅适用于群集磁盘，而不适用于群集共享卷 (CSV)。
- 服务器终结点不能嵌套， 但可以与另一终结点并行共存于同一卷上。
- 请勿在服务器终结点位置中存储 OS 或应用程序分页文件。
- 如果重命名服务器，则不会更新门户中的服务器名称。

### <a name="cloud-endpoint"></a>云终结点
- Azure 文件同步支持直接对 Azure 文件共享进行更改。 但是，首先需要通过 Azure 文件同步更改检测作业来发现对 Azure 文件共享进行的更改。 每 24 小时针对云终结点启动一次更改检测作业。 若要立即同步 Azure 文件共享中已更改的文件，可使用 [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell cmdlet 手动启动 Azure 文件共享中的更改检测。 此外，通过 REST 协议对 Azure 文件共享所做的更改将不会更新 SMB 上次修改时间，亦不会被视为同步更改。
- 可以将存储同步服务和/或存储帐户移到现有 Azure AD 租户中的其他资源组或订阅。 如果移动了存储帐户，则需要向混合文件同步服务授予对存储帐户的访问权限（请参阅[确保 Azure 文件同步可以访问存储帐户](./storage-sync-files-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)）。

    > [!Note]  
    > Azure 文件同步不支持将订阅移到其他 Azure AD 租户。

### <a name="cloud-tiering"></a>云分层
- 如果使用 Robocopy 将分层的文件复制到另一位置，生成的文件不会分层。 可能会对脱机属性进行设置，因为 Robocopy 会在复制操作中错误地包括该属性。
- 使用 robocopy 复制文件时，可使用 /MIR 选项保留文件时间戳。 这将确保较旧的文件比最近访问的文件更早分层。
- 如果 pagefile.sys 位于启用了云分层的卷上，则文件可能无法分层。 pagefile.sys 应位于禁用云分层的卷上。
