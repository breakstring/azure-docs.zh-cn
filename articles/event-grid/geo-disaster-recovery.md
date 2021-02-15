---
title: Azure 事件网格中的异地灾难恢复 | Microsoft Docs
description: 介绍 Azure 事件网格如何自动支持异地灾难恢复 (GeoDR)。
ms.topic: conceptual
ms.date: 11/19/2020
ms.openlocfilehash: 10beaf0ae25f3ed9b7bcda5961a89494b18b84d9
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94980841"
---
# <a name="server-side-geo-disaster-recovery-in-azure-event-grid"></a>Azure 事件网格中的服务器端异地灾难恢复
现在，事件网格不仅可以针对新的，而且还能针对现有的所有域、主题和事件订阅提供元数据的自动异地灾难恢复 (GeoDR)。 在整个 Azure 区域出现故障时，事件网格已将所有与事件相关的基础结构元数据同步到配对的区域。 无需你的干预，新事件就能再次开始流动。 

根据两项指标衡量灾难恢复：

- 恢复点目标 (RPO)：可以丢失数据的分钟数或小时数。
- 恢复时间目标 (RTO) ：服务可能关闭的分钟或小时数。

事件网格的自动故障转移针对元数据（事件订阅等）和数据（事件）提供不同的 RPO 和 RTO。 如果所需的规范不同于下述规范，仍可以[使用主题运行状况 API 实现你自己的客户端故障转移](custom-disaster-recovery.md)。

## <a name="recovery-point-objective-rpo"></a>恢复点目标 (RPO)
- **元数据 RPO**：0 分钟。 每当在事件网格中创建某个资源时，该资源会立即跨区域复制。 发生故障转移时，不会丢失任何元数据。
- **数据 RPO**：如果系统正常，并且在发生区域故障转移时能够跟上现有流量的进度，则事件 RPO 大约为 5 分钟。

## <a name="recovery-time-objective-rto"></a>恢复时间目标 (RTO)
- **元数据 RTO**：事件网格在 60 分钟内即会开始接受对主题和订阅发出的创建/更新/删除调用，不过，此间隔通常要短得多。
- **数据 RTO**：与元数据类似，其发生速度通常要快得多，不过，在发生区域性故障转移后，事件网格在 60 分钟内即会开始接受新流量。

> [!NOTE]
> 事件网格中元数据 GeoDR 的成本为：$0。


## <a name="next-steps"></a>后续步骤
若要实现自己的客户端故障转移逻辑，请参阅 [# 在事件网格为自定义主题构建自己的灾难恢复方案](custom-disaster-recovery.md)
