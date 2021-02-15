---
title: 管理智能组
description: 管理通过警报实例创建的智能组
ms.topic: conceptual
ms.subservice: alerts
ms.date: 09/24/2018
ms.openlocfilehash: 564440336bf6a90cbd40aba6c8284cbc6c5e3ff3
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186042"
---
# <a name="manage-smart-groups"></a>管理智能组

[智能组](./alerts-smartgroups-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json)使用机器学习算法根据共现或相似性将警报分组在一起，以便用户现在可以管理智能组，而不必单独管理每个警报。 本文将介绍如何在 Azure Monitor 中访问和使用智能组。

1. 若要查看为警报实例创建的智能组，可以执行以下任一操作：

     1. 从“警报摘要”页单击“智能组”。    
    ![屏幕截图显示了突出显示“智能组”的“警报摘要”页。](./media/alerts-managing-smart-groups/sg-alerts-summary.jpg)
    
     1. 从“所有警报”页单击“警报(按智能组)”。   
     ![屏幕截图显示了突出显示“警报(按智能组)”的“所有警报”页。](./media/alerts-managing-smart-groups/sg-all-alerts.jpg)

2. 这会转到通过警报实例创建的所有智能组的列表视图。 你现在可以处理智能组，而不是筛选多个警报。   
![屏幕截图显示了“所有警报”页。](./media/alerts-managing-smart-groups/sg-list.jpg)

3. 单击任何智能组将打开详细信息页，你可以在其中查看分组原因以及成员警报。 此聚合允许你处理单个智能组，而不是筛选多个警报。   
![屏幕截图显示了“详细信息”页。](./media/alerts-managing-smart-groups/sg-details.jpg)