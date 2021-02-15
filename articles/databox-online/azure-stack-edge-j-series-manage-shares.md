---
title: Azure Stack Edge Pro GPU 共享管理 |Microsoft Docs
description: 介绍如何使用 Azure 门户管理 Azure Stack Edge Pro GPU 上的共享。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/04/2021
ms.author: alkohli
ms.openlocfilehash: b1ff10290e0059295f14b55e90ed05e9690c9f5c
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97896576"
---
# <a name="use-azure-portal-to-manage-shares-on-your-azure-stack-edge-pro"></a>使用 Azure 门户管理 Azure Stack Edge Pro 上的共享

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

本文介绍如何在 Azure Stack Edge Pro 上管理共享。 可以通过 Azure 门户或通过本地 web UI 管理 Azure Stack Edge Pro。 使用 Azure 门户可以添加、删除和刷新共享，或者同步与共享相关联的存储帐户的存储密钥。 本文适用于 Azure Stack Edge Pro GPU、Azure Stack Edge Pro R 和 Azure Stack 边缘迷你 R 设备。

## <a name="about-shares"></a>关于共享

若要将数据传输到 Azure，需要在 Azure Stack Edge Pro 上创建共享。 在 Azure Stack Edge Pro 设备上添加的共享可以是将数据推送到云的本地共享或共享。

 - **本地共享**：如果想要在设备上本地处理数据，请使用这些共享。
 - **共享**：如果想要将设备数据自动推送到云中的存储帐户，请使用这些共享。 所有云功能（如 **刷新** 和 **同步存储密钥** ）都适用于共享。


## <a name="add-a-share"></a>添加共享

在 Azure 门户中执行以下步骤，以创建共享。

1. 在 Azure 门户中转到 Azure Stack Edge 资源，然后在 " **云存储网关 > 共享**"。 在命令栏上选择“+ 添加共享”。

    ![选择“添加共享”](media/azure-stack-edge-j-series-manage-shares/add-share-1.png)

2. 在“添加共享”中指定共享设置。 提供共享的唯一名称。
    
    共享名称只能包含数字、小写字母和连字符。 共享名称必须为 3 到 63 个字符长，并且必须以字母或数字开头。 每个连字符的前后必须为非连字符字符。

3. 选择共享的 **类型**。 类型可以是“SMB”或“NFS”，默认为“SMB”。   SMB 是 Windows 客户端的标准，NFS 用于 Linux 客户端。 根据你选择 SMB 共享还是 NFS 共享，显示的选项略有不同。

4. 提供共享所在的 **存储帐户**。 如果还没有容器，则使用共享名称在存储帐户中创建一个容器。 如果该容器已存在，则使用现有容器。

5. 从下拉列表中，选择 "块 blob、页 blob 或文件中的 **存储服务** "。 所选服务的类型取决于数据需要以何种格式驻留在 Azure 中。 例如，在此实例中，我们想要将数据作为块 blob 驻留在 Azure 中，因此我们选择 " **块 blob**"。 如果选择 " **页 Blob**"，则必须确保数据的对齐方式为512字节。 对于始终为512字节的 Vhd 或 VHDX，请使用 **页 blob** 。

6. 此步骤取决于你是创建 SMB 共享还是 NFS 共享。
    - **如果创建 SMB 共享** - 在“所有特权本地用户”字段中，选择“新建”或“使用现有”。 如果创建新的本地用户，请提供 **用户名**、**密码** 和确认密码。 这样就会为本地用户分配权限。 在此处分配权限以后，即可使用文件资源管理器修改这些权限。

        ![添加 SMB 共享](media/azure-stack-edge-j-series-manage-shares/add-smb-share.png)

        如果针对此共享数据勾选“仅允许读取操作”，则可以指定只读用户。
    - **如果创建 NFS 共享** - 需要提供能够访问共享的 **受允许客户端的 IP 地址**。

        ![添加 NFS 共享](media/azure-stack-edge-j-series-manage-shares/add-nfs-share.png)

7. 若要轻松地从 Edge 计算模块访问共享，请使用本地装入点。 选择“将共享与 Edge 计算配合使用”，这样就会在创建共享后自动装载共享。 选择此选项后，Edge 模块也可将计算与本地装入点配合使用。

8. 单击 " **创建** " 创建共享。 将通知你“正在创建共享”。 创建具有指定设置的共享后，" **共享** " 边栏选项卡会进行更新以反映新的共享。

## <a name="add-a-local-share"></a>添加本地共享

1. 在 Azure 门户中转到 Azure Stack Edge 资源，然后在 " **云存储网关 > 共享**"。 在命令栏上选择“+ 添加共享”。

    ![选择添加共享2](media/azure-stack-edge-j-series-manage-shares/add-local-share-1.png)

