---
title: 为 Microsoft Azure 地图选择适当的定价层
description: 了解 Azure Maps 定价层。 查看在哪些层提供哪些功能，以及查看选择定价层的关键注意事项。
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: cb57e424642141e73588a61d026d1ff2f1a8b096
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905292"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>在 Azure Maps 中选择适当的定价层

Azure Maps 提供了两个定价层： S0 和 S1。 本文旨在帮助你根据需要选择适当的定价层。 若要选择适当的定价层，请询问自己以下两个问题。

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>计划支持多少位并发用户？

S0 和 S1 定价层处理不同数量的数据吞吐量。 S0 定价层每秒最多可处理 50 次查询。 而 S1 层 **每秒** 处理超过50个查询。

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>计划使用哪些地理空间功能？

如果核心地理空间 Api 满足你的服务要求，请选择 S0 定价层。 如果需要更高级的应用程序功能，请考虑选择 S1 定价层。 高级功能包括：高空加混合图像、获取路由范围和批处理地理编码。 若要选择最适合你的应用程序的定价层，请查看下面的 **定价层功能** 表：

### <a name="pricing-tier-capabilities"></a>定价层功能

| 功能                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| 地图呈现                              | ✓                   | ✓       |
| 卫星图像                       |                     | ✓        |
| 搜索                                  | ✓                    | ✓        |
| 批量搜索                            |                     | ✓        |
| 路由                                   | ✓                    |✓        |
| 批处理路由                            |                    | ✓        |
| 矩阵路由                          |                     | ✓        |
| 路由范围 (等时线)                 |                     | ✓        |
| 交通                                |✓                    |✓        |
| 时区                               |✓                    |✓        |
|  (预览的地理位置)                     |✓                   |✓        |
| 空间操作                        |                    |✓        |
| 地理围栏                                |                    |✓        |
| Azure Maps 数据 (预览)                 |                     | ✓        |
| 移动性 (预览)                        |                     | ✓        |
| 天气 (预览)                         |✓                    |✓        |
|  Creator (预览)                          |                   |✓        |
|  提升 (预览)                         |                   |✓        |

请考虑以下其他要点：

* 你拥有哪种类型的企业？
* 应用程序的重要程度如何？

### <a name="pricing-tier-targeted-customers"></a>定价层目标客户

请参阅“定价层目标客户”表，以便更好地了解 S0 和 S1 定价层。 有关详细信息，请参阅 [Azure Maps 定价](https://azure.microsoft.com/pricing/details/azure-maps/)。 

| 定价层  |     目标客户                                                                |
|-----------------|:-----------------------------------------------------------------------------------------|
| S0            |    S0 定价层适用于所有生产阶段的应用程序：从概念证明开发和初期阶段测试到应用程序生产和部署。 但是，此层专为小规模开发而设计，同时为具有低并发用户的客户或两者。 
| S1            |    S1 定价层适用于具有大规模企业应用程序、任务关键型应用程序或大量并发用户的客户。 它也适用于那些需要高级地理空间服务的客户。

## <a name="next-steps"></a>后续步骤

了解有关如何查看和更改定价层的详细信息：

> [!div class="nextstepaction"]
> [管理定价层](how-to-manage-pricing-tier.md)
