---
title: 教程：使用 Azure CLI 部署到现有虚拟网络中 - Azure 专用 HSM | Microsoft Docs
description: 本教程介绍如何使用 CLI 将专用 HSM 部署到现有虚拟网络中
services: dedicated-hsm
documentationcenter: na
author: msmbaldwin
manager: rkarlin
editor: ''
ms.service: key-vault
ms.topic: tutorial
ms.custom: mvc, seodec18, devx-track-azurecli
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/20/2020
ms.author: mbaldwin
ms.openlocfilehash: b6f4610887092b1dac5cdc85622739318d5921d7
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2020
ms.locfileid: "96852228"
---
# <a name="tutorial-deploying-hsms-into-an-existing-virtual-network-using-the-azure-cli"></a>教程：使用 Azure CLI 将 HSM 部署到现有虚拟网络中

Azure 专用 HSM 提供供单个客户使用的物理设备，由客户对设备进行完全的管理控制并承担完全的管理责任。 由于使用物理设备，因此需要 Microsoft 来控制设备分配，确保对容量进行有效的管理。 因此，在 Azure 订阅中，专用 HSM 服务通常不可见，不可用于资源预配。 Azure 客户如果需要访问专用 HSM 服务，必须首先联系其 Microsoft 客户主管，提交专用 HSM 服务注册请求。 只有在此流程成功完成以后，才可以进行预配。 

本教程介绍一个典型的预配过程，其中：

- 客户已经有一个虚拟网络
- 客户有一个虚拟机
- 客户需将 HSM 资源添加到现有环境中。

典型的高可用性多区域部署体系结构看起来如下所示：

![多区域部署](media/tutorial-deploy-hsm-cli/high-availability-architecture.png)

本教程重点介绍一对 HSM 和必需的 ExpressRoute 网关（参见上面的子网 1），该网关会集成到现有的虚拟网络（参见上面的 VNET 1）中。  所有其他资源都是标准的 Azure 资源。 同一集成过程可以用于上述 VNET 3 上的子网 4 中的 HSM。

## <a name="prerequisites"></a>先决条件

Azure 专用 HSM 目前在 Azure 门户中不可用。 与该服务的所有交互将通过命令行或 PowerShell 进行。 本教程将使用 Azure Cloud Shell 中的命令行 (CLI) 界面。 如果不熟悉 Azure CLI，请按以下入门说明操作：[Azure CLI 2.0 入门](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest&preserve-view=true)。

假设：

- 已完成 Azure 专用 HSM 注册过程
- 已获允使用此服务。 否则，请与 Microsoft 客户代表联系，了解详细信息。
- 已为这些资源创建一个资源组，在本教程中部署的新资源将加入该组。
- 已根据上图创建必需的虚拟网络、子网和虚拟机，现在需将 2 个 HSM 集成到该部署中。

下面的所有说明假定你已导航到 Azure 门户并打开 Cloud Shell（选择门户右上角的“\>\_”）。

## <a name="provisioning-a-dedicated-hsm"></a>预配专用 HSM

可以通过 ExpressRoute 网关预配 HSM 并将其集成到现有虚拟网络中，这一操作将通过 ssh 进行验证。 该验证可确保 HSM 设备的可访问性以及基本的可用性，以便进行进一步的配置活动。

### <a name="validating-feature-registration"></a>验证功能注册

如上所述，任何预配活动都要求为订阅注册专用 HSM 服务。 若要对此进行验证，请在 Azure 门户 Cloud Shell 中运行以下命令。

```azurecli
az feature show \
   --namespace Microsoft.HardwareSecurityModules \
   --name AzureDedicatedHSM
```

命令应该返回“已注册”状态（如下所示）。 如果命令没有返回“已注册”，你需要通过与 Microsoft 客户代表联系来注册此服务。

![订阅状态](media/tutorial-deploy-hsm-cli/subscription-status.png)

### <a name="creating-hsm-resources"></a>创建 HSM 资源

在创建 HSM 资源之前，需要具备一些必需的资源。 必须有一个子网范围适用于计算、HSM 和网关的虚拟网络。 以下命令以示例方式说明了如何才能创建此类虚拟网络。

