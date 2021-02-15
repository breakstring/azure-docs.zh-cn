---
title: 使用虚拟网络保护 Azure 机器学习工作区
titleSuffix: Azure Machine Learning
description: 使用独立的 Azure 虚拟网络保护 Azure 机器学习工作区和关联资源。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 10/06/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy20q4, tracking-python, contperf-fy21q1
ms.openlocfilehash: 07b8c130a2a22554e4cd5b33996d5a5ee967d47f
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97029526"
---
# <a name="secure-an-azure-machine-learning-workspace-with-virtual-networks"></a>使用虚拟网络保护 Azure 机器学习工作区

本文中介绍如何在虚拟网络中保护 Azure 机器学习工作区及其关联资源。


本文是由两部分组成的系列文章的第五部分，指导你如何保护 Azure 机器学习工作流。 强烈建议您通读第 [一部分： VNet 概述](how-to-network-security-overview.md) 以首先了解总体体系结构。 

请参阅本系列中的其他文章：

[1. VNet 概述](how-to-network-security-overview.md)  >  **2。保护工作区**  >  [3。保护定型环境](how-to-secure-training-vnet.md)  >  [4。保护推断环境](how-to-secure-inferencing-vnet.md)  >  [5。启用 studio 功能](how-to-enable-studio-virtual-network.md)

本文介绍如何在虚拟网络中保护以下工作区资源：
> [!div class="checklist"]
> - Azure 机器学习工作区
> - Azure 存储帐户
> - Azure 机器学习数据存储和数据集
> - Azure Key Vault
> - Azure 容器注册表

## <a name="prerequisites"></a>先决条件

+ 阅读 [网络安全概述](how-to-network-security-overview.md) 一文，了解常见的虚拟网络方案和总体虚拟网络体系结构。

+ 用于计算资源的现有虚拟网络和子网。

