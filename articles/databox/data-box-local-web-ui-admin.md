---
title: 使用本地 web UI 管理 Azure Data Box/Azure Data Box Heavy
description: 介绍如何使用本地 Web UI 管理 Data Box 和 Data Box Heavy 设备
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 12/18/2020
ms.author: alkohli
ms.openlocfilehash: e84f39fa5b9245d1874e60d651156e99c0885040
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2020
ms.locfileid: "97678913"
---
# <a name="use-the-local-web-ui-to-administer-your-data-box-and-data-box-heavy"></a>使用本地 Web UI 管理 Data Box 和 Data Box Heavy

本文介绍 Data Box 和 Data Box Heavy 设备上执行的一些配置和管理任务。 可以通过 Azure 门户 UI 和设备的本地 Web UI 管理 Data Box 和 Data Box Heavy 设备。 本文重点介绍使用本地 web UI 执行的任务。

用于 Data Box 和 Data Box Heavy 的本地 web UI 用于设备的初始配置。 你还可以使用本地 web UI 关闭或重新启动设备、运行诊断测试、更新软件、查看复制日志、从设备中擦除本地数据以及为 Microsoft 支持部门生成支持包。 在具有两个独立节点的 Data Box Heavy 设备上，可以访问对应于每个设备节点的两个独立本地 Web UI。

## <a name="generate-support-package"></a>生成支持包

如果遇到任何设备问题，可通过系统日志创建支持包。 Microsoft 支持部门使用此包来解决此问题。

要生成支持包，请执行以下步骤：

1. 在本地 web UI 中，前往 " **联系支持** 人员"。 还可以选择 " **包括内存转储**"。 然后选择 " **创建支持包**"。

    内存转储是指在系统出现故障后保存的设备内存内容。

    不应选择 " **包括内存转储** " 选项，除非支持人员请求一个选项。 需要很长时间来收集包含内存转储的支持包，并包含敏感数据。

    ![创建支持包 1](media/data-box-local-web-ui-admin/create-support-package-1.png)

    将收集到一个支持包。 如果只包含系统日志，此操作需要几分钟时间。 如果包括内存转储，则需要花费很长时间。

    ![创建支持包 2](media/data-box-local-web-ui-admin/create-support-package-2.png)

2. 支持包创建完成后，选择 " **下载支持包**"。

    ![创建支持包3](media/data-box-local-web-ui-admin/create-support-package-3.png)

3. 浏览并选择下载位置。 打开文件夹以查看内容。

    ![创建支持包 4](media/data-box-local-web-ui-admin/create-support-package-4.png)

## <a name="erase-local-data-from-your-device"></a>擦除设备上的本地数据

在将本地数据返回到 Azure 数据中心之前，可以使用本地 web UI 从设备中擦除本地数据。

> [!IMPORTANT]
> 无法反转数据擦除。 在从设备中擦除本地数据之前，请务必备份这些文件。

若要从设备中擦除本地数据，请执行以下步骤：

1. 在本地 web UI 中，请执行 " **数据清除**"。
2. 输入设备密码，并选择 " **清除数据**"。

    ![设备的数据清除选项](media/data-box-local-web-ui-admin/erase-local-data-1.png)

3. 出现确认提示时，选择 **"是"** 以继续。 数据删除可能需要50分钟。

   在从设备中擦除本地数据之前，请务必备份本地数据。 无法反转数据擦除。

    ![数据清除确认提示](media/data-box-local-web-ui-admin/erase-local-data-2.png)

## <a name="shut-down-or-restart-your-device"></a>关闭或重启设备

可使用本地 Web UI 关闭或重启设备。 建议在重新启动之前，先在主机和设备上使共享脱机。 这样做会使数据损坏的可能性降至最低。 确保关闭设备时未进行数据复制。

若要关闭设备，请执行以下步骤。

1. 在本地 Web UI 中，转到“关闭或重启”。

2. 选择“关闭”。

    ![关闭 Data Box 1](media/data-box-local-web-ui-admin/shut-down-local-web-ui-1.png)

3. 出现确认提示时，选择 **"确定"** 继续。

    ![关闭 Data Box 2](media/data-box-local-web-ui-admin/shut-down-local-web-ui-2.png)

关闭设备后，使用前面板上的电源按钮打开设备。

