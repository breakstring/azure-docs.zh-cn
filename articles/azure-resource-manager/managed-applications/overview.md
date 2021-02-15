---
title: 托管应用程序的概述
description: 描述 Azure 托管应用程序的概念，这些应用程序提供易于用户部署和操作的云解决方案。
author: tfitzmac
ms.topic: overview
ms.date: 07/12/2019
ms.author: tomfitz
ms.openlocfilehash: 65b8c21251f092311150a50480f5abecb1ad7c17
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96434560"
---
# <a name="azure-managed-applications-overview"></a>Azure 托管应用程序概述

使用 Azure 托管应用程序，可以提供可让使用者轻松部署和操作的云解决方案。 可实施基础结构并提供持续支持。 若要向所有客户提供托管应用程序，可将其发布到 Azure 市场。 如果只希望组织中的用户使用托管应用程序，可将其发布到内部目录。 

托管应用程序类似于市场中的解决方案模板，但有一个重大差异。 在托管应用程序中，资源将部署到由应用发布者管理的资源组。 资源组在使用者的订阅中，但发布者租户中的标识有权访问该资源组。 发布者指定解决方案的持续支持费用。

> [!NOTE]
> 以前，Azure 自定义提供程序的文档包含在托管应用程序的文档中。 但该文档现已改变位置。 现在，请参阅 [Azure 自定义提供程序](../custom-providers/overview.md)。

## <a name="advantages-of-managed-applications"></a>托管应用程序的优点

托管应用程序降低了解决方案使用者面临的壁垒。 他们不需要云基础结构的专业知识即可使用你的解决方案。 使用者对关键资源的访问权限有限，在管理时不必担心会出错。 

使用托管应用程序能够与使用者建立持续的关系。 可以定义有关管理应用程序的条款，所有费用通过 Azure 计费系统进行处理。

尽管客户将这些托管应用程序部署在他们自己的订阅中，但不需要维护、更新或修复这些应用程序。 可以确保所有客户均使用批准的版本。 客户不需要培养应用程序特定的域方面的知识就能管理这些应用程序。 客户可以自动获取应用程序更新，而无需担心如何排查和诊断应用程序问题。 

IT 团队可以使用托管应用程序为组织中的用户提供预先批准的解决方案。 你知道这些解决方案符合组织标准。

托管应用程序支持 [Azure 资源的托管标识](./publish-managed-identity.md)。

## <a name="types-of-managed-applications"></a>托管应用程序的类型

可在外部或内部发布托管应用程序。

![在发布或外部发布](./media/overview/manage_app_options.png)

### <a name="service-catalog"></a>服务目录

服务目录是为组织中的用户提供的已批准解决方案内部目录。 可以使用该目录来确保满足组织标准，同时让他们为组织提供解决方案。 员工可以使用目录轻松找到 IT 部门推荐和批准的应用程序。 他们可以看到组织中其他人员与他们共享的托管应用程序。

有关发布服务目录托管应用程序的信息，请参阅[创建服务目录应用程序](publish-service-catalog-app.md)。

### <a name="marketplace"></a>市场

希望为服务计费的供应商可以通过 Azure 市场提供托管应用程序。 供应商发布应用程序后，该应用程序可供组织外部的用户使用。 通过这种方法，托管服务提供商 (MSP)、独立软件供应商 (ISV) 和系统集成商 (SI) 可向所有 Azure 客户提供其解决方案。

有关将托管应用程序发布到市场的信息，请参阅[创建市场应用程序](../../marketplace/create-new-azure-apps-offer.md)。

## <a name="resource-groups-for-managed-applications"></a>托管应用程序的资源组

通常，托管应用程序的资源位于两个资源组中。 使用者管理一个资源组，发布者管理另一个资源组。 定义托管应用程序时，发布者可指定访问级别。 发布者可以请求永久角色分配，也可以请求[实时访问](request-just-in-time-access.md)限制在某个时间段内的分配。

目前，Azure 中的所有数据提供程序都不支持限制[数据操作](../../role-based-access-control/role-definitions.md)的访问。

下图显示了发布者请求托管资源组所有者角色的方案。 发布者在此资源组中针对使用者放置了一个只读锁。 授予对托管资源组的访问权限的发布者标识不受该锁控制。

![资源组访问权限](./media/overview/access.png)

### <a name="application-resource-group"></a>应用程序资源组

此资源组保存托管应用程序实例。 此资源组只能包含一个资源。 托管应用程序的资源类型为 **Microsoft.Solutions/applications**。

使用者对资源组拥有完全访问权限，可以使用它来管理托管应用程序的生命周期。

### <a name="managed-resource-group"></a>托管资源组

此资源组包含托管应用程序所需的所有资源。 例如，此资源组包含解决方案的虚拟机、存储帐户和虚拟网络。 使用者对此资源组拥有有限的访问权限，因为使用者不会管理托管应用程序的单个资源。 发布者对此资源组的访问权限对应于托管应用程序定义中指定的角色。 例如，发布者可以请求此资源组的“所有者”或“参与者”角色。 访问权限可以是永久性的，也可以限制为特定的时间。

将[托管应用程序发布到市场](../../marketplace/create-new-azure-apps-offer.md)时，发布者可以授予使用者对托管资源组中的资源执行特定操作的能力。 例如，发布者可以指定使用者可以重启虚拟机。 仍拒绝除读取操作外的其他所有操作。 使用者通过已授权操作对托管资源组中的资源所做的更改受限于使用者租户的托管资源组范围内的 [Azure Policy](../../governance/policy/overview.md) 分配。

当使用者删除托管应用程序时，也会一并删除托管资源组。

## <a name="azure-policy"></a>Azure Policy

可以应用 [Azure Policy](../../governance/policy/overview.md) 来审核托管应用程序。 可以应用策略定义以确保托管应用程序的已部署实例满足数据和安全要求。 如果应用程序与敏感数据进行交互，请确保你已评估应当如何对该数据进行保护。 例如，如果应用程序与来自 Microsoft 365 的数据进行交互，则可应用策略定义来确保启用数据加密。

## <a name="next-steps"></a>后续步骤

在本文中，你了解了使用托管应用程序的好处。 请转到下一篇文章以创建托管应用程序定义。

> [!div class="nextstepaction"]
> [快速入门：发布 Azure 托管应用程序定义](publish-service-catalog-app.md)