---
title: 静态数据的双重加密
titleSuffix: Azure HDInsight
description: 本文介绍可用于 Azure HDInsight 群集上静态数据的两个加密层。
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/10/2020
ms.openlocfilehash: 58b3d892ea24430a9d951a5a0230282f6c4fd584
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988623"
---
# <a name="azure-hdinsight-double-encryption-for-data-at-rest"></a>Azure HDInsight 静态数据双重加密

本文介绍 Azure HDInsight 群集中静态数据的加密方法。 静态数据加密是指对附加到 HDInsight 群集虚拟机的托管磁盘（数据磁盘、OS 磁盘和临时磁盘）进行加密。 

本文档不会探讨 Azure 存储帐户中存储的数据。 群集中可能附加了一个或多个 Azure 存储帐户，其中的加密密钥可能是由 Microsoft 托管的或者客户自己管理的，但加密服务是不同的。 有关 Azure 存储加密的详细信息，请参阅[静态数据的 Azure 存储加密](../storage/common/storage-service-encryption.md)。

## <a name="introduction"></a>简介

在 Azure 中有三个主要的托管磁盘角色：数据磁盘、OS 磁盘和临时磁盘。 有关不同类型的托管磁盘的详细信息，请参阅 [Azure 托管磁盘简介](../virtual-machines/managed-disks-overview.md)。 

HDInsight 支持两个不同层中多种类型的加密：

- 服务器端加密 (SSE) - SSE 由存储服务执行。 在 HDInsight 中，SSE 用于加密 OS 磁盘和数据磁盘。 它默认为启用状态。 SSE 是第 1 层加密服务。
- 在主机上使用平台管理的密钥进行加密（类似于 SSE），此类型的加密由存储服务执行。 但是，它仅适用于临时磁盘，且默认情况下不启用。 主机加密也是第 1 层加密服务。
- 使用客户管理的密钥进行静态加密 - 可对数据和临时磁盘使用此类型的加密。 默认情况下不启用，并要求客户通过 Azure 密钥保管库提供自己的密钥。 静态加密是第 2 层加密服务。

下表汇总了这些类型。