+ 若要将资源部署到虚拟网络或子网，你的用户帐户必须对 azure RBAC)  (azure 基于角色的访问控制具有以下操作的权限：

    - “Microsoft.Network/virtualNetworks/join/action”（在虚拟网络资源上）。
    - “Microsoft.Network/virtualNetworks/subnet/join/action”（在子网资源上）。

    有关 Azure RBAC with 网络的详细信息，请参阅 [联网内置角色](../role-based-access-control/built-in-roles.md#networking)


## <a name="secure-the-workspace-with-private-endpoint"></a>通过专用终结点保护工作区

Azure Private Link 允许使用专用终结点连接到工作区。 专用终结点是虚拟网络中的一组专用 IP 地址。 然后，你可以限制工作区访问权限，只允许通过专用 IP 地址访问你的工作区。 专用链接有助于降低数据外泄风险。

有关设置专用链接工作区的详细信息，请参阅 how [to Configure Private link](how-to-configure-private-link.md)。

## <a name="secure-azure-storage-accounts-with-service-endpoints"></a>使用服务终结点保护 Azure 存储帐户

Azure 机器学习支持将存储帐户配置为使用服务终结点或专用终结点。 本部分介绍如何使用服务终结点保护 Azure 存储帐户。 对于专用终结点，请参阅下一部分。

> [!IMPORTANT]
> 可将 Azure 机器学习的默认存储帐户或者将非默认存储帐户放在虚拟网络中。 
>
> 创建工作区时，会自动预配默认存储帐户。
>
> 对于非默认存储帐户，可以使用 [`Workspace.create()` 函数](/python/api/azureml-core/azureml.core.workspace%28class%29?preserve-view=true&view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-&preserve-view=true)中的 `storage_account` 参数按 Azure 资源 ID 指定自定义的存储帐户。

若要在虚拟网络中使用工作区的 Azure 存储帐户，请按照以下步骤操作：

1. 在 Azure 门户中，转到你要在工作区中使用的存储服务。

   [![已附加到 Azure 机器学习工作区的存储](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. 在存储服务帐户页上，选择“防火墙和虚拟网络”。

   ![Azure 门户中“Azure 存储”页上的“防火墙和虚拟网络”区域](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. 在“防火墙和虚拟网络”页上执行以下操作：
    1. 选择“所选网络”。
    1. 在“虚拟网络”下，选择“添加现有的虚拟网络”链接。  此操作将添加计算资源所在的虚拟网络（参阅步骤 1）。

        > [!IMPORTANT]
        > 存储帐户必须与用于训练或推理的计算实例或群集位于同一虚拟网络和子网中。

    1. 选中“允许受信任的 Microsoft 服务访问此存储帐户”复选框。 这不会使所有 Azure 服务获得对你的存储帐户的访问权限。
    
        * 某些服务的资源在注册到订阅后，可在同一订阅中访问存储帐户以便执行选择操作 。 例如，写入日志或创建备份。
        * 可通过向其系统分配的托管标识分配 Azure 角色，向某些服务的资源授予对存储帐户的显式访问权限。

        有关详细信息，请参阅[配置 Azure 存储防火墙和虚拟网络](../storage/common/storage-network-security.md#trusted-microsoft-services)。

    > [!IMPORTANT]
    > 使用 Azure 机器学习 SDK 时，开发环境必须能够连接到 Azure 存储帐户。 当存储帐户位于虚拟网络中时，防火墙必须允许从开发环境的 IP 地址进行访问。
    >
    > 若要启用对存储帐户的访问，请从开发客户端上的 Web 浏览器访问存储帐户的“防火墙和虚拟网络”。 然后选中“添加客户端 IP 地址”复选框，将客户端的 IP 地址添加到“地址范围”。  也可以使用“地址范围”字段手动输入开发环境的 IP 地址。 添加客户端的 IP 地址后，该客户端可以使用 SDK 访问存储帐户。

   [![Azure 门户中的“防火墙和虚拟网络”窗格](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="secure-azure-storage-accounts-with-private-endpoints"></a>保护具有专用终结点的 Azure 存储帐户

Azure 机器学习支持将存储帐户配置为使用服务终结点或专用终结点。 如果存储帐户使用专用终结点，则必须为默认存储帐户配置两个专用终结点：
1. 具有 **blob** 目标子资源的专用终结点。
1. 具有文件目标子资源 (**文件** 共享) 的专用终结点。

![显示具有 blob 和文件选项的专用终结点配置页的屏幕截图](./media/how-to-enable-studio-virtual-network/configure-storage-private-endpoint.png)

若要为 **非** 默认存储的存储帐户配置专用终结点，请选择对应于要添加的存储帐户的 **目标子资源** 类型。

有关详细信息，请参阅 [使用 Azure 存储的专用终结点](../storage/common/storage-private-endpoints.md)

## <a name="secure-datastores-and-datasets"></a>保护数据存储和数据集

本部分介绍如何通过虚拟网络在 SDK 体验中使用数据存储和数据集。 有关工作室体验的详细信息，请参阅[在 Azure 虚拟网络中使用机器学习工作室](how-to-enable-studio-virtual-network.md)。

若要使用 SDK 访问数据，必须使用存储数据的单个服务所需的身份验证方法。 例如，如果注册数据存储区以访问 Azure Data Lake Store Gen2，则仍必须使用[连接到 Azure 存储服务](how-to-access-data.md#azure-data-lake-storage-generation-2)中所述的服务主体。

### <a name="disable-data-validation"></a>禁用数据验证

默认情况下，当你尝试使用 SDK 访问数据时，Azure 机器学习会执行数据有效性和凭据检查。 如果数据位于虚拟网络后面，则 Azure 机器学习无法完成这些检查。 若要避免这种情况，你必须创建跳过验证的数据存储和数据集。

### <a name="use-datastores"></a>使用数据存储

 Azure Data Lake Store Gen1 和 Azure Data Lake Store Gen2 默认跳过验证，因此无需进一步操作。 但是，对于以下服务，你可以使用类似的语法来跳过数据存储验证：

- Azure Blob 存储
- Azure 文件共享
- PostgreSQL
- Azure SQL 数据库

下面的代码示例创建一个新的 Azure Blob 数据存储并设置 `skip_validation=True`。

```python
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,  

                                                         datastore_name=blob_datastore_name,  

                                                         container_name=container_name,  

                                                         account_name=account_name, 

                                                         account_key=account_key, 

                                                         skip_validation=True ) // Set skip_validation to true
```

### <a name="use-datasets"></a>使用数据集

对于下列数据集类型，用于跳过数据集验证的语法是类似的：
- 带分隔符的文件
- JSON 
- Parquet
- SQL
- 文件

下面的代码创建一个新的 JSON 数据集并设置 `validate=False`。

```python
json_ds = Dataset.Tabular.from_json_lines_files(path=datastore_paths, 

validate=False) 

```

## <a name="secure-azure-key-vault"></a>保护 Azure Key Vault

Azure 机器学习使用关联的 Key Vault 实例存储以下凭据：
* 关联的存储帐户连接字符串
* Azure 容器存储库实例的密码
* 数据存储的连接字符串

若要在虚拟网络的后面将 Azure 机器学习试验功能与 Azure Key Vault 配合使用，请执行以下步骤：

1. 转到与工作区关联的 Key Vault。

1. 在“Key Vault”页上的左侧窗格中，选择“网络” 。

1. 在“防火墙和虚拟网络”选项卡上执行以下操作：
    1. 在“允许访问来源”下，选择“专用终结点和所选网络” 。
    1. 在“虚拟网络”下，选择“添加现有的虚拟网络”，以添加试验计算资源所在的虚拟网络。 
    1. 在“允许受信任的 Microsoft 服务跳过此防火墙？”下选择“是” 。

   [![“Key Vault”窗格中的“防火墙和虚拟网络”部分](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="enable-azure-container-registry-acr"></a>启用 Azure 容器注册表 (ACR)

若要在虚拟网络内部使用 Azure 容器注册表，必须先满足以下要求：

* Azure 容器注册表必须是高级版。 若要详细了解如何升级，请参阅[更改 SKU](../container-registry/container-registry-skus.md#changing-tiers)。

* Azure 容器注册表必须与用于训练或推理的存储帐户和计算目标位于同一虚拟网络和子网中。

* Azure 机器学习工作区必须包含 [Azure 机器学习计算群集](how-to-create-attach-compute-cluster.md)。

    如果 ACR 位于虚拟网络后面，Azure 机器学习无法使用它来直接生成 Docker 映像。 而是使用计算群集来生成映像。

* 在虚拟网络中使用 ACR 与 Azure 机器学习之前，必须创建支持事件以启用此功能。 有关详细信息，请参阅[管理和增加配额](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases)。

满足这些要求后，请使用以下步骤启用 Azure 容器注册表。

1. 请使用以下方法之一查找工作区的 Azure 容器注册表的名称：

    __Azure 门户__

    在工作区的“概述”部分中，“注册表”值链接到 Azure 容器注册表。

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="工作区的 Azure 容器注册表" border="true":::

    __Azure CLI__

    如果[安装了用于 Azure CLI 的机器学习扩展](reference-azure-machine-learning-cli.md)，可以运行 `az ml workspace show` 命令来显示工作区信息。

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    此命令会返回类似于 `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"` 的值。 此字符串的最后一部分是工作区的 Azure 容器注册表的名称。

1. 使用[配置注册表的网络访问权限](../container-registry/container-registry-vnet.md#configure-network-access-for-registry)中的步骤来限制对虚拟网络的访问。 添加虚拟网络时，为 Azure 机器学习资源选择虚拟网络和子网。

1. 使用 Azure 机器学习 Python SDK 将计算群集配置为生成 Docker 映像。 下面的代码片段展示了如何执行此操作：

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    ```

    > [!IMPORTANT]
    > 存储帐户、计算群集和 Azure 容器注册表必须都位于虚拟网络的同一子网中。
    
    有关详细信息，请参阅 [update()](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-&preserve-view=true) 方法参考。

1. 应用以下 Azure 资源管理器模板。 借助此模板，工作区可以与 ACR 进行通信。

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "keyVaultArmId": {
        "type": "string"
        },
        "workspaceName": {
        "type": "string"
        },
        "containerRegistryArmId": {
        "type": "string"
        },
        "applicationInsightsArmId": {
        "type": "string"
        },
        "storageAccountArmId": {
        "type": "string"
        },
        "location": {
        "type": "string"
        }
    },
    "resources": [
        {
        "type": "Microsoft.MachineLearningServices/workspaces",
        "apiVersion": "2019-11-01",
        "name": "[parameters('workspaceName')]",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "sku": {
            "tier": "basic",
            "name": "basic"
        },
        "properties": {
            "sharedPrivateLinkResources":
    [{"Name":"Acr","Properties":{"PrivateLinkResourceId":"[concat(parameters('containerRegistryArmId'), '/privateLinkResources/registry')]","GroupId":"registry","RequestMessage":"Approve","Status":"Pending"}}],
            "keyVault": "[parameters('keyVaultArmId')]",
            "containerRegistry": "[parameters('containerRegistryArmId')]",
            "applicationInsights": "[parameters('applicationInsightsArmId')]",
            "storageAccount": "[parameters('storageAccountArmId')]"
        }
        }
    ]
    }
    ```

    此模板创建一个专用终结点用于通过网络从工作区访问你的 ACR。 下面的屏幕截图显示该专用终结点的示例。

    :::image type="content" source="media/how-to-secure-workspace-vnet/acr-private-endpoint.png" alt-text="ACR 专用终结点设置":::

    > [!IMPORTANT]
    > 不要删除此终结点！ 如果意外删除此终结点，可以重新应用本步骤中的模板创建新终结点。

## <a name="next-steps"></a>后续步骤

本文是由四部分构成的虚拟网络系列中的第一部分。 若要了解如何保护虚拟网络，请参阅其余文章：

* [第1部分：虚拟网络概述](how-to-network-security-overview.md)
* [第 3 部分：保护训练环境](how-to-secure-training-vnet.md)
* [第 4 部分：保护推理环境](how-to-secure-inferencing-vnet.md)
* [第 5 部分：启用工作室功能](how-to-enable-studio-virtual-network.md)