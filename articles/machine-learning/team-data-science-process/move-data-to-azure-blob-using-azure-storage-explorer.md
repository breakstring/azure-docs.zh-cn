---
title: 使用 Azure 存储资源管理器移动 Blob 存储数据 - Team Data Science Process
description: 了解如何使用 Azure 存储资源管理器上传和下载 Azure Blob 存储中的数据。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 53cb8cdd1c5f9824b07b16b8b6c70648603b9f38
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "98788903"
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>使用 Azure 存储资源管理器将数据移入和移出 Azure Blob 存储
Azure 存储资源管理器是 Microsoft 免费提供的应用，可用于在 Windows、macOS 和 Linux 上处理 Azure 存储数据。 本主题介绍如何使用它从 Azure Blob 存储上传和下载数据。 该工具可以从 [Microsoft Azure 存储资源管理器](https://storageexplorer.com/)下载。

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> 如果正在使用通过 [Azure 中数据科学虚拟机](../data-science-virtual-machine/overview.md)提供的脚本设置的 VM，则 VM 上已安装 Azure 存储资源管理器。
> 
> [!NOTE]
> 有关 Azure Blob 存储的完整介绍，请参阅 [Azure Blob 基本知识](../../storage/blobs/storage-quickstart-blobs-dotnet.md) 和 [azure blob 服务](/rest/api/storageservices/Blob-Service-Concepts)。   
> 
> 

## <a name="prerequisites"></a>必备条件
本文档假定已有 Azure 订阅、存储帐户，以及该帐户对应的存储密钥。 在上传/下载数据之前，必须知道 Azure 存储帐户名称和帐户密钥。 

* 若要设置 Azure 订阅，请参阅[免费试用一个月版](https://azure.microsoft.com/pricing/free-trial/)。
* 若要查看存储帐户创建说明并了解如何获取帐户和密钥信息，请参阅[关于 Azure 存储帐户](../../storage/common/storage-account-create.md)。 记下存储帐户的访问密钥，因为在使用 Azure 存储资源管理器工具连接到帐户时需要此密钥。
* Azure Storage Explorer 工具可以从 [Microsoft Azure 存储资源管理器](https://storageexplorer.com/)下载。 在安装过程中接受默认值。

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>使用 Azure 存储资源管理器
以下步骤介绍如何使用 Azure 存储资源管理器上传/下载数据。 

1. 启动 Microsoft Azure 存储资源管理器
2. 要打开“**登录到帐户**”向导，请选择“**Azure 帐户设置**”图标，然后选择“**添加帐户**”并输入凭据。 
![添加 Azure 存储帐户](./media/move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. 要打开“连接到 Azure 存储”向导，请选择“连接到 Azure 存储”图标 。 ![单击“连接到 Azure 存储”](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. 在“连接到 Azure 存储”向导中，输入 Azure 存储帐户中的访问密钥，然后选择“下一步” 。 ![输入 Azure 存储帐户访问密钥](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. 在“**帐户名**”框中，输入存储帐户名称，并选择“**下一步**”。 ![附加外部存储](./media/move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. 现在应显示添加的存储帐户。 要在存储帐户中创建一个 blob 容器，请用鼠标右键单击帐户中的“**Blob 容器**”节点，选择“**创建 Blob 容器**”，并输入一个名称。
7. 要将数据上传到容器，请选择目标容器，然后单击“上传”按钮。
![存储帐户](./media/move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. 单击“文件”框右侧的“...”，选择要从文件系统上载的一个或多个文件，并单击“上载”开始上载文件。![上载文件](./media/move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. 要下载数据，请在相应容器中选择要下载的 blob，并单击“**下载**”。 ![下载文件](./media/move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)
