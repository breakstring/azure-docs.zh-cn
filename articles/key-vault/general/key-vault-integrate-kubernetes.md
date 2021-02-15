---
title: 将 Azure Key Vault 与 Kubernetes 集成
description: 在本教程中，你将使用机密存储容器存储接口 (CSI) 驱动程序从 Azure 密钥保管库访问和检索机密，以便将其装载到 Kubernetes Pod。
author: msmbaldwin
ms.author: mbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/25/2020
ms.openlocfilehash: f4981036ca92f6efe2d3e23ea1f507a3a1f3c70a
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2021
ms.locfileid: "98234250"
---
# <a name="tutorial-configure-and-run-the-azure-key-vault-provider-for-the-secrets-store-csi-driver-on-kubernetes"></a>教程：为 Kubernetes 上的机密存储 CSI 驱动程序配置并运行 Azure Key Vault 提供程序

> [!IMPORTANT]
> CSI 驱动程序是不受 Azure 技术支持支持的开源项目。 请通过[此处](https://github.com/Azure/secrets-store-csi-driver-provider-azure/issues)的 github 链接报告关于 CSI 驱动程序 Key Vault 集成的所有反馈和问题。 此工具可供用户自行安装到群集中并从我们的社区收集反馈。


在本教程中，你将使用机密存储容器存储接口 (CSI) 驱动程序从 Azure 密钥保管库访问和检索机密，以便将机密装载到 Kubernetes Pod。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 使用托管标识。
> * 使用 Azure CLI 部署 Azure Kubernetes 服务 (AKS) 群集。
> * 安装 Helm 和机密存储 CSI 驱动程序。
> * 创建 Azure 密钥保管库并设置机密。
> * 创建自己的 SecretProviderClass 对象。
> * 通过密钥保管库中已装载的机密部署 Pod。

## <a name="prerequisites"></a>先决条件

* 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 在开始学习本教程之前，请安装 [Azure CLI](/cli/azure/install-azure-cli-windows?view=azure-cli-latest)。

本教程假定你在 Linux 节点上运行 Azure Kubernetes 服务。

## <a name="use-managed-identities"></a>使用托管标识

此图说明了托管标识的 AKS–Key Vault 集成流：

![显示托管标识的 AKS–Key Vault 集成流的关系图](../media/aks-key-vault-integration-flow.png)

## <a name="deploy-an-azure-kubernetes-service-aks-cluster-by-using-the-azure-cli"></a>使用 Azure CLI 部署 Azure Kubernetes 服务 (AKS) 群集

无需使用 Azure Cloud Shell。 安装了 Azure CLI 的命令提示符（终端）足以满足要求。 

完成[使用 Azure CLI 部署 Azure Kubernetes 服务群集](../../aks/kubernetes-walkthrough.md)中的“创建资源组”、“创建 AKS 群集”和“连接到群集”部分。 

> [!NOTE] 
> 如果计划使用 Pod 标识，请确保在创建 Kubernetes 群集时启用它，如以下命令中所示：
>
> ```azurecli
> az aks create -n contosoAKSCluster -g contosoResourceGroup --kubernetes-version 1.16.9 --node-count 1 --enable-managed-identity
> ```

1. [将 PATH 环境变量设置](https://www.java.com/en/download/help/path.xml)为已下载的 kubectl.exe 文件。
1. 使用以下命令查看 Kubernetes 版本，该命令输出客户端和服务器版本。 客户端版本是已安装的 kubectl.exe 文件，而服务器版本是正在运行群集的 Azure Kubernetes 服务 (AKS)。
    ```azurecli
    kubectl version
    ```
1. 确保 Kubernetes 版本是 1.16.0 或更高版本。 对于 windows 群集，请确保 Kubernetes 版本为 1.18.0 或更高版本。 以下命令将同时升级 Kubernetes 群集和节点池。 执行该命令可能需要几分钟的时间。 在本例中，资源组是 contosoResourceGroup，Kubernetes 群集是 contosoAKSCluster 。
    ```azurecli
    az aks upgrade --kubernetes-version 1.16.9 --name contosoAKSCluster --resource-group contosoResourceGroup
    ```
1. 若要显示已创建的 AKS 群集的元数据，请使用以下命令。 复制 principalId、clientId、subscriptionId 和 nodeResourceGroup 供稍后使用   。 如果在创建 AKS 群集时未启用托管标识，则 principalId 和 clientId 将为 null 。 

    ```azurecli
    az aks show --name contosoAKSCluster --resource-group contosoResourceGroup
    ```

    输出显示两个突出显示的参数：
    
    ![突出显示 principalId 和 clientId 值的 Azure CLI 屏幕截图](../media/kubernetes-key-vault-2.png)![突出显示 subscriptionId 和 nodeResourceGroup 值的 Azure CLI 屏幕截图](../media/kubernetes-key-vault-3.png)
    
## <a name="install-helm-and-the-secrets-store-csi-driver"></a>安装 Helm 和机密存储 CSI 驱动程序
> [!NOTE]
> 以下安装仅适用于 Linux 上的 AKS。 有关机密存储 CSI 驱动程序安装的详细信息，请参阅[适用于机密存储 CSI 驱动程序的 Azure Key Vault 提供程序](https://github.com/Azure/secrets-store-csi-driver-provider-azure) 

若要安装机密存储 CSI 驱动程序，首先需要安装 [Helm](https://helm.sh/docs/intro/install/)。

通过[机密存储 CSI](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/charts/csi-secrets-store-provider-azure/README.md) 驱动程序接口，可获取 Azure 密钥保管库实例中存储的机密，并使用驱动程序接口将这些机密内容装载到 Kubernetes Pod 中。

1. 检查以确保 Helm 版本为 v3 或更高版本：
    ```azurecli
    helm version
    ```
1. 为驱动程序安装机密存储 CSI 驱动程序和 Azure Key Vault 提供程序：
    ```azurecli
    helm repo add csi-secrets-store-provider-azure https://raw.githubusercontent.com/Azure/secrets-store-csi-driver-provider-azure/master/charts

    helm install csi-secrets-store-provider-azure/csi-secrets-store-provider-azure --generate-name
    ```

## <a name="create-an-azure-key-vault-and-set-your-secrets"></a>创建 Azure 密钥保管库并设置机密

若要创建你自己的密钥保管库并设置机密，请按照[使用 Azure CLI 在 Azure Key Vault 中设置和检索机密](../secrets/quick-create-cli.md)中的说明执行操作。

> [!NOTE] 
> 无需使用 Azure Cloud Shell，也无需创建新的资源组。 可使用之前为 Kubernetes 群集创建的资源组。

## <a name="create-your-own-secretproviderclass-object"></a>创建自己的 SecretProviderClass 对象

若要自行创建自定义 SecretProviderClass 对象，并为机密存储 CSI 驱动程序指定提供程序特定的参数，请[使用此模板](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/v1alpha1_secretproviderclass_service_principal.yaml)。 此对象将提供对密钥保管库的标识访问。

在示例 SecretProviderClass YAML 文件中，填写缺少的参数。 下列参数必填：

* **userAssignedIdentityID**: # [REQUIRED] 如果该值为空，则默认情况下，将在 VM 上使用系统分配的标识 
* **keyvaultName**：密钥保管库的名称
* **对象**：包含要装载的所有机密内容的容器
    * **objectName**：机密内容的名称
    * **objectType**：对象类型（机密、密钥、证书）
* **resourceGroup**：资源组的名称 # [版本 < 0.0.4 时需要] KeyVault 的资源组
* **subscriptionId**：密钥保管库的订阅 ID # [版本 < 0.0.4 时需要] KeyVault 的订阅 ID
* **tenantID**：密钥保管库的租户 ID 或目录 ID

此处提供所有必填字段的文档：[链接](https://github.com/Azure/secrets-store-csi-driver-provider-azure#create-a-new-azure-key-vault-resource-or-use-an-existing-one)。

更新后的模板如以下代码所示。 将其下载为 YAML 文件，并填写必填字段。 在本例中，密钥保管库为 contosoKeyVault5。 它有两个机密，secret1 和 secret2 。

> [!NOTE] 
> 如果使用的是托管标识，请将 usePodIdentity 值设置为“true”，并将 userAssignedIdentityID 值设置为一对引号 ("") 。 

```yaml
apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
kind: SecretProviderClass
metadata:
  name: azure-kvname
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"                   # [REQUIRED] Set to "true" if using managed identities
    useVMManagedIdentity: "false"             # [OPTIONAL] if not provided, will default to "false"
    userAssignedIdentityID: "servicePrincipalClientID"       # [REQUIRED]  If you're using a user-assigned identity as the VM's managed identity, specify the identity's client id. If the value is empty, it defaults to use the system-assigned identity on the VM
                                                         
    keyvaultName: "contosoKeyVault5"          # [REQUIRED] the name of the key vault
                                              #     az keyvault show --name contosoKeyVault5
                                              #     the preceding command will display the key vault metadata, which includes the subscription ID, resource group name, key vault 
    cloudName: ""                                # [OPTIONAL for Azure] if not provided, Azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1                 # [REQUIRED] object name
                                              #     az keyvault secret list --vault-name "contosoKeyVault5"
                                              #     the above command will display a list of secret names from your key vault
          objectType: secret                  # [REQUIRED] object types: secret, key, or cert
          objectVersion: ""                   # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: secret2
          objectType: secret
          objectVersion: ""
    resourceGroup: "contosoResourceGroup"     # [REQUIRED] the resource group name of the key vault
    subscriptionId: "subscriptionID"          # [REQUIRED] the subscription ID of the key vault
    tenantId: "tenantID"                      # [REQUIRED] the tenant ID of the key vault
```
下图显示 az keyvault show --name contosoKeyVault5 的控制台输出，其中包含突出显示的相关元数据：

![显示“az keyvault show --name contosoKeyVault5”的控制台输出的屏幕截图](../media/kubernetes-key-vault-4.png)

## <a name="assign-managed-identity"></a>分配托管标识

请将特定角色分配给已创建的 AKS 群集。 

1. 若要创建、列出或读取用户分配的托管标识，需要为 AKS 群集分配[托管标识操作员](../../role-based-access-control/built-in-roles.md#managed-identity-operator)角色。 请确保 $clientId 是 Kubernetes 群集的 clientId。 就范围而言，它处于 Azure 订阅服务（特别是创建 AKS 群集时创建的节点资源组）下。 此范围将确保仅该组中的资源受下面分配的角色的影响。 

    ```azurecli
    RESOURCE_GROUP=contosoResourceGroup
    
    az role assignment create --role "Managed Identity Operator" --assignee $clientId --scope /subscriptions/<SUBID>/resourcegroups/$RESOURCE_GROUP
    
    az role assignment create --role "Virtual Machine Contributor" --assignee $clientId --scope /subscriptions/<SUBID>/resourcegroups/$RESOURCE_GROUP
    ```

1. 将 Azure Active Directory (Azure AD) 标识安装到 AKS。
    ```azurecli
    helm repo add aad-pod-identity https://raw.githubusercontent.com/Azure/aad-pod-identity/master/charts

    helm install pod-identity aad-pod-identity/aad-pod-identity
    ```

1. 创建 Azure AD 标识。 在输出中，复制 clientId 和 principalId 供以后使用 。
    ```azurecli
    az identity create -g $resourceGroupName -n $identityName
    ```

1. 将“读取者”角色分配给在上一步中为密钥保管库创建的 Azure AD 标识，然后授予该标识权限以从密钥保管库获取机密。 使用 Azure AD 标识中的 clientId 和 principalId 。
    ```azurecli
    az role assignment create --role "Reader" --assignee $principalId --scope /subscriptions/<SUBID>/resourceGroups/contosoResourceGroup/providers/Microsoft.KeyVault/vaults/contosoKeyVault5

    az keyvault set-policy -n contosoKeyVault5 --secret-permissions get --spn $clientId
    az keyvault set-policy -n contosoKeyVault5 --key-permissions get --spn $clientId
    ```

## <a name="deploy-your-pod-with-mounted-secrets-from-your-key-vault"></a>通过密钥保管库中已装载的机密部署 Pod

若要配置 SecretProviderClass 对象，请运行以下命令：
```azurecli
kubectl apply -f secretProviderClass.yaml
```

### <a name="use-managed-identities"></a>使用托管标识

如果使用的是托管标识，请在群集中创建 AzureIdentity，它将引用先前创建的标识。 然后，创建 AzureIdentityBinding，它将引用所创建的 AzureIdentity。 填写以下模板中的参数，并将其另存为 podIdentityAndBinding.yaml。  

```yml
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentity
metadata:
    name: "azureIdentityName"               # The name of your Azure identity
spec:
    type: 0                                 # Set type: 0 for managed service identity
    resourceID: /subscriptions/<SUBID>/resourcegroups/<RESOURCEGROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<AZUREIDENTITYNAME>
    clientID: "managedIdentityClientId"     # The clientId of the Azure AD identity that you created earlier
---
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentityBinding
metadata:
    name: azure-pod-identity-binding
spec:
    azureIdentity: "azureIdentityName"      # The name of your Azure identity
    selector: azure-pod-identity-binding-selector
```
    
运行以下命令来执行绑定：

```azurecli
kubectl apply -f podIdentityAndBinding.yaml
```

接下来，部署 Pod。 以下代码是部署 YAML 文件，它使用上一步中的 pod 标识绑定。 将此文件保存为 podBindingDeployment. yaml。

```yml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-secrets-store-inline
  labels:
    aadpodidbinding: azure-pod-identity-binding-selector
spec:
  containers:
    - name: nginx
      image: nginx
      volumeMounts:
        - name: secrets-store-inline
          mountPath: "/mnt/secrets-store"
          readOnly: true
  volumes:
    - name: secrets-store-inline
      csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
          secretProviderClass: azure-kvname
```

运行以下命令来部署 Pod：

```azurecli
kubectl apply -f podBindingDeployment.yaml
```

### <a name="check-the-pod-status-and-secret-content"></a>检查 Pod 状态和机密内容 

若要显示已部署的 Pod，请运行以下命令：
```azurecli
kubectl get pods
```

若要检查 Pod 的状态，请运行以下命令：
```azurecli
kubectl describe pod/nginx-secrets-store-inline
```

![显示 Pod 的“正在运行”状态并将所有事件显示为“正常”的 Azure CLI 输出的屏幕截图 ](../media/kubernetes-key-vault-6.png)

在“输出”窗口中，部署的 Pod 应处于“正在运行”状态。 在底部的“事件”部分中，所有事件类型都显示为“正常”。

验证确定 Pod 正在运行后，可验证 Pod 是否包含来自密钥保管库的机密。

若要显示 Pod 中包含的所有机密，请运行以下命令：
```azurecli
kubectl exec -it nginx-secrets-store-inline -- ls /mnt/secrets-store/
```

若要显示特定机密的内容，请运行以下命令：
```azurecli
kubectl exec -it nginx-secrets-store-inline -- cat /mnt/secrets-store/secret1
```

请验证机密内容已显示。

## <a name="next-steps"></a>后续步骤

为帮助确保密钥保管库可恢复，请参阅：
> [!div class="nextstepaction"]
> [启用软删除](./key-vault-recovery.md)
