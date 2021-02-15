---
title: 查看和管理服务提供商
description: 客户可以使用 Azure 门户中的“服务提供商”页面来查看有关服务提供商、服务提供商产品/服务和委派资源的信息。
ms.date: 12/16/2020
ms.topic: how-to
ms.openlocfilehash: 5ee897503c997ab10fdb489f7921c9d2d001e472
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2020
ms.locfileid: "97617198"
---
# <a name="view-and-manage-service-providers"></a>查看和管理服务提供商

[Azure 门户](https://portal.azure.com)中的 "**服务提供商**" 页可让客户对使用 [Azure Lighthouse](../overview.md)的服务提供商进行控制和查看。 客户可以查看有关服务提供商的详细信息、委派特定的资源、购买新的服务提供商、删除服务提供商访问等。

> [!TIP]
> 尽管我们将在此处参考服务提供商和客户，但 [管理多个租户的企业](../concepts/enterprise.md) 可以使用相同的过程来整合其管理体验。

要访问 Azure 门户中的“服务提供者”页面，客户可以选择“所有服务”，然后搜索“服务提供商”并选择它。 他们还可以通过在 Azure 门户顶部附近的搜索框中输入 "服务提供商" 或 "Azure Lighthouse" 来找到它。

> [!NOTE]
> 若要查看 " **服务提供程序** " 页，客户租户中的用户必须具有 [读取器内置角色](../../role-based-access-control/built-in-roles.md#reader) (或其他包含读者访问) 的内置角色。
>
> 若要添加或更新产品/服务、委托资源和删除提供程序，用户必须具有订阅的 [所有者内置角色](../../role-based-access-control/built-in-roles.md#owner) 。

请记住，" **服务提供商** " 页仅显示有关可通过 Azure Lighthouse 访问客户的订阅或资源组的服务提供商的信息。 如果客户与不使用 Azure Lighthouse 访问客户资源的其他服务提供商合作，此处将不会显示有关这些服务提供商的信息。

> [!TIP]
> 服务提供商可以通过导航到 Azure 门户中的 **"我的客户** " 来查看有关客户的信息。 有关详细信息，请参阅[查看和管理客户及委派资源](view-manage-customers.md)。

## <a name="view-service-provider-details"></a>查看服务提供商详情

若要查看有关服务提供商的详细信息，客户可以在 "**服务提供商**" 页面左侧选择 "**服务提供商产品**/服务"。

对于每个服务提供商产品/服务，客户都可看到服务提供商的名称和与之关联的产品/服务，以及客户在登记过程中输入的名称。

在“委派”列中，客户可看到针对该产品/服务已委派给服务提供商的订阅数和/或资源组数。 服务提供商将能够根据产品/服务中指定的访问级别访问和管理这些订阅和/或资源组。

## <a name="add-or-remove-service-provider-offers"></a>添加或删除服务提供商产品/服务

客户通过选择 "**添加产品**"，可以从 "**服务提供商优惠**" 页中添加新的服务提供商优惠。 服务提供商必须已为此客户发布了产品/服务。 然后，客户可以从“专用产品/服务”屏幕中选择该产品/服务，然后选择“创建”。

如果客户想要删除服务提供商提供的产品/服务，则可以随时通过选择该产品的行中的 "垃圾桶" 图标来执行此操作。 确认删除后，该服务提供商将无法再访问之前为该产品/服务委派的客户资源。

## <a name="delegate-resources"></a>委派资源

在服务提供商可以访问和管理客户的资源之前，必须对其进行委派。 如果客户接受了产品/服务，但尚未委托任何资源，则他们会在 **服务提供商优惠** 部分的顶部看到一条说明。 这让客户知道他们需要在服务提供商可以访问客户的任何资源之前采取措施。

委派订阅或资源组：

1. 选中包含服务提供商、产品/服务和名称的行的复选框。 然后选择屏幕顶部的“委派资源”。
1. 在“委派资源”页面的“产品/服务详细信息”部分中，查看有关服务提供商和产品/服务的详细信息。 要查看产品/服务的角色分配，请选择“单击此处查看所选产品/服务的详情信息”。
1. 在“委派”部分中，选择“委派订阅”或“委派资源组”。
1. 选择要为此产品/服务委派的订阅和/或资源组，然后选择“添加”。
1. 选中页面底部的复选框，确认你想要授予此服务提供商对所选资源的访问权限，然后选择“委派”。

## <a name="update-service-provider-offers"></a>更新服务提供商提供的服务

在客户添加了产品/服务后，服务提供商可以将同一产品/服务的更新版本发布到 Azure Marketplace。 例如，他们可能要添加新的角色定义。 如果发布了新版本的产品/服务，则 " **服务提供商提供** " 页面将在该产品的行中显示 "更新" 图标。 客户可以选择此图标来查看当前版本的产品/服务和新版本的产品/服务之间的差异。

 ![更新提议图标](../media/update-offer.jpg)

查看更改后，客户可以选择是否更新为新版本。 完成此操作后，在新版本中指定的授权和其他设置将适用于已为该产品/服务委派的所有订阅和/或资源组。

## <a name="view-delegations"></a>查看委派

委派表示为客户委派的资源授予服务提供者权限的角色分配。 若要查看此信息，请选择“服务提供程序”页面左侧的“委派”。

页面顶部的筛选器使你可以对委派信息进行排序和分组。 你还可以按特定的客户、产品/服务或关键字进行筛选。

> [!NOTE]
> 当查看 Azure 门户或通过 Api [中的委托作用域的角色分配](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-at-a-scope) 时，客户不会看到这些角色分配，或已授予这些角色的服务提供商租户中的任何用户。

## <a name="audit-delegations-in-your-environment"></a>审核环境中的委派

客户可能需要了解已委派给 Azure Lighthouse 的订阅和/或资源组。 对于具有大量订阅的客户，或者拥有执行管理任务的多个用户，此操作特别有用。

我们提供了一个 [Azure 策略内置策略定义](../../governance/policy/samples/built-in-policies.md#lighthouse) ，用于 [审核作用域到管理租户的委派](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/Lighthouse_Delegations_Audit.json)。 你可以将此策略分配到包含你要审核的所有订阅的管理组。 检查此策略的符合性时，将在策略分配到的管理组中 (任何委派的订阅和/或资源组，) 将显示为不符合要求的状态。 然后，可以查看结果，并确认没有任何意外委托。

另一个 [内置策略定义](../../governance/policy/samples/built-in-policies.md#lighthouse) 使你 [能够将委派限制到特定的管理租户](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/AllowCertainManagingTenantIds_Deny.json)。 此策略同样可以应用于包含要限制委派的任何订阅的管理组。 部署策略后，将拒绝将订阅委托给指定的租户的任何尝试。

有关如何分配策略和查看符合性状态结果的详细信息，请参阅 [快速入门：创建策略分配](../../governance/policy/assign-policy-portal.md)。

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure Lighthouse ](../overview.md)。
- 了解如何 [审核服务提供程序活动](view-service-provider-activity.md)。
- 了解服务提供商如何在 Azure 门户中的 "**我的客户**" 页上 [查看和管理客户](view-manage-customers.md)。
