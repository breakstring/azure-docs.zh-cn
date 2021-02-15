---
title: SAP HANA 操作系统兼容性矩阵（大型实例）| Microsoft Docs
description: 兼容性矩阵呈现了不同版本的操作系统与不同硬件类型（大型实例）之间的兼容性
services: virtual-machines-linux
documentationcenter: ''
author: sasarava
manager: hrushib
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/21/2020
ms.author: sasarava
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6d9ae1f7b8741da1116c6591ee672e4073cfc5af
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94955542"
---
# <a name="compatible-operating-systems-for-hana-large-instances"></a>适用于 HANA 大型实例的兼容操作系统

## <a name="hana-large-instance-type-i"></a>HANA 大型实例示例 I     
  | 操作系统 | 可用性        | SKU                                                          |
  |------------------|---------------------|---------------------------------------------------------------|
  | SLES 12 SP2      | 不再提供 | S72、S72m、S96、S144、S144m、S192、S192m、S192xm              |
  | SLES 12 SP3      | 可用           | S72、S72m、S96、S144、S144m、S192、S192m、S192xm              |
  | SLES 12 SP4      | 可用           | S72、S72m、S96、S144、S144m、S192、S192m、S192xm、S224、S224m |
  | SLES 12 SP5      | 可用           | S72、S72m、S96、S144、S144m、S192、S192m、S192xm、S224、S224m |
  | SLES 15 SP1      | 可用           | S72、S72m、S96、S144、S144m、S192、S192m、S192xm、S224、S224m |
  | RHEL 7.6         | 可用           | S72、S72m、S96、S144、S144m、S192、S192m、S192xm、S224、S224m |

  
### <a name="persistent-memory-skus"></a>永久性内存 SKU
  | 操作系统 | 可用性 | SKU                             |
  |------------------|--------------|----------------------------------|
  | SLES 12 SP4      | 可用    | S224oo、S224om、S224ooo、S224oom |
  
## <a name="hana-large-instance-type-ii"></a>HANA 大型实例示例 II     
  |  操作系统       | 可用性        | SKU                                                                     |
  |-------------------------|---------------------|--------------------------------------------------------------------------|
  | SLES 12 SP2             | 不再提供 | S384、S384m、S384xm、S384xxm、S576m、S576xm、S768m、S768xm、S960m        |
  | SLES 12 SP3             | 可用           | S384、S384m、S384xm、S384xxm、S576m、S576xm、S768m、S768xm、S960m        |
  | SLES 12 SP4             | 可用           | S384、S384m、S384xm、S384xxm、S576m、S576xm、S768m、S768xm、S960m        |
  | SLES 12 SP5             | 可用           | S384、S384m、S384xm、S384xxm、S576m、S576xm、S768m、S768xm、S896m、S960m |
  | SLES 15 SP1             | 可用           | S384、S384m、S384xm、S384xxm、S576m、S576xm、S768m、S768xm、S896m、S960m |
  | RHEL 7.6                | 可用           | S384、S384m、S384xm、S384xxm、S576m、S576xm、S768m、S768xm、S896m、S960m |

## <a name="related-documents"></a>相关文档

- 了解有关[可用 SKU](hana-available-skus.md) 的更多信息
- 了解[升级操作系统](os-upgrade-hana-large-instance.md)
  

  
  
