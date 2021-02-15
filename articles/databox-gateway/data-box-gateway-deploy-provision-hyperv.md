---
title: 有关如何在 Hyper-V 中预配 Azure Data Box Gateway 的教程 | Microsoft Docs
description: 有关如何部署 Azure Data Box Gateway 的第二个教程介绍如何在 Hyper-V 中预配虚拟设备。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 12/21/2020
ms.author: alkohli
ms.openlocfilehash: 22d7ee518a458143fe25b677b680a964c538904f
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791555"
---
# <a name="tutorial-provision-azure-data-box-gateway-in-hyper-v"></a>教程：在 Hyper-V 中预配 Azure Data Box Gateway

## <a name="overview"></a>概述

本教程介绍如何在主机系统上预配 Data Box Gateway，此类主机系统在 Windows Server 2016、Windows Server 2012 R2 或 Windows Server 2012 上运行 Hyper-V。

需要管理员权限才能预配和配置虚拟设备。 完成预配和初始设置可能需要大约 10 分钟。 

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
>
> * 确保主机满足最低设备要求
> * 预配虚拟机监控程序中的虚拟设备
> * 启动虚拟设备并获取 IP 地址

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

对于在 Windows Server 2016 或 Windows Server 2012 R2 上运行 Hyper-V 的主机系统，在其上预配虚拟设备的先决条件如下所示。

### <a name="for-the-data-box-gateway-resource"></a>对于 Data Box Gateway 资源

在开始之前，请确保：

* 已完成[为 Data Box Gateway 准备门户](data-box-gateway-deploy-prep.md)中的所有步骤。
* 已按照[为 Data Box Gateway 准备门户](data-box-gateway-deploy-prep.md)中的说明从 Azure 门户下载 Hyper-V 的虚拟设备映像。

  > [!IMPORTANT]
  > 在 Data Box Gateway 上运行的软件只能结合 Data Box Gateway 资源使用。

### <a name="for-the-data-box-gateway-virtual-device"></a>对于 Data Box Gateway 虚拟设备

在部署设备之前，请确保：

* 有权访问在 Windows Server 2012 R2 或更高版本上运行 Hyper-V 的主机系统，以便预配设备。
* 主机系统能够将以下资源专用于预配虚拟设备：

  * 至少 4 个核心。
  * 至少 8 GB 的 RAM。
  * 一个网络接口。
  * 一个 250 GB 的 OS 磁盘。
  * 一个用于存储数据的 2 TB 虚拟磁盘。

### <a name="for-the-network-in-the-datacenter"></a>对于数据中心中的网络

开始之前：