要重启 Data Box，请执行以下步骤。

1. 在本地 Web UI 中，转到“关闭或重启”。
2. 选择“重启”。 

    ![重启 Data Box 1](media/data-box-local-web-ui-admin/restart-local-web-ui-1.png)

3. 出现确认提示时，选择 **"确定"** 继续。

   设备将关闭并重启。

## <a name="download-bom-or-manifest-files"></a>下载 BOM 或清单文件

BOM 或清单文件包含复制到 Data Box 或 Data Box Heavy 的文件的列表。 当您准备要装运的设备时，将生成导入顺序的这些文件。

在开始之前，请按照以下步骤下载导入顺序的 BOM 或清单文件：

1. 转到设备的本地 Web UI。 验证设备是否已完成 **准备交付** 步骤。 设备准备完成后，设备状态将显示为“已准备好寄送”。

    ![设备已准备好寄送](media/data-box-local-web-ui-admin/prepare-to-ship-3.png)

2. 选择 " **下载文件列表** "，下载 Data Box 上复制的文件列表。

    <!-- ![Select Download list of files](media/data-box-portal-admin/download-list-of-files.png) -->

3. 在文件资源管理器中，将根据用于连接到设备的协议和使用的 Azure 存储类型来生成不同的文件列表。

    <!-- ![Files for storage type and connection protocol](media/data-box-portal-admin/files-storage-connection-type.png) -->
    ![根据存储类型和连接协议生成的文件](media/data-box-local-web-ui-admin/prepare-to-ship-5.png)

   下表将文件名映射到 Azure 存储类型和使用的连接协议。

    |文件名  |Azure 存储类型  |使用的连接协议 |
    |---------|---------|---------|
    |utSAC1_202006051000_BlockBlob-BOM.txt     |块 Blob         |SMB/NFS         |
    |utSAC1_202006051000_PageBlob-BOM.txt     |页 Blob         |SMB/NFS         |
    |utSAC1_202006051000_AzFile-BOM.txt    |Azure 文件         |SMB/NFS         |
    |utsac1_PageBlock_Rest-BOM.txt     |页 Blob         |REST        |
    |utsac1_BlockBlock_Rest-BOM.txt    |块 Blob         |REST         |

将 Data Box 寄回到 Azure 数据中心后，使用此列表来验证已上传到 Azure 存储帐户的文件。 示例清单文件如下所示。

> [!NOTE]
> 在 Data Box Heavy 上，提供了对应于两个设备节点的两组文件列表（BOM 文件）。

