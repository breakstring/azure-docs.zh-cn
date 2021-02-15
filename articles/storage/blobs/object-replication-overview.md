---
title: 对象复制概述
titleSuffix: Azure Storage
description: 对象复制以异步方式在源存储帐户和目标帐户之间复制块 blob。 使用对象复制可最大程度地降低读取请求延迟，提高计算工作负载的效率，优化数据分发，并最大限度地降低成本。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 391c33e72f45e7c0c0b56128b32a8e73399e417a
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2021
ms.locfileid: "99834317"
---
# <a name="object-replication-for-block-blobs"></a>块 blob 的对象复制

对象复制以异步方式在源存储帐户和目标帐户之间复制块 blob。 对象复制支持的部分方案包括：

- **最大程度地降低延迟。** 通过使客户端可以从物理位置更接近的区域使用数据，对象复制可以降低读取请求的延迟。
- **提高计算工作负载的效率。** 通过对象复制，计算工作负载可以在不同区域中处理相同的块 blob 集。
- **优化数据分发。** 您可以在单个位置处理或分析数据，然后仅将结果复制到其他区域。
- **优化成本。** 复制数据后，可以使用生命周期管理策略将数据转移到存档层，从而降低成本。

下图显示了对象复制如何将块 blob 从一个区域中的源存储帐户复制到两个不同区域中的目标帐户。

:::image type="content" source="media/object-replication-overview/object-replication-diagram.svg" alt-text="显示对象复制工作方式的图":::

若要了解如何配置对象复制，请参阅 [配置对象复制](object-replication-configure.md)。

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="prerequisites-for-object-replication"></a>对象复制的先决条件

对象复制需要同时启用以下 Azure 存储功能：

