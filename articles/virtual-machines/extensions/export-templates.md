---
title: 导出包含 VM 扩展的 Azure 资源组
description: 导出包含虚拟机扩展的 Resource Manager 模板。
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 7f4e2ca6-f1c7-4f59-a2cc-8f63132de279
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: akjosh
ms.openlocfilehash: 40b8ff01a5c8d00d3234af31877ea58c471026e8
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94955746"
---
# <a name="exporting-resource-groups-that-contain-vm-extensions"></a>导出包含 VM 扩展的资源组

可以将 Azure 资源组导出到新的 Resource Manager 模板，然后重新部署该模板。 导出过程会对现有资源进行解释，并创建一个 Resource Manager 模板，该模板在部署后会生成类似的资源组。 针对包含虚拟机扩展的资源组使用资源组导出选项时，需考虑多个事项，例如扩展兼容性和受保护设置。

本文档详细介绍在使用虚拟机扩展（包括支持的扩展的列表）时如何实施资源组导出过程，以及如何处理受保护的数据。

## <a name="supported-virtual-machine-extensions"></a>支持的虚拟机扩展

提供多种虚拟机扩展。 并非所有扩展都可以使用“自动化脚本”功能导出到 Resource Manager 模板中。 如果某个虚拟机扩展不受支持，则需通过手动方式将其重新放置到导出的模板中。

以下扩展可以使用自动化脚本功能导出。

> Acronis Backup、Acronis Backup Linux、Bg Info、BMC CTM Agent Linux、BMC CTM Agent Windows、Chef Client、自定义脚本、自定义脚本扩展、适用于 Linux 的自定义脚本、Datadog Linux 代理、Datadog Windows 代理、Docker 扩展、DSC 扩展、Dynatrace Linux、Dynatrace Windows、HPE Security Application Defender、IaaS Antimalware、IaaS Diagnostics、Linux Chef Client、Linux 诊断、OS Patching For Linux、Puppet 代理、Site 24x7 Apm Insight、Site 24x7 Linux Server、Site 24x7 Windows Server、Trend Micro DSA、Trend Micro DSA Linux、VM Access For Linux、适用于 Linux 的 VM 访问权限、VM Snapshot、VM Snapshot Linux

## <a name="export-the-resource-group"></a>导出资源组

要将资源组导出到可重用模板中，请完成以下步骤：

1. 登录到 Azure 门户
2. 在“中心”菜单上，单击“资源组”
3. 从列表中选择目标资源组
4. 在“资源组”边栏选项卡中，单击“自动化脚本”

![模板导出](./media/export-templates/template-export.png)

Azure 资源管理器自动化脚本生成一个资源管理器模板、一个参数文件以及多个示例部署脚本（例如 PowerShell 和 Azure CLI）。 目前可以使用下载按钮下载导出的模板、将其作为新模板添加到模板库，或使用部署按钮重新对其进行部署。

## <a name="configure-protected-settings"></a>配置受保护的设置

许多 Azure 虚拟机扩展包括受保护的设置配置，用于加密敏感数据（例如凭据和配置字符串）。 受保护的设置不可通过自动化脚本导出。 可以根据需要将受保护的设置重新插入到导出的模板中。

### <a name="step-1---remove-template-parameter"></a>步骤 1 - 删除模板参数

导出资源组时，会创建单个模板参数，为导出的受保护设置提供值。 可以删除此参数。 若要删除此参数，可通过参数列表查找并删除看起来类似于此 JSON 示例的参数。

```json
"extensions_extensionname_protectedSettings": {
    "defaultValue": null,
    "type": "SecureObject"
}
```

### <a name="step-2---get-protected-settings-properties"></a>步骤 2 - 获取受保护设置属性

由于每个受保护的设置都有一组必需的属性，因此需收集这些属性的列表。 可以在 [GitHub 上的 Azure 资源管理器架构](https://raw.githubusercontent.com/Azure/azure-resource-manager-schemas/master/schemas/2015-08-01/Microsoft.Compute.json)中找到受保护设置配置的每个参数。 此架构仅包括本文档概述部分列出的扩展的参数集。 

从架构存储库中搜索所需的扩展（在本示例中为 `IaaSDiagnostics`）。 找到扩展的 `protectedSettings` 对象以后，记下每个参数。 以 `IaasDiagnostic` 扩展为例，所需参数为 `storageAccountName`、`storageAccountKey` 和 `storageAccountEndPoint`。

```json
"protectedSettings": {
    "type": "object",
    "properties": {
        "storageAccountName": {
            "type": "string"
        },
        "storageAccountKey": {
            "type": "string"
        },
        "storageAccountEndPoint": {
            "type": "string"
        }
    },
    "required": [
        "storageAccountName",
        "storageAccountKey",
        "storageAccountEndPoint"
    ]
}
```

### <a name="step-3---re-create-the-protected-configuration"></a>步骤 3 - 重新创建受保护的配置

在导出的模板中，搜索 `protectedSettings` 并将导出的受保护设置对象替换为新的对象，其中包括所需的扩展参数以及每个参数的值。

以 `IaasDiagnostic` 扩展为例，新的受保护设置配置将如以下示例所示：

```json
"protectedSettings": {
    "storageAccountName": "[parameters('storageAccountName')]",
    "storageAccountKey": "[parameters('storageAccountKey')]",
    "storageAccountEndPoint": "https://core.windows.net"
}
```

最终的扩展资源看起来类似于以下 JSON 示例：

```json
{
    "name": "Microsoft.Insights.VMDiagnosticsSettings",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "[variables('apiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "tags": {
        "displayName": "AzureDiagnostics"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Diagnostics",
        "type": "IaaSDiagnostics",
        "typeHandlerVersion": "1.5",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
            "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
        },
        "protectedSettings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]",
            "storageAccountEndPoint": "https://core.windows.net"
        }
    }
}
```

如果使用模板参数提供属性值，则需创建这些参数。 为受保护的设置值创建模板参数时，请确保使用 `SecureString` 参数类型，以便保护敏感值。 如需详细了解如何使用参数，请参阅[创作 Azure 资源管理器模板](../../azure-resource-manager/templates/template-syntax.md)。

以 `IaasDiagnostic` 扩展为例，会在 Resource Manager 模板的参数部分创建以下参数。

```json
"storageAccountName": {
    "defaultValue": null,
    "type": "SecureString"
},
"storageAccountKey": {
    "defaultValue": null,
    "type": "SecureString"
}
```

目前可以使用任何模板部署方法部署此模板。
