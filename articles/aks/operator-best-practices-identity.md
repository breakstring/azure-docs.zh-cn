---
title: 管理身份的最佳做法
titleSuffix: Azure Kubernetes Service
description: 了解有关为 Azure Kubernetes 服务 (AKS) 中的群集管理身份验证和授权的群集操作员最佳做法
services: container-service
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 8e0c7324f5b73b3a2ac5e5fd6fa256202035077a
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790963"
---
# <a name="best-practices-for-authentication-and-authorization-in-azure-kubernetes-service-aks"></a>有关 Azure Kubernetes 服务 (AKS) 中的身份验证和授权的最佳做法

在 Azure Kubernetes 服务 (AKS) 中部署和维护群集时，需要实施相应的方法来管理对资源和服务的访问。 如果不采用这些控制措施，帐户可能会有权访问它们不需要访问的资源和服务。 此外，可能难以跟踪用于做出更改的凭据集。

本最佳做法文章重点介绍群集操作员如何管理 AKS 群集的访问和标识。 在本文中，学习如何：

> [!div class="checklist"]
>
> * 使用 Azure Active Directory 对 AKS 群集用户进行身份验证
> * 使用 Kubernetes 基于角色的访问控制 (Kubernetes RBAC) 控制对资源的访问权限
> * 使用 Azure RBAC 大规模精细控制对 AKS 资源和 Kubernetes API 的访问权限，以及对 kubeconfig 的访问权限。
> * 使用托管标识在其他服务中对 Pod 本身进行身份验证

## <a name="use-azure-active-directory"></a>使用 Azure Active Directory

**最佳做法指导** - 使用 Azure AD 集成部署 AKS 群集。 使用 Azure AD 可以集中化标识管理组件。 访问 AKS 群集时，用户帐户或组状态的任何更改会自动更新。 根据下一部分所述，使用角色或群集角色和绑定，将用户或组的权限范围限定为所需的最少量权限。

Kubernetes 群集的开发人员和应用程序所有者需要访问不同的资源。 Kubernetes 不提供标识管理解决方案来控制哪些用户可与哪些资源交互。 通常，你会将群集与现有的标识解决方案相集成。 Azure Active Directory (AD) 提供企业就绪的标识管理解决方案，并可与 AKS 群集相集成。

使用 AKS 中与 Azure AD 集成的群集，创建角色或群集角色用于定义对资源的访问权限。  然后，从 Azure AD 将角色绑定到用户或组。 下一部分将介绍这些 Kubernetes 基于角色的访问控制 (Kubernetes RBAC)。 下图显示了 Azure AD 集成，以及如何控制对资源的访问：

