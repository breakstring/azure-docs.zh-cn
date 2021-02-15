---
title: 通过模板在 Azure Stack Edge Pro 设备上部署 Vm
description: 介绍如何使用模板在 Azure Stack Edge Pro 设备上创建和管理 (Vm) 的虚拟机。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/25/2021
ms.author: alkohli
ms.openlocfilehash: 66d537b79819aecab4ce88a56ed465679363f421
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "98805206"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-templates"></a>通过模板在 Azure Stack Edge Pro GPU 设备上部署 Vm

本教程介绍如何使用模板在 Azure Stack Edge Pro 设备上创建和管理 VM。 这些模板 JavaScript 对象表示法 (JSON) 文件，用于定义 VM 的基础结构和配置。 在这些模板中，可以指定要部署的资源以及这些资源的属性。

模板在不同环境中是灵活的，因为它们可以在运行时从文件中获取参数作为输入。 标准命名结构 `TemplateName.json` 用于模板和 `TemplateName.parameters.json` 参数文件。 有关 ARM 模板的详细信息，请参阅 [什么是 Azure 资源管理器模板？](../azure-resource-manager/templates/overview.md)。

在本教程中，我们将使用预编写的示例模板来创建资源。 无需编辑模板文件，只需修改 `.parameters.json` 文件即可自定义计算机的部署。 

## <a name="vm-deployment-workflow"></a>VM 部署工作流

若要跨多个设备部署 Azure Stack Edge Pro Vm，你可以将单个经过系统准备 VHD 用于整个汽油，为部署使用相同的模板，并且只需对每个部署位置的模板的参数做少许更改 (这些更改可能在此处进行手动操作，或以编程方式进行。 )  

使用模板的部署工作流的高级摘要如下：

