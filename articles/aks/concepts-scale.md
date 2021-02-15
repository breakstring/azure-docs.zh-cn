---
title: 概念 - 在 Azure Kubernetes 服务 (AKS) 中缩放应用程序
description: 了解 Azure Kubernetes 服务 (AKS) 中的缩放，包括水平 Pod 自动缩放程序、群集自动缩放程序和 Azure 容器实例连接器。
services: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.openlocfilehash: b72ed7cefc6a16eb484e1337dbd64e5f069a2201
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686032"
---
# <a name="scaling-options-for-applications-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 服务 (AKS) 中的应用程序缩放选项

在 Azure Kubernetes 服务 (AKS) 中运行应用程序时，可能需要增加或减少计算资源量。 随着所需应用程序实例数量的变化，可能还需要更改基础 Kubernetes 节点的数量。 可能还需要快速预配大量其他应用程序实例。

本文介绍有助于在 AKS 中缩放应用程序的核心概念：

- [手动缩放](#manually-scale-pods-or-nodes)
- [水平 Pod 自动缩放程序 (HPA)](#horizontal-pod-autoscaler)
- [群集自动缩放程序](#cluster-autoscaler)
- [Azure 容器实例 (ACI) 与 AKS 集成](#burst-to-azure-container-instances)

## <a name="manually-scale-pods-or-nodes"></a>手动缩放 Pod 或节点

可以手动缩放副本 (Pod) 和节点，以测试应用程序如何响应可用资源和状态的更改。 手动缩放资源还可以定义用于维持固定成本的设定数量的资源，例如节点数。 若要手动缩放，请定义副本或节点计数。 然后，Kubernetes API 根据该副本或节点计数计划创建其他 Pod 或排空节点。

缩减节点时，Kubernetes API 将调用与群集使用的计算类型绑定的相关 Azure 计算 API。 例如，对于基于 VM 规模集构建的群集，选择要删除的节点的逻辑由 VM 规模集 API 确定。 若要详细了解如何在缩减节点时选择要删除的节点，请参阅 [VMSS 常见问题解答](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#if-i-reduce-my-scale-set-capacity-from-20-to-15-which-vms-are-removed)。

若要开始使用手动缩放 Pod 和节点，请参阅[在 AKS 中缩放应用程序][aks-scale]。

## <a name="horizontal-pod-autoscaler"></a>水平 Pod 自动缩放程序

Kubernetes 使用水平 Pod 自动缩放程序 (HPA) 来监视资源需求并自动缩放副本数量。 默认情况下，水平 Pod 自动缩放程序每隔 30 秒检查一次指标 API，以了解副本计数所需的任何更改。 当需要进行更改时，副本的数量会相应增加或减少。 水平 Pod 自动缩放程序与已经为 Kubernetes 1.8+ 部署了指标服务器的 AKS 群集配合使用。

![Kubernetes 水平 Pod 自动缩放](media/concepts-scale/horizontal-pod-autoscaling.png)

为给定部署配置水平 Pod 自动缩放程序时，请定义可运行的最小和最大副本数。 还可以定义指标以监视任何缩放决策并以此为依据，例如 CPU 使用情况。

若要开始使用 AKS 中的水平 Pod 自动缩放程序，请参阅[在 AKS 中自动缩放 Pod][aks-hpa]。

### <a name="cooldown-of-scaling-events"></a>缩放事件的冷却时间

由于水平 Pod 自动缩放程序每 30 秒检查一次指标 API，因此在进行另一次检查之前，先前的缩放事件可能尚未成功完成。 此行为可能导致水平 Pod 自动缩放程序会在上一个缩放事件能够接收应用程序工作负荷且需要对资源进行相应调整之前更改副本数。

若要最大程度地减少争用事件，请设置延迟值。 此值定义水平 Pod 自动缩放程序在一个缩放事件之后必须等待多长时间才能触发另一个缩放事件。 此行为允许新副本计数生效，指标 API 反映分布式工作负荷。 [从 Kubernetes 1.12 开始，纵向扩展事件没有延迟](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/#support-for-cooldown-delay)，但是纵向缩减事件的延迟默认为 5 分钟。

目前，无法从默认值调整这些冷却时间值。

## <a name="cluster-autoscaler"></a>群集自动缩放程序

Kubernetes 有一个可响应不断变化的 Pod 需求的群集自动缩放程序，可根据节点池中请求的计算资源调整节点数。 默认情况下，群集自动缩放程序每隔 10 秒检查一次指标 API 服务器，以了解节点计数所需的任何更改。 如果群集自动缩放程序确定需要进行更改，则 AKS 群集中的节点数会相应增加或减少。 Cluster 自动缩放程序适用于启用了 Kubernetes RBAC 的 AKS 群集，这些群集运行 Kubernetes 1.10. x 或更高版本。

![Kubernetes 群集自动缩放程序](media/concepts-scale/cluster-autoscaler.png)

群集自动缩放程序通常与水平 Pod 自动缩放程序配合使用。 当两者组合在一起时，水平 Pod 自动缩放程序会根据应用程序需求增加或减少 Pod 数量，群集自动缩放程序会根据需要调整节点数以相应地运行额外的 Pod。

若要开始使用 AKS 中的群集自动缩放程序，请参阅 [AKS 上的群集自动缩放程序][aks-cluster-autoscaler]。

### <a name="scale-out-events"></a>横向扩展事件

如果节点没有足够的计算资源来运行请求的 Pod，则该 Pod 无法按照计划继续运行。 除非节点池中有其他可用的计算资源，否则无法启动该 Pod。

当群集自动缩放程序通知由于节点池资源限制而无法将 Pod 列入计划时，节点池中的节点数量会增加，提供额外的计算资源。 当这些额外的节点成功部署并可在节点池中使用时，可将 Pod 计划为运行。

如果应用程序需要快速缩放，则某些 Pod 可能会保持等待计划的状态，直到群集自动缩放程序部署的其他节点可以接受列入计划的 Pod。 对于具有高突发需求的应用程序，可以使用虚拟节点和 Azure 容器实例进行缩放。

### <a name="scale-in-events"></a>横向缩减事件

群集自动缩放程序还会监视最近未收到新计划请求的节点的 Pod 计划状态。 此方案表明节点池具有的计算资源多于所需资源，并且可以减少节点数。

默认情况下，计划删除传递超过 10 分钟不再需要的阈值的节点。 发生这种情况时，会计划 Pod 在节点池中的其他节点上运行，并且群集自动缩放程序会减少节点数。

当群集自动缩放程序减少节点数时，由于在不同节点上计划 Pod，应用程序可能会发生一些中断。 为最大限度地减少中断，请避免使用单个 Pod 实例的应用程序。

## <a name="burst-to-azure-container-instances"></a>迸发到 Azure 容器实例

若要快速缩放 AKS 群集，可以与 Azure 容器实例 (ACI) 集成。 Kubernetes 具有内置组件，可缩放副本和节点数。 但是，如果应用程序需要快速缩放，则水平 Pod 自动缩放程序可以计划比节点池中现有计算资源提供的更多 Pod。 如果已配置，则此方案将触发群集自动缩放程序在节点池中部署其他节点，但可能需要几分钟后，这些节点才能成功配置并允许 Kubernetes 计划程序在其上运行 Pod。

![Kubernetes 迸发缩放到 ACI](media/concepts-scale/burst-scaling.png)

使用 ACI，可以快速部署容器实例，而无需额外的基础结构开销。 当与 AKS 连接时，ACI 会成为 AKS 群集的安全逻辑扩展。 [虚拟节点][virtual-nodes-cli]组件（基于[virtual Kubelet][virtual-kubelet]）安装在 AKS 群集中，该群集将 ACI 显示为虚拟 Kubernetes 节点。 然后，Kubernetes 可以计划通过虚拟节点作为 ACI 实例运行的 Pod，而不是直接在 AKS 群集中的 VM 节点上运行的 Pod。

应用程序无需修改即可使用虚拟节点。 集群自动缩放程序在 AKS 集群中部署新节点后，部署可以跨 AKS 和 ACI 进行缩放，且没有延迟。

虚拟节点将部署到与 AKS 群集相同的虚拟网络中的其他子网。 此虚拟网络配置可以保护 ACI 和 AKS 之间的流量。 与 AKS 群集一样，ACI 实例是独立于其他用户的安全逻辑计算资源。

## <a name="next-steps"></a>后续步骤

若要开始缩放应用程序，请首先按照[使用 Azure CLI 创建 AKS 群集的快速入门][aks-quickstart]进行操作。 然后，可以开始手动或自动缩放 AKS 群集中的应用程序：

- 手动缩放 [Pod][aks-manually-scale-pods] 或[节点][aks-manually-scale-nodes]
- 使用[水平 Pod 自动缩放程序][aks-hpa]
- 使用[群集自动缩放程序][aks-cluster-autoscaler]

有关核心 Kubernetes 和 AKS 概念的详细信息，请参阅以下文章：

- [Kubernetes/AKS 群集和工作负荷][aks-concepts-clusters-workloads]
- [Kubernetes/AKS 访问和标识][aks-concepts-identity]
- [Kubernetes/AKS 安全性][aks-concepts-security]
- [Kubernetes/AKS 虚拟网络][aks-concepts-network]
- [Kubernetes/AKS 存储][aks-concepts-storage]

<!-- LINKS - external -->
[virtual-kubelet]: https://virtual-kubelet.io/

<!-- LINKS - internal -->
[aks-quickstart]: kubernetes-walkthrough.md
[aks-hpa]: tutorial-kubernetes-scale.md#autoscale-pods
[aks-scale]: tutorial-kubernetes-scale.md
[aks-manually-scale-pods]: tutorial-kubernetes-scale.md#manually-scale-pods
[aks-manually-scale-nodes]: tutorial-kubernetes-scale.md#manually-scale-aks-nodes
[aks-cluster-autoscaler]: ./cluster-autoscaler.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-network]: concepts-network.md
[virtual-nodes-cli]: virtual-nodes-cli.md
