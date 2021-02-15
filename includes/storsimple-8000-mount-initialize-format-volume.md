---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 23d0f16e025727f8d733f973a2c751ba7114fa49
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95562583"
---
#### <a name="to-mount-initialize-and-format-a-volume"></a>装载、初始化和格式化卷
1. 启动 Microsoft iSCSI 发起程序。
2. 在“iSCSI 发起程序属性”窗口中的“发现”选项卡上，单击“发现门户”。
3. 在“发现目标门户”对话框中，提供支持 iSCSI 网络接口的 IP 地址，并单击“确定”。 
4. 在“iSCSI 发起程序属性”窗口中的“目标”选项卡上，找到“已发现目标”。 设备状态应显示为 **非活动**。
5. 选择目标设备，并单击“ **连接**”。 设备连接后，状态应更改为 **已连接**。 （有关使用 Microsoft iSCSI 发起程序的详细信息，请参阅[安装和配置 Microsoft iSCSI 发起程序][1]）。
6. 在 Windows 主机上，按 Windows 徽标键 + X，并单击“ **运行**”。 
7. 在“运行”对话框中，键入 **Diskmgmt.msc**。 单击 **“确定”**，此时将显示 **“磁盘管理”** 对话框。 右窗格中会显示在主机上的卷。
8. 在“磁盘管理”窗口中，将会显示载入的卷，如下图所示。 右键单击发现的卷（单击磁盘名称），然后单击“联机”。
   
     ![初始化格式化卷](./media/storsimple-8000-mount-initialize-format-volume/step7initializeformatvolume.png) 
9. 再次右键单击该卷（单击磁盘名称），然后单击“初始化”。
10. 若要格式化简单卷，请执行以下步骤：
    
    1. 选择卷，右键单击该卷（单击右侧区域），然后单击 **“新建简单卷”**。
    2. 在“新建简单卷”向导中，指定卷大小和驱动器号，并配置该卷为 NTFS 文件系统。
    3. 指定 64 KB 分配单元大小。 此分配单元大小适用于 StorSimple 解决方案中使用的重复数据删除算法。
    4. 执行快速格式化。

<!--Link references-->
[1]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee338480(v=ws.10)