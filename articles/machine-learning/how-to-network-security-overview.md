---
title: 虚拟网络隔离和安全概述
titleSuffix: Azure Machine Learning
description: 使用与 Azure 机器学习隔离的 Azure 虚拟网络来保护工作区资源和计算环境。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 10/06/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, references_regions, contperf-fy21q1
ms.openlocfilehash: 857fba6dfa6191163c06c423cefb42d57f25dc1d
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2021
ms.locfileid: "99980569"
---
# <a name="virtual-network-isolation-and-privacy-overview"></a>虚拟网络隔离和隐私概述

本文介绍如何使用虚拟网络 (Vnet) 在 Azure 机器学习中保护网络通信。 本文使用一个示例方案来说明如何配置完整的虚拟网络。

本文是由五部分组成的系列文章的第一部分，指导你完成保护 Azure 机器学习工作流的工作。 我们强烈建议您通读此概述文章，以首先了解相关概念。 

下面是此系列中的其他文章：

**1. VNet 概述**  >  [2。保护工作区](how-to-secure-workspace-vnet.md)  >  [3。保护定型环境](how-to-secure-training-vnet.md)  >  [4。保护推断环境](how-to-secure-inferencing-vnet.md)  >  [5。启用 studio 功能](how-to-enable-studio-virtual-network.md)

## <a name="prerequisites"></a>先决条件

本文假定您已熟悉以下主题：
+ [Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)
+ [IP 网络](../virtual-network/public-ip-addresses.md)
+ [Azure 专用链接](how-to-configure-private-link.md)
+ [ (NSG 的网络安全组) ](../virtual-network/network-security-groups-overview.md)
+ [网络防火墙](../firewall/overview.md)

## <a name="example-scenario"></a>示例方案

在本部分中，你将了解如何设置一个通用的网络方案来保护 Azure 机器学习与专用 IP 地址的通信。

下表比较了服务如何使用 VNet 而不使用 VNet 访问 Azure 机器学习网络的不同部分。

| 方案 | 工作区 |  关联的资源 | 训练计算环境 | 推断计算环境 |
|-|-|-|-|-|-|
|**无虚拟网络**| 公共 IP | 公共 IP | 公共 IP | 公共 IP |
|**保护虚拟网络中的资源**| 专用 IP (专用终结点)  | 公共 IP (服务终结点)  <br> **或** <br> 专用 IP (专用终结点)  | 专用 IP | 专用 IP  | 

* **工作区** -从 VNet 创建专用终结点，以连接到工作区上的专用链接。 专用终结点通过多个专用 IP 地址将工作区连接到 vnet。
* **关联的资源** -使用服务终结点或专用终结点连接到工作区资源，如 azure 存储、Azure Key Vault 和 Azure 容器服务。
    * **服务终结点** 为 Azure 服务提供虚拟网络的标识。 在虚拟网络中启用服务终结点后，可以添加虚拟网络规则，以在虚拟网络中保护 Azure 服务资源。 服务终结点使用公共 IP 地址。
    * **专用终结点** 是网络接口，可安全地连接到由 Azure 专用链接提供支持的服务。 专用终结点使用 VNet 中的专用 IP 地址，从而有效地将服务引入 VNet。
* 使用专用 IP 地址，**培训计算访问**-访问的培训计算目标，如 Azure 机器学习计算实例和 Azure 机器学习计算群集。 
* **推断计算访问** -访问 Azure Kubernetes 服务 (AKS) 使用专用 IP 地址的计算群集。


接下来的五个部分介绍了如何保护上述网络方案。 若要保护网络，必须：

