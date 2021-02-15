---
title: 专用终结点
description: 了解创建 Azure 备份的专用终结点的过程以及使用专用终结点帮助维护资源安全的方案。
ms.topic: conceptual
ms.date: 05/07/2020
ms.openlocfilehash: 0d9d77c139896f9067f73943dbb213fc655f00f6
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054866"
---
# <a name="private-endpoints-for-azure-backup"></a>Azure 备份的专用终结点

通过 Azure 备份，你可以使用[专用终结点](../private-link/private-endpoint-overview.md)从恢复服务保管库安全地备份和还原数据。 专用终结点使用 VNet 中的一个或多个专用 IP 地址将服务有效接入 VNet 中。

本文将帮助你了解创建 Azure 备份的专用终结点的过程，以及使用专用终结点帮助维护资源安全的方案。

## <a name="before-you-start"></a>开始之前

- 仅可为新的恢复服务保管库创建专用终结点（没有任何项已注册到保管库）。 因此必须先创建专用终结点，然后才能尝试保护保管库中的任何项。
- 一个虚拟网络可以包含用于多个恢复服务保管库的专用终结点。 此外，一个恢复服务保管库可以在多个虚拟网络中包含要使用的专用终结点。 但是，最多只能为保管库创建 12 个专用终结点。
- 为保管库创建专用终结点后，保管库将被锁定。 除包含该保管库的专用终结点的网络之外，无法从其他网络访问它（用于备份和还原）。 如果删除该保管库的所有专用终结点，则可以从所有网络访问该保管库。
- 用于备份的专用终结点连接在子网中总共使用 11 个专用 IP，其中包括 Azure 备份用于存储的 IP。 对于某些 Azure 区域，此数字可能更高（最多 25 个）。 因此，我们建议你在尝试创建用于备份的专用终结点时，拥有足够的可用专用 IP。
- 尽管恢复服务保管库可用于 Azure 备份和 Azure Site Recovery 这两种服务，但本文仅介绍将专用终结点用于 Azure 备份的情况。
- Azure Active Directory 当前不支持专用终结点。 因此在 Azure VM 中执行数据库备份和使用 MARS 代理进行备份时，需要允许 Azure Active Directory 在区域中操作所需的 IP 和 FQDN 从受保护的网络进行出站访问。 如果适用，还可以使用 NSG 标记和 Azure 防火墙标记来允许访问 Azure AD。
- 具有网络策略的虚拟网络不支持专用终结点。 在继续之前，需要禁用网络策略。
- 如果在 2020 年 5 月 1 日之前注册了恢复服务资源提供程序，则需在订阅中重新注册它。 若要重新注册提供程序，请转到 Azure 门户中的订阅，导航到左侧导航栏上的“资源提供程序”，然后选择“Microsoft.RecoveryServices”，并选择“重新注册”  。
- 如果保管库启用了专用终结点，则不支持 SQL 和 SAP HANA 数据库备份的[跨区域还原](backup-create-rs-vault.md#set-cross-region-restore)。

## <a name="recommended-and-supported-scenarios"></a>推荐和支持的方案

虽然为保管库启用了专用终结点，但它们仅用于在 Azure VM 中备份和还原 SQL 和 SAP HANA 工作负载以及进行 MARS 代理备份。 还可以使用保管库来备份其他工作负载（尽管它们不需要专用终结点）。 除了备份 SQL 和 SAP HANA 工作负载以及使用 MARS 代理进行备份，专用终结点还可用于在 Azure VM 备份时执行文件恢复。 有关详细信息，请参阅下表：

| 在 Azure VM 中备份工作负荷 (SQL，SAP HANA) ，使用 MARS 代理备份 | 建议使用私有终结点，以允许备份和还原，而无需为虚拟网络中的 Azure 备份或 Azure 存储允许列表任何 Ip/Fqdn。 在这种情况下，请确保承载 SQL 数据库的 Vm 可以访问 Azure AD 的 Ip 或 Fqdn。 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Azure VM 备份**                                         | VM 备份不要求你允许访问任何 IP 或 FQDN。 因此，它不需要专用终结点来备份和还原磁盘。  <br><br>   但是，从包含专用终结点的保管库执行文件恢复将限制为包含该保管库的终结点的虚拟网络。 <br><br>    使用 ACL 非托管磁盘时，请确保包含磁盘的存储帐户允许访问受信任的 Microsoft 服务（如果为 ACL）。 |
| **Azure 文件备份**                                      | Azure 文件备份存储在本地存储帐户中。 因此，它不需要专用终结点来进行备份和还原。 |

## <a name="creating-and-using-private-endpoints-for-backup"></a>创建和使用专用终结点以进行备份

本部分介绍在虚拟网络中创建和使用 Azure 备份的专用终结点时所涉及的步骤。

>[!IMPORTANT]
> 强烈建议按照本文档中所述的顺序执行步骤。 如果未按照顺序操作，可能导致保管库呈现为不兼容，无法使用专用终结点，并要求你使用新保管库重启此进程。

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

请参阅[此部分](#create-a-recovery-services-vault-using-the-azure-resource-manager-client)，了解如何使用 Azure 资源管理器客户端创建保管库。 这会创建一个已启用托管标识的保管库。 有关恢复服务保管库的详细信息，请参阅[在此](./backup-azure-recovery-services-vault-overview.md)。

## <a name="enable-managed-identity-for-your-vault"></a>为保管库启用托管标识

托管标识允许保管库创建和使用专用终结点。 本部分介绍如何为保管库启用托管标识。

1. 转到“恢复服务保管库”->“标识”。

    ![将标识状态更改为“启用”](./media/private-endpoints/identity-status-on.png)

1. 将“状态”更改为“开”，然后选择“保存”  。

1. 此时将生成一个“对象 ID”，它是保管库的托管标识。

    >[!NOTE]
    >启用后，不得禁用托管标识（即使是暂时禁用）。 禁用托管标识可能导致出现不一致的行为。

## <a name="grant-permissions-to-the-vault-to-create-required-private-endpoints"></a>授予保管库创建所需专用终结点的权限

若要为 Azure 备份创建所需的专用终结点，保管库（保管库的托管标识）必须具有以下资源组的权限：

- 包含目标 VNet 的资源组
- 要在其中创建专用终结点的资源组
- 包含专用 DNS 区域的资源组，如[此处](#creating-private-endpoints-for-backup)详细讨论的那样

建议向保管库（托管标识）授予这三个资源组的“参与者”角色。 以下步骤介绍了如何针对特定的资源组执行此操作（需要为三个资源组中的每个资源组执行此操作）：

1. 转到“资源组”，并导航到左侧栏中的“访问控制(IAM)”。
1. 在“访问控制”中，转到“添加角色分配” 。

    ![添加角色分配](./media/private-endpoints/add-role-assignment.png)

1. 在“添加角色分配”中，选择“参与者”作为“角色”，然后使用保管库的“名称”作为“主体”    。 选择保管库，并在完成后选择“保存”。

    ![选择角色和主体](./media/private-endpoints/choose-role-and-principal.png)

若要以更精细的级别管理权限，请参阅[手动创建角色和权限](#create-roles-and-permissions-manually)。

## <a name="creating-and-approving-private-endpoints-for-azure-backup"></a>创建和批准 Azure 备份的专用终结点

### <a name="creating-private-endpoints-for-backup"></a>创建专用终结点以进行备份

本部分介绍为保管库创建专用终结点的过程。

1. 在搜索栏中，搜索并选择“专用链接”。 这会转到“专用链接中心”。

    ![搜索专用链接](./media/private-endpoints/search-for-private-link.png)

1. 选择左侧导航栏中的“专用终结点”。 转到“专用终结点”窗格后，选择“+ 添加”以开始为保管库创建专用终结点 。

    ![在专用链接中心添加专用终结点](./media/private-endpoints/add-private-endpoint.png)

1. 在“创建专用终结点”过程中，需要指定用于创建专用终结点连接的详细信息。

    1. **基本信息**：填写专用终结点的基本详细信息。 该区域应与保管库和资源相同。

        ![填写基本详细信息](./media/private-endpoints/basic-details.png)

    1. **资源**：此选项卡要求你提及要为其创建连接的 PaaS 资源。 从所需订阅的资源类型中选择“Microsoft.RecoveryServices/vaults”。 完成后，选择恢复服务保管库的名称作为“资源”，选择“AzureBackup”作为“目标子资源”  。

        ![填写“资源”选项卡](./media/private-endpoints/resource-tab.png)

    1. **配置**：从配置中，指定要在其中创建专用终结点的虚拟网络和子网。 这将是 VM 所在的 Vnet。 可以选择将专用终结点与专用 DNS 区域集成。 或者，也可以使用自定义 DNS 服务器或创建专用 DNS 区域。

        ![填写“配置”选项卡](./media/private-endpoints/configuration-tab.png)

        如果要使用自定义 DNS 服务器，而不与 Azure 专用 DNS 区域集成，请参阅[此部分](#dns-changes-for-custom-dns-servers)。  

    1. （可选）可以为专用终结点添加标记。

    1. 输入详细信息后，继续“查看 + 创建”。 完成验证后，选择“创建”以创建专用终结点。

## <a name="approving-private-endpoints"></a>批准专用终结点

如果创建专用终结点的用户也是恢复服务保管库的所有者，则将自动批准上面创建的专用终结点。 否则，保管库的所有者必须先批准专用终结点，然后才能使用该终结点。 本部分介绍如何通过 Azure 门户手动批准专用终结点。

请参阅[使用 Azure 资源管理器客户端手动批准专用终结点](#manual-approval-of-private-endpoints-using-the-azure-resource-manager-client)，以使用 Azure 资源管理器客户端批准专用终结点。

1. 在恢复服务保管库中，导航到左侧栏的“专用终结点连接”。
1. 选择要批准的专用终结点连接。
1. 在顶部栏上选择“批准”。 如果要拒绝或删除终结点连接，还可以选择“拒绝”或“删除” 。

    ![批准专用终结点](./media/private-endpoints/approve-private-endpoints.png)

## <a name="using-private-endpoints-for-backup"></a>使用专用终结点进行备份

当 VNet 中为保管库创建的专用终结点获得批准后，即可开始使用它们来执行备份和还原。

>[!IMPORTANT]
>在继续之前，请确保已成功完成文档中的上述所有步骤。 概括起来，必须完成以下清单中的步骤：
>
>1. 已创建（新的）恢复服务保管库
>1. 已启用保管库，以使用系统分配的托管标识
>1. 已向保管库的托管标识分配相关权限
>1. 已为保管库创建专用终结点
>1. 已批准专用终结点（如果未自动批准）

### <a name="backup-and-restore-of-workloads-in-azure-vm-sql-sap-hana"></a>在 Azure VM 中备份和还原工作负载（SQL、SAP HANA）

创建并批准专用终结点后，客户端不需要进行其他更改即可使用该专用终结点。 从受保护的网络到保管库的所有通信和数据传输都将通过专用终结点执行。
但是，如果在某个服务器 (SQL/SAP HANA) 注册到保管库后删除了该保管库的专用终结点，则需要向该保管库重新注册容器。 不需要停止对它们的保护。

### <a name="backup-and-restore-through-mars-agent"></a>通过 MARS 代理进行备份和还原

使用 MARS 代理备份本地资源时，请确保已将本地网络（包含要备份的资源）与包含保管库的专用终结点的 Azure VNet 对等互连，以便可以使用它。 然后可以继续安装 MARS 代理并配置备份，如此处所述。 但必须确保仅通过对等网络进行所有备份通信。

不过，如果在某个 MARS 代理注册到保管库后删除了该保管库的专用终结点，则需要向该保管库重新注册容器。 不需要停止对它们的保护。

## <a name="additional-topics"></a>其他主题

### <a name="create-a-recovery-services-vault-using-the-azure-resource-manager-client"></a>使用 Azure 资源管理器客户端创建恢复服务保管库

可以使用 Azure 资源管理器客户端创建恢复服务保管库，并启用其托管标识（必须启用托管标识，稍后会进行介绍）。 执行此操作的示例如下所示：

```rest
armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>?api-version=2017-07-01-preview @C:\<filepath>\MSIVault.json
```

上面的 JSON 文件应包含以下内容：

请求 JSON：

```json
{
  "location": "eastus2",
  "name": "<vaultname>",
  "etag": "W/\"datetime'2019-05-24T12%3A54%3A42.1757237Z'\"",
  "tags": {
    "PutKey": "PutValue"
  },
  "properties": {},
  "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
  "type": "Microsoft.RecoveryServices/Vaults",
  "sku": {
    "name": "RS0",
    "tier": "Standard"
  },
  "identity": {
    "type": "systemassigned"
  }
}
```

响应 JSON：

```json
{
   "location": "eastus2",
   "name": "<vaultname>",
   "etag": "W/\"datetime'2020-02-25T05%3A26%3A58.5181122Z'\"",
   "tags": {
     "PutKey": "PutValue"
   },
   "identity": {
     "tenantId": "<tenantid>",
     "principalId": "<principalid>",
     "type": "SystemAssigned"
   },
   "properties": {
     "provisioningState": "Succeeded",
     "privateEndpointStateForBackup": "None",
     "privateEndpointStateForSiteRecovery": "None"
   },
   "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
   "type": "Microsoft.RecoveryServices/Vaults",
   "sku": {
     "name": "RS0",
     "tier": "Standard"
   }
 }
```

>[!NOTE]
>已通过 Azure 资源管理器客户端创建此示例中的保管库，该保管库具有系统分配的托管标识。

### <a name="managing-permissions-on-resource-groups"></a>管理资源组的权限

保管库的托管标识需要在将创建专用终结点的资源组和虚拟网络中具有以下权限：

- `Microsoft.Network/privateEndpoints/*` 必须具有此权限才能在资源组中的专用终结点上执行 CRUD。 应对资源组分配该权限。
- `Microsoft.Network/virtualNetworks/subnets/join/action` 必须具有此权限才能将专用 IP 附加到专用终结点的虚拟网络。
- `Microsoft.Network/networkInterfaces/read` 资源组需要此权限，才能获取为专用终结点创建的网络接口。
- 专用 DNS 区域参与者角色 此角色已存在并且可用于提供 `Microsoft.Network/privateDnsZones/A/*` 和 `Microsoft.Network/privateDnsZones/virtualNetworkLinks/read` 权限。

可以使用以下方法之一创建具有所需权限的角色：

#### <a name="create-roles-and-permissions-manually"></a>手动创建角色和权限

创建以下 JSON 文件，并使用本部分末尾的 PowerShell 命令来创建角色：

//PrivateEndpointContributorRoleDef.json

```json
{
  "Name": "PrivateEndpointContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows management of Private Endpoint",
  "Actions": [
    "Microsoft.Network/privateEndpoints/*",
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

//NetworkInterfaceReaderRoleDef.json

```json
{
  "Name": "NetworkInterfaceReader",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows read on networkInterfaces",
  "Actions": [
    "Microsoft.Network/networkInterfaces/read"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

//PrivateEndpointSubnetContributorRoleDef.json

```json
{
  "Name": "PrivateEndpointSubnetContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows adding of Private Endpoint connection to Virtual Networks",
  "Actions": [
    "Microsoft.Network/virtualNetworks/subnets/join/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

```azurepowershell
 New-AzRoleDefinition -InputFile "PrivateEndpointContributorRoleDef.json"
 New-AzRoleDefinition -InputFile "NetworkInterfaceReaderRoleDef.json"
 New-AzRoleDefinition -InputFile "PrivateEndpointSubnetContributorRoleDef.json"
```

#### <a name="use-a-script"></a>使用脚本

1. 启动 Azure 门户中的 **Cloud Shell** ，然后在 PowerShell 窗口中选择 " **上传文件** "。

    ![选择 "在 PowerShell 中上传文件" 窗口](./media/private-endpoints/upload-file-in-powershell.png)

1. 上传以下脚本： [VaultMsiPrereqScript](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/VaultMsiPrereqScript.ps1)

1. 中转到主文件夹 (例如： `cd /home/user`) 

1. 运行以下脚本：

    ```azurepowershell
    ./VaultMsiPrereqScript.ps1 -subscription <subscription-Id> -vaultPEResourceGroup <vaultPERG> -vaultPESubnetResourceGroup <subnetRG> -vaultMsiName <msiName>
    ```

    参数如下：

    - **subscription**：**SubscriptionId，具有要在其中为保管库创建专用终结点的资源组以及将在其中附加保管库的专用终结点的子网

    - **vaultPEResourceGroup**：将在其中为保管库创建专用终结点的资源组

    - **vaultPESubnetResourceGroup**：专用终结点将联接到的子网的资源组

    - **vaultMsiName**：保管库的 MSI 名称，与 VaultName 相同

1. 完成身份验证，该脚本将获取上面提供的给定订阅的上下文。 如果租户中缺少适当的角色，则该脚本将创建角色并将角色分配给保管库的 MSI。

### <a name="creating-private-endpoints-using-azure-powershell"></a>使用 Azure PowerShell 创建专用终结点

#### <a name="auto-approved-private-endpoints"></a>自动批准专用终结点

```azurepowershell
$vault = Get-AzRecoveryServicesVault `
        -ResourceGroupName $vaultResourceGroupName `
        -Name $vaultName
  
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
        -Name $privateEndpointConnectionName `
        -PrivateLinkServiceId $vault.ID `
        -GroupId "AzureBackup"  
  
$privateEndpoint = New-AzPrivateEndpoint `
        -ResourceGroupName $vmResourceGroupName `
        -Name $privateEndpointName `
        -Location $location `
        -Subnet $subnet `
        -PrivateLinkServiceConnection $privateEndpointConnection `
        -Force
```

### <a name="manual-approval-of-private-endpoints-using-the-azure-resource-manager-client"></a>使用 Azure 资源管理器客户端手动批准专用终结点

1. 使用 GetVault 获取专用终结点的专用终结点连接 ID。

    ```rest
    armclient GET /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/vaults/<vaultname>?api-version=2017-07-01-preview
    ```

    这将返回专用终结点连接 ID。 可以使用连接 ID 的第一部分检索连接的名称，如下所示：

    `privateendpointconnectionid = {peName}.{vaultId}.backup.{guid}`

1. 从响应中获取专用终结点连接 ID（以及专用终结点名称，如有需要），将其替换为以下 JSON 和 Azure 资源管理器 URI，并尝试将状态更改为“已批准/已拒绝/已断开连接”，如以下示例所示 ：

    ```rest
    armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>?api-version=2020-02-02-preview @C:\<filepath>\BackupAdminApproval.json
    ```

    JSON：

    ```json
    {
    "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>",
    "properties": {
        "privateEndpoint": {
        "id": "/subscriptions/<subscriptionid>/resourceGroups/<pergname>/providers/Microsoft.Network/privateEndpoints/pename"
        },
        "privateLinkServiceConnectionState": {
        "status": "Disconnected",  //choose state from Approved/Rejected/Disconnected
        "description": "Disconnected by <userid>"
        }
    }
    }
    ```

### <a name="dns-changes-for-custom-dns-servers"></a>自定义 DNS 服务器的 DNS 更改

#### <a name="create-dns-zones-for-custom-dns-servers"></a>为自定义 DNS 服务器创建 DNS 区域

需要创建三个专用 DNS 区域，并将其链接到虚拟网络。 请记住，与 Blob 和队列不同的是，备份服务公共 Url 不在 Azure 公共 DNS 中注册，以重定向到专用链接 DNS 区域。 

| **区域**                                                     | **服务** |
| ------------------------------------------------------------ | ----------- |
| `privatelink.<geo>.backup.windowsazure.com`      | Backup      |
| `privatelink.blob.core.windows.net`                            | Blob        |
| `privatelink.queue.core.windows.net`                           | 队列       |

>[!NOTE]
>在上述文本中，geo 指地区代码。 例如， *wcus* 和 *NE* 分别用于美国中部和北欧。

请参阅[此列表](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx)，了解地区代码。 请参阅以下链接，了解国家地区的 URL 命名约定：

- [中国](/azure/china/resources-developer-guide#check-endpoints-in-azure)
- [德国](../germany/germany-developer-guide.md#endpoint-mapping)
- [US Gov](../azure-government/documentation-government-developer-guide.md)

#### <a name="adding-dns-records-for-custom-dns-servers"></a>为自定义 DNS 服务器添加 DNS 记录

这要求你将专用终结点中的每个 FQDN 条目添加到专用 DNS 区域。

应注意，我们将使用为备份、Blob 和队列服务创建的专用终结点。

- 保管库的专用终结点使用创建专用终结点时指定的名称。
- blob 和队列服务的专用终结点以保管库的名称作为前缀。

例如，下图显示了为专用终结点连接创建的三个专用终结点，带有名称 pee2epe：

![专用终结点连接的三个专用终结点](./media/private-endpoints/three-private-endpoints.png)

备份服务的 DNS 区域 (`privatelink.<geo>.backup.windowsazure.com`)：

1. 在“专用链接中心”导航到用于备份的专用终结点。 概述页面列出了专用终结点的 FQDN 和专用 IP。

1. 为每个 FQDN 和专用 IP 添加一个条目作为 A 类型记录。

    ![为每个 FQDN 和专用 IP 添加条目](./media/private-endpoints/add-entry-for-each-fqdn-and-ip.png)

Blob 服务的 DNS 区域 (`privatelink.blob.core.windows.net`)：

1. 在“专用链接中心”导航到用于 Blob 的专用终结点。 概述页面列出了专用终结点的 FQDN 和专用 IP。

1. 为 FQDN 和专用 IP 添加一个条目作为 A 类型记录。

    ![针对 Blob 服务，为 FQDN 和专用 IP 添加一个条目作为 A 类型记录](./media/private-endpoints/add-type-a-record-for-blob.png)

队列服务的 DNS 区域 (`privatelink.queue.core.windows.net`)：

1. 在“专用链接中心”导航到用于队列的专用终结点。 概述页面列出了专用终结点的 FQDN 和专用 IP。

1. 为 FQDN 和专用 IP 添加一个条目作为 A 类型记录。

    ![针对队列服务，为 FQDN 和专用 IP 添加一个条目作为 A 类型记录](./media/private-endpoints/add-type-a-record-for-queue.png)

## <a name="frequently-asked-questions"></a>常见问题解答

问： 是否可以为现有的备份保管库创建专用终结点？<br>
A. 不可以，只能为新备份保管库创建专用终结点。 因此，保管库不能包含受保护的任何项。 事实上，在创建专用终结点之前，不会尝试保护保管库中的任何项。

问： 我尝试过保护保管库中的某一项，但失败了，并且保管库仍未包含任何受保护的项。 是否可以为此保管库创建专用终结点？<br>
A. 不可以，保管库在过去不能尝试保护任何项。

问： 我有一个使用专用终结点进行备份和还原的保管库。 在我具有保管库的受保护备份项的情况下，是否可以在之后添加或删除此保管库的专用终结点？<br>
A. 是的。 如果已为保管库创建专用终结点并保护了该保管库的备份项，则可以根据需要在之后添加或删除专用终结点。

问： Azure 备份的专用终结点是否也可用于 Azure Site Recovery？<br>
A. 否，用于备份的专用终结点仅可用于 Azure 备份。 如果服务支持，则需要为 Azure Site Recovery 创建新的专用终结点。

问： 我漏掉了本文中的一个步骤，并继续对数据源进行了保护。 我是否仍可以使用专用终结点？<br>
A. 未遵循本文的步骤操作并继续保护项可能导致无法使用专用终结点。 因此，建议在继续保护项之前查看此清单。

问： 我是否可以使用自己的 DNS 服务器，而不使用 Azure 专用 DNS 区域或集成的专用 DNS 区域？<br>
A. 是的，你可以使用自己的 DNS 服务器。 但是，请确保按照本部分中的建议添加所有必需的 DNS 记录。

问： 按照本文中的过程操作后，是否需要在服务器上执行任何其他步骤？<br>
A. 按照本文中详细说明的过程操作后，无需执行其他操作即可使用专用终结点进行备份和还原。

## <a name="next-steps"></a>后续步骤

- 了解 [Azure 备份中的所有安全功能](security-overview.md)