```azurecli
az network vnet create \
  --name myHSM-vnet \
  --resource-group myRG \
  --address-prefix 10.2.0.0/16 \
  --subnet-name compute \
  --subnet-prefix 10.2.0.0/24
```

```azurecli
az network vnet subnet create \
  --vnet-name myHSM-vnet \
  --resource-group myRG \
  --name hsmsubnet \
  --address-prefixes 10.2.1.0/24 \
  --delegations Microsoft.HardwareSecurityModules/dedicatedHSMs
```

```azurecli
az network vnet subnet create \
  --vnet-name myHSM-vnet \
  --resource-group myRG \
  --name GatewaySubnet \
  --address-prefixes 10.2.255.0/26
```

>[!NOTE]
>此虚拟网络需要注意的最重要配置，是 HSM 设备的子网必须将委托设置为“Microsoft.HardwareSecurityModules/dedicatedHSMs”。  如果不设置此选项，HSM 预配将不起作用。

配置网络后，请使用以下 Azure CLI 命令预配 HSM。

1. 使用 [az dedicated-hsm create](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_create) 命令预配第一个 HSM。 HSM 的名称为 hsm1。 替换你的订阅：

   ```azurecli
   az dedicated-hsm create --location westus --name hsm1 --resource-group myRG --network-profile-network-interfaces \
        /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Network/virtualNetworks/MyHSM-vnet/subnets/MyHSM-vnet
   ```

   完成此部署应该需要大约 25 到 30 分钟，大部分时间花在 HSM 设备上。

1. 若要查看当前的 HSM，请运行 [az dedicated-hsm show](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_show) 命令：

   ```azurecli
   az dedicated-hsm show --resource group myRG --name hsm1
   ```

1. 使用以下命令预配第二个 HSM：

   ```azurecli
   az dedicated-hsm create --location westus --name hsm2 --resource-group myRG --network-profile-network-interfaces \
        /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Network/virtualNetworks/MyHSM-vnet/subnets/MyHSM-vnet
   ```

1. 运行 [az dedicated-hsm list](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_list) 命令，查看有关当前 HSM 的详细信息：

   ```azurecli
   az dedicated-hsm list --resource-group myRG
   ```

还有其他可能有用的命令。 使用 [az dedicated-hsm update](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_update) 命令更新 HSM：

```azurecli
az dedicated-hsm update --resource-group myRG –name hsm1
```

若要删除 HSM，请使用 [az dedicated-hsm delete](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_delete) 命令：

```azurecli
az dedicated-hsm delete --resource-group myRG –name hsm1
```

## <a name="verifying-the-deployment"></a>验证部署

若要验证设备是否已预配并查看设备属性，请运行以下命令集。 确保资源组已正确设置且资源名称与参数文件中的名称完全一样。

```azurecli
subid=$(az account show --query id --output tsv)
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM1
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM2
```

输出如下所示：

```json
{
    "id": n/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/HSM-RG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSMl",
    "identity": null,
    "kind": null,
    "location": "westus",
    "managedBy": null,
    "name": "HSM1",
    "plan": null,
    "properties": {
        "networkProfile": {
            "networkInterfaces": [
            {
            "id": n/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/HSM-RG/providers/Microsoft.Network/networkInterfaces/HSMl_HSMnic", "privatelpAddress": "10.0.2.5",
            "resourceGroup": "HSM-RG"
            }
            L
            "subnet": {
                "id": n/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/HSM-RG/providers/Microsoft.Network/virtualNetworks/demo-vnet/subnets/hsmsubnet", "resourceGroup": "HSM-RG"
            }
        },
        "provisioningState": "Succeeded",
        "stampld": "stampl",
        "statusMessage": "The Dedicated HSM device is provisioned successfully and ready to use."
    },
    "resourceGroup": "HSM-RG",
    "sku": {
        "capacity": null,
        "family": null,
        "model": null,
        "name": "SafeNet Luna Network HSM A790",
        "size": null,
        "tier": null
    },
    "tags": {
        "Environment": "prod",
        "resourceType": "Hsm"
    },
    "type": "Microsoft.HardwareSecurityModules/dedicatedHSMs"
}
```

