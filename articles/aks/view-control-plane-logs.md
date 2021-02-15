---
title: 查看 Azure Kubernetes 服务 (AKS) 控制器日志
description: '了解如何启用和查看 Azure Kubernetes Service (AKS 中的 Kubernetes 控制平面日志) '
services: container-service
ms.topic: article
ms.date: 01/27/2020
ms.openlocfilehash: 7fce3db6f3636a5ee984c8be44f877c5636f4e16
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948748"
---
# <a name="enable-and-review-kubernetes-control-plane-logs-in-azure-kubernetes-service-aks"></a>启用并查看 Azure Kubernetes 服务中的 Kubernetes 控制平面日志 (AKS) 

使用 Azure Kubernetes Service (AKS) 时，控制平面组件（例如 *kube-apiserver* 和 *kube* ）将作为托管服务提供。 创建和管理运行 *kubelet* 与容器运行时的节点，并通过托管的 Kubernetes API 服务器部署应用程序。 为了帮助排查您的应用程序和服务问题，您可能需要查看这些控制平面组件生成的日志。 本文介绍如何使用 Azure Monitor 日志来启用和查询来自 Kubernetes 控制平面组件的日志。

## <a name="before-you-begin"></a>准备阶段

本文要求在 Azure 帐户中运行一个现有的 AKS 群集。 如果还没有 AKS 群集，请使用 [Azure CLI][cli-quickstart] 或 [Azure 门户][portal-quickstart]创建一个。 Azure Monitor 日志适用于支持 Kubernetes RBAC、Azure RBAC 和非 RBAC 的 AKS 群集。

## <a name="enable-resource-logs"></a>启用资源日志

为帮助收集和审查来自多个源的数据，Azure Monitor 日志提供了查询语言和分析引擎，该引擎可提供环境的见解。 工作区用于整理和分析数据，并可与 Application Insights 和安全中心等其他 Azure 服务集成。 若要使用不同的平台分析日志，可以选择将资源日志发送到 Azure 存储帐户或事件中心。 有关详细信息，请参阅[什么是 Azure Monitor 日志？][log-analytics-overview]。

Azure Monitor 日志是在 Azure 门户中启用和管理的。 若要为 AKS 群集中的 Kubernetes 控制平面组件启用日志收集，请在 web 浏览器中打开 Azure 门户并完成以下步骤：

1. 选择 AKS 群集的资源组，例如 *myResourceGroup*。 不要选择包含单个 AKS 群集资源的资源组，例如 *MC_myResourceGroup_myAKSCluster_eastus*。

2. 在左侧选择“诊断设置”。

3. 选择 AKS 群集（如 *myAKSCluster*），然后选择 " **添加诊断设置**"。
  :::image type="content" source="media\view-control-plane-logs\select-add-diagnostic-setting.PNG" alt-text="浏览器窗口中的 Azure 门户屏幕截图，显示诊断设置，指出应选择 &quot;添加诊断设置&quot;":::

4. 输入名称（例如 myAKSClusterLogs），然后选择“发送到 Log Analytics 工作区”选项。

5. 选择现有工作区或者创建新的工作区。 如果创建工作区，请提供工作区名称、资源组和位置。

6. 在可用日志列表中，选择要启用的日志。 对于本示例，请启用 kube-audit 和 kube-audit-admin 日志 。 常见日志包括 kube-apiserver、kube-controller-manager 和 kube-scheduler。 启用 Log Analytics 工作区后，可以返回并更改收集的日志。

7. 准备就绪后，选择“保存”以启用收集选定日志。
  :::image type="content" source="media\view-control-plane-logs\settings-selected.PNG" alt-text="Azure 门户的 &quot;添加诊断设置&quot; 屏幕的屏幕截图。已选择 &quot;发送到 Log Analytics 工作区&quot; 和日志 &quot;kube&quot; 和 &quot;kube&quot; 的目标":::

## <a name="log-categories"></a>日志类别

除了 Kubernetes 编写的条目，项目的审核日志还包含 来自 AKS 的条目。

审核日志记录为三种类别：kube-audit、kube-audit-admin 和 guard  。

- kube-audit 类别包含每个审核事件的所有审核日志数据，包括 get、list、create、update、delete、patch 和 post       。
- kube-audit-admin 类别是 kube-audit 日志类别的子集 。 kube-audit-admin 通过从日志中排除 get 和 list 审核事件，大大减少了日志数量  。
- guard 类别是托管的 Azure AD 和 Azure RBAC 审核。 对于托管的 Azure AD：输入令牌，输出用户信息。对于 Azure RBAC：输入和输出访问评审。

## <a name="schedule-a-test-pod-on-the-aks-cluster"></a>在 AKS 群集上计划测试 pod

若要生成某些日志，请在 AKS 群集中创建新的 pod。 以下示例 YAML 清单可用于创建一个基本的 NGINX 实例。 在所选的编辑器中创建名为 `nginx.yaml` 的文件，并在其中粘贴以下内容：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  nodeSelector:
    "beta.kubernetes.io/os": linux
  containers:
  - name: mypod
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    ports:
    - containerPort: 80
```

使用 [kubectl create][kubectl-create] 命令创建 Pod 并指定 YAML 文件，如以下示例所示：

```
$ kubectl create -f nginx.yaml

pod/nginx created
```

## <a name="view-collected-logs"></a>查看收集的日志

可能需要等待长达 10 分钟，诊断日志才会启用并显示。

> [!NOTE]
> 如果需要将所有审核日志数据用于实现合规性或其他目的，请收集这些数据并将其存储在成本较低的存储（例如 blob 存储）中。 使用 kube-audit-admin 日志类别收集和保存有意义的审核日志数据集，以便进行监视和发出警报。

在 Azure 门户中导航到 AKS 群集，然后选择左侧的“日志”。 关闭“示例查询”窗口（如果出现了此窗口）。

在左侧选择“日志”。 若要查看 kube-audit 日志，请在文本框中输入以下查询：

```
AzureDiagnostics
| where Category == "kube-audit"
| project log_s
```

可能会返回多个日志。 若要缩小查询范围，以便查看上一步骤中创建的 NGINX pod 的相关日志，请额外添加一个 where 语句来搜索 nginx，如以下示例查询所示：

```
AzureDiagnostics
| where Category == "kube-audit"
| where log_s contains "nginx"
| project log_s
```

若要查看 kube-audit-admin 日志，请在文本框中输入以下查询：

```
AzureDiagnostics
| where Category == "kube-audit-admin"
| project log_s
```

在本例中，查询显示了 kube-audit-admin 中的所有创建作业。返回的结果可能很多，若要缩小查询范围，以便查看上一步创建的 NGINX Pod 的相关日志，请另外添加一个 where 语句来搜索 nginx，如以下示例查询所示 。

```
AzureDiagnostics
| where Category == "kube-audit-admin"
| where log_s contains "nginx"
| project log_s
```


有关如何查询和筛选日志数据的详细信息，请参阅[查看或分析使用 Log Analytics 日志搜索收集的数据][analyze-log-analytics]。

## <a name="log-event-schema"></a>日志事件架构

AKS 记录以下事件：

* [AzureActivity][log-schema-azureactivity]
* AzureDiagnostics
* [AzureMetrics][log-schema-azuremetrics]
* [ContainerImageInventory][log-schema-containerimageinventory]
* [ContainerInventory][log-schema-containerinventory]
* [ContainerLog][log-schema-containerlog]
* [ContainerNodeInventory][log-schema-containernodeinventory]
* [ContainerServiceLog][log-schema-containerservicelog]
* [检测信号][log-schema-heartbeat]
* [InsightsMetrics][log-schema-insightsmetrics]
* [KubeEvents][log-schema-kubeevents]
* [KubeHealth][log-schema-kubehealth]
* [KubeMonAgentEvents][log-schema-kubemonagentevents]
* [KubeNodeInventory][log-schema-kubenodeinventory]
* [KubePodInventory][log-schema-kubepodinventory]
* [KubeServices][log-schema-kubeservices]
* [性能][log-schema-perf]

## <a name="log-roles"></a>日志角色

| 角色                     | 说明 |
|--------------------------|-------------|
| *aksService*             | 审核日志中控制平面操作的显示名称（来自 hcpService） |
| *masterclient*           | 审核日志中 MasterClientCertificate（通过 az aks get-credentials 获得的证书）的显示名称 |
| *nodeclient*             | 代理节点使用的 ClientCertificate 的显示名称 |

## <a name="next-steps"></a>后续步骤

本文介绍了如何在 AKS 群集中启用和查看 Kubernetes 控制平面组件的日志。 若要进一步进行监视和故障排除，还可以[查看 Kubelet 日志][kubelet-logs]并[启用 SSH 节点访问][aks-ssh]。

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create

<!-- LINKS - internal -->
[cli-quickstart]: kubernetes-walkthrough.md
[portal-quickstart]: kubernetes-walkthrough-portal.md
[log-analytics-overview]: ../azure-monitor/log-query/log-query-overview.md
[analyze-log-analytics]: ../azure-monitor/log-query/log-analytics-tutorial.md
[kubelet-logs]: kubelet-logs.md
[aks-ssh]: ssh.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[log-schema-azureactivity]: /azure/azure-monitor/reference/tables/azureactivity
[log-schema-azurediagnostics]: /azure/azure-monitor/reference/tables/azurediagnostics
[log-schema-azuremetrics]: /azure/azure-monitor/reference/tables/azuremetrics
[log-schema-containerimageinventory]: /azure/azure-monitor/reference/tables/containerimageinventory
[log-schema-containerinventory]: /azure/azure-monitor/reference/tables/containerinventory
[log-schema-containerlog]: /azure/azure-monitor/reference/tables/containerlog
[log-schema-containernodeinventory]: /azure/azure-monitor/reference/tables/containernodeinventory
[log-schema-containerservicelog]: /azure/azure-monitor/reference/tables/containerservicelog
[log-schema-heartbeat]: /azure/azure-monitor/reference/tables/heartbeat
[log-schema-insightsmetrics]: /azure/azure-monitor/reference/tables/insightsmetrics
[log-schema-kubeevents]: /azure/azure-monitor/reference/tables/kubeevents
[log-schema-kubehealth]: /azure/azure-monitor/reference/tables/kubehealth
[log-schema-kubemonagentevents]: /azure/azure-monitor/reference/tables/kubemonagentevents
[log-schema-kubenodeinventory]: /azure/azure-monitor/reference/tables/kubenodeinventory
[log-schema-kubepodinventory]: /azure/azure-monitor/reference/tables/kubepodinventory
[log-schema-kubeservices]: /azure/azure-monitor/reference/tables/kubeservices
[log-schema-perf]: /azure/azure-monitor/reference/tables/perf