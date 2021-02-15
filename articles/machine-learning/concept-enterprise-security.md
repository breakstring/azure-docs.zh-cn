---
title: 企业安全和管理
titleSuffix: Azure Machine Learning
description: 安全使用 Azure 机器学习：身份验证、授权、网络安全性、数据加密和监视。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 11/20/2020
ms.openlocfilehash: a079504872eaf3840416a99e784c4d33a6828b0c
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94992023"
---
# <a name="enterprise-security-and-governance-for-azure-machine-learning"></a>Azure 机器学习的企业安全和管理

在本文中，你将了解 Azure 机器学习可用的安全和监管功能。 这些功能对于想要创建符合公司策略的安全配置的管理员、DevOps 和 MLOps 十分有用。 通过 Azure 机器学习和 Azure 平台，你可以：

* 按用户帐户或组限制对资源和操作的访问权限
* 限制传入和传出的网络通信
* 加密传输中的数据和静态数据
* 扫描漏洞
* 应用和审核配置策略

## <a name="restrict-access-to-resources-and-operations"></a>限制对资源和操作的访问

[Azure Active Directory (Azure AD) ](../active-directory/fundamentals/active-directory-whatis.md) 是 Azure 机器学习的标识服务提供程序。 使用此功能，你可以创建和管理 (用户、组、服务主体和用于对 Azure 资源 _进行身份验证_ 的托管标识) 的安全对象。 如果 Azure AD 配置为使用多重身份验证，则支持多重身份验证。

下面是在 Azure AD 中使用多重身份验证的 Azure 机器学习的身份验证过程：

1. 客户端登录到 Azure AD 并获取 Azure 资源管理器令牌。
1. 客户端将令牌提供给 Azure 资源管理器和所有 Azure 机器学习服务。
1. Azure 机器学习向用户计算目标 (提供机器学习服务标记，例如 Azure 机器学习计算群集) 。 运行完成后，用户计算目标使用此令牌回调机器学习服务。 范围限制为工作区。