2. 在“添加共享”中指定共享设置。 提供共享的唯一名称。
    
    共享名称只能包含数字、小写字母、大写字母和连字符。 共享名称必须为 3 到 63 个字符长，并且必须以字母或数字开头。 每个连字符的前后必须为非连字符字符。

3. 选择共享的 **类型**。 类型可以是“SMB”或“NFS”，默认为“SMB”。   SMB 是 Windows 客户端的标准，NFS 用于 Linux 客户端。 根据你选择 SMB 共享还是 NFS 共享，显示的选项略有不同。

   > [!IMPORTANT]
   > 确保未对所用的 Azure 存储帐户设置不可变策略，才可将该帐户用于 Azure Stack Edge Pro 或 Data Box Gateway 设备。 有关详细信息，请参阅[为 blob 存储设置和管理不可变策略](../storage/blobs/storage-blob-immutability-policies-manage.md)。

4. 若要轻松地从 Edge 计算模块访问共享，请使用本地装入点。 选择“将共享与 Edge 计算配合使用”，使 Edge 模块能够将计算与本地装入点配合使用。

5. 选择“配置为 Edge 本地共享”。 本地共享中的数据将保留在设备本地。 可在本地处理此数据。

6. 在“所有特权本地用户”字段中，选择“新建”或“使用现有项”。

7. 选择“创建”  。 

    ![创建本地共享](media/azure-stack-edge-j-series-manage-shares/add-local-share-2.png)

    此时会显示一条通知，指出正在创建共享。 创建具有指定设置的共享后，" **共享** " 边栏选项卡会进行更新以反映新的共享。

    ![查看更新的“共享”边栏选项卡](media/azure-stack-edge-j-series-manage-shares/add-local-share-3.png)
    
    选择该共享，以查看其 Edge 计算模块的本地装入点。

    ![查看本地共享详细信息](media/azure-stack-edge-j-series-manage-shares/add-local-share-4.png)

## <a name="mount-a-share"></a>装载共享

如果在 Azure Stack Edge Pro 设备上配置计算之前创建了共享，则需要安装该共享。 通过以下步骤装载共享。


1. 在 Azure 门户中转到 Azure Stack Edge 资源，然后在 " **云存储网关 > 共享**"。 从共享列表中选择要装载的共享。 “用于计算”列会将所选共享的状态显示为“已禁用”。

    ![选择共享](media/azure-stack-edge-j-series-manage-shares/mount-share-1.png)

2. 选择“装载”。

    ![选择装载](media/azure-stack-edge-j-series-manage-shares/mount-share-2.png)

3. 出现确认提示时，选择“是”。 此时会装载共享。

    ![确认装载](media/azure-stack-edge-j-series-manage-shares/mount-share-3.png)

4. 装载共享以后，转到共享列表。 此时会看到“用于计算”列将共享状态显示为“已启用”。

    ![装载的共享](media/azure-stack-edge-j-series-manage-shares/mount-share-4.png)

5. 再次选择共享，查看该共享的本地装入点。 Edge 计算模块使用共享的这个本地装入点。

    ![共享的本地装入点](media/azure-stack-edge-j-series-manage-shares/mount-share-5.png)

## <a name="unmount-a-share"></a>卸载共享

在 Azure 门户中执行以下步骤，以卸载共享。

1. 在 Azure 门户中转到 Azure Stack Edge 资源，然后在 " **云存储网关 > 共享**"。 从共享列表中选择要卸载的共享。 需确保卸载的共享不被任何模块使用。 如果共享被某个模块使用，则相应模块会出问题。

    ![选择共享2](media/azure-stack-edge-j-series-manage-shares/unmount-share-1.png)

2.  选择 " **卸载**"。

    ![选择卸载](media/azure-stack-edge-j-series-manage-shares/unmount-share-2.png)

3. 出现确认提示时，选择“是”。 此时会卸载共享。

    ![确认卸载](media/azure-stack-edge-j-series-manage-shares/unmount-share-3.png)

4. 卸载共享以后，转到共享列表。 此时会看到“用于计算”列将共享状态显示为“已禁用”。

    ![卸载的共享](media/azure-stack-edge-j-series-manage-shares/unmount-share-4.png)

## <a name="delete-a-share"></a>删除共享

在 Azure 门户中执行以下步骤，以删除共享。

1. 在共享列表中，选择并单击要删除的共享。

    ![选择共享3](media/azure-stack-edge-j-series-manage-shares/delete-share-1.png)

2. 单击 **“删除”** 。

    ![单击“删除”](media/azure-stack-edge-j-series-manage-shares/delete-share-2.png)

