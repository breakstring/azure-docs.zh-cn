---
title: 将 Azure Policy 大规模部署到委托订阅
description: 了解 Azure Lighthouse 如何允许跨多个租户部署策略定义和策略分配。
ms.date: 11/09/2020
ms.topic: how-to
ms.openlocfilehash: 5af938c61ad3e42e36360a15c6011b54fa1e823d
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2020
ms.locfileid: "94412062"
---
# <a name="deploy-azure-policy-to-delegated-subscriptions-at-scale"></a>将 Azure Policy 大规模部署到委托订阅

作为服务提供商，你可能已将多个客户租户载入 [Azure Lighthouse](../overview.md)。 Azure Lighthouse 允许服务提供商同时在多个租户之间大规模执行操作，从而提高管理任务的效率。

本主题说明如何按照 [Azure Policy](../../governance/policy/index.yml) 使用 PowerShell 命令在多个租户中部署策略定义和策略分配。 在此示例中，策略定义确保通过允许仅 HTTPS 流量来保护存储帐户。

> [!TIP]
> 尽管我们指的是本主题中的服务提供商和客户，但 [管理多个租户的企业](../concepts/enterprise.md) 可以使用相同的过程。

## <a name="use-azure-resource-graph-to-query-across-customer-tenants"></a>使用 Azure Resource Graph 在客户租户之间执行查询

你可以使用 [Azure Resource Graph](../../governance/resource-graph/index.yml) 在所管理的客户租户的所有订阅中执行查询。 在此示例中，我们将确定这些订阅中当前不需要 HTTPS 通信的任何存储帐户。  

```powershell
$MspTenant = "insert your managing tenantId here"

$subs = Get-AzSubscription

$ManagedSubscriptions = Search-AzGraph -Query "ResourceContainers | where type == 'microsoft.resources/subscriptions' | where tenantId != '$($mspTenant)' | project name, subscriptionId, tenantId" -subscription $subs.subscriptionId

Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | project name, location, subscriptionId, tenantId, properties.supportsHttpsTrafficOnly" -subscription $ManagedSubscriptions.subscriptionId | convertto-json
```

## <a name="deploy-a-policy-across-multiple-customer-tenants"></a>跨多个客户租户部署策略

以下示例演示如何使用 [Azure 资源管理器模板](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-enforce-https-storage/enforceHttpsStorage.json)跨多个客户租户中的委托订阅部署策略定义和策略分配。 此策略定义要求所有存储帐户都使用 HTTPS 流量，阻止创建不符合要求并标记现有存储帐户的任何新存储帐户，而不将设置为不符合。

```powershell
Write-Output "In total, there are $($ManagedSubscriptions.Count) delegated customer subscriptions to be managed"

foreach ($ManagedSub in $ManagedSubscriptions)
{
    Select-AzSubscription -SubscriptionId $ManagedSub.subscriptionId

    New-AzSubscriptionDeployment -Name mgmt `
                     -Location eastus `
                     -TemplateUri "https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/templates/policy-enforce-https-storage/enforceHttpsStorage.json" `
                     -AsJob
}
```

## <a name="validate-the-policy-deployment"></a>验证策略部署

部署 Azure 资源管理器模板之后，可以通过尝试在某个委派的订阅中创建 **EnableHttpsTrafficOnly** 设置为 **false** 的存储帐户来确认策略定义已成功应用。 由于策略分配，你应该无法创建此存储帐户。  

```powershell
New-AzStorageAccount -ResourceGroupName (New-AzResourceGroup -name policy-test -Location eastus -Force).ResourceGroupName `
                     -Name (get-random) `
                     -Location eastus `
                     -EnableHttpsTrafficOnly $false `
                     -SkuName Standard_LRS `
                     -Verbose                  
```

## <a name="clean-up-resources"></a>清理资源

完成后，删除部署所创建的策略定义和分配。

```powershell
foreach ($ManagedSub in $ManagedSubscriptions)
{
    select-azsubscription -subscriptionId $ManagedSub.subscriptionId

    Remove-AzSubscriptionDeployment -Name mgmt -AsJob

    $Assignment = Get-AzPolicyAssignment | where-object {$_.Name -like "enforce-https-storage-assignment"}

    if ([string]::IsNullOrEmpty($Assignment))
    {
        Write-Output "Nothing to clean up - we're done"
    }
    else
    {

    Remove-AzPolicyAssignment -Name 'enforce-https-storage-assignment' -Scope "/subscriptions/$($ManagedSub.subscriptionId)" -Verbose

    Write-Output "Deployment has been deleted - we're done"
    }
}
```

> [!NOTE]
> 虽然可以跨多个租户部署策略，但目前无法查看这些租户中不符合资源的 [符合性详细信息](../../governance/policy/how-to/determine-non-compliance.md#compliance-details) 。

## <a name="next-steps"></a>后续步骤

- 了解 [Azure Policy](../../governance/policy/index.yml)。
- 了解[跨租户管理体验](../concepts/cross-tenant-management-experience.md)。
- 了解如何 [部署可](deploy-policy-remediation.md) 在委托订阅中修正的策略。