[![Azure 机器学习中的身份验证](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication.png#lightbox)

每个工作区都具有关联的系统分配的 [托管标识](../active-directory/managed-identities-azure-resources/overview.md) ，该标识与工作区同名。 此托管标识用于安全地访问工作区使用的资源。 它在附加资源上具有以下 Azure RBAC 权限：

| 资源 | 权限 |
| ----- | ----- |
| 工作区 | 参与者 |
| 存储帐户 | 存储 Blob 数据参与者 |
| 密钥保管库 | 访问所有密钥、机密和证书 |
| Azure 容器注册表 | 参与者 |
| 包含工作区的资源组 | 参与者 |
| 包含 Key Vault 的资源组（如果不同于包含工作区的资源组） | 参与者 |

不建议管理员撤销托管标识对上表中所述资源的访问权限。 你可以使用 "重新 [同步密钥" 操作](how-to-change-storage-access-key.md)还原访问权限。

Azure 机器学习还会创建一个附加应用程序 (名称以开头， `aml-` 或 `Microsoft-AzureML-Support-App-` 在每个工作区区域的订阅中) 参与者级别的访问权限。 例如，在同一订阅中，如果在美国东部和欧洲北部各有一个工作区，则会看到两个这样的应用程序。 通过这些应用程序，Azure 机器学习可帮助管理计算资源。

你还可以配置自己的托管标识以便与 Azure 虚拟机配合使用，并 Azure 机器学习计算群集。 使用 VM 时，可使用托管标识从 SDK 访问工作区，而不是单个用户的 Azure AD 帐户。 使用计算群集时，托管标识用于访问运行训练作业的用户可能无权访问的资源，例如安全数据存储。 有关详细信息，请参阅 [Azure 机器学习工作区的身份验证](how-to-setup-authentication.md)。

> [!TIP]
> Azure 机器学习中使用 Azure AD 和 Azure RBAC 有一些例外：
> * 你可以选择启用对计算资源（例如 Azure 机器学习计算实例和计算群集）的 __SSH__ 访问。 SSH 访问基于公钥/私钥对，而不是 Azure AD。 SSH 访问不受 Azure RBAC 的管辖。
> * 你可以使用 __密钥__ 或基于 __令牌__ 的身份验证将部署为 web 服务 (推理终结点) 的模型进行身份验证。 键为静态字符串，而使用 Azure AD 安全对象检索令牌。 有关详细信息，请参阅为 [部署为 web 服务的模型配置身份验证](how-to-authenticate-web-service.md)。

有关详细信息，请参阅以下文章：
* [Azure 机器学习工作区的身份验证](how-to-setup-authentication.md)
* [管理对 Azure 机器学习的访问](how-to-assign-roles.md)
* [连接到存储服务](how-to-access-data.md)
* [训练时使用机密 Azure Key Vault](how-to-use-secrets-in-runs.md)
* [使用 Azure 机器学习的 Azure AD 托管标识](how-to-use-managed-identities.md)
* [在 web 服务中使用 Azure AD 托管标识](how-to-use-azure-ad-identity.md)

## <a name="network-security-and-isolation"></a>网络安全和隔离

若要限制对 Azure 机器学习资源的网络访问，可以使用 [Azure 虚拟网络 (VNet) ](../virtual-network/virtual-networks-overview.md)。 Vnet 允许你创建部分或完全独立于公共 internet 的网络环境。 这会减少解决方案的受攻击面以及数据渗透的几率。

你可以使用虚拟专用网络 (VPN) 网关将单个客户端或你自己的网络连接到 VNet

Azure 机器学习工作区可以使用 [Azure 专用链接](../private-link/private-link-overview.md) 在 VNet 后面创建专用终结点。 这将提供一组专用 IP 地址，这些地址可用于从 VNet 中访问工作区。 Azure 机器学习依赖的某些服务也可使用 Azure 专用链接，但某些服务依赖于网络安全组或用户定义的路由。

有关详细信息，请参阅以下文档：

* [虚拟网络隔离和隐私概述](how-to-network-security-overview.md)
* [保护工作区资源](how-to-secure-workspace-vnet.md)
* [保护训练环境](how-to-secure-training-vnet.md)
* [安全推理环境](how-to-secure-inferencing-vnet.md)
* [在受保护的虚拟网络中使用 studio](how-to-enable-studio-virtual-network.md)
* [使用自定义 DNS](how-to-custom-dns.md)
* [配置防火墙](how-to-access-azureml-behind-firewall.md)

<a id="encryption-at-rest"></a><a id="azure-blob-storage"></a>

## <a name="data-encryption"></a>数据加密

Azure 机器学习在 Azure 平台上使用各种计算资源和数据存储。 若要详细了解其中每个如何支持静态数据加密和传输中的数据，请参阅 [Azure 机器学习的数据加密](concept-data-encryption.md)。

在将模型部署为 web 服务时，可以启用传输层安全性 (TLS) 以加密传输中的数据。 有关详细信息，请参阅 [配置安全 web 服务](how-to-secure-web-service.md)。

## <a name="vulnerability-scanning"></a>漏洞扫描

[Azure 安全中心](../security-center/security-center-introduction.md) 跨混合云工作负荷提供统一的安全管理和高级威胁防护。 对于 Azure 机器学习，应启用对 [Azure 容器注册表](../container-registry/container-registry-intro.md) 资源和 Azure Kubernetes 服务资源的扫描。 有关详细信息，请参阅安全中心和[Azure Kubernetes Services 与安全中心集成](../security-center/defender-for-kubernetes-introduction.md)[的 azure 容器注册表映像扫描](../security-center/defender-for-container-registries-introduction.md)。

## <a name="audit-and-manage-compliance"></a>审核和管理合规性

[Azure Policy](../governance/policy/index.yml) 是一种管理工具，你可用它来确保 Azure 资源符合你的策略。 可以设置策略以允许或强制实施特定配置，例如 Azure 机器学习工作区是否使用专用终结点。 有关 Azure Policy 的详细信息，请参阅 [Azure Policy 文档](../governance/policy/overview.md)。 若要详细了解特定于 Azure 机器学习的策略，请参阅[使用 Azure Policy 审核和管理合规性](how-to-integrate-azure-policy.md)。

## <a name="next-steps"></a>后续步骤

* [使用 TLS 保护 Azure 机器学习 Web 服务](how-to-secure-web-service.md)
* [使用部署为 Web 服务的机器学习模型](how-to-consume-web-service.md)
* [将 Azure 机器学习与 Azure 防火墙配合使用](how-to-access-azureml-behind-firewall.md)
* [通过 Azure 虚拟网络使用 Azure 机器学习](how-to-network-security-overview.md)
* [静态数据和传输中的数据加密](concept-data-encryption.md)
* [在 Azure 上生成实时建议 API](/azure/architecture/reference-architectures/ai/real-time-recommendation)
