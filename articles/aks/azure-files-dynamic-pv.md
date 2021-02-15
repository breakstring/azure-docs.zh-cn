---
title: 动态创建 Azure 文件共享
titleSuffix: Azure Kubernetes Service
description: 了解如何使用 Azure 文件动态创建永久性卷，以便与 Azure Kubernetes 服务 (AKS) 中的多个并发 Pod 一起使用
services: container-service
ms.topic: article
ms.date: 07/01/2020
ms.openlocfilehash: 2ad2affee34348e8c2fc7b734c8b49d0aec8db40
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2020
ms.locfileid: "96744903"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-files-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes 服务 (AKS) 中动态创建永久性卷并将其用于 Azure 文件

永久性卷表示已经过预配可以用于 Kubernetes Pod 的存储块。 永久性卷可供一个或多个 Pod 使用，并可动态或静态预配。 如果多个 Pod 需要同时访问同一存储卷，则可以使用 Azure 文件存储通过[服务器消息块 (SMB) 协议][smb-overview]进行连接。 本文介绍如何动态创建 Azure 文件共享以供 Azure Kubernetes 服务 (AKS) 群集中的多个 Pod 使用。

有关 Kubernetes 卷的详细信息，请参阅 [AKS 中应用程序的存储选项][concepts-storage]。

## <a name="before-you-begin"></a>准备阶段

本文假定你拥有现有的 AKS 群集。 如果需要 AKS 群集，请参阅 AKS 快速入门[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 门户][aks-quickstart-portal]。

还需安装并配置 Azure CLI 2.0.59 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][install-azure-cli]。

## <a name="create-a-storage-class"></a>创建存储类

存储类用于定义如何创建 Azure 文件共享。 [节点资源组][node-resource-group]中会自动创建一个存储帐户来与存储类配合使用，以保存 Azure 文件共享。 为 skuName 选择下列任一 [Azure 存储冗余][storage-skus]：

* *Standard_LRS* - 标准本地冗余存储 (LRS)
* *Standard_GRS* - 标准异地冗余存储 (GRS)
* *Standard_ZRS* - 标准区域冗余存储 (ZRS)
* *Standard_RAGRS* - 标准读取访问异地冗余存储 (RA-GRS)
* *Premium_LRS* - 高级本地冗余存储 (LRS)
* *Premium_ZRS* 高级区域冗余存储 (ZRS) 

> [!NOTE]
> Azure 文件存储在运行 Kubernetes 1.13 或更高版本的 AKS 群集中支持高级存储，最小高级文件共享为 100GB

有关 Azure 文件存储的 Kubernetes 存储类的详细信息，请参阅 [Kubernetes 存储类][kubernetes-storage-classes]。

创建名为 `azure-file-sc.yaml` 的文件，并将其复制到以下示例清单中。 有关 *mountOptions* 的详细信息，请参阅 [装载选项][mount-options]部分。

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: my-azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict
  - actimeo=30
parameters:
  skuName: Standard_LRS
```

使用 [kubectl apply][kubectl-apply] 命令创建存储类：

```console
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-a-persistent-volume-claim"></a>创建永久性卷声明

永久性卷声明 (PVC) 使用存储类对象来动态预配 Azure 文件共享。 可使用以下 YAML 创建大小为 *5 GB*、访问权限为 *ReadWriteMany* 的永久性卷声明。 有关访问模式的详细信息，请参阅 [Kubernetes 永久性卷][access-modes]文档。

现在，创建名为 `azure-file-pvc.yaml` 的文件，并将其复制到以下 YAML 中。 请确保 *storageClassName* 与上一步骤中创建的存储类匹配：

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: my-azurefile
  resources:
    requests:
      storage: 5Gi
```

> [!NOTE]
> 如果将 *Premium_LRS* SKU 用于存储类，则存储的最小值必须为 100Gi。

使用 [kubectl apply][kubectl-apply] 命令创建永久性卷声明：

```console
kubectl apply -f azure-file-pvc.yaml
```

完成此步骤后，文件共享即创建完毕。 同时还会创建一个包含连接信息和凭据的 Kubernetes 机密。 可以使用 [kubectl get][kubectl-get] 命令查看 PVC 的状态：

```console
$ kubectl get pvc my-azurefile

NAME           STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
my-azurefile   Bound     pvc-8436e62e-a0d9-11e5-8521-5a8664dc0477   5Gi        RWX            my-azurefile      5m
```

## <a name="use-the-persistent-volume"></a>使用永久性卷

以下 YAML 创建的 Pod 使用永久性卷声明 my-azurefile 将 Azure 文件共享装载到 /mnt/azure 路径 。 对于 Windows Server 容器，请使用 Windows 路径约定指定 mountPath，例如“D:”。

创建名为 `azure-pvc-files.yaml` 的文件，并将其复制到以下 YAML 中。 请确保 *claimName* 与上一步骤中创建的 PVC 匹配。

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
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
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: my-azurefile
```

使用 [kubectl apply][kubectl-apply] 命令创建 Pod。

```console
kubectl apply -f azure-pvc-files.yaml
```

现有一个正在运行的 Pod，其中的 Azure 文件存储共享已装载到 /mnt/azure 目录中。 通过 `kubectl describe pod mypod` 检查 pod 时，可以看到此配置。 以下精简示例输出显示容器中装载的卷：

```
Containers:
  mypod:
    Container ID:   docker://053bc9c0df72232d755aa040bfba8b533fa696b123876108dec400e364d2523e
    Image:          mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    Image ID:       docker-pullable://nginx@sha256:d85914d547a6c92faa39ce7058bd7529baacab7e0cd4255442b04577c4d1f424
    State:          Running
      Started:      Fri, 01 Mar 2019 23:56:16 +0000
    Ready:          True
    Mounts:
      /mnt/azure from volume (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-8rv4z (ro)
[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  my-azurefile
    ReadOnly:   false
[...]
```

## <a name="mount-options"></a>装载选项

对于 Kubernetes 版本 1.13.0 及更高版本，fileMode 和 dirMode 的默认值为 0777。 如果使用存储类动态创建永久性卷，则可以在存储类对象上指定装载选项。 以下示例设置 *0777*：

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: my-azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict
  - actimeo=30
parameters:
  skuName: Standard_LRS
```

## <a name="next-steps"></a>后续步骤

如需相关的最佳做法，请参阅[在 AKS 中存储和备份的最佳做法][operator-best-practices-storage]。

有关存储类参数，请参阅[动态预配](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/docs/driver-parameters.md#dynamic-provision)。

详细了解使用 Azure 文件的 Kubernetes 永久性卷。

> [!div class="nextstepaction"]
> [用于 Azure 文件的 Kubernetes 插件][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[pv-static]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#static
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/aks#az-aks-show
[az-storage-account-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[mount-options]: #mount-options
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[storage-skus]: ../storage/common/storage-redundancy.md
[kubernetes-rbac]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
