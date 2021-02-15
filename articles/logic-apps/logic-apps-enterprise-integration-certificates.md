---
title: 使用证书保护 B2B 消息
description: 使用 Enterprise Integration Pack 在 Azure 逻辑应用中添加证书以帮助保护 B2B 消息
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/17/2018
ms.openlocfilehash: 03fc17c0d071cef4c8de92c6b50d60d961d18aef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91565253"
---
# <a name="improve-security-for-b2b-messages-by-using-certificates"></a>使用证书提高 B2B 消息的安全性

需要使 B2B 通信保持机密时，可以通过向集成帐户添加证书来提高企业集成应用（特别是逻辑应用）中 B2B 通信的安全性。 证书是数字文档，用于在电子通信中验证参与者身份并通过以下方式保护通信安全：

* 对消息内容进行加密。
* 对消息进行数字签名。

在企业集成应用中，可以使用以下证书：

* [公用证书](https://en.wikipedia.org/wiki/Public_key_certificate)，必须从公用 Internet [证书颁发机构 (CA)](https://en.wikipedia.org/wiki/Certificate_authority) 购买，不需要任何密钥。 

* 私有证书或[*自签名证书*](https://en.wikipedia.org/wiki/Self-signed_certificate)，由你自己创建并颁发，但还需要私钥。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="upload-a-public-certificate"></a>上传公用证书

要在具有 B2B 功能的逻辑应用中使用“公用证书”，必须首先将证书上传到集成帐户中。 在你创建的[协议](logic-apps-enterprise-integration-agreements.md)中定义属性后，可以使用证书来帮助你保护 B2B 消息。

1. 登录到 [Azure 门户](https://portal.azure.com)。 在 Azure 主菜单中，选择“所有资源”。 在搜索框中，输入你的集成帐户名称，然后选择所需的集成帐户。

   ![查找并选择你的集成帐户](media/logic-apps-enterprise-integration-certificates/select-integration-account.png)  

2. 在“组件”下，选择“证书”磁贴。

   ![选择“证书”](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

3. 在“证书”下，选择“添加”。  在“添加证书”下，提供证书的这些详细信息。 完成后，选择“确定”。

   | 属性 | Value | 说明 | 
   |----------|-------|-------------|
   | **名称** | <*证书名称*> | 你的证书的名称，在本例中为“publicCert” | 
   | **证书类型** | 公共 | 你的证书的类型 |
   | **证书** | <*证书文件名*> | 若要查找并选择要上传的证书文件，请选择“证书”框旁边的文件夹图标。 |
   ||||

   ![屏幕截图显示在何处选择 "添加" 以提供证书详细信息。](media/logic-apps-enterprise-integration-certificates/public-certificate-details.png)

   在 Azure 验证你的选择后，Azure 会上传你的证书。

   ![显示 Azure 显示新证书的位置的屏幕截图。](media/logic-apps-enterprise-integration-certificates/new-public-certificate.png) 

## <a name="upload-a-private-certificate"></a>上传私有证书

要在具有 B2B 功能的逻辑应用中使用“私有证书”，必须首先将证书上传到集成帐户中。 还需要有一个私钥，需要首先将其添加到 [Azure Key Vault](../key-vault/general/overview.md)。 

在你创建的[协议](logic-apps-enterprise-integration-agreements.md)中定义属性后，可以使用证书来帮助你保护 B2B 消息。

> [!NOTE]
> 对于专用证书，请确保添加相应的公共证书（出现在 [AS2 协议的](logic-apps-enterprise-integration-as2.md)“发送和接收”设置中），用于对消息进行签名和加密。

1. [将私钥添加到 Azure Key Vault](../key-vault/certificates/certificate-scenarios.md#import-a-certificate) 并提供**密钥名称**。
   
2. 授权 Azure 逻辑应用对 Azure Key Vault 执行操作。 若要向逻辑应用服务主体授予访问权限，请使用 PowerShell 命令 [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)，例如：

   `Set-AzKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName 
   '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`
 
3. 登录到 [Azure 门户](https://portal.azure.com)。 在 Azure 主菜单中，选择“所有资源”。 在搜索框中，输入你的集成帐户名称，然后选择所需的集成帐户。

   ![查找集成帐户](media/logic-apps-enterprise-integration-certificates/select-integration-account.png) 

4. 在“组件”下，选择“证书”磁贴。  

   ![选择“证书”磁贴](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

5. 在“证书”下，选择“添加”。  在“添加证书”下，提供证书的这些详细信息。 完成后，选择“确定”。

   | 属性 | Value | 说明 | 
   |----------|-------|-------------|
   | **名称** | <*证书名称*> | 你的证书的名称，在本例中为“privateCert” | 
   | **证书类型** | 专用 | 你的证书的类型 |
   | **证书** | <*证书文件名*> | 若要查找并选择要上传的证书文件，请选择“证书”框旁边的文件夹图标。 使用密钥保管库保存私钥时，上传的文件将是公共证书。 | 
   | **资源组** | <*集成帐户资源组*> | 你的集成帐户的资源组，在本例中为“MyResourceGroup” | 
   | **密钥保管库** | <*密钥保管库名称*> | 你的 Azure 密钥保管库的名称 |
   | **密钥名称** | <*key-name*> | 你的密钥的名称 |
   ||||

   ![选择“添加”，提供证书详细信息](media/logic-apps-enterprise-integration-certificates/private-certificate-details.png)

   在 Azure 验证你的选择后，Azure 会上传你的证书。

   ![Azure 显示新证书](media/logic-apps-enterprise-integration-certificates/new-private-certificate.png) 

## <a name="next-steps"></a>后续步骤

* [创建 B2B 协议](logic-apps-enterprise-integration-agreements.md)
