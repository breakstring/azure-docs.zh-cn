---
title: Azure Kubernetes 服务 (AKS) 中的 HTTP 应用程序路由加载项
description: 使用 HTTP 应用程序路由外接程序访问部署在 Azure Kubernetes Service (AKS) 上的应用程序。
services: container-service
author: lachie83
ms.topic: article
ms.date: 07/20/2020
ms.author: laevenso
ms.openlocfilehash: bbedb20d9e5c75fd49c08950bbf5d459130206ce
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93125863"
---
# <a name="http-application-routing"></a>HTTP 应用程序路由

可以通过 HTTP 应用程序路由解决方案轻松地访问部署到 Azure Kubernetes 服务 (AKS) 群集的应用程序。 启用该解决方案后，它将在 AKS 群集中配置 [入口控制器](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/) 。 在部署应用程序以后，此解决方案还可以为应用程序终结点创建可公开访问的 DNS 名称。

启用此加载项后，它会在订阅中创建 DNS 区域。 有关 DNS 成本的详细详细，请参阅 [DNS 定价][dns-pricing]。

> [!CAUTION]
> HTTP 应用程序路由加载项旨在可以快速创建入口控制器并访问应用程序。 此加载项当前不适用于生产环境，不建议用于生产环境。 对于包含多个副本和 TLS 支持的生产就绪入口部署，请参阅[创建 HTTPS 入口控制器](./ingress-tls.md)。

## <a name="http-routing-solution-overview"></a>HTTP 路由解决方案概述

外接程序部署两个组件：一个 [Kubernetes 入口控制器][ingress] 和一个 [外部 DNS][external-dns] 控制器。

- **入口控制器** ：入口控制器通过类型为 LoadBalancer 的 Kubernetes 服务公开给 Internet。 入口控制器监视并实现 [Kubernetes 入口资源][ingress-resource]，这些资源创建指向应用程序终结点的路由。
- **External-DNS 控制器** ：监视 Kubernetes 入口资源并在特定于群集的 DNS 区域中创建 DNS A 记录。

## <a name="deploy-http-routing-cli"></a>部署 HTTP 路由：CLI

HTTP 应用程序路由加载项可以在部署 AKS 群集时通过 Azure CLI 来启用。 若要执行此操作，请将 [az aks create][az-aks-create] 命令与 `--enable-addons` 参数结合使用。

```azurecli
az aks create --resource-group myResourceGroup --name myAKSCluster --enable-addons http_application_routing
```

> [!TIP]
> 如果要启用多个加载项，请以逗号分隔列表的形式提供。 例如，要启用 HTTP 应用程序路由和监视，请使用格式 `--enable-addons http_application_routing,monitoring`。

还可以使用 [az aks enable-addons][az-aks-enable-addons] 命令在现有 AKS 群集上启用 HTTP 路由。 若要在现有群集上启用 HTTP 路由，请添加 `--addons` 参数并指定 *http_application_routing* ，如以下示例所示：

```azurecli
az aks enable-addons --resource-group myResourceGroup --name myAKSCluster --addons http_application_routing
```

部署或更新群集后，使用 [az aks show][az-aks-show] 命令检索 DNS 区域名称。

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query addonProfiles.httpApplicationRouting.config.HTTPApplicationRoutingZoneName -o table
```

此名称是将应用程序部署到 AKS 群集所必需的，并显示在下面的示例输出中：

```console
9f9c1fe7-21a1-416d-99cd-3543bb92e4c3.eastus.aksapp.io
```

## <a name="deploy-http-routing-portal"></a>部署 HTTP 路由：门户

HTTP 应用程序路由加载项可以在部署 AKS 群集时通过 Azure 门户来启用。

![启用 HTTP 路由功能](media/http-routing/create.png)

部署群集以后，浏览到自动创建的 AKS 资源组，然后选择 DNS 区域。 记下 DNS 区域名称。 将应用程序部署到 AKS 群集时，需要此名称。

![获取 DNS 区域名称](media/http-routing/dns.png)

## <a name="connect-to-your-aks-cluster"></a>连接到 AKS 群集

若要从本地计算机连接到 Kubernetes 群集，请使用 [kubectl][kubectl]（Kubernetes 命令行客户端）。

如果使用的是 Azure Cloud Shell，则 `kubectl` 已安装。 也可使用 [az aks install-cli][] 命令在本地安装它：

```azurecli
az aks install-cli
```

若要将 `kubectl` 配置为连接到 Kubernetes 群集，请使用 [az aks get-credentials][] 命令。 以下示例获取 *MyResourceGroup* 中名为 *MyAKSCluster* 的 AKS 群集的凭据：

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKSCluster
```

## <a name="use-http-routing"></a>使用 HTTP 路由

