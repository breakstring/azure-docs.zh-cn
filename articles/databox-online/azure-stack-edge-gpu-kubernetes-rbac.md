---
title: 了解 Azure Stack Edge Pro 设备上基于角色的访问控制 Kubernetes |Microsoft Docs
description: 描述如何在 Azure Stack Edge Pro 设备上执行基于角色的访问控制 Kubernetes。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: alkohli
ms.openlocfilehash: 9a9625dcf40ae7d11e1154fc89b7f04652c8ca16
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2020
ms.locfileid: "94635834"
---
# <a name="kubernetes-role-based-access-control-on-your-azure-stack-edge-pro-gpu-device"></a>在 Azure Stack Edge Pro GPU 设备上 Kubernetes 基于角色的访问控制


在 Azure Stack Edge Pro 设备上，配置计算角色时，会创建 Kubernetes 群集。 可以使用 Kubernetes 的基于角色的访问控制 (Kubernetes RBAC) 来限制对设备上群集资源的访问。

本文概述了 Kubernetes 提供的 Kubernetes RBAC 系统，以及如何在 Azure Stack Edge Pro 设备上实现 Kubernetes RBAC。 

## <a name="kubernetes-rbac"></a>Kubernetes RBAC

Kubernetes RBAC 允许您分配用户或用户组、执行操作（如创建或修改资源）或查看运行应用程序工作负荷的日志。 这些权限可以限定为单个命名空间，也可以在整个群集中授予。 

设置 Kubernetes 群集时，将创建对应于此群集的单个用户，并将其称为群集管理员用户。  `kubeconfig`文件与群集管理员用户相关联。 `kubeconfig`文件是一个文本文件，其中包含连接到群集以对用户进行身份验证所需的所有配置信息。

## <a name="namespaces-types"></a>命名空间类型

Kubernetes 资源（如 pod 和部署）按逻辑分组到一个命名空间中。 这些分组提供了一种逻辑划分 Kubernetes 群集的方法，并限制了创建、查看或管理资源的访问权限。 用户只能与分配的命名空间内的资源进行交互。

命名空间适用于具有多个用户分布在多个团队或项目中的环境。 有关详细信息，请参阅 [Kubernetes 命名空间](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)。

Azure Stack Edge Pro 设备具有以下命名空间：

- **系统命名空间** -此命名空间是核心资源所在的位置，例如 DNS 和代理等网络功能，或 Kubernetes 仪表板。 通常不会将应用程序部署到此命名空间中。 使用此命名空间调试任何 Kubernetes 群集问题。 

    设备上有多个系统命名空间，并且保留了与这些系统命名空间对应的名称。 下面是保留系统命名空间的列表： 
    - kube-系统
    - metallb-系统
    - dbe-命名空间
    - default
    - kubernetes-dashboard
    - kube-租赁
    - kube-公共


    请确保不要将任何保留名称用于创建的用户命名空间。 
<!--- **default namespace** - This namespace is where pods and deployments are created by default when none is provided and you have admin access to this namespace. When you interact with the Kubernetes API, such as with `kubectl get pods`, the default namespace is used when none is specified.-->

- **用户命名空间** -这些是可以通过 **kubectl** 创建的命名空间，也可以是通过设备的 PowerShell 接口创建以本地方式部署应用程序的命名空间。
 
- **IoT Edge 命名空间** -你连接到此 `iotedge` 命名空间以管理通过 IoT Edge 部署的应用程序。

- **Azure arc 命名空间** -连接到此 `azure-arc` 命名空间，管理通过 Azure Arc 部署的应用程序。利用 Azure Arc，还可以将应用程序部署到其他用户命名空间中。 

## <a name="namespaces-and-users"></a>命名空间和用户

在现实生活中，将群集划分为多个命名空间非常重要。 

- **多个用户** ：如果你有多个用户，则多个命名空间将允许这些用户将其应用程序和服务分别部署在其特定的命名空间中。 
- **单个用户** ：即使只有一个用户，多个命名空间也允许该用户在同一个 Kubernetes 群集中运行多个版本的应用程序。