```xml
<file size="52689" crc64="0x95a62e3f2095181e">\databox\media\data-box-deploy-copy-data\prepare-to-ship2.png</file>
<file size="22117" crc64="0x9b160c2c43ab6869">\databox\media\data-box-deploy-copy-data\connect-shares-file-explorer2.png</file>
<file size="57159" crc64="0x1caa82004e0053a4">\databox\media\data-box-deploy-copy-data\verify-used-space-dashboard.png</file>
<file size="24777" crc64="0x3e0db0cd1ad438e0">\databox\media\data-box-deploy-copy-data\prepare-to-ship5.png</file>
<file size="162006" crc64="0x9ceacb612ecb59d6">\databox\media\data-box-cable-options\cabling-dhcp-data-only.png</file>
<file size="155066" crc64="0x051a08d36980f5bc">\databox\media\data-box-cable-options\cabling-2-port-setup.png</file>
<file size="150399" crc64="0x66c5894ff328c0b1">\databox\media\data-box-cable-options\cabling-with-switch-static-ip.png</file>
<file size="158082" crc64="0xbd4b4c5103a783ea">\databox\media\data-box-cable-options\cabling-mgmt-only.png</file>
<file size="148456" crc64="0xa461ad24c8e4344a">\databox\media\data-box-cable-options\cabling-with-static-ip.png</file>
<file size="40417" crc64="0x637f59dd10d032b3">\databox\media\data-box-portal-admin\delete-order1.png</file>
<file size="33704" crc64="0x388546569ea9a29f">\databox\media\data-box-portal-admin\clone-order1.png</file>
<file size="5757" crc64="0x9979df75ee9be91e">\databox\media\data-box-safety\japan.png</file>
<file size="998" crc64="0xc10c5a1863c5f88f">\databox\media\data-box-safety\overload_tip_hazard_icon.png</file>
<file size="5870" crc64="0x4aec2377bb16136d">\databox\media\data-box-safety\south-korea.png</file>
<file size="16572" crc64="0x05b13500a1385a87">\databox\media\data-box-safety\taiwan.png</file>
<file size="999" crc64="0x3f3f1c5c596a4920">\databox\media\data-box-safety\warning_icon.png</file>
<file size="1054" crc64="0x24911140d7487311">\databox\media\data-box-safety\read_safety_and_health_information_icon.png</file>
<file size="1258" crc64="0xc00a2d5480f4fcec">\databox\media\data-box-safety\heavy_weight_hazard_icon.png</file>
<file size="1672" crc64="0x4ae5cfa67c0e895a">\databox\media\data-box-safety\no_user_serviceable_parts_icon.png</file>
<file size="3577" crc64="0x99e3d9df341b62eb">\databox\media\data-box-safety\battery_disposal_icon.png</file>
<file size="993" crc64="0x5a1a78a399840a17">\databox\media\data-box-safety\tip_hazard_icon.png</file>
<file size="1028" crc64="0xffe332400278f013">\databox\media\data-box-safety\electrical_shock_hazard_icon.png</file>
<file size="58699" crc64="0x2c411d5202c78a95">\databox\media\data-box-deploy-ordered\data-box-ordered.png</file>
<file size="46816" crc64="0x31e48aa9ca76bd05">\databox\media\data-box-deploy-ordered\search-azure-data-box1.png</file>
<file size="24160" crc64="0x978fc0c6e0c4c16d">\databox\media\data-box-deploy-ordered\select-data-box-option1.png</file>
<file size="115954" crc64="0x0b42449312086227">\databox\media\data-box-disk-deploy-copy-data\data-box-disk-validation-tool-output.png</file>
<file size="6093" crc64="0xadb61d0d7c6d4deb">\databox\data-box-cable-options.md</file>
<file size="6499" crc64="0x080add29add367d9">\databox\data-box-deploy-copy-data-via-nfs.md</file>
<file size="11089" crc64="0xc3ce6b13a4fe3001">\databox\data-box-deploy-copy-data-via-rest.md</file>
<file size="9126" crc64="0x820856b5a54321ad">\databox\data-box-overview.md</file>
<file size="10963" crc64="0x5e9a14f9f4784fd8">\databox\data-box-safety.md</file>
<file size="5941" crc64="0x8631d62fbc038760">\databox\data-box-security.md</file>
<file size="12536" crc64="0x8c8ff93e73d665ec">\databox\data-box-system-requirements-rest.md</file>
<file size="3220" crc64="0x7257a263c434839a">\databox\data-box-system-requirements.md</file>
<file size="2823" crc64="0x63db1ada6fcdc672">\databox\index.yml</file>
<file size="4364" crc64="0x62b5710f58f00b8b">\databox\data-box-local-web-ui-admin.md</file>
<file size="3603" crc64="0x7e34c25d5606693f">\databox\TOC.yml</file>
```

此文件包含已在 Data Box 或 Data Box Heavy 上复制的所有文件的列表。 在此文件中，*crc64* 值与针对相应文件生成的校验和相关。

## <a name="view-available-capacity-of-the-device"></a>查看设备的可用容量

可使用设备仪表板查看设备的可用和已用容量。

1. 在本地 web UI 中，转到“查看仪表板”。
2. “连接和复制”下显示设备上的可用和已用空间。

    ![查看可用容量](media/data-box-local-web-ui-admin/verify-used-space-dashboard.png)

## <a name="skip-checksum-validation"></a>跳过校验和验证

准备寄送时，默认会生成数据的校验和。 在某些罕见的情况下，根据所用的数据类型（小型文件），性能可能较慢。 在这种情况，则可以跳过校验和。

准备发货期间的校验和计算仅适用于进口订单，不适用于出口订单。

我们强烈建议您不要禁用校验和，除非性能受到严重影响。

1. 在设备的本地 web UI 的右上角，单击 " **设置**"。

    ![禁用校验和](media/data-box-local-web-ui-admin/disable-checksum.png)

2. 禁用校验和验证
3. 选择“应用”。

