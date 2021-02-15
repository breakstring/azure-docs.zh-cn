---
title: 存储帐户概述
titleSuffix: Azure Storage
description: 了解 Azure 存储中不同类型的存储帐户。 查看帐户命名、性能层、访问层、冗余、加密、终结点等。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/08/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 5cf43310c68c8446b9465a39d85f84c8273a68d8
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98051218"
---
# <a name="storage-account-overview"></a>存储帐户概述

Azure 存储帐户包含所有的 Azure 存储数据对象：Blob、文件、队列、表和磁盘。 存储帐户为你的 Azure 存储数据提供了一个唯一的命名空间，可以从世界上的任何位置通过 HTTP 或 HTTPS 访问该命名空间。 Azure 存储帐户中的数据是持久的，高度可用、安全且可大规模缩放。

若要了解如何创建 Azure 存储帐户，请参阅[创建存储帐户](storage-account-create.md)。

## <a name="types-of-storage-accounts"></a>存储帐户的类型

Azure 存储提供多种类型的存储帐户。 每种类型支持不同的功能，并且具有自己的定价模型。 在创建存储帐户之前，需考虑到这些差异，以便确定最适合应用程序的帐户类型。 存储帐户的类型包括：

- **常规用途 v2 帐户**：Blob、文件、队列和表的基本存储帐户类型。 建议在大多数情况下使用 Azure 存储。
- **常规用途 v1 帐户**：Blob、文件、队列和表的旧帐户类型。 如果可能，请改用常规用途 v2 帐户。
- **BlockBlobStorage 帐户**；具有适用于块 Blob 和追加 Blob 的高级性能特征的存储帐户。 建议用于事务率较高的方案，或者用于使用较小对象或需要存储延迟始终较低的方案。
- **FileStorage 帐户**：仅支持文件的存储帐户，具有高级性能特征。 建议用于企业级应用程序或高性能级应用程序。
- **Blob 存储帐户**：旧版仅限 Blob 存储帐户。 如果可能，请改用常规用途 v2 帐户。

下表描述了存储帐户的类型、支持的服务以及每种帐户类型支持的部署模型：

| 存储帐户类型 | 支持的服务 | 冗余选项 | 部署模型<sup>1</sup> |
|--|--|--|--|
| 常规用途 V2 | Blob、文件、队列、表、磁盘和 Data Lake Gen2<sup>2</sup> | LRS，GRS，RA-GRS，ZRS，GZRS，RA-GZRS<sup>3</sup> | 资源管理器 |
| 常规用途 V1 | Blob、文件、队列、表和磁盘 | LRS、GRS、RA-GRS | 资源管理器、经典 |
| BlockBlobStorage | Blob（仅块 Blob 和追加 Blob） | LRS，ZRS<sup>3</sup> | 资源管理器 |
| FileStorage | 仅文件 | LRS，ZRS<sup>3</sup> | 资源管理器 |
| BlobStorage | Blob（仅块 Blob 和追加 Blob） | LRS、GRS、RA-GRS | Resource Manager |

<sup>1</sup>建议使用 Azure 资源管理器部署模型。 使用经典部署模型的存储帐户仍可在某些位置创建，而现有的经典帐户仍然会受支持。 有关详细信息，请参阅 [Azure 资源管理器与经典部署：了解部署模型和资源状态](../../azure-resource-manager/management/deployment-models.md)。

<sup>2</sup>Azure Data Lake Storage Gen2 是一组专门用于在 Azure Blob 存储上构建的大数据分析的功能。 只有启用了分层命名空间的常规用途 V2 存储帐户才支持 Data Lake Storage Gen2。 有关 Data Lake Storage Gen2 的详细信息，请参阅 [Azure Data Lake Storage Gen2 简介](../blobs/data-lake-storage-introduction.md)。

<sup>3</sup>区域冗余存储 (ZRS) 和地域冗余存储 (GZRS/RA-GZRS) 仅适用于某些区域中的标准常规用途 V2、BlockBlobStorage 和 FileStorage 帐户。 有关 Azure 存储冗余选项的详细信息，请参阅 [Azure 存储冗余](storage-redundancy.md)。

### <a name="storage-account-redundancy"></a>存储帐户冗余

存储帐户的冗余选项包括：

