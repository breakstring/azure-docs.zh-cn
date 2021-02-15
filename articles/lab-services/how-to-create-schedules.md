---
title: 在 Azure 实验室服务中创建实验室计划 |Microsoft Docs
description: 了解如何在 Azure 实验室服务中创建实验室的计划，以便实验室中的 Vm 在指定时间启动和关闭。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 2a827c3d9f3022cb7d27ee43c9c95227c44f97e7
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96434018"
---
# <a name="create-and-manage-schedules-for-labs-in-azure-lab-services"></a>在 Azure 实验室服务中创建和管理实验室计划 
借助日程安排，可以配置课堂实验室，这样实验室中的 VM 就能在指定时间自动启动和关闭。 可以定义一次性日程安排，也可以定义定期日程安排。 下面的过程逐步介绍了如何创建和管理课堂实验室日程安排： 

> [!IMPORTANT]
> Vm 的计划运行时间不会计 [为分配给用户的配额](how-to-configure-student-usage.md#set-quotas-for-users)。 配额是指学生在 VM 上花费的计划外时间。 

## <a name="set-a-schedule-for-the-lab"></a>为实验室设置计划
为实验室创建计划事件，这样实验室中的 VM 就会在特定时间自动启动/停止。 此前指定的用户配额是在此计划时间之外为每个用户分配的额外时间。 

> [!NOTE]
> 在开始之前，计划如何影响实验室虚拟机： 
>- 计划中未包含模板虚拟机。 
>- 仅启动已分配的虚拟机。 这意味着，如果某个计算机不是由最终用户声称 (学生) ，则该计算机将不会在计划的时间启动。 
>- 所有虚拟机都 (是否根据实验室计划停止了用户或未) 的声明。 

1. 切换到“日程安排”页，再选择工具栏上的“添加计划事件”。 

    ![显示 Azure 实验室服务 "计划" 页的屏幕截图，其中选择了 "添加计划" 按钮。](./media/how-to-create-schedules/add-schedule-button.png)
2. 确认已选择“标准”作为“事件类型”。  选择“仅启动”，这样就可以只指定 VM 的启动时间。 选择“仅停止”，这样就可以只指定 VM 的停止时间。 
7. 在“重复”部分，选择当前计划。 

    ![“日程安排”页上的“添加日程安排”按钮](./media/how-to-create-schedules/select-current-schedule.png)
5. 在“重复”对话框中，执行以下步骤：
    1. 对于“重复”字段，请确认已设置“每周” 。 
    3. 指定“启动日期”。
    4. 指定“启动时间”，这是你希望 VM 启动的时间。
    5. 指定“停止时间”，这是 VM 将要关闭的时间。 
    6. 指定“时区”，该时区适用于已指定的启动时间和停止时间。 
    2. 选择希望计划生效的日期。 在下面的示例中，选中的是“星期一到星期四”。 
    8. 选择“保存”。 

        ![设置重复计划](./media/how-to-create-schedules/set-repeat-schedule.png)

3. 现在，请在“添加计划事件”页上输入该计划的任何说明或备注作为“备注(可选)”。 
4. 在“添加计划事件”页上，选择“保存”。 

    ![每周日程安排](./media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>查看日历中的日程安排
可以看到，日历视图中突出显示日程安排日期和时间，如下图所示：

![日历视图中的日程安排](./media/how-to-create-schedules/schedules-calendar.png)

选择右上角的“今天”按钮可以在日历中切换到当前日期。 选择向左箭头和向右箭头可以在日历中分别切换到上周和下周。 

## <a name="edit-a-schedule"></a>编辑日程安排
当你在日历中选择突出显示的计划时，将看到用于 **编辑** 或 **删除** 计划的按钮。 

![“编辑日程安排”页](./media/how-to-create-schedules/schedule-edit-button.png)

在 " **编辑计划事件** " 页上，可以更新计划，并选择 " **保存**"。 

## <a name="delete-a-schedule"></a>删除计划

1. 若要删除计划，请在日历中选择突出显示的计划，并选择 "垃圾桶" 图标 (删除) "按钮：

    ![工具栏上的删除按钮](./media/how-to-create-schedules/schedule-delete-button.png)
2. 在 " **删除计划事件** " 对话框中，选择 **"是"** 以确认删除。 



## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [以管理员身份创建并管理实验室帐户](how-to-manage-lab-accounts.md)
- [以实验室所有者身份创建并管理实验室](how-to-manage-classroom-labs.md)
- [以实验室所有者身份配置并控制实验室的使用](how-to-configure-student-usage.md)
- [作为实验室用户，访问实验室](how-to-use-classroom-lab.md)
