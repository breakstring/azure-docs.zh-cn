---
title: 使用 Azure Site Recovery 进行 Azure VM 灾难恢复的支持矩阵
description: 汇总了使用 Azure Site Recovery 将 Azure VM 灾难恢复到次要区域时的支持情况。
ms.topic: article
ms.date: 11/29/2020
ms.author: raynew
ms.openlocfilehash: 78c27292a92152946ba33258d27940e3c1aea47d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100391569"
---
# <a name="support-matrix-for-azure-vm-disaster-recovery-between-azure-regions"></a>在 Azure 区域之间进行 Azure VM 灾难恢复的支持矩阵

本文汇总了使用 [Azure Site Recovery](site-recovery-overview.md) 服务在不同的 Azure 区域之间对 Azure VM 进行灾难恢复时的支持和先决条件。


## <a name="deployment-method-support"></a>部署方法支持

**部署** |  **支持**
--- | ---
**Azure 门户** | 支持。
**PowerShell** | 支持。 [了解详细信息](azure-to-azure-powershell.md)
**REST API** | 支持。
**CLI** | 目前不支持


## <a name="resource-support"></a>资源支持

**资源操作** | **详细信息**
--- | ---
**跨资源组移动保管库** | 不支持
**跨资源组移动计算/存储/网络资源** | 不支持。<br/><br/> 如果在 VM 复制后移动 VM 或相关组件（如存储/网络），则需要禁用并重新启用 VM 的复制。
**将 Azure VM 从一个订阅复制到另一个订阅以进行灾难恢复** | 在同一 Azure Active Directory 租户中受支持。
**在受支持的地理群集内跨区域迁移虚拟机（订阅内和跨订阅）** | 在同一 Azure Active Directory 租户中受支持。
**在同一区域内迁移 VM** | 不支持。
**Azure 专用主机** | 不支持。

## <a name="region-support"></a>区域支持

可以在同一地理群集中的任意两个区域之间复制和恢复 VM。 在定义地理群集时请注意数据延迟和主权。


**地理群集** | **Azure 区域**
-- | --
美洲 | 加拿大东部、加拿大中部、美国中南部、美国中西部、美国东部、美国东部 2、美国西部、美国西部 2、美国中部、美国中北部
欧洲 | 英国西部、英国南部、北欧、西欧、南非西部、南非北部、挪威东部、法国中部、瑞士北部、德国中西部
亚洲 | 印度南部、印度中部、印度西部、东南亚、东亚、日本东部、日本西部、韩国中部、韩国南部
澳大利亚    | 澳大利亚东部、澳大利亚东南部、澳大利亚中部、澳大利亚中部 2
Azure Government    | US Gov 弗吉尼亚州、US Gov 爱荷华州、US Gov 亚利桑那州、US Gov 德克萨斯州、US DOD 东部、US DOD 中部
德国    | 德国中部、德国东北部
中国 | 中国东部、中国北部、中国北部 2、中国东部 2
受限区域是为国家/地区内部的灾难恢复保留的 |为瑞士北部瑞士西部保留，法国南部保留用于法国中部，阿拉伯联合酋长国中部限制为阿拉伯联合酋长国北部客户，挪威西部用于挪威东部客户

>[!NOTE]
>
> - 对于 **巴西南部**，可以复制和故障转移到以下区域：美国中南部、美国中西部、美国东部、美国东部 2、美国西部、美国西部 2 和美国中北部。
> - 巴西南部只能用作可从中使用 Site Recovery 复制 VM 的源区域。 它不能充当目标区域。 这是因为地理距离导致的延迟问题。 请注意，如果从用作源区域的巴西南部故障转移到目标，则支持从目标区域故障回复到巴西南部。
> - 可以在你对其拥有相应访问权限的区域中操作。
> - 如果你要在其中创建保管库的区域未显示，请确保你的订阅有权在该区域中创建资源。
> - 如果在启用复制时无法在地理群集中看到某个区域，请确保你的订阅有权在该区域中创建 VM。



## <a name="cache-storage"></a>缓存存储

此表汇总了对复制期间 Site Recovery 使用的缓存存储帐户的支持。