1. 保护 [**工作区和关联的资源**](#secure-the-workspace-and-associated-resources)。
1. 保护 [**培训环境**](#secure-the-training-environment)。
1. 保护 [**推断环境**](#secure-the-inferencing-environment)。
1. （可选）： [**启用 studio 功能**](#optional-enable-studio-functionality)。
1. 配置 [**防火墙设置**](#configure-firewall-settings)

## <a name="secure-the-workspace-and-associated-resources"></a>保护工作区和关联的资源

使用以下步骤来保护工作区和关联的资源。 这些步骤允许你的服务在虚拟网络中进行通信。

1. 创建 [启用了专用链接的工作区](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint) ，以启用 VNet 与工作区之间的通信。
1. 使用 [服务终结](../key-vault/general/overview-vnet-service-endpoints.md) 点或 [专用终结点](../key-vault/general/private-link-service.md)将 Azure Key Vault 添加到虚拟网络。 将 Key Vault 设置为 ["允许受信任的 Microsoft 服务跳过此防火墙"](how-to-secure-workspace-vnet.md#secure-azure-key-vault)。
1. 使用 [服务终结](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints) 点或 [专用终结点](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-private-endpoints)将 Azure 存储帐户添加到虚拟网络。
1. [将 Azure 容器注册表配置为使用专用终结点](how-to-secure-workspace-vnet.md#enable-azure-container-registry-acr) ，并 [在 Azure 容器实例中启用子网委托](how-to-secure-inferencing-vnet.md#enable-azure-container-instances-aci)。

![显示工作区和关联资源如何通过服务终结点或 VNet 内的专用终结点相互通信的体系结构关系图](./media/how-to-network-security-overview/secure-workspace-resources.png)

有关如何完成这些步骤的详细说明，请参阅 [保护 Azure 机器学习工作区](how-to-secure-workspace-vnet.md)。 

### <a name="limitations"></a>限制

保护工作区和虚拟网络中的关联资源具有以下限制：
- 在 Azure 政府版或 Azure 中国世纪互联区域中，使用具有专用链接的 Azure 机器学习工作区不可用。
- 所有资源都必须位于同一个 VNet 中。 但允许使用同一 VNet 中的子网。

## <a name="secure-the-training-environment"></a>保护定型环境

本部分介绍如何在 Azure 机器学习中保护培训环境。 你还将了解 Azure 机器学习如何完成训练作业来了解网络配置如何协同工作。

若要保护定型环境，请使用以下步骤：

1. [在虚拟网络中创建一个 Azure 机器学习计算实例和计算机群集](how-to-secure-training-vnet.md#compute-instance)，以运行定型作业。
1. [允许来自 Azure Batch 服务的入站通信](how-to-secure-training-vnet.md#mlcports) ，以便 Batch 服务可以将作业提交到你的计算资源。 

![显示如何保护托管计算群集和实例的体系结构示意图](./media/how-to-network-security-overview/secure-training-environment.png)

有关如何完成这些步骤的详细说明，请参阅 [保护培训环境](how-to-secure-training-vnet.md)。 

### <a name="example-training-job-submission"></a>训练作业提交示例 

本部分介绍如何 Azure 机器学习安全地在服务之间进行通信，以提交培训作业。 这会显示所有配置如何协同工作以确保通信安全。

1. 客户端将训练脚本和培训数据上传到使用服务或专用终结点保护的存储帐户。

1. 客户端通过专用终结点将定型作业提交到 Azure 机器学习工作区。

1. Azure Batch 服务从工作区接收作业，并通过使用计算资源预配的公共负载均衡器将定型作业提交到计算环境。 

1. 计算资源接收作业并开始定型。 计算资源访问安全存储帐户以下载培训文件和上传输出。 

![显示如何在使用 VNet 时提交 Azure 机器学习培训作业的体系结构关系图](./media/how-to-network-security-overview/secure-training-job-submission.png)


### <a name="limitations"></a>限制

- Azure 计算实例和 Azure 计算群集必须与工作区及其关联的资源位于同一 VNet、区域和订阅中。 

## <a name="secure-the-inferencing-environment"></a>保护推断环境

本部分介绍可用于保护推断环境的选项。 建议使用 Azure Kubernetes Services (AKS) 群集进行大规模的生产部署。

虚拟网络中的 AKS 群集有两个选项：

- 将默认 AKS 群集部署或附加到 VNet。
- 将专用 AKS 群集附加到 VNet。

**默认 AKS 群集** 具有一个具有公共 IP 地址的控制平面。 可以在部署过程中将默认的 AKS 群集添加到 VNet，也可以在创建群集后附加群集。

**专用 AKS 群集** 具有控制平面，只能通过专用 ip 进行访问。 创建群集后，必须附加专用 AKS 群集。

有关如何添加默认群集和专用群集的详细说明，请参阅 [保护推断环境](how-to-secure-inferencing-vnet.md)。 

以下网络示意图显示了一个受保护的 Azure 机器学习工作区，其中包含附加到虚拟网络的专用 AKS 群集。

![显示如何将专用 AKS 群集附加到虚拟网络的体系结构关系图。 AKS 控制平面置于 customer VNet 之外](./media/how-to-network-security-overview/secure-inferencing-environment.png)

### <a name="limitations"></a>限制
- AKS 群集必须与工作区及其关联资源属于同一 VNet。 

## <a name="optional-enable-public-access"></a>可选：启用公共访问

你可以使用专用终结点保护 VNet 后面的工作区，并且仍允许通过公共 internet 进行访问。 初始配置与 [保护工作区和关联资源](#secure-the-workspace-and-associated-resources)的方式相同。 

使用专用链接保护工作区后，将 [启用公共访问](how-to-configure-private-link.md#enable-public-access)。 此后，你可以从公共 internet 和 VNet 访问工作区。

### <a name="limitations"></a>限制

- 如果通过公共 internet 使用 Azure 机器学习 studio，则设计器等某些功能可能无法访问你的数据。 如果数据存储在 VNet 保护的服务中，则会发生此问题。 例如，Azure 存储帐户。
## <a name="optional-enable-studio-functionality"></a>可选：启用 studio 功能

[保护工作区](#secure-the-workspace-and-associated-resources)  > [保护定型环境](#secure-the-training-environment)  > [保护推断环境](#secure-the-inferencing-environment)  > **启用 studio 功能**  > [配置防火墙设置](#configure-firewall-settings)

如果存储在 VNet 中，则必须先执行其他配置步骤，以便在 [工作室](overview-what-is-machine-learning-studio.md)中启用全部功能。 默认情况下，禁用以下功能：

* 预览工作室中的数据。
* 在设计器中将数据可视化。
* 在设计器中部署模型。
* 提交 AutoML 试验。
* 启动标记项目。

若要在 VNet 内部启用完整的 studio 功能，请参阅 [在虚拟网络中使用 Azure 机器学习 studio](how-to-enable-studio-virtual-network.md#configure-data-access-in-the-studio)。 Studio 支持使用服务终结点或专用终结点的存储帐户。

### <a name="limitations"></a>限制

[ML 辅助数据标记](how-to-create-labeling-projects.md#use-ml-assisted-data-labeling) 不支持在虚拟网络后保护的默认存储帐户。 对于 ML 辅助数据标签，必须使用非默认存储帐户。 请注意，非默认存储帐户可在虚拟网络后受到保护。 

## <a name="configure-firewall-settings"></a>配置防火墙设置

将防火墙配置为控制对 Azure 机器学习工作区资源和公共 internet 的访问。 尽管我们建议使用 Azure 防火墙，但你应该能够使用其他防火墙产品来保护你的网络。 如果你对如何允许通过防火墙进行通信有疑问，请查阅你正在使用的防火墙的相关文档。

有关防火墙设置的详细信息，请参阅 [使用防火墙后面的工作区](how-to-access-azureml-behind-firewall.md)。

## <a name="custom-dns"></a>自定义 DNS

如果需要为虚拟网络使用自定义 DNS 解决方案，则必须为工作区添加主机记录。

有关所需域名和 IP 地址的详细信息，请参阅 [如何将工作区与自定义 DNS 服务器配合使用](how-to-custom-dns.md)。

## <a name="next-steps"></a>后续步骤

本文是由四部分构成的虚拟网络系列中的第一部分。 若要了解如何保护虚拟网络，请参阅其余文章：

* [第2部分：虚拟网络概述](how-to-secure-workspace-vnet.md)
* [第 3 部分：保护训练环境](how-to-secure-training-vnet.md)
* [第 4 部分：保护推理环境](how-to-secure-inferencing-vnet.md)
* [第 5 部分：启用工作室功能](how-to-enable-studio-virtual-network.md)