|群集类型 |OS 磁盘（托管磁盘） |数据磁盘（托管磁盘） |临时数据磁盘（本地 SSD） |
|---|---|---|---|
|Kafka、有加速写入的 HBase|Layer1：默认为 [SSE 加密](../virtual-machines/managed-disks-overview.md#encryption)|Layer1：默认为 [SSE 加密](../virtual-machines/managed-disks-overview.md#encryption)，Layer2：使用 CMK 的可选静态加密|Layer1：使用 PMK 的可选主机加密，Layer2：使用 CMK 的可选静态加密|
|所有其他群集（Spark、Interactive、Hadoop、无加速写入的 HBase）|Layer1：默认为 [SSE 加密](../virtual-machines/managed-disks-overview.md#encryption)|空值|Layer1：使用 PMK 的可选主机加密，Layer2：使用 CMK 的可选静态加密|

## <a name="encryption-at-rest-using-customer-managed-keys"></a>使用客户管理的密钥进行静态加密

客户管理的密钥加密是在群集创建期间处理的单步骤过程，不收取额外的费用。 需要做的就是使用 Azure Key Vault 授权托管标识，并在创建群集时添加加密密钥。

群集每个节点上的数据磁盘和临时磁盘均已使用对称数据加密密钥 (DEK) 进行加密。 使用密钥保管库中的密钥加密密钥 (KEK) 保护 DEK。 加密和解密过程完全由 Azure HDInsight 处理。

对于附加到群集 VM 的 OS 磁盘，只有一个加密层 (PMK) 可用。 如果方案需要 CMK 加密，建议客户避免将敏感数据复制到 OS 磁盘。

如果在存储磁盘加密密钥的密钥保管库上启用密钥保管库防火墙，则必须将用于部署群集的区域的 HDInsight 区域资源提供程序 IP 地址添加到密钥保管库防火墙配置。 这是必需的，因为 HDInsight 不是受信任的 Azure 密钥保管库服务。

可以使用 Azure 门户或 Azure CLI 安全地旋转密钥保管库中的密钥。 轮换密钥时，HDInsight 群集在几分钟内即可开始使用新密钥。 启用[软删除](../key-vault/general/soft-delete-overview.md)密钥保护功能可以防范勒索软件和意外删除。 未启用此项保护功能的 Key Vault 不受支持。

### <a name="get-started-with-customer-managed-keys"></a>客户托管密钥入门

若要创建已启用客户管理的密钥的 HDInsight 群集，请完成以下步骤：

1. 创建 Azure 资源的托管标识
1. 创建 Azure Key Vault
1. 创建密钥
1. 创建访问策略
1. 创建已启用客户管理的密钥的 HDInsight 群集
1. 转换加密密钥

以下各部分详细介绍了每个步骤。

### <a name="create-managed-identities-for-azure-resources"></a>创建 Azure 资源的托管标识

创建用户分配的托管标识，以便向 Key Vault 进行身份验证。

有关具体步骤，请参阅[创建用户分配的托管标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。 有关 Azure HDInsight 中托管标识的工作原理的详细信息，请参阅 [Azure HDInsight 中的托管标识](hdinsight-managed-identities.md)。 将托管标识资源 ID 添加到 Key Vault 访问策略时，请务必保存该 ID。

### <a name="create-azure-key-vault"></a>创建 Azure Key Vault

创建密钥保管库。 有关具体步骤，请参阅[创建 Azure Key Vault](../key-vault/general/quick-create-portal.md)。

HDInsight 仅支持 Azure Key Vault。 如果拥有自己的密钥保管库，则可以将密钥导入 Azure Key Vault。 请记住，密钥保管库必须启用“软删除”。 有关导入现有密钥的详细信息，请访问[关于密钥、机密和证书](../key-vault/general/about-keys-secrets-certificates.md)。

### <a name="create-key"></a>创建密钥

1. 在新密钥保管库中，导航到“设置” > “密钥” > “生成/导入”。  

    ![在 Azure Key Vault 中生成新密钥](./media/disk-encryption/create-new-key.png "在 Azure Key Vault 中生成新密钥")

1. 提供名称，然后选择“创建”。 保留默认 **密钥类型** **RSA**。

    ![生成密钥名称](./media/disk-encryption/create-key.png "生成密钥名称")

1. 返回到“密钥”页时，选择创建的密钥。

    ![Key Vault 密钥列表](./media/disk-encryption/key-vault-key-list.png)

1. 选择要打开“密钥版本”页的版本。 使用自己的密钥加密 HDInsight 群集时，需要提供密钥 URI。 复制“密钥标识符”并将其保存在某处，直到你准备好创建群集。

    ![获取密钥标识符](./media/disk-encryption/get-key-identifier.png)

### <a name="create-access-policy"></a>创建访问策略

1. 在新密钥保管库中，导航到“设置” > “访问策略” > “+ 添加访问策略”。  

    ![创建新的 Azure Key Vault 访问策略](./media/disk-encryption/key-vault-access-policy.png)

1. 在“添加访问策略”页中提供以下信息：

    |属性 |说明|
    |---|---|
    |密钥权限|选择“获取”、“解包密钥”和“包装密钥”。|
    |机密权限|选择“获取”、“设置”和“删除”。  |
    |选择主体|选择前面创建的用户分配的托管标识。|

    ![为 Azure Key Vault 访问策略设置“选择主体”](./media/disk-encryption/azure-portal-add-access-policy.png)

1. 选择“添加”  。

1. 选择“保存” 。

    ![保存 Azure Key Vault 访问策略](./media/disk-encryption/add-key-vault-access-policy-save.png)

### <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>创建支持客户管理的密钥磁盘加密的群集

现在已准备好新建 HDInsight 群集。 客户管理的密钥只能在群集创建期间应用于新群集。 无法从客户管理的密钥群集中删除加密，无法将客户管理的密钥添加到现有群集。

从 2020 年 11 月版本开始，HDInsight 支持使用版本控制和不限版本的密钥 URI 创建群集。 如果使用不限版本的密钥 URI 创建群集，则在 Azure Key Vault 中的密钥更新时，HDInsight 群集将尝试执行密钥自动轮替。 如果使用版本控制的密钥 URI 创建群集，则必须执行手动密钥轮替，如[轮换加密密钥](#rotating-the-encryption-key)中所述。

对于在 2020 年 11 月版本之前创建的群集，必须使用版本控制的密钥 URI 手动执行密钥轮替。

#### <a name="using-the-azure-portal"></a>使用 Azure 门户

在群集创建过程中，可以按以下方式使用版本控制的密钥或不限版本的密钥：

- 版本控制 - 在群集创建期间，提供完整的“密钥标识符”，包括密钥版本 。 例如，`https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4`。
- 不限版本 - 在群集创建过程中，仅提供密钥标识符 。 例如，`https://contoso-kv.vault.azure.net/keys/myClusterKey`。

还需要将托管标识分配给群集。

![创建新群集](./media/disk-encryption/create-cluster-portal.png)

#### <a name="using-azure-cli"></a>使用 Azure CLI

以下示例演示如何使用 Azure CLI 来创建已启用磁盘加密的新 Apache Spark 群集。 有关详细信息，请参阅 [Azure CLI az hdinsight create](/cli/azure/hdinsight#az-hdinsight-create)。 `encryption-key-version` 参数是可选的。

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \
-p "HttpPassword1234!" --workernode-data-disks-per-node 2 \
--storage-account MyStorageAccount \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--assign-identity MyMSI
```

#### <a name="using-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板

以下示例演示如何使用 Azure 资源管理器模板来创建已启用磁盘加密的新 Apache Spark 群集。 有关详细信息，请参阅[什么是 ARM 模板？](../azure-resource-manager/templates/overview.md)。 资源管理器模板属性 `diskEncryptionKeyVersion` 是可选的。

此示例使用 PowerShell 调用模板。

```powershell
$templateFile = "azuredeploy.json"
$ResourceGroupName = "MyResourceGroup"
$clusterName = "MyCluster"
$password = ConvertTo-SecureString 'HttpPassword1234!' -AsPlainText -Force
$diskEncryptionVaultUri = "https://MyKeyVault.vault.azure.net"
$diskEncryptionKeyName = "SparkClusterKey"
$diskEncryptionKeyVersion = "00000000000000000000000000000000"
$managedIdentityName = "MyMSI"

New-AzResourceGroupDeployment `
  -Name mySpark `
  -TemplateFile $templateFile `
  -ResourceGroupName $ResourceGroupName `
  -clusterName $clusterName `
  -clusterLoginPassword $password `
` -sshPassword $password `
  -diskEncryptionVaultUri $diskEncryptionVaultUri `
  -diskEncryptionKeyName $diskEncryptionKeyName `
  -diskEncryptionKeyVersion $diskEncryptionKeyVersion `
  -managedIdentityName $managedIdentityName
```

资源管理模板 `azuredeploy.json` 的内容：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "0.9.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "metadata": {
        "description": "The name of the HDInsight cluster to create."
      }
    },
    "clusterLoginUserName": {
      "type": "string",
      "defaultValue": "admin",
      "metadata": {
        "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
      }
    },
    "clusterLoginPassword": {
      "type": "securestring",
      "metadata": {
        "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "The location where all azure resources will be deployed."
      }
    },
    "sshUserName": {
      "type": "string",
      "defaultValue": "sshuser",
      "metadata": {
        "description": "These credentials can be used to remotely access the cluster."
      }
    },
    "sshPassword": {
      "type": "securestring",
      "metadata": {
        "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
      }
    },
    "headNodeSize": {
      "type": "string",
      "defaultValue": "Standard_D12_v2",
      "metadata": {
        "description": "The VM size of the head nodes."
      }
    },
    "workerNodeSize": {
      "type": "string",
      "defaultValue": "Standard_D13_v2",
      "metadata": {
        "description": "The VM size of the worker nodes."
      }
    },
    "diskEncryptionVaultUri": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault DNSname."
      }
    },
    "diskEncryptionKeyName": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault key name."
      }
    },
    "diskEncryptionKeyVersion": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault key version for the selected key."
      }
    },
    "managedIdentityName": {
      "type": "string",
      "metadata": {
        "description": "The user-assigned managed identity."
      }
    }
  },
  "variables": {
    "defaultStorageAccount": {
      "name": "[uniqueString(resourceGroup().id)]",
      "type": "Standard_LRS"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('defaultStorageAccount').name]",
      "location": "[parameters('location')]",
      "apiVersion": "2019-06-01",
      "sku": {
        "name": "[variables('defaultStorageAccount').type]"
      },
      "kind": "Storage",
      "properties": {}
    },
    {
      "apiVersion": "2018-06-01-preview",
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.HDInsight/clusters",
      "location": "[parameters('location')]",
      "properties": {
        "clusterVersion": "3.6",
        "osType": "Linux",
        "tier": "standard",
        "clusterDefinition": {
          "kind": "spark",
          "componentVersion": {
            "Spark": "2.3"
          },
          "configurations": {
            "gateway": {
              "restAuthCredential.isEnabled": true,
              "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
              "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
            }
          }
        },
        "storageProfile": {
          "storageaccounts": [
            {
              "name": "[replace(replace(reference(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccount').name), '2019-06-01').primaryEndpoints.blob,'https://',''),'/','')]",
              "isDefault": true,
              "container": "[parameters('clusterName')]",
              "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccount').name), '2019-06-01').keys[0].value]"
            }
          ]
        },
        "computeProfile": {
          "roles": [
            {
              "name": "headnode",
              "minInstanceCount": 1,
              "targetInstanceCount": 2,
              "hardwareProfile": {
                "vmSize": "[parameters('headNodeSize')]"
              },
              "osProfile": {
                "linuxOperatingSystemProfile": {
                  "username": "[parameters('sshUserName')]",
                  "password": "[parameters('sshPassword')]"
                },
              },
            },
            {
              "name": "workernode",
              "targetInstanceCount": 1,
              "hardwareProfile": {
                "vmSize": "[parameters('workerNodeSize')]"
              },
              "osProfile": {
                "linuxOperatingSystemProfile": {
                  "username": "[parameters('sshUserName')]",
                  "password": "[parameters('sshPassword')]"
                },
              },
            }
          ]
        },
        "minSupportedTlsVersion": "1.2",
        "diskEncryptionProperties": {
          "vaultUri": "[parameters('diskEncryptionVaultUri')]",
          "keyName": "[parameters('diskEncryptionKeyName')]",
          "keyVersion": "[parameters('diskEncryptionKeyVersion')]",
          "msiResourceId": "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('managedIdentityName'))]"
        }
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('managedIdentityName'))]": {}
        }
      }
    }
  ]
}
```

### <a name="rotating-the-encryption-key"></a>转换加密密钥

你可以使用 Azure 门户或 Azure CLI 更改正在运行的群集中使用的加密密钥。 对于此操作，群集必须有权访问当前密钥和所需的新密钥，否则轮换密钥操作将会失败。 对于在 2020 年 11 月版本之后创建的群集，可以选择新密钥是否控制版本。 对于在 2020 年 11 月版本之前创建的群集，在轮换加密密钥时必须使用版本控制的密钥。

#### <a name="using-the-azure-portal"></a>使用 Azure 门户

若要轮换密钥，需要基密钥保管库 URI。 完成此操作后，转到门户中的“HDInsight 群集属性”部分，单击“磁盘加密密钥 URL”下的“更改密钥”。  输入新密钥的 URL，并提交轮换密钥的操作。

![轮换磁盘加密密钥](./media/disk-encryption/change-key.png)

#### <a name="using-azure-cli"></a>使用 Azure CLI

以下示例演示如何轮换现有 HDInsight 群集的磁盘加密密钥。 有关详细信息，请参阅 [Azure CLI az hdinsight rotate-disk-encryption-key](/cli/azure/hdinsight#az-hdinsight-rotate-disk-encryption-key)。

```azurecli
az hdinsight rotate-disk-encryption-key \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--name MyCluster \
--resource-group MyResourceGroup
```

## <a name="faq-for-customer-managed-key-encryption"></a>客户管理的密钥加密的常见问题解答

**HDInsight 群集如何访问我的 Key Vault？**

HDInsight 使用与 HDInsight 群集关联的托管标识来访问 Azure Key Vault 实例。 可以在群集创建之前或创建期间创建此托管标识。 还需要对存储密钥的密钥保管库的托管标识授予访问权限。

**此功能是否适用于 HDInsight 上的所有群集？**

客户管理的密钥加密适用于除 Spark 2.1 和 2.2 以外的其他所有群集类型。

**是否可以使用多个密钥来加密不同的磁盘或文件夹？**

不可以，所有托管磁盘和资源磁盘由同一个密钥加密。

**如果群集失去了对 Key Vault 或密钥的访问权限，会发生什么情况？**

如果群集失去了对密钥的访问权限，Apache Ambari 门户中会显示警告。 在此状态下，“更改密钥”操作将会失败。 恢复密钥访问权限后，Ambari 警告将会消失，密钥轮换等操作可以成功执行。

![密钥访问 Ambari 警报](./media/disk-encryption/ambari-alert.png)

**如果删除密钥，如何恢复群集？**

由于仅支持已启用“软删除”的密钥，因此，如果 Key Vault 中的密钥已恢复，则群集应重新获得对密钥的访问权限。 若要恢复 Azure Key Vault 密钥，请参阅 [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) 或 [az-keyvault-key-recover](/cli/azure/keyvault/key#az-keyvault-key-recover)。


**如果群集已纵向扩展，新节点是否无缝支持客户管理的密钥？**

是的。 在纵向扩展期间，群集需要访问密钥保管库中的密钥。 将使用同一个密钥来加密群集中的托管磁盘和资源磁盘。

**是否可以在我的位置使用客户管理的密钥？**

可以在所有公有云和国家云中使用 HDInsight 客户管理的密钥。

## <a name="encryption-at-host-using-platform-managed-keys"></a>使用平台管理的密钥加密主机

### <a name="enable-in-the-azure-portal"></a>在 Azure 门户中启用

在 Azure 门户中创建群集期间，可以启用主机加密。

> [!Note]
> 启用主机加密时，不能将应用程序从 Azure 市场添加到 HDInsight 群集。

:::image type="content" source="media/disk-encryption/encryption-at-host.png" alt-text="启用主机加密。":::

使用此选项可以 [在主机上](../virtual-machines/disks-enable-host-based-encryption-portal.md) 使用 PMK 对 HDInsight vm 临时数据磁盘进行加密。 仅 [支持在有限区域中的特定 VM sku 上](../virtual-machines/disks-enable-host-based-encryption-portal.md) 加密，且 HDInsight 支持 [以下节点配置和 sku](./hdinsight-supported-node-configuration.md)。

要了解适用于 HDInsight 群集的正确 VM 大小，请参阅[选择适用于 Azure HDInsight 群集的正确 VM 大小](hdinsight-selecting-vm-size.md)。 启用主机加密时，Zookeeper 节点的默认 VM SKU 将为 DS2V2。

### <a name="enable-using-powershell"></a>使用 PowerShell 进行启用

以下代码片段演示如何使用 PowerShell 创建启用了主机加密的新 Azure HDInsight 群集。 它使用参数 `-EncryptionAtHost $true` 来启用此功能。

```powershell
$storageAccountResourceGroupName = "Group"
$storageAccountName = "yourstorageacct001"
$storageAccountKey = Get-AzStorageAccountKey `
    -ResourceGroupName $storageAccountResourceGroupName `
    -Name $storageAccountName | %{ $_.Key1 }
