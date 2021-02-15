---
title: Blob 存储的加密范围（预览）
description: 可以通过加密范围在容器或单个 Blob 级别管理加密。 可以使用加密范围在驻留在同一存储帐户中但属于不同客户的数据之间创建安全边界。
services: storage
author: tamram
ms.service: storage
ms.date: 09/22/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 881a7fc915ab986577599b85f8412fa8107f7902
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017383"
---
# <a name="encryption-scopes-for-blob-storage-preview"></a>Blob 存储的加密范围（预览）

可以通过加密范围在容器或单个 Blob 级别管理加密。 可以使用加密范围在驻留在同一存储帐户中但属于不同客户的数据之间创建安全边界。

默认情况下，使用作用域为整个存储帐户的密钥对存储帐户进行加密。 使用加密范围时，可以指定使用作用域仅为这些容器的密钥对一个或多个容器加密。

你可以选择使用 Microsoft 管理的密钥或存储在 Azure Key Vault 中的客户管理的密钥来保护和控制对用于加密数据的密钥的访问。 同一存储帐户上的不同加密范围可以使用 Microsoft 管理的密钥或客户管理的密钥。

创建加密范围后，可以对创建容器或 Blob 的请求指定加密范围。 有关如何创建加密范围的详细信息，请参阅[创建和管理加密范围（预览）](encryption-scope-manage.md)。

> [!NOTE]
> 读取访问异地冗余存储不支持加密作用域 (GRS) 或读取访问地域冗余存储 (在预览期间 GZRS) 帐户。

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

> [!IMPORTANT]
> 此加密范围预览版仅用于非生产用途。 生产服务级别协议 (SLA) 当前不可用。
>
> 为避免意外费用，请确保禁用当前不需要的任何加密范围。

## <a name="create-a-container-or-blob-with-an-encryption-scope"></a>创建具有加密范围的容器或 Blob

在加密范围下创建的 Blob 使用为该范围指定的密钥进行加密。 在创建单个 Blob 时，可以为该 Blob 指定加密范围，也可以在创建容器时指定默认的加密范围。 若在容器级别指定了默认加密范围，该容器中的所有 Blob 都将使用与该默认范围相关联的密钥进行加密。

在具有默认加密范围的容器中创建 Blob 时，如果该容器配置为允许替代默认加密范围，则可以指定用于替代默认加密范围的加密范围。 若要防止替代默认加密范围，请将容器配置为拒绝单个 Blob 的替代。

只要未禁用加密范围，对属于该加密范围的 Blob 执行读取操作以透明方式执行。

## <a name="disable-an-encryption-scope"></a>禁用加密范围

禁用加密范围时，使用该加密范围进行的任何后续读取或写入操作都将失败，并显示 HTTP 错误代码 403（已禁止）。 如果重新启用加密范围，读取和写入操作将再次正常进行。

禁用加密范围后，将不再为此付费。 禁用不需要的任何加密范围以避免不必要的费用。

如果你的加密范围受客户管理的密钥保护，则还可以删除密钥保管库中的关联密钥来禁用加密范围。 请记住，客户管理的密钥受到密钥保管库中的软删除和清除保护功能的保护，删除的密钥受到为这些属性定义的行为的约束。 有关详细信息，请参阅 Azure Key Vault 文档中的以下主题之一：

- [如何在 PowerShell 中使用软删除](../../key-vault/general/key-vault-recovery.md)
- [如何在 CLI 中使用软删除](../../key-vault/general/key-vault-recovery.md)

> [!NOTE]
> 不能删除加密范围。

## <a name="next-steps"></a>后续步骤

- [静态数据的 Azure 存储加密](../common/storage-service-encryption.md)
- [创建和管理加密范围（预览）](encryption-scope-manage.md)
- [用于 Azure 存储加密的客户管理的密钥](../common/customer-managed-keys-overview.md)
- [什么是 Azure 密钥保管库？](../../key-vault/general/overview.md)