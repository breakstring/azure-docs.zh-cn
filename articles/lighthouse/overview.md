---
title: 什么是 Azure Lighthouse？
description: 借助 Azure Lighthouse，服务提供商可为其客户提供自动化程度更高且更高效的规模级托管服务。
ms.date: 12/16/2020
ms.topic: overview
ms.openlocfilehash: b9215f42e3ad3b7517d14fdaee710a31680ce453
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2020
ms.locfileid: "97617147"
---
# <a name="what-is-azure-lighthouse"></a>什么是 Azure Lighthouse？

Azure Lighthouse 允许跨租户和多租户管理，允许跨资源和租户提升自动化程度、可伸缩性并增强管理。

借助 Azure Lighthouse，服务提供商可以使用 Azure 平台内置的全面而强大的管理工具来提供托管服务。 客户对可访问其租户的人员、可以访问的资源以及可执行的操作保持控制。 此产品/服务还可让[企业 IT 组织](concepts/enterprise.md)跨多个租户管理资源。

![Azure Lighthouse 概述示意图](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>优点

Azure Lighthouse 可帮助服务提供商有效地生成和交付托管服务。 优点包括：

- **大规模管理**：用于管理客户资源的客户参与和生命周期操作更加简单且更具可缩放性。 现有的 API、管理工具和工作流可以与委托的资源（包括在 Azure 外部托管的计算机）配合使用，而不管资源位于哪个区域。
- **为客户提供更好的可见性和控制**：客户可精确控制他们委托管理的范围以及允许的权限。 如果需要，他们可以审核服务提供商操作并完全删除访问权限。
- **全面统一的平台工具**：我们的工具体验解决了重要的服务提供商方案，其中包括多种许可模型，如 EA、CSP 和即用即付。 Azure Lighthouse 适用于现有工具和 API、许可模型、[Azure 托管应用程序](concepts/managed-applications.md)和合作伙伴计划，如[云解决方案提供商计划 (CSP)](/partner-center/csp-overview)。 可以将 Azure Lighthouse 集成到现有的工作流和应用程序中，并通过[关联合作伙伴 ID](./how-to/partner-earned-credit.md) 来跟踪你对客户参与的影响。

使用 Azure Lighthouse 管理 Azure 资源不会产生额外的费用。 任何 Azure 客户或合作伙伴都可以使用 Azure Lighthouse。

## <a name="capabilities"></a>功能

Azure Lighthouse 提供了多种方法来协助简化参与和管理：

- **Azure 委派资源管理**：无需切换上下文和控制平面即可 [从自己的租户中安全地管理客户的 Azure 资源](concepts/azure-delegated-resource-management.md)。 可以将客户订阅和资源组委托给管理租户中的指定用户和角色，并可以根据需要删除访问权限。
- **新 Azure 门户体验**：在 Azure 门户中的 [“我的客户”页面](how-to/view-manage-customers.md)中查看跨租户信息。 使用相应的[“服务提供商”页面](how-to/view-manage-service-providers.md)，客户可以查看和管理其服务提供商访问。
- **Azure 资源管理器模板**：使用 ARM 模板 [加入委托的客户资源](how-to/onboard-customer.md)和 [执行跨租户管理任务](samples/index.md)。
- **Azure 市场中的托管服务产品**：通过专用或公开产品/服务 [为客户提供服务](concepts/managed-services-offers.md)，并将其自动加入 Azure Lighthouse。

> [!TIP]
> 一个类似的产品 [Microsoft 365 Lighthouse](https://techcommunity.microsoft.com/t5/small-and-medium-business-blog/announcing-microsoft-365-lighthouse-for-managed-service/ba-p/1698181) 可帮助 IT 合作伙伴大规模载入、监视和管理其 Microsoft 365 客户。 Microsoft 365 Lighthouse 当前为个人预览版。

## <a name="next-steps"></a>后续步骤

- 了解 [Azure 委派资源管理](concepts/azure-delegated-resource-management.md)。
- 了解[跨租户管理体验](concepts/cross-tenant-management-experience.md)。
- 了解如何[在企业内使用 Azure Lighthouse](concepts/enterprise.md)。
- 查看 Azure Lighthouse 的[可用性](https://azure.microsoft.com/global-infrastructure/services/?products=azure-lighthouse&regions=all)和 [FedRAMP 和 DoD CC SRG 审核范围](../azure-government/compliance/azure-services-in-fedramp-auditscope.md)详细信息。
