---
title: 为 Azure Batch 帐户配置 Azure Key Vault 和托管标识的客户托管密钥
description: 了解如何使用客户管理的密钥加密 Batch 数据。
author: pkshultz
ms.topic: how-to
ms.date: 02/11/2021
ms.author: peshultz
ms.openlocfilehash: d3f10436b95aaeb5eb35a873c2a3862c1492bd47
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100385058"
---
# <a name="configure-customer-managed-keys-for-your-azure-batch-account-with-azure-key-vault-and-managed-identity"></a>为 Azure Batch 帐户配置 Azure Key Vault 和托管标识的客户托管密钥

默认情况下 Azure Batch 使用平台管理的密钥来加密存储在 Azure Batch 服务中的所有客户数据，如证书、作业/任务元数据。 （可选）可以使用自己的密钥（即客户托管的密钥）来加密存储在 Azure Batch 中的数据。

提供的密钥必须在 [Azure Key Vault](../key-vault/general/basic-concepts.md)中生成，并且必须通过 [Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)进行访问。

有两种类型的托管标识： [*系统分配* 和 *用户分配*](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)。

您可以使用系统分配的托管标识创建 Batch 帐户，也可以创建单独的用户分配的托管标识，该标识将有权访问客户管理的密钥。 查看 [比较表](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) 以了解不同之处，并考虑哪个选项最适合您的解决方案。 例如，如果想要使用同一托管标识来访问多个 Azure 资源，则需要使用用户分配的托管标识。 否则，与 Batch 帐户关联的系统分配的托管标识可能已足够。 使用用户分配的托管标识，还可以选择在创建 Batch 帐户时强制执行客户托管的密钥，如 [以下示例中](#create-a-batch-account-with-user-assigned-managed-identity-and-customer-managed-keys)所示。

## <a name="create-a-batch-account-with-system-assigned-managed-identity"></a>使用系统分配的托管标识创建 Batch 帐户

如果你不需要单独的用户分配的托管标识，你可以在创建批处理帐户时启用系统分配的托管标识。

### <a name="azure-portal"></a>Azure 门户

在 [Azure 门户](https://portal.azure.com/)中创建 Batch 帐户时，请在 "**高级**" 选项卡下的 "标识类型" 中选择 "**系统分配**"。

![使用系统分配的标识类型的新 Batch 帐户的屏幕截图。](./media/batch-customer-managed-key/create-batch-account.png)

创建帐户后，可以在 "**属性**" 部分下的 "**标识主体 Id** " 字段中找到唯一 GUID。 将显示 **标识类型** `System assigned` 。

![显示 "标识主体 Id" 字段中的唯一 GUID 的屏幕截图。](./media/batch-customer-managed-key/linked-batch-principal.png)

你将需要此值以便向此批处理帐户授予对 Key Vault 的访问权限。

### <a name="azure-cli"></a>Azure CLI

创建新的 Batch 帐户时，请 `SystemAssigned` 为参数指定 `--identity` 。

```azurecli
resourceGroupName='myResourceGroup'
accountName='mybatchaccount'

az batch account create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' \
    --identity 'SystemAssigned'
```

创建帐户后，你可以验证是否已在此帐户上启用系统分配的托管标识。 请务必注意 `PrincipalId` ，因为此值将需要授予此批处理帐户对 Key Vault 的访问权限。

```azurecli
az batch account show \
    -n $accountName \
    -g $resourceGroupName \
    --query identity
```

> [!NOTE]
> 在 Batch 帐户中创建的系统分配的托管标识仅用于从 Key Vault 检索客户管理的密钥。 此标识在 Batch 池上不可用。 若要在池中使用用户分配的托管标识，请参阅 [在 Batch 池中配置托管标识](managed-identity-pools.md)。

## <a name="create-a-user-assigned-managed-identity"></a>创建用户分配的托管标识

如果需要，可以 [创建用户分配的托管标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) ，该标识可用于访问客户管理的密钥。

你将需要此标识的 **客户端 ID** 值，以便它能够访问 Key Vault。

## <a name="configure-your-azure-key-vault-instance"></a>配置 Azure Key Vault 实例

必须在与 Batch 帐户相同的租户中创建要在其中生成密钥的 Azure Key Vault。 它不需要位于同一资源组中，甚至不需要位于同一订阅中。

### <a name="create-an-azure-key-vault"></a>创建 Azure Key Vault

使用 Azure Batch 的客户托管密钥 [创建 Azure Key Vault 实例](../key-vault/general/quick-create-portal.md) 时，请确保已启用 **软删除** 和 **清除保护** 。

![Key Vault 创建屏幕的屏幕截图。](./media/batch-customer-managed-key/create-key-vault.png)

### <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>将访问策略添加到 Azure Key Vault 实例

在 Key Vault Azure 门户的 "设置" 下的 " **访问策略** " 中，在 " **设置**" 下，添加使用托管标识的 Batch 帐户访问权限。 在 " **密钥权限**" 下，选择 " **获取**"、" **换行** 并 **解包** 密钥"。

![显示 "添加访问策略" 屏幕的屏幕截图。](./media/batch-customer-managed-key/key-permissions.png)

在 "**主体**" 下的 "**选择**" 字段中，填写以下项之一：

- 对于系统分配的托管标识：输入 `principalId` 先前检索的或批处理帐户的名称。
- 对于用户分配的托管标识：输入先前检索的 **客户端 ID** 或用户分配的托管标识的名称。

![主体屏幕的屏幕截图。](./media/batch-customer-managed-key/principal-id.png)

### <a name="generate-a-key-in-azure-key-vault"></a>在 Azure Key Vault 中生成密钥

在 Azure 门户中，在 " **密钥** " 部分中转到 Key Vault 实例，然后选择 " **生成/导入**"。 选择要作为的 **密钥类型** `RSA` ，将 **RSA 密钥大小** 至少为 `2048` 位。 `EC` 当前不支持将密钥类型作为 Batch 帐户上的客户托管密钥。

![创建密钥](./media/batch-customer-managed-key/create-key.png)

创建密钥后，单击新创建的密钥和当前版本，在 "**属性**" 部分中复制 **密钥标识符**。  请确保选中 " **允许的操作**" 下的 " **自动换行** " 和 " **解包密钥** "。

## <a name="enable-customer-managed-keys-on-a-batch-account"></a>在批处理帐户上启用客户管理的密钥

完成上述步骤后，可以在 Batch 帐户上启用客户管理的密钥。

### <a name="azure-portal"></a>Azure 门户

在 [Azure 门户](https://portal.azure.com/)中，请切换到 "Batch 帐户" 页。 在 " **加密** " 部分下，启用 **客户管理的密钥**。 你可以直接使用密钥标识符，也可以选择密钥保管库，然后单击 " **选择密钥保管库和密钥**"。

![显示加密部分和选项以启用客户托管密钥的屏幕截图](./media/batch-customer-managed-key/encryption-page.png)

### <a name="azure-cli"></a>Azure CLI

使用系统分配的托管标识创建批处理帐户并授予对 Key Vault 的访问权限后，请使用参数下的 URL 更新批处理帐户 `{Key Identifier}` `keyVaultProperties` 。 还将 **encryption_key_source** 设置为 `Microsoft.KeyVault` 。

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_source Microsoft.KeyVault \
    --encryption_key_identifier {YourKeyIdentifier} 
```

## <a name="create-a-batch-account-with-user-assigned-managed-identity-and-customer-managed-keys"></a>使用用户分配的托管标识和客户托管的密钥创建 Batch 帐户

使用 Batch management .NET 客户端，您可以创建一个批处理帐户，该帐户将具有用户分配的托管标识和客户管理的密钥。

```c#
EncryptionProperties encryptionProperties = new EncryptionProperties()
{
    KeySource = KeySource.MicrosoftKeyVault,
    KeyVaultProperties = new KeyVaultProperties()
    {
        KeyIdentifier = "Your Key Azure Resource Manager Resource ID"
    }
};

BatchAccountIdentity identity = new BatchAccountIdentity()
{
    Type = ResourceIdentityType.UserAssigned,
    UserAssignedIdentities = new Dictionary<string, BatchAccountIdentityUserAssignedIdentitiesValue>
    {
            ["Your Identity Azure Resource Manager ResourceId"] = new BatchAccountIdentityUserAssignedIdentitiesValue()
    }
};
var parameters = new BatchAccountCreateParameters(TestConfiguration.ManagementRegion, encryption:encryptionProperties, identity: identity);

var account = await batchManagementClient.Account.CreateAsync("MyResourceGroup",
    "mynewaccount", parameters); 
```

## <a name="update-the-customer-managed-key-version"></a>更新客户托管的密钥版本

当你创建密钥的新版本时，请更新 Batch 帐户以使用新版本。 请执行下列步骤：

1. 在 Azure 门户中导航到 Batch 帐户，并显示加密设置。
2. 输入新密钥版本的 URI。 或者，你可以再次选择 "Key Vault" 和 "密钥" 来更新版本。
3. 保存所做更改。

你还可以使用 Azure CLI 来更新版本。

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourKeyIdentifierWithNewVersion} 
```

## <a name="use-a-different-key-for-batch-encryption"></a>使用不同的密钥进行批处理加密

若要更改用于批处理加密的密钥，请执行以下步骤：

1. 导航到 Batch 帐户并显示加密设置。
2. 输入新密钥的 URI。 或者，您可以选择 Key Vault 并选择一个新密钥。
3. 保存所做更改。

你还可以使用 Azure CLI 来使用不同的密钥。

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourNewKeyIdentifier} 
```

## <a name="frequently-asked-questions"></a>常见问题

- **现有批处理帐户是否支持客户托管的密钥？** 不是。 仅新的批处理帐户支持客户管理的密钥。
- **能否选择大于2048位的 RSA 密钥大小？** 是的，还支持 RSA 密钥大小 `3072` 和 `4096` 位数。
- **吊销客户管理的密钥后可执行哪些操作？** 如果批处理失去了对客户管理的密钥的访问权限，则唯一允许的操作是帐户删除。
- **如果意外删除了 Key Vault 的密钥，应该如何还原批处理帐户的访问权限？** 由于已启用清除保护和软删除，因此可以还原现有密钥。 有关详细信息，请参阅 [恢复 Azure Key Vault](../key-vault/general/key-vault-recovery.md)。
- **能否禁用客户管理的密钥？** 你可以随时将批处理帐户的加密类型设置回 "Microsoft 托管密钥"。 此后，可以随意删除或更改该密钥。
- **如何轮换密钥？** 客户管理的密钥不会自动轮替。 若要轮换密钥，请更新与帐户相关联的密钥标识符。
- **还原访问权限后，批处理帐户再次工作需要多长时间？** 还原访问后，最多可能需要10分钟的时间才能访问该帐户。
- **虽然批处理帐户不可用，但我的资源会发生什么情况呢？** 当 Batch 访问客户托管密钥时，正在运行的所有池将继续运行。 但是，节点将转换为不可用状态，并且任务将停止运行 (，并) 重新排队。 还原访问权限后，节点将再次变得可用，任务将重新启动。
- **此加密机制是否适用于批处理池中的 VM 磁盘？** 不是。 对于云服务配置池，不对 OS 和临时磁盘应用加密。 对于虚拟机配置池，默认情况下，将使用 Microsoft 平台托管密钥对 OS 和任何指定的数据磁盘进行加密。 目前，不能为这些磁盘指定您自己的密钥。 若要使用 Microsoft 平台托管密钥为批处理池加密 Vm 的临时磁盘，必须在[虚拟机配置](/rest/api/batchservice/pool/add#virtualmachineconfiguration)池中启用[diskEncryptionConfiguration](/rest/api/batchservice/pool/add#diskencryptionconfiguration)属性。 对于高度敏感的环境，我们建议启用临时磁盘加密，避免将敏感数据存储在 OS 和数据磁盘上。 有关详细信息，请参阅 [创建启用了磁盘加密的池](./disk-encryption.md)
- **系统分配的托管标识在计算节点上可用的批处理帐户上吗？** 不是。 系统分配的托管标识目前仅用于访问客户托管的密钥的 Azure Key Vault。 若要在计算节点上使用用户分配的托管标识，请参阅 [在 Batch 池中配置托管标识](managed-identity-pools.md)。

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure Batch 中的最佳安全方案](security-best-practices.md)。
- 详细了解[Azure Key Vault](../key-vault/general/basic-concepts.md)。
