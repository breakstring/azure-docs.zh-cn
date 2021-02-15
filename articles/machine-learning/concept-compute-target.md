---
title: 什么是计算目标
titleSuffix: Azure Machine Learning
description: 了解如何指定计算资源或环境，以使用 Azure 机器学习培训或部署模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 09/29/2020
ms.openlocfilehash: 1ff20ab19390dcdadb9a4aa79822f7ace3d31989
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791166"
---
# <a name="what-are-compute-targets-in-azure-machine-learning"></a>什么是 Azure 机器学习中的计算目标?

计算目标是指定的计算资源或环境，用来运行训练脚本或托管服务部署。 此位置可以是你的本地计算机，也可以是基于云的计算资源。 如果使用计算目标，以后无需更改代码即可轻松更改计算环境。

在典型的模型开发生命周期中，你可以：

1. 首先，基于少量数据进行开发和试验。 在此阶段，请使用本地环境（如本地计算机或基于云的虚拟机 (VM)）作为计算目标。
1. 通过使用其中一种[训练计算目标](#train)，纵向扩展到更多的数据或进行分布式训练。
1. 在模型准备就绪后，请使用其中一种[部署计算目标](#deploy)将该模型部署到 Web 托管环境或 IoT 设备。

你用于计算目标的计算资源附加到[工作区](concept-workspace.md)。 本地计算机以外的计算资源由工作区的用户共享。

## <a name="training-compute-targets"></a><a name="train"></a> 训练计算目标

Azure 机器学习为不同的计算目标提供不同的支持。 典型的模型开发生命周期从针对少量数据进行开发或试验开始。 在此阶段，请使用本地环境，如本地计算机或基于云的 VM。 在针对更大的数据集纵向扩展训练或执行分布式训练时，请使用 Azure 机器学习计算来创建可在每次提交运行时自动缩放的单节点或多节点群集。 你也可以附加自己的计算资源，不过，为不同方案提供的支持可能会有所不同。

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

详细了解如何[将训练运行提交到计算目标](how-to-set-up-training-targets.md)。

## <a name="compute-targets-for-inference"></a><a name="deploy"></a>用于推理的计算目标

以下计算资源可用来托管模型部署。

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

执行推理时，Azure 机器学习会创建托管模型和使用该模型所需的关联资源的 Docker 容器。 然后，在以下任一部署场景中使用此容器：

* 作为用于实时推理的 Web 服务。 Web 服务部署使用以下计算目标之一：

    * [本地计算机](how-to-attach-compute-targets.md#local)
    * [Azure 机器学习计算实例](how-to-create-manage-compute-instance.md)
    * [Azure 容器实例](how-to-attach-compute-targets.md#aci)
    * [Azure Kubernetes 服务](how-to-create-attach-kubernetes.md)
    * Azure Functions（预览版）。 部署到 Functions 仅依赖 Azure 机器学习来生成 Docker 容器。 从该容器中，通过使用 Functions 来部署机器学习模型。 有关详细信息，请参阅[将机器学习模型部署到 Azure Functions（预览版）](how-to-deploy-functions.md)。

* 作为用于定期处理批量数据的“批量推理”终结点。 批量推理使用 [Azure 机器学习计算群集](how-to-create-attach-compute-cluster.md)。

* 转到 IoT 设备（预览版）。 IoT 设备的部署仅依赖 Azure 机器学习来生成 Docker 容器。 从该容器中，通过使用 Azure IoT Edge 来部署机器学习模型。 有关详细信息，请参阅[部署为 IoT Edge 模块（预览版）](../iot-edge/tutorial-deploy-machine-learning.md)。

了解[在何处以及如何将模型部署到计算目标](how-to-deploy-and-where.md)。

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure 机器学习计算（托管）

托管计算资源是由 Azure 机器学习创建和管理的。 此计算针对机器学习工作负荷进行了优化。 Azure 机器学习计算群集和[计算实例](concept-compute-instance.md)是仅有的托管计算。

可以通过以下方法创建 Azure 机器学习计算实例或计算群集：

* [Azure 机器学习工作室](how-to-create-attach-compute-studio.md)。
* Python SDK 和 CLI：
    * [计算实例](how-to-create-manage-compute-instance.md)。
    * [计算群集](how-to-create-attach-compute-cluster.md)。
* [R SDK](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-compute-targets)（预览版）。
* Azure 资源管理器模板。 有关示例模板，请参阅[创建 Azure 机器学习计算群集](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-amlcompute)。
* [Azure CLI 的机器学习扩展](reference-azure-machine-learning-cli.md#resource-management)。

在创建时，这些计算资源会自动成为工作区的一部分，这一点与其他类型的计算目标不同。


|功能  |计算群集  |计算实例  |
|---------|---------|---------|
|单节点或多节点群集     |    **&check;**       |         |
|每次提交运行时自动缩放     |     **&check;**      |         |
|自动化群集管理和作业计划     |   **&check;**        |     **&check;**      |
|为 CPU 和 GPU 资源提供支持     |  **&check;**         |    **&check;**       |


> [!NOTE]
> 计算群集在处于空闲状态时会自动缩放到 0 个节点，因此，在未使用它时无需付费。 计算实例始终处于启用状态，并且不会自动缩放。 在未使用计算实例时，应[停止计算实例](how-to-create-manage-compute-instance.md#manage)，以免产生额外费用。

### <a name="supported-vm-series-and-sizes"></a>支持的 VM 系列和大小

为 Azure 机器学习中的托管计算资源选择节点大小时，可以从 Azure 提供的选定 VM 大小中进行选择。 Azure 针对不同工作负载为 Linux 和 Windows 提供了一系列大小。 若要了解详细信息，请参阅 [VM 类型和大小](../virtual-machines/sizes.md)。

选择 VM 大小时有几个例外和限制：

* Azure 机器学习不支持某些 VM 系列。
* 某些 VM 系列是受限制的。 若要使用受限制的系列，请与支持团队联系并请求为该系列增加配额。 若要了解如何联系支持人员，请参阅 [Azure 支持选项](https://azure.microsoft.com/support/options/)。

请查看下表，了解有关支持的系列和限制的详细信息。

| **支持的 VM 系列**  | **限制** |
|------------|------------|
| D | 无。 |
| Dv2 | 无。 |  
| Dv3 | 无。|
| DSv2 | 无。 | 
| DSv3 | 无。|
| FSv2 | 无。 | 
| HBv2 | 需要审批。 |  
| HCS | 需要审批。 |  
| M | 需要审批。 |
| NC | 无。 |    
| NCsv2 | 需要审批。 |
| NCsv3 | 需要审批。 |  
| NDs | 需要审批。 |
| NDv2 | 需要审批。 |
| NV | 无。 |
| NVv3 | 需要审批。 | 


虽然 Azure 机器学习支持这些 VM 系列，但它们可能并非在所有 Azure 区域中均可用。 若要检查 VM 系列是否可用，请参阅[可用产品（按区域）](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)。

> [!NOTE]
> Azure 机器学习不支持 Azure 计算支持的所有 VM 大小。 若要列出可用的 VM 大小，请使用以下某种方法：
> * [REST API](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable/2020-08-01/examples/ListVMSizesResult.json)
> * [Python SDK](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py.md#supported-vmsizes-workspace--location-none-)
>

### <a name="compute-isolation"></a>计算隔离

Azure 机器学习计算提供已隔离到特定硬件类型并专用于单个客户的 VM 大小。 独立 VM 大小最适合为满足合规性和监管要求等原因而需要与其他客户的工作负载高度隔离的工作负载。 使用独立大小可保证你的 VM 将是该特定服务器实例上唯一运行的 VM。

当前的独立 VM 产品/服务包括：

* Standard_M128ms
* Standard_F72s_v2
* Standard_NC24s_v3
* Standard_NC24rs_v3*

*支持 RDMA

若要了解有关隔离的详细信息，请参阅 [Azure 公有云中的隔离](../security/fundamentals/isolation-choices.md)。

## <a name="unmanaged-compute"></a>非托管计算

非托管计算目标不是由 Azure 机器学习托管的。 请在 Azure 机器学习外部创建此类型的计算目标，然后将其附加到工作区。 对于非托管计算资源，可能需要执行额外的步骤才能保持或提高机器学习工作负荷的性能。

## <a name="next-steps"></a>后续步骤

了解如何：
* [使用计算目标训练模型](how-to-set-up-training-targets.md)
* [将模型部署到计算目标](how-to-deploy-and-where.md)