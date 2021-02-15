---
title: 使用服务标记
titleSuffix: Azure SignalR Service
description: 使用服务标记允许到 Azure SignalR 服务的出站流量
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: article
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: aaa97c2cb062f30b1260ec7f80f85a3caccf932f
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2020
ms.locfileid: "92152316"
---
# <a name="use-service-tags-for-azure-signalr-service"></a>使用 Azure SignalR 服务的服务标记

配置[网络安全组](../virtual-network/network-security-groups-overview.md#network-security-groups)时，可以使用 Azure SignalR 服务的[服务标记](../virtual-network/network-security-groups-overview.md#service-tags)。 它允许你定义到 Azure SignalR 服务终结点的出站网络安全规则，而无需对 IP 地址进行硬编码。

Azure SignalR 服务管理这些服务标记。 不能创建自己的服务标记或修改现有的服务标记。 Microsoft 管理与服务标记匹配的这些地址前缀，并在地址更改时自动更新服务标记。

## <a name="use-service-tag-on-portal"></a>使用门户上的服务标记

可以通过添加新的出站网络安全规则来允许到 Azure SignalR 服务的出站流量：

1. 请参阅网络安全组。

1. 单击名为 " **出站安全规则**" 的 "设置" 菜单。

1. 单击顶部的按钮 " **+ 添加** "。

1. 在 "**目标**" 下选择 "**服务标记**"。

1. 选择 **AzureSignalR** 下的 " **目标服务标记**"。

1. 填写 "**目标端口范围**" 中的**443** 。

    ![创建出站安全规则](media/howto-service-tags/portal-add-outbound-security-rule.png)

1. 根据需要调整其他字段。

1. 单击“添加”  。


## <a name="next-steps"></a>后续步骤

- [网络安全组：服务标记](../virtual-network/network-security-groups-overview.md#security-rules)