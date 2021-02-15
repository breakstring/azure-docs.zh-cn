---
title: 在将云服务部署到 Azure 时对 ConstrainedAllocationFailed 进行故障排除 |Microsoft Docs
description: 本文介绍如何在将云服务部署到 Azure 时解决 ConstrainedAllocationFailed 异常。
services: cloud-services
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/04/2020
ms.openlocfilehash: de344bbcd89158676bacf2a8aa1743d282700b9d
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520904"
---
# <a name="troubleshoot-constrainedallocationfailed-when-deploying-a-cloud-service-to-azure"></a>在将云服务部署到 Azure 时对 ConstrainedAllocationFailed 进行故障排除

在本文中，你将排查 Azure 云服务由于约束而无法部署的分配失败。

Microsoft Azure 在你执行以下操作时分配：

- 升级云服务实例

- 添加新的 web 角色或辅助角色实例

- 将实例部署到云服务

甚至在达到 Azure 订阅限制之前，甚至可能会在这些操作期间收到错误。

> [!TIP]
> 规划服务的部署时，本信息可能也有用。

## <a name="symptom"></a>症状

在 Azure 门户中，导航到云服务，并在侧栏中选择 " *操作日志 (经典")* 查看日志。

检查云服务的日志时，你将看到以下异常：

|异常类型  |错误消息  |
|---------|---------|
|ConstrainedAllocationFailed     |Azure 操作 " `{Operation ID}` " 失败，代码为 ConstrainedAllocationFailed。 详细信息：分配失败；无法满足请求中的约束。 请求的新服务部署绑定至地缘组，或以虚拟网络为目标，或此托管服务下已经有部署。 上述任一情况都会将新的部署局限于特定的 Azure 资源。 请稍后重试，或尝试减少 VM 大小或角色实例的数量。 或者，可能的话，删除先前提到的约束，或尝试部署到不同的区域。|

## <a name="cause"></a>原因

正在部署到的区域或群集存在容量问题。 当所选的资源 SKU 不适用于指定的位置时，会发生这种情况。

> [!NOTE]
> 在部署云服务的第一个节点时，该节点 *固定* 到资源池。 资源池可以是单个群集，也可以是一组分类。
>
> 随着时间的推移，此资源池中的资源可能会完全使用。 如果在固定的资源池中有足够的资源可用时，云服务对其他资源进行分配请求，则请求将导致 [分配失败](cloud-services-allocation-failures.md)。

## <a name="solution"></a>解决方案

在这种情况下，你应该选择一个不同的区域或 SKU，将云服务部署到。 在部署或升级云服务之前，可以确定区域或可用性区域中可用的 Sku。 遵循下面的 [Azure CLI](#list-skus-in-region-using-azure-cli)、 [PowerShell](#list-skus-in-region-using-powershell)或 [REST API](#list-skus-in-region-using-rest-api) 进程。

### <a name="list-skus-in-region-using-azure-cli"></a>使用 Azure CLI 列出区域中的 Sku

你可以使用 [az vm list-sku](https://docs.microsoft.com/cli/azure/vm.html#az_vm_list_skus) 命令。

- 使用 `--location` 参数将输出筛选为你正在使用的位置。
- 使用 `--size` 参数按部分大小名称搜索。
- 有关详细信息，请参阅 " [SKU 不可用的解决错误](../azure-resource-manager/templates/error-sku-not-available.md#solution-2---azure-cli) " 指南。

    例如：

    ```azurecli
    az vm list-skus --location southcentralus --size Standard_F --output table
    ```

    **示例结果：** ![运行 "az vm list-sku--location default-machinelearning-southcentralus--size Standard_F--output table" 命令的 Azure CLI 输出，其中显示了可用的 Sku。](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-constrained-allocation-failed-1.png)

#### <a name="list-skus-in-region-using-powershell"></a>使用 PowerShell 列出区域中的 Sku

可以使用 [AzComputeResourceSku](https://docs.microsoft.com/powershell/module/az.compute/get-azcomputeresourcesku) 命令。

- 按位置筛选结果。
- 必须拥有最新版本 PowerShell 才能运行此命令。
- 有关详细信息，请参阅 " [SKU 不可用的解决错误](../azure-resource-manager/templates/error-sku-not-available.md#solution-1---powershell) " 指南。

例如：

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

**其他一些有用的命令：**

筛选出包含大小 (Standard_DS14_v2) 的位置：

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("Standard_DS14_v2")}
```

筛选出所有包含大小 (V3) 的位置：

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("v3")} | fc
```

#### <a name="list-skus-in-region-using-rest-api"></a>使用 REST API 列出区域中的 Sku

可以使用 [资源 Sku 列表](https://docs.microsoft.com/rest/api/compute/resourceskus/list) 操作。 它会以下列格式返回可用 SKU 和区域：

```json
{
  "value": [
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A0",
      "tier": "Standard",
      "size": "A0",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A1",
      "tier": "Standard",
      "size": "A1",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    <Rest_of_your_file_is_located_here...>
  ]
}
    
```

## <a name="next-steps"></a>后续步骤

对于更多的分配失败解决方案，并更好地了解如何生成它们：

> [!div class="nextstepaction"]
> [云服务 (分配失败) ](cloud-services-allocation-failures.md)

如果本文未解决你的 Azure 问题，请访问 MSDN 上的 Azure 论坛 [并 Stack Overflow](https://azure.microsoft.com/support/forums/)。 可将问题发布到这些论坛上，或发布到 [Twitter 上的 @AzureSupport](https://twitter.com/AzureSupport)。 还可提交 Azure 支持请求。 若要提交支持请求，请在 [Azure 支持](https://azure.microsoft.com/support/options/)页上，选择“获取支持”。
