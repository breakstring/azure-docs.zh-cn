---
title: Azure NetApp 文件的资源限制 | Microsoft Docs
description: 介绍 Azure NetApp 文件资源的限制以及如何请求增加资源限制。
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
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: b-juche
ms.openlocfilehash: c82e834c0af3737c1e5ef19c7aa789b94d87f6d8
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2021
ms.locfileid: "99095385"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Azure NetApp 文件的资源限制

了解 Azure NetApp 文件的资源限制，可帮助你管理卷。

## <a name="resource-limits"></a>资源限制

下表描述了 Azure NetApp 文件的资源限制：

|  资源  |  默认限制  |  可通过支持请求进行调整  |
|----------------|---------------------|--------------------------------------|
|  每个 Azure 区域每个订阅的 NetApp 帐户数  |  10    |  是   |
|  每个 NetApp 帐户的容量池数   |    25     |   是   |
|  每个订阅的卷数   |    500     |   是   |
|  每个容量池的卷数量     |    500   |    是     |
|  每个卷的快照数       |    255     |    否        |
|  每个 Azure 虚拟网络) 委托给 Azure NetApp 文件 (的子网数    |   1   |    否    |
|  VNet 中使用的 Ip 数 (包括使用 Azure NetApp 文件立即对等互连 Vnet)    |    1000   |    否   |
|  单个容量池的最小大小   |  4 TiB     |    否  |
|  单个容量池的最大大小    |  500 TiB   |   否   |
|  单个卷的最小大小    |    100 GiB    |    否    |
|  单个卷的最大大小     |    100 TiB    |    否    |
|  单个文件的最大大小     |    16 TiB    |    否    |    
|  单个目录中目录元数据的最大大小      |    320 MB    |    否    |    
|  每卷 ([maxfiles](#maxfiles)) 的最大文件数     |    1 亿    |    是    |    
|  手动 QoS 卷的最小分配吞吐量     |    1 MiB/秒   |    否    |    
|  手动 QoS 卷的最大分配吞吐量     |    4500 MiB/秒    |    否    |    
|  跨区域复制数据保护卷 (目标卷的数量)      |    5    |    是    |     

若要查看目录是否接近目录元数据的最大大小限制 (320 MB) ，请参阅 [如何实现确定目录是否接近限制大小](azure-netapp-files-faqs.md#how-do-i-determine-if-a-directory-is-approaching-the-limit-size)。   

有关详细信息，请参阅 [容量管理常见问题](azure-netapp-files-faqs.md#capacity-management-faqs)。

## <a name="maxfiles-limits"></a>Maxfiles 限制 <a name="maxfiles"></a> 

Azure NetApp 文件卷具有称为 *maxfiles* 的限制。 Maxfiles 限制是卷可以包含的文件数。 根据卷 (配额) 大小对 Azure NetApp 文件卷的 maxfiles 限制进行索引。 对于每个 TiB 的预配卷大小，卷的 maxfiles 限制会增加或降低20000000文件的速率。 

服务基于其预配的大小动态调整卷的 maxfiles 限制。 例如，最初配置为大小为 1 TiB 的卷的 maxfiles 限制将为20000000。 对卷大小的后续更改会导致自动 readjustment 基于以下规则的 maxfiles 限制： 

|    卷大小 (配额)      |  Maxfiles 限制的自动 readjustment    |
|----------------------------|-------------------|
|    <= 1 TiB                |    2000 万     |
|    > 1 TiB，但 <= 2 TiB    |    40000000     |
|    > 2 TiB，但 <= 3 TiB    |    6000 万     |
|    > 3 TiB，但 <= 4 TiB    |    80000000     |
|    > 4 TiB                 |    1 亿    |

如果已为卷分配至少 4 TiB 的配额，则可以启动 [支持请求](#limit_increase) ，以将 maxfiles 限制增加到100000000以上。 对于增加的每个100000000文件 (或) 的分数，需要将相应的卷配额增加 4 TiB。  例如，如果将100000000文件中的 maxfiles 限制增加到200000000个文件 (或) 之间的任意数字，则需要将卷配额从4个 TiB 增加到8个 TiB。

## <a name="request-limit-increase"></a>请求限制增加 <a name="limit_increase"></a> 

可以创建 Azure 支持请求，增加上表中的可调整限制。 

从 Azure 门户导航平面： 

1. 单击 " **帮助 + 支持**"。
2. 单击 " **+ 新建支持请求**"。
3. 在“基本信息”选项卡中提供以下信息： 
    1. 问题类型：选择 **服务和订阅限制)  (配额**。
    2. 订阅：选择需要增加配额的资源的订阅。
    3. 配额类型：选择 " **存储"： Azure NetApp 文件限制**。
    4. 单击 " **下一步：解决方案**"。
4. 在 "详细信息" 选项卡上：
    1. 在 "说明" 框中，为相应的资源类型提供以下信息：

        |  资源  |    父资源      |    请求的新限制     |    配额增加的原因       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  帐户 |  *订阅 ID*   |  *请求的新的最大 **帐户** 编号*    |  *哪个方案或用例提示请求？*  |
        |  池    |  *订阅 ID，NetApp 帐户 URI*  |  *请求的新的最大 **池** 编号*   |  *哪个方案或用例提示请求？*  |
        |  Volume  |  *订阅 ID，NetApp 帐户 URI，容量池 URI*   |  *请求的新的最大 **卷** 号*     |  *哪个方案或用例提示请求？*  |
        |  Maxfiles  |  *订阅 ID，NetApp 帐户 URI，容量池 URI，卷 URI*   |  *请求的新的最大 **maxfiles** 数*     |  *哪个方案或用例提示请求？*  |    
        |  跨区域复制数据保护卷  |  *订阅 ID、目标 NetApp 帐户 URI、目标容量池 URI、源 NetApp 帐户 URI、源容量池 URI、源卷 URI*   |  * 请求新的 **跨区域复制数据保护卷的最大数目 (目标卷)** _     |  _What 方案或用例提示请求？ *  |    

    2. 指定相应的支持方法并提供你的协定信息。

    3. 单击 " **下一步"：查看 + 创建** 以创建请求。 


## <a name="next-steps"></a>后续步骤  

- [了解 Azure NetApp 文件的存储层次结构](azure-netapp-files-understand-storage-hierarchy.md)
- [Azure NetApp 文件的成本模型](azure-netapp-files-cost-model.md)
