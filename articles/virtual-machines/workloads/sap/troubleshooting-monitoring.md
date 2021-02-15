---
title: 监视 Azure 上的 SAP HANA（大型实例）| Microsoft Docs
description: 监视 Azure 上的 SAP HANA（大型实例）。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1a8d6fc6acb33af0c36978aa79dc90aeac3cf596
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966745"
---
# <a name="how-to-monitor-sap-hana-large-instances-on-azure"></a>如何监视 Azure 上的 SAP HANA（大型实例）

Azure 上的 SAP HANA（大型实例）与其他任何 IaaS 部署一样，都需要监视 OS 与应用程序在执行哪些操作，以及这些应用程序对以下资源的消耗情况：

- CPU
- 内存
- 网络带宽
- 硬盘空间

对于 Azure 虚拟机，你需要确定上述资源类是否足够，或者它们是否已耗尽。 下面提供了有关每个不同的类的更多详细信息：

**CPU 资源消耗：** 系统实施 SAP 针对 HANA 上的特定工作负荷定义的消耗比，确保有足够的 CPU 资源用于处理内存中存储的数据。 不过，在某些情况下，HANA 可能会由于缺失索引或类似问题，在执行查询时消耗大量 CPU 资源。 这意味着，应该监视 HANA 大型实例单元消耗的 CPU 资源，以及特定 HANA 服务消耗的 CPU 资源。

**内存消耗：** 必须从 HANA 内部监视，以及从单元上的 HANA 外部监视。 在 HANA 内部，应该监视数据对 HANA 分配的内存的消耗情况，使之不会超出 SAP 规定的消耗量指导原则。 此外，还需要在大型实例级别监视内存消耗，确保其他已安装的非 HANA 软件不会消耗过多的内存，从而与 HANA 争用内存。

**网络带宽：** 将数据移入 Azure VNet 时，Azure VNet 网关会限制带宽，因此，监视 VNet 中的所有 Azure VM 接收的数据有助于确定与所选 Azure 网关 SKU 的限制还有多大的差距。 在 HANA 大型实例单元上，监视传入和传出的网络流量以及跟踪不同时间处理的数据量确实很有作用。

**磁盘空间：** 磁盘空间消耗量通常会随着时间的推移而增大。 最常见的原因包括：数据量增加、执行事务日志备份、存储跟踪文件，以及执行存储快照。 因此，必须监视磁盘空间用量，管理与 HANA 大型实例单元相关的磁盘空间。

对于 HANA 大型实例类型 II SKU，服务器附带预载的系统诊断工具。 可以利用这些诊断工具执行系统健康状况检查。 运行以下命令，在 /var/log/health_check 生成健康状况检查日志文件。
```
/opt/sgi/health_check/microsoft_tdi.sh
```
同 Microsoft 支持团队一起排除故障时，还可能需要使用这些诊断工具提供日志文件。 可以使用以下命令压缩文件。
```
tar  -czvf health_check_logs.tar.gz /var/log/health_check
```

后续步骤

- 参阅[如何监视 Azure 上的 SAP HANA（大型实例）](./hana-monitor-troubleshoot.md)。