### <a name="roles-and-rolebindings"></a>Role 和 RoleBindings

Kubernetes 具有角色和角色绑定的概念，使你能够在命名空间级别和群集级别向用户或资源提供权限。 

- **角色** ：你可以将对用户的权限定义为 **角色** ，然后使用 **角色** 授予命名空间中的权限。 
- **RoleBindings** ：定义角色后，可以使用 **RoleBindings** 为给定命名空间分配角色。 

此方法可让你以逻辑方式隔离单个 Kubernetes 群集，并且用户只能访问其已分配命名空间中的应用程序资源。 

## <a name="kubernetes-rbac-on-azure-stack-edge-pro"></a>Azure Stack Edge Pro 上的 Kubernetes RBAC

在 Kubernetes RBAC 的当前实现中，Azure Stack Edge Pro 允许从受限制的 PowerShell 运行空间执行以下操作：

- 创建命名空间。  
- 创建其他用户。
- 向你授予对你创建的命名空间的管理员访问权限。 请记住，您不能访问群集管理员角色或群集范围内的资源。
- 获取 `kubeconfig` 包含信息的文件以访问 Kubernetes 群集。


Azure Stack Edge Pro 设备具有多个系统命名空间，你可以创建具有文件的用户命名空间 `kubeconfig` 来访问这些命名空间。 用户可以完全控制这些命名空间，可以创建或修改用户，也可以授予用户访问权限。 只有群集管理员对系统命名空间和群集范围内的资源具有完全访问权限。 对 `aseuser` 系统命名空间具有只读访问权限。

以下图表描述了 Azure Stack Edge Pro 设备上 Kubernetes RBAC 的实现。

![Azure Stack Edge Pro 设备上的 Kubernetes RBAC](./media/azure-stack-edge-gpu-kubernetes-rbac/rbac-view-1.png)

在此关系图中，Alice、Bob 和 Chuck 只能访问分配的用户命名空间，在这种情况下，这种命名空间 `ns1` 分别为、 `ns2` 和 `ns3` 。 在这些命名空间内，它们具有管理访问权限。 另一方面，群集管理员可以管理对系统命名空间和群集范围内资源的访问权限。

用户可以创建命名空间和用户、将用户分配到命名空间或下载 `kubeconfig` 文件。 有关详细的分步说明，请参阅 [Azure Stack Edge Pro 上的通过 Kuebctl 访问 Kubernetes 群集](azure-stack-edge-gpu-create-kubernetes-cluster.md)。


在 Azure Stack Edge Pro 设备上使用命名空间和用户时，请注意以下事项：

- 对于任何系统命名空间，不允许执行任何操作，如创建用户、授予或撤消命名空间访问权限。 系统命名空间的示例包括 `kube-system` 、 `metallb-system` 、、 `kubernetes-dashboard` `default` 、 `kube-node-lease` 和 `kube-public` 。 系统命名空间还包括为部署类型保留的命名空间 `iotedge` ，如 (IoT Edge 命名空间) 和 `azure-arc` (Azure Arc 命名空间) 。
- 你可以创建用户命名空间，并在这些命名空间内创建其他用户，并授予或撤消对这些用户的命名空间访问权限。
- 不允许创建名称与任何系统命名空间相同的命名空间。 系统命名空间的名称是保留名称。  
- 不允许创建名称已被其他用户命名空间使用的任何用户命名空间。 例如，如果你具有创建的 `test-ns` ，则不能创建另一个 `test-ns` 命名空间。
- 不允许你创建具有已保留名称的用户。 例如， `aseuser` 是一个保留用户，不能使用。


## <a name="next-steps"></a>后续步骤

若要了解如何创建用户、创建命名空间以及向用户授予对命名空间的访问权限，请参阅 [通过 Kubectl 访问 Kubernetes 群集](azure-stack-edge-gpu-create-kubernetes-cluster.md)。

