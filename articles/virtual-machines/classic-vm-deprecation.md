---
title: 我们将于 2023 年 3 月 1 日停用 Azure VM（经典）
description: 本文简要概述了使用经典部署模型创建的虚拟机 (VM) 的停用情况。
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: 7ed2a672f0f7149240e799b5529a7a3a6836a702
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499299"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>请于 2023 年 3 月 1 日之前将 IaaS 资源迁移到 Azure 资源管理器 

2014 年，我们在 [Azure 资源管理器](https://azure.microsoft.com/features/resource-manager/)上推出了基础结构即服务 (IaaS)。 从那时起，我们一直在强化功能。 由于 Azure 资源管理器现具有完整的 IaaS 功能和其他改进，因此我们在 2020 年 2 月 28 日弃用了通过 [Azure Service Manager](./migration-classic-resource-manager-faq.md#what-is-azure-service-manager-and-what-does-it-mean-by-classic) (ASM) 管理 IaaS 虚拟机 (VM) 的功能。 此功能将于 2023 年 3 月 1 日完全停用。 

目前，大约有 90% 的 IaaS VM 在使用 Azure 资源管理器。 如果你在通过 ASM 使用 IaaS 资源，请立即开始计划迁移。 为了使用 [Azure 资源管理器](../azure-resource-manager/management/index.yml)请在 2023 年 3 月 1 日之前完成迁移。

使用经典部署模型创建的 VM 将按照[新式生命周期策略](https://support.microsoft.com/help/30881/modern-lifecycle-policy)进行停用。

## <a name="how-does-this-affect-me"></a>这对我有何影响？ 

- 自 2020 年 2 月 28 日起，截至 2020 年 2 月仍未通过 ASM 使用 IaaS VM 的客户无法再创建 VM（经典）。 
- 在 2023 年 3 月 1 日，客户将无法再使用 ASM 启动 IaaS VM。 所有仍在运行或已分配的 VM 都将停止并解除分配。 
- 在 2023 年 3 月 1 日，尚未迁移到 Azure 资源管理器的订阅将收到通知，其中附有关于删除所有剩余 VM（经典）的时间安排。  

本次停用不影响下列 Azure 服务和功能： 
- Azure 云服务 
- VM（经典）未使用的存储帐户 
- VM（经典）未使用的虚拟网络 
- 其他经典资源

## <a name="what-actions-should-i-take"></a>我应该采取什么措施？ 

立即开始规划到 Azure 资源管理器的迁移。 

1. 列出所有受影响的 VM： 

   - 在订阅中，只有 [Azure 门户的 VM 窗格](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ClassicCompute%2FVirtualMachines)上类型为“虚拟机（经典）”的 VM 才受到影响。 
   - 还可使用[门户](https://portal.azure.com/#blade/HubsExtension/ArgQueryBlade/query/resources%0A%7C%20where%20type%20%3D%3D%20%22microsoft.classiccompute%2Fvirtualmachines%22)或 [PowerShell](../governance/resource-graph/concepts/work-with-data.md) 查询 Azure Resource Graph，进而查看已选订阅的所有带标记的 VM（经典）及相关信息的列表。 
   - 在 2020 年 2 月 8 日和 9 月 2 日，我们向订阅所有者发送了电子邮件，其中附有包含这些 VM（经典）的所有订阅的列表。 请使用它们来生成此列表。 

1. [详细了解](./migration-classic-resource-manager-overview.md)如何将 [Linux](./migration-classic-resource-manager-plan.md) 和 [Windows](./migration-classic-resource-manager-plan.md) VM（经典）迁移到 Azure 资源管理器。 若要了解详细信息，请参阅[有关从经典部署模型迁移到 Azure 资源管理器部署模型的常见问题](./migration-classic-resource-manager-faq.md)。

1. 建议使用[平台支持迁移工具](./migration-classic-resource-manager-overview.md)开始规划，按照验证、准备和提交这三个简单步骤迁移现有 VM。 此工具旨在迁移 VM 的同时确保不停机或停机时间最少。 

   1. 第一步是验证，这不影响现有部署，并提供列表可查看所有不受支持的迁移方案。 
   1. 请浏览[暂时解决方法列表](./migration-classic-resource-manager-overview.md#unsupported-features-and-configurations)来修复部署，使其做好迁移准备。 
   1. 理想情况下，解决所有验证错误后，在准备和提交步骤中不会遇到任何问题。 提交成功后，部署将实时迁移到 Azure 资源管理器，然后可通过 Azure 资源管理器公开的新的 API 进行管理。 

   如果迁移工具不适合迁移，可探索[其他计算服务/产品](/azure/architecture/guide/technology-choices/compute-decision-tree)来进行迁移。 有许多 Azure 计算服务/产品，而且它们各不相同，因此我们无法为其提供平台支持的迁移路径。  

1. 如果有技术问题、疑惑或在将订阅添加到允许列表时需要帮助，请[联系支持人员](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"8a82f77d-c3ab-7b08-d915-776b4ff64ff4"})。

1. 请尽快完成迁移，从而避免业务影响，并利用 Azure 资源管理器改进后的性能、安全性和新功能。 

## <a name="what-resources-are-available-for-this-migration"></a>哪些资源可用于此迁移？

- [Microsoft Q&](/answers/topics/azure-virtual-machines-migration.html)：用于迁移的 microsoft 和社区支持。

- [Azure 迁移支持](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"1135e3d0-20e2-aec5-4ef0-55fd3dae2d58"})：在迁移过程中为技术协助提供专用支持团队。

- [Microsoft 快速跟踪](https://www.microsoft.com/fasttrack)：快速跟踪可以通过计划 & 执行此迁移来帮助符合条件的客户。 [您自己](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fprograms%2Fazure-fasttrack%2F%23nomination&data=02%7C01%7CTanmay.Gore%40microsoft.com%7C3e75bbf3617944ec663a08d85c058340%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637360526032558561&sdata=CxWTVQQPVWNwEqDZKktXzNV74pX91uyJ8dY8YecIgGc%3D&reserved=0)。  

- 如果你的公司/组织已与 Microsoft 合作，或与 Microsoft 代表 (如云解决方案架构师 (Csa) 或技术客户经理 (Tam) #