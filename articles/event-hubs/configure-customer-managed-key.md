---
title: 为静态 Azure 事件中心数据配置你自己的密钥
description: 本文提供了有关如何配置自己的密钥来加密 Azure 事件中心数据 rest 的信息。
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: 53622344e36e514543d547dec95caaf1b0b76a13
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430673"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest-by-using-the-azure-portal"></a>配置客户托管的密钥，以便通过使用 Azure 门户来加密静态 Azure 事件中心数据
Azure 事件中心通过 azure 存储服务加密 (Azure SSE) 提供静态数据的加密。 事件中心服务使用 Azure 存储来存储数据。 使用 Azure 存储空间存储的所有数据都使用 Microsoft 托管密钥进行加密。 如果你使用自己的密钥 (也称为创建自己的密钥 (BYOK) 或客户托管的密钥) ，则仍使用 Microsoft 托管的密钥对数据进行加密，但另外，将使用客户管理的密钥加密 Microsoft 托管密钥。 利用此功能，您可以创建、轮换、禁用和撤消对用于加密 Microsoft 托管密钥的客户托管密钥的访问权限。 启用 BYOK 功能是在命名空间中执行的一次性设置过程。

> [!NOTE]
> - BYOK 功能受 [事件中心专用单租户](event-hubs-dedicated-overview.md) 群集支持。 不能为标准事件中心命名空间启用此功能。
> - 只能为新的或空的命名空间启用加密。 如果命名空间包含事件中心，则加密操作将失败。

可以使用 Azure Key Vault 管理密钥并审核密钥使用情况。 可以创建自己的密钥并将其存储在 Key Vault 中，或者使用 Azure Key Vault API 来生成密钥。 有关 Azure 密钥保管库的详细信息，请参阅[什么是 Azure 密钥保管库？](../key-vault/general/overview.md)

本文介绍了如何使用 Azure 门户配置包含客户管理的密钥的密钥保管库。 若要了解如何使用 Azure 门户创建 Key Vault，请参阅[快速入门：使用 Azure 门户创建 Azure Key Vault](../key-vault/general/quick-create-portal.md)。

> [!IMPORTANT]
> 通过 Azure 事件中心使用客户托管的密钥，要求密钥保管库配置了两个必需的属性。 它们具有以下特点：“软删除”和“不清除”。 在 Azure 门户中创建新的 Key Vault 时，默认会启用这些属性。 但是，如果需要针对现有的 Key Vault 启用这些属性，必须使用 PowerShell 或 Azure CLI。

## <a name="enable-customer-managed-keys"></a>启用客户管理的密钥
若要在 Azure 门户中启用客户管理的密钥，请执行以下步骤：