只能在具有如下注释的入口资源上触发 HTTP 应用程序路由解决方案：

```yaml
annotations:
  kubernetes.io/ingress.class: addon-http-application-routing
```

创建名为 **samples-http-application-routing.yaml** 的文件，并将其复制到以下 YAML 中。 在第 43 行，将 `<CLUSTER_SPECIFIC_DNS_ZONE>` 更新为在本文上一步中收集的 DNS 区域名称。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld  
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld
  template:
    metadata:
      labels:
        app: aks-helloworld
    spec:
      containers:
      - name: aks-helloworld
        image: mcr.microsoft.com/azuredocs/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "Welcome to Azure Kubernetes Service (AKS)"
---
apiVersion: v1
kind: Service
metadata:
  name: aks-helloworld  
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld
---
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: aks-helloworld
  annotations:
    kubernetes.io/ingress.class: addon-http-application-routing
spec:
  rules:
  - host: aks-helloworld.<CLUSTER_SPECIFIC_DNS_ZONE>
    http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /
```

使用 [kubectl apply][kubectl-apply] 命令创建资源。

```bash
kubectl apply -f samples-http-application-routing.yaml
```

下面的示例显示了已创建的资源：

```bash
$ kubectl apply -f samples-http-application-routing.yaml

deployment.apps/aks-helloworld created
service/aks-helloworld created
ingress.networking.k8s.io/aks-helloworld created
```

打开 web 浏览器以 *aks-helloworld \<CLUSTER_SPECIFIC_DNS_ZONE\>* ，例如 *aks-helloworld.9f9c1fe7-21a1-416d-99cd-3543bb92e4c3.eastus.aksapp.io* 并验证你是否看到了演示应用程序。 应用程序可能需要几分钟时间才能显示。

## <a name="remove-http-routing"></a>删除 HTTP 路由

可以使用 Azure CLI 删除 HTTP 路由解决方案。 为此，请运行以下命令，并替换 AKS 群集和资源组名称。

```azurecli
az aks disable-addons --addons http_application_routing --name myAKSCluster --resource-group myResourceGroup --no-wait
```

当禁用了 HTTP 应用程序路由加载项时，某些 Kubernetes 资源可能会保留在群集中。 这些资源包括 *configMap* 和 *secret* ，并且是在 *kube-system* 命名空间中创建的。 为了维护一个干净的群集，你可能想要删除这些资源。

使用以下 [kubectl get][kubectl-get] 命令查找 *addon-http-application-routing* 资源：

```console
kubectl get deployments --namespace kube-system
kubectl get services --namespace kube-system
kubectl get configmaps --namespace kube-system
kubectl get secrets --namespace kube-system
```

以下示例输出显示了应当删除的 configMap：

```
$ kubectl get configmaps --namespace kube-system

NAMESPACE     NAME                                                       DATA   AGE
kube-system   addon-http-application-routing-nginx-configuration         0      9m7s
kube-system   addon-http-application-routing-tcp-services                0      9m7s
kube-system   addon-http-application-routing-udp-services                0      9m7s
```

若要删除资源，请使用 [kubectl delete][kubectl-delete] 命令。 指定资源类型、资源名称和命名空间。 以下示例删除上面的 configmap 之一：

```console
kubectl delete configmaps addon-http-application-routing-nginx-configuration --namespace kube-system
```

针对群集中剩余的所有 *addon-http-application-routing* 资源重复前面的 `kubectl delete` 步骤。

## <a name="troubleshoot"></a>疑难解答

请使用 [kubectl logs][kubectl-logs] 命令查看 External-DNS 应用程序的应用程序日志。 这些日志应确认已成功创建 A 和 TXT DNS 记录。

```
$ kubectl logs -f deploy/addon-http-application-routing-external-dns -n kube-system

time="2018-04-26T20:36:19Z" level=info msg="Updating A record named 'aks-helloworld' to '52.242.28.189' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
time="2018-04-26T20:36:21Z" level=info msg="Updating TXT record named 'aks-helloworld' to '"heritage=external-dns,external-dns/owner=default"' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
```

也可在 Azure 门户的 DNS 区域资源上查看这些记录。

![获取 DNS 记录](media/http-routing/clippy.png)

使用 [kubectl logs][kubectl-logs] 命令可查看 Nginx 入口控制器的应用程序日志。 这些日志应确认入口资源的 `CREATE` 操作和控制器的重新加载操作。 所有 HTTP 活动都会记录到日志中。

```bash
$ kubectl logs -f deploy/addon-http-application-routing-nginx-ingress-controller -n kube-system

-------------------------------------------------------------------------------
NGINX Ingress controller
  Release:    0.13.0
  Build:      git-4bc943a
  Repository: https://github.com/kubernetes/ingress-nginx