- [更改](storage-blob-change-feed.md)源：必须在源帐户上启用。 若要了解如何启用更改源，请参阅 [启用和禁用更改源](storage-blob-change-feed.md#enable-and-disable-the-change-feed)。
- [Blob 版本控制](versioning-overview.md)：必须同时在源帐户和目标帐户上启用。 若要了解如何启用版本控制，请参阅 [启用和管理 blob 版本控制](versioning-enable.md)。

启用更改源和 blob 版本控制可能会产生额外的成本。 有关更多详细信息，请参阅 [Azure 存储定价页](https://azure.microsoft.com/pricing/details/storage/)。

只有常规用途 v2 存储帐户才支持对象复制。 源帐户和目标帐户都必须是常规用途 v2。 

## <a name="how-object-replication-works"></a>对象复制的工作原理

根据你配置的规则，对象复制以异步方式复制容器中的块 blob。 Blob 的内容、与 blob 关联的任何版本以及 blob 的元数据和属性都将从源容器复制到目标容器。

> [!IMPORTANT]
> 因为块 blob 数据是以异步方式复制的，所以源帐户和目标帐户不会立即同步。目前没有关于将数据复制到目标帐户所需时间的 SLA。 可以在源 blob 上检查复制状态，以确定复制是否已完成。 有关详细信息，请参阅 [检查 blob 的复制状态](object-replication-configure.md#check-the-replication-status-of-a-blob)。

### <a name="blob-versioning"></a>Blob 版本控制

对象复制要求在源帐户和目标帐户上启用 blob 版本控制。 当修改源帐户中的复制的 blob 时，将在修改之前，在源帐户中创建 blob 的新版本，以反映 blob 的以前状态。 源帐户中 (或基本 blob) 的当前版本反映了最新的更新。 更新的当前版本和新的早期版本都将复制到目标帐户。 有关写入操作如何影响 blob 版本的详细信息，请参阅 [编写操作的版本控制](versioning-overview.md#versioning-on-write-operations)。

删除源帐户中的 blob 时，将在以前的版本中捕获 blob 的当前版本，然后将其删除。 即使在删除当前版本后，该 blob 的所有早期版本也会保持不变。 此状态被复制到目标帐户。 有关删除操作如何影响 blob 版本的详细信息，请参阅 [有关删除操作的版本控制](versioning-overview.md#versioning-on-delete-operations)。

### <a name="snapshots"></a>快照

对象复制不支持 blob 快照。 源帐户中的 blob 上的任何快照都不会复制到目标帐户。

### <a name="blob-tiering"></a>Blob 分层

当源帐户和目标帐户位于 "热" 层或 "冷" 层时，支持对象复制。 源帐户和目标帐户可能位于不同的层。 但是，如果源或目标帐户中的 blob 已移到存档层，对象复制将失败。 有关 blob 层的详细信息，请参阅 [Azure Blob 存储的访问层-热、冷和存档](storage-blob-storage-tiers.md)。

### <a name="immutable-blobs"></a>不可变 blob

对象复制不支持不可变 blob。 如果源或目标容器具有基于时间的保留策略或合法保留，则对象复制将失败。 有关不可变 blob 的详细信息，请参阅[使用不可变存储来存储业务关键型 blob 数据](storage-blob-immutable-storage.md)。

## <a name="object-replication-policies-and-rules"></a>对象复制策略和规则

配置对象复制时，需要创建复制策略，以指定源存储帐户和目标帐户。 复制策略包括一个或多个规则，用于指定源容器和目标容器，并指明复制源容器中的哪些块 blob。

配置对象复制以后，Azure 存储会定期检查源帐户的更改源，并将所有写入或删除操作以异步方式复制到目标帐户。 复制延迟取决于要复制的块 blob 的大小。

### <a name="replication-policies"></a>复制策略

配置对象复制时，需要通过 Azure 存储资源提供程序针对源帐户和目标帐户创建复制策略。 复制策略通过策略 ID 进行标识。 源帐户和目标帐户的策略必须具有相同的策略 ID，才能进行复制。

存储帐户可用作最多两个目标帐户的源帐户。 源帐户和目标帐户可能位于同一区域或不同区域。 它们也可以驻留在不同的订阅中，并以不同的 Azure Active Directory (Azure AD) 租户。 只能为每个源帐户/目标帐户对创建一个复制策略。

### <a name="replication-rules"></a>复制规则

复制规则指定 Azure 存储如何将 blob 从源容器复制到目标容器。 可以为每个复制策略最多指定 10 个复制规则。 每个复制规则定义单个源和目标容器，并且每个源和目标容器只能在一个规则中使用。

创建复制规则时，默认情况下仅复制随后添加到源容器的新块 blob。 您可以指定复制新的块 blob 和现有块 blob，也可以定义自定义复制范围，以复制从指定的时间开始创建的块 blob。

也可以将一个或多个筛选器指定为复制规则的一部分，以按前缀筛选块 blob。 指定前缀时，只会将源容器中与该前缀匹配的 blob 复制到目标容器。

源和目标容器必须都存在，然后才能在规则中指定它们。 创建复制策略后，不允许将操作写入目标容器。 任何尝试写入目标容器的操作都会失败，错误代码为 409（冲突）。 若要写入为其配置了复制规则的目标容器，你必须删除为该容器配置的规则，或者删除复制策略。 当复制策略处于活动状态时，允许对目标容器执行读取和删除操作。

您可以对目标容器中的 blob 调用 " [设置 Blob 层](/rest/api/storageservices/set-blob-tier) " 操作，以将其移到 "存档" 层中。 有关存档层的详细信息，请参阅 [Azure Blob 存储： "热"、"冷" 和 "存档" 访问层](storage-blob-storage-tiers.md#archive-access-tier)。

## <a name="replication-status"></a>复制状态

可以在源帐户中查看 blob 的复制状态。 有关详细信息，请参阅 [检查 blob 的复制状态](object-replication-configure.md#check-the-replication-status-of-a-blob)。

如果源帐户中 blob 的复制状态指示失败，则调查以下可能的原因：

- 请确保已在目标帐户上配置对象复制策略。
- 验证目标容器是否仍然存在。
- 如果源 blob 已使用客户提供的密钥在写入操作过程中进行了加密，则对象复制将失败。 有关客户提供的密钥的详细信息，请参阅[在对 Blob 存储的请求中提供加密密钥](encryption-customer-provided-keys.md)。

## <a name="billing"></a>计费

对于源帐户和目标帐户，对象复制会产生额外的成本，以及将数据从源帐户复制到目标帐户以及读取计费以处理更改源的传出费用。

## <a name="next-steps"></a>后续步骤

- [配置对象复制](object-replication-configure.md)
- [Azure Blob 存储中的更改源支持](storage-blob-change-feed.md)
- [启用和管理 blob 版本控制](versioning-enable.md)