$storageContainer = "container002"
# Cluster configuration info
$location = "East US 2"
$clusterResourceGroupName = "Group"
$clusterName = "your-hadoop-002"
$clusterCreds = Get-Credential
# If the cluster's resource group doesn't exist yet, run:
# New-AzResourceGroup -Name $clusterResourceGroupName -Location $location
# Create the cluster
New-AzHDInsightCluster `
    -ClusterType Hadoop `
    -ClusterSizeInNodes 4 `
    -ResourceGroupName $clusterResourceGroupName `
    -ClusterName $clusterName `
    -HttpCredential $clusterCreds `
    -Location $location `
    -DefaultStorageAccountName "$storageAccountName.blob.core.contoso.net" `
    -DefaultStorageAccountKey $storageAccountKey `
    -DefaultStorageContainer $storageContainer `
    -SshCredential $clusterCreds `
    -EncryptionAtHost $true `
```

### <a name="enable-using-azure-cli"></a>启用 Azure CLI

以下代码片段演示如何使用 Azure CLI 创建启用了主机加密的新 Azure HDInsight 群集。 它使用参数 `--encryption-at-host true` 来启用此功能。

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \\
-p "yourpass" \\
--storage-account MyStorageAccount --encryption-at-host true
```

## <a name="next-steps"></a>后续步骤

* 有关 Azure Key Vault 的详细信息，请参阅[什么是 Azure Key Vault](../key-vault/general/overview.md)。
* [Azure HDInsight 中的企业安全性概述](./domain-joined/hdinsight-security-overview.md)。