* 查看部署 Data Box Gateway 的网络要求，并根据要求配置数据中心网络。 有关详细信息，请参阅 [Data Box Gateway 网络要求](data-box-gateway-system-requirements.md#networking-port-requirements)。
* 请确保最小 Internet 带宽为 20 Mbps，以便实现设备的理想运行。

## <a name="check-the-host-system"></a>检查主机系统

若要创建虚拟设备，需满足以下条件：

* 在 Windows Server 2016、Windows Server 2012 R2 或 Windows Server 2012 上安装 Hyper-V 角色。
* 将 Microsoft Windows 客户端上的 Microsoft Hyper-V 管理器连接到主机。
* 确保在其上创建虚拟设备的基础硬件（主机系统）能够将以下资源专用于虚拟设备：

  * 至少 4 个虚拟处理器。
  * 至少 8 GB 的 RAM。
  * 一个连接到网络的网络接口，可以将流量路由到 Internet。
  * 一个 250 GB 的 OS 磁盘。
  * 一个用于存储系统数据的 2 TB 虚拟磁盘。

## <a name="bitlocker-considerations"></a>BitLocker 注意事项

* 建议在 Data Box Gateway 虚拟机上启用 BitLocker。 默认情况下，不启用 BitLocker。 有关详细信息，请参阅：
  * [Hyper-V 管理器中的加密支持设置](/windows-server/virtualization/hyper-v/learn-more/generation-2-virtual-machine-security-settings-for-hyper-v#encryption-support-settings-in-hyper-v-manager)
  * [虚拟机中的 BitLocker 支持](https://kb.vmware.com/s/article/2036142)

## <a name="provision-a-virtual-device-in-hypervisor"></a>预配虚拟机监控程序中的虚拟设备

执行以下步骤，预配虚拟机监控程序中的设备。

1. 在 Windows Server 主机上，将虚拟设备映像复制到本地驱动器。 已通过 Azure 门户下载该 VHDX 映像。 记下复制映像的位置，因为在以后的过程中将使用此映像。

2. 打开 **“服务器管理器”** 。 单击右上角的“工具”，并选择“Hyper-V 管理器”。 

    ![在服务器管理器中选择“Hyper-V 管理器”](./media/data-box-gateway-deploy-provision-hyperv/image1.png)

3. 在“Hyper-V 管理器”的作用域窗格中，右键单击系统节点打开上下文菜单，并单击“新建” > “虚拟机”。  

   ![在 Hyper-V 管理器中创建新的虚拟机](./media/data-box-gateway-deploy-provision-hyperv/image2.png)
4. 在新建虚拟机向导的“准备工作”页上，单击“下一步”。 
5. 在“指定名称和位置”页上，提供虚拟设备的“名称”。  单击“下一步”。

   ![“指定名称和位置”页](./media/data-box-gateway-deploy-provision-hyperv/image3.png)
6. 在“指定代数”页上选择“第 2 代”作为 .vhdx 设备映像类型，然后单击“下一步”。      

   ![“指定代数”页](./media/data-box-gateway-deploy-provision-hyperv/image4.png)
7. 在“分配内存”页上，将“启动内存”指定为至少“8192 MB”，且不启用动态内存，然后单击“下一步”。   

   ![“分配内存”页](./media/data-box-gateway-deploy-provision-hyperv/image5.png) 
8. 在“配置网络”页上，指定连接到 Internet 的虚拟交换机，并单击“下一步”。 

   ![“配置网络”页](./media/data-box-gateway-deploy-provision-hyperv/image6.png)
9. 在“连接虚拟硬盘”页上，选择“使用现有虚拟硬盘”，指定虚拟设备映像的位置，然后单击“下一步”。  

   ![“连接虚拟硬盘”页](./media/data-box-gateway-deploy-provision-hyperv/image7.png)
10. 查看“摘要”，并单击“完成”创建虚拟机。 

    ![完成“新建虚拟机”向导页](./media/data-box-gateway-deploy-provision-hyperv/image8.png)
11. 若要满足最低要求，需要 4 个虚拟处理器。 若要添加 4 个虚拟处理器，请在“Hyper-V 管理器”窗口中选择主机系统。 在“虚拟机”列表下的右窗格中，找到刚创建的虚拟机。 选择计算机名称，右键单击该名称后选择“设置”。

    ![虚拟机设置](./media/data-box-gateway-deploy-provision-hyperv/image9.png)
12. 在“设置”页的左窗格中，单击“处理器”。  在右窗格中，将“虚拟处理器数目”设置为 4（或 4 以上）。 单击“应用”。

    ![在“设置”页上设置虚拟处理器数量](./media/data-box-gateway-deploy-provision-hyperv/image10.png)
13. 若要满足最低要求，还需添加 2 TB 的虚拟数据磁盘。 在“设置”页中：

    1. 在左窗格中，选择“SCSI 控制器”。
    2. 在右窗格中，选择“硬盘驱动器”，并单击“添加”。 

    ![在“设置”页上添加硬盘驱动器](./media/data-box-gateway-deploy-provision-hyperv/image11.png)
14. 在“硬盘驱动器”页上，选择“虚拟硬盘”选项，并单击“新建”。   此时会启动“新建虚拟硬盘向导”。

    ![新建虚拟硬盘向导](./media/data-box-gateway-deploy-provision-hyperv/image12.png)
15. 在新建虚拟硬盘向导的“准备工作”页上，单击“下一步”。 
16. 在“选择磁盘格式”页上，接受默认选项：“VHDX”格式。  单击“下一步”。
17. 在“选择磁盘类型”页上，将虚拟硬盘类型设置为“动态扩展”（推荐）。  也可以选择“固定大小”磁盘，但可能需等待很长时间。 建议不要使用“差异”选项。 单击“下一步”。

    ![“选择磁盘类型”页](./media/data-box-gateway-deploy-provision-hyperv/image13.png)
18. 在“指定名称和位置”页上，提供数据磁盘的“名称”和“位置”（可以通过浏览选择一个）。   单击“下一步”。

    ![“指定名称和位置”页](./media/data-box-gateway-deploy-provision-hyperv/image14.png)
19. 在“配置磁盘”页上，选择“新建空白虚拟硬盘”选项，将大小指定为“2 TB”（或 2 TB 以上）。  

    2 TB 为最低要求，可以预配更大的磁盘。 请注意，不能压缩已预配的磁盘。 尝试收缩磁盘会导致设备上的所有本地数据丢失。 不支持扩展数据磁盘。 单击“下一步”。

    ![“配置磁盘”页](./media/data-box-gateway-deploy-provision-hyperv/image15.png)
20. 在“摘要”页上查看虚拟数据磁盘的详细信息，如果一切符合要求，则请单击“完成”创建该磁盘。  此时会关闭向导并向虚拟机添加虚拟硬盘。

    ![完成“新建虚拟硬盘向导”页](./media/data-box-gateway-deploy-provision-hyperv/image16.png)
21. 返回到“设置”页。 单击“确定”关闭“设置”页，返回到“Hyper-V 管理器”窗口。 

    ![“设置”页](./media/data-box-gateway-deploy-provision-hyperv/image17.png)

虚拟机现已完全配置。

> [!NOTE]
> 无法通过复制已配置的 VHD 来配置新的 Data Box Gateway。 每个新 Data Box Gateway 虚拟设备必须通过从 Azure 门户下载的 Hyper-V 虚拟设备映像进行预配。

## <a name="start-the-virtual-device-and-get-the-ip"></a>启动虚拟设备并获取 IP

执行以下步骤，启动虚拟设备并与其进行连接。

#### <a name="to-start-the-virtual-device"></a>启动虚拟设备

1. 启动虚拟设备。

   ![启动虚拟设备](./media/data-box-gateway-deploy-provision-hyperv/image18.png)
2. 设备运行后，选择该设备，右键单击后选择“连接”。

3. 可能需要等待 10-15 分钟设备才能准备就绪。 控制台会显示指示进度的状态消息。 设备就绪后，转到“操作”。 按 `Ctrl + Alt + Delete` 登录到虚拟设备。 默认用户为 *EdgeUser*，默认密码为 *Password1*。

   ![登录到虚拟设备](./media/data-box-gateway-deploy-provision-hyperv/image21.png)

4. 步骤 5-7 仅适用于在非 DHCP 环境中启动的情况。 如果是在 DHCP 环境中，请跳过这些步骤。 如果已在非 DHCP 环境中启动设备，则会看到指示此内容的消息。

5. 若要配置网络，请使用 `Get-HcsIpAddress` 命令列出在虚拟设备上启用的网络接口。 如果设备启用了单个网络接口，则分配到该接口的默认名称为 `Ethernet`。

6. 使用 `Set-HcsIpAddress` cmdlet 配置网络。 请参阅以下示例：

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

7. 完成初始设置并启动设备以后，会显示设备横幅文本。 记下显示在横幅文本中的 IP 地址和 URL，以便管理设备。 请使用该 IP 地址连接到虚拟设备的 Web UI 并完成本地设置和激活。

   ![带有 IP 地址和连接 URL 的虚拟设备横幅](./media/data-box-gateway-deploy-provision-hyperv/image23.png)

如果设备不符合最低配置要求，则会在横幅文本中显示错误。 修改设备配置，使虚拟机有足够的资源来满足最低要求。 然后即可重新启动设备并与之进行连接。 请参阅[检查主机系统是否满足最小虚拟设备要求](#check-the-host-system)中的最低配置要求。

若在使用本地 Web UI 进行初始配置期间遇到其他错误，请参阅以下工作流：

* [运行诊断测试，排除 Web UI 设置故障](data-box-gateway-troubleshoot.md#run-diagnostics)。
* [生成日志包并查看日志文件](data-box-gateway-troubleshoot.md#collect-support-package)。

## <a name="next-steps"></a>后续步骤

在本教程中，我们已了解有关 Data Box Gateway 的主题，例如：

> [!div class="checklist"]
> * 确保主机满足最低设备要求
> * 预配虚拟机监控程序中的虚拟设备
> * 启动虚拟设备并获取 IP 地址

请继续学习下一教程，了解如何连接、设置和激活虚拟设备。

> [!div class="nextstepaction"]
> [连接和设置 Data Box Gateway](./data-box-gateway-deploy-connect-setup-activate.md)
