---
title: 教师在 Azure 实验室服务中访问 VM
description: 本文介绍了教师如何从教师视图访问其学生 VM。 例如，助教可以是某个课堂的教师，但同时也是其他课堂的学生。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: ad4f9cfd11b372e5c6da5c17eaeba82b0cd8a91f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85445094"
---
# <a name="access-virtual-machines-as-a-student-from-the-educator-view"></a>作为学生从教师视图访问虚拟机
本文介绍了教师如何访问他们作为学生加入的课堂的 VM。 

以下是利用此功能的场景。 某个助教是某个课堂的教师，但同时也是其他课堂的学生。 然后，这名助教希望从显示其拥有的实验室的教师视图中查看和访问学生 VM。 

## <a name="access-vms-from-educator-view"></a>从教师视图访问 VM

1. 登录到 [Azure 实验室服务网站](https://labs.azure.com)。 可以看到自己拥有的实验室。 这些实验室可以是自己创建的实验室，也可以是管理员将你分配为所有者的实验室。 有关详细信息，请参阅[如何向现有实验室添加其他所有者](how-to-add-user-lab-owner.md)
2. 若要访问作为学生加入的课堂的 VM，请选择右上角的计算机图标。 确认看到可以作为学生访问的 VM。 在下面的示例中，用户是 Python 实验室的助教，但同时也是 Java 实验室的学生。 因此，用户可以看到下拉列表中的 Java 实验室中的 VM。 用户可以启动 VM 并连接到该 VM。 
    
    ![访问学生 VM](./media/instructors-access-virtual-machines/access-student-virtual-machines.png)

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [连接到 VM](how-to-use-classroom-lab.md#connect-to-the-vm)
- [在 Mac 上使用 RDP 连接到 VM](connect-virtual-machine-mac-remote-desktop.md)
- [在 Chromebook 上使用 RDP 连接到 VM](connect-virtual-machine-chromebook-remote-desktop.md)
- [使用适用于 Linux 虚拟机的远程桌面](how-to-use-remote-desktop-linux-student.md)