> [!NOTE]
> "跳过校验和计算" 选项仅在 Azure Data Box 处于解锁状态时才可用。 锁定设备时，不会显示此选项。

## <a name="enable-smb-signing"></a>启用 SMB 签名

服务器消息块 (SMB) 签名是一项功能，使用 SMB 的通信可以在数据包级别进行数字签名。 此签名可防止在传输过程中修改 SMB 数据包的攻击。

有关 SMB 签名的详细信息，请参阅 [服务器消息块签名概述](https://support.microsoft.com/help/887429/overview-of-server-message-block-signing)。

在 Azure 设备中启用 SMB 签名：

1. 在设备的本地 web UI 的右上角，选择 " **设置**"。

    ![打开“设置”](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. **启用** SMB 签名。

    ![启用 SMB 签名](media/data-box-local-web-ui-admin/data-box-smb-signing-1.png)

3. 选择“应用”。
4. 在本地 Web UI 中，转到“关闭或重启”。
5. 选择“重启”。 

## <a name="enable-backup-operator-privileges"></a>启用备份操作员权限

默认情况下，web UI 用户具有 SMB 共享的备份操作员特权。 如果你不想这样做，请使用 **Enable Operator 权限** 来禁用或启用权限。

有关信息，请参阅 [Active Directory 安全组](/windows/security/identity-protection/access-control/active-directory-security-groups#backup-operators)中的备份操作员。

在 Azure 设备中启用备份操作员权限：

1. 在设备的本地 web UI 的右上角，选择 " **设置**"。

   ![打开 Data Box 设置-1](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. **启用** 备份操作员特权。

   ![启用备份操作员权限](media/data-box-local-web-ui-admin/data-box-backup-operator-privileges-1.png)

3. **选择 "应用**"。
4. 在本地 Web UI 中，转到“关闭或重启”。
5. 选择“重启”。 

## <a name="enable-acls-for-azure-files"></a>为 Azure 文件启用 Acl

当用户通过 SMB 将数据上载到 Data Box 时，文件中的元数据默认情况下传输。 该元数据包括访问控制列表 (Acl) 、文件属性和时间戳。 如果不想这样做，请使用 **Azure 文件的 acl** 来禁用或启用此功能。

<!--For more information about metadata that is transferred, see [Preserving the ACLs and metadata with Azure Data Box](./data-box-local-web-ui-admin.md#enable-backup-operator-privileges) - IN DEVELOPMENT-->

> [!Note]
> 若要将元数据与文件一起传输，则必须是备份操作员。 使用此功能时，请确保 web UI 的本地用户是备份操作员。 请参阅 [启用备份操作员特权](#enable-backup-operator-privileges)。

若要为 Azure 文件启用 Acl 传输：

1. 在设备的本地 web UI 的右上角，选择 " **设置**"。

    ![打开 Data Box 设置-2](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. **启用** Azure 文件的 Acl。

     ![为 Azure 文件启用 Acl](media/data-box-local-web-ui-admin/data-box-acls-for-azure-files-1.png)
  
3. 选择“应用”。
4. 在本地 Web UI 中，转到“关闭或重启”。
5. 选择“重启”。 

## <a name="enable-tls-11"></a>启用 TLS 1。1

默认情况下，Azure Data Box 使用传输层安全性 (TLS) 1.2 进行加密，因为它比 TSL 1.1 更安全。 但是，如果你或你的客户端使用浏览器访问不支持 TLS 1.2 的数据，则可以启用 TLS 1.1。

有关与 TLS 相关的详细信息，请参阅 [Azure Data Box Gateway 安全性](../databox-gateway/data-box-gateway-security.md)。

在 Azure 设备中启用 TLS 1.1：

1. 在设备的本地 web UI 的右上角，选择 " **设置**"。

    ![打开 Data Box 设置-3](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. **启用** TLS 1.1。

    ![启用 TLS 1。1](media/data-box-local-web-ui-admin/data-box-tls-1-1.png)

3. 选择“应用”。
4. 在本地 Web UI 中，转到“关闭或重启”。
5. 选择“重启”。 

## <a name="next-steps"></a>后续步骤

- 了解如何[通过 Azure 门户管理 Data Box 和 Azure Data Box Heavy](data-box-portal-admin.md)。
