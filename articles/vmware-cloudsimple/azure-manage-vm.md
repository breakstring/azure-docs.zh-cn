---
title: Azure VMware 解决方案（通过 CloudSimple）-在 Azure 中管理私有云 Vm
description: 介绍如何管理 Azure 门户中的 CloudSimple 私有云 Vm，包括添加磁盘、更改 VM 容量和添加网络接口
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 090b7711ab061b989eae13113fe7048e8dd875ee
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97895183"
---
# <a name="manage-your-cloudsimple-private-cloud-virtual-machines-in-azure"></a>在 Azure 中管理你的 CloudSimple 私有云虚拟机

若要管理 [为 CloudSimple 私有云创建](azure-create-vm.md)的虚拟机，请登录到 [Azure 门户](https://portal.azure.com)。 在侧菜单上的 " **所有服务** " 或 " **虚拟机** " 下搜索并选择虚拟 (搜索) 。

## <a name="control-virtual-machine-operation"></a>控制虚拟机操作

你选择的虚拟机的 " **概述** " 页中提供了下列控件。

| 控制 | 说明 |
| ------------ | ------------- |
| 连接 | 连接到指定的 VM。  |
| 开始 | 启动指定的虚拟机。  |
| 重启 | 关闭并打开指定 VM 的电源。  |
| 停止 | 关闭特定的 VM。  |
| 捕获 | 捕获指定 VM 的映像，以便将其用作创建其他虚拟机的映像。 请参阅 [在 Azure 中创建通用 VM 的托管映像](../virtual-machines/windows/capture-image-resource.md)。   |
| 移动 | 移动到指定的虚拟机。  |
| 删除 | 删除指定的 VM。  |
| 刷新 | 刷新显示的数据。  |

### <a name="view-performance-information"></a>查看性能信息

" **概述** " 页的下半部分中的图表显示所选时间间隔的性能数据 (最后一个小时到过去30天;默认值为最后一个小时) 。 在每个图表中，可以通过将光标移到图表上方，在间隔内的任何时间显示数值。

将显示以下图表。

| Item | 说明 |
| ------------ | ------------- |
| CPU (平均)  | 所选间隔内平均 CPU 使用率（以百分比表示）。   |
| 网络 | 传入和传出网络的流量 (MB) 超出选定的间隔。  |
| 磁盘字节数 | 所选时间间隔内从磁盘读取并写入磁盘 (MB) 的总数据。  |
| 磁盘操作 | 所选间隔内每秒 (操作/秒) 的磁盘操作的平均速率。 |

## <a name="manage-vm-disks"></a>管理 VM 磁盘

若要添加 VM 磁盘，请打开所选 VM 的 " **磁盘** " 页。 若要添加磁盘，请单击 " **添加磁盘**"。 通过输入或选择内联选项来配置以下每个设置。 单击“ **保存**”。

   | Item | 说明 |
   | ------------ | ------------- |
   | 名称 | 输入一个名称用于标识磁盘。  |
   | 大小 | 选择一种可用大小。  |
   | SCSI 控制器 | 选择 "SCSI 控制器"。 可用控制器因支持的操作系统不同而有所不同。  |
   | 模式 | 确定磁盘如何参与快照。 选择以下选项之一： <br> 独立持久性：写入到磁盘的所有数据都将被永久写入。<br> 独立的非永久性：在关闭或重置虚拟机时，写入磁盘的更改将被丢弃。  此模式允许你始终重启处于相同状态的 VM。 有关详细信息，请参阅 [VMware 文档](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html)。 |

若要删除磁盘，请选择它，然后单击 " **删除**"。

## <a name="change-the-capacity-of-the-vm"></a>更改 VM 的容量

若要更改 VM 的容量，请打开所选 VM 的 " **大小** " 页。 指定以下任何项，然后单击 " **保存**"。

| Item | 说明 |
| ------------ | ------------- |
| 内核数 | 分配给 VM 的内核数。  |
| 硬件虚拟化 | 选中此复选框可向来宾操作系统公开硬件虚拟化。 请参阅 VMware 文章 [公开 Vmware 硬件辅助虚拟化](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html)。 |
| 内存大小 | 选择要分配给 VM 的内存量。  

## <a name="manage-network-interfaces"></a>管理网络接口

若要添加接口，请单击 " **添加网络接口**"。 通过输入或选择内联选项来配置以下每个设置。 单击“ **保存**”。

   | 控制 | 说明 |
   | ------------ | ------------- |
   | 名称 | 输入一个名称用于标识接口。  |
   | 网络 | 从私有云 vSphere 中已配置的网络列表中进行选择。  |
   | 适配器 | 从为 VM 配置的可用类型列表中选择 vSphere 适配器。 有关详细信息，请参阅 VMware 知识库文章 [为虚拟机选择网络适配器](https://kb.vmware.com/s/article/1001805)。 |
   | 启动时开机 | 选择是否要在启动 VM 时启用 NIC 硬件。 默认设置为“启用”。 |

若要删除网络接口，请选择它，然后单击 " **删除**"。
