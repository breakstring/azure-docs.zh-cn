---
title: Azure 计算-Linux 诊断扩展4。0
description: 如何配置 Azure Linux 诊断扩展 (LAD) 4.0，收集 Azure 中运行的 Linux Vm 的指标和日志事件。
services: virtual-machines-linux
author: axayjo
manager: gwallace
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 02/05/2021
ms.author: akjosh
ms.openlocfilehash: ebc4867f0ce16657c550b3d33d76fccdb41cef54
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2021
ms.locfileid: "99980637"
---
# <a name="use-linux-diagnostic-extension-40-to-monitor-metrics-and-logs"></a>使用 Linux 诊断扩展4.0 监视指标和日志

本文档介绍 Linux 诊断扩展的版本4.0 和更高版本。

> [!IMPORTANT]
> 有关版本 3. * 的信息，请参阅  [此文档](https://docs.microsoft.com/azure/virtual-machines/extensions/diagnostics-linux-v3)。 有关 2.3 版和更早版本，请参阅[此文档](/previous-versions/azure/virtual-machines/linux/classic/diagnostic-extension-v2)。

## <a name="introduction"></a>简介

Linux 诊断扩展可帮助用户监视 Microsoft Azure 上运行的 Linux VM 的运行状况。 它具有以下功能：

* 从 VM 收集系统性能指标，并存储在指定存储帐户中的特定表中。
* 从 syslog 检索日志事件，并存储在指定存储帐户中的特定表中。
* 让用户能够自定义收集并上传的数据指标。
* 让用户能够自定义收集和上传的事件的 syslog 辅助参数和严重性级别。
* 让用户能够将指定日志文件上传到指定的存储表。
* 支持将指标和日志事件发送到指定存储帐户中的任意 EventHub 终结点和 JSON 格式的 blob。

此扩展适用于这两种 Azure 部署模型。

## <a name="installing-the-extension-in-your-vm"></a>在 VM 中安装扩展

可以使用 Azure PowerShell cmdlet、Azure CLI 脚本、ARM 模板或 Azure 门户启用此扩展。 有关详细信息，请参阅[扩展功能](features-linux.md)。

>[!NOTE]
>[Log Analytics VM 扩展](./oms-linux.md)中还随附了诊断 VM 扩展的某些组件。 由于这种体系结构，如果在同一 ARM 模板中对两个扩展进行实例化，则可能会发生冲突。 为避免这些安装时冲突，请使用 [`dependsOn` 指令](../../azure-resource-manager/templates/define-resource-dependency.md#dependson)，确保按顺序安装扩展。 可按任一顺序安装扩展。

这些安装说明和 [可下载示例配置](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) 会将 LAD 4.0 配置为：

* 捕获并存储与 LAD 2.3，3 *;
* 将度量值与常用接收器一起发送到 Azure Monitor 接收器，并将其发送到 Azure 存储，Lad 4.0 中的新增指标
* 捕获一组有用的文件系统指标，如 LAD 3.0 所提供;
* 捕获 LAD 2.3 允许的默认 syslog 收集；
* 允许 Azure 门户体验，以便对 VM 指标进行制图以及就其发送警报。

可下载配置只是一个示例；请根据需要对其进行修改。

### <a name="supported-linux-distributions"></a>支持的 Linux 分发

Linux 诊断扩展支持以下分发和版本。 分发和版本的列表仅适用于 Azure 认可的 Linux 供应商映像。 Linux 诊断扩展通常不支持第三方 BYOL 和 BYOS 映像（如设备）。

仅列出主要版本（如 Debian 7）的分发对于所有次要版本也均受支持。 如果指定了特定的次要版本，则仅支持该特定版本；如果追加了“+”，则支持的次要版本等于或大于指定的版本。

支持的分发和版本：

- Ubuntu 18.04、16.04、14.04
- CentOS 7、6.5+
- Oracle Linux 7、6.4+
- OpenSUSE 13.1+
- SUSE Linux Enterprise Server 12
- Debian 9、8、7
- RHEL 7、6.7+

### <a name="prerequisites"></a>先决条件

* Azure Linux 代理 2.2.0 版或更高版本。 大部分 Azure VM Linux 库映像包含 2.2.7 或更高版本。 运行 `/usr/sbin/waagent -version` 以确认 VM 上安装的版本。 如果 VM 正在运行较早版本的来宾代理，请按照[以下说明](./update-linux-agent.md)将其更新。
* **Azure CLI**。 在计算机上[设置 Azure CLI](/cli/azure/install-azure-cli) 环境。
* wget 命令（如果尚无此命令，请运行 `sudo apt-get install wget`。
* 现有 Azure 订阅以及用于存储数据的现有常规用途存储帐户。  常规用途存储帐户支持必需的表存储。  Blob 存储帐户将不起作用。
* Python 2

### <a name="python-requirement"></a>Python 要求

Linux 诊断扩展需要 Python 2。 如果虚拟机使用的发行版默认情况下不包括 Python 2，则必须进行安装。 以下示例命令将在不同的发行版上安装 Python 2。    

 - Red Hat、CentOS、Oracle：`yum install -y python2`
 - Ubuntu、Debian：`apt-get install -y python2`
 - SUSE: `zypper install -y python2`

Python2 可执行文件必须将别名设置为“python”。 下面是可用来设置此别名的一种方法：

1. 运行以下命令以删除所有现有别名。
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. 运行以下命令以创建别名。

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

### <a name="sample-installation"></a>示例安装

> [!NOTE]
> 对于其中一个示例，在运行前，为第一部分中的变量填写正确的值。 

在这些示例中下载的示例配置将收集一组标准数据，并将其发送到表存储。 示例配置的 URL 及其内容可能会有所更改。 在大多数情况下，你应该下载门户设置 JSON 文件的副本并根据需要对其进行自定义，然后让你构造的任何模板或自动化都使用你自己版本的配置文件，而不是每次都下载该 URL。

> [!NOTE]
> 为了启用新的 Azure Monitor 接收器，Vm 需要为 MSI 身份验证令牌生成启用系统分配的标识。 此操作可在创建 VM 的过程中或创建 VM 之后完成。 通过门户、CLI、PowerShell 和资源管理器启用系统分配的标识的步骤。  [此处](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)详细列出。 

#### <a name="azure-cli-sample"></a>Azure CLI 示例

```azurecli
# Set your Azure VM diagnostic variables correctly below
my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
my_linux_vm=<your_azure_linux_vm_name>
my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Should login to Azure first before anything else
az login

# Select the subscription containing the storage account
az account set --subscription <your_azure_subscription_id>

# Enable System Assigned Identity to the existing VM
az vm identity assign -g $my_resource_group -n $my_linux_vm

# Download the sample Public settings. (You could also use curl or any web browser)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VM resource ID. Replace storage account name and resource ID in the public settings.
my_vm_resource_id=$(az vm show -g $my_resource_group -n $my_linux_vm --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vm_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token)
my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally tell Azure to install and enable the extension
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 4.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```
#### <a name="azure-cli-sample-for-installing-lad-40-extension-on-the-virtual-machine-scale-set-instance"></a>在虚拟机规模集实例上安装 LAD 4.0 扩展 Azure CLI 示例

```azurecli
#Set your Azure VMSS diagnostic variables correctly below
$my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
$my_linux_vmss=<your_azure_linux_vmss_name>
$my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Should login to Azure first before anything else
az login

# Select the subscription containing the storage account
az account set --subscription <your_azure_subscription_id>

# Enable System Assigned Identity to the existing VMSS
az vmss identity assign -g $my_resource_group -n $my_linux_vmss

# Download the sample Public settings. (You could also use curl or any web browser)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VMSS resource ID. Replace storage account name and resource ID in the public settings.
$my_vmss_resource_id=$(az vmss show -g $my_resource_group -n $my_linux_vmss --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vmss_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token)
$my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
$my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally tell Azure to install and enable the extension
az vmss extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 4.0 --resource-group $my_resource_group --vmss-name $my_linux_vmss --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

#### <a name="powershell-sample"></a>PowerShell 示例

```powershell
$storageAccountName = "yourStorageAccountName"
$storageAccountResourceGroup = "yourStorageAccountResourceGroupName"
$vmName = "yourVMName"
$VMresourceGroup = "yourVMResourceGroupName"

# Get the VM object
$vm = Get-AzVM -Name $vmName -ResourceGroupName $VMresourceGroup

# Enable System Assigned Identity on an existing VM
Update-AzVM -ResourceGroupName $VMresourceGroup -VM $vm -IdentityType SystemAssigned

# Get the public settings template from GitHub and update the templated values for storage account and resource ID
$publicSettings = (Invoke-WebRequest -Uri https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json).Content
$publicSettings = $publicSettings.Replace('__DIAGNOSTIC_STORAGE_ACCOUNT__', $storageAccountName)
$publicSettings = $publicSettings.Replace('__VM_RESOURCE_ID__', $vm.Id)

# If you have your own customized public settings, you can inline those rather than using the template above: $publicSettings = '{"ladCfg":  { ... },}'

# Generate a SAS token for the agent to use to authenticate with the storage account
$sasToken = New-AzStorageAccountSASToken -Service Blob,Table -ResourceType Service,Container,Object -Permission "racwdlup" -Context (Get-AzStorageAccount -ResourceGroupName $storageAccountResourceGroup -AccountName $storageAccountName).Context -ExpiryTime $([System.DateTime]::Now.AddYears(10))

# Build the protected settings (storage account SAS token)
$protectedSettings="{'storageAccountName': '$storageAccountName', 'storageAccountSasToken': '$sasToken'}"

# Finally install the extension with the settings built above
Set-AzVMExtension -ResourceGroupName $VMresourceGroup -VMName $vmName -Location $vm.Location -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 4.0 
```

### <a name="updating-the-extension-settings"></a>更新扩展设置

更改“受保护”或“公开”设置后，通过运行相同的命令将其部署到 VM。 如果设置中有任何变化，更新的设置将发送到扩展。 LAD 将重载配置并自行重启。

### <a name="migration-from-previous-versions-of-the-extension"></a>从以前版本的扩展迁移

此扩展的最新版本为 **4.0，目前为公共预览版**。 在 **2018 年7月31日之后弃用版本的2.x 时，仍支持早期版本的** 2.x。

> [!IMPORTANT]
> 若要从1.x 迁移到此新版本的扩展，必须卸载旧扩展，然后安装扩展 (的版本4和系统分配的标识的更新配置和用于将指标发送到 Azure Monitor 接收器的接收器。 ) 

建议：

* 启用自动次要版本升级后再安装扩展。
  * 在经典部署模型 Vm 上，如果要通过 Azure XPLAT CLI 或 PowerShell 安装扩展，请指定 "4. *" 作为版本。
  * 在 Azure 资源管理器部署模型 VM 上，在 VM 部署模板中加入“"autoUpgradeMinorVersion": true”。
* 可以使用 LAD 4.0 的相同存储帐户，与 LAD 3. * 一起使用。 

## <a name="protected-settings"></a>受保护的设置

此组配置信息不应公开的敏感信息，例如存储凭据。 扩展以加密形式传输和存储这些设置。

```json
{
    "storageAccountName" : "the storage account to receive data",
    "storageAccountEndPoint": "the hostname suffix for the cloud for this account",
    "storageAccountSasToken": "SAS access token",
    "mdsdHttpProxy": "HTTP proxy settings",
    "sinksConfig": { ... }
}
```

名称 | 值
---- | -----
storageAccountName | 扩展写入数据的存储帐户的名称。
storageAccountEndPoint | （可选）标识存储帐户所在云的终结点。 如果缺少此设置，则 LAD 默认为 Azure 公有云`https://core.windows.net`。 若要使用 Azure Germany、Azure 政府或 Azure China 中的存储帐户，请相应地设置此值。
storageAccountSasToken | Blob 服务和表服务的[帐户 SAS 令牌](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) (`ss='bt'`)，适用于容器和对象 (`srt='co'`)，用于授予添加、创建、列出、更新和写入权限 (`sp='acluw'`)。 请勿使用前导问号 (?)。
mdsdHttpProxy | （可选）允许扩展连接到指定存储帐户和终结点所需的 HTTP 代理信息。
sinksConfig | （可选）可将指标和事件传递到的替换目标的详细信息。 扩展所支持的每个数据接收器的具体详细信息将在下面各节中介绍。

若要在资源管理器模板中获取 SAS 令牌，请使用 **listAccountSas** 函数。 有关示例模板，请参阅 [List 函数示例](../../azure-resource-manager/templates/template-functions-resource.md#list-example)。

可通过 Azure 门户轻松构造所需的 SAS 令牌。

1. 选择想要扩展写入的通用存储帐户
1. 在左侧菜单的“设置”部分选择“共享访问签名”
1. 如上所述设置相应部分
1. 单击“生成 SAS”按钮。

:::image type="content" source="./media/diagnostics-linux/make_sas.png" alt-text="屏幕截图显示了带有“生成 SAS”的“共享访问签名”页。":::

将生成的 SAS 复制到 storageAccountSasToken 字段中；删除前导问号（“?”）。

### <a name="sinksconfig"></a>sinksConfig

```json
"sinksConfig": {
    "sink": [
        {
            "name": "sinkname",
            "type": "sinktype",
            ...
        },
        ...
    ]
},
```

此可选部分所定义的附加目标是扩展将所收集信息发送到其中的目标。 “sink”数组包含每个附加数据接收器的对象。 “type”属性确定对象中的其他属性。

元素 | 值
------- | -----
name | 在扩展配置中其他位置用于引用此接收器的字符串。
type | 要定义的接收器的类型。 确定此类型实例中的其他值（如果有）。

Linux 诊断扩展4.0 版支持两种接收器类型： EventHub 和 JsonBlob。

#### <a name="the-eventhub-sink"></a>EventHub 接收器

```json
"sink": [
    {
        "name": "sinkname",
        "type": "EventHub",
        "sasURL": "https SAS URL"
    },
    ...
]
```

“sasURL”条目包含应将数据发布到其中的事件中心的完整 URL，包括 SAS 令牌。 LAD 要求 SAS 命名启用发送声明的策略。 示例：

* 创建名为 `contosohub` 的事件中心命名空间
* 在名为 `syslogmsgs` 的命名空间中创建事件中心
* 在名为 `writer` 的事件中心创建启用发送声明的共享访问策略

如果创建在 UTC 2018 年 1 月 1 日午夜之前有效的 SAS，sasURL 值可能为：

```https
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

有关为事件中心生成和检索 SAS 令牌信息的详细信息，请参阅[此网页](/rest/api/eventhub/generate-sas-token#powershell)。

#### <a name="the-jsonblob-sink"></a>JsonBlob 接收器

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

定向到 JsonBlob 接收器的数据将存储在 Azure 存储中的 blob 内。 每个 LAD 实例每小时为每个接收器名称创建一个 blob。 每个 blob 总是包含一个语法上有效的 JSON 对象数组。 新条目以基元形式添加到数组。 blob 存储在与接收器同名的容器中。 用于 blob 容器名称的 Azure 存储规则也适用于 JsonBlob 接收器的名称：长度介于 3 到 63 个字符之间，可使用小写字母数字 ASCII 字符或短划线。

## <a name="public-settings"></a>公用设置

此结构包含多个设置块，这些块控制扩展收集的信息。 除 ladCfg) 之外的每个设置 (都是可选的。 如果在中指定指标或 syslog 集合 `ladCfg` ，则还必须指定 `StorageAccount` 。 需要指定 sinksConfig 元素才能为 LAD 4.0 中的指标启用 Azure Monitor 接收器

```json
{
    "ladCfg":  { ... },
    "fileLogs": { ... },
    "StorageAccount": "the storage account to receive data",
    "sinksConfig": { ... },
    "mdsdHttpProxy" : ""
}
```

元素 | 值
------- | -----
StorageAccount | 扩展写入数据的存储帐户的名称。 必须与[受保护的设置](#protected-settings)中指定的名称相同。
mdsdHttpProxy | （可选）与[受保护的设置](#protected-settings)中的相同。 如果设置，则专用值将重写公用值。 将包含机密（如密码）的代理设置放在[受保护的设置](#protected-settings)中。

将在下面各节中详细介绍剩余的元素。

### <a name="ladcfg"></a>ladCfg

```json
"ladCfg": {
    "diagnosticMonitorConfiguration": {
        "eventVolume": "Medium",
        "metrics": { ... },
        "performanceCounters": { ... },
        "syslogEvents": { ... }
    },
    "sampleRateInSeconds": 15
}
```

此结构控制指标和日志的收集，用于传递到 Azure 指标服务和其他数据接收器。 必须指定 `performanceCounters` 和/或 `syslogEvents`。 必须指定 `metrics` 结构。

如果你不想启用 syslog 或指标收集，则可以只为 ladCfg 元素指定一个空的结构，如下所示- 

```json
"ladCfg": {
    "diagnosticMonitorConfiguration": {}
    }
```

元素 | 值
------- | -----
eventVolume | （可选）控制在存储表中创建的分区数。 必须是 `"Large"`、`"Medium"` 或 `"Small"`。 如果未指定，默认值为 `"Medium"`。
sampleRateInSeconds | （可选）两次收集原始（未聚合）指标之间的默认时间间隔。 支持的最小采样率为 15 秒。 如果未指定，默认值为 `15`。

#### <a name="metrics"></a>指标

```json
"metrics": {
    "resourceId": "/subscriptions/...",
    "metricAggregation" : [
        { "scheduledTransferPeriod" : "PT1H" },
        { "scheduledTransferPeriod" : "PT5M" }
    ]
}
```

元素 | 值
------- | -----
ResourceId | VM 或 VM 所属虚拟机规模集的 Azure 资源管理器资源 ID。 如果配置中使用了任何 JsonBlob 接收器，也必须指定此设置。
scheduledTransferPeriod | 计算聚合指标并将转移到 Azure Metrics 的频率，以 IS 8601 时间间隔形式表示。 最小传输周期为 60 秒，即 PT1M。 必须指定至少一个 scheduledTransferPeriod。

performanceCounters 节中指定的指标样本每 15 秒收集一次，或者按计数器明确定义的采样率进行收集。 如果出现多个 scheduledTransferPeriod 频率（如示例所示），则每个聚合都将独立计算。

#### <a name="performancecounters"></a>performanceCounters

```json
"performanceCounters": {
    "sinks": "",
    "performanceCounterConfiguration": [
        {
            "type": "builtin",
            "class": "Processor",
            "counter": "PercentIdleTime",
            "counterSpecifier": "/builtin/Processor/PercentIdleTime",
            "condition": "IsAggregate=TRUE",
            "sampleRate": "PT15S",
            "unit": "Percent",
            "annotation": [
                {
                    "displayName" : "Aggregate CPU %idle time",
                    "locale" : "en-us"
                }
            ]
        }
    ]
}
```

此可选部分控制指标的收集。 每个 [scheduledTransferPeriod](#metrics) 的原始样本聚合产生以下值：

* 平均值
* 最小值
* 最大值
* 上一次收集的值
* 用于计算聚合的原始样本数

元素 | 值
------- | -----
sinks | （可选）LAD 将聚合指标结果发送到的接收器的名称的逗号分隔列表。 所有聚合指标都将发布到列出的每个接收器。 请参阅 [sinksConfig](#sinksconfig)。 示例：`"EHsink1, myjsonsink"`。
type | 标识指标的实际提供程序。
class | 与“counter”一起标识提供程序的命名空间中的特定指标。
counter | 与“class”一起标识提供程序的命名空间中的特定指标。
counterSpecifier | 标识 Azure Metrics 命名空间中的特定指标。
condition | （可选）选择指标适用对象的特定实例，或选择该对象所有实例的聚合。 有关详细信息，请参阅 `builtin` 指标定义。
sampleRate | IS 8601 时间间隔，用于设置收集此指标原始样本的速率。 如果未设置，则收集时间间隔由 [sampleRateInSeconds](#ladcfg) 的值设置。 支持的最短采样率为 15 秒 (PT15S)。
unit | 应为以下字符串之一：“Count”、“Bytes”、“Seconds”、“Percent”、“CountPerSecond”、“BytesPerSecond”、“Millisecond”。 定义指标的单位。 所收集数据的使用者会预期收集到的数据值与此单位匹配。 LAD 会忽略此字段。
displayName | Azure Metrics 中要附加到此数据的标签（使用由相关区域设置指定的语言）。 LAD 会忽略此字段。

counterSpecifier 是一个任意标识符。 Azure 门户绘图和警报功能等指标使用者使用 counterSpecifier 作为标识指标或指标实例的“关键字”。 对于 `builtin` 指标，建议使用以 `/builtin/` 开头的 counterSpecifier 值。 如果要收集指标的特定实例，建议将该实例的标识符附加到 counterSpecifier 值。 下面是一些示例：

* `/builtin/Processor/PercentIdleTime` - 所有 vCPU 的平均空闲时间
* `/builtin/Disk/FreeSpace(/mnt)` - /mnt 文件系统的可用空间
* `/builtin/Disk/FreeSpace` - 已装入的所有文件系统的平均可用空间

LAD 和 Azure 门户都不需要 counterSpecifier 值匹配任何模式。 请以相同的模式构造各个 counterSpecifier 值。

指定 `performanceCounters` 时，LAD 始终将数据写入 Azure 存储中的表。 可将相同的数据写入 JSON blob 和/或事件中心，但不能禁止将数据存储到表中。 配置为使用相同存储帐户名称和终结点的所诊断扩展有实例，其指标和日志会添加到同一个表中。 如果有过多 VM 写入同一表分区，则 Azure 可能限制写入该分区。 eventVolume 设置会使条目分散在 1 个 (Small)、10 个 (Medium) 或 100 个 (Large) 分区中。 通常，“Medium”足以确保流量不被限制。 Azure 门户的 Azure Metrics 功能使用此表中的数据来生成图形或触发警报。 表名是这些字符串的串联：

* `WADMetrics`
* 表中存储的聚合值的“scheduledTransferPeriod”
* `P10DV2S`
* 日期格式为“YYYYMMDD”，每 10 天更改一次

示例包括 `WADMetricsPT1HP10DV2S20170410` 和 `WADMetricsPT1MP10DV2S20170609`。

#### <a name="syslogevents"></a>syslogEvents

```json
"syslogEvents": {
    "sinks": "",
    "syslogEventConfiguration": {
        "facilityName1": "minSeverity",
        "facilityName2": "minSeverity",
        ...
    }
}
```

此可选部分控制 syslog 中日志事件的收集。 如果省略此部分，则不会捕获 syslog 事件。

syslogEventConfiguration 收集会为相关的每个 syslog 辅助参数创建一个条目。 如果特定辅助参数的 minSeverity 为“NONE”，或者该辅助参数并未出现在元素中，则不会捕获该辅助参数下的任何事件。

元素 | 值
------- | -----
sinks | 一个逗号分隔列表，包含要将单个日志事件发布到其中的接收器的名称。 与 syslogEventConfiguration 中的限制匹配的所有日志事件都会发布到列出的每个接收器。 示例：“EHforsyslog”
facilityName | Syslog 辅助参数名称（例如“LOG\_USER”或“LOG\_LOCAL0”）。 有关完整列表，请参阅 [syslog 手册页](http://man7.org/linux/man-pages/man3/syslog.3.html)的“facility”部分。
minSeverity | Syslog 严重性级别（例如“LOG\_ERR”或“LOG\_INFO”）。 有关完整列表，请参阅 [syslog手册页](http://man7.org/linux/man-pages/man3/syslog.3.html)的“level”部分。 扩展将捕获发送到该辅助参数的等于或高于指定级别的事件。

指定 `syslogEvents` 时，LAD 始终将数据写入 Azure 存储中的表。 可将相同的数据写入 JSON blob 和/或事件中心，但不能禁止将数据存储到表中。 此表的分区行为与 `performanceCounters` 的描述相同。 表名是这些字符串的串联：

* `LinuxSyslog`
* 日期格式为“YYYYMMDD”，每 10 天更改一次

示例包括 `LinuxSyslog20170410` 和 `LinuxSyslog20170609`。

### <a name="sinksconfig"></a>sinksConfig

此可选部分用于除了存储帐户和默认来宾度量值边栏选项卡外，还允许将指标发送到 Azure Monitor 接收器。

> [!NOTE]
> 这要求在 Vm/VMSS 上启用系统分配的标识。 可以通过门户、CLI、PowerShell 和 resource manager 完成此操作。 [此处](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)详细列出了步骤。 若要启用此操作，还会在上面的 AZ CLI 安装示例中列出。 

```json
  "sinksConfig": {
    "sink": [
      {
        "name": "AzMonSink",
        "type": "AzMonSink",
        "AzureMonitor": {}
      }
    ]
  },
```


### <a name="filelogs"></a>fileLogs

控制日志文件的捕获。 LAD 在文件中写入新的文本行时捕获这些行，并将其写入表行和/或任何指定的接收器（JsonBlob 或 EventHub）。

> [!NOTE]
> fileLogs 是由名为 `omsagent` 的 LAD 的子组件捕获的。 若要收集 fileLogs，必须确保 `omsagent` 用户对指定的文件具有读取权限，并对该文件的路径中的所有目录具有执行权限。 可以通过在安装 LAD 后运行 `sudo su omsagent -c 'cat /path/to/file'` 来检查此情况。

```json
"fileLogs": [
    {
        "file": "/var/log/mydaemonlog",
        "table": "MyDaemonEvents",
        "sinks": ""
    }
]
```

元素 | 值
------- | -----
file | 要监视和捕获的日志文件的完整路径名。 路径名必须命名单个文件；它不能命名目录，也不能包含通配符。 “Omsagent”用户帐户必须具有文件路径的读取访问权限。
表 | （可选）指定的存储帐户（在受保护的配置中指定）中的 Azure 存储表，文件“结尾”处的新行将写入此表。
sinks | （可选）日志行发送到的附加接收器的名称的逗号分隔列表。

必须指定“表”和/或“接收器”。

## <a name="metrics-supported-by-the-builtin-provider"></a>内置提供程序支持的指标

> [!NOTE]
> LAD 支持的默认指标在所有文件系统/磁盘/名称上聚合。 有关非聚合指标，请参阅新的 Azure Monitor 接收器指标支持。

内置指标提供程序可提供大量用户最感兴趣的指标。 这些指标分为五个大类：

* 处理器
* 内存
* 网络
* Filesystem
* 磁盘

### <a name="builtin-metrics-for-the-processor-class"></a>处理器类的内置指标

处理器类指标提供有关 VM 中处理器使用情况的信息。 聚合百分比时，结果是所有 CPU 的平均值。 在双 vCPU VM 中，如果一个 vCPU 100% 忙，另一个 vCPU 100% 空闲，则报告的 PercentIdleTime 将是 50。 如果在相同时段内每个 vCPU 都是 50% 忙，则报告的结果也将是 50。 在四 vCPU VM 中，如果一个 vCPU 100% 忙，其他 vCPU 空闲，则报告的 PercentIdleTime 将是 75。

counter | 含义
------- | -------
PercentIdleTime | 聚合窗口期内，处理器执行内核空闲循环的时间所占百分比
PercentProcessorTime | 执行非空闲线程的时间所占百分比
PercentIOWaitTime | 等待 IO 操作完成的时间所占百分比
PercentInterruptTime | 执行硬件/软件中断和 DPC（延迟过程调用）的时间所占百分比
PercentUserTime | 聚合窗口期非空闲时间内，更常处于常规优先级的用户所花费的时间所占百分比
PercentNiceTime | 非空闲时间内，处于降低（良好）优先级的时间所占百分比
PercentPrivilegedTime | 非空闲时间内，处于特权（内核）模式的时间所占百分比

前四个计数器结果之和应为 100%。 后三个计数器结果之和也应为 100%，它们对 PercentProcessorTime、PercentIOWaitTime 和 PercentInterruptTime 之和（非空闲时间）进行了细分。

### <a name="builtin-metrics-for-the-memory-class"></a>内存类的内置指标

内存类指标提供有关内存使用率、分页和交换的信息。

counter | 含义
------- | -------
AvailableMemory | 可用物理内存 (MB)
PercentAvailableMemory | 可用物理内存占内存总量的百分比
UsedMemory | 已用物理内存 (MB)
PercentUsedMemory | 已用物理内存占内存总量的百分比
PagesPerSec | 总分页（读取/写入）
PagesReadPerSec | 从后备存储（交换文件、程序文件、映射文件等）读取的页面数
PagesWrittenPerSec | 写入后备存储（交换文件、映射文件等）的页面数
AvailableSwap | 未使用的交换空间 (MB)
PercentAvailableSwap | 未使用的交换空间占交换空间总量的百分比
UsedSwap | 已使用的交换空间 (MB)
PercentUsedSwap | 已使用的交换空间占交换空间总量的百分比

此类指标仅有一个实例。 “condition”属性没有有用的设置，应当省略。

### <a name="builtin-metrics-for-the-network-class"></a>网络类的内置指标

网络类指标提供有关自启动以来各个网络接口上网络活动的信息。 LAD 不会公开带宽指标，该指标可从主机指标中检索。

counter | 含义
------- | -------
BytesTransmitted | 自启动以来发送的字节数总计
BytesReceived | 自启动以来接收的字节数总计
BytesTotal | 自启动以来发送或接收的字节数总计
PacketsTransmitted | 自启动以来发送的包数总计
PacketsReceived | 自启动以来接收的包数总计
TotalRxErrors | 自启动以来接收的错误数
TotalTxErrors | 自启动以来发送的错误数
TotalCollisions | 自启动以来网络端口报告的冲突数

### <a name="builtin-metrics-for-the-filesystem-class"></a>文件系统类的内置指标

文件系统类指标提供有关文件系统使用情况的信息。 将报告绝对值和百分比值，因为这些指标将向普通用户（而不是 root 用户）显示。

counter | 含义
------- | -------
FreeSpace | 可用磁盘空间（字节）
UsedSpace | 已用磁盘空间（字节）
PercentFreeSpace | 可用空间百分比
PercentUsedSpace | 已用空间百分比
PercentFreeInodes | 未使用的 Inode 的百分比
PercentUsedInodes | 所有文件系统中已分配的（使用中）inode 的百分比之和
BytesReadPerSecond | 每秒读取的字节数
BytesWrittenPerSecond | 每秒写入的字节数
每秒字节数 | 每秒读取或写入的字节数
ReadsPerSecond | 每秒读取操作数
WritesPerSecond | 每秒写入操作数
TransfersPerSecond | 每秒读取或写入操作数

### <a name="builtin-metrics-for-the-disk-class"></a>磁盘类的内置指标

磁盘类指标提供有关磁盘设备使用情况的信息。 这些统计信息适用于整个驱动器。 如果设备上有多个文件系统，则针对该设备的计数器将有效地跨所有文件系统聚合。

counter | 含义
------- | -------
ReadsPerSecond | 每秒读取操作数
WritesPerSecond | 每秒写入操作数
TransfersPerSecond | 每秒总操作数
AverageReadTime | 每个读取操作的平均秒数
AverageWriteTime | 每个写入操作的平均秒数
AverageTransferTime | 每个操作的平均秒数
AverageDiskQueueLength | 队列中磁盘操作的平均数
ReadBytesPerSecond | 每秒读取的字节数
WriteBytesPerSecond | 每秒写入的字节数
每秒字节数 | 每秒读取或写入的字节数

## <a name="installing-and-configuring-lad-40"></a>安装和配置 LAD 4。0

### <a name="azure-cli"></a>Azure CLI

假设受保护的设置位于 ProtectedSettings.json 文件中，而公用配置信息位于 PublicSettings.json 中，请运行以下命令：

```azurecli
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 4.0 --resource-group <resource_group_name> --vm-name <vm_name> --protected-settings ProtectedSettings.json --settings PublicSettings.json
```

该命令假定你使用 Azure CLI 的 Azure 资源管理模式。 若要为经典部署模型 (ASM) VM 配置 LAD，请切换到“asm”模式 (`azure config mode asm`)，并在命令中省略资源组名称。 有关详细信息，请参阅[跨平台 CLI 文档](/cli/azure/authenticate-azure-cli)。

### <a name="powershell"></a>PowerShell

假设受保护的设置位于 `$protectedSettings` 变量中，而公共配置信息位于 `$publicSettings` 变量中，请运行以下命令：

```powershell
Set-AzVMExtension -ResourceGroupName <resource_group_name> -VMName <vm_name> -Location <vm_location> -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 4.0
```

## <a name="an-example-lad-40-configuration"></a>LAD 4.0 配置示例

基于前面的定义，下面是一个示例 LAD 4.0 扩展配置，其中包含一些说明。 要将此示例应用于具体情况，应使用自己的存储帐户名称、帐户 SAS 令牌和 EventHubs SAS 令牌。

> [!NOTE]
> 提供公共和受保护设置的方法将有所不同，具体取决于是否使用 Azure CLI 或 PowerShell 安装 LAD。 如果使用 Azure CLI，请将以下设置保存到 ProtectedSettings.json 和 PublicSettings.json，以与上面的示例命令配合使用。 如果使用 PowerShell，请通过运行 `$protectedSettings = '{ ... }'` 将设置保存到 `$protectedSettings` 和 `$publicSettings`。

### <a name="protected-settings"></a>受保护的设置

这些受保护的设置配置：

* 存储帐户
* 匹配的帐户 SAS 令牌
* 几个接收器（有 SAS 令牌的 JsonBlob 或 EventHubs）

```json
{
  "storageAccountName": "yourdiagstgacct",
  "storageAccountSasToken": "sv=xxxx-xx-xx&ss=bt&srt=co&sp=wlacu&st=yyyy-yy-yyT21%3A22%3A00Z&se=zzzz-zz-zzT21%3A22%3A00Z&sig=fake_signature",
  "sinksConfig": {
    "sink": [
      {
        "name": "SyslogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "FilelogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "MyJsonMetricsBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=fake_signature&se=1808096361&skn=yourehpolicy"
      },
      {
        "name": "MyMetricEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&skn=yourehpolicy"
      },
      {
        "name": "LoggingEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&se=1808096361&skn=yourehpolicy"
      }
    ]
  }
}
```

### <a name="public-settings"></a>公共设置

这些公用设置将使 LAD 执行以下操作：

* 将 percent-processor-time 和 used-disk-space 指标上传到 `WADMetrics*` 表
* 将 syslog 辅助参数“user”和严重性“info”之下的消息上传到 `LinuxSyslog*` 表
* 将文件 `/var/log/myladtestlog` 中的追加行上传到 `MyLadTestLog` 表

无论如何，数据都还会上传到以下位置：

* Azure Blob 存储（容器名称在 JsonBlob 接收器中定义）
* EventHubs 终结点（在 EventHubs 接收器中指定）

```json
{
  "StorageAccount": "yourdiagstgacct",
  "ladCfg": {
    "sampleRateInSeconds": 15,
    "diagnosticMonitorConfiguration": {
      "performanceCounters": {
        "sinks": "MyMetricEventHub,MyJsonMetricsBlob",
        "performanceCounterConfiguration": [
          {
            "unit": "Percent",
            "type": "builtin",
            "counter": "PercentProcessorTime",
            "counterSpecifier": "/builtin/Processor/PercentProcessorTime",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Aggregate CPU %utilization"
              }
            ],
            "condition": "IsAggregate=TRUE",
            "class": "Processor"
          },
          {
            "unit": "Bytes",
            "type": "builtin",
            "counter": "UsedSpace",
            "counterSpecifier": "/builtin/FileSystem/UsedSpace",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Used disk space on /"
              }
            ],
            "condition": "Name=\"/\"",
            "class": "Filesystem"
          }
        ]
      },
      "metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1H"
          },
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "/subscriptions/your_azure_subscription_id/resourceGroups/your_resource_group_name/providers/Microsoft.Compute/virtualMachines/your_vm_name"
      },
      "eventVolume": "Large",
      "syslogEvents": {
        "sinks": "SyslogJsonBlob,LoggingEventHub",
        "syslogEventConfiguration": {
          "LOG_USER": "LOG_INFO"
        }
      }
    }
  },
  "sinksConfig": {
    "sink": [
      {
        "name": "AzMonSink",
        "type": "AzMonSink",
        "AzureMonitor": {}
      }
    ]
  },
  "fileLogs": [
    {
      "file": "/var/log/myladtestlog",
      "table": "MyLadTestLog",
      "sinks": "FilelogJsonBlob,LoggingEventHub"
    }
  ]
}
```

配置中的 `resourceId` 必须与 VM 或虚拟机规模集中的相匹配。

* Azure 平台指标绘图和警报了解你所用 VM 的 resourceId。 它会预期能够使用 resourceId 查找关键字找到该 VM 的数据。
* 如果使用 Azure 自动缩放，则自动缩放配置中的 resourceId 必须与 LAD 使用的 resourceId 相匹配。
* LAD 所编写的 JsonBlob 名称包含 resourceId。

## <a name="view-your-data"></a>查看数据

使用 Azure 门户查看性能数据或设置警报：

:::image type="content" source="./media/diagnostics-linux/graph_metrics.png" alt-text="屏幕截图显示了 Azure 门户，其中包含已选择的“已用磁盘空间”指标和生成的图表。":::

`performanceCounters` 数据始终存储在 Azure 存储表中。 Azure 存储 API 适用于多种语言和平台。

发送到 JsonBlob 接收器的数据存储在[受保护的设置](#protected-settings)中指定的存储帐户中的 blob 内。 可使用任意 Azure Blob 存储 API 使用这些 blob 数据。

此外，可使用这些 UI 工具来访问 Azure 存储中的数据：

* Visual Studio 服务器资源管理器。
* [屏幕截图显示了 Azure 存储资源管理器中的容器和表。](https://azurestorageexplorer.codeplex.com/ "Azure 存储资源管理器")

这是 Microsoft Azure 存储资源管理器会话的快照，它显示了测试 VM 上正确配置的 LAD 3.0 扩展生成的 Azure 存储表和容器。 此图与[示例 LAD 3.0 配置](#an-example-lad-40-configuration)不完全匹配。

:::image type="content" source="./media/diagnostics-linux/stg_explorer.png" alt-text="屏幕截图显示 Azure 存储资源管理器。":::

请参阅相关 [EventHubs 文档](../../event-hubs/event-hubs-about.md)，了解如何使用发布到 EventHubs 终结点的消息。

## <a name="next-steps"></a>后续步骤

* 在 [Azure Monitor](../../azure-monitor/platform/alerts-classic-portal.md) 中为收集的指标创建指标警报。
* 为指标创建[监控图表](../../azure-monitor/platform/data-platform.md)。
* 了解如何使用指标[创建虚拟机规模集](../linux/tutorial-create-vmss.md)以控制自动缩放。