![与 AKS 集成的 Azure Active Directory 的群集级身份验证](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. 开发人员在 Azure AD 中进行验证身份。
1. Azure AD 令牌颁发终结点颁发访问令牌。
1. 开发人员使用 Azure AD 令牌执行操作，例如 `kubectl create pod`
1. Kubernetes 使用 Azure Active Directory 验证令牌，并提取开发人员的组成员身份。
1. 应用 Kubernetes 基于角色的访问控制 (Kubernetes RBAC) 和群集策略。
1. 开发人员的请求成功或失败，具体取决于前面的 Azure AD 组成员身份和 Kubernetes RBAC 验证以及策略。

若要创建使用 Azure AD 的 AKS 群集，请参阅[将 Azure Active Directory 与 AKS 集成][aks-aad]。

## <a name="use-kubernetes-role-based-access-control-kubernetes-rbac"></a>使用 Kubernetes 基于角色的访问控制 (Kubernetes RBAC)

**最佳做法指导** - 使用 Kubernetes RBAC 定义用户或组对群集中的资源拥有的权限。 创建角色和绑定，用于分配所需的最少量权限。 与 Azure AD 集成，使用户状态或组成员身份的任何更改可自动更新，并使群集资源访问权限保持最新状态。

在 Kubernetes 中，可以针对群集中的资源提供精细的访问控制。 在群集级别或者针对特定的命名空间定义权限。 可以定义能够使用哪些权限管理哪些资源。 然后，将这些角色通过绑定应用于用户或组。 有关角色、群集角色和绑定的详细信息，请参阅 [Azure Kubernetes 服务 (AKS) 的访问和标识选项][aks-concepts-identity]。  

例如，可以创建一个角色，用于授予对名为 *finance-app* 的命名空间中的资源的完全访问权限，如以下示例 YAML 清单中所示：

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role
  namespace: finance-app
rules:
- apiGroups: [""]
  resources: ["*"]
  verbs: ["*"]
```

然后创建 RoleBinding，用于将 Azure AD 用户 *developer1\@contoso.com* 绑定到该 RoleBinding，如以下 YAML 清单所示：

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role-binding
  namespace: finance-app
subjects:
- kind: User
  name: developer1@contoso.com
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: finance-app-full-access-role
  apiGroup: rbac.authorization.k8s.io
```

*developer1\@contoso.com* 通过 AKS 群集进行身份验证后，便对 *finance-app* 命名空间中的资源拥有了完全权限。 这样，即可以逻辑方式隔离和控制对资源的访问权限。 应根据上一部分中所述，将 Kubernetes RBAC 与 Azure AD 集成结合使用。

若要了解如何使用 Azure AD 组通过 Kubernetes RBAC 来控制对 Kubernetes 资源的访问，请参阅[在 AKS 中使用基于角色的访问控制和 Azure Active Directory 标识来控制对群集资源的访问][azure-ad-rbac]。

## <a name="use-azure-rbac"></a>使用 Azure RBAC 
**最佳做法指导** - 使用 Azure RBAC 来定义用户或组对一个或多个订阅中 AKS 资源拥有的所需最低权限。

完全操作 AKS 群集需要两个级别的访问权限： 
1. 访问 Azure 订阅上的 AKS 资源。 具有此访问级别，可以使用 AKS API 来控制群集的缩放或升级，还可以拉取 kubeconfig。
要查看如何控制对 AKS 资源和 kubeconfig 的访问权限，请参阅[限制对群集配置文件的访问](control-kubeconfig-access.md)。

2. 访问 Kubernetes API。 此访问级别可通过 [Kubernetes RBAC](#use-kubernetes-role-based-access-control-kubernetes-rbac)（传统上）进行控制，或通过将 Azure RBAC 与 AKS 集成以实现 Kubernetes 授权来进行控制。
若要了解如何使用 Azure RBAC 以粒度方式向 Kubernetes API 提供权限，请参阅 [使用 AZURE rbac 进行 Kubernetes 授权](manage-azure-rbac.md)。

## <a name="use-pod-managed-identities"></a>使用 Pod 托管标识

**最佳做法指导** - 不要在 pod 或容器映像中使用固定的凭据，因为它们存在泄漏或滥用的风险。 应该使用 pod 标识通过中心 Azure AD 标识解决方案来自动请求访问权限。 Pod 标识仅适用于 Linux Pod 和容器映像。

> [!NOTE]
> Pod Windows 容器的托管标识支持即将推出。

当 pod 需要访问其他 Azure 服务（例如 Cosmos DB、Key Vault 或 Blob 存储）时，pod 需要访问凭据。 可以使用容器映像定义这些访问凭据或将其注入为 Kubernetes 机密，但需要手动创建并分配这些凭据。 通常，凭据会在不同的 pod 之间重复使用，并且不会定期轮换。

Pod-Azure 资源的托管标识使你可以通过 Azure AD 自动请求访问服务。 Pod 托管标识目前以预览版提供 Azure Kubernetes 服务。 若要开始使用，请参阅 [Azure Kubernetes Service 中的使用 Azure Active Directory pod 管理的标识 (预览版) ]( https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity) 文档。 使用 Pod 托管标识，你不会手动定义 Pod 的凭据，而是会实时请求访问令牌，并且可以使用它来仅访问分配给他们的服务。 在 AKS 中，有两个组件用于处理允许 pod 使用托管标识的操作：

* **节点管理标识 (NMI) 服务器** 是在 AKS 群集中每个节点上作为守护程序集运行的 pod。 NMI 服务器侦听发送到 Azure 服务的 pod 请求。
* **Azure 资源提供程序** 将查询 Kubernetes API 服务器，并检查与 pod 相对应的 Azure 标识映射。

当 pod 请求 Azure 服务访问权限时，网络规则会将流量重定向到节点管理标识 (NMI) 服务器。 NMI 服务器根据其远程地址来识别请求访问 Azure 服务的 pod，并查询 Azure 资源提供程序。 Azure 资源提供程序会检查 AKS 群集中的 Azure 标识映射，NMI 服务器会根据 pod 的标识映射从 Azure Active Directory (AD) 请求访问令牌。 Azure AD 提供对 NMI 服务器的访问权限，该访问权限将返回给 pod。 然后，pod 可以使用此访问令牌请求对 Azure 中服务的访问权限。

在以下示例中，开发人员创建使用托管标识请求 Azure SQL 数据库访问权限的 Pod：

![pod 标识可让 pod 自动请求对其他服务的访问权限](media/operator-best-practices-identity/pod-identities.png)

1. 群集操作员首先创建一个服务帐户，当 pod 请求服务访问权限时，可以使用该帐户来映射标识。
1. 将 NMI 服务器部署为与 Azure 资源提供程序一起中继所有 pod 请求，以便 Azure AD 的访问令牌。
1. 开发人员使用托管标识部署一个 pod，该 pod 可通过 NMI 服务器请求访问令牌。
1. 该令牌将返回给 Pod，并用于访问 Azure SQL 数据库

> [!NOTE]
> Pod 托管标识目前处于预览状态。

若要使用 Pod 托管标识，请参阅 [在 Azure Kubernetes 服务中使用 Azure Active Directory Pod 管理的标识 (预览版) ]( https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity)。

## <a name="next-steps"></a>后续步骤

本最佳做法文章重点介绍了群集和资源的身份验证与授权。 若要实施其中的某些最佳做法，请参阅以下文章：

* [将 Azure Active Directory 与 AKS 集成][aks-aad]
* [在 Azure Kubernetes Service (预览版中使用 Azure Active Directory pod 托管标识) ]( https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity)

有关 AKS 中的群集操作的详细信息，请参阅以下最佳做法：

* [多租户和群集隔离][aks-best-practices-cluster-isolation]
* [基本 Kubernetes 计划程序功能][aks-best-practices-scheduler]
* [高级 Kubernetes 计划程序功能][aks-best-practices-advanced-scheduler]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-aad]: azure-ad-integration-cli.md
[managed-identities:]: ../active-directory/managed-identities-azure-resources/overview.md
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[azure-ad-rbac]: azure-ad-rbac.md