1. **配置先决条件** -对于 VM，有三种类型的先决条件：设备、客户端和。

    1. **设备先决条件**

        1. 连接到设备上的 Azure 资源管理器。
        2. 通过本地 UI 启用计算。

    2. **客户端先决条件**

        1. 下载 VM 模板和客户端上的关联文件。
        1. （可选）在客户端上配置 TLS 1.2。
        1. [下载 Microsoft Azure 存储资源管理器，并将其安装](https://go.microsoft.com/fwlink/?LinkId=708343&clcid=0x409) 在客户端上。

    3. **VM 先决条件**

        1. 在设备位置中创建将包含所有 VM 资源的资源组。
        1. 创建存储帐户以上传用于创建 VM 映像的 VHD。
        1. 将本地存储帐户 URI 添加到访问设备的客户端上的 DNS 或主机文件中。
        1. 在设备上和访问设备的本地客户端上安装 blob 存储证书。 （可选）将 blob 存储证书安装在存储资源管理器上。
        1. 创建 VHD 并将其上传到之前创建的存储帐户。

2. **从模板创建 VM**

    1. 使用 `CreateImage.parameters.json` 参数文件和部署模板创建 VM 映像 `CreateImage.json` 。
    1. 使用 `CreateVM.parameters.json` 参数文件和部署模板创建包含以前创建的资源的 VM  `CreateVM.json` 。

## <a name="device-prerequisites"></a>设备先决条件

在 Azure Stack Edge Pro 设备上配置这些先决条件。

[!INCLUDE [azure-stack-edge-gateway-deploy-virtual-machine-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]

## <a name="client-prerequisites"></a>客户端先决条件

在客户端上配置将用于访问 Azure Stack Edge Pro 设备的先决条件。

1. 如果你使用它来上传 VHD，请[下载存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)。 或者，你可以下载 AzCopy 来上传 VHD。 如果运行的是较旧版本的 AzCopy，则可能需要在客户端计算机上配置 TLS 1.2。 
1. 将[VM 模板和参数文件下载](https://aka.ms/ase-vm-templates)到客户端计算机。 将它解压缩到将用作工作目录的目录中。


## <a name="vm-prerequisites"></a>VM 先决条件

配置这些先决条件以创建创建 VM 所需的资源。 

    
### <a name="create-a-resource-group"></a>创建资源组

使用 [New-AzureRmResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 创建 Azure 资源组。 资源组是在其中部署和管理 Azure 资源（例如存储帐户、磁盘、托管磁盘）的逻辑容器。

> [!IMPORTANT]
> 所有资源都在与设备相同的位置创建，并且位置设置为 **DBELocal**。

```powershell
New-AzureRmResourceGroup -Name <Resource group name> -Location DBELocal
```

下面显示了示例输出。

```powershell
PS C:\windows\system32> New-AzureRmResourceGroup -Name myasegpurgvm -Location DBELocal

ResourceGroupName : myasegpurgvm
Location          : dbelocal
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/DDF9FC44-E990-42F8-9A91-5A6A5CC472DB/resourceGroups/myasegpurgvm

PS C:\windows\system32>
```

### <a name="create-a-storage-account"></a>创建存储帐户

使用在上一步中创建的资源组创建新的存储帐户。 此帐户是一个 **本地存储帐户** ，将用于上载 VM 的虚拟磁盘映像。

```powershell
New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Resource group name> -Location DBELocal -SkuName Standard_LRS
```

> [!NOTE]
> 只能通过 Azure 资源管理器创建本地存储帐户，如本地冗余存储 (Standard_LRS 或 Premium_LRS) 。 若要创建分层存储帐户，请参阅 [添加、连接到 Azure Stack Edge Pro 上的存储帐户](azure-stack-edge-j-series-deploy-add-storage-accounts.md)中的步骤。

下面显示了示例输出。

```powershell
PS C:\windows\system32> New-AzureRmStorageAccount -Name myasegpusavm -ResourceGroupName myasegpurgvm -Location DBELocal -SkuName Standard_LRS

StorageAccountName ResourceGroupName Location SkuName     Kind    AccessTier CreationTime
------------------ ----------------- -------- -------     ----    ---------- ------------
myasegpusavm       myasegpurgvm      DBELocal StandardLRS Storage            7/29/2020 10:11:16 PM

PS C:\windows\system32>
```

若要获取存储帐户密钥，请运行 `Get-AzureRmStorageAccountKey` 命令。 此处显示了此命令的示例输出。

```powershell
PS C:\windows\system32> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: myasegpurgvm
Name: myasegpusavm

KeyName    Value                                                  Permissions   
-------     -----                                                   --
key1 GsCm7QriXurqfqx211oKdfQ1C9Hyu5ZutP6Xl0dqlNNhxLxDesDej591M8y7ykSPN4fY9vmVpgc4ftkwAO7KQ== 11 
key2 7vnVMJUwJXlxkXXOyVO4NfqbW5e/5hZ+VOs+C/h/ReeoszeV+qoyuBitgnWjiDPNdH4+lSm1/ZjvoBWsQ1klqQ== ll
```

### <a name="add-blob-uri-to-hosts-file"></a>将 blob URI 添加到 hosts 文件

请确保已在用于连接到 Blob 存储的客户端的 hosts 文件中添加 blob URI。 以 **管理员身份运行记事本**，并为中的 blob URI 添加以下条目 `C:\windows\system32\drivers\etc\hosts` ：

`<Device IP> <storage account name>.blob.<Device name>.<DNS domain>`

在典型的环境中，你将配置 DNS，以便所有存储帐户都指向 Azure Stack Edge Pro 设备，其中包含一个 `*.blob.devicename.domainname.com` 条目。

### <a name="optional-install-certificates"></a> (可选) 安装证书

如果你将通过存储资源管理器使用 *http* 进行连接，请跳过此步骤。 如果使用的是 *https*，则需要在存储资源管理器中安装适当的证书。 在这种情况下，请安装 blob 端点证书。 有关详细信息，请参阅如何在 " [管理证书](azure-stack-edge-j-series-manage-certificates.md)" 中创建和上载证书。 

### <a name="create-and-upload-a-vhd"></a>创建并上传 VHD

请确保你有一个可用于在后面的步骤中上传的虚拟磁盘映像。 按照 [创建 VM 映像](azure-stack-edge-gpu-create-virtual-machine-image.md)中的步骤操作。 

将所有要使用的磁盘映像复制到在前面步骤中创建的本地存储帐户中的页 blob 中。 你可以使用 [存储资源管理器](https://azure.microsoft.com/features/storage-explorer/) 或 AzCopy 之类的工具将 [VHD 上传到](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#upload-a-vhd) 你在前面步骤中创建的存储帐户。 

### <a name="use-storage-explorer-for-upload"></a>使用存储资源管理器进行上传

1. 打开存储资源管理器。 请参阅 **编辑** 并确保将应用程序设置为 **目标 Azure Stack api**。

    ![将目标设置为 Azure Stack Api](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/set-target-apis-1.png)

2. 以 PEM 格式安装客户端证书。 **> 导入证书，请参阅编辑 > SSL 证书**。 指向客户端证书。

    ![导入 blob 存储终结点证书](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/import-blob-storage-endpoint-certificate-1.png)

    - 如果你使用的是设备生成的证书，请将 blob 存储终结点证书下载并转换 `.cer` 为 `.pem` 格式。 运行以下命令。 
    
        ```powershell
        PS C:\windows\system32> Certutil -encode 'C:\myasegpu1_Blob storage (1).cer' .\blobstoragecert.pem
        Input Length = 1380
        Output Length = 1954
        CertUtil: -encode command completed successfully.
        ```
    - 如果要引入自己的证书，请使用格式的签名链根证书 `.pem` 。

3. 导入证书后，重新启动存储资源管理器以使更改生效。

    ![重启存储资源管理器](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/restart-storage-explorer-1.png)

4. 在左窗格中，右键单击 " **存储帐户** " 并选择 " **连接到 Azure 存储**"。 

    ![连接到 Azure 存储1](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-azure-storage-1.png)

5. 选择“使用存储帐户名称和密钥”。 选择“下一步”  。

    ![连接到 Azure 存储2](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-azure-storage-2.png)

6. 在 " **连接方式" 和 "名称" 和 "密钥**" 中，提供 **显示名称**、 **存储帐户名称** 和 Azure 存储 **帐户密钥**。 选择 " **其他** 存储域"，然后提供 `<device name>.<DNS domain>` 连接字符串。 如果未在存储资源管理器中安装证书，请选中 " **使用 HTTP** " 选项。 选择“下一步”  。

    ![用名称和密钥连接](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-name-key-1.png)

7. 查看 **连接摘要** ，然后选择 " **连接**"。

8. 存储帐户将显示在左窗格中。 选择并展开存储帐户。 选择 " **Blob 容器**"，右键单击，然后选择 " **创建 blob 容器**"。 提供 blob 容器的名称。

9. 选择刚创建的容器，并在右窗格中选择 " **上传 > 上传文件**"。 

    ![上载 VHD 文件1](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-1.png)

10. 在 **选定的文件** 中浏览并指向要上传的 VHD。 选择 " **blob 类型** " 作为 **页面 blob** ，然后选择 " **上传**"。

    ![上载 VHD 文件2](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-2.png)

11. 将 VHD 加载到 blob 容器后，选择 VHD，右键单击，然后选择 " **属性**"。 

    ![上载 VHD 文件3](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-3.png)

12. 复制并保存将在后面的步骤中使用的 **Uri**。

    ![复制 URI](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/copy-uri-1.png)


## <a name="create-image-for-your-vm"></a>为 VM 创建映像

若要为 VM 创建映像，请编辑 `CreateImage.parameters.json` 参数文件，然后部署 `CreateImage.json` 使用此参数文件的模板。


### <a name="edit-parameters-file"></a>编辑参数文件

文件 `CreateImage.parameters.json` 采用以下参数： 

```json
"parameters": {
        "osType": {
              "value": "<Operating system corresponding to the VHD you upload can be Windows or Linux>"
        },
        "imageName": {
            "value": "<Name for the VM image>"
        },
        "imageUri": {
              "value": "<Path to the VHD that you uploaded in the Storage account>"
        },
    }
```

编辑文件 `CreateImage.parameters.json` ，为 Azure Stack Edge Pro 设备包括以下值：

1. 提供对应于要上传的 VHD 的操作系统类型。 OS 类型可以是 Windows 或 Linux。

    ```json
    "parameters": {
            "osType": {
              "value": "Windows"
            },
    ```

2. 将映像 URI 更改为在前面的步骤中上传的映像的 URI：

   ```json
   "imageUri": {
       "value": "https://myasegpusavm.blob.myasegpu1.wdshcsso.com/windows/WindowsServer2016Datacenter.vhd"
       },
   ```

   如果在存储资源管理器中使用 *http* ，请将 URI 更改为 *http* uri。

3. 提供唯一的映像名称。 在后续步骤中，此映像用于创建 VM。 

   下面是本文中使用的示例 json。

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
      "parameters": {
        "osType": {
          "value": "Linux"
        },
        "imageName": {
          "value": "myaselinuximg"
        },
        "imageUri": {
          "value": "https://sa2.blob.myasegpuvm.wdshcsso.com/con1/ubuntu18.04waagent.vhd"
        }
      }
    }
    ```

5. 保存参数文件。


### <a name="deploy-template"></a>部署模板 

部署模板 `CreateImage.json` 。 此模板部署将用于在后面的步骤中创建 Vm 的映像资源。

> [!NOTE]
> 当你在遇到身份验证错误时部署模板时，此会话的 Azure 凭据可能已过期。 重新运行该 `login-AzureRM` 命令，再次连接到 Azure Stack Edge Pro 设备上的 Azure 资源管理器。

1. 运行以下命令： 
    
    ```powershell
    $templateFile = "Path to CreateImage.json"
    $templateParameterFile = "Path to CreateImage.parameters.json"
    $RGName = "<Name of your resource group>"
    New-AzureRmResourceGroupDeployment `
        -ResourceGroupName $RGName `
        -TemplateFile $templateFile `
        -TemplateParameterFile $templateParameterFile `
        -Name "<Name for your deployment>"
    ```
    此命令部署映像资源。 若要查询资源，请运行以下命令：

    ```powershell
    Get-AzureRmImage -ResourceGroupName <Resource Group Name> -name <Image Name>
    ``` 
    下面是成功创建的映像的示例输出。
    
    ```powershell
    PS C:\WINDOWS\system32> login-AzureRMAccount -EnvironmentName aztest -TenantId c0257de7-538f-415c-993a-1b87a031879d
    
    Account               SubscriptionName              TenantId                             Environment
    -------               ----------------              --------                             -----------
    EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d aztest
    
   PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\CreateImage\CreateImage.json"
    PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\CreateImage\CreateImage.parameters.json"
    PS C:\WINDOWS\system32> $RGName = "rg2"
    PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment4"
        
    DeploymentName          : deployment4
    ResourceGroupName       : rg2
    ProvisioningState       : Succeeded
    Timestamp               : 12/10/2020 7:06:57 PM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name             Type                       Value
                              ===============  =========================  ==========
                              osType           String                     Linux
                              imageName        String                     myaselinuximg
                              imageUri         String
                              https://sa2.blob.myasegpuvm.wdshcsso.com/con1/ubuntu18.04waagent.vhd
    
    Outputs                 :
    DeploymentDebugLogLevel :    
    PS C:\WINDOWS\system32>
    ```
    
## <a name="create-vm"></a>创建 VM

### <a name="edit-parameters-file-to-create-vm"></a>编辑参数文件以创建 VM
 
若要创建 VM，请使用 `CreateVM.parameters.json` 参数文件。 它采用以下参数。
    
```json
"vmName": {
            "value": "<Name for your VM>"
        },
        "adminUsername": {
            "value": "<Username to log into the VM>"
        },
        "Password": {
            "value": "<Password to log into the VM>"
        },
        "imageName": {
            "value": "<Name for your image>"
        },
        "vmSize": {
            "value": "<A supported size for your VM>"
        },
        "vnetName": {
            "value": "<Name for the virtual network, use ASEVNET>"
        },
        "subnetName": {
            "value": "<Name for the subnet, use ASEVNETsubNet>"
        },
        "vnetRG": {
            "value": "<Resource group for Vnet, use ASERG>"
        },
        "nicName": {
            "value": "<Name for the network interface>"
        },
        "privateIPAddress": {
            "value": "<Private IP address, enter a static IP in the subnet created earlier or leave empty to assign DHCP>"
        },
        "IPConfigName": {
            "value": "<Name for the ipconfig associated with the network interface>"
        }
```    

`CreateVM.parameters.json`为 Azure Stack Edge Pro 设备分配适当的参数。

1. 提供唯一名称、网络接口名称和 ipconfig 名称。 
1. 输入用户名、密码和受支持的 VM 大小。
1. 当你为计算启用网络接口时，会在该网络接口上自动创建虚拟交换机和虚拟网络。 可以查询现有虚拟网络以获取 Vnet 名称、子网名称和 Vnet 资源组名称。

    运行以下命令：

    ```powershell
    Get-AzureRmVirtualNetwork
    ```
    下面是示例输出：
    
    ```powershell
    
    PS C:\WINDOWS\system32> Get-AzureRmVirtualNetwork
    
    Name                   : ASEVNET
    ResourceGroupName      : ASERG
    Location               : dbelocal
    Id                     : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/ASERG/providers/Microsoft
                             .Network/virtualNetworks/ASEVNET
    Etag                   : W/"990b306d-18b6-41ea-a456-b275efe21105"
    ResourceGuid           : f8309d81-19e9-42fc-b4ed-d573f00e61ed
    ProvisioningState      : Succeeded
    Tags                   :
    AddressSpace           : {
                               "AddressPrefixes": [
                                 "10.57.48.0/21"
                               ]
                             }
    DhcpOptions            : null
    Subnets                : [
                               {
                                 "Name": "ASEVNETsubNet",
                                 "Etag": "W/\"990b306d-18b6-41ea-a456-b275efe21105\"",
                                 "Id": "/subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/ASERG/provider
                             s/Microsoft.Network/virtualNetworks/ASEVNET/subnets/ASEVNETsubNet",
                                 "AddressPrefix": "10.57.48.0/21",
                                 "IpConfigurations": [],
                                 "ResourceNavigationLinks": [],
                                 "ServiceEndpoints": [],
                                 "ProvisioningState": "Succeeded"
                               }
                             ]
    VirtualNetworkPeerings : []
    EnableDDoSProtection   : false
    EnableVmProtection     : false
    
    PS C:\WINDOWS\system32>
    ```

    将 ASEVNET 用于 Vnet 名称，ASEVNETsubNet 用于子网名称，ASERG 用于 Vnet 资源组名称。
    
1. 现在，你将需要一个静态 IP 地址，以分配给上面定义的子网网络中的 VM。 将 **PrivateIPAddress** 替换为参数文件中的此地址。 若要让 VM 从本地 DCHP 服务器获取 IP 地址，请将值保留 `privateIPAddress` 为空。  
    
    ```json
    "privateIPAddress": {
                "value": "5.5.153.200"
            },
    ```
    
4. 保存参数文件。

    下面是本文中使用的示例 json。
    
    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "vmName": {
              "value": "VM1"
          },
          "adminUsername": {
              "value": "Administrator"
          },
          "Password": {
              "value": "Password1"
          },
        "imageName": {
          "value": "myaselinuximg"
        },
        "vmSize": {
          "value": "Standard_NC4as_T4_v3"
        },
        "vnetName": {
          "value": "ASEVNET"
        },
        "subnetName": {
          "value": "ASEVNETsubNet"
        },
        "vnetRG": {
          "value": "aserg"
        },
        "nicName": {
          "value": "nic5"
        },
        "privateIPAddress": {
          "value": ""
        },
        "IPConfigName": {
          "value": "ipconfig5"
        }
      }
    }
    ```      

### <a name="deploy-template-to-create-vm"></a>部署模板以创建 VM

部署 VM 创建模板 `CreateVM.json` 。 此模板从现有 VNet 创建一个网络接口，并从部署的映像创建 VM。

1. 运行以下命令： 
    
    ```powershell
    Command:
        
        $templateFile = "<Path to CreateVM.json>"
        $templateParameterFile = "<Path to CreateVM.parameters.json>"
        $RGName = "<Resource group name>"
             
        New-AzureRmResourceGroupDeployment `
            -ResourceGroupName $RGName `
            -TemplateFile $templateFile `
            -TemplateParameterFile $templateParameterFile `
            -Name "<DeploymentName>"
    ```   

    创建 VM 需要15-20 分钟。 下面是成功创建的 VM 的示例输出。
    
    ```powershell
    PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\CreateVM\CreateVM.json"
    PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\CreateVM\CreateVM.parameters.json"
    PS C:\WINDOWS\system32> $RGName = "rg2"
    PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "Deployment6"
       
    DeploymentName          : Deployment6
    ResourceGroupName       : rg2
    ProvisioningState       : Succeeded
    Timestamp               : 12/10/2020 7:51:28 PM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name             Type                       Value
                              ===============  =========================  ==========
                              vmName           String                     VM1
                              adminUsername    String                     Administrator
                              password         String                     Password1
                              imageName        String                     myaselinuximg
                              vmSize           String                     Standard_NC4as_T4_v3
                              vnetName         String                     ASEVNET
                              vnetRG           String                     aserg
                              subnetName       String                     ASEVNETsubNet
                              nicName          String                     nic5
                              ipConfigName     String                     ipconfig5
                              privateIPAddress  String
    
    Outputs                 :
    DeploymentDebugLogLevel :
    
    PS C:\WINDOWS\system32
    ```   

    还可以 `New-AzureRmResourceGroupDeployment` 通过参数异步运行命令 `–AsJob` 。 下面是在后台运行 cmdlet 时的示例输出。 然后，可以查询使用 cmdlet 创建的作业的状态 `Get-Job` 。

    ```powershell   
    PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment `
    >>     -ResourceGroupName $RGName `
    >>     -TemplateFile $templateFile `
    >>     -TemplateParameterFile $templateParameterFile `
    >>     -Name "Deployment2" `
    >>     -AsJob
     
    Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
    --     ----            -------------   -----         -----------     --------             -------
    2      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmResourceGro...
     
    PS C:\WINDOWS\system32> Get-Job -Id 2
     
    Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
    --     ----            -------------   -----         -----------     --------             -------
    ```

7. 检查 VM 是否已成功预配。 运行以下命令：

    `Get-AzureRmVm`


## <a name="connect-to-a-vm"></a>连接到 VM

根据你创建的是 Windows 还是 Linux VM，连接步骤可能会有所不同。

### <a name="connect-to-windows-vm"></a>连接到 Windows VM

按照以下步骤连接到 Windows VM。

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]

### <a name="connect-to-linux-vm"></a>连接到 Linux VM

按照以下步骤连接到 Linux VM。

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]



## <a name="next-steps"></a>后续步骤

[Azure 资源管理器 cmdlet](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)