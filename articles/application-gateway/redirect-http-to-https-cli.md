---
title: 使用 CLI 进行 HTTP 到 HTTPS 重定向
titleSuffix: Azure Application Gateway
description: 了解如何使用 Azure CLI 创建 HTTP 到 HTTPS 重定向并为 TLS 终端添加证书。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 09/24/2020
ms.author: victorh
ms.openlocfilehash: 0d56a1c46f251307755416ef44991ac6f809f330
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566735"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-cli"></a>使用 Azure CLI 创建支持 HTTP 到 HTTPS 重定向的应用程序网关

可以通过 Azure CLI 使用 TLS/SSL 终端的证书创建[应用程序网关](overview.md)。 路由规则用于将 HTTP 流量重定向到应用程序网关中的 HTTPS 端口。 在此示例中，还会为包含两个虚拟机实例的应用程序网关的后端池创建一个[虚拟机规模集](../virtual-machine-scale-sets/overview.md)。

在本文中，学习如何：

* 创建自签名证书
* 设置网络
* 使用证书创建应用程序网关
* 添加侦听器和重定向规则
* 使用默认后端池创建虚拟机规模集

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - 本教程需要 Azure CLI 版本的2.0.4 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="create-a-self-signed-certificate"></a>创建自签名证书

为供生产使用，应导入由受信任的提供程序签名的有效证书。 对于本教程，请使用 openssl 命令创建自签名证书和 pfx 文件。

```console
openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out appgwcert.crt
```

输入对证书有意义的值。 可接受默认值。

```console
openssl pkcs12 -export -out appgwcert.pfx -inkey privateKey.key -in appgwcert.crt
```

输入证书的密码。 在此示例中，使用 *Azure123456!* 。

## <a name="create-a-resource-group"></a>创建资源组

资源组是在其中部署和管理 Azure 资源的逻辑容器。 使用 [az group create](/cli/azure/group) 创建资源组。

以下示例在 eastus 位置创建名为 myResourceGroupAG 的资源组 。

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>创建网络资源

使用 [az network vnet create](/cli/azure/network/vnet) 创建名为 *myVNet* 的虚拟网络和名为 *myAGSubnet* 的子网。 然后，可以使用 [az network vnet subnet create](/cli/azure/network/vnet/subnet) 添加后端服务器所需的名为 *myBackendSubnet* 的子网。 使用 [az network public-ip create](/cli/azure/network/public-ip) 创建名为 *myAGPublicIPAddress* 的公共 IP 地址。

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-the-application-gateway"></a>创建应用程序网关

可以使用 [az network application-gateway create](/cli/azure/network/application-gateway#az-network-application-gateway-create) 创建名为 *myAppGateway* 的应用程序网关。 使用 Azure CLI 创建应用程序网关时，请指定配置信息，例如容量、sku 和 HTTP 设置。 

将应用程序网关分配给之前创建的 *myAGSubnet* 和 *myAGPublicIPAddress* 。 在此示例中，在创建应用程序网关时将关联所创建的证书及其密码。 

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 443 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress \
  --cert-file appgwcert.pfx \
  --cert-password "Azure123456!"

```

 创建应用程序网关可能需要几分钟时间。 创建应用程序网关后，可以看到它的这些新功能：

- *appGatewayBackendPool* - 应用程序网关必须至少具有一个后端地址池。
- *appGatewayBackendHttpSettings* - 指定将端口 80 和 HTTP 协议用于通信。
- *appGatewayHttpListener* - 与 *appGatewayBackendPool* 关联的默认侦听器。
- *appGatewayFrontendIP* - 将 *myAGPublicIPAddress* 分配给 *appGatewayHttpListener* 。
- *rule1* - 与 *appGatewayHttpListener* 关联的默认路由规则。

## <a name="add-a-listener-and-redirection-rule"></a>添加侦听器和重定向规则

### <a name="add-the-http-port"></a>添加 HTTP 端口

可以使用 [az network application-gateway frontend-port create](/cli/azure/network/application-gateway/frontend-port#az-network-application-gateway-frontend-port-create) 向应用程序网关添加 HTTP 端口。

```azurecli-interactive
az network application-gateway frontend-port create \
  --port 80 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name httpPort
```

### <a name="add-the-http-listener"></a>添加 HTTP 侦听器

可以使用 [az network application-gateway http-listener create](/cli/azure/network/application-gateway/http-listener#az-network-application-gateway-http-listener-create) 向应用程序网关添加名为 *myListener* 的侦听器。

```azurecli-interactive
az network application-gateway http-listener create \
  --name myListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port httpPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-the-redirection-configuration"></a>添加重定向配置

使用 [az network application-gateway redirect-config create](/cli/azure/network/application-gateway/redirect-config#az-network-application-gateway-redirect-config-create) 将 HTTP 到 HTTPS 重定向配置添加到应用程序网关。

```azurecli-interactive
az network application-gateway redirect-config create \
  --name httpToHttps \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --type Permanent \
  --target-listener appGatewayHttpListener \
  --include-path true \
  --include-query-string true
```

### <a name="add-the-routing-rule"></a>添加路由规则

使用 [az network application-gateway rule create](/cli/azure/network/application-gateway/rule#az-network-application-gateway-rule-create) 将具有重定向配置的名为 *rule2* 的路由规则添加到应用程序网关。

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name rule2 \
  --resource-group myResourceGroupAG \
  --http-listener myListener \
  --rule-type Basic \
  --redirect-config httpToHttps
```

## <a name="create-a-virtual-machine-scale-set"></a>创建虚拟机规模集

在此示例中，将创建一个名为 *myvmss* 的虚拟机规模集，以便为应用程序网关的后端池提供服务器。 规模集中的虚拟机与 *myBackendSubnet* 和 *appGatewayBackendPool* 相关联。 若要创建规模集，可以使用 [az vmss create](/cli/azure/vmss#az-vmss-create)。

```azurecli-interactive
az vmss create \
  --name myvmss \
  --resource-group myResourceGroupAG \
  --image UbuntuLTS \
  --admin-username azureuser \
  --admin-password Azure123456! \
  --instance-count 2 \
  --vnet-name myVNet \
  --subnet myBackendSubnet \
  --vm-sku Standard_DS2 \
  --upgrade-policy-mode Automatic \
  --app-gateway myAppGateway \
  --backend-pool-name appGatewayBackendPool
```

### <a name="install-nginx"></a>安装 NGINX

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroupAG \
  --vmss-name myvmss \
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'
```

## <a name="test-the-application-gateway"></a>测试应用程序网关

若要获取应用程序网关的公共 IP 地址，可以使用 [az network public-ip show](/cli/azure/network/public-ip)。 复制该公共 IP 地址，并将其粘贴到浏览器的地址栏。

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![安全警告](./media/redirect-http-to-https-cli/application-gateway-secure.png)

若要接受有关使用自签名证书的安全警告，请依次选择“详细信息”和“继续转到网页”。   随即显示受保护的 NGINX 站点，如下例所示：

![在应用程序网关中测试基 URL](./media/redirect-http-to-https-cli/application-gateway-nginxtest.png)

## <a name="next-steps"></a>后续步骤

- [使用 Azure CLI 创建支持内部重定向的应用程序网关](redirect-internal-site-cli.md)