现在也可通过 [Azure 资源浏览器](https://resources.azure.com/)来查看资源。   进入浏览器中以后，请依次展开左侧的“订阅”、专用 HSM 的特定订阅、“资源组”、所使用的资源组，最后选择“资源”项。

## <a name="testing-the-deployment"></a>测试部署

测试部署就是先连接到能够访问 HSM 的虚拟机，然后直接连接到 HSM 设备。 上述操作将确认 HSM 是否可以访问。
ssh 工具用于连接到虚拟机。 命令将如下所示，但使用在参数中指定的管理员名称和 dns 名称。

`ssh adminuser@hsmlinuxvm.westus.cloudapp.azure.com`

也可使用 VM 的 IP 地址来代替上述命令中的 DNS 名称。 如果命令成功，它会提示你输入密码，你应该输入该密码。 登录到虚拟机以后，则可使用在门户中发现的与 HSM 相关联的网络接口资源的专用 IP 地址登录到 HSM。

![组件列表](media/tutorial-deploy-hsm-cli/resources.png)

>[!NOTE]
>请注意“显示隐藏的类型”复选框，该复选框在选中时会显示 HSM 资源。

在上面的屏幕截图中，单击“HSM1_HSMnic”或“HSM2_HSMnic”会显示相应的专用 IP 地址。 也可通过上面使用的 `az resource show` 命令来确定正确的 IP 地址。 

有了正确的 IP 地址以后，请运行以下命令，用该地址进行替换：

`ssh tenantadmin@10.0.2.4`

如果成功，系统会提示你输入密码。 默认的密码为 PASSWORD，HSM 会首先要求你更改密码。因此，请设置一个强密码，并使用组织首选的机制来存储密码，防止丢失。

>[!IMPORTANT]
>如果丢失此密码，则需重置 HSM，这意味着丢失密钥。

使用 ssh 连接到 HSM 以后，请运行以下命令，确保 HSM 可以正常运行。

`hsm show`

输出应该如下图所示：

![屏幕截图显示了 PowerShell 窗口中的输出。](media/tutorial-deploy-hsm-cli/hsm-show-output.png)

目前已为一个高度可用的双 HSM 型部署分配了所有资源，并验证了访问权限和运行状态。 进一步的配置或测试涉及更多针对 HSM 设备本身的工作。 因此，应该按照《Thales Luna 网络 HSM 7 管理指南》第 7 章中的说明操作，以便初始化 HSM 并创建分区。 在 Thales 客户支持门户中注册并获得客户 ID 以后，即可直接从 Thales 下载所有文档和软件。 下载客户端软件 7.2 版即可获取所有必需的组件。

## <a name="delete-or-clean-up-resources"></a>删除或清理资源

如果已完成 HSM 设备的操作，则可将其作为资源删除，并让其返回到可用池中。 执行该操作时，最需要关注的问题是设备上的敏感客户数据。 将设备“归零”的最佳方式是让 HSM 管理员密码错误 3 次（注意：这不是设备管理员，而是实际的 HSM 管理员）。 在设备处于“已归零”状态之前，不能将其作为 Azure 资源删除，这是一种保护密钥材料的安全措施。

> [!NOTE]
> 如果有 Thales 设备配置的问题，则应联系 [Thales 客户支持](https://safenet.gemalto.com/technical-support/)。

如果已完成此资源组中所有资源的相关操作，则可使用以下命令将其全部删除：

```azurecli
az group delete \
   --resource-group myRG \
   --name HSMdeploy \
   --verbose
```

## <a name="next-steps"></a>后续步骤

完成本教程中的步骤以后，你就预配好了专用 HSM 资源，并有了一个虚拟网络，该虚拟网络包含与 HSM 通信所需的 HSM 和其他网络组件。  现在可以根据首选部署体系结构的要求，使用更多资源来补充此部署了。 若要详细了解如何进行部署规划，请参阅概念文档。
建议在进行设计时，在主要区域使用两个 HSM 解决机架级可用性问题，在次要区域使用两个 HSM 解决区域可用性问题。 

* [高可用性](high-availability.md)
* [物理安全性](physical-security.md)
* [网络](networking.md)
* [可支持性](supportability.md)
* [Monitoring](monitoring.md)
