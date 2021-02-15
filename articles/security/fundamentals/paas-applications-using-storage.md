---
title: 使用 Azure 存储保护 PaaS 应用程序 | Microsoft Docs
description: 了解有关保护 PaaS Web 和移动应用程序的 Azure 存储安全最佳做法。
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: tomsh
ms.openlocfilehash: 082408f357e97a2ed2153d43dbea459ff09ba704
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2020
ms.locfileid: "94693171"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-storage"></a>使用 Azure 存储保护 PaaS Web 和移动应用程序的最佳做法
本文介绍 Azure 存储安全在保护平台即服务 (PaaS) Web 和移动应用程序方面的最佳做法。 这些最佳实践衍生自我们的 Azure 经验和客户经验。

Azure 可以用本地不易实现的方式来部署并使用存储。 通过 Azure 存储，可用相对较少的工作量达到高水平的可伸缩性和可用性。 Azure 存储不仅是 Windows 和 Linux Azure 虚拟机的基础，还可以支持大型分布式应用程序。

Azure 存储提供了以下四种服务：Blob 存储、表存储、队列存储和文件存储。 若要了解详细信息，请参阅 [Microsoft Azure 存储简介](../../storage/common/storage-introduction.md)。

[Azure 存储安全指南](../../storage/blobs/security-recommendations.md)是有关 Azure存储和安全性的详细信息的重要来源。 本篇最佳做法文章高度概括地介绍了安全指南中的一些概念，并提供了获得详细信息的安全指南及其他来源的链接。

本文将探讨以下最佳做法：

- 共享访问签名 (SAS)
- Azure 基于角色的访问控制 (Azure RBAC)
- 高价值数据的客户端加密
- 存储服务加密


## <a name="use-a-shared-access-signature-instead-of-a-storage-account-key"></a>使用共享访问签名代替了存储帐户密钥
访问控制是关键。 若要帮助控制对 Azure 存储的访问，当创建存储帐户时，Azure 将生成两个 512 位存储帐户密钥 (SAK)。 在例程秘钥轮换期间，通过密钥冗余级别可避免服务中断。 

存储访问密钥是高优先级的机密，只能由负责存储访问控制的人员访问。 如果不当人员获取访问这些密钥的权限，他们就能够完全控制存储，并可以替换、删除文件或将文件添加到存储。 这些文件包括恶意内容和可能会危及组织或客户的其他类型的内容。

但你仍然需要一种方法来提供存储中对象的访问权限。 若要提供更精细的访问权限，可以利用共享访问签名 (SAS)。 通过 SAS，可以使用特定权限在预定义的时间间隔共享存储中的特定对象。 通过共享访问签名，可定义：

- SAS 有效的时间间隔，包括开始时间和到期时间。
- SAS 授予的权限。 例如，blob SAS 可能授予用户对 blob 的读取和写入权限，但不是删除权限。
- Azure 存储接受 SAS 的可选 IP 地址或 IP 地址范围。 例如，你可能指定属于组织的 IP 地址范围。 这为 SAS 提供了另一个安全性度量。
- Azure 存储接受 SAS 所依据的协议。 可通过此可选参数使用 HTTPS 限制对客户端的访问。

通过 SAS 可以用希望的方式共享内容，而无需分配存储帐户密钥。 在应用程序中始终使用 SAS 可以安全地共享存储资源，不会危及存储帐户密钥。

若要了解有关共享访问签名的详细信息，请参阅[使用共享访问签名](../../storage/common/storage-sas-overview.md)。 

## <a name="use-azure-role-based-access-control"></a>使用 Azure 基于角色的访问控制
管理访问的另一种方法是使用 [Azure 基于角色的访问控制](../../role-based-access-control/overview.md) (Azure RBAC)。 借助 Azure RBAC，你可以专注于根据需要知道和最低权限安全原则，为员工提供他们所需的确切权限。 权限过多，可能会向攻击者公开帐户。 权限太少意味着员工无法有效地完成其工作。 Azure RBAC 通过为 Azure 提供精细的访问管理，帮助解决此问题。 对于想要实施数据访问安全策略的组织，这是必须要做的事。

可以使用 Azure 中的 Azure 内置角色向用户分配权限。 例如，将存储帐户参与者用于需要管理存储帐户的云操作员，并使用经典存储帐户参与者角色来管理经典存储帐户。 如果云操作员需要管理 VM 但不管理他们连接到的虚拟网络或存储帐户，则可以将他们添加到虚拟机参与者角色。

不通过使用 Azure RBAC 等功能实施数据访问控制的组织可能会向其用户提供比所需权限更多的特权。 一开始就允许某些用户访问他们不应有权访问的数据可能会导致数据泄漏。

若要了解有关 Azure RBAC 的详细信息，请参阅：

- [使用 Azure 门户添加或删除 Azure 角色分配](../../role-based-access-control/role-assignments-portal.md)
- [Azure 内置角色](../../role-based-access-control/built-in-roles.md)
- [Azure 存储安全指南](../../storage/blobs/security-recommendations.md) 

## <a name="use-client-side-encryption-for-high-value-data"></a>对高价值数据使用客户端加密
通过客户端加密，可在上传到 Azure 存储之前以编程方式加密传输中的数据，并在检索数据时以编程方式解密数据。 这提供传输中的数据加密，但也提供静态数据加密。 客户端加密是最安全的加密数据方法，但它要求以编程方式更改应用程序，并将密钥管理程序放在正确的位置。

客户端加密还可以对加密密钥进行单独控制。 可生成和管理自己的加密密钥。 客户端加密使用信封技术，其中 Azure 存储客户端库生成内容加密密钥 (CEK)，然后使用密钥加密密钥 (KEK) 包装（加密）密钥。 KEK 由密钥标识符标识，可以是非对称密钥对或对称密钥，还可以在本地托管或存储在 [Azure Key Vault](../../key-vault/general/overview.md) 中。

客户端加密内置于 Java 和 .NET 存储客户端库中。 有关在客户端应用程序中加密数据并生成和管理自己的加密密钥的信息，请参阅[适用于 Microsoft Azure 存储的客户端加密和 Azure Key Vault](../../storage/common/storage-client-side-encryption.md)。

## <a name="enable-storage-service-encryption-for-data-at-rest"></a>为静态数据启用存储服务加密
当启用文件存储的[存储服务加密](../../storage/common/storage-service-encryption.md)时，将使用 AES-256 加密自动加密数据。 Microsoft 处理所有加密、解密和密钥管理。 此功能适用于 LRS 和 GRS 冗余类型。

## <a name="next-steps"></a>后续步骤

本文介绍了有关保护 PaaS Web 和移动应用程序的一系列 Azure 存储安全最佳做法。 若要了解有关保护 PaaS 部署的详细信息，请参阅：

- [保护 PaaS 部署](paas-deployments.md)
- [使用 Azure 应用服务保护 PaaS Web 和移动应用程序](paas-applications-using-app-services.md)
- [在 Azure 中保护 PaaS 数据库](paas-applications-using-sql.md)