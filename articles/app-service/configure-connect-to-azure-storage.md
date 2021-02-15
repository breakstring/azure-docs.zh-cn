---
title: '将 Azure 存储 (容器) '
description: 了解如何在 Azure App Service 中的容器化应用中附加自定义网络共享。 在应用之间共享文件、远程管理静态内容和本地访问等。
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: ab27de9274043439f790a8fecd443223e5f26b08
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736198"
---
# <a name="access-azure-storage-preview-as-a-network-share-from-a-container-in-app-service"></a>从应用服务中的容器作为网络共享访问 Azure 存储 (预览) 

::: zone pivot="container-windows"

本指南说明如何将 Azure 存储文件作为网络共享附加到应用服务中的 windows 容器。 仅支持 [Azure 文件共享](../storage/files/storage-how-to-use-files-cli.md) 和 [高级文件共享](../storage/files/storage-how-to-create-premium-fileshare.md) 。 优势包括内容受保护，内容可移植，可以访问多个应用以及使用多种传输方法。

> [!NOTE]
>应用服务中的 Azure 存储为 **预览版**，**不支持****生产方案**。

::: zone-end

::: zone pivot="container-linux"

本指南说明如何将 Azure 存储附加到 Linux 容器应用服务。 优点包括受保护的内容、内容可移植性、持久存储、对多个应用的访问和多种传输方法。

> [!NOTE]
>应用服务中的 Azure 存储处于 Linux 和用于容器的 Web 应用上的应用服务 **预览版中** 。 对于 **生产方案**，**不支持** 此方法。

::: zone-end

## <a name="prerequisites"></a>先决条件

::: zone pivot="container-windows"

- [Azure App Service 中的现有 Windows 容器应用](quickstart-custom-container.md)
- [创建 Azure 文件共享](../storage/files/storage-how-to-use-files-cli.md)
- [将文件上传到 Azure 文件共享](../storage/files/storage-how-to-create-file-share.md)

::: zone-end

::: zone pivot="container-linux"

- [Linux 应用上的现有应用服务](index.yml)。
- [Azure 存储帐户](../storage/common/storage-account-create.md?tabs=azure-cli)
- [Azure 文件共享和目录](../storage/files/storage-how-to-use-files-cli.md)。

::: zone-end

> [!NOTE]
> Azure 文件是非默认存储并单独计费，不包括在 web 应用中。 由于基础结构的限制，它不支持使用防火墙配置。
>

## <a name="limitations"></a>限制

::: zone pivot="container-windows"

- 目前，应用服务中的 Azure 存储 **不支持** 引入你自己的代码方案 (非容器化 Windows 应用) 。
- 由于基础结构限制，应用服务中的 Azure 存储 **不支持** 使用 **存储防火墙** 配置。
- 利用应用服务的 Azure 存储，可以为每个应用指定 **最多5个** 装入点。
- 无法通过应用服务 FTP/FTPs 终结点访问装载到应用的 Azure 存储。 使用 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)。

::: zone-end

::: zone pivot="container-linux"

- 应用服务中的 azure 存储支持装载 **Azure 文件容器** (读/写) 和 **azure Blob 容器** (只读) 
- 利用应用服务中的 Azure 存储，可以为每个应用指定 **最多5个** 装入点。
- 无法通过应用服务 FTP/FTPs 终结点访问装载到应用的 Azure 存储。 使用 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)。

::: zone-end

## <a name="link-storage-to-your-app"></a>将存储链接到你的应用

::: zone pivot="container-windows"

创建 [Azure 存储帐户、文件共享和目录](#prerequisites)后，现在可以通过 Azure 存储空间配置应用。

若要将 Azure 文件共享装载到应用服务应用中的目录，请使用 [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) 命令。 存储类型必须为 AzureFiles。

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory of form c:<directory name> >
```

应为要链接到 Azure 文件共享的任何其他目录执行此操作。

::: zone-end

::: zone pivot="container-linux"

创建 [Azure 存储帐户、文件共享和目录](#prerequisites)后，现在可以通过 Azure 存储空间配置应用。

若要在应用服务应用中将存储帐户装载到目录中，请使用 [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) 命令。 存储类型可以是 AzureBlob 或 AzureFiles。 本示例使用 AzureFiles。 装载路径设置对应于要装载到 Azure 存储的容器中的文件夹。 如果将其设置为 "/"，则会将整个容器装载到 Azure 存储中。


> [!CAUTION]
> 指定为 web 应用中的装载路径的目录应为空。 添加外部装载时，此目录中存储的任何内容都将被删除。 如果要迁移现有应用的文件，在开始之前，请备份你的应用及其内容。
>

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory>
```

应当对要链接到存储帐户的任何其他目录执行此操作。

::: zone-end

## <a name="verify-linked-storage"></a>验证链接的存储

共享链接到应用后，可通过运行以下命令进行验证：

```azurecli
az webapp config storage-account list --resource-group <resource-group> --name <app-name>
```

## <a name="next-steps"></a>后续步骤

::: zone pivot="container-windows"

- [使用自定义容器将自定义软件迁移到 Azure App Service](tutorial-custom-container.md?pivots=container-windows)。

::: zone-end

::: zone pivot="container-linux"

- [配置自定义容器](configure-custom-container.md?pivots=platform-linux)。

::: zone-end