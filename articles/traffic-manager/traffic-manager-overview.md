---
title: Azure 流量管理器 | Microsoft Docs
description: 本文概述了 Azure 流量管理器。 了解它是否是针对应用程序进行用户流量负载均衡的正确选择。
services: traffic-manager
author: duongau
ms.service: traffic-manager
customer intent: As an IT admin, I want to learn about Traffic Manager and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/19/2021
ms.author: duau
ms.openlocfilehash: 09b82eed5ad6a9ad121ca56d197eb9c003d027f5
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624903"
---
# <a name="what-is-traffic-manager"></a>什么是流量管理器？
Azure 流量管理器是基于 DNS 的流量负载均衡器。 此服务让你可以在全球 Azure 区域将流量分布到面向公众的应用程序。 流量管理器还提供具有高可用性和快速响应能力的公共终结点。

流量管理器根据流量路由方法，使用 DNS 将客户端请求定向到合适的服务终结点。 流量管理器还为每个终结点提供运行状况监视功能。 终结点可以是托管在 Azure 内部或外部的任何面向 Internet 的服务。 流量管理器提供多种[流量路由方法](traffic-manager-routing-methods.md)和[终结点监视选项](traffic-manager-monitoring.md)来满足不同的应用程序需求和自动故障转移模型。 流量管理器能够灵活应对故障，包括整个 Azure 区域的故障。

>[!NOTE]
> Azure 为方案提供了一套完全托管的负载均衡解决方案。 若要寻求传输层安全性 (TLS) 协议终止（“SSL 卸载”）或每个 HTTP/HTTPS 请求的应用层处理，请查看[应用程序网关](../application-gateway/overview.md)。 如果想要实现区域负载均衡，请查看[负载均衡器](../load-balancer/load-balancer-overview.md)。 端到端场景可从结合所需的解决方案中受益。
>
> 有关 Azure 负载平衡选项的比较，请参阅 [Overview of load-balancing options in Azure](/azure/architecture/guide/technology-choices/load-balancing-overview)（Azure 中的负载平衡选项概述）。

流量管理器提供了以下功能：

## <a name="increase-application-availability"></a>提高应用程序可用性

流量管理器可以监视终结点，在终结点发生故障时提供自动故障转移，实现关键应用程序的高可用性。
    
## <a name="improve-application-performance"></a>改善应用程序性能

在 Azure 中，可以运行位于世界各地的数据中心内的云服务和网站。 流量管理器可以将流量以尽可能低的延迟定向到终结点，从而提高网站的响应能力。

## <a name="service-maintenance-without-downtime"></a>在不停机的情况下进行服务维护

无需停机即可对应用程序进行计划内维护。 当维护正在进行时，流量管理器可以将流量定向到备用终结点。

## <a name="combine-hybrid-applications"></a>组合混合应用程序

流量管理器支持外部非 Azure 终结点，因此可以用于混合云部署和本地部署，包括“[云爆发](https://azure.microsoft.com/overview/what-is-cloud-bursting/)”、“云迁移”和“云故障转移”方案。

## <a name="distribute-traffic-for-complex-deployments"></a>分发复杂部署的流量

使用[嵌套式流量管理器配置文件](traffic-manager-nested-profiles.md)，可以组合使用多种流量路由方法来创建复杂、灵活且可缩放的规则来满足更大、更复杂部署的需求。

## <a name="pricing"></a>定价

有关定价信息，请参阅[流量管理器定价](https://azure.microsoft.com/pricing/details/traffic-manager/)。


## <a name="next-steps"></a>后续步骤

- 了解如何[创建流量管理器配置文件](./quickstart-create-traffic-manager-profile.md)。
- 了解[流量管理器工作原理](traffic-manager-how-it-works.md)。
- 查看流量管理器[常见问题解答](traffic-manager-FAQs.md)。