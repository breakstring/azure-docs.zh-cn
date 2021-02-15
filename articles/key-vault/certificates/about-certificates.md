---
title: 关于 Azure Key Vault 证书 - Azure Key Vault
description: 概述了 Azure Key Vault REST 接口和证书。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 66f077028b9f9f7a7644a318d4447eeaaab19e98
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2020
ms.locfileid: "94919924"
---
# <a name="about-azure-key-vault-certificates"></a>关于 Azure Key Vault 证书

密钥保管库证书支持适用于 x509 证书管理，它提供以下行为：  

-   允许证书所有者通过密钥保管库创建过程或通过导入现有证书来创建证书。 包括自签名证书和证书颁发机构生成的证书。
-   允许密钥保管库证书所有者在不与私钥材料交互的情况下实现 X509 证书的安全存储和管理。  
-   允许证书所有者创建策略来指示密钥保管库如何管理证书的生命周期。  
-   允许证书所有者提供联系信息用于接收有关证书过期和续订生命周期事件的通知。  
-   支持在选定的颁发者（密钥保管库合作伙伴 X509 证书提供者/证书颁发机构）处自动续订证书。

>[!Note]
>也允许使用非合作伙伴提供者/颁发机构，但将不支持自动续订功能。

## <a name="composition-of-a-certificate"></a>证书组合

创建 Key Vault 证书后，还可以创建具有相同名称的可寻址密钥和机密。 Key Vault 密钥允许密钥操作，Key Vault 机密允许以机密的形式检索证书值。 Key Vault 证书还包含公共 x509 证书元数据。  

标识符和证书版本与密钥和机密的类似。 使用 Key Vault 证书版本创建的特定版本的可寻址密钥和机密可用于 Key Vault 证书响应。
 
![证书是复杂的对象](../media/azure-key-vault.png)

## <a name="exportable-or-non-exportable-key"></a>可导出或不可导出密钥

创建 Key Vault 证书后，可以使用 PFX 或 PEM 格式的私钥从可寻址机密中检索该证书。 用于创建证书的策略必须指示密钥可导出。 如果策略指示密钥不可导出，则在作为机密检索私钥时，该私钥不包括在值中。  

可寻址密钥与不可导出的 KV 证书的相关性变得更高。 可寻址 KV 密钥的操作是从用于创建 KV 证书的 KV 证书策略的“密钥使用情况”字段映射的。  

