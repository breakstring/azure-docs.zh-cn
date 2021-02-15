---
title: 将节点类型添加到 Azure Service Fabric 群集
description: 了解如何通过添加虚拟机规模集横向扩展 Service Fabric 群集。
ms.topic: article
ms.date: 02/13/2019
ms.openlocfilehash: efd329c07b4881c6710d4173857b4186965438d8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88719319"
---
# <a name="scale-a-service-fabric-cluster-out-by-adding-a-virtual-machine-scale-set"></a>通过添加虚拟机规模集扩大 Service Fabric 群集
本文介绍如何通过将新的节点类型添加到现有群集来扩大 Azure Service Fabric 群集。 Service Fabric 群集是一组通过网络连接在一起的虚拟机或物理计算机，微服务会在其中部署和管理。 属于群集一部分的计算机或 VM 称为节点。 虚拟机规模集是一种 Azure 计算资源，用于将一组 VM 作为一个集进行部署和管理。 Azure 群集中定义的每个节点类型[设置为独立的规模集](service-fabric-cluster-nodetypes.md)。 然后可以单独管理每个节点类型。 创建 Service Fabric 群集之后，可以通过将新的节点类型（虚拟机规模集）添加到现有群集来水平缩放群集。  随时可以缩放群集，即使该群集上正在运行工作负荷。  在缩放群集的同时，应用程序也会随之自动缩放。

## <a name="add-an-additional-scale-set-to-an-existing-cluster"></a>将其它规模集添加到现有群集
将新的节点类型（由虚拟机规模集支持）添加到现有群集，该操作类似于[升级主节点类型](service-fabric-scale-up-primary-node-type.md)，但不会使用相同的 NodeTypeRef；很显然，这不会禁用任何活跃使用的虚拟机规模集，并且如果不更新主节点类型，将不会失去群集可用性。 

NodeTypeRef 属性在虚拟机规模集 Service Fabric 扩展属性中声明：
```json
<snip>
"publisher": "Microsoft.Azure.ServiceFabric",
     "settings": {
     "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
     "nodeTypeRef": "[parameters('vmNodeType2Name')]",
     "dataPath": "D:\\\\SvcFab",
     "durabilityLevel": "Silver",
<snip>
```

此外，需要将此新节点类型添加到 Service Fabric 群集资源：

```json
<snip>
"nodeTypes": [
      {
      "name": "[parameters('vmNodeType2Name')]",
      "applicationPorts": {
                "endPort": "[parameters('nt2applicationEndPort')]",
                "startPort": "[parameters('nt2applicationStartPort')]"
      },
      "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
      "durabilityLevel": "Silver",
       "ephemeralPorts": {
                "endPort": "[parameters('nt2ephemeralEndPort')]",
                "startPort": "[parameters('nt2ephemeralStartPort')]"
      },
      "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
      "isPrimary": false,
      "vmInstanceCount": "[parameters('nt2InstanceCount')]"
},
<snip>
```

## <a name="next-steps"></a>后续步骤
* 了解如何[纵向扩展主节点类型](service-fabric-scale-up-primary-node-type.md)
* 了解[应用程序可伸缩性](service-fabric-concepts-scalability.md)。
* [横向扩展或缩减 Azure 群集](service-fabric-tutorial-scale-cluster.md)
* 使用 fluent Azure 计算 SDK [以编程方式缩放 Azure 群集](service-fabric-cluster-programmatic-scaling.md)。
* [横向扩展或缩减独立群集](service-fabric-cluster-windows-server-add-remove-nodes.md)。