- **本地冗余存储 (LRS)**：一种简单的低成本冗余策略。 数据在主要区域中的单个物理位置中同步复制三次。
- **区域冗余存储 (ZRS)**：冗余，适用于需要高可用性的方案。 跨主要区域中的 3 个 Azure 可用性区域同步复制数据。
- **异地冗余存储 (GRS)**：跨区域冗余以防范区域性服务中断。 在主要区域同步复制数据三次，然后将数据异步复制到次要区域。 若要对次要区域中的数据进行读取访问，请启用读取访问异地冗余存储 (RA-GRS)。
- **地域冗余存储 (GZRS)**：冗余，适用于要求高可用性和最大持续性的方案。 跨主要区域中的 3 个 Azure 可用性区域同步复制数据，然后将数据异步复制到次要区域。 若要对次要区域进行读取访问，可启用读取访问异地区域冗余存储 (RA-GZRS)。

若要详细了解 Azure 存储中的冗余选项，请参阅 [Azure 存储冗余](storage-redundancy.md)。

### <a name="general-purpose-v2-accounts"></a>常规用途 v2 帐户

常规用途 v2 存储帐户支持最新的 Azure 存储功能，并纳入了常规用途 v1 存储帐户和 Blob 存储帐户的所有功能。 常规用途 v2 帐户提供适用于 Azure 存储的最低单 GB 容量价格，以及具有行业竞争力的事务价格。 常规用途 v2 存储帐户支持以下 Azure 存储服务：

- Blob（所有类型：块、追加、页）
- Data Lake Gen2
- 文件
- 磁盘
- 队列
- 表

> [!NOTE]
> Microsoft 建议对大多数方案使用常规用途 v2 存储帐户。 可以轻松将常规用途 v1 或 Blob 存储帐户升级到常规用途 v2 帐户，无需停机且无需复制数据。
>
> 若要详细了解如何升级到常规用途 v2 帐户，请参阅[升级到常规用途 v2 存储帐户](storage-account-upgrade.md)。