**设置** | **支持** | **详细信息**
--- | --- | ---
常规用途 V2 存储帐户（热存储层和冷存储层） | 支持 | 建议不要使用 GPv2，因为 V2 的事务成本远高于 V1 存储帐户。
高级存储 | 不支持 | 标准存储帐户用于缓存存储，有助于优化成本。
虚拟网络的 Azure 存储防火墙  | 支持 | 如果你使用的是启用了防火墙的缓存存储帐户或目标存储帐户，请确保“[允许受信任的 Microsoft 服务](../storage/common/storage-network-security.md#exceptions)”。<br></br>同时，确保允许访问至少一个源 Vnet 子网。


## <a name="replicated-machine-operating-systems"></a>复制的计算机操作系统

Site Recovery 支持复制那些运行本节中所列操作系统的 Azure VM。 请注意，如果已复制的计算机随后 (升级或降级) 到其他主要内核，则需要禁用复制，并在升级后重新启用复制。

### <a name="windows"></a>Windows


**操作系统** | **详细信息**
--- | ---
Windows Server 2019 | 支持 Server Core、带桌面体验的服务器。
Windows Server 2016  | 支持 Server Core、带桌面体验的服务器。
Windows Server 2012 R2 | 。
Windows Server 2012 | 。
Windows Server 2008 R2 SP1/SP2 | 。<br/><br/> 从适用于 Azure VM 的移动服务扩展版本 [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) 开始，需要在运行 Windows Server 2008 R2 SP1/SP2 的计算机上安装 Windows [服务堆栈更新 (SSU)](https://support.microsoft.com/help/4490628) 和 [SHA-2 更新](https://support.microsoft.com/help/4474419)。  从 2019 年 9 月开始不再支持 SHA-1。如果未启用 SHA-2 代码签名，则无法按预期方式安装/升级代理扩展。 详细了解 [SHA-2 升级和要求](https://aka.ms/SHA-2KB)。
Windows 10 (x64) | 支持。
Windows 8.1 (x64) | 支持。
Windows 8 (x64) | 支持。
Windows 7 (x64) SP1 和更高版本 | 从适用于 Azure VM 的移动服务扩展版本 [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) 开始，需要在运行 Windows 7 SP1 的计算机上安装 Windows [服务堆栈更新 (SSU)](https://support.microsoft.com/help/4490628) 和 [SHA-2 更新](https://support.microsoft.com/help/4474419)。  从 2019 年 9 月开始不再支持 SHA-1。如果未启用 SHA-2 代码签名，则无法按预期方式安装/升级代理扩展。 详细了解 [SHA-2 升级和要求](https://aka.ms/SHA-2KB)。



#### <a name="linux"></a>Linux

**操作系统** | **详细信息**
--- | ---
Red Hat Enterprise Linux | 6.7、6.8、6.9、6.10、7.0、7.1、7.2、7.3、7.4、7.5、7.6、[7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery)、[7.8](https://support.microsoft.com/help/4564347/)、[7.9](https://support.microsoft.com/help/4578241/)、[8.0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery)、8.1、[8.2](https://support.microsoft.com/help/4570609/)、[8.3](https://support.microsoft.com/help/4597409/)
CentOS | 6.5、6.6、6.7、6.8、6.9、6.10 </br> 9.37 热修复程序**支持 7.0、7.1、7.2、7.3、7.4、7.5、7.6、7.7、[7.8](https://support.microsoft.com/help/4564347/)、[7.9 预正式发布版](https://support.microsoft.com/help/4578241/)、7.9 正式发布版 </br> 8.0、8.1、[8.2](https://support.microsoft.com/en-us/help/4570609)、[8.3](https://support.microsoft.com/help/4597409/)
Ubuntu 14.04 LTS Server | 支持所有 14.04.x 版本；[支持的内核版本](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)； 
Ubuntu 16.04 LTS Server | 支持所有 16.04.x 版本；[支持的内核版本](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> 使用基于密码的身份验证和登录的 Ubuntu 服务器以及用于配置云 VM 的 cloud-init 包可能会在故障转移时禁用基于密码的登录（具体取决于 cloudinit 配置）。 通过从“支持”>“故障排除”>“设置”菜单（位于 Azure 门户的故障转移 VM 中）重置密码，可以在虚拟机上重新启用基于密码的登录。
Ubuntu 18.04 LTS Server | 支持所有 18.04.x 版本；[支持的内核版本](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> 使用基于密码的身份验证和登录的 Ubuntu 服务器以及用于配置云 VM 的 cloud-init 包可能会在故障转移时禁用基于密码的登录（具体取决于 cloudinit 配置）。 通过从“支持”>“故障排除”>“设置”菜单（位于 Azure 门户的故障转移 VM 中）重置密码，可以在虚拟机上重新启用基于密码的登录。
Ubuntu 20.04 LTS Server | 支持所有 20.04.x 版本；[支持的内核版本](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Debian 7 | 包括对所有 7. x 版本的支持 [受支持的内核版本](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | 包括对所有 8. x 版本的支持 [受支持的内核版本](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 9 | 支持 Debian 9.1 到 9.13。 不支持 Debian 9.0。 [受支持的内核版本](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 10 | [受支持的内核版本](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1、SP2、SP3、SP4、SP5[（受支持的内核版本）](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 15 | 15、SP1、SP2[（受支持的内核版本）](#supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3<br/><br/> 不支持将复制计算机从 SP3 升级到 SP4。 如果已升级复制的计算机，则需要禁用复制并在升级后重新启用复制。
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6.4、6.5、6.6、6.7、6.8、6.9、6.10、7.0、7.1、7.2、7.3、 [7.4、7.5](https://support.microsoft.com/help/4573888/)、7.6、 [7.7、7.8](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery)、 [7.9](https://support.microsoft.com/help/4597409)、 [8.0](https://support.microsoft.com/help/4573888/)、 [8.1 (运行](https://support.microsoft.com/help/4573888/)Red Hat 兼容内核或 UNBREAKABLE Enterprise 内核 Release 3、4 & 5 (UEK3、UEK4、UEK5) <br/><br/>8.1 (在所有 UEK 内核和 RedHat <内核上运行， [9.35](https://support.microsoft.com/help/4573888/)上支持 3.10.0-1062。 [9.36](https://support.microsoft.com/help/4578241/)) 提供对其余 RedHat 内核的支持

> [!NOTE]
> 对于 Linux 版本，Azure Site Recovery 不支持自定义的操作系统映像。 仅支持属于次要分发版本/更新的原版内核。

**注意：为支持 15 天内发布的最新 Linux 内核，Azure Site Recovery 推出了基于最新移动代理版本的热修补程序。 此修补程序是在两个主要版本之间推出的。 若要更新到移动代理的最新版本（包括热修补程序），请按照[本文](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure)中提及的步骤进行操作。 此修补程序当前是针对 Azure 到 Azure DR 方案中使用的移动代理推出的。

#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Azure 虚拟机支持的 Ubuntu 内核版本

**版本** | **移动服务版本** | **内核版本** |
--- | --- | --- |
14.04 LTS | [9.36](https://support.microsoft.com/help/4578241/)、 [9.37](https://support.microsoft.com/help/4582666/)、 [9.38](https://support.microsoft.com/help/4590304/)、 [9.39](https://support.microsoft.com/help/4597409/)、 [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)| 3.13.0-24-generic 到 3.13.0-170-generic、<br/>3.16.0-25-generic 到 3.16.0-77-generic、<br/>3.19.0-18-generic 到 3.19.0-80-generic、<br/>4.2.0-18-generic 到 4.2.0-42-generic、<br/>4.4.0-21-generic 到 4.4.0-148-generic、<br/>4.15.0-1023-azure 到 4.15.0-1045-azure |
|||
16.04 LTS | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.4.0-21-泛型到 4.4.0-泛型，<br/>4.8.0-34-generic 到 4.8.0-58-generic、<br/>4.10.0-14-generic 到 4.10.0-42-generic、<br/>4.11.0-13-generic 到 4.11.0-14-generic、<br/>4.13.0-16-generic 到 4.13.0-45-generic、<br/>4.15.0-13-泛型到 4.15.0-128-泛型<br/>4.11.0-1009-azure 到 4.11.0-1016-azure、<br/>4.13.0-1005-azure 到 4.13.0-1018-azure <br/>4.15.0-1012-azure 到 4.15.0-1102-azure </br> 4.15.0-132-泛型，4.4.0-泛型到9.40 热修复修补程序 * *|
16.04 LTS | [9.39](https://support.microsoft.com/help/4597409/) | 4.4.0-21-generic 到 4.4.0-194-generic、<br/>4.8.0-34-generic 到 4.8.0-58-generic、<br/>4.10.0-14-generic 到 4.10.0-42-generic、<br/>4.11.0-13-generic 到 4.11.0-14-generic、<br/>4.13.0-16-generic 到 4.13.0-45-generic、<br/>4.15.0-13-generic 到 4.15.0-123-generic<br/>4.11.0-1009-azure 到 4.11.0-1016-azure、<br/>4.13.0-1005-azure 到 4.13.0-1018-azure <br/>4.15.0-1012-azure 到 4.15.0-1098-azure </br> 4.4.0-197-generic、4.15.0-126-generic、4.15.0-128-generic、4.15.0-1100-azure、4.15.0-1102-azure 到 9.39 热修补程序**|
16.04 LTS | [9.38](https://support.microsoft.com/help/4590304/) | 4.4.0-21-generic 到 4.4.0-190-generic，<br/>4.8.0-34-generic 到 4.8.0-58-generic、<br/>4.10.0-14-generic 到 4.10.0-42-generic、<br/>4.11.0-13-generic 到 4.11.0-14-generic、<br/>4.13.0-16-generic 到 4.13.0-45-generic、<br/>4.15.0-13-generic 到 4.15.0-118-generic<br/>4.11.0-1009-azure 到 4.11.0-1016-azure、<br/>4.13.0-1005-azure 到 4.13.0-1018-azure <br/>4.15.0-1012-azure 到 4.15.0-1096-azure </br> 4.4.0-193-generic、4.15.0-120-generic、4.15.0-122-generic 和 4.15.0-1098-azure 到 9.38 热修补程序**|
16.04 LTS | [9.37](https://support.microsoft.com/help/4582666/) | 4.4.0-21-generic 到 4.4.0-189-generic、<br/>4.8.0-34-generic 到 4.8.0-58-generic、<br/>4.10.0-14-generic 到 4.10.0-42-generic、<br/>4.11.0-13-generic 到 4.11.0-14-generic、<br/>4.13.0-16-generic 到 4.13.0-45-generic、<br/>4.15.0-13-generic 到 4.15.0-115-generic<br/>4.11.0-1009-azure 到 4.11.0-1016-azure、<br/>4.13.0-1005-azure 到 4.13.0-1018-azure <br/>4.15.0-1012-azure 到 4.15.0-1093-azure </br> 4.4.0-190-generic、4.15.0-117-generic、4.15.0-118-generic、4.15.0-1095-azure、4.15.0-1096-azure 到 9.37 热修补程序**|
16.04 LTS | [9.36](https://support.microsoft.com/help/4578241/)| 4.4.0-21-generic 到 4.4.0-187-generic、<br/>4.8.0-34-generic 到 4.8.0-58-generic、<br/>4.10.0-14-generic 到 4.10.0-42-generic、<br/>4.11.0-13-generic 到 4.11.0-14-generic、<br/>4.13.0-16-generic 到 4.13.0-45-generic、<br/>4.15.0-13-generic 到 4.15.0-112-generic<br/>4.11.0-1009-azure 到 4.11.0-1016-azure、<br/>4.13.0-1005-azure 到 4.13.0-1018-azure <br/>4.15.0-1012-azure 到 4.15.0-1092-azure |
|||
18.04 LTS | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.15.0-20-泛型到 4.15.0-129-泛型 </br> 4.18.0-13-generic 到 4.18.0-25-generic </br> 5.0.0-15-generic 到 5.0.0-63-generic </br> 5.3.0-19-generic 到 5.3.0-69-generic </br> 5.4.0--一般到 5.4.0-59-泛型</br> 4.15.0-1009-azure 到 4.15.0-azure </br> 4.18.0-1006-azure 到 4.18.0-1025-azure </br> 5.0.0-1012-azure 到 5.0.0-1036-azure </br> 5.3.0-1007-azure 到 5.3.0-1035-azure </br> 5.4.0-1020-azure 到 5.4.0--azure </br> 4.15.0-1104-azure，4.15.0-130-generic，4.15.0-132-泛型，5.4.0-1036-azure，5.4.0-60 9.40--|
18.04 LTS | [9.39](https://support.microsoft.com/help/4597409/) | 4.15.0-20-generic 到 4.15.0-123-generic </br> 4.18.0-13-generic 到 4.18.0-25-generic </br> 5.0.0-15-generic 到 5.0.0-63-generic </br> 5.3.0-19-generic 到 5.3.0-69-generic </br> 5.4.0-37-generic 到 5.4.0-53-generic</br> 4.15.0-1009-azure 到 4.15.0-1099-azure </br> 4.18.0-1006-azure 到 4.18.0-1025-azure </br> 5.0.0-1012-azure 到 5.0.0-1036-azure </br> 5.3.0-1007-azure 到 5.3.0-1035-azure </br> 5.4.0-1020-azure 到 5.4.0-1031-azure </br> 4.15.0-124-generic、5.4.0-54-generic、5.4.0-1032-azure、5.4.0-56-generic、4.15.0-1100-azure、4.15.0-126-generic、4.15.0-128-generic、5.4.0-58-generic、4.15.0-1102-azure、5.4.0-1034-azure 到 9.39 热修补程序**|
18.04 LTS | [9.38](https://support.microsoft.com/help/4590304/) | 4.15.0-20-generic 到 4.15.0-118-generic </br> 4.18.0-13-generic 到 4.18.0-25-generic </br> 5.0.0-15-generic 到 5.0.0-61-generic </br> 5.3.0-19-generic 到 5.3.0-67-generic </br> 5.4.0-37-generic 到 5.4.0-48-generic</br> 4.15.0-1009-azure 到 4.15.0-1096-azure </br> 4.18.0-1006-azure 到 4.18.0-1025-azure </br> 5.0.0-1012-azure 到 5.0.0-1036-azure </br> 5.3.0-1007-azure 到 5.3.0-1035-azure </br> 5.4.0-1020-azure 到 5.4.0-1026-azure </br> 4.15.0-121-generic、4.15.0-122-generic、5.0.0-62-generic、5.3.0-68-generic、5.4.0-51-generic、5.4.0-52-generic、4.15.0-1099-azure、5.4.0-1031-azure 到 9.38 热修补程序**|
18.04 LTS | [9.37](https://support.microsoft.com/help/4582666/) | 4.15.0-20-generic 到 4.15.0-115-generic </br> 4.18.0-13-generic 到 4.18.0-25-generic </br> 5.0.0-15-generic 到 5.0.0-60-generic </br> 5.3.0-19-generic 到 5.3.0-66-generic </br> 5.4.0-37-generic 到 5.4.0-45-generic</br> 4.15.0-1009-azure 到 4.15.0-1093-azure </br> 4.18.0-1006-azure 到 4.18.0-1025-azure </br> 5.0.0-1012-azure 到 5.0.0-1036-azure </br> 5.3.0-1007-azure 到 5.3.0-1035-azure </br> 5.4.0-1020-azure 到 5.4.0-1023-azure</br> 4.15.0-117-generic、4.15.0-118-generic、5.0.0-61-generic、5.3.0-67-generic、5.4.0-47-generic、5.4.0-48-generic、4.15.0-1095-azure、4.15.0-1096-azure、5.4.0-1025-azure、5.4.0-1026-azure 到 9.37 热修补程序**|
18.04 LTS | [9.36](https://support.microsoft.com/help/4578241/) | 4.15.0-20-generic 到 4.15.0-112-generic </br> 4.18.0-13-generic 到 4.18.0-25-generic </br> 5.0.0-15-generic 到 5.0.0-58-generic </br> 5.3.0-19-generic 到 5.3.0-65-generic </br> 5.4.0-37-generic 到 5.4.0-42-generic</br> 4.15.0-1009-azure 到 4.15.0-1092-azure </br> 4.18.0-1006-azure 到 4.18.0-1025-azure </br> 5.0.0-1012-azure 到 5.0.0-1036-azure </br> 5.3.0-1007-azure 到 5.3.0-1032-azure </br> 5.4.0-1020-azure 到 5.4.0-1022-azure </br> 5.0.0-60-generic 和 5.3.0-1035-azure 到 9.36 热修补程序**|
|||
20.04 LTS |[9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)| 5.4.0-26-泛型到 5.4.0-59 </br> -generic 5.4.0-5.4.0-azure 到-azure </br> 5.8.0-29-泛型到 5.8.0-34-泛型 </br> 5.4.0-1036-azure，5.4.0-60-泛型，5.4.0-62-泛型，5.8.0-36-泛型，5.8.0-38-泛型到9.40 热修复修补程序 * *|
20.04 LTS |[9.39](https://support.microsoft.com/help/4597409/) | 5.4.0-26-generic 到 5.4.0-53 </br> -generic 5.4.0-1010-azure 到 5.4.0-1031-azure </br> 5.4.0-54-generic、5.8.0-29-generic、5.4.0-1032-azure、5.4.0-56-generic、5.8.0-31-generic、5.8.0-33-generic、5.4.0-58-generic、5.4.0-1034-azure 到 9.39 热修补程序**
20.04 LTS |[9.39](https://support.microsoft.com/help/4597409/) | 5.4.0-26-generic 到 5.4.0-53 </br> -generic 5.4.0-1010-azure 到 5.4.0-1031-azure </br> 5.4.0-54-generic、5.8.0-29-generic、5.4.0-1032-azure、5.4.0-56-generic、5.8.0-31-generic、5.8.0-33-generic、5.4.0-58-generic、5.4.0-1034-azure 到 9.39 热修补程序**
20.04 LTS |[9.38](https://support.microsoft.com/help/4590304/) | 5.4.0-26-generic 到 5.4.0-48 </br> -generic 5.4.0-1010-azure 到 5.4.0-1026-azure </br> 5.4.0-51-generic、5.4.0-52-generic、5.8.0-23-generic、5.8.0-25-generic、5.4.0-1031-azure 到 9.38 热修补程序**
20.04 LTS |[9.37](https://support.microsoft.com/help/4582666/) | 5.4.0-26-generic 到 5.4.0-45 </br> -generic 5.4.0-1010-azure 到 5.4.0-1023-azure </br> 5.4.0-47-generic、5.4.0-48-generic 和 5.4.0-1025-azure 5.4.0-1026-azure 到 9.37 热修补程序**
20.04 LTS |[9.36](https://support.microsoft.com/help/4578241/) | 5.4.0-26-generic 到 5.4.0-42 </br> -generic 5.4.0-1010-azure 到 5.4.0-1022-azure

**注意：为支持 15 天内发布的最新 Linux 内核，Azure Site Recovery 推出了基于最新移动代理版本的热修补程序。 此修补程序是在两个主要版本之间推出的。 若要更新到移动代理的最新版本（包括热修补程序），请按照[本文](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure)中提及的步骤进行操作。 此修补程序当前是针对 Azure 到 Azure DR 方案中使用的移动代理推出的。

#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Azure 虚拟机支持的 Debian 内核版本

**版本** | **移动服务版本** | **内核版本** |
--- | --- | --- |
Debian 7 | [9.36](https://support.microsoft.com/help/4578241/)、 [9.37](https://support.microsoft.com/help/4582666/)、 [9.38](https://support.microsoft.com/help/4590304/)、 [9.39](https://support.microsoft.com/help/4597409/)、 [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 3.2.0-4-amd64 到 3.2.0-6-amd64、3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.36](https://support.microsoft.com/help/4578241/)、 [9.37](https://support.microsoft.com/help/4582666/)、 [9.38](https://support.microsoft.com/help/4590304/)、 [9.39](https://support.microsoft.com/help/4597409/)、 [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 3.16.0-4-amd64 到 3.16.0-11-amd64、4.9.0-0.bpo.4-amd64 到 4.9.0-0.bpo.11-amd64 |
|||
Debian 9.1 | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.9.0-1-amd64 到 4.9.0-14-amd64 </br> 4.19.0 旁 1/-0-amd64 到4.19.0 旁 1/-0. bpo </br> 4.19.0 旁 1/-0. bpo-amd64 到4.19.0 旁 1/-0. bpo-amd64 </br> 
Debian 9.1 | [9.39](https://support.microsoft.com/help/4597409/) | 4.9.0-1-amd64 到 4.9.0-14-amd64 </br> 4.19.0-0.bpo.1-amd64 到 4.19.0-0.bpo.12-amd64 </br> 4.19.0-0.bpo.1-cloud-amd64 到 4.19.0-0.bpo.12-cloud-amd64 </br> 4.19.0 旁 1/-0. 13-amd64，4.19.0 旁 1/-0. bpo-amd64 到9.39 热修复修补程序 * *</br> 
Debian 9.1 | [9.38](https://support.microsoft.com/help/4590304/) | 4.9.0-1-amd64 到 4.9.0-13-amd64 </br> 4.19.0-0.bpo.1-amd64 到 4.19.0-0.bpo.11-amd64 </br> 4.19.0-0.bpo.1-cloud-amd64 到 4.19.0-0.bpo.11-cloud-amd64 </br> 4.9.0-14-amd64，4.19.0 旁 1/-0-amd64，4.19.0 旁 1/-0. bpo-amd64 到9.38 热修复修补程序 * *
Debian 9.1 | [9.37](https://support.microsoft.com/help/4582666/) | 4.9.0-3-amd64 到 4.9.0-13-amd64、4.19.0-0.bpo.6-amd64 到 4.19.0-0.bpo.10-amd64、4.19.0-0.bpo.6-cloud-amd64 到 4.19.0-0.bpo.10-cloud-amd64
|||
Debian 10 | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.19.0-5-amd64 到 4.19.0-13-amd64 </br> 4.19.0--4.19.0--13--i </br> 5.8.0 旁 1/-0. 2-amd64 </br> 5.8.0 旁 1/-0. 2-amd64

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Azure 虚拟机支持的 SUSE Linux Enterprise Server 12 内核版本

**版本** | **移动服务版本** | **内核版本** |
--- | --- | --- |
SUSE Linux Enterprise Server 12（SP1、SP2、SP3、SP4、SP5） | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 所有 [股票 SUSE 12 SP1、SP2、SP3、SP4、SP5 内核](https://www.suse.com/support/kb/doc/?id=000019587) 都受支持。</br></br> 4.4.138-4.7-azure 到 4.4.180-4.31-azure、</br>4.12.14-6.3-azure 到 4.12.14-6.43-azure </br> 4.12.14-16.7-azure 到 4.12.14-16.38-azure </br> 4.12.14-16.41-azure 到9.40 热修复修补程序 * *|
SUSE Linux Enterprise Server 12（SP1、SP2、SP3、SP4、SP5） | [9.39](https://support.microsoft.com/help/4597409/) | 所有 [股票 SUSE 12 SP1、SP2、SP3、SP4、SP5 内核](https://www.suse.com/support/kb/doc/?id=000019587) 都受支持。</br></br> 4.4.138-4.7-azure 到 4.4.180-4.31-azure、</br>4.12.14-6.3-azure 到 4.12.14-6.43-azure </br> 4.12.14-16.7-azure 到 4.12.14-16.34-azure </br> 4.12.14-16.38-azure 到 9.39 热修补程序**|
SUSE Linux Enterprise Server 12（SP1、SP2、SP3、SP4、SP5） | [9.38](https://support.microsoft.com/help/4590304/) | 所有 [股票 SUSE 12 SP1、SP2、SP3、SP4、SP5 内核](https://www.suse.com/support/kb/doc/?id=000019587) 都受支持。</br></br> 4.4.138-4.7-azure 到 4.4.180-4.31-azure、</br>4.12.14-6.3-azure 到 4.12.14-6.43-azure </br> 4.12.14-16.7-azure 到 4.12.14-16.28-azure |
SUSE Linux Enterprise Server 12（SP1、SP2、SP3、SP4、SP5） | [9.36](https://support.microsoft.com/help/4578241/)、[9.37](https://support.microsoft.com/help/4582666/) | 所有 [股票 SUSE 12 SP1、SP2、SP3、SP4、SP5 内核](https://www.suse.com/support/kb/doc/?id=000019587) 都受支持。</br></br> 4.4.138-4.7-azure 到 4.4.180-4.31-azure、</br>4.12.14-6.3-azure 到 4.12.14-6.43-azure </br> 4.12.14-16.7-azure 到 4.12.14-16.22-azure </br> 4.12.14-16.25-azure、4.12.14-16.28-azure 到 9.37 热修补程序**|

#### <a name="supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines"></a>Azure 虚拟机支持的 SUSE Linux Enterprise Server 15 内核版本

**版本** | **移动服务版本** | **内核版本** |
--- | --- | --- |
SUSE Linux Enterprise Server 15、SP1、SP2 | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)  | 默认情况下，所有 [股票 SUSE 15、SP1、SP2 内核](https://www.suse.com/support/kb/doc/?id=000019587) 都受支持。</br></br> 4.12.14-5.5-azure 到 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure 到 4.12.14-8.55-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure 到 5.3.18-18.29-azure </br> 5.3.18-18.32-azure、4.12.14-8.58-azure 到9.40 热修补程序 * *
SUSE Linux Enterprise Server 15、SP1、SP2 | [9.39](https://support.microsoft.com/help/4597409/)  | 默认情况下，所有 [股票 SUSE 15、SP1、SP2 内核](https://www.suse.com/support/kb/doc/?id=000019587) 都受支持。</br></br> 4.12.14-5.5-azure 到 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure 到 4.12.14-8.47-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure 到 5.3.18-18.21-azure </br> 4.12.14-8.52-azure、5.3.18-18.24-azure、4.12.14-8.55-azure、5.3.18-18.29-azure 到 9.39 热修补程序**
SUSE Linux Enterprise Server 15、SP1、SP2 | [9.38](https://support.microsoft.com/help/4590304/)  | 默认情况下，所有 [股票 SUSE 15、SP1、SP2 内核](https://www.suse.com/support/kb/doc/?id=000019587) 都受支持。</br></br> 4.12.14-5.5-azure 到 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure 到 4.12.14-8.44-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure 到 5.3.18-18.18-azure </br> 4.12.14-8.47-azure、5.3.18-18.21-azure 到 9.38 热修补程序**
SUSE Linux Enterprise Server 15 和 15 SP1 | [9.36](https://support.microsoft.com/help/4578241/)、[9.37](https://support.microsoft.com/help/4582666/)  | 默认情况下，所有 [股票 SUSE 15、SP1、SP2 内核](https://www.suse.com/support/kb/doc/?id=000019587) 都受支持。</br></br> 4.12.14-5.5-azure 到 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure 到 4.12.14-8.38-azure </br> 4.12.14-8.41-azure、4.12.14-8.44-azure 到 9.37 热修补程序**


## <a name="replicated-machines---linux-file-systemguest-storage"></a>复制的计算机 - Linux 文件系统/来宾存储

* 文件系统：ext3、ext4、XFS、BTRFS
* 卷管理器：LVM2

> [!NOTE]
> 不支持多路径软件。


## <a name="replicated-machines---compute-settings"></a>复制的计算机 - 计算设置

**设置** | **支持** | **详细信息**
--- | --- | ---
大小 | 具有至少 2 个 CPU 内核和 1 GB RAM 的任意 Azure VM 大小 | 验证 [Azure 虚拟机大小](../virtual-machines/sizes.md)。
可用性集 | 支持 | 对于 Azure VM，如果启用使用默认选项进行复制的功能，则会根据源区域设置来自动创建可用性集。 可以修改这些设置。
可用性区域 | 支持 |
混合使用权益 (HUB) | 支持 | 如果源 VM 启用了 HUB 许可证，则测试故障转移或故障转移 VM 也使用 HUB 许可证。
虚拟机规模集 | 不支持 |
Azure 库映像 — 由 Microsoft 发布 | 支持 | 如果 VM 在受支持的操作系统上运行，则支持该配置。
Azure 库映像 - 由第三方发布 | 支持 | 如果 VM 在受支持的操作系统上运行，则支持该配置。
自定义映像 - 由第三方发布 | 支持 | 如果 VM 在受支持的操作系统上运行，则支持该配置。
使用 Site Recovery 迁移的 VM | 支持 | 如果使用 Site Recovery 将 VMware VM 或物理计算机迁移到 Azure，则需要卸载计算机上运行的旧版移动服务，并在重启计算机后将该计算机复制到另一个 Azure 区域。
Azure RBAC 策略 | 不支持 | VM 上的 Azure 基于角色的访问控制 (Azure RBAC) 策略不会复制到目标区域中的故障转移 VM。
扩展 | 不支持 | 扩展不会复制到目标区域中的故障转移 VM。 需要在故障转移后手动进行安装。
邻近放置组 | 支持 | 可使用 Site Recovery 保护位于邻近放置组内的虚拟机。
Tags  | 支持 | 应用于源虚拟机上的用户生成的标签会在测试故障转移或故障转移后转移到目标虚拟机上。 只要 VM (的) 在目标区域中，就会每24小时复制一次 VM (s) 的标记。


## <a name="replicated-machines---disk-actions"></a>复制的计算机 - 磁盘操作

**操作** | **详细信息**
-- | ---
调整复制的 VM 上的磁盘大小 | 故障转移前在源 VM 上受支持。 无需禁用/重新启用复制。<br/><br/> 如果在故障转移后更改源 VM，则不会捕获这些更改。<br/><br/> 如果在故障转移后更改 Azure VM 上的磁盘大小，则 Site Recovery 不会捕获这些更改，将故障回复到原始 VM 大小。
将磁盘添加到复制的 VM | 支持
对受保护磁盘的脱机更改 | 断开磁盘连接并对其进行脱机修改需要触发完整的重新同步操作。

## <a name="replicated-machines---storage"></a>复制的计算机 - 存储

此表汇总了对 Azure VM OS 磁盘、数据磁盘和临时磁盘的支持。

- 请务必遵循[托管磁盘](../virtual-machines/disks-scalability-targets.md)的 VM 磁盘限制以及目标，以避免任何性能问题。
- 如果使用默认设置进行部署，Site Recovery 会根据源设置自动创建磁盘和存储帐户。
- 如果自定义，请确保遵循指南。

**组件** | **支持** | **详细信息**
--- | --- | ---
OS 磁盘的最大大小 | 2048 GB | [深入了解 ](../virtual-machines/managed-disks-overview.md)VM 磁盘相关信息。
临时磁盘 | 不支持 | 始终从复制中排除临时磁盘。<br/><br/> 请勿在临时磁盘上存储任何持久性数据。 [了解详细信息](../virtual-machines/managed-disks-overview.md)。
数据磁盘的最大大小 | 托管磁盘为 32 TB<br></br>非托管磁盘为 4 TB|
数据磁盘最小大小 | 对非托管磁盘没有限制。 托管磁盘为 2 GB |
数据磁盘的最大数量 | 最多为 64，根据对特定的 Azure VM 大小的支持而定 | [深入了解 ](../virtual-machines/sizes.md)VM 大小相关信息。
数据磁盘更改率 | 每个高级存储的磁盘最大为 20 MBps。 每个标准存储的磁盘最大为 2 MBps。 | 如果磁盘上的平均数据更改率持续高于最大值，复制将跟不上。<br/><br/>  但是，如果偶尔超出最大值，则复制可跟上，但可能会看到稍有延迟的恢复点。
数据磁盘 - 标准存储帐户 | 支持 |
数据磁盘 - 高级存储帐户 | 支持 | 如果 VM 将磁盘分散在高级和标准存储帐户上，则可以为每个磁盘选择不同的目标存储帐户，以确保在目标区域中具有相同的存储配置。
托管磁盘 - 标准 | 在支持 Azure Site Recovery 的 Azure 区域中受支持。 |
托管磁盘 - 高级 | 在支持 Azure Site Recovery 的 Azure 区域中受支持。 |
标准 SSD | 支持 |
冗余 | LRS 和 GRS 受支持。<br/><br/> ZRS 不受支持。
冷存储和热存储 | 不支持 | 冷存储和热存储不支持 VM 磁盘
存储空间 | 支持 |
NVMe 存储接口 | 不支持
静态加密 (SSE) | 支持 | SSE 是存储帐户的默认设置。
静态加密 (CMK) | 支持 | 托管磁盘支持软件密钥和 HSM 密钥
静态双重加密 | 支持 | 详细了解 [Windows](../virtual-machines/disk-encryption.md) 和 [Linux](../virtual-machines/disk-encryption.md) 支持的区域
适用于 Windows OS 的 Azure 磁盘加密 (ADE) | 支持使用托管磁盘的 VM。 | 不支持使用非托管磁盘的 VM。 <br/><br/> 不支持 HSM 保护的密钥。 <br/><br/> 不支持加密单个磁盘上的各个卷。 |
适用于 Linux OS 的 Azure 磁盘加密 (ADE) | 支持使用托管磁盘的 VM。 | 不支持使用非托管磁盘的 VM。 <br/><br/> 不支持 HSM 保护的密钥。 <br/><br/> 不支持对单个磁盘上的单个卷进行加密。 <br><br> 有关启用复制的已知问题。 [了解详细信息。](./azure-to-azure-troubleshoot-errors.md#enable-protection-failed-as-the-installer-is-unable-to-find-the-root-disk-error-code-151137) |
SAS 密钥轮换 | 不支持 | 如果对存储帐户的 SAS 密钥进行轮换，客户需要禁用复制后再将其重新启用。 |
热添加    | 支持 | 使用托管磁盘的 VM 支持对添加到已复制 Azure VM 的数据磁盘启用复制。 <br/><br/> 一次只能将一个磁盘热添加到 Azure VM。 不支持并行添加多个磁盘。 |
热删除磁盘    | 不支持 | 如果在 VM 上删除数据磁盘，需要对 VM 禁用复制，然后重新启用复制。
排除磁盘 | 支持。 必须使用 [PowerShell](azure-to-azure-exclude-disks.md) 进行配置。 |    默认会排除临时磁盘。
存储空间直通  | 崩溃一致恢复点支持。 不支持应用程序一致恢复点。 |
横向扩展文件服务器  | 崩溃一致恢复点支持。 不支持应用程序一致恢复点。 |
DRBD | 不支持 DRBD 设置中的磁盘。 |
LRS | 支持 |
GRS | 支持 |
RA-GRS | 支持 |
ZRS | 不支持 |
冷存储和热存储 | 不支持 | 冷存储和热存储不支持虚拟机磁盘
虚拟网络的 Azure 存储防火墙  | 支持 | 如果限制通过虚拟网络访问存储帐户，请启用[允许受信任的 Microsoft 服务](../storage/common/storage-network-security.md#exceptions)。
常规用途 V2 存储帐户（冷热存储层） | 支持 | 与常规用途 V1 存储帐户相比，事务成本显著增加
第 2 代（UEFI 启动） | 支持
NVMe 磁盘 | 不支持
Azure 共享磁盘 | 不支持
安全传输选项 | 支持
已启用写入加速器的磁盘 | 不支持
Tags  | 支持 | 用户生成的标签每 24 小时复制一次。

>[!IMPORTANT]
> 为了避免出现性能问题，请务必遵循[托管磁盘](../virtual-machines/disks-scalability-targets.md)的 VM 磁盘可伸缩性和性能目标。 如果使用默认设置，Site Recovery 会基于源配置创建所需的磁盘和存储帐户。 如果自定义和选择自己的设置，请遵守源 VM 的磁盘可伸缩性和性能目标。

## <a name="limits-and-data-change-rates"></a>限制和数据更改率

下表汇总了 Site Recovery 限制。

- 这些限制基于我们的测试，但很明显，它们并未涵盖所有可能的应用程序 I/O 组合。
- 实际结果可能因应用 I/O 的混合形式而异。
- 有两个限制需要考虑：每个磁盘的数据变动率，以及每个虚拟机的数据变动率。
- 无论大小如何，当前每个虚拟机数据变动量限制为 54 MB/秒。

**存储目标** | **平均源磁盘 I/O** |**平均源磁盘数据变动量** | **每天的总源磁盘数据变动量**
---|---|---|---
标准存储 | 8 KB    | 2 MB/秒 | 每个磁盘 168 GB
高级 P10 或 P15 磁盘 | 8 KB    | 2 MB/秒 | 每个磁盘 168 GB
高级 P10 或 P15 磁盘 | 16 KB | 4 MB/秒 |    每个磁盘 336 GB
高级 P10 或 P15 磁盘 | 至少 32 KB | 8 MB/秒 | 每个磁盘 672 GB
高级 P20、P30、P40 或 P50 磁盘 | 8 KB    | 5 MB/秒 | 每个磁盘 421 GB
高级 P20、P30、P40 或 P50 磁盘 | 至少 16 KB |20 MB/秒 | 每个磁盘 1684 GB

## <a name="replicated-machines---networking"></a>复制的计算机 - 网络
**设置** | **支持** | **详细信息**
--- | --- | ---
NIC | 特定 Azure VM 大小支持的最大数量 | 在故障转移期间创建 VM 时会创建 NIC。<br/><br/> 故障转移 VM 上的 NIC 数目取决于启用复制时源 VM 上的 NIC 数目。 如果在启用复制后添加或删除 NIC，不会影响故障转移后复制的 VM 上的 NIC 数目。 <br/><br/> 不保证故障转移后的 NIC 顺序与原始顺序相同。 <br/><br/> 可以根据组织的命名约定，重命名目标区域中的 NIC。 使用 PowerShell 支持 NIC 重命名。
内部负载均衡器 | 不支持 | 可在主要区域中设置公共/internet 负载均衡器。 但在 DR 区域中，Azure Site Recovery 不支持公共/Internet 负载均衡器。
内部负载均衡器 | 支持 | 在恢复计划中使用 Azure 自动化脚本关联预配置的负载均衡器。
公共 IP 地址 | 支持 | 将现有的公共 IP 地址与 NIC 关联。 或者，在恢复计划中使用 Azure 自动化脚本创建公共 IP 地址并将其与 NIC 关联。
NIC 上的 NSG | 支持 | 在恢复计划中使用 Azure 自动化脚本将 NSG 与 NIC 关联。
子网上的 NSG | 支持 | 在恢复计划中使用 Azure 自动化脚本将 NSG 与子网关联。
保留（静态）IP 地址 | 支持 | 如果源 VM 上的 NIC 具有静态 IP 地址，并且目标子网具有相同的可用 IP 地址，则会将它分配给故障转移 VM。<br/><br/> 如果目标子网没有相同的可用 IP 地址，则为 VM 保留子网中的某个可用 IP 地址。<br/><br/> 此外可以在“复制的项” > “设置” > “计算和网络” > “网络接口”   中指定固定的 IP 地址和子网。
动态 IP 地址 | 支持 | 如果源上的 NIC 具有动态 IP 地址，故障转移 VM 上的 NIC 也默认为动态。<br/><br/> 如果有需要，可以将其修改为固定的 IP 地址。
多个 IP 地址 | 不支持 | 如果故障转移的 VM 包含采用多个 IP 地址的 NIC，则只会保留源区域中 NIC 的主要 IP 地址。 若要分配多个 IP 地址，可将 VM 添加到[恢复计划](recovery-plan-overview.md)，并附加一个用于将其他 IP 地址分配到该计划的脚本，也可手动进行更改，或故障转移后使用脚本进行更改。
流量管理器     | 支持 | 可以预配置流量管理器，这样在正常情况下，流量路由到源区域中的终结点；发生故障转移时，流量路由到目标区域中的终结点。
Azure DNS | 支持 |
自定义 DNS    | 支持 |
未经身份验证的代理 | 支持 | [了解详细信息](./azure-to-azure-about-networking.md)
经过身份验证的代理 | 不支持 | 如果 VM 对出站连接使用经过身份验证的代理，则不能使用 Azure Site Recovery 复制该 VM。
与本地建立 VPN 站点到站点连接<br/><br/>（使用或不使用 ExpressRoute）| 支持 | 配置 UDR 和 NSG 时，请确保 Site Recovery 流量不会路由到本地。 [了解详细信息](./azure-to-azure-about-networking.md)
VNET 到 VNET 连接    | 支持 | [了解详细信息](./azure-to-azure-about-networking.md)
虚拟网络服务终结点 | 支持 | 若要限制对存储帐户的虚拟网络访问，请确保允许受信任的 Microsoft 服务访问存储帐户。
加速网络 | 支持 | 必须在源 VM 上启用加速网络。 [了解详细信息](azure-vm-disaster-recovery-with-accelerated-networking.md)。
Palo Alto 网络设备 | 不支持 | 使用第三方设备时，虚拟机内部通常存在提供商施加的一些限制。 要使 Azure Site Recovery 可用，需在其上安装代理、扩展并为其提供出站连接。 但是，该设备不允许在虚拟机内部配置任何出站活动。
IPv6  | 不支持 | 此外，不支持同时包含 IPv4 和 IPv6 的混合配置。 请在执行任何 Site Recovery 操作之前释放 IPv6 范围的子网。
对 Site Recovery 服务的专用链接访问 | 支持 | [了解详细信息](azure-to-azure-how-to-enable-replication-private-endpoints.md)
Tags  | 支持 | NIC 上用户生成的标签每 24 小时复制一次。



## <a name="next-steps"></a>后续步骤

- 阅读[网络指南](./azure-to-azure-about-networking.md)，了解如何复制 Azure VM。
- 通过[复制 Azure VM](./azure-to-azure-quickstart.md) 来部署灾难恢复。
