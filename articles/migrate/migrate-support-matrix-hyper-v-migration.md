---
title: Azure Migrate 中的 Hyper-v 迁移支持
description: 了解支持 Azure Migrate 的 Hyper-v 迁移。
author: bsiva
ms.author: bsiva
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 90da16789344754c02d46022160db71ee261a056
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754056"
---
# <a name="support-matrix-for-hyper-v-migration"></a>Hyper-v 迁移的支持矩阵

本文汇总了有关迁移 Hyper-v Vm 与 [Azure Migrate：服务器迁移](migrate-services-overview.md#azure-migrate-server-migration-tool) 的支持设置和限制。 如果正在寻找有关评估要迁移到 Azure 的 Hyper-v Vm 的信息，请查看 [评估支持矩阵](migrate-support-matrix-hyper-v.md)。

## <a name="migration-limitations"></a>迁移限制

对于复制，最多可以选择10个 Vm。 如果要迁移更多计算机，请在10个组中进行复制。


## <a name="hyper-v-host-requirements"></a>Hyper-v 主机要求

| **支持**                | **详细信息**               
| :-------------------       | :------------------- |
| **部署**       | Hyper-v 主机可以是独立的，也可以部署到群集中。 <br/>在 Hyper-v 主机上安装了 (Hyper-v 复制提供程序) Azure Migrate 复制软件。|
| **权限**           | 你需要在 Hyper-v 主机上具有管理员权限。 |
| **主机操作系统** | Windows Server 2019、Windows Server 2016 或 Windows Server 2012 R2 （含最新更新）。 请注意，还支持这些操作系统的服务器核心安装。 |
| **其他软件要求** | .NET Framework 4.7 或更高版本 |
| **端口访问** |  HTTPS 端口443上的出站连接，用于发送 VM 复制数据。
| **可用磁盘空间 (缓存)** |  600 GB |
| **可用磁盘空间（保留磁盘）** |  600 GB |


## <a name="hyper-v-vms"></a>Hyper-V VM

| **支持**                  | **详细信息**               
| :----------------------------- | :------------------- |
| **操作系统** | Azure 支持的所有 [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) 和 [Linux](../virtual-machines/linux/endorsed-distros.md) 操作系统。 |
**Windows Server 2003** | 对于运行 Windows Server 2003 的 Vm，需要在迁移之前 [安装 hyper-v Integration Services](prepare-windows-server-2003-migration.md) 。 | 
**Azure 中的 Linux Vm** | 某些 VM 可能需要经过更改才能在 Azure 中运行。<br/><br/> 对于 Linux，Azure Migrate 会自动对这些操作系统进行更改：<br/> -Red Hat Enterprise Linux 7.8、7.7、7.6、7.5、7.4、7.0、1。x<br/> -O o 7.7，7.6，7.5，7.4，1。x</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -SUSE Linux Enterprise Server 15 SP1 <br/>-Ubuntu 19.04、19.10、14.04 LTS、16.04 LTS、18.04 LTS<br/> -Debian 7、8 <br/> Oracle Linux 7.7、7.7-CI<br/> 对于其他操作系统，请手动进行 [所需的更改](prepare-for-migration.md#verify-required-changes-before-migrating) 。
| **Azure 所需的更改** | 某些 VM 可能需要经过更改才能在 Azure 中运行。 在迁移之前手动进行调整。 相关文章包含有关如何执行此操作的说明。 |
| **Linux 启动**                 | 如果/boot 位于专用分区上，则它应驻留在 OS 磁盘上，而不会分布在多个磁盘上。<br/> 如果/boot 是根 (/) 分区的一部分，则 "/" 分区应在 OS 磁盘上，而不是在其他磁盘上。 |
| **UEFI 启动**                  | 支持。 基于 UEFI 的 Vm 将迁移到 Azure 第2代 Vm。  |
| **UEFI-安全启动**         | 不支持迁移。|
| **磁盘大小**                  | 对于 OS 磁盘，为 2 TB (BIOS 启动) ，操作系统磁盘为 4 TB (UEFI 启动) ，4 TB 用于数据磁盘。|
| **磁盘编号** | 每个 VM 最多16个磁盘。|
| **加密磁盘/卷**    | 不支持迁移。|
| **RDM/传递磁盘**      | 不支持迁移。|
| **共享磁盘** | 使用共享磁盘的 Vm 不支持迁移。|
| **NFS**                        | 不会复制装载为 Vm 上的卷的 NFS 卷。|
| **/ISCSI**                      | 具有 iSCSI 目标的 Vm 不支持迁移。
| **目标磁盘**                | 仅可将 Azure Vm 迁移到托管磁盘。 |
| **IPv6** | 不支持。|
| **NIC 组合** | 不支持。|
| **Azure Site Recovery** | 如果 VM 启用了与 Azure Site Recovery 的复制，则无法使用 Azure Migrate Server 迁移进行复制。|
| **端口** | HTTPS 端口443上的出站连接，用于发送 VM 复制数据。|

### <a name="url-access-public-cloud"></a> (公有云) 的 URL 访问

Hyper-v 主机上的复制提供程序软件将需要对这些 Url 的访问权限。

**URL** | **详细信息**
--- | ---
login.microsoftonline.com | 使用 Active Directory 进行访问控制和标识管理。
backup.windowsazure.com | 复制数据传输和协调。
*.hypervrecoverymanager.windowsazure.com | 用于复制管理。
*.blob.core.windows.net | 将数据上传到存储帐户。 
dc.services.visualstudio.com | 上传用于内部监视的应用日志。
time.windows.com | 验证系统与全局时间之间的时间同步。

### <a name="url-access-azure-government"></a>Azure 政府) 的 URL 访问 (

Hyper-v 主机上的复制提供程序软件将需要对这些 Url 的访问权限。

**URL** | **详细信息**
--- | ---
login.microsoftonline.us | 使用 Active Directory 进行访问控制和标识管理。
backup.windowsazure.us | 复制数据传输和协调。
*.hypervrecoverymanager.windowsazure.us | 用于复制管理。
*.blob.core.usgovcloudapi.net | 将数据上传到存储帐户。
dc.services.visualstudio.com | 上传用于内部监视的应用日志。
time.nist.gov | 验证系统与全局时间之间的时间同步。

## <a name="azure-vm-requirements"></a>Azure VM 要求

复制到 Azure 的所有本地 Vm 必须满足此表中汇总的 Azure VM 要求。

**组件** | **要求** | **详细信息**
--- | --- | ---
操作系统磁盘大小 | 最大 2,048 GB。 | 如果不支持，检查会失败。
操作系统磁盘计数 | 1 | 如果不支持，检查会失败。
数据磁盘计数 | 16或更少。 | 如果不支持，检查会失败。
数据磁盘大小 | 最大 4,095 GB | 如果不支持，检查会失败。
网络适配器 | 支持多个适配器。 |
共享 VHD | 不支持。 | 如果不支持，检查会失败。
FC 磁盘 | 不支持。 | 如果不支持，检查会失败。
BitLocker | 不支持。 | 为计算机启用复制之前，必须先禁用 BitLocker。
VM 名称 | 1 到 63 个字符。<br/> 限制为字母、数字和连字符。<br/><br/> 计算机名称必须以字母或数字开头和结尾。 |  请在 Site Recovery 中的计算机属性中更新该值。
迁移后连接-Windows | 若要在迁移后连接到运行 Windows 的 Azure Vm：<br/><br/> -迁移之前，请在本地 VM 上启用 RDP。 请确保为“公共”配置文件添加了 TCP 和 UDP 规则，并确保在“Windows 防火墙” > “允许的应用”中针对所有配置文件允许 RDP  。<br/><br/> -对于站点到站点 VPN 访问，请启用 rdp，并允许 **Windows 防火墙** 中  ->  的 rdp 允许 **域和专用** 网络的 **应用和功能**。 此外，请检查操作系统的 SAN 策略是否设置为 **OnlineAll**。 [了解详细信息](prepare-for-migration.md)。 |
迁移后连接-Linux | 使用 SSH 迁移后连接到 Azure Vm：<br/><br/> -迁移之前，请在本地计算机上检查安全外壳服务是否设置为 "启动"，以及防火墙规则是否允许 SSH 连接。<br/><br/> -迁移后，在 Azure VM 上，允许到 SSH 端口的传入连接连接到已故障转移的 VM 上的网络安全组规则，以及连接到的 Azure 子网。 此外，为 VM 添加公共 IP 地址。 |  

## <a name="next-steps"></a>后续步骤

迁移用于迁移的[Hyper-v vm](tutorial-migrate-hyper-v.md) 。