常规用途 v2 存储帐户提供多个访问层，可以根据使用模式来存储数据。 有关详细信息，请参阅[块 Blob 数据的访问层](#access-tiers-for-block-blob-data)。

### <a name="general-purpose-v1-accounts"></a>常规用途 v1 帐户

常规用途 v1 存储帐户可以访问所有 Azure 存储服务，但可能没有最新功能，其单 GB 定价也可能不是最低的。 常规用途 v1 存储帐户支持以下 Azure 存储服务：

- Blob（所有类型）
- 文件
- 磁盘
- 队列
- 表

在大多数情况下，Microsoft 建议常规用途 v2 帐户。 以下情况可以使用常规用途 v1 帐户：

- 应用程序要求使用 Azure 经典部署模型。 常规用途 v2 帐户和 Blob 存储帐户只支持 Azure 资源管理器部署模型。

- 应用程序为事务密集型，或者使用很大的异地复制带宽，但不需要大的容量。 在这种情况下，常规用途 v1 可能是最经济的选择。

- 使用的[存储服务 REST API](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) 版本早于 2014-02-14，或使用的客户端库的版本低于 4.x， 并且无法升级应用程序。

### <a name="blockblobstorage-accounts"></a>BlockBlobStorage 帐户

BlockBlobStorage 帐户是高级性能层中的专用存储帐户，用于将非结构化对象数据作为块 Blob 或追加 Blob 存储。 与常规用途 v2 和 BlobStorage 帐户相比，BlockBlobStorage 帐户提供低且一致的延迟和更高的事务速率。

BlockBlobStorage 帐户目前不支持分层为 “热”、“冷”或“存档”访问层。 此类型的存储帐户不支持页 blob、表或队列。

### <a name="filestorage-accounts"></a>FileStorage 帐户

FileStorage 帐户是用于存储和创建高级文件共享的专用存储帐户。 此存储帐户类型支持文件，但不支持块 blob、追加 blob、页 blob、表或队列。

FileStorage 帐户提供了独特的性能专用特征，例如 IOPS 突发。 有关这些特征的更多信息，请参阅文件规划指南的[文件共享存储层](../files/storage-files-planning.md#storage-tiers)部分。

## <a name="naming-storage-accounts"></a>为存储帐户命名

为存储帐户命名时，请记住以下规则：

- 存储帐户名称必须为 3 到 24 个字符，并且只能包含数字和小写字母。
- 存储帐户名称在 Azure 中必须是唯一的。 没有两个存储帐户可以有相同的名称。

## <a name="performance-tiers"></a>性能层

根据所创建的存储帐户类型，可以在标准性能层和高级性能层之间进行选择。 下表总结了可用于哪种类型的存储帐户的性能层。

| 存储帐户类型 | 支持的性能层 |
|--|--|
| 常规用途 V2 | 标准版，高级版<sup>1</sup> |
| 常规用途 V1 | 标准版，高级版<sup>1</sup> |
| BlockBlobStorage | 高级 |
| FileStorage | 高级 |
| BlobStorage | 标准 |

<sup>1</sup>常规用途 v2 和常规用途 v1 帐户的高级性能仅适用于磁盘和页 blob。 用于块或追加 Blob 的高级性能仅在 BlockBlobStorage 帐户中提供。 用于文件的高级性能仅在 FileStorage 帐户中提供。

### <a name="general-purpose-storage-accounts"></a>通用存储帐户

可以针对下述两个性能层之一配置常规用途存储帐户：

- 用于存储 Blob、文件、表、队列和 Azure 虚拟机磁盘的标准性能层。 有关标准存储帐户的可伸缩性目标的详细信息，请参阅[标准存储帐户的可伸缩性目标](scalability-targets-standard-account.md)。
- 用于存储非托管虚拟机磁盘的高级性能层。 Microsoft 建议将托管磁盘与 Azure 虚拟机配合使用，而不是使用非托管磁盘。 若要详细了解高级性能层的可伸缩性目标，请参阅[高级页 Blob 存储帐户的可伸缩性目标](../blobs/scalability-targets-premium-page-blobs.md)。

### <a name="blockblobstorage-storage-accounts"></a>BlockBlobStorage 存储帐户

BlockBlobStorage 存储帐户提供用于存储块 blob 和追加 blob 的高级性能层。 有关详细信息，请参阅[高级块 blob 存储帐户的可伸缩性目标](../blobs/scalability-targets-premium-block-blobs.md)。

### <a name="filestorage-storage-accounts"></a>FileStorage 存储帐户

FileStorage 存储帐户为 Azure 文件共享提供高级性能层。 有关详细信息，请参阅 [Azure 文件存储可伸缩性和性能目标](../files/storage-files-scale-targets.md)。

## <a name="access-tiers-for-block-blob-data"></a>块 Blob 数据的访问层

Azure 存储提供不同的选项，适用于根据使用模型访问块 Blob 数据。 Azure 存储中的每个访问层都针对特定的数据使用模式进行了优化。 根据需要选择适当的访问层以后，即可以最经济有效的方式存储块 Blob 数据。

可用的访问层包括：

- **热** 访问层。 此层已优化，适合频繁访问存储帐户中的对象。 访问热层中的数据最经济高效，但存储费用较高。 新的存储帐户默认在热层中创建。
- **冷** 访问层。 此层已优化，适合存储大量不常访问且存储时间至少为 30 天的数据。 将数据存储在冷层中更经济高效，但与访问热层中的数据相比，访问该数据的费用可能较高。
- **存档** 层。 此层仅适用于单个块 Blob。 存档层已针对可以容忍数小时的检索延迟且会保留在存档层至少 180 天的数据进行优化。 存档层是最经济高效的数据存储选项。 但是，访问这些数据的开销比访问热层或冷层中的数据要高。

如果数据的使用模式有所更改，可以随时在这些访问层之间切换。 有关访问层的详细信息，请参阅 [Azure Blob 存储：热、冷和存档访问层](../blobs/storage-blob-storage-tiers.md)。

下表显示了可用于每种类型的存储帐户中的 blob 的访问层。

| 存储帐户类型 | 支持的访问层 |
|--|--|
| 常规用途 V2 | 热、冷、存档<sup>1</sup> |
| 常规用途 V1 | 不适用 |
| BlockBlobStorage | 不适用 |
| FileStorage | 空值 |
| BlobStorage | 热、冷、存档<sup>1</sup> |

<sup>1</sup> 存档存储和 blob 级别分层仅支持块 blob。 存档层仅在单个 Blob 的级别可用，在存储帐户级别不可用。 有关详细信息，请参阅 [Azure Blob 存储的访问层-热、冷和存档](../blobs/storage-blob-storage-tiers.md)。

> [!IMPORTANT]
> 更改现有存储帐户或 Blob 的访问层可能会产生额外费用。 有关详细信息，请参阅 [存储帐户计费](#storage-account-billing)。

## <a name="encryption"></a>Encryption

存储帐户中的所有数据均在服务端加密。 有关加密的详细信息，请参阅[静态数据的 Azure 存储服务加密](storage-service-encryption.md)。

## <a name="storage-account-endpoints"></a>存储帐户终结点

存储帐户在 Azure 中为数据提供唯一的命名空间。 存储在 Azure 存储中的每个对象都有一个地址，其中包含唯一的帐户名称。 将帐户名称与 Azure 存储服务终结点组合在一起，即可构成适用于存储帐户的终结点。

下表列出了每个 Azure 存储服务的终结点。

| 存储服务 | 终结点 |
|--|--|
| Blob 存储 | `https://<storage-account>.blob.core.windows.net` |
| Azure Data Lake Storage Gen2 | `https://<storage-account>.dfs.core.windows.net` |
| Azure 文件 | `https://<storage-account>.file.core.windows.net` |
| 队列存储 | `https://<storage-account>.queue.core.windows.net` |
| 表存储 | `https://<storage-account>.table.core.windows.net` |

> [!NOTE]
> 块 blob 和 blob 存储帐户仅公开 Blob 服务终结点。

构造用于访问存储帐户中某个对象的 URL，方法是：将对象在存储帐户中的位置追加到终结点。 例如，Blob 地址可能具有以下格式： http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*。

也可将存储帐户配置为对 Blob 使用自定义域。 有关详细信息，请参阅[为 Azure 存储帐户配置自定义域名](../blobs/storage-custom-domain-name.md)。  

## <a name="control-access-to-account-data"></a>控制对帐户数据的访问

默认情况下，只有你，即帐户所有者，才能使用帐户中的数据。 你可以控制哪些用户可以访问你的数据，以及这些用户可以有什么权限。

对存储帐户发出的每个请求都必须获得授权。 在服务级别，请求必须包含有效的 *Authorization* 标头。 具体说来，该标头包含服务在执行请求之前对其进行验证所需的所有信息。

可以通过下述任意方法授予对存储帐户中数据的访问权限：

- **Azure Active Directory：** 使用 Azure Active Directory (Azure AD) 凭据对访问 Blob 和队列数据的用户、组或其他标识进行身份验证。 如果某个标识的身份验证成功，则 Azure AD 会返回一个令牌，在对访问 Azure Blob 存储或队列存储的请求授权时可以使用该令牌。 有关详细信息，请参阅[使用 Azure Active Directory 对 Azure 存储访问进行身份验证](storage-auth-aad.md)。
- **共享密钥授权：** 使用存储帐户访问密钥构造一个连接字符串，应用程序在运行时将使用该连接字符串来访问 Azure 存储。 连接字符串中的值用于构造传递给 Azure 存储的 *Authorization* 标头。 有关详细信息，请参阅[配置 Azure 存储连接字符串](storage-configure-connection-string.md)。
- **共享访问签名：** (SAS) 的共享访问签名是一个令牌，该令牌允许对存储帐户中的资源进行委托访问。 SAS 令牌封装了向 URL 请求 Azure 存储请求所需的所有信息。 创建 SAS 时，可以指定 SAS 向其授予资源的权限以及权限有效的时间间隔。 可以使用 Azure AD 凭据或共享密钥来签署 SAS 令牌。 有关详细信息，请参阅[使用共享访问签名 (SAS) 授予对 Azure 存储资源的有限访问权限](storage-sas-overview.md)。

> [!NOTE]
> 与其他授权方式相比，使用 Azure AD 凭据对用户或应用程序进行身份验证可以提供优越的安全性和易用性。 虽然可以继续为应用程序使用共享密钥授权，但是，使用 Azure AD 不需要将帐户访问密钥与代码存储在一起。 也可以继续使用共享访问签名 (SAS) 授予对存储帐户中的资源的精细访问权限，但 Azure AD 提供了类似的功能，并且不需要管理 SAS 令牌，也不需要担心吊销已泄露的 SAS。
>
> Microsoft 建议尽可能使用 Azure 存储 blob 的 Azure AD 授权和队列应用程序。

## <a name="copying-data-into-a-storage-account"></a>将数据复制到存储帐户中

Microsoft 提供了用于从本地存储设备或第三方云存储提供程序导入数据的实用工具和库。 使用哪种解决方案取决于要传输的数据量。

从常规用途 v1 存储帐户或 Blob 存储帐户升级到常规用途 v2 帐户时，数据会自动迁移。 Microsoft 建议使用这种路径来升级帐户。 但是，如果决定将数据从常规用途 v1 帐户移到 Blob 存储帐户，则使用下述工具和库手动迁移数据。

### <a name="azcopy"></a>AzCopy

AzCopy 是一个 Windows 命令行实用程序，用于将数据高性能复制到 Azure 存储（或从中进行复制）。 可以使用 AzCopy 将数据从现有的常规用途存储帐户复制到 Blob 存储帐户，或者将数据从本地存储设备上传。 有关详细信息，请参阅[使用 AzCopy 命令行实用程序传输数据](./storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

### <a name="data-movement-library"></a>数据移动库

适用于 .NET 的 Azure 存储数据移动库基于为 AzCopy 提供技术支持的核心数据移动框架。 库旨在实现类似于 AzCopy 的高性能、可靠且简单的数据传输操作。 可以通过数据移动库以本机方式利用 AzCopy 功能。 有关详细信息，请参阅[适用于 .NET 的 Azure 存储数据移动库](https://github.com/Azure/azure-storage-net-data-movement)

### <a name="rest-api-or-client-library"></a>REST API 或客户端库

可以创建自定义应用程序，以便将数据从常规用途 v1 存储帐户迁移到 Blob 存储帐户中。 使用 Azure 客户端库之一或 Azure 存储服务 REST API。 Azure 存储对多种语言和平台（如 .NET、Java、C++、Node.JS、PHP、Ruby 和 Python）提供了内容丰富的客户端库。 客户端库提供高级功能，如重试逻辑、日志记录和并行上传。 也可以直接针对 REST API（可发出 HTTP/HTTPS 请求的任何语言都可调用它）进行开发。

有关 Azure 存储 REST API 的详细信息，请参阅 [Azure Storage Services REST API Reference](/rest/api/storageservices/)（Azure 存储服务 REST API 参考）。

> [!IMPORTANT]
> 使用客户端加密进行加密的 Blob 会将与加密相关的元数据与 Blob 一起存储。 如果复制使用客户端加密来加密的 Blob，请确保复制操作保留 Blob 元数据，尤其是与加密相关的元数据。 如果复制不包含此加密元数据的 Blob，则不能再次检索 Blob 内容。 有关加密相关元数据的详细信息，请参阅 [Azure 存储客户端加密](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

## <a name="storage-account-billing"></a>存储帐户计费

基于存储帐户使用情况的 Azure 存储计费。 存储帐户中的所有对象会作为组共同计费。 存储成本根据以下因素计算：

- **区域** 是指帐户所在的地理区域。
- **帐户类型** 是指所使用的存储帐户类型。
- **访问层** 是指你为常规用途 v2 或 Blob 存储帐户指定的数据使用模式。
- **容量** 是指用来存储数据的存储帐户配额量。
- **复制** 确定一次维护的数据副本的数量以及位置。
- **事务** 指的是对 Azure 存储的所有读取和写入操作。
- **数据流出量** 指的是传出某个 Azure 区域的数据。 当存储帐户中的数据被不在同一区域中运行的应用程序访问时，需要为数据流出量付费。 有关使用资源组对同一区域内的数据和服务进行分组以限制数据流出费用的信息，请参阅[什么是 Azure 资源组？](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group)。

[Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/) 页提供基于帐户类型、存储容量、复制和交易的详细定价信息。 [数据传输定价详细信息](https://azure.microsoft.com/pricing/details/data-transfers/) 提供了针对数据流出量的详细定价信息。 可以使用 [Azure 存储定价计算器](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) 来帮助估算成本。

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="next-steps"></a>后续步骤

- [创建存储帐户](storage-account-create.md)
- [创建块 Blob 存储帐户](../blobs/storage-blob-create-account-block-blob.md)
- [升级到常规用途 v2 存储帐户](storage-account-upgrade.md)
- [恢复删除的存储帐户](storage-account-recover.md)