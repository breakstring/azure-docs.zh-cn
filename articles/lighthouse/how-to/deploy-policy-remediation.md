---
title: 部署可修正的策略
description: 若要通过 Azure Lighthouse 部署使用更正任务的策略，需要在客户租户中创建托管标识。
ms.date: 01/14/2021
ms.topic: how-to
ms.openlocfilehash: 01070133241117596bdf2b8e1e7c3fa101fc656c
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2021
ms.locfileid: "98233876"
---
# <a name="deploy-a-policy-that-can-be-remediated-within-a-delegated-subscription"></a>部署可以在委派的订阅中修正的策略

[Azure Lighthouse](../overview.md) 允许服务提供程序在委派的订阅中创建和编辑策略定义。 但是，若要部署使用 [修正任务](../../governance/policy/how-to/remediate-resources.md) 的策略 (即，具有 [deployIfNotExists](../../governance/policy/concepts/effects.md#deployifnotexists) 或 [modify](../../governance/policy/concepts/effects.md#modify) 效果) 的策略，你需要在客户租户中创建一个 [托管标识](../../active-directory/managed-identities-azure-resources/overview.md) 。 此托管标识可供 Azure Policy 用来在策略中部署模板。 在载入 Azure 委托资源管理的客户时，以及部署策略本身时，需要执行相关步骤来启用此方案。

> [!TIP]
> 尽管我们指的是本主题中的服务提供商和客户，但 [管理多个租户的企业](../concepts/enterprise.md) 可以使用相同的过程。

## <a name="create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant"></a>创建一个可以在客户租户中将角色分配给托管标识的用户

将客户登记到 Azure Lighthouse 时，请使用 [azure 资源管理器模板](onboard-customer.md#create-an-azure-resource-manager-template) 以及参数文件来定义授权，以便向客户租户中的委派资源授予访问权限。 每个授权都指定一个 **principalId** ，它对应于管理租户中的 Azure AD 用户、组或服务主体，以及与将授予的 [Azure 内置角色](../../role-based-access-control/built-in-roles.md)对应的 **roleDefinitionId** 。

若要允许 **principalId** 在客户租户中创建托管标识，必须将其 **roleDefinitionId** 设置为“用户访问管理员”。 虽然通常不支持此角色，但可以在此特定方案中使用它，这允许具有此权限的用户帐户将一个或多个特定的内置角色分配给托管标识。 这些角色是在 **delegatedRoleDefinitionIds** 属性中定义的，并且可以包括除 "用户访问管理员" 或 "所有者" 之外的任何 [受支持的 Azure 内置角色](../concepts/tenants-users-roles.md#role-support-for-azure-lighthouse) 。

将客户加入以后，在此授权中创建的 **principalId** 即可将这些内置角色分配给客户租户中的托管标识。 但是，他们不会有通常与“用户访问管理员”角色关联的任何其他权限。

下面的示例显示了一个 **principalId**，该 ID 将有“用户访问管理员”角色。 此用户将能够将两个内置角色分配到客户租户中的托管标识：参与者和 Log Analytics 参与者。

```json
{
    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
    "principalIdDisplayName": "Policy Automation Account",
    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "delegatedRoleDefinitionIds": [
         "b24988ac-6180-42a0-ab88-20f7382dd24c",
         "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
    ]
}
```

## <a name="deploy-policies-that-can-be-remediated"></a>部署可修正的策略

按照上述必要权限创建用户后，该用户可以在委派的客户订阅中部署使用修正任务的策略。

例如，假设你想要对客户租户中的 Azure Key Vault 资源启用诊断，如本 [示例](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring)中所示。 管理租户中具有适当权限（如上所述）的用户会部署 [Azure 资源管理器模板](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-enforce-keyvault-monitoring/enforceAzureMonitoredKeyVault.json)来启用此方案。

请注意，目前必须通过 API 而不是 Azure 门户创建与委托订阅配合使用的策略分配。 这样做时，必须将 **apiVersion** 设置为 **2019-04-01-preview**，其中包含新的 **delegatedManagedIdentityResourceId** 属性。 此属性允许你在已载入到 Azure Lighthouse) 的订阅或资源组中包含位于客户租户 (的托管标识。

以下示例显示具有 **delegatedManagedIdentityResourceId** 的角色分配。

```json
"type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2019-04-01-preview",
            "name": "[parameters('rbacGuid')]",
            "dependsOn": [
                "[variables('policyAssignment')]"
            ],
            "properties": {
                "roleDefinitionId": "[concat(subscription().id, '/providers/Microsoft.Authorization/roleDefinitions/', variables('rbacContributor'))]",
                "principalType": "ServicePrincipal",
                "delegatedManagedIdentityResourceId": "[concat(subscription().id, '/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment'))]",
                "principalId": "[toLower(reference(concat('/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment')), '2018-05-01', 'Full' ).identity.principalId)]"
            }
```

> [!TIP]
> 这里有[类似的示例](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag)，该示例演示如何部署策略，以便为委托的订阅添加或删除标记（使用 modify 效果）。

## <a name="next-steps"></a>后续步骤

- 了解 [Azure Policy](../../governance/policy/index.yml)。
- 了解 [Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。