-------------------------------------------------------------------------------

I0426 20:30:12.212936       9 flags.go:162] Watching for ingress class: addon-http-application-routing
W0426 20:30:12.213041       9 flags.go:165] only Ingress with class "addon-http-application-routing" will be processed by this ingress controller
W0426 20:30:12.213505       9 client_config.go:533] Neither --kubeconfig nor --master was specified.  Using the inClusterConfig.  This might not work.
I0426 20:30:12.213752       9 main.go:181] Creating API client for https://10.0.0.1:443
I0426 20:30:12.287928       9 main.go:225] Running in Kubernetes Cluster version v1.8 (v1.8.11) - git (clean) commit 1df6a8381669a6c753f79cb31ca2e3d57ee7c8a3 - platform linux/amd64
I0426 20:30:12.290988       9 main.go:84] validated kube-system/addon-http-application-routing-default-http-backend as the default backend
I0426 20:30:12.294314       9 main.go:105] service kube-system/addon-http-application-routing-nginx-ingress validated as source of Ingress status
I0426 20:30:12.426443       9 stat_collector.go:77] starting new nginx stats collector for Ingress controller running in namespace  (class addon-http-application-routing)
I0426 20:30:12.426509       9 stat_collector.go:78] collector extracting information from port 18080
I0426 20:30:12.448779       9 nginx.go:281] starting Ingress controller
I0426 20:30:12.463585       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-nginx-configuration", UID:"2588536c-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"559", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-nginx-configuration
I0426 20:30:12.466945       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-tcp-services", UID:"258ca065-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"561", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-tcp-services
I0426 20:30:12.467053       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-udp-services", UID:"259023bc-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"562", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-udp-services
I0426 20:30:13.649195       9 nginx.go:302] starting NGINX process...
I0426 20:30:13.649347       9 leaderelection.go:175] attempting to acquire leader lease  kube-system/ingress-controller-leader-addon-http-application-routing...
I0426 20:30:13.649776       9 controller.go:170] backend reload required
I0426 20:30:13.649800       9 stat_collector.go:34] changing prometheus collector from  to default
I0426 20:30:13.662191       9 leaderelection.go:184] successfully acquired lease kube-system/ingress-controller-leader-addon-http-application-routing
I0426 20:30:13.662292       9 status.go:196] new leader elected: addon-http-application-routing-nginx-ingress-controller-5cxntd6
I0426 20:30:13.763362       9 controller.go:179] ingress backend successfully reloaded...
I0426 21:51:55.249327       9 event.go:218] Event(v1.ObjectReference{Kind:"Ingress", Namespace:"default", Name:"aks-helloworld", UID:"092c9599-499c-11e8-a5e1-0a58ac1f0ef2", APIVersion:"extensions", ResourceVersion:"7346", FieldPath:""}): type: 'Normal' reason: 'CREATE' Ingress default/aks-helloworld
W0426 21:51:57.908771       9 controller.go:775] service default/aks-helloworld does not have any active endpoints
I0426 21:51:57.908951       9 controller.go:170] backend reload required
I0426 21:51:58.042932       9 controller.go:179] ingress backend successfully reloaded...
167.220.24.46 - [167.220.24.46] - - [26/Apr/2018:21:53:20 +0000] "GET / HTTP/1.1" 200 234 "" "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Trident/5.0)" 197 0.001 [default-aks-helloworld-80] 10.244.0.13:8080 234 0.004 200
```

## <a name="clean-up"></a>清理

使用删除在本文中创建的关联的 Kubernetes 对象 `kubectl delete` 。

```bash
kubectl delete -f samples-http-application-routing.yaml
```

示例输出显示已删除 Kubernetes 对象。

```bash
$ kubectl delete -f samples-http-application-routing.yaml

deployment "aks-helloworld" deleted
service "aks-helloworld" deleted
ingress "aks-helloworld" deleted
```

## <a name="next-steps"></a>后续步骤

有关如何在 AKS 中安装受 HTTPS 保护的入口控制器的信息，请参阅 [Azure Kubernetes 服务 (AKS) 中的 HTTPS 入口][ingress-https]。

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-show]: /cli/azure/aks?view=azure-cli-latest#az-aks-show
[ingress-https]: ./ingress-tls.md
[az-aks-enable-addons]: /cli/azure/aks#az-aks-enable-addons
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials

<!-- LINKS - external -->
[dns-pricing]: https://azure.microsoft.com/pricing/details/dns/
[external-dns]: https://github.com/kubernetes-incubator/external-dns
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[ingress]: https://kubernetes.io/docs/concepts/services-networking/ingress/
[ingress-resource]: https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource
