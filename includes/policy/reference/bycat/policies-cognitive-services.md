---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/29/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 27dbb8a97d55b11a4ad35169840933747c635f58
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2021
ms.locfileid: "99220227"
---
|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[认知服务帐户应启用数据加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2bdd0062-9d75-436e-89df-487dd8e4b3c7) |此策略审核未使用数据加密的任何认知服务帐户。 对于具有存储的各个认知服务帐户，应启用使用客户托管密钥或 Microsoft 管理密钥的数据加密。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_Encryption_Audit.json) |
|[认知服务帐户应启用使用客户管理的密钥 (CMK) 进行数据加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F67121cc7-ff39-4ab8-b7e3-95b84dab487d) |客户管理的密钥 (CMK) 通常是满足法规符合性标准所必需的。 CMK 支持使用由你创建并拥有的 Azure Key Vault 密钥对存储在认知服务中的数据进行加密。 你可以完全控制并负责关键生命周期，包括轮换和管理。 访问 [https://aka.ms/cosmosdb-cmk](https://aka.ms/cosmosdb-cmk)，了解有关 CMK 加密的详细信息。 |Audit、Deny、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_CustomerManagedKey_Audit.json) |
|[认知服务帐户应限制网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F037eea7a-bd0a-46c5-9a66-03aea78705d3) |应限制对认知服务帐户的网络访问。 配置网络规则，使只有来自允许的网络的应用程序才能访问认知服务帐户。 若要允许来自特定 Internet 或本地客户端的连接，可以向来自特定 Azure 虚拟网络或到公共 Internet IP 地址范围的流量授予访问权限。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_NetworkAcls_Audit.json) |
|[认知服务帐户应使用客户自有存储](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F46aa9b05-0e60-4eae-a88b-1e9d374fa515) |此策略审核未使用客户自有存储的任何认知服务帐户。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_UserOwnedStorage_Audit.json) |
|[认知服务帐户应使用客户自有存储或启用数据加密。](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F11566b39-f7f7-4b82-ab06-68d8700eb0a4) |此策略审核未使用客户自有存储或数据加密的任何认知服务帐户。 对于具有存储的各个认知服务帐户，应使用客户自有存储或启用数据加密。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_BYOX_Audit.json) |
|[应为认知服务帐户禁用公用网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0725b4dd-7e76-479c-a735-68e7ee23d5ca) |此策略审核你的环境中启用了公用网络访问的任何认知服务帐户。 应禁用公用网络访问，仅允许来自专用终结点的连接。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_DisablePublicNetworkAccess_Audit.json) |
