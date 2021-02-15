---
title: 设置源设置，以便使用 Azure Site Recovery 将 VMware 灾难恢复到 Azure
description: 本文介绍如何设置本地环境，以便使用 Azure Site Recovery 将 VMware VM 复制到 Azure。
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/14/2019
ms.author: ramamill
ms.openlocfilehash: afd3979690b8952c915a49099ee04b3d416031fd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88189735"
---
# <a name="set-up-the-source-environment-for-vmware-to-azure-replication"></a>设置源环境以便从 VMware 复制到 Azure

本文介绍如何设置源本地环境，以便将 VMware VM 复制到 Azure。 文本包括以下相关步骤：选择复制方案、将本地计算机设置为 Site Recovery 配置服务器和自动发现本地 VM。

## <a name="prerequisites"></a>必备条件

本文假设已完成以下操作：

- 借助 [Azure Site Recovery 部署规划器](site-recovery-deployment-planner.md)规划了你的部署。 这可以帮助你根据每日数据更改率分配足够的带宽以满足你需要的恢复点目标 (RPO)。
- 在 [Azure 门户](https://portal.azure.com)中[设置资源](tutorial-prepare-azure.md)。
- [设置本地 VMware](vmware-azure-tutorial-prepare-on-premises.md)，包括用于自动发现的专用帐户。

## <a name="choose-your-protection-goals"></a>选择保护目标

1. 在“恢复服务保管库”  中，选择保管库名称。 我们在此方案中使用 **ContosoVMVault**。
2. 在“入门”中，选择“Site Recovery”  ， 然后选择“准备基础结构”  。
3. 在“保护目标” > “计算机所在位置”中，选择“本地”。
4. 在“要将计算机复制到何处?”中，选择“复制到 Azure”   。
5. 在“你的计算机是否已虚拟化”中，选择“是，带有 VMware vSphere 虚拟机监控程序”   。 然后选择“确定”。 

## <a name="set-up-the-configuration-server"></a>设置配置服务器

可以通过开放虚拟化应用程序 (OVA) 模板将配置服务器设置为本地 VMware VM。 [详细了解](./vmware-azure-architecture.md)将在 VMware VM 上安装的组件。

1. 了解配置服务器部署的[先决条件](vmware-azure-deploy-configuration-server.md#prerequisites)。
2. [检查容量数字](vmware-azure-deploy-configuration-server.md#sizing-and-capacity-requirements)以便进行部署。
3. [下载](vmware-azure-deploy-configuration-server.md#download-the-template)并[导入](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware) OVA 模板，设置运行配置服务器的本地 VMware VM。 通过模板提供的许可证是评估许可证，有效期为 180 天。 超过有效期之后，客户需要使用购买的许可证来激活 Windows。
4. 打开 VMware VM，并将其[注册](vmware-azure-deploy-configuration-server.md#register-the-configuration-server-with-azure-site-recovery-services)到恢复服务保管库中。

## <a name="azure-site-recovery-folder-exclusions-from-antivirus-program"></a>从防病毒程序中排除 Azure Site Recovery 文件夹

### <a name="if-antivirus-software-is-active-on-source-machine"></a>如果防病毒软件在源计算机上处于活动状态

如果源计算机具有处于活动状态的防病毒软件，则应排除安装文件夹。 因此，为了平稳进行复制，请排除文件夹 *C:\ProgramData\ASR\agent*。

### <a name="if-antivirus-software-is-active-on-configuration-server"></a>如果防病毒软件在配置服务器上处于活动状态

为了平稳进行复制并避免出现连接问题，请从防病毒软件中排除以下文件夹

- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\Program Files\Microsoft Azure Site Recovery Provider
- C:\Program Files\Microsoft Azure Site Recovery Configuration Manager 
- C:\Program Files\Microsoft Azure Site Recovery Error Collection Tool 
  - C:\thirdparty
  - C:\Temp
  - C:\strawberry
  - C:\ProgramData\MySQL
  - C:\Program Files (x86)\MySQL
  - C:\ProgramData\ASR
  - C:\ProgramData\Microsoft Azure Site Recovery
  - C:\ProgramData\ASRLogs
  - C:\ProgramData\ASRSetupLogs
  - C:\ProgramData\LogUploadServiceLogs
  - C:\inetpub
  - Site Recovery 服务器安装目录。 例如：E:\Program Files (x86)\Microsoft Azure Site Recovery

### <a name="if-antivirus-software-is-active-on-scale-out-process-servermaster-target"></a>如果防病毒软件在横向扩展流程服务器/主目标上处于活动状态

请从防病毒软件中排除以下文件夹

1. C:\Program Files\Microsoft Azure Recovery Services Agent
2. C:\ProgramData\ASR
3. C:\ProgramData\ASRLogs
4. C:\ProgramData\ASRSetupLogs
5. C:\ProgramData\LogUploadServiceLogs
6. C:\ProgramData\Microsoft Azure Site Recovery
7. Azure Site Recovery 负载均衡的进程服务器安装目录，示例：C:\Program Files (x86)\Microsoft Azure Site Recovery

### <a name="if-antivirus-software-is-active-on-the-linux-master-target"></a>如果防病毒软件在 Linux 主目标服务器上处于活动状态

请从防病毒软件中排除以下文件夹

1.  /usr/local/ASR
2.  /usr/local/InMage
3.  /var/log/vxlogs
4.  /var/log
5.  /var/log/ApplicationPolicyLogs
6.  /var/log/ASRsetuptelemetry
7.  /var/log/ASRsetuptelemetry_uploaded


## <a name="next-steps"></a>后续步骤
[设置目标环境](./vmware-azure-set-up-target.md) 
