---
title: RequestDisallowedByPolicy 错误
description: 描述使用 Azure 资源管理器部署资源时出现 RequestDisallowedByPolicy 错误的原因。
author: genlin
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 41581ba48da2f2e717c5abf2a749f8fd2b86ac06
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "75477663"
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Azure 资源策略的 RequestDisallowedByPolicy 错误

本文说明了 RequestDisallowedByPolicy 错误的原因，它还提供了此错误的解决方案。

## <a name="symptom"></a>症状

部署过程中，可能会收到阻止创建资源的 RequestDisallowedByPolicy 错误  。 以下示例显示错误：

```json
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>故障排除

若要检索有关阻止部署的策略的详细信息，请使用以下方法之一：

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

在 PowerShell 中，提供该策略标识符作为 `Id` 参数，检索有关阻止部署的策略的详细信息。

```powershell
(Get-AzPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="azure-cli"></a>Azure CLI

在 Azure CLI 中，提供策略定义的名称：

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>解决方案

为了安全性和符合性，订阅管理员可能会分配限制资源部署方式的策略。 例如，订阅可能具有阻止创建公共 IP 地址、网络安全组、用户定义的路由或路由表的策略。 “症状”部分中的错误消息显示策略的名称  。
要解决此问题，请查看资源策略，并确定如何部署符合这些策略的资源。

有关详细信息，请参阅以下文章：

- [什么是 Azure Policy？](../../governance/policy/overview.md)
- [创建和管理策略以强制实施符合性](../../governance/policy/tutorials/create-and-manage.md)
