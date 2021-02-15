---
title: 安全存储访问凭据
titleSuffix: Azure Data Science Virtual Machine
description: 了解如何在 Data Science Virtual Machine 上安全地存储访问凭据。 你将了解如何使用托管服务标识和 Azure Key Vault 存储访问凭据。
keywords: 深度学习, AI, 数据科学工具, 数据科学虚拟机, 地理空间分析, 团队数据科学过程
services: machine-learning
ms.service: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 05/08/2018
ms.openlocfilehash: adc66fc794ac6cce3fc0f8fd1552f701a4968860
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519671"
---
# <a name="store-access-credentials-securely-on-an-azure-data-science-virtual-machine"></a>在 Azure Data Science Virtual Machine 上安全存储访问凭据

通常，云应用程序中的代码包含用于对云服务进行身份验证的凭据。 如何管理和保护这些凭据是构建云应用程序的已知挑战。 理想情况下，凭据应永远不会出现在开发者工作站上，也永远不会被签入源代码管理系统中。

[Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)功能为 Azure 服务提供了 Azure Active Directory (Azure AD) 中的自动托管标识，更巧妙地解决了这个问题。 可以使用此标识向支持 Azure AD 身份验证的任何服务进行身份验证，而无需在代码中放入任何凭据。

保护凭据的一个常见方法是结合使用 Windows Installer (MSI) 和 [Azure Key Vault](../../key-vault/index.yml)（一项安全存储机密和加密密钥的托管 Azure 服务）。 可以使用托管标识访问密钥保管库，然后从该密钥保管库检索授权机密和加密密钥。

Azure 资源的托管标识和 Key Vault 文档包含用于深入了解这些服务信息的综合资源。 本文的其余部分将介绍数据科学虚拟机 (DSVM) 上 MSI 和 Key Vault 的基本使用步骤，以访问 Azure 资源。 

## <a name="create-a-managed-identity-on-the-dsvm"></a>在 DSVM 上创建托管标识

```azurecli-interactive
# Prerequisite: You have already created a Data Science VM in the usual way.

# Create an identity principal for the VM.
az vm assign-identity -g <Resource Group Name> -n <Name of the VM>
# Get the principal ID of the DSVM.
az resource list -n <Name of the VM> --query [*].identity.principalId --out tsv
```

## <a name="assign-key-vault-access-permissions-to-a-vm-principal"></a>为 VM 主体分配 Key Vault 访问权限

```azurecli-interactive
# Prerequisite: You have already created an empty Key Vault resource on Azure by using the Azure portal or Azure CLI.

# Assign only get and set permissions but not the capability to list the keys.
az keyvault set-policy --object-id <Principal ID of the DSVM from previous step> --name <Key Vault Name> -g <Resource Group of Key Vault>  --secret-permissions get set
```

## <a name="access-a-secret-in-the-key-vault-from-the-dsvm"></a>从 DSVM 访问 Key Vault 中的机密

```bash
# Get the access token for the VM.
x=`curl http://localhost:50342/oauth2/token --data "resource=https://vault.azure.net" -H Metadata:true`
token=`echo $x | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`

# Access the key vault by using the access token.
curl https://<Vault Name>.vault.azure.net/secrets/SQLPasswd?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

## <a name="access-storage-keys-from-the-dsvm"></a>从 DSVM 访问存储密钥

```bash
# Prerequisite: You have granted your VMs MSI access to use storage account access keys based on instructions at https://docs.microsoft.com/azure/active-directory/managed-service-identity/tutorial-linux-vm-access-storage. This article describes the process in more detail.

y=`curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true`
ytoken=`echo $y | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`
curl https://management.azure.com/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup of Storage account>/providers/Microsoft.Storage/storageAccounts/<Storage Account Name>/listKeys?api-version=2016-12-01 --request POST -d "" -H "Authorization: Bearer $ytoken"

# Now you can access the data in the storage account from the retrieved storage account keys.
```

## <a name="access-the-key-vault-from-python"></a>从 Python 访问 Key Vault

```python
from azure.keyvault import KeyVaultClient
from msrestazure.azure_active_directory import MSIAuthentication

"""MSI Authentication example."""

# Get credentials.
credentials = MSIAuthentication(
    resource='https://vault.azure.net'
)

# Create a Key Vault client.
key_vault_client = KeyVaultClient(
    credentials
)

key_vault_uri = "https://<key Vault Name>.vault.azure.net/"

secret = key_vault_client.get_secret(
    key_vault_uri,  # Your key vault URL.
    # The name of your secret that already exists in the key vault.
    "SQLPasswd",
    ""              # The version of the secret; empty string for latest.
)
print("My secret value is {}".format(secret.value))
```

## <a name="access-the-key-vault-from-azure-cli"></a>从 Azure CLI 访问 Key Vault

```azurecli-interactive
# With managed identities for Azure resources set up on the DSVM, users on the DSVM can use Azure CLI to perform the authorized functions. The following commands enable access to the key vault from Azure CLI without requiring login to an Azure account.
# Prerequisites: MSI is already set up on the DSVM as indicated earlier. Specific permissions, like accessing storage account keys, reading specific secrets, and writing new secrets, are provided to the MSI.

# Authenticate to Azure CLI without requiring an Azure account. 
az login --msi

# Retrieve a secret from the key vault. 
az keyvault secret show --vault-name <Vault Name> --name SQLPasswd

# Create a new secret in the key vault.
az keyvault secret set --name MySecret --vault-name <Vault Name> --value "Helloworld"

# List access keys for the storage account.
az storage account keys list -g <Storage Account Resource Group> -n <Storage Account Name>
```