1. 导航到事件中心专用层群集。
1. 选择要在其上启用 BYOK 的命名空间。
1. 在事件中心命名空间的 " **设置** " 页上，选择 " **加密**"。 
1. 选择“客户管理的密钥加密(静态)”，如下图所示。 

    ![启用客户管理的密钥](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>设置密钥保管库与密钥
启用客户管理的密钥后，需要将客户托管密钥与 Azure 事件中心命名空间相关联。 事件中心仅支持 Azure Key Vault。 如果启用了上一部分所述的“使用客户管理的密钥进行加密”选项，则需要将密钥导入 Azure Key Vault。 此外，必须为密钥配置“软删除”和“不清除”。 可以使用 [PowerShell](../key-vault/general/key-vault-recovery.md) 或 [CLI](../key-vault/general/key-vault-recovery.md) 配置这些设置。

1. 若要创建新的密钥保管库，请遵循 Azure Key Vault [快速入门](../key-vault/general/overview.md)。 有关导入现有密钥的详细信息，请参阅[关于密钥、机密和证书](../key-vault/general/about-keys-secrets-certificates.md)。
1. 若要在创建保管库时启用“软删除”和“清除保护”，请使用 [az keyvault create](/cli/azure/keyvault#az-keyvault-create) 命令。

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. 若要向现有保管库（已启用“软删除”）添加“清除保护”，请使用 [az keyvault update](/cli/azure/keyvault#az-keyvault-update) 命令。

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. 遵循以下步骤创建密钥：
    1. 若要创建新密钥，请从“设置”  下的“密钥”  菜单中选择“生成/导入”  。
        
        ![选择“生成/导入”按钮](./media/configure-customer-managed-key/select-generate-import.png)
    1. 将“选项”  设置为“生成”  并提供密钥名称。

        ![创建密钥](./media/configure-customer-managed-key/create-key.png) 
    1. 你现在可以从下拉列表中选择此密钥以与用于加密的事件中心命名空间相关联。 

        ![从密钥保管库中选择密钥](./media/configure-customer-managed-key/select-key-from-key-vault.png)
    1. 填写密钥详细信息，然后单击“选择”。 这将允许使用密钥 (客户管理的密钥) 来加密 Microsoft 托管密钥。 


## <a name="rotate-your-encryption-keys"></a>轮换加密密钥
可以使用 Azure Key Vault 轮换机制来轮换密钥保管库中的密钥。 还可以设置激活和过期日期以自动轮换密钥。 事件中心服务将检测新的密钥版本，并自动开始使用它们。

## <a name="revoke-access-to-keys"></a>撤销对密钥的访问权限
撤消对加密密钥的访问权限不会清除事件中心的数据。 但是，无法从事件中心命名空间访问数据。 可以通过使用访问策略或删除密钥来撤销加密密钥。 在[保护对密钥保管库的访问](../key-vault/general/secure-your-key-vault.md)中详细了解访问策略以及如何保护密钥保管库。

撤消加密密钥后，已加密命名空间上的事件中心服务将无法操作。 如果启用了对密钥的访问权限，或者还原了删除密钥，事件中心服务将选取密钥，以便可以从加密的事件中心命名空间访问数据。

## <a name="set-up-diagnostic-logs"></a>设置诊断日志 
为启用了 BYOK 的命名空间设置诊断日志可提供有关操作所需的信息。 可以启用这些日志，稍后将其流式传输到事件中心，或通过 log analytics 进行分析或流式传输到存储，以执行自定义的分析。 若要了解有关诊断日志的详细信息，请参阅 [Azure 诊断日志概述](../azure-monitor/platform/platform-logs-overview.md)。

## <a name="enable-user-logs"></a>启用用户日志
按照以下步骤为客户管理的密钥启用日志。

1. 在 Azure 门户中，导航到已启用 BYOK 的命名空间。
1. 选择 "**监视**" 下的 **诊断设置**。

    ![选择诊断设置](./media/configure-customer-managed-key/select-diagnostic-settings.png)
1. 选择 " **+ 添加诊断设置**"。 

    ![选择 "添加诊断设置"](./media/configure-customer-managed-key/select-add-diagnostic-setting.png)
1. 提供一个 **名称** ，并选择要将日志流式传输到的位置。
1. 选择 **CustomerManagedKeyUserLogs** 并 **保存**。 此操作可在命名空间上启用 BYOK 的日志。

    ![选择客户托管的密钥用户日志选项](./media/configure-customer-managed-key/select-customer-managed-key-user-logs.png)

## <a name="log-schema"></a>日志架构 
所有日志均以 JavaScript 对象表示法 (JSON) 格式存储。 每个条目都具有使用下表中描述的格式的字符串字段。 

| 名称 | 说明 |
| ---- | ----------- | 
| TaskName | 失败的任务的说明。 |
| ActivityId | 用于跟踪的内部 ID。 |
| category | 定义任务的分类。 例如，如果密钥保管库中的密钥处于禁用状态，则它将是信息类别; 如果密钥无法解包，则可能发生错误。 |
| ResourceId | Azure Resource Manager 资源 ID |
| keyVault | 密钥保管库的完整名称。 |
| key | 用于加密事件中心命名空间的密钥名称。 |
| 版本 | 所使用的密钥的版本。 |
| operation | 对密钥保管库中的密钥执行的操作。 例如，禁用/启用密钥、包装或解包 |
| code | 与操作关联的代码。 示例：错误代码404，表示找不到键。 |
| message | 与操作关联的任何错误消息 |

下面是客户托管密钥的日志示例：

```json
{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111-1111-1111-1111-111111111111",
   "category": "error"
   "resourceId": "/SUBSCRIPTIONS/11111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "1111111111111111111111111111111",
   "operation": "wrapKey",
   "code": "404",
   "message": "Key not found: ehbyok0/111111111111111111111111111111",
}



{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111111111-1111-1111-1111111111111",
   "category": "info"
   "resourceId": "/SUBSCRIPTIONS/111111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "111111111111111111111111111111",
   "operation": "disable" | "restore",
   "code": "",
   "message": "",
}
```

## <a name="use-resource-manager-template-to-enable-encryption"></a>使用资源管理器模板启用加密
本部分介绍了如何使用 **Azure 资源管理器模板** 执行以下任务。 

1. 使用托管服务标识创建 **事件中心命名空间** 。
2. 创建 **密钥保管库** 并向服务标识授予对密钥保管库的访问权限。 
3. 用 (键/值) 的密钥保管库信息更新事件中心命名空间。 


### <a name="create-an-event-hubs-cluster-and-namespace-with-managed-service-identity"></a>使用托管服务标识创建事件中心群集和命名空间
本部分说明如何使用 Azure 资源管理器模板和 PowerShell 创建具有托管服务标识的 Azure 事件中心命名空间。 

1. 创建 Azure 资源管理器模板，以使用托管服务标识创建事件中心命名空间。 为文件命名： **CreateEventHubClusterAndNamespace.js**： 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/clusters",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('clusterName')]",
             "location":"[parameters('location')]",
             "sku":{
                "name":"Dedicated",
                "capacity":1
             }
          },
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             },
             "dependsOn":[
                "[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             ]
          }
       ],
       "outputs":{
          "EventHubNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.EventHub/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. 创建名为的模板参数文件： **CreateEventHubClusterAndNamespaceParams.json**。 

    > [!NOTE]
    > 请替换以下值： 
    > - `<EventHubsClusterName>` -事件中心群集的名称    
    > - `<EventHubsNamespaceName>` -事件中心命名空间的名称
    > - `<Location>` -事件中心命名空间的位置

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    
    ```
3. 运行以下 PowerShell 命令以部署模板，以创建事件中心命名空间。 然后，检索事件中心命名空间的 ID 以供以后使用。 在运行该命令之前，请将 `{MyRG}` 替换为资源组的名称。  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateEventHubClusterAndNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateEventHubClusterAndNamespace.json -TemplateParameterFile ./CreateEventHubClusterAndNamespaceParams.json

    $EventHubNamespaceId = $outputs.Outputs["eventHubNamespaceId"].value
    ```
 
### <a name="grant-event-hubs-namespace-identity-access-to-key-vault"></a>向事件中心命名空间标识授予对密钥保管库的访问权限

1. 运行以下命令，以便在启用 **清除保护** 和 **软删除** 的情况下创建密钥保管库。 

    ```powershell
    New-AzureRmKeyVault -Name {keyVaultName} -ResourceGroupName {RGName}  -Location {location} -EnableSoftDelete -EnablePurgeProtection    
    ```     
    
    （或者）    
    
    运行以下命令来更新 **现有的密钥保管库**。 在运行该命令之前，请指定资源组和密钥保管库名称的值。 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. 设置密钥保管库访问策略，以便事件中心命名空间的托管标识可以访问密钥保管库中的密钥值。 使用上一节中的事件中心命名空间的 ID。 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $EventHubNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-event-hubs-namespace-with-customer-managed-key-from-key-vault"></a>利用 key vault 中客户托管的密钥加密事件中心命名空间中的数据
到目前为止，你已完成以下步骤： 

1. 创建了具有托管标识的高级命名空间。
2. 创建了密钥保管库，并向托管标识授予了对该密钥保管库的访问权限。 

在此步骤中，将更新包含密钥保管库信息的事件中心命名空间。 

1. 使用以下内容创建名为 **CreateEventHubClusterAndNamespace.js** 的 JSON 文件： 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          },
          "keyVaultUri":{
             "type":"string",
             "metadata":{
                "description":"URI of the KeyVault."
             }
          },
          "keyName":{
             "type":"string",
             "metadata":{
                "description":"KeyName."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]",
                "encryption":{
                   "keySource":"Microsoft.KeyVault",
                   "keyVaultProperties":[
                      {
                         "keyName":"[parameters('keyName')]",
                         "keyVaultUri":"[parameters('keyVaultUri')]"
                      }
                   ]
                }
             }
          }
       ]
    }
    ``` 

2. 创建模板参数文件： **UpdateEventHubClusterAndNamespaceParams.js**。 

    > [!NOTE]
    > 请替换以下值： 
    > - `<EventHubsClusterName>` -事件中心群集的名称。        
    > - `<EventHubsNamespaceName>` -事件中心命名空间的名称
    > - `<Location>` -事件中心命名空间的位置
    > - `<KeyVaultName>` - 你的密钥保管库的名称
    > - `<KeyName>` - 密钥保管库中密钥的名称

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          },
          "keyName":{
             "value":"<KeyName>"
          },
          "keyVaultUri":{
             "value":"https://<KeyVaultName>.vault.azure.net"
          }
       }
    }
    ```             
