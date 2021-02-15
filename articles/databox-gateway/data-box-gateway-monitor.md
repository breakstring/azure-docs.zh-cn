---
title: 监视 Azure Data Box Gateway 设备 | Microsoft Docs
description: 介绍如何使用 Azure 门户和本地 Web UI 监视 Azure Data Box Gateway。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: how-to
ms.date: 10/20/2020
ms.author: alkohli
ms.openlocfilehash: ed05f3d60f8ba4fbb06327136c7a117ae1d1d2db
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581555"
---
# <a name="monitor-your-azure-data-box-gateway"></a>监视 Azure Data Box Gateway

本文介绍如何监视 Azure Data Box Gateway。 若要监视设备，可以使用 Azure 门户或本地 Web UI。 使用 Azure 门户查看设备事件、配置和管理警报，以及查看指标。

在本文中，学习如何：

> [!div class="checklist"]
>
> * 查看设备事件和相应的警报
> * 查看设备的容量和事务指标
> * 配置和管理警报

## <a name="view-device-events"></a>查看设备事件

[!INCLUDE [data-box-gateway-view-device-events](../../includes/data-box-gateway-view-device-events.md)]

## <a name="view-metrics"></a>查看指标

[!INCLUDE [data-box-gateway-view-metrics](../../includes/data-box-gateway-view-metrics.md)]

### <a name="metrics-on-your-device"></a>设备上的指标

本部分介绍设备上的监视指标。 指标可为：

* 容量指标。 容量指标与设备的容量有关。

* 事务指标。 事务指标与对 Azure 存储执行的读取和写入操作有关。

下表显示了指标的完整列表：

|容量度量值                     |说明  |
|-------------------------------------|-------------|
|**可用容量**               | 指的是能向设备写入的数据量。 也就是设备上可用的容量。 <br></br>通过删除在设备和云中都有副本的文件的本地副本，可以释放设备容量。        |
|**总容量**                   | 指的是设备上能写入的数据的总字节数。 也称为本地缓存的总大小。 <br></br> 通过添加数据磁盘，可以提升现有虚拟设备的容量。 通过虚拟机监控程序管理为 VM 添加数据磁盘，然后重启 VM。 网关设备的本地存储池将进行扩展，以容纳新添加的数据磁盘。 <br></br>若要了解详细信息，请转到[为 Hyper-V 虚拟机添加硬盘](https://www.youtube.com/watch?v=EWdqUw9tTe4)。 |

|事务度量值              | 说明         |
|-------------------------------------|---------|
|**已上传的云字节数（设备）**    | 在设备上的所有共享中上传的所有字节数之和        |
|**已上传的云字节数（共享）**     | 每个共享中上传的字节数。 这可以是： <br></br> 平均值，即（每个共享中上传的字节数之和/共享数），  <br></br>最大值，一个共享中上传的字节数量的最大值 <br></br>最小值，一个共享中上传的字节数量的最小值      |
|**云下载吞吐量（共享）**| 每个共享中下载的字节数。 这可以是： <br></br> 平均值，即（每个共享中下载的字节数之和/共享数） <br></br> 最大值，一个共享中下载的字节数量的最大值<br></br> 以及最小值，一个共享中下载的字节数量的最小值  |
|**云读取吞吐量**            | 在设备上的所有共享中从云读取的所有字节数之和     |
|**云上传吞吐量**          | 在设备上的所有共享中写入云的所有字节数之和     |
|**云上传吞吐量（共享）**  | 在共享中写入云的所有字节数之和/共享数得出的是平均值、每个共享的最大值和最小值      |
|**读取吞吐量（网络）**           | 包括从云中读取的所有字节数的系统网络吞吐量。 此视图可以包含不限于共享的数据。 <br></br>使用拆分功能，可以显示设备上所有网络适配器上的流量。 包括未连接和未启用的适配器。      |
|**写入吞吐量（网络）**       | 包括写入云的所有字节数的系统网络吞吐量。 此视图可以包含不限于共享的数据。 <br></br>使用拆分功能，可以显示设备上所有网络适配器上的流量。 包括未连接和未启用的适配器。          |

## <a name="manage-alerts"></a>管理警报

[!INCLUDE [data-box-gateway-manage-alerts](../../includes/data-box-gateway-manage-alerts.md)]

## <a name="next-steps"></a>后续步骤

了解如何[管理带宽](data-box-gateway-manage-bandwidth-schedules.md)。
