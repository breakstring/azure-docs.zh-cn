---
title: Azure 安全中心的内置策略定义
description: 列出 Azure 安全中心的 Azure Policy 内置策略定义。 这些内置的策略定义提供了管理 Azure 资源的常用方法。
ms.date: 02/09/2021
ms.topic: reference
author: memildin
ms.author: memildin
ms.service: security-center
ms.custom: subject-policy-reference
ms.openlocfilehash: ab6556bc391eb3136fa8c4ba40f5a5d71da956e7
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/10/2021
ms.locfileid: "100093026"
---
# <a name="azure-policy-built-in-definitions-for-azure-security-center"></a>Azure 安全中心的 Azure Policy 内置定义

此页是与 Azure 安全中心相关的 [Azure 策略](../governance/policy/overview.md) 内置策略定义的索引。 策略定义进行下列分组：

- [计划](#azure-security-center-initiatives)组在“安全中心”类别中列出 Azure Policy 计划定义。
- [默认计划](#azure-security-center-initiatives)组列出作为安全中心默认计划（ [azure 安全基准](../security/benchmarks/introduction.md)）一部分的所有 Azure 策略定义。 此 Microsoft 创作的、广泛遵从的基准是从 [中心针对 Internet 安全 (CIS) ](https://www.cisecurity.org/benchmark/azure/) 和 [美国国家标准与技术研究院 (NIST) ](https://www.nist.gov/) ，重点介绍以云为中心的安全性。
- [类别](#azure-security-center-category)组列出“安全中心”类别中的所有 Azure Policy 定义。

有关安全策略的详细信息，请参阅[使用安全策略](./tutorial-security-policy.md)。 有关其他服务的其他 Azure Policy 内置定义，请参阅 [Azure Policy 内置定义](../governance/policy/samples/built-in-policies.md)。

每个内置策略定义链接（指向 Azure 门户中的策略定义）的名称。 使用“版本”列中的链接查看 [Azure Policy GitHub 存储库](https://github.com/Azure/azure-policy)上的源。

## <a name="azure-security-center-initiatives"></a>Azure 安全中心计划

若要了解通过安全中心监视的内置计划，请参阅下表：

[!INCLUDE [azure-policy-reference-policyset-security-center](../../includes/policy/reference/bycat/policysets-security-center.md)]

## <a name="azure-security-center-default-initiative"></a>Azure 安全中心默认计划

若要了解通过安全中心监视的内置策略，请参阅下表：

[!INCLUDE [azure-policy-reference-init-asc](../../includes/policy/reference/custom/init-asc.md)]

## <a name="azure-security-center-category"></a>Azure 安全中心类别

[!INCLUDE [azure-policy-reference-category-securitycenter](../../includes/policy/reference/bycat/policies-security-center.md)]

## <a name="next-steps"></a>后续步骤

本文介绍了安全中心的 Azure Policy 安全策略定义。 若要了解详细信息，请参阅以下文章。

- 在 [Azure Policy GitHub 存储库](https://github.com/Azure/azure-policy)中查看这些内置项。
- 查看 [Azure Policy 定义结构](../governance/policy/concepts/definition-structure.md)。
- 查看[了解策略效果](../governance/policy/concepts/effects.md)。
- [Azure 安全中心规划和操作指南](./security-center-planning-and-operations-guide.md)：了解如何规划和理解 Azure 安全中心的设计考虑事项。
- [在 Azure 安全中心进行安全运行状况监视](./security-center-monitoring.md)：了解如何监视 Azure 资源的运行状况。
- [在 Azure 安全中心内管理和响应安全警报](./security-center-managing-and-responding-alerts.md)：了解如何管理和响应安全警报。
- [通过 Azure 安全中心监视合作伙伴解决方案](./security-center-partner-integration.md)：了解如何监视合作伙伴解决方案的运行状况。
- [Azure Policy](../governance/policy/overview.md)：了解如何审核和管理 Azure 资源。