3. 运行以下 PowerShell 命令以部署资源管理器模板。 在运行该命令之前，请将 `{MyRG}` 替换为你的资源组名称。 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateEventHubNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateEventHubClusterAndNamespace.json -TemplateParameterFile ./UpdateEventHubClusterAndNamespaceParams.json 
    ```

## <a name="troubleshoot"></a>疑难解答
最佳做法是始终启用日志，如前一部分中所示。 启用 BYOK 加密后，它有助于跟踪活动。 它还有助于限制问题的范围。

下面是启用 BYOK 加密时要查找的常见错误代码。

| 操作 | 错误代码 | 数据的生成状态 |
| ------ | ---------- | ----------------------- | 
| 从密钥保管库中删除包装/解包权限 | 403 |    Inaccessible |
| 从授予了 "包装/解包" 权限的 AAD 主体中删除 AAD 角色成员身份 | 403 |  Inaccessible |
| 从密钥保管库中删除加密密钥 | 404 | Inaccessible |
| 删除密钥保管库 | 404 | 无法访问 (假设启用软删除，这是必需的设置。 )  |
| 更改加密密钥的过期期限，使其已过期 | 403 |   Inaccessible  |
| 在) 之前更改 NBF (，这样密钥加密密钥不会处于活动状态 | 403 | Inaccessible  |
| 为 key vault 防火墙选择 " **允许 MSFT 服务** " 选项，或阻止对具有加密密钥的密钥保管库进行网络访问 | 403 | Inaccessible |
| 将密钥保管库移到其他租户 | 404 | Inaccessible |  
| 间歇性网络问题或 DNS/AAD/MSI 中断 |  | 可使用缓存的数据加密密钥进行访问 |

> [!IMPORTANT]
> 若要在使用 BYOK 加密的命名空间上启用异地灾难恢复，辅助命名空间必须在专用群集中，并且必须在该群集上启用系统分配的托管标识。 若要了解详细信息，请参阅 [Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)。

## <a name="next-steps"></a>后续步骤
请参阅以下文章：
- [事件中心概述](event-hubs-about.md)
- [Key Vault 概述](../key-vault/general/overview.md)