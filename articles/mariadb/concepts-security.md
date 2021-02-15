---
title: 安全性 - Azure Database for MariaDB
description: Azure Database for MariaDB 中的安全性功能概述。
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 9e6ca797b1e982e1ef632ba99e9210e3c347a2f1
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664174"
---
# <a name="security-in-azure-database-for-mariadb"></a>Azure Database for MariaDB 中的安全性

可以通过多层安全性来保护 Azure Database for MariaDB 服务器上的数据。 本文概述了这些安全选项。

## <a name="information-protection-and-encryption"></a>信息保护和加密

### <a name="in-transit"></a>动态
Azure Database for MariaDB 使用传输层安全性来加密动态数据，通过这种方式来保护数据。 默认情况下，强制实施加密 (SSL/TLS)。

### <a name="at-rest"></a>静态
Azure Database for MariaDB 服务使用 FIPS 140-2 验证的加密模块对静态数据进行存储加密。 数据（包括备份）在磁盘上加密，运行查询时创建的临时文件除外。 该服务使用包含在 Azure 存储加密中的 AES 256 位密码，并且密钥由系统进行管理。 存储加密始终处于启用状态，无法禁用。


## <a name="network-security"></a>网络安全性
到 Azure Database for MariaDB 服务器的连接首先通过区域性网关进行路由。 网关 IP 可以公开访问，而服务器 IP 地址则受保护。 有关网关的详细信息，请参阅[连接体系结构文章](concepts-connectivity-architecture.md)。  

新创建的 Azure Database for MariaDB 服务器有一个防火墙，可以阻止所有外部连接。 它们可以到达网关，但不能连接到服务器。 

### <a name="ip-firewall-rules"></a>IP 防火墙规则
IP 防火墙规则基于每个请求的起始 IP 地址授予对服务器的访问权限。 有关详细信息，请参阅[防火墙规则概述](concepts-firewall-rules.md)。

### <a name="virtual-network-firewall-rules"></a>虚拟网络防火墙规则
虚拟网络服务终结点将虚拟网络连接扩展到 Azure 主干网。 使用虚拟网络规则，Azure Database for MariaDB 服务器就会允许从虚拟网络中的所选子网进行连接。 有关详细信息，请参阅[虚拟网络服务终结点概述](concepts-data-access-security-vnet.md)。


## <a name="access-management"></a>访问管理

在创建 Azure Database for MariaDB 服务器时，我们会提供管理员用户的凭据。 可以通过此管理员创建其他 MariaDB 用户。


## <a name="threat-protection"></a>威胁防护

你可以选择 " [高级威胁防护](concepts-data-access-and-security-threat-protection.md) "，它会检测异常活动，这些活动表示对访问或利用服务器的异常和潜在有害尝试。

[审核日志记录](concepts-audit-logs.md) 可用于跟踪数据库中的活动。 


## <a name="next-steps"></a>后续步骤
- 为 [IP](concepts-firewall-rules.md) 或[虚拟网络](concepts-data-access-security-vnet.md)启用防火墙规则