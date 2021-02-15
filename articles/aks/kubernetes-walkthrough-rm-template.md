---
title: 快速入门 - 创建 Azure Kubernetes 服务 (AKS) 群集
description: 了解如何使用 Azure 资源管理器模板快速创建 Kubernetes 群集，并在 Azure Kubernetes 服务 (AKS) 中部署应用程序
services: container-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.custom: mvc,subject-armqs, devx-track-azurecli
ms.openlocfilehash: 77c4bf2153d8c80abc81b26f453a8b689fee367f
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2021
ms.locfileid: "98249867"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-an-arm-template"></a>快速入门：使用 ARM 模板部署 Azure Kubernetes 服务 (AKS) 群集

Azure Kubernetes 服务 (AKS) 是可用于快速部署和管理群集的托管式 Kubernetes 服务。 本快速入门将使用 Azure 资源管理器模板（ARM 模板）部署 AKS 群集。 该群集中将运行一个包含 Web 前端和 Redis 实例的多容器应用程序。

![浏览到 Azure Vote 的图像](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

本快速入门假设读者基本了解 Kubernetes 的概念。 有关详细信息，请参阅 [Azure Kubernetes 服务 (AKS) 的 Kubernetes 核心概念][kubernetes-concepts]。

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aks%2Fazuredeploy.json)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要 Azure CLI 版本 2.0.61 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