3. 当系统提示你进行确认时，单击 **“是”**。

    ![确认删除](media/azure-stack-edge-j-series-manage-shares/delete-share-3.png)

共享列表将会更新，以反映删除后的结果。

## <a name="refresh-shares"></a>刷新共享

使用刷新功能可以刷新共享的内容。 刷新共享时，将启动搜索，以查找自上次刷新以来添加到云中的所有 Azure 对象，包括 Blob 和文件。 然后，将下载这些附加文件以刷新设备上的共享内容。

> [!IMPORTANT]
> - 无法刷新本地共享。
> - 权限和访问控制列表 (ACL) 不会通过刷新操作保存。 

在 Azure 门户中执行以下步骤，以刷新共享。

1.  在 Azure 门户中转到“共享”。 选择并单击要刷新的共享。

    ![选择共享4](media/azure-stack-edge-j-series-manage-shares/refresh-share-1.png)

2.  单击“刷新”。 

    ![单击“刷新”](media/azure-stack-edge-j-series-manage-shares/refresh-share-2.png)
 
3.  当系统提示你进行确认时，单击 **“是”**。 此时会启动一个作业来刷新本地共享的内容。

    ![确认刷新](media/azure-stack-edge-j-series-manage-shares/refresh-share-3.png)
 
4.   刷新正在进行时，上下文菜单中的刷新选项会灰显。 单击作业通知以查看刷新作业的状态。

5.   刷新所需的时间取决于 Azure 容器中的文件数以及设备上的文件数。 成功完成刷新后，共享时间戳将会更新。 即使刷新已部分失败，刷新操作也被视为成功，并时间戳会更新。 刷新错误日志也会更新。

![更新的时间戳](media/azure-stack-edge-j-series-manage-shares/refresh-share-4.png)
 
失败时会引发警报。 警报中详细描述了原因，以及问题的建议解决方法。 警报中还会提供某个文件的链接，该文件包含失败的完整摘要，其中指出了无法更新或删除哪些文件。

## <a name="sync-pinned-files"></a>同步固定文件 

若要自动同步固定文件，请在 Azure 门户中执行以下步骤：
 
1. 选择现有的 Azure 存储帐户。 

2. 中转到 " **容器** "，然后选择 " **+ 容器** " 创建容器。 将此容器命名为 *newcontainer*。 将 **公共访问级别** 设置为 "容器"。

    ![固定文件的自动同步1](media/azure-stack-edge-j-series-manage-shares/image-1.png)

3. 选择容器名称并设置以下元数据：  

    - Name = "固定" 
    - 值 = "True" 

    ![自动同步固定文件2](media/azure-stack-edge-j-series-manage-shares/image-2.png)
 
4. 在设备上创建新共享。 通过选择 "现有容器" 选项，将其映射到固定容器。 将共享标记为只读。 创建新用户并指定此共享的用户名和相应密码。  

    ![固定文件的自动同步3](media/azure-stack-edge-j-series-manage-shares/image-3.png)
 
5. 在 Azure 门户中，浏览到所创建的容器。 上传要固定到 newcontainer 的文件，该文件的元数据设置为 "固定"。 

6. 选择 "刷新 Azure 门户设备的 **数据** "，以下载该特定 Azure 存储容器的固定策略。  

    ![固定文件的自动同步4](media/azure-stack-edge-j-series-manage-shares/image-4.png)
 
7. 访问在设备上创建的新共享。 已上传到存储帐户的文件现在已下载到本地共享。 

    当设备断开连接或重新连接时，它会触发刷新。 刷新将仅关闭那些已更改的文件。 


## <a name="sync-storage-keys"></a>同步存储密钥

如果已轮换存储帐户密钥，则需要同步存储访问密钥。 同步可帮助设备获取存储帐户的最新密钥。

在 Azure 门户中执行以下步骤，以同步存储访问密钥。

1. 在资源中转到“概述”。 在共享列表中，选择并单击与需要同步的存储帐户相关联的共享。

    ![选择包含相关存储帐户的共享](media/azure-stack-edge-j-series-manage-shares/sync-storage-key-1.png)

2. 单击“同步存储密钥”。 当系统提示确认时，单击“是”。

     ![选择“同步存储密钥”](media/azure-stack-edge-j-series-manage-shares/sync-storage-key-2.png)

3. 同步完成后，请退出对话框。

>[!NOTE]
> 只需针对给定的存储帐户执行此操作一次。 对于与同一存储帐户关联的所有共享，无需重复此操作。


## <a name="next-steps"></a>后续步骤

- 了解如何[通过 Azure 门户管理用户](azure-stack-edge-j-series-manage-users.md)。