---
title: 使用 Azure Cosmos DB 限制用户对数据操作的访问
description: 了解如何使用 Azure Cosmos DB 限制对数据操作的访问
author: voellm
ms.service: cosmos-db
ms.topic: how-to
ms.date: 12/9/2019
ms.author: tvoellm
ms.openlocfilehash: 61e8ee2cbcd1252704b2d1e220fcfaaff17c2f28
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93085983"
---
# <a name="restrict-user-access-to-data-operations-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中限制用户对数据操作的访问权限
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

在 Azure Cosmos DB 中，可通过两种方式来验证与数据库服务的交互：

- 与 Azure 门户交互时使用 Azure Active Directory 标识；
- 从 API 和 SDK 发出调用时使用 Azure Cosmos DB [密钥](database-security.md#primary-keys)或[资源令牌](secure-access-to-data.md#resource-tokens)。

每种身份验证方法授予对不同操作集的访问权限，但存在某种重叠：

:::image type="content" source="./media/how-to-restrict-user-data/operations.png" alt-text="按身份验证类型拆分操作" border="false":::

在某些情况下，你可能希望仅限组织中的某些用户执行数据操作（即 CRUD 请求和查询）。 不需要创建或删除资源，或者不需要更改所用容器的预配吞吐量的开发人员通常希望实施这种限制。

可通过应用以下步骤来限制访问：
1. 针对你要限制其访问权限的用户创建自定义的 Azure Active Directory 角色。 该自定义 Active Directory 角色应使用 Azure Cosmos DB 的[粒度操作](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)对操作进行精细粒度的访问。
1. 不允许使用密钥执行非数据操作。 可以通过将这些操作仅限为 Azure 资源管理器调用来实现此目的。

本文的后续部分将介绍如何执行这些步骤。

> [!NOTE]
> 若要执行后续部分所述的命令，需要安装 Azure PowerShell 模块 3.0.0 或更高版本，并且在尝试修改的订阅中需要具有 [Azure 所有者角色](../role-based-access-control/built-in-roles.md#owner)。

在后续部分所述的 PowerShell 脚本中，请将以下占位符替换为特定于环境的值：
- `$MySubscriptionId` - 订阅 ID，包含要在其中限制权限的 Azure Cosmos 帐户。 例如：`e5c8766a-eeb0-40e8-af56-0eb142ebf78e`。
- `$MyResourceGroupName` - 包含 Azure Cosmos 帐户的资源组。 例如：`myresourcegroup`。
- `$MyAzureCosmosDBAccountName` - Azure Cosmos 帐户的名称。 例如：`mycosmosdbsaccount`。
- `$MyUserName` - 要限制其访问权限的用户的登录名 (username@domain)。 例如：`cosmosdbuser@contoso.com`。

## <a name="select-your-azure-subscription"></a>选择 Azure 订阅

Azure PowerShell 命令要求登录，并选择要执行命令的订阅：

```azurepowershell
Login-AzAccount
Select-AzSubscription $MySubscriptionId
```

## <a name="create-the-custom-azure-active-directory-role"></a>创建自定义 Azure Active Directory 角色

以下脚本为 Azure Cosmos 帐户创建具有“仅限密钥”访问权限的 Azure Active Directory 角色分配。 该角色基于 [Azure 自定义角色](../role-based-access-control/custom-roles.md)以及 [Azure Cosmos DB 的粒度操作](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)。 这些角色和操作是 `Microsoft.DocumentDB` Azure Active Directory 命名空间的一部分。

1. 首先，创建包含以下内容的名为 `AzureCosmosKeyOnlyAccess.json` 的 JSON 文档：

    ```
    {
        "Name": "Azure Cosmos DB Key Only Access Custom Role",
        "Id": "00000000-0000-0000-0000-0000000000",
        "IsCustom": true,
        "Description": "This role restricts the user to read the account keys only.",
        "Actions":
        [
            "Microsoft.DocumentDB/databaseAccounts/listKeys/action"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "AssignableScopes":
        [
            "/subscriptions/$MySubscriptionId"
        ]
    }
    ```

1. 运行以下命令以创建角色分配，并将其分配给用户：

    ```azurepowershell
    New-AzRoleDefinition -InputFile "AzureCosmosKeyOnlyAccess.json"
    New-AzRoleAssignment -SignInName $MyUserName -RoleDefinitionName "Azure Cosmos DB Key Only Access Custom Role" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName -ResourceType "Microsoft.DocumentDb/databaseAccounts"
    ```

## <a name="disallow-the-execution-of-non-data-operations"></a>不允许执行非数据操作

以下命令消除使用密钥执行以下操作的能力：
- 创建、修改或删除资源
- 更新容器设置（包括索引策略、吞吐量等）。

```azurepowershell
$cdba = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName
$cdba.Properties.disableKeyBasedMetadataWriteAccess="True"
$cdba | Set-AzResource -Force
```

## <a name="next-steps"></a>后续步骤

- 详细了解 [Cosmos DB 的基于角色的访问控制](role-based-access-control.md)
- 获取[安全访问 Cosmos DB 中的数据](secure-access-to-data.md)的概述
