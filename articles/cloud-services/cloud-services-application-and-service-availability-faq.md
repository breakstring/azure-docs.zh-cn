---
title: 应用程序和服务可用性常见问题解答
description: 本文列出了一些关于 Microsoft Azure 云服务配应用程序和服务可用性问题的常见问题解答。
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: c1a5b63a33f951857bd4837380c1465af5b7583e
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742890"
---
# <a name="application-and-service-availability-issues-for-azure-cloud-services-classic-frequently-asked-questions-faqs"></a> (经典) 的 Azure 云服务的应用程序和服务可用性问题：常见问题解答 (常见问题) 

> [!IMPORTANT]
> [Azure 云服务 (扩展支持) ](../cloud-services-extended-support/overview.md) 是适用于 Azure 云服务产品的新的基于 azure 资源管理器的部署模型。进行此更改后，基于 Azure Service Manager 的部署模型运行的 Azure 云服务已重命名为云服务 (经典) ，所有新部署应使用 [云服务 (扩展支持) ](../cloud-services-extended-support/overview.md)。

本文包括关于 [Microsoft Azure 云服务](https://azure.microsoft.com/services/cloud-services)应用程序和服务可用性问题的常见问题解答。 还可以参阅[云服务 VM 大小页面](cloud-services-sizes-specs.md)，了解大小信息。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="my-role-got-recycled-was-there-any-update-rolled-out-for-my-cloud-service"></a>我的角色已被回收。 是否针对我的云服务推出了任何更新？
Microsoft 大约每月一次为 Microsoft Azure PaaS VM 发布新的来宾 OS 版本。 来宾 OS 只不过是此类更新的其中一种。 发布可能会受到其他许多因素的影响。 此外，Azure 在数十万台计算机上运行。 因此，无法预测重新启动角色的确切日期和时间。 我们使用具备的最新信息更新来宾 OS 更新 RSS 源，但你应该考虑到，报告的时间是近似值。 我们意识到这对于客户构成问题，并正在致力于限制重新启动或为重新启动精确定时的计划。

有关最新来宾 OS 更新的完整详细信息，请参阅 [Azure 来宾 OS 版本和 SDK 兼容性矩阵](cloud-services-guestos-update-matrix.md)。

有关重启的有用信息以及有关来宾和主机 OS 更新的技术详情链接，请阅读 MSDN 博客文章[角色实例因 OS 升级而重启](/archive/blogs/kwill/role-instance-restarts-due-to-os-upgrades)。

## <a name="why-does-the-first-request-to-my-cloud-service-after-the-service-has-been-idle-for-some-time-take-longer-than-usual"></a>云服务空闲一段时间后，为何对服务发出的第一个请求花费的时间比平时要长？
当 Web 服务器收到第一个请求时，它会先重新编译代码，然后处理请求。 这就是第一个请求花费的时间长于其他请求的原因。 默认情况下，当用户处于非活动状态时，应用池将会关闭。 此外，在默认情况下，应用池会每隔 1,740 分钟（29 小时）回收一次。

可能会定期回收 Internet Information Services (IIS) 应用程序池，以免发生可能会导致应用程序崩溃、挂起或内存泄漏的不稳定状态。

以下文档可帮助你了解和缓解此问题：
* [修复 IIS 初始加载速度较慢的问题](https://stackoverflow.com/questions/13386471/fixing-slow-initial-load-for-iis)
* [应用池回收后，IIS 7.5 Web 应用程序的第一个请求速度很慢](https://stackoverflow.com/questions/13917205/iis-7-5-web-application-first-request-after-app-pool-recycle-very-slow)

若要更改 IIS 的默认行为，需要使用启动任务，因为如果向 Web 角色实例手动应用更改，更改最终会丢失。

有关详细信息，请参阅[如何配置和运行云服务的启动任务](cloud-services-startup-tasks.md)。