证书支持的密钥对的类型

 - 支持的 KeyType：RSA、RSA-HSM、EC、EC-HSM 等（参见[此处](/rest/api/keyvault/createcertificate/createcertificate#jsonwebkeytype)）“可导出”状态仅与 RSA 和 EC 一起使用。 HSM 密钥不可导出。

|密钥类型|关于|安全性|
|--|--|--|
|**RSA**| “受软件保护的”RSA 密钥|FIPS 140-2 级别 1|
|**RSA-HSM**| “受 HSM 保护的”RSA 密钥（仅限高级 SKU）|FIPS 140-2 级别 2 HSM|
|**EC**| “受软件保护的”椭圆曲线密钥|FIPS 140-2 级别 1|
|**EC-HSM**| “受 HSM 保护的”Elliptic Curve 密钥（仅限高级 SKU）|FIPS 140-2 级别 2 HSM|
|||

## <a name="certificate-attributes-and-tags"></a>证书属性和标记

除了证书元数据、可寻址密钥和可寻址机密外，Key Vault 证书还包含属性和标记。  

### <a name="attributes"></a>属性

证书属性将镜像到创建 KV 证书时创建的可寻址密钥和机密的属性。  

Key Vault 证书具有以下属性：  

-   enabled：布尔型，可选，默认值为 true。 可以指定，以指示证书数据是否可以作为机密进行检索，或者可以作为密钥进行操作。 还可与 nbf 和 exp 结合使用，如果在 nbf 和 exp 之间出现操作，只有在 enabled 设置为 true 时，才允许该操作   。 nbf 和 exp 时段外的操作会自动禁止。  

在响应中还包括以下其他只读属性：

-   *created*：IntDate：指示创建此版本的证书的时间。  
-   *updated*：IntDate：指示更新此版本的证书的时间。  
-   *exp*：IntDate：包含 x509 证书的过期日期的值。  
-   *nbf*：IntDate：包含 x509 证书的日期的值。  

> [!Note] 
> 如果 Key Vault 证书过期，则它是可寻址密钥，机密变得无法操作。  

### <a name="tags"></a>Tags

 客户端指定的键值对字典，类似于密钥和机密中的标记。  

 > [!Note]
> 如果调用方具有该对象类型（密钥、机密或证书）的列出或获取权限，则调用方可读取标记 。

## <a name="certificate-policy"></a>证书策略

证书策略包含有关如何创建和管理 Key Vault 证书生命周期的信息。 具有私钥的证书导入到密钥保管库时，将通过阅读 x509 证书创建一个默认策略。  

从零开始创建 Key Vault 证书时，需要提供策略。 该策略指定如何创建此 Key Vault 证书版本或下一个 Key Vault 证书版本。 建立策略后，便不需要使用连续创建操作创建将来的版本。 所有版本的 Key Vault 证书只有一个策略实例。  

概括而言，证书策略包含以下信息（可在[此处](/powershell/module/az.keyvault/set-azkeyvaultcertificatepolicy?view=azps-4.4.0)找到其定义）：  

-   X509 证书属性：包含主题名称、主题备用名称以及用于创建 x509 证书请求的其他属性。  
-   密钥属性：包含密钥类型、密钥长度、可导出字段和 ReuseKeyOnRenewal 字段。 这些字段指示密钥保管库如何生成密钥。 
     - 支持的 KeyType：RSA、RSA-HSM、EC、EC-HSM、oct（已在[此处](/rest/api/keyvault/createcertificate/createcertificate#jsonwebkeytype)列出） 
-   机密属性：包含可寻址机密的内容类型等机密属性以生成机密值，用于以机密的形式检索证书。  
-   生存期操作：包含 KV 证书生命周期的操作。 每个生存期操作包含：  

     - 触发器：通过距离到期的天数或生存期范围百分比指定  

     - 操作：指定操作类型 - emailContacts 或 autoRenew  

-   颁发者：有关用于颁发 x509 证书的证书颁发者的参数。  
-   策略属性：包含与策略关联的属性  

### <a name="x509-to-key-vault-usage-mapping"></a>X509 到 Key Vault 使用情况的映射

下表表示 x509 密钥使用策略映射到在创建 Key Vault 证书过程中创建的密钥的有效密钥操作。

|X.509 密钥使用情况标记|Key Vault 密钥的操作|默认行为|
|----------|--------|--------|
|DataEncipherment|加密、解密| 空值 |
|DecipherOnly|解密| 空值  |
|DigitalSignature|签名、验证| Key Vault 在创建证书时默认为无使用规范 | 
|EncipherOnly|encrypt| 空值 |
|KeyCertSign|签名、验证|空值|
|KeyEncipherment|包装密钥、解包密钥| Key Vault 在创建证书时默认为无使用规范 | 
|NonRepudiation|签名、验证| 空值 |
|crlsign|签名、验证| 空值 |

## <a name="certificate-issuer"></a>证书颁发者

Key Vault 证书对象包含与所选证书颁发者提供者进行通信的配置以订购 x509 证书。  

-   Key Vault 合作伙伴，具有 TLS/SSL 证书的以下证书颁发者提供者

|提供者名称|**位置**|
|----------|--------|
|DigiCert|公有云和 Azure 政府中的所有密钥保管库服务位置均支持|
|GlobalSign|公有云和 Azure 政府中的所有密钥保管库服务位置均支持|

可以在 Key Vault 中创建的证书颁发者之前，必须成功完成以下必需的步骤 1 和 2。  

1. 加入证书颁发机构 (CA) 提供者  

    -   组织管理员必须将他们的公司（例如， Contoso）加入到至少一个 CA 提供者。  

2. 管理员为 Key Vault 创建请求者证书以注册（和续订）TLS/SSL 证书  

    -   提供用于在密钥保管库中创建提供程序的颁发者对象的配置  

有关从证书门户创建颁发者对象的详细信息，请参阅 [Key Vault 证书博客](/archive/blogs/kv/manage-certificates-via-azure-key-vault)  

Key Vault 允许使用其他颁发者提供者的配置创建多个颁发者对象。 在创建颁发者对象以后，即可在一个或多个证书的策略中引用其名称。 在创建和续订证书的过程中从 CA 提供者请求 x509 证书时，引用颁发者对象可以指示 Key Vault 按颁发者对象中的规定使用配置。  

颁发者对象在保管库中创建，并且仅可用于同一个保管库中的 KV 证书。  

## <a name="certificate-contacts"></a>证书联系人

证书联系人包含联系人信息以发送由证书生存期事件触发的通知。 密钥保管库中的所有证书共享联系人信息。 如果保管库中的任何证书发生事件，所有指定联系人都会收到通知。 有关如何设置证书联系人的信息，请参阅[此处](overview-renew-certificate.md#steps-to-set-certificate-notifications)  

## <a name="certificate-access-control"></a>证书访问控制

 证书的访问控制由 Key Vault 托管，并且由包含这些证书的 Key Vault 提供。 在同一 Key Vault 中，证书的访问控制策略不同于密钥和机密的访问控制策略。 用户可以创建一个或多个保管库来保存证书，以维护方案相应的证书分段和管理。  有关证书访问控制的详细信息，请参阅[此处](certificate-access-control.md)

## <a name="next-steps"></a>后续步骤

- [关于 Key Vault](../general/overview.md)
- [关于键、密钥和证书](../general/about-keys-secrets-certificates.md)
- [关于密钥](../keys/about-keys.md)
- [关于机密](../secrets/about-secrets.md)
- [身份验证、请求和响应](../general/authentication-requests-and-responses.md)
- [Key Vault 开发人员指南](../general/developers-guide.md)
