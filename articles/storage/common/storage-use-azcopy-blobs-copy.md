---
title: 在 Azure 存储帐户和 AzCopy v10 之间复制 blob |Microsoft Docs
description: 本文包含一系列 AzCopy 示例命令，可帮助你在存储帐户之间复制 blob。
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 524e3f70f9588cfae2c739722fc1a44e683f9a7f
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2020
ms.locfileid: "97617283"
---
# <a name="copy-blobs-between-azure-storage-accounts-by-using-azcopy-v10"></a>使用 AzCopy v10 在 Azure 存储帐户之间复制 blob

可以使用 AzCopy v10 命令行实用程序在存储帐户之间复制 blob、目录和容器。 

若要查看其他类型任务的示例，例如上传文件、下载 blob 以及与 Blob 存储同步，请参阅本文 [后续步骤](#next-steps) 部分中提供的链接。

AzCopy 使用[服务器到服务器](/rest/api/storageservices/put-block-from-url) [API](/rest/api/storageservices/put-page-from-url)，因此，数据会直接在存储服务器之间复制。 这些复制操作不会占用计算机的网络带宽。

若要下载 AzCopy 并了解你可以向存储服务提供授权凭据的方式，请参阅 [AzCopy 入门](storage-use-azcopy-v10.md)。 

## <a name="guidelines"></a>指南

将以下准则应用于 AzCopy 命令。 

- 客户端必须对源和目标存储帐户具有网络访问权限。 若要了解如何配置每个存储帐户的网络设置，请参阅 [配置 Azure 存储防火墙和虚拟网络](storage-network-security.md?toc=/azure/storage/blobs/toc.json)。

- 向每个源 URL 追加一个 SAS 令牌。 

  如果使用 Azure Active Directory (Azure AD) 提供授权凭据，则只能从目标 URL 中省略 SAS 令牌。 请确保已在目标帐户中设置了适当的角色。 请参阅[选项 1：使用 Azure Active Directory](storage-use-azcopy-v10.md?toc=/azure/storage/blobs/toc.json#option-1-use-azure-active-directory)。 

  本文中的示例假设已通过使用 Azure AD 对标识进行身份验证，因此，这些示例将从目标 URL 中省略 SAS 令牌。

-  如果复制到高级块 blob 存储帐户，请通过将设置 `s2s-preserve-access-tier` 为 `false` (例如：) ，从复制操作中省略 blob 的访问层 `--s2s-preserve-access-tier=false` 。 高级块 Blob 存储帐户不支持访问层。 

- 如果从具有分层命名空间的帐户复制到或，请 `blob.core.windows.net` `dfs.core.windows.net` 在 URL 语法中使用而不是。 [Data Lake Storage 上的多协议访问](../blobs/data-lake-storage-multi-protocol-access.md) 允许你使用 `blob.core.windows.net` ，而且它是帐户到帐户复制方案的唯一受支持的语法。 

- 可以通过设置环境变量的值来提高复制操作的吞吐量 `AZCOPY_CONCURRENCY_VALUE` 。 有关详细信息，请参阅[优化吞吐量](storage-use-azcopy-configure.md#optimize-throughput)。 

- 如果源 blob 具有索引标记，并且你想要保留这些标记，则必须将它们重新应用于目标 blob。 有关如何设置索引标记的信息，请参阅本文的将 [Blob 复制到另一个具有索引标记的存储帐户](#copy-between-accounts-and-add-index-tags) 部分。

## <a name="copy-a-blob"></a>复制 Blob

使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令将 blob 复制到另一个存储帐户。 

> [!TIP]
> 此示例将路径参数括在单引号 ('') 内。 在除 Windows 命令 Shell (cmd.exe) 以外的所有命令 shell 中，都请使用单引号。 如果使用 Windows 命令 Shell (cmd.exe)，请用双引号 ("") 而不是单引号 ('') 括住路径参数。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **示例** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

复制操作是同步的，因此，当命令返回时，表示已复制所有文件。 

## <a name="copy-a-directory"></a>复制目录

使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令将目录复制到另一个存储帐户。 

> [!TIP]
> 此示例将路径参数括在单引号 ('') 内。 在除 Windows 命令 Shell (cmd.exe) 以外的所有命令 shell 中，都请使用单引号。 如果使用 Windows 命令 Shell (cmd.exe)，请用双引号 ("") 而不是单引号 ('') 括住路径参数。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **示例** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

复制操作是同步的，因此，当命令返回时，表示已复制所有文件。

## <a name="copy-a-container"></a>复制容器

使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令将容器复制到另一个存储帐户。

> [!TIP]
> 此示例将路径参数括在单引号 ('') 内。 在除 Windows 命令 Shell (cmd.exe) 以外的所有命令 shell 中，都请使用单引号。 如果使用 Windows 命令 Shell (cmd.exe)，请用双引号 ("") 而不是单引号 ('') 括住路径参数。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **示例** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

复制操作是同步的，因此，当命令返回时，表示已复制所有文件。

## <a name="copy-containers-directories-and-blobs"></a>复制容器、目录和 blob

使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令将所有容器、目录和 blob 复制到另一个存储帐户。

> [!TIP]
> 此示例将路径参数括在单引号 ('') 内。 在除 Windows 命令 Shell (cmd.exe) 以外的所有命令 shell 中，都请使用单引号。 如果使用 Windows 命令 Shell (cmd.exe)，请用双引号 ("") 而不是单引号 ('') 括住路径参数。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **示例** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

复制操作是同步的，因此，当命令返回时，表示已复制所有文件。

<a id="copy-between-accounts-and-add-index-tags"></a>

## <a name="copy-blobs-and-add-index-tags"></a>复制 blob 并添加索引标记

将 blob 复制到另一个存储帐户，并将 [blob 索引标记 (预览) ](../blobs/storage-manage-find-blobs.md) 添加到目标 blob。

如果使用 Azure AD 授权，则必须向安全主体分配 [存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) 角色，或者必须 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` 通过自定义 azure 角色向其授予对 [Azure 资源提供程序操作](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) 的权限。 如果你使用共享访问签名 (SAS) 令牌，则该令牌必须通过 SAS 权限提供对 blob 的标记的访问权限 `t` 。

若要添加标记，请使用 `--blob-tags` 选项以及 URL 编码的键值对。 

例如，若要添加键 `my tag` 和值 `my tag value` ，请将添加 `--blob-tags='my%20tag=my%20tag%20value'` 到目标参数。 

使用与号)  (分隔多个索引标记 `&` 。  例如，如果要添加键 `my second tag` 和值 `my second tag value` ，则完整选项字符串将为 `--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` 。

下面的示例演示如何使用 `--blob-tags` 选项。

> [!TIP]
> 这些示例用单引号将路径参数 ( "" ) 。 在除 Windows 命令 Shell (cmd.exe) 以外的所有命令 shell 中，都请使用单引号。 如果使用 Windows 命令 Shell (cmd.exe)，请用双引号 ("") 而不是单引号 ('') 括住路径参数。

|    |     |
|--------|-----------|
| **Blob** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **Directory** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **容器** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags="--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **帐户** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive --blob-tags="--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |

复制操作是同步的，因此，当命令返回时，表示已复制所有文件。

> [!NOTE]
> 如果为源指定目录、容器或帐户，则复制到目标的所有 blob 都将具有在命令中指定的相同标记。

## <a name="copy-with-optional-flags"></a>带有可选标志的副本

可以通过使用可选标志来调整复制操作。 下面是几个示例。

|方案|标志|
|---|---|
|将 Blob 复制为块 Blob、页 Blob 或追加 Blob。|**--blob-type**=\[BlockBlob\|PageBlob\|AppendBlob\]|
|复制到特定访问层（如存档层）。|**--block-blob-tier**=\[None\|Hot\|Cool\|Archive\]|
|自动解压缩文件。|**--decompress**=\[gzip\|deflate\]|

有关完整列表，请参阅[选项](storage-ref-azcopy-copy.md#options)。 

## <a name="next-steps"></a>后续步骤

在以下文章中查找更多示例：

- [示例：上载](storage-use-azcopy-blobs-upload.md)
- [示例：下载](storage-use-azcopy-blobs-download.md)
- [示例：同步](storage-use-azcopy-blobs-synchronize.md)
- [示例：Amazon S3 存储桶](storage-use-azcopy-s3.md)
- [示例： Azure 文件](storage-use-azcopy-files.md)
- [教程：使用 AzCopy 将本地数据迁移到云存储](storage-use-azcopy-migrate-on-premises-data.md)
- [对 AzCopy 进行配置、优化和故障排除](storage-use-azcopy-configure.md)