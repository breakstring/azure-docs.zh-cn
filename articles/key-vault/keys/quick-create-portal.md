---
title: Azure 快速入门 - 使用 Azure 门户在密钥保管库中设置和检索密钥 | Microsoft Docs
description: 本快速入门展示了如何使用 Azure 门户在 Azure Key Vault 中设置和检索密钥
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/24/2020
ms.author: mbaldwin
ms.openlocfilehash: 419fe72d400177ce9fa3d9811d7dfa06e74c0810
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935047"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-the-azure-portal"></a>快速入门：使用 Azure 门户在 Azure Key Vault 中设置和检索密钥

Azure Key Vault 是一项云服务，它为机密提供了安全的存储。 可以安全地存储密钥、密码、证书和其他机密。 可以通过 Azure 门户创建和管理 Azure Key Vault。 在本快速入门中，你将创建一个密钥保管库并使用它来存储密钥。 有关 Key Vault 的详细信息，请参阅[概述](../general/overview.md)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="create-a-vault"></a>创建保管库

1. 在 Azure 门户菜单或“主页”中，选择“创建资源” 。
2. 在“搜索”框中输入“Key Vault”。
3. 从结果列表中选择“Key Vault”。
4. 在“Key Vault”部分，选择“创建”。
5. 在“创建密钥保管库”部分，提供以下信息：
    - **Name**：必须提供唯一的名称。 在本快速入门中，我们使用 Example-Vault。 
    - **订阅**：选择订阅。
    - 在“资源组”下选择“新建”，然后输入资源组名称。
    - 在“位置”下拉菜单中选择一个位置。
    - 让其他选项保留默认值。
6. 提供上述信息后，选择“创建”。

请记下下面列出的两个属性：

* **保管库名称**：在示例中，此项为 Example-Vault。 将在其他步骤中使用此名称。
* **保管库 URI**：在本示例中，此项为 https://example-vault.vault.azure.net/ 。 通过其 REST API 使用保管库的应用程序必须使用此 URI。

目前，只有你的 Azure 帐户有权对这个新保管库执行操作。

![Key Vault 创建完成后的输出](../media/keys/quick-create-portal/vault-properties.png)

## <a name="add-a-key-to-key-vault"></a>向密钥保管库中添加密钥

只需再执行几个步骤便可向保管库中添加密钥。 在此示例中，我们添加可供应用程序使用的密钥。 此密钥称为 ExampleKey  。

1. 在密钥保管库属性页中，选择“密钥”  。
2. 单击“生成/导入”  。
3. 在“创建密钥”  屏幕上，选择以下值：
    - **选项**：生成。
    - **名称**：ExampleKey。
    - 让其他值保留默认设置。 单击 **创建**。

收到密钥已成功创建的消息后，即可单击列表中的该密钥， 然后就可以看到某些属性。 如果单击当前版本，则可看到在上一步指定的值。

![密钥属性](../media/keys/quick-create-portal/current-version-hidden.png)


## <a name="clean-up-resources"></a>清理资源

其他 Key Vault 快速入门和教程是在本快速入门的基础上制作的。 如果打算继续使用后续的快速入门和教程，则可能需要保留这些资源。
如果不再需要资源组，可以将其删除，这将删除 Key Vault 和相关的资源。 要通过门户删除资源组，请执行以下操作：

1. 在门户顶部的“搜索”框中输入资源组的名称。 在搜索结果中看到在本快速入门中使用的资源组后，将其选中。
2. 选择“删除资源组”。
3. 在“键入资源组名称:”框中，键入资源组的名称，然后选择“删除” 。


## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了一个密钥保管库并在其中存储了一个密钥。 若要详细了解 Key Vault 以及如何将其与应用程序集成，请继续阅读以下文章。

- 阅读 [Azure Key Vault 概述](../general/overview.md)
- 参阅 [Azure Key Vault 开发人员指南](../general/developers-guide.md)
- 请参阅 [Key Vault 安全性概述](../general/security-overview.md)