- 若要使用资源管理器模板创建 AKS 群集，请提供 SSH 公钥和 Azure Active Directory 服务主体。 或者，可以使用[托管标识](use-managed-identity.md)而不是服务主体来获得权限。 如果需要其中的任一资源，请参阅以下部分；否则请跳到[查看模板](#review-the-template)部分。

### <a name="create-an-ssh-key-pair"></a>创建 SSH 密钥对

若要访问 AKS 节点，请使用 SSH 密钥对进行连接。 使用 `ssh-keygen` 命令生成 SSH 公钥和私钥文件。 默认情况下，这些文件在 *~/.ssh* 目录中创建。 如果给定位置存在具有相同名称的 SSH 密钥对，则这些文件将被覆盖。

在浏览器中访问 [https://shell.azure.com](https://shell.azure.com) 以打开 Cloud Shell。

以下命令使用 RSA 加密和 2048 位长度创建 SSH 密钥对：

```console
ssh-keygen -t rsa -b 2048
```

有关创建 SSH 密钥的详细信息，请参阅[在 Azure 中创建和管理用于身份验证的 SSH 密钥][ssh-keys]。

### <a name="create-a-service-principal"></a>创建服务主体

若要允许 AKS 群集与其他 Azure 资源交互，请使用 Azure Active Directory 服务主体。 使用 [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] 命令创建服务主体。 `--skip-assignment` 参数限制分配任何其他权限。 默认情况下，此服务主体的有效期为一年。 请注意，可以使用托管标识而不是服务主体。 有关详细信息，请参阅[使用托管标识](use-managed-identity.md)。

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

输出类似于以下示例：

```json
{
  "appId": "8b1ede42-d407-46c2-a1bc-6b213b04295f",
  "displayName": "azure-cli-2019-04-19-21-42-11",
  "name": "http://azure-cli-2019-04-19-21-42-11",
  "password": "27e5ac58-81b0-46c1-bd87-85b4ef622682",
  "tenant": "73f978cf-87f2-41bf-92ab-2e7ce012db57"
}
```

记下 *appId* 和 *password*。 后续步骤会用到这些值。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/101-aks/)。

:::code language="json" source="~/quickstart-templates/101-aks/azuredeploy.json":::

有关更多 AKS 示例，请参阅 [AKS 快速入门模板][aks-quickstart-templates]站点。

## <a name="deploy-the-template"></a>部署模板

1. 选择下图登录到 Azure 并打开一个模板。

    [![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aks%2Fazuredeploy.json)

2. 选择或输入以下值。

    对于本快速入门，请保留“OS 磁盘大小(GB)”、“代理计数”、“代理 VM 大小”、“OS 类型”和“Kubernetes 版本”的默认值。     为以下模板参数提供自己的值：

    * 订阅：选择 Azure 订阅。
    * 资源组：选择“新建”。 输入资源组的唯一名称（例如 *myResourceGroup*），然后选择“确定”。
    * 位置：选择一个位置，例如“美国东部”。
    * **群集名称**：输入 AKS 群集的唯一名称，例如 *myAKSCluster*。
    * **DNS 前缀**：输入群集的唯一 DNS 前缀，例如 *myakscluster*。
    * **Linux 管理员用户名**：输入一个用户名用于通过 SSH 进行连接，例如 *azureuser*。
    * **SSH RSA 公钥**：复制并粘贴 SSH 密钥对的 *public* 部分（默认为 *~/.ssh/id_rsa.pub* 的内容）。
    * **服务主体客户端 ID**：复制并粘贴 `az ad sp create-for-rbac` 命令输出的、服务主体的 *appId*。
    * **服务主体客户端机密**：复制并粘贴 `az ad sp create-for-rbac` 命令输出的、服务主体的 *password*。
    * **我同意上述条款和条件**：选中此框表示同意。

    ![用于在门户中创建 Azure Kubernetes 服务群集的资源管理器模板](./media/kubernetes-walkthrough-rm-template/create-aks-cluster-using-template-portal.png)

3. 选择“购买”。

创建 AKS 群集需要几分钟时间。 等待群集成功部署，然后转到下一步骤。

## <a name="validate-the-deployment"></a>验证部署

### <a name="connect-to-the-cluster"></a>连接到群集

若要管理 Kubernetes 群集，请使用 Kubernetes 命令行客户端 [kubectl][kubectl]。 如果使用的是 Azure Cloud Shell，则 `kubectl` 已安装。 若要在本地安装 `kubectl`，请使用 [az aks install-cli][az-aks-install-cli] 命令：

```azurecli
az aks install-cli
```

若要将 `kubectl` 配置为连接到 Kubernetes 群集，请使用 [az aks get-credentials][az-aks-get-credentials] 命令。 此命令将下载凭据，并将 Kubernetes CLI 配置为使用这些凭据。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

若要验证到群集的连接，请使用 [kubectl get][kubectl-get] 命令返回群集节点列表。

```console
kubectl get nodes
```

以下示例输出显示在上一步骤中创建的节点。 确保所有节点的状态为“就绪”：

```output
NAME                       STATUS   ROLES   AGE     VERSION
aks-agentpool-41324942-0   Ready    agent   6m44s   v1.12.6
aks-agentpool-41324942-1   Ready    agent   6m46s   v1.12.6
aks-agentpool-41324942-2   Ready    agent   6m45s   v1.12.6
```

### <a name="run-the-application"></a>运行应用程序

Kubernetes 清单文件定义群集的所需状态，例如，要运行哪些容器映像。 在本快速入门中，清单用于创建运行 Azure Vote 应用程序所需的所有对象。 此清单包括两个 [Kubernetes 部署][kubernetes-deployment] - 一个用于 Azure Vote Python 示例应用程序，另一个用于 Redis 实例。 此外，还会创建两个 [Kubernetes 服务][kubernetes-service] - 一个内部服务用于 Redis 实例，一个外部服务用于从 Internet 访问 Azure Vote 应用程序。

创建名为 `azure-vote.yaml` 的文件，并将其复制到以下 YAML 定义中。 如果使用 Azure Cloud Shell，则可以使用 `vi` 或 `nano` 来创建此文件，就像在虚拟或物理系统中操作一样：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
        env:
        - name: ALLOW_EMPTY_PASSWORD
          value: "yes"
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

使用 [kubectl apply][kubectl-apply] 命令部署应用程序，并指定 YAML 清单的名称：

```console
kubectl apply -f azure-vote.yaml
```

以下示例输出显示已成功创建了部署和服务：

```output
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

### <a name="test-the-application"></a>测试应用程序

应用程序运行时，Kubernetes 服务将向 Internet 公开应用程序前端。 此过程可能需要几分钟才能完成。

若要监视进度，请将 [kubectl get service][kubectl-get] 命令与 `--watch` 参数配合使用。

```console
kubectl get service azure-vote-front --watch
```

最初，*azure-vote-front* 服务的 *EXTERNAL-IP* 显示为 *pending*。

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

当 *EXTERNAL-IP* 地址从 *pending* 更改为实际公共 IP 地址时，请使用 `CTRL-C` 停止 `kubectl` 监视进程。 以下示例输出显示向服务分配了有效的公共 IP 地址：

```output
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

若要查看 Azure Vote 应用的实际效果，请打开 Web 浏览器并转到服务的外部 IP 地址。

![浏览到 Azure Vote 的图像](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="clean-up-resources"></a>清理资源

如果不再需要群集，可以使用 [az group delete][az-group-delete] 命令删除资源组、容器服务及所有相关资源。

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> 删除群集时，AKS 群集使用的 Azure Active Directory 服务主体不会被删除。 有关如何删除服务主体的步骤，请参阅 [AKS 服务主体的注意事项和删除][sp-delete]。 如果你使用了托管标识，则该标识由平台托管，不需要删除。

## <a name="get-the-code"></a>获取代码

本快速入门使用预先创建的容器映像创建了 Kubernetes 部署。 GitHub 上提供了相关的应用程序代码、Dockerfile 和 Kubernetes 清单文件。

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>后续步骤

在本快速入门中，部署了 Kubernetes 群集，并向该群集部署了多容器应用程序。 访问已创建的群集的 [Kubernetes Web 仪表板][kubernetes-dashboard]。

若要详细了解 AKS 并演练部署示例的完整代码，请继续阅读“Kubernetes 群集”教程。

> [!div class="nextstepaction"]
> [AKS 教程][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[azure-dev-spaces]: ../dev-spaces/index.yml
[aks-quickstart-templates]: https://azure.microsoft.com/resources/templates/?term=Azure+Kubernetes+Service

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: ../azure-monitor/insights/container-insights-onboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks?view=azure-cli-latest&preserve-view=true#az-aks-browse
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest&preserve-view=true#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest&preserve-view=true#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest&preserve-view=true#az-aks-install-cli
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[ssh-keys]: ../virtual-machines/linux/create-ssh-keys-detailed.md
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
