---
title: 教程 - 缩放私有云
description: 在本教程中，使用 Azure 门户缩放 Azure VMware 解决方案私有云。
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: d49d973cc6d97280dc0c7ea6681f2602b871e1ba
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791233"
---
# <a name="tutorial-scale-an-azure-vmware-solution-private-cloud"></a>教程：缩放 Azure VMware 解决方案私有云

若要充分利用 Azure VMware 解决方案私有云体验，请缩放群集和主机，使之反映你对计划内工作负载的需求。 可以根据应用程序工作负载的需求，缩放私有云中的群集和主机。 应该逐一解决特定服务的性能和可用性限制。 [私有云概念](concepts-private-clouds-clusters.md)一文提供了群集和主机限制。

在本教程中，你将使用 Azure 门户执行以下操作：

> [!div class="checklist"]
> * 向现有私有云添加群集
> * 向现有群集添加主机

## <a name="prerequisites"></a>先决条件

若要完成本教程，需要一个私有云。 如果未创建私有云，请使用[创建私有云教程](tutorial-create-private-cloud.md)来进行创建。 在 Azure 中为 VMware 私有云配置网络，以设置必需的虚拟网络。

## <a name="add-a-new-cluster"></a>添加新群集

1. 在现有私有云的概览页的“管理”  下，选择“缩放私有云”  。 接下来，选择“+ 添加群集”  。

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss2-select-add-cluster.png" alt-text="选择“添加群集”" border="true":::

1. 在“添加群集”  页中，使用滑块选择主机数。 选择“保存”。 

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss3-configure-new-cluster.png" alt-text="在“添加群集”页中，使用滑块选择主机数。选择“保存”。" border="true":::

   将开始部署新群集。

## <a name="scale-a-cluster"></a>缩放群集 

1. 在现有私有云的概览页上，选择“缩放私有云”  ，然后选择铅笔图标以编辑群集。

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss4-select-scale-private-cloud-2.png" alt-text="在“概览”中选择“缩放私有云”" border="true":::

1. 在“编辑群集”  页中，使用滑块选择主机数。 选择“保存”。 

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss5-scale-cluster.png" alt-text="在“编辑群集”页中，使用滑块选择主机数。选择“保存”。" border="true":::

   将开始向群集添加主机。

## <a name="next-steps"></a>后续步骤

如果需要另一个 Azure VMware 解决方案私有云，请[创建另一个私有云](tutorial-create-private-cloud.md)，遵循相同的网络先决条件、群集和主机限制。

<!-- LINKS - external-->

<!-- LINKS - internal -->
