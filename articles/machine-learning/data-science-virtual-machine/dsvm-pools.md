---
title: 共享池
titleSuffix: Azure Data Science Virtual Machine
description: 了解如何创建 Data Science Virtual Machine (DSVM) 共享池并将其部署为团队的共享资源。
keywords: 深度学习, AI, 数据科学工具, 数据科学虚拟机, 地理空间分析, 团队数据科学过程
services: machine-learning
ms.service: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 12/10/2018
ms.openlocfilehash: a118d5a3e716a80bda21ffe82a8cefd1da1202f3
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519705"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>创建数据科学虚拟机共享池

本文将介绍如何为团队创建 Data Science Virtual Machine (DSVM) 共享池。 使用共享池的好处包括：提高资源利用率、方便共享和协作以及提高管理 DSVM 资源的效率。

可以使用许多方法和技术创建 DSVM 池。 本文重点介绍交互式虚拟机 (VM) 池。 另一个可选的托管计算基础结构是 Azure 机器学习计算。 有关详细信息，请参阅[创建计算群集](../how-to-create-attach-compute-cluster.md)。

## <a name="interactive-vm-pool"></a>交互式 VM 池

使用供整个 AI/数据科学团队共享的交互式 VM 池，用户可以登录到提供的 DSVM 实例，因此不必为每组用户分配专用的实例。 此安装程序可以提高资源的可用性和利用效率。

可使用 [Azure 虚拟机规模集](../../virtual-machine-scale-sets/index.yml)技术来创建交互式 VM 池。 可以使用规模集创建并管理一组完全相同的、负载均衡的、自动缩放的 VM。

用户登录到主池的 IP 或 DNS 地址。 规模集将会话自动路由到规模集中的可用 DSVM。 由于用户希望不管登录到什么 VM 都能使用一致且熟悉的环境，因此规模集中的所有 VM 实例都会装载共享网络驱动器，例如 Azure 文件共享或网络文件系统 (NFS) 共享。 用户的共享工作区通常保留在已装载到每个实例上的共享文件存储上。

[GitHub](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json) 上提供了一个示例性的 Azure 资源管理器模板，用于通过 Ubuntu DSVM 实例创建规模集。 同一位置还提供了 Azure 资源管理器模板的[参数文件](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json)示例。

可以在 Azure CLI 中指定参数文件的值，以便通过 Azure 资源管理器模板创建规模集：

```azurecli-interactive
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az deployment group create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```

前面的命令假定你有：

* 一个参数文件副本，其中包含为规模集实例指定的值。
* VM 实例数。
* 指向 Azure 文件共享的指针。
* 将在每个 VM 上装载的存储帐户的凭据。

在命令中，参数文件是在本地引用的。 也可通过内联的方式传递参数，或者在脚本中提示用户输入这些参数。  

上面的模板启用 SSH 以及 JupyterHub 端口（从 Ubuntu DSVM 的前端规模集通往后端池）。 请以用户身份通过正常方式登录到安全外壳 (SSH) 或 JupyterHub 上的 VM。 由于 VM 实例可以动态地进行纵向扩展或减小，因此必须将所有状态保存在装载的 Azure 文件共享中。 可以使用同一方法创建 Windows DSVM 池。

GitHub 中的 Azure DataScienceVM 存储库也提供[装载 Azure 文件共享的脚本](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh)。 该脚本在参数文件的指定装载点装载 Azure 文件共享。 该脚本还在初始用户的主目录中创建指向已装载驱动器的软链接。 Azure 文件存储共享中的特定于用户的笔记本目录是指向 `$HOME/notebooks/remote` 目录的软链接，以便用户可以访问、运行和保存其 Jupyter 笔记本。 在 VM 上创建其他用户，以便将每个用户的 Jupyter 工作区指向 Azure 文件共享时，可以使用同一约定。

虚拟机规模集支持自动缩放。 可以设置有关何时创建其他实例以及何时减小实例的规则。 例如，可以纵向缩减至零个实例以在未使用任何 VM 的情况下节省云硬件的使用成本。 VM 规模集的文档页提供了有关[自动缩放](../../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md)的详细步骤。

## <a name="next-steps"></a>后续步骤

* [设置通用标识](dsvm-common-identity.md)
* [以安全方式存储访问云资源的凭据](dsvm-secure-access-keys.md)