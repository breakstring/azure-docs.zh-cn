---
title: 为 Azure NetApp 文件创建 NFS 卷 |Microsoft Docs
description: 本文介绍如何在 Azure NetApp 文件中创建 NFS 卷。 了解注意事项，如要使用的版本和最佳实践。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/24/2020
ms.author: b-juche
ms.openlocfilehash: 2cc9d3e0fb711a0662852ce4f2c5a08dc626f246
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2020
ms.locfileid: "96854727"
---
# <a name="create-an-nfs-volume-for-azure-netapp-files"></a>创建用于 Azure NetApp 文件的 NFS 卷

Azure NetApp 文件支持使用 NFS (NFSv3 和 NFSv 4.1) 、SMB3 或双重协议 (NFSv3 和 SMB) 创建卷。 卷的容量消耗是依据其池的预配容量计数的。 本文介绍如何创建 NFS 卷。 

## <a name="before-you-begin"></a>开始之前 
* 必须已设置容量池。  
    请参阅 [设置容量池](azure-netapp-files-set-up-capacity-pool.md)。   
* 子网必须委派给 Azure NetApp 文件。  
    请参阅 [向 Azure NetApp 文件委托子网](azure-netapp-files-delegate-subnet.md)。

## <a name="considerations"></a>注意事项 

* 确定要使用的 NFS 版本  
  NFSv3 可以处理各种用例，通常部署在大多数企业应用程序中。 应该验证 (NFSv3 或 NFSv 4.1) 应用程序所需的版本，并使用适当的版本创建你的卷。 例如，如果使用 [Apache ActiveMQ](https://activemq.apache.org/shared-file-system-master-slave)，则建议通过 NFSv3 使用 nfsv 4.1 进行文件锁定。 

* 安全  
  支持 UNIX 模式位 (读取、写入和执行) 适用于 NFSv3 和 NFSv 4.1。 NFS 客户端上需要根级别的访问权限才能装载 NFS 卷。

* NFSv 4.1 的本地用户/组和 LDAP 支持  
  目前，NFSv 4.1 仅支持对卷进行根访问。 请参阅 [为 Azure NetApp 文件配置 nfsv 4.1 默认域](azure-netapp-files-configure-nfsv41-domain.md)。 

## <a name="best-practice"></a>最佳做法

* 确保为卷使用正确的装入说明。  请参阅 [装入或卸载 Windows 或 Linux 虚拟机的卷](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)。

* NFS 客户端应与 Azure NetApp 文件卷位于同一 VNet 或对等互连 VNet 中。 支持从 VNet 外部进行连接;但是，它会引入额外的延迟并降低整体性能。

* 确保 NFS 客户端是最新的，并且运行最新的操作系统更新。

## <a name="create-an-nfs-volume"></a>创建 NFS 卷

1.  从“容量池”边栏选项卡中单击“卷”边栏选项卡。 单击“+ 添加卷”以创建卷。 

    ![导航到卷](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png) 

2.  在 "创建卷" 窗口中，单击 " **创建**"，并在 "基本信息" 选项卡下提供以下字段的信息：   
    * **卷名称**      
        指定要创建的卷的名称。   

        卷名称在每个容量池中必须是唯一的。 它的长度必须至少为三个字符。 你可以使用任何字母数字字符。   

        不能将 `default` 或 `bin` 用作卷名称。

    * **容量池**  
        指定要在其中创建卷的容量池。

    * **配额**  
        指定分配给卷的逻辑存储量。  

        “可用配额”字段显示了所选容量池中可以用来创建新卷的未使用空间量。 新卷的大小不能超过可用配额。  

    * **吞吐量 (MiB/秒)**   
        如果在手动 QoS 容量池中创建了卷，请指定要用于卷的吞吐量。   

        如果卷是在自动 QoS 容量池中创建的，则此字段中显示的值为 (配额 x 服务级别吞吐量) 。   

    * **虚拟网络**  
        指定要从中访问卷的 Azure 虚拟网络 (VNet)。  

        你指定的 Vnet 必须已将子网委托给 Azure NetApp 文件。 只能从同一 Vnet 或者从与卷位于同一区域的 Vnet 通过 Vnet 对等互连来访问 Azure NetApp 文件服务。 还可以从本地网络通过 Express Route 来访问卷。   

    * **子网**  
        指定要用于卷的子网。  
        你指定的子网必须委派给 Azure NetApp 文件。 
        
        如果尚未委派子网，可以在“创建卷”页面上单击“新建”。 然后，在“创建子网”页面中，指定子网信息，并选择“Microsoft.NetApp/卷”来为 Azure NetApp 文件委派子网。 在每个 Vnet 中，只能将一个子网委托给 Azure NetApp 文件。   
 
        ![创建卷](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![创建子网](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * 如果要将现有的快照策略应用到卷，请单击 " **显示高级" 部分** 将其展开，指定是否要隐藏快照路径，并在下拉菜单中选择快照策略。 

        有关创建快照策略的信息，请参阅 [管理快照策略](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)。

        ![显示高级选择](../media/azure-netapp-files/volume-create-advanced-selection.png)

3. 单击“协议”  ，然后完成以下操作：  
    * 选择“NFS”  作为卷的协议类型。   
    * 指定将用于创建新卷的导出路径的 **文件路径** 。 导出路径用来装载并访问卷。

        文件路径名只能包含字母、数字和连字符 ("-")。 它的长度必须介于 16 到 40 个字符之间。 

        文件路径在每个订阅和每个区域中必须是唯一的。 

    * 选择卷的 NFS 版本（**NFSv3** 或 **NFSv4.1**）。  

    * 如果使用的是 NFSv 4.1，请指出是否要为卷启用 **Kerberos** 加密。  

        如果将 Kerberos 与 NFSv 4.1 一起使用，则需要进行其他配置。 按照 [配置 nfsv 4.1 Kerberos 加密](configure-kerberos-encryption.md)中的说明进行操作。

    * （可选） [配置 NFS 卷的导出策略](azure-netapp-files-configure-export-policy.md)。

    ![指定 NFS 协议](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

4. 单击“查看 + 创建”以查看卷详细信息。  然后单击 " **创建** " 来创建卷。

    创建的卷将显示在“卷”页中。 
 
    卷从其容量池继承订阅、资源组和位置属性。 若要监视卷部署状态，可以使用“通知”选项卡。


## <a name="next-steps"></a>后续步骤  

* [为 Azure NetApp 文件配置 NFSv4.1 默认域](azure-netapp-files-configure-nfsv41-domain.md)
* [配置 NFSv 4.1 Kerberos 加密](configure-kerberos-encryption.md)
* [为 Windows 或 Linux 虚拟机装载或卸载卷](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [为 NFS 卷配置导出策略](azure-netapp-files-configure-export-policy.md)
* [Azure NetApp 文件的资源限制](azure-netapp-files-resource-limits.md)
* [了解 Azure 服务的虚拟网络集成](../virtual-network/virtual-network-for-azure-services.md)