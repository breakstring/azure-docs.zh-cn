---
title: 使用自定义 NGINX 入口控制器并配置 HTTPS
services: azure-dev-spaces
ms.date: 12/10/2019
ms.topic: conceptual
description: 了解如何配置 Azure Dev Spaces 以使用自定义 NGINX 入口控制器并使用该入口控制器配置 HTTPS
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器, Helm, 服务网格, 服务网格路由, kubectl, k8s
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 873057e88809c1aaa8047ac02129d83ca8e9a478
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2020
ms.locfileid: "96608548"
---
# <a name="use-a-custom-nginx-ingress-controller-and-configure-https"></a>使用自定义 NGINX 入口控制器并配置 HTTPS

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

本文介绍如何将 Azure Dev Spaces 配置为使用自定义 NGINX 入口控制器。 本文还介绍如何将该自定义入口控制器配置为使用 HTTPS。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有帐户，可以[创建一个免费帐户][azure-account-create]。
* [已安装 Azure CLI][az-cli]。
* Azure Dev Spaces 启用了) 群集的 Azure Kubernetes Service (。
* 已安装[kubectl][kubectl] 。
* [已安装 Helm 3][helm-installed]。
* 具有[DNS 区域][dns-zone]的[自定义域][custom-domain]。  本文假定自定义域和 DNS 区域与 AKS 群集位于同一资源组中，但可以在不同的资源组中使用自定义域和 DNS 区域。

## <a name="configure-a-custom-nginx-ingress-controller"></a>配置自定义 NGINX 入口控制器

使用 [kubectl][kubectl]和 Kubernetes 命令行客户端连接到群集。 若要将 `kubectl` 配置为连接到 Kubernetes 群集，请使用 [az aks get-credentials][az-aks-get-credentials] 命令。 此命令将下载凭据，并将 Kubernetes CLI 配置为使用这些凭据。

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKS
```

若要验证到群集的连接，请使用 [kubectl get][kubectl-get] 命令返回群集节点列表。

```console
kubectl get nodes
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
```

添加 [官方稳定的 Helm 存储库][helm-stable-repo]，其中包含 NGINX 入口控制器 Helm 图表。

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
```

为 NGINX 入口控制器创建 Kubernetes 命名空间，并使用安装它 `helm` 。

```console
kubectl create ns nginx
helm install nginx stable/nginx-ingress --namespace nginx --version 1.27.0
```

> [!NOTE]
> 上面的示例为入口控制器创建一个公共终结点。 如果需要改用入口控制器的专用终结点，请添加 *--set controller。helm install 命令的 \\ \\ kubernetes \\ /azure-load--internal "= true* 参数。 *helm install* 例如：
> ```console
> helm install nginx stable/nginx-ingress --namespace nginx --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-load-balancer-internal"=true --version 1.27.0
> ```
> 此专用终结点在部署 AKS 群集的虚拟网络中公开。

使用 [kubectl get][kubectl-get]获取 NGINX 入口控制器服务的 IP 地址。

```console
kubectl get svc -n nginx --watch
```

示例输出显示 *nginx* 命名空间中所有服务的 IP 地址。

```console
NAME                                  TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                      AGE
nginx-nginx-ingress-controller        LoadBalancer   10.0.19.39     <pending>        80:31314/TCP,443:30521/TCP   10s
nginx-nginx-ingress-default-backend   ClusterIP      10.0.210.231   <none>           80/TCP                       10s
...
nginx-nginx-ingress-controller        LoadBalancer   10.0.19.39     MY_EXTERNAL_IP   80:31314/TCP,443:30521/TCP   26s
```

使用 [az network dns record-set a add-record][az-network-dns-record-set-a-add-record] 将 *A* 记录添加到使用 NGINX 服务的外部 IP 地址的 DNS 区域。

```azurecli
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.nginx \
    --ipv4-address MY_EXTERNAL_IP
```

上面的示例将 *A* 记录添加到 *MY_CUSTOM_DOMAIN* DNS 区域。

本文使用 [Azure Dev Spaces 单车共享示例应用程序](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp)来演示 Azure Dev Spaces 的用法。 从 GitHub 克隆此应用程序，然后导航到其目录中：

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/charts
```

打开 [yaml][values-yaml] 并进行以下更新：
* 将 *<REPLACE_ME_WITH_HOST_SUFFIX>* 的所有实例替换为 *nginx。MY_CUSTOM_DOMAIN* 使用域进行 *MY_CUSTOM_DOMAIN*。 
* 替换 *kubernetes.io/ingress.class： traefik-azds # Dev Spaces-特定于* *kubernetes.io/ingress.class： Nginx # 自定义入口*。 

下面是已更新文件的示例 `values.yaml` ：

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: nginx  # Custom Ingress
    hosts:
      - dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: nginx  # Custom Ingress
    hosts:
      - dev.gateway.nginx.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
```

保存更改并关闭该文件。

使用创建使用的示例应用程序的 *开发* 环境 `azds space select` 。

```console
azds space select -n dev -y
```

使用部署示例应用程序 `helm install` 。

```console
helm install bikesharingsampleapp . --dependency-update --namespace dev --atomic
```

上面的示例将示例应用程序部署到 *dev* 命名空间。

显示使用访问示例应用程序的 Url `azds list-uris` 。

```console
azds list-uris
```

下面的输出显示了来自的示例 Url `azds list-uris` 。

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN/  Available
http://dev.gateway.nginx.MY_CUSTOM_DOMAIN/         Available
```

通过 `azds list-uris` 命令打开公共 URL，导航到 *bikesharingweb* 服务。 在以上示例中，*bikesharingweb* 服务的公共 URL 为 `http://dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN/`。

> [!NOTE]
> 如果看到错误页面而不是 *bikesharingweb* 服务，请验证是否已在 *yaml* 文件 **中更新** *kubernetes.io/ingress.class* 批注和主机。

使用 `azds space select` 命令在 " *开发* " 下创建子空间，并列出用于访问子开发人员空间的 url。

```console
azds space select -n dev/azureuser1 -y
azds list-uris
```

以下输出显示了中的示例 Url `azds list-uris` ，可用于访问 *azureuser1* 子开发人员空间中的示例应用程序。

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://azureuser1.s.dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN/  Available
http://azureuser1.s.dev.gateway.nginx.MY_CUSTOM_DOMAIN/         Available
```

通过从命令中打开公共 URL，导航到 *azureuser1* 子 dev 空间中的 *bikesharingweb* 服务 `azds list-uris` 。 在上面的示例中， *azureuser1* 子开发人员空间中 *bikesharingweb* 服务的公共 URL 是 `http://azureuser1.s.dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN/` 。

## <a name="configure-the-nginx-ingress-controller-to-use-https"></a>将 NGINX 入口控制器配置为使用 HTTPS

将 NGINX 入口控制器配置为使用 HTTPS 时，请使用 [证书管理器][cert-manager] 自动管理 TLS 证书。 使用 `helm` 安装 *certmanager* 图表。

```console
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.12/deploy/manifests/00-crds.yaml --namespace nginx
kubectl label namespace nginx certmanager.k8s.io/disable-validation=true
helm repo add jetstack https://charts.jetstack.io
helm repo update
helm install cert-manager --namespace nginx --version v0.12.0 jetstack/cert-manager --set ingressShim.defaultIssuerName=letsencrypt --set ingressShim.defaultIssuerKind=ClusterIssuer
```

创建一个 `letsencrypt-clusterissuer.yaml` 文件，并使用您的电子邮件地址更新 "电子邮件" 字段。

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: ClusterIssuer
metadata:
  name: letsencrypt
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: MY_EMAIL_ADDRESS
    privateKeySecretRef:
      name: letsencrypt
    solvers:
      - http01:
          ingress:
            class: nginx
```

> [!NOTE]
> 对于测试，还有一个可用于 *ClusterIssuer* 的 [过渡服务器][letsencrypt-staging-issuer]。

使用 `kubectl` 应用 `letsencrypt-clusterissuer.yaml` 。

```console
kubectl apply -f letsencrypt-clusterissuer.yaml --namespace nginx
```

将 [yaml][values-yaml] 更新为包含使用 *证书管理器* 和 HTTPS 的详细信息。 下面是已更新文件的示例 `values.yaml` ：

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: nginx  # Custom Ingress
      cert-manager.io/cluster-issuer: letsencrypt
    hosts:
      - dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
    tls:
    - hosts:
      - dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN
      secretName: dev-bikesharingweb-secret

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: nginx  # Custom Ingress
      cert-manager.io/cluster-issuer: letsencrypt
    hosts:
      - dev.gateway.nginx.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
    tls:
    - hosts:
      - dev.gateway.nginx.MY_CUSTOM_DOMAIN
      secretName: dev-gateway-secret
```

使用以下内容升级示例应用程序 `helm` ：

```console
helm upgrade bikesharingsampleapp . --namespace dev --atomic
```

导航到 *dev/azureuser1* 子空间中的示例应用程序，注意将重定向到 "使用 HTTPS"。 另请注意，页面加载，但浏览器显示一些错误。 打开浏览器控制台时，将显示与尝试加载 HTTP 资源的 HTTPS 页相关的错误。 例如：

```console
Mixed Content: The page at 'https://azureuser1.s.dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN/devsignin' was loaded over HTTPS, but requested an insecure resource 'http://azureuser1.s.dev.gateway.nginx.MY_CUSTOM_DOMAIN/api/user/allUsers'. This request has been blocked; the content must be served over HTTPS.
```

若要修复此错误，请更新 [BikeSharingWeb/azds][azds-yaml] ，如下所示：

```yaml
...
    ingress:
      annotations:
        kubernetes.io/ingress.class: nginx
        cert-manager.io/cluster-issuer: letsencrypt
      hosts:
      # This expands to [space.s.][rootSpace.]bikesharingweb.<random suffix>.<region>.azds.io
      - $(spacePrefix)$(rootSpacePrefix)bikesharingweb.nginx.MY_CUSTOM_DOMAIN
      tls:
      - hosts:
        - $(spacePrefix)$(rootSpacePrefix)bikesharingweb.nginx.MY_CUSTOM_DOMAIN
        secretName: dev-bikesharingweb-secret
...
```

使用 *url* 包的依赖项更新 [上的 BikeSharingWeb/package.js][package-json] 。

```json
{
...
    "react-responsive": "^6.0.1",
    "universal-cookie": "^3.0.7",
    "url": "0.11.0"
  },
...
```

更新 [BikeSharingWeb/lib/helpers.js][helpers-js]中的 *getApiHostAsync* 方法，以使用 HTTPS：

```javascript
...
    getApiHostAsync: async function() {
        const apiRequest = await fetch('/api/host');
        const data = await apiRequest.json();
        
        var urlapi = require('url');
        var url = urlapi.parse(data.apiHost);

        console.log('apiHost: ' + "https://"+url.host);
        return "https://"+url.host;
    },
...
```

导航到 `BikeSharingWeb` 目录并使用 `azds up` 运行更新后的 *BikeSharingWeb* 服务。

```console
cd ../BikeSharingWeb/
azds up
```

导航到 *dev/azureuser1* 子空间中的示例应用程序，请注意，将重定向到使用 HTTPS，但不会出现任何错误。

## <a name="next-steps"></a>后续步骤

深入了解 Azure Dev Spaces 的工作方式。

> [!div class="nextstepaction"]
> [Azure Dev Spaces 工作原理](../how-dev-spaces-works.md)


[az-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[custom-domain]: ../../app-service/manage-custom-dns-buy-domain.md#buy-an-app-service-domain
[dns-zone]: ../../dns/dns-getstarted-cli.md
[azds-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/azds.yaml
[azure-account-create]: https://azure.microsoft.com/free
[cert-manager]: https://cert-manager.io/
[helm-installed]: https://helm.sh/docs/intro/install/
[helm-stable-repo]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helpers-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/lib/helpers.js#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[letsencrypt-staging-issuer]: https://cert-manager.io/docs/configuration/acme/#creating-a-basic-acme-issuer
[package-json]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/package.json
[values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml
