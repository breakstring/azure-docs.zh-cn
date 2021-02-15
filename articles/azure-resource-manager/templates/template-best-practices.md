---
title: 模板最佳实践
description: 介绍创作 Azure 资源管理器模板（ARM 模板）的建议方法。 提供相关建议，避免在使用模板时出现常见问题。
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: 583a113df9cdb1951daf1002dd69531f050cfb54
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99257991"
---
# <a name="arm-template-best-practices"></a>ARM 模板最佳做法

本文介绍了在构造 Azure 资源管理器模板（ARM 模板）时如何使用建议的做法。 这些建议有助于在使用 ARM 模板部署解决方案时避免出现常见问题。

## <a name="template-limits"></a>模板限制

将模板大小限制为 4 MB 以内，每个参数文件大小限制为 64 KB 以内。 4-MB 限制适用于模板使用迭代资源定义以及变量和参数值进行扩展后的最终状态。

还将受限于：

* 256 个参数
* 256 个变量
* 800 个资源（包括副本计数）
* 64 个输出值
* 模板表达式中不超过 24,576 个字符

通过使用嵌套模板，可超出某些模板限制。 有关详细信息，请参阅[在部署 Azure 资源时使用链接模板和嵌套模板](linked-templates.md)。 若要减少参数、变量或输出的数量，可以将几个值合并为一个对象。 有关详细信息，请参阅[对象即参数](/azure/architecture/guide/azure-resource-manager/advanced-templates/objects-as-parameters)。

## <a name="resource-group"></a>资源组

将资源部署到资源组时，资源组会存储与资源有关的元数据。 元数据存储在资源组的位置中。

如果资源组的区域临时不可用，则不能更新资源组中的资源，因为元数据不可用。 其他区域中的资源仍可按预期运行，但你不能更新它们。 为了尽量降低风险，请将资源组和资源放入同一个区域。

## <a name="parameters"></a>参数

使用[参数](template-parameters.md)时，本部分中的信息可以提供帮助。

### <a name="general-recommendations-for-parameters"></a>有关参数的一般建议

* 尽量不要使用参数。 相反，为无需在部署期间指定的属性使用变量或文本值。

* 对参数名称使用混合大小写。

* 为由具体环境（如 SKU、大小或容量）决定的设置使用参数。

* 为要指定的资源名使用参数以便于识别。

* 在元数据中提供每个参数的说明。

    ```json
    "parameters": {
      "storageAccountType": {
        "type": "string",
        "metadata": {
          "description": "The type of the new storage account created to store the VM disks."
        }
      }
    }
    ```

* 为不敏感的参数定义默认值。 通过指定默认值，可更轻松地部署模板，并同时为模板用户提供了一个合适值的示例。 默认参数值须对默认部署配置中的所有用户有效。

    ```json
    "parameters": {
      "storageAccountType": {
        "type": "string",
        "defaultValue": "Standard_GRS",
        "metadata": {
          "description": "The type of the new storage account created to store the VM disks."
        }
      }
    }
    ```

* 若要指定可选参数，请勿将空字符串用作默认值。 而是使用文本值或语言表达式来构造值。

   ```json
   "storageAccountName": {
     "type": "string",
     "defaultValue": "[concat('storage', uniqueString(resourceGroup().id))]",
     "metadata": {
       "description": "Name of the storage account"
     }
   }
   ```

* 请尽量少使用 `allowedValues`。 仅当必须确保允许的选项中不含特定值时使用它。 如果过于广泛地使用 `allowedValues`，可能会因未将列表保持最新而阻碍有效部署。

* 当模板中的参数名称与 PowerShell 部署命令中的参数相同时，资源管理器会将 postfix FromTemplate 添加到模板参数中，以解决此命名冲突。 例如，如果在模板中包括一个名为“ResourceGroupName”的参数，则该参数会与 [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) cmdlet 中的“ResourceGroupName”参数冲突。 在部署期间，系统会提示用户提供 ResourceGroupNameFromTemplate 的值。

### <a name="security-recommendations-for-parameters"></a>有关参数的安全性建议

* 始终为用户名和密码（或机密）使用参数。

* 为所有密码和机密使用 `securestring`。 如果将敏感数据传入 JSON 对象，请使用 `secureObject` 类型。 部署资源后，无法读取带 secureString 或 secureObject 类型的模板参数。

    ```json
    "parameters": {
      "secretValue": {
        "type": "securestring",
        "metadata": {
          "description": "The value of the secret to store in the vault."
        }
      }
    }
    ```

* 请勿为用户名、密码或任何需要 `secureString` 类型的值提供默认值。

* 请勿为导致应用程序攻击面扩大的属性提供默认值。

### <a name="location-recommendations-for-parameters"></a>有关参数的位置建议

* 使用参数指定资源的位置，并将默认值设置为 `resourceGroup().location`。 提供位置参数可以使模板用户能够指定其有权部署资源的位置。

   ```json
   "parameters": {
     "location": {
       "type": "string",
       "defaultValue": "[resourceGroup().location]",
       "metadata": {
         "description": "The location in which the resources should be deployed."
       }
     }
   }
   ```

* 请勿为位置参数指定 `allowedValues`。 指定的位置可能并非在所有云中均可用。

* 为可能位于同一位置的资源使用位置参数值。 此方法可以最大程度地减少用户必须提供位置信息的次数。

* 对于并非在所有位置都可用的资源，请使用单独的参数或指定文本位置值。

## <a name="variables"></a>变量

使用[变量](template-variables.md)时，以下信息可以提供帮助：

* 对变量名称使用混合大小写。

* 针对需要在模板中多次使用的值使用变量。 如果一次只使用一个值，则硬编码值可使模板更易于阅读。

* 为从复杂的复合模板函数构造的值使用变量。 如果复杂的表达式仅出现在变量中，模板会更易读取。

* 不能在模板的 `variables` 节中使用 [reference](template-functions-resource.md#reference) 函数。 `reference` 函数从资源的运行时状态中派生其值。 但是，变量是在初始模板分析期间解析的。 直接在模板的 `resources` 或 `outputs` 节中构造需要 `reference` 函数的值。

* 包括的变量适用于必须唯一的资源名称。

* [在变量中使用复制循环](copy-variables.md)来创建重复的 JSON 对象模式。

* 删除未使用的变量。

## <a name="api-version"></a>API 版本

将 `apiVersion` 属性设置为资源类型的硬编码 API 版本。 创建新模板时，建议使用资源类型的最新 API 版本。 若要确定可用值，请参阅[模板参考](/azure/templates/)。

当模板按预期方式工作时，建议你继续使用同一个 API 版本。 使用同一个 API 版本，你无需担心可能会在更高版本中引入的中断性变更。

请勿对 API 版本使用参数。 资源的属性和值可能因 API 版本而异。 如果将 API 版本设置为参数，代码编辑器中的 IntelliSense 无法确定正确架构。 如果传入的 API 版本与模板中的属性不匹配，则部署会失败。

请勿对 API 版本使用变量。 特别是，不要使用 [providers 函数](template-functions-resource.md#providers)在部署期间动态获取 API 版本。 动态检索到的 API 版本可能与模板中的属性不匹配。

## <a name="resource-dependencies"></a>资源依赖关系

在决定要设置的[依赖项](define-resource-dependency.md)时，请遵循以下准则：

* 使用 `reference` 函数并传入资源名称以在需要共享属性的资源之间设置隐式依赖项。 在已定义隐式依赖项的情况下，请勿添加显式 `dependsOn` 元素。 此方法降低了设置不必要依赖项的风险。 有关设置隐式依赖项的示例，请参阅 [reference 和 list 函数](define-resource-dependency.md#reference-and-list-functions)。

* 将子资源设置为依赖于其父资源。

* 会自动从依赖项顺序中删除 [condition 元素](conditional-resource-deployment.md)设置为 false 的资源。 像始终部署了资源那样设置依赖项。

* 让依赖项级联，无需对其进行显式设置。 例如，虚拟机依赖于虚拟网络接口，虚拟网络接口依赖于虚拟网络和公共 IP 地址。 因此，虚拟机在所有这三个资源之后部署，但请勿将虚拟机显式设置为依赖于所有这三个资源。 此方法阐明了依赖顺序，在以后更改模板会更容易。

* 如果某个值可以在部署之前确定，请尝试在没有依赖项的情况下部署资源。 例如，如果某个配置值需要另一资源的名称，则可能不需要依赖项。 本指南并非始终适用，因为某些资源会验证其他资源是否存在。 如果收到错误，请添加一个依赖项。

## <a name="resources"></a>资源

使用[资源](template-syntax.md#resources)时，以下信息可以提供帮助：

* 为了帮助其他参与者理解该资源的用途，请为模板中的每个资源指定 `comments`。

    ```json
    "resources": [
      {
        "name": "[variables('storageAccountName')]",
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2019-06-01",
        "location": "[resourceGroup().location]",
        "comments": "This storage account is used to store the VM disks.",
          ...
      }
    ]
    ```

* 如果在模板中使用“公共终结点”（例如 Azure Blob 存储公共终结点），请不要将命名空间硬编码 。 使用 `reference` 函数可动态检索命名空间。 可以使用此方法将模板部署到不同的公共命名空间环境，而无需在模板中手动更改终结点。 在模板中将 API 版本设置为用于存储帐户的版本。

    ```json
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": "true",
        "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
      }
    }
    ```

   如果存储帐户部署在正在创建的模板中，并且存储帐户的名称未与模板中的其他资源共享，则在引用资源时不需要指定提供程序命名空间或 `apiVersion`。 以下示例显示了简化的语法。

    ```json
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": "true",
        "storageUri": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
      }
    }
    ```

   还可以引用不同资源组中的现有存储帐户。

    ```json
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": "true",
        "storageUri": "[reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('existingStorageAccountName')), '2019-06-01').primaryEndpoints.blob]"
      }
    }
    ```

* 仅当应用程序有需要时，才将公共 IP 地址分配到虚拟机。 若要连接到虚拟机 (VM) 进行调试或管理，请使用出站 NAT 规则、虚拟网络网关或 jumpbox。

     有关连接到虚拟机的详细信息，请参阅：

   * [在 Azure 中运行用于 N 层体系结构的 VM](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
   * [在 Azure Resource Manager 中设置对 VM 的 WinRM 访问](../../virtual-machines/windows/winrm.md)
   * [使用 Azure 门户实现对 VM 的外部访问](../../virtual-machines/windows/nsg-quickstart-portal.md)
   * [使用 PowerShell 实现对 VM 的外部访问](../../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [使用 Azure CLI 实现对 Linux VM 的外部访问](../../virtual-machines/linux/nsg-quickstart.md)

* 公共 IP 地址的 `domainNameLabel` 属性必须唯一。 `domainNameLabel` 值的长度必须为 3 到 63 个字符，并遵循正则表达式 `^[a-z][a-z0-9-]{1,61}[a-z0-9]$` 指定的规则。 由于 `uniqueString` 函数生成长度为 13 个字符的字符串，因此 `dnsPrefixString` 参数限制为不超过 50 个字符。

    ```json
    "parameters": {
      "dnsPrefixString": {
        "type": "string",
        "maxLength": 50,
        "metadata": {
          "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$"
        }
      }
    },
    "variables": {
      "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
    }
    ```

* 将密码添加到自定义脚本扩展时，请在 `protectedSettings` 属性中使用 `commandToExecute` 属性。

    ```json
    "properties": {
      "publisher": "Microsoft.Azure.Extensions",
      "type": "CustomScript",
      "typeHandlerVersion": "2.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "fileUris": [
          "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
        ]
      },
      "protectedSettings": {
        "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
      }
    }
    ```

   > [!NOTE]
   > 为了确保机密内容作为参数传递给 VM 和扩展时经过加密，请使用相关扩展的 `protectedSettings` 属性。

* 为具有可能会随时间变化的默认值的属性指定显式值。 例如，如果要部署 AKS 群集，则可以指定或省略 `kubernetesVersion` 属性。 如果未指定此项，则 [群集默认为 N 1 次版本和最新修补程序](../../aks/supported-kubernetes-versions.md#azure-portal-and-cli-versions)。 使用 ARM 模板部署群集时，此默认行为可能不是预期的行为。 重新部署模板可能导致群集意外升级到新的 Kubernetes 版本。 相反，请考虑指定显式版本号，然后在准备好升级群集时手动更改版本号。

## <a name="use-test-toolkit"></a>使用测试工具包

ARM 模板测试工具包是一个脚本，用于检查模板是否使用建议的做法。 如果模板不符合建议的做法，它将返回包含建议的更改的警告列表。 测试工具包可帮助你了解如何在模板中实施最佳做法。

完成模板后，请运行测试工具包，看是否有方法可以改进它的实现。 有关详细信息，请参阅[使用 ARM 模板测试工具包](test-toolkit.md)。

## <a name="next-steps"></a>后续步骤

* 有关模板文件结构的信息，请参阅[了解 ARM 模板的结构和语法](template-syntax.md)。
* 有关如何生成在所有 Azure 云环境中工作的模板的建议，请参阅[开发用于实现云一致性的 ARM 模板](templates-cloud-consistency.md)。
