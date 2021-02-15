---
title: Azure NetApp 文件的指标 | Microsoft Docs
description: Azure NetApp 文件提供了有关已分配存储、实际存储使用情况、卷 IOPS 和延迟的指标。 使用这些指标来了解使用情况和性能。
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
ms.date: 12/04/2020
ms.author: b-juche
ms.openlocfilehash: a17e6cc0479cf8ff2306736994a369d9e44dfdda
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745938"
---
# <a name="metrics-for-azure-netapp-files"></a>Azure NetApp 文件的指标

Azure NetApp 文件提供了有关已分配存储、实际存储使用情况、卷 IOPS 和延迟的指标。 通过分析这些指标，可以更好地了解 NetApp 帐户的使用模式和卷性能。  

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>容量池的使用情况指标

- *池分配的大小*   
    池的预配大小。

- *分配到卷大小的池*  
    卷配额 (GiB) 在给定的容量池中， (也就是说，容量池中的卷预配大小总计) 。  
    此大小是在创建卷时选择的大小。  

- *池消耗大小*  
    容量池中的所有卷使用的逻辑空间 () 。  

- *池的总快照大小*    
    池内所有卷的快照大小之和。

## <a name="usage-metrics-for-volumes"></a><a name="volumes"></a>卷的使用情况指标

- *消耗的卷大小百分比*    
    使用的卷的百分比，包括快照。  
- *卷分配大小*   
    卷的预配大小
- *卷配额大小*    
    ) 为卷设置 (配额大小。   
- *卷消耗大小*   
    卷的逻辑大小 (使用的字节数) 。  
    此大小包括活动文件系统和快照使用的逻辑空间。  
- *卷快照大小*   
   卷中所有快照的大小。  

## <a name="performance-metrics-for-volumes"></a>卷的性能指标

- *平均读取延迟*   
    从卷读取的平均时间（以毫秒为单位）。
- *平均写入延迟*   
    从卷写入的平均时间（以毫秒为单位）。
- *读取 IOPS*   
    每秒读取到卷的次数。
- *写入 IOPS*   
    每秒向卷进行写入的次数。
<!-- These two metrics are not yet available, until ~ 2020.09
- *Read MiB/s*   
    Read throughput in bytes per second.
- *Write MiB/s*   
    Write throughput in bytes per second.
--> 
<!-- ANF-4128; 2020.07
- *Pool Provisioned Throughput*   
    The total throughput a capacity pool can provide to its volumes based on "Pool Provisioned Size" and "Service Level".
- *Pool Allocated to Volume Throughput*   
    The total throughput allocated to volumes in a given capacity pool (that is, the total of the volumes' allocated throughput in the capacity pool).
-->

<!-- ANF-6443; 2020.11
- *Pool Consumed Throughput*    
    The total throughput being consumed by volumes in a given capacity pool.
-->


## <a name="volume-replication-metrics"></a><a name="replication"></a>卷复制指标

> [!NOTE] 
> * 网络传输大小 (例如， *卷复制总传输* 指标) 可能不同于跨区域复制的源或目标卷。 此行为是由于使用有效的复制引擎来最大程度地减少网络传输成本而导致的。
> * 卷复制指标当前填充了复制目标卷，而不是复制关系的源。

- *卷复制状态是否正常*   
    复制关系的条件。 正常状态表示为 `1` 。 不正常状态表示为 `0` 。

- *卷复制传输*    
    卷复制的状态是否为 "正在传输"。 
 
- *卷复制滞后时间*   
    镜像上的数据落后于源的时间（秒）。 

- *卷复制上次传输持续时间*   
    上次传输完成所用的时间（秒）。 

- *卷复制的上次传输大小*    
    作为上次传输的一部分传输的总字节数。 

- *卷复制进度*    
    当前传输操作传输的总数据量。 

- *卷复制总传输*   
    为关系传输的累计字节数。 

## <a name="next-steps"></a>后续步骤

* [了解 Azure NetApp 文件的存储层次结构](azure-netapp-files-understand-storage-hierarchy.md)
* [设置容量池](azure-netapp-files-set-up-capacity-pool.md)
* [为 Azure NetApp 文件创建卷](azure-netapp-files-create-volumes.md)
