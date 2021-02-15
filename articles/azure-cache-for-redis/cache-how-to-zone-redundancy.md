---
title: '为 Redis (预览版的 Azure Cache 启用区域冗余) '
description: 了解如何为高级和企业级 Azure 缓存的 Redis 实例设置区域冗余
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 0cb7ee5b9fa02e726d03bf1ae9935c07ded6e4a6
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93088006"
---
# <a name="enable-zone-redundancy-for-azure-cache-for-redis-preview"></a>为 Redis (预览版的 Azure Cache 启用区域冗余) 
本文介绍如何使用 Azure 门户配置区域冗余的 Azure 缓存实例。

适用于 Redis 标准、高级和企业层的 Azure 缓存通过在两个专用虚拟机上托管每个缓存来提供内置冗余)  (Vm。 即使这些 Vm 位于不同的 [Azure 容错域和更新域](../virtual-machines/manage-availability.md) 中，并且具有高可用性，它们也容易受到数据中心级故障的影响。 适用于 Redis 的 Azure 缓存还支持其高级和企业层中的区域冗余。 区域冗余缓存在跨多个 [可用性区域](../virtual-machines/manage-availability.md#use-availability-zones-to-protect-from-datacenter-level-failures)的 vm 上运行。 它提供更高的复原能力和可用性。

## <a name="prerequisites"></a>先决条件
* Azure 订阅 - [创建免费帐户](https://azure.microsoft.com/free/)

> [!NOTE]
> 此功能目前处于预览阶段，如果你感兴趣， [请](mailto:azurecache@microsoft.com) 联系我们。
>

## <a name="create-a-cache"></a>创建缓存
若要创建缓存，请执行以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com) ，然后选择 " **创建资源** "。
  
1. 在“新建”页上选择“数据库”，然后选择“Azure Cache for Redis”。

    :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="选择 Azure Cache for Redis。&quot;:::
   
1. 在 &quot; **基本** 信息&quot; 页上，配置新缓存的设置。
   
    | 设置      | 建议的值  | 说明 |
    | ------------ |  ------- | -------------------------------------------------- |
    | **订阅** | 选择订阅。 | 要在其下创建此新的 Azure Cache for Redis 实例的订阅。 | 
    | **资源组** | 选择资源组，或选择 &quot; **新建&quot; 并输入** 新的资源组名称。 | 要在其中创建缓存和其他资源的资源组的名称。 将所有应用资源放入一个资源组可以轻松地统一管理或删除这些资源。 | 
    | **DNS 名称** | 输入任何全局唯一的名称。 | 缓存名称必须是包含 1 到 63 个字符的字符串，只能包含数字、字母或连字符。 该名称必须以数字或字母开头和结尾，且不能包含连续的连字符。 缓存实例的主机名是 *\<DNS name> .redis.cache.windows.net* 。 | 
    | **位置** | 选择一个位置。 | 选择与要使用该缓存的其他服务靠近的[区域](https://azure.microsoft.com/regions/)。 |
    | **缓存类型** | 选择 &quot; [高级&quot; 或 &quot;企业&quot; 层](https://azure.microsoft.com/pricing/details/cache/) 缓存。 |  定价层决定可用于缓存的大小、性能和功能。 有关详细信息，请参阅[用于 Redis 的 Azure 缓存概述](cache-overview.md)。 |
   
1. 在 &quot; **高级** &quot; 页上，对于 &quot;高级&quot; 层缓存，选择 " **副本计数** "。
   
    :::image type="content" source="media/cache-how-to-multi-replicas/create-multi-replicas.png" alt-text="选择 Azure Cache for Redis。&quot;:::
   
1. 在 &quot; **基本** 信息&quot; 页上，配置新缓存的设置。
   
    | 设置      | 建议的值  | 说明 |
    | ------------ |  ------- | -------------------------------------------------- |
    | **订阅** | 选择订阅。 | 要在其下创建此新的 Azure Cache for Redis 实例的订阅。 | 
    | **资源组** | 选择资源组，或选择 &quot; **新建&quot; 并输入** 新的资源组名称。 | 要在其中创建缓存和其他资源的资源组的名称。 将所有应用资源放入一个资源组可以轻松地统一管理或删除这些资源。 | 
    | **DNS 名称** | 输入任何全局唯一的名称。 | 缓存名称必须是包含 1 到 63 个字符的字符串，只能包含数字、字母或连字符。 该名称必须以数字或字母开头和结尾，且不能包含连续的连字符。 缓存实例的主机名是 *\<DNS name> .redis.cache.windows.net* 。 | 
    | **位置** | 选择一个位置。 | 选择与要使用该缓存的其他服务靠近的[区域](https://azure.microsoft.com/regions/)。 |
    | **缓存类型** | 选择 &quot; [高级&quot; 或 &quot;企业&quot; 层](https://azure.microsoft.com/pricing/details/cache/) 缓存。 |  定价层决定可用于缓存的大小、性能和功能。 有关详细信息，请参阅[用于 Redis 的 Azure 缓存概述](cache-overview.md)。 |
   
1. 在 &quot; **高级** &quot; 页上，对于 &quot;高级&quot; 层缓存，选择 " **可用性区域** "。 
   
    :::image type="content" source="media/cache-how-to-zone-redundancy/create-zones.png" alt-text="选择 Azure Cache for Redis。&quot;:::
   
1. 在 &quot; **基本** 信息&quot; 页上，配置新缓存的设置。
   
    | 设置      | 建议的值  | 说明 |
    | ------------ |  ------- | -------------------------------------------------- |
    | **订阅** | 选择订阅。 | 要在其下创建此新的 Azure Cache for Redis 实例的订阅。 | 
    | **资源组** | 选择资源组，或选择 &quot; **新建&quot; 并输入** 新的资源组名称。 | 要在其中创建缓存和其他资源的资源组的名称。 将所有应用资源放入一个资源组可以轻松地统一管理或删除这些资源。 | 
    | **DNS 名称** | 输入任何全局唯一的名称。 | 缓存名称必须是包含 1 到 63 个字符的字符串，只能包含数字、字母或连字符。 该名称必须以数字或字母开头和结尾，且不能包含连续的连字符。 缓存实例的主机名是 *\<DNS name> .redis.cache.windows.net* 。 | 
    | **位置** | 选择一个位置。 | 选择与要使用该缓存的其他服务靠近的[区域](https://azure.microsoft.com/regions/)。 |
    | **缓存类型** | 选择 &quot; [高级&quot; 或 &quot;企业&quot; 层](https://azure.microsoft.com/pricing/details/cache/) 缓存。 |  定价层决定可用于缓存的大小、性能和功能。 有关详细信息，请参阅[用于 Redis 的 Azure 缓存概述](cache-overview.md)。 |
   
1. 在 &quot; **高级** &quot; 页上，对于 &quot;高级&quot; 层缓存，选择 ":::

1. 将其他选项保留为其默认设置。 

    > [!NOTE]
    > 区域冗余支持仅适用于非群集和非异地复制的缓存。 此外，它不支持专用链接、缩放、数据暂留或导入/导出。
    >

1. 单击“创建”。  
   
    创建缓存需要花费片刻时间。 可以在 Azure Cache for Redis 的“概述”页上监视进度。  如果“状态”显示为“正在运行”，则表示该缓存可供使用。 
   
    > [!NOTE]
    > 创建缓存后，无法更改可用性区域。
    >

## <a name="next-steps"></a>后续步骤
了解有关 Azure Cache for Redis 功能的详细信息。

> [!div class="nextstepaction"]
> [Azure Cache for Redis 高级服务层](cache-overview.md#service-tiers)