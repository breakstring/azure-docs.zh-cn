---
title: 为 SMTP 横幅检查配置反向查找区域
titlesuffix: Azure Virtual Network
description: 介绍如何在 Azure 中为 SMTP 横幅检查配置反向查找区域
services: virtual-network
documentationcenter: virtual-network
author: genlin
manager: dcscontentpm
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: c8ffadb8d54db0c2a99dc12e45b5990155a0505e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86135055"
---
# <a name="configure-reverse-lookup-zones-for-an-smtp-banner-check"></a>为 SMTP 横幅检查配置反向查找区域

本文介绍如何使用 Azure DNS 中的反向区域，以及如何为 SMTP 横幅检查创建反向 DNS (PTR) 记录。

## <a name="symptom"></a>症状

如果在 Microsoft Azure 中托管 SMTP 服务器，则通过自远程邮件服务器收发邮件时，可能收到以下错误消息：

**554: 无 PTR 记录**

## <a name="solution"></a>解决方案

对于 Azure 中的虚拟 IP 地址，将在 Microsoft 拥有的域区域（而不是自定义域区域）创建反向记录。

若要在 Microsoft 拥有区域配置 PTR 记录，请对 PublicIpAddress 资源使用 -ReverseFqdn 属性。 有关详细信息，请参阅[为 Azure 中托管的服务配置反向 DNS](../dns/dns-reverse-dns-for-azure-services.md)。

配置 PTR 记录时，请确保 IP 地址和反向 FQDN 为订阅所有。 如果尝试设置不属于订阅的反向 FQDN，将收到以下错误消息：

```output
Set-AzPublicIpAddress : ReverseFqdn mail.contoso.com that PublicIPAddress ip01 is trying to use does not belong to subscription <Subscription ID>. One of the following conditions need to be met to establish ownership:
                    
1) ReverseFqdn matches fqdn of any public ip resource under the subscription;
2) ReverseFqdn resolves to the fqdn (through CName records chain) of any public ip resource under the subscription;
3) It resolves to the ip address (through CName and A records chain) of a static public ip resource under the subscription.
```

如果将 SMTP 横幅手动更改为与默认反向 FQDN 相匹配，远程邮件服务器仍可能失败，因为它可能期望 SMTP 横幅主机与域的 MX 记录相匹配。
