---
title: 常见问题 - Azure 专用 HSM | Microsoft Docs
description: 查找有关 Azure 专用硬件安全模块的常见问题的解答，如基本信息、互操作性、高可用性和支持。
services: dedicated-hsm
author: keithp
manager: rkarlin
tags: azure-resource-manager
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/10/2020
ms.author: keithp
ms.openlocfilehash: 1f349931fb479be3b3ba936b3d0a665427357f29
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2020
ms.locfileid: "97369032"
---
# <a name="frequently-asked-questions-faq"></a>常见问题 (FAQ)

查找有关 Microsoft Azure 专用 HSM 的常见问题的解答。

## <a name="the-basics"></a>基础知识

### <a name="q-what-is-a-hardware-security-module-hsm"></a>问：什么是硬件安全模块 (HSM)？

硬件安全模块 (HSM) 是用于保护和管理加密密钥的物理计算设备。 HSM 中存储的密钥可用于加密操作。 密钥材料安全保存在防篡改的硬件模块中。 HSM 仅允许经过身份验证和授权的应用程序使用密钥。 密匙材料永远不会离开 HSM 保护边界。

### <a name="q-what-is-the-azure-dedicated-hsm-offering"></a>问：什么是 Azure 专用 HSM 产品？

Azure 专用 HSM 是一种基于云的服务，提供由可直接连接到客户虚拟网络的 Azure 数据中心托管的 HSM。 这些 Hsm 是专用的网络设备 (Thales 网络 Luna HSM 7) 。 它们直接部署到客户的专用 IP 地址空间，Microsoft 没有任何权限访问 HSM 的加密功能。 只有客户才对这些设备拥有完全的管理和加密控制权。 客户负责设备的管理，他们可以直接从设备获取完整的活动日志。 专用 HSM 可帮助客户满足合规性/法规要求，例如 FIPS 140-2 级别 3、HIPAA、PCI-DSS、eIDAS 等。

### <a name="q-what-hardware-is-used-for-dedicated-hsm"></a>问：哪些硬件用于专用 HSM？

Microsoft 与 Thales 合作，提供 Azure 专用 HSM 服务。 使用的特定设备是 [Thales 网络 LUNA HSM 7](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms)。 此设备不仅提供 FIPS 140-2 级别 3 验证的固件，而且还通过 10 个分区提供低延迟、高性能和高容量。 

### <a name="q-what-is-an-hsm-used-for"></a>问：HSM 有什么作用？

Hsm 用于存储加密功能（如 TLS (传输层安全性) 、加密数据、PKI (公钥基础结构) 、DRM (数字版权管理) 和签名文档）所使用的加密密钥。

### <a name="q-how-does-dedicated-hsm-work"></a>问：专用 HSM 的工作原理是怎样的？

客户可以使用 PowerShell 或命令行接口在特定区域内预配 HSM。 客户指定要将 HSM 连接到哪个虚拟网络；预配后，可以通过客户专用 IP 地址空间中分配的 IP 地址在指定的子网中使用 HSM。 然后，客户可以使用 SSH 连接到 HSM 进行设备管理和控制、建立 HSM 客户端连接、初始化 HSM、创建分区以及定义和分配角色（例如分区管理人员、加密管理人员和加密用户）。 然后，客户将使用 Thales 提供的 HSM 客户端工具/SDK/软件来从其应用程序执行加密操作。

### <a name="q-what-software-is-provided-with-the-dedicated-hsm-service"></a>问：专用 HSM 服务提供哪些软件？

Thales 为 HSM 设备提供一次预配的所有软件。 该软件在 [Thales 客户支持门户](https://supportportal.gemalto.com/csm/)中提供。 需要为 Thales 支持人员注册使用专用 HSM 服务的客户，并获得支持访问和下载相关软件的客户 ID。 支持的客户端软件是版本7.2，与 FIPS 140-2 第3级验证固件版本7.0.3 兼容。 

### <a name="q-what-extra-costs-may-be-incurred-with-dedicated-hsm-service"></a>问：专用 HSM 服务可能导致哪些额外的成本？

使用专用 HSM 服务时，以下各项会产生额外的费用。 
* 使用专用的本地备份设备可与专用 HSM 服务一起使用，但这会产生额外的费用，并且应直接源自 Thales。
* 专用 HSM 提供10个分区许可证。 如果客户需要更多分区，这会产生额外的费用，以便直接来源于 Thales。
* 专用 HSM 需要 (VNET、VPN 网关等 ) 和资源（例如用于设备配置的虚拟机）的网络基础结构。 这些额外资源会产生额外的费用，而不包括在专用 HSM 服务定价中。

### <a name="q-does-azure-dedicated-hsm-offer-password-based-and-ped-based-authentication"></a>问： Azure 专用 HSM 提供基于密码和基于 PED 的身份验证吗？

目前，Azure 专用 HSM 仅为 HSM 提供了基于密码的身份验证。

### <a name="q-will-azure-dedicated-hsm-host-my-hsms-for-me"></a>问： Azure 专用 HSM 是否会托管我的 Hsm？

Microsoft 仅通过专用 HSM 服务提供 Thales 网络 Luna HSM 7，并且不能托管任何客户提供的设备。

### <a name="q-does-azure-dedicated-hsm-support-payment-pineft-features"></a>问： Azure 专用 HSM 是否支持支付 (固定/EFT) 功能？

Azure 专用 HSM 服务使用 Thales 网络 Luna HSM 7 设备。 这些设备不支持特定于 HSM 的功能 (例如 PIN 或 EFT) 或证书。 如果希望 Azure 专用 HSM 服务在将来支持支付 Hsm，请将反馈传递给你的 Microsoft 客户代表。

### <a name="q-which-azure-regions-is-dedicated-hsm-available-in"></a>问：哪些 Azure 区域是专用的 HSM？

从2019年3月晚晚些，专用 HSM 将在下面列出的14个区域中提供。 还规划了更多区域，可以通过 Microsoft 客户代表进行讨论。

* 美国东部
* 美国东部 2
* 美国西部
* 美国西部 2
* 美国中南部
* Southeast Asia
* 东亚
* 印度中部
* 印度南部
* 日本东部
* 日本西部
* 北欧
* 西欧
* 英国南部
* 英国西部
* 加拿大中部
* 加拿大东部
* 澳大利亚东部
* 澳大利亚东南部
* 瑞士北部
* 瑞士西部
* US Gov 弗吉尼亚州
* US Gov 德克萨斯州

## <a name="interoperability"></a>互操作性

### <a name="q-how-does-my-application-connect-to-a-dedicated-hsm"></a>问：应用程序如何连接到专用 HSM？

使用 Thales 提供的 HSM 客户端工具/SDK/软件来从应用程序执行加密操作。 该软件在 [Thales 客户支持门户](https://supportportal.gemalto.com/csm/)中提供。 需要为 Thales 支持人员注册使用专用 HSM 服务的客户，并获得支持访问和下载相关软件的客户 ID。

### <a name="q-can-an-application-connect-to-dedicated-hsm-from-a-different-vnet-in-or-across-regions"></a>问：应用程序是否可以从不同的 VNET 或跨区域连接到专用 HSM？

是的。需要在某个区域中使用 [VNET 对等互连](../virtual-network/virtual-network-peering-overview.md)跨虚拟网络建立连接。 对于跨区域连接，必须使用 [VPN 网关](../vpn-gateway/vpn-gateway-about-vpngateways.md)。

### <a name="q-can-i-synchronize-dedicated-hsm-with-on-premises-hsms"></a>问：是否可将专用 HSM 与本地 HSM 同步？

是的，可将本地 HSM 与专用 HSM 同步。 可以使用[点到点 VPN 或点到站点连接](../vpn-gateway/vpn-gateway-about-vpngateways.md)来与本地网络建立连接。

### <a name="q-can-i-encrypt-data-used-by-other-azure-services-using-keys-stored-in-dedicated-hsm"></a>问：是否可以使用专用 HSM 中存储的密钥来加密其他 Azure 服务所用的数据？

错误。 只能从虚拟网络内部访问 Azure 专用 HSM。

### <a name="q-can-i-import-keys-from-an-existing-on-premises-hsm-to-dedicated-hsm"></a>问：是否可将现有本地 HSM 中的密钥导入到专用 HSM？

是，如果你有本地 Thales 网络 Luna HSM 7 Hsm。 可以使用多种方法， 请参阅 [THALES HSM 文档](https://thalesdocs.com/gphsm/luna/7.2/docs/network/Content/Home_network.htm)。

### <a name="q-what-operating-systems-are-supported-by-dedicated-hsm-client-software"></a>问：专用 HSM 客户端软件支持哪些操作系统？

* Windows、Linux、Solaris、AIX、HP-UX、FreeBSD
* 虚拟： VMware、Hyper-v、Xen、KVM

### <a name="q-how-do-i-configure-my-client-application-to-create-a-high-availability-configuration-with-multiple-partitions-from-multiple-hsms"></a>问：如何将客户端应用程序配置为使用多个 HSM 中的多个分区创建高可用性配置？

若要获得高可用性，需将 HSM 客户端应用程序配置设置为使用每个 HSM 中的分区。 请参阅 Thales HSM 客户端软件文档。

### <a name="q-what-authentication-mechanisms-are-supported-by-dedicated-hsm"></a>问：专用 HSM 支持哪些身份验证机制？

Azure 专用 HSM 使用 SafeNet Network HSM 7 设备（型号 A790），支持基于密码的身份验证。

### <a name="q-what-sdks-apis-client-software-is-available-to-use-with-dedicated-hsm"></a>问：可在专用 HSM 中使用哪些 SDK、API 和客户端软件？

PKCS#11、Java (JCA/JCE)、Microsoft CAPI、CNG 和 OpenSSL

### <a name="q-can-i-importmigrate-keys-from-luna-56-hsms-to-azure-dedicated-hsms"></a>问：是否可将 Luna 5/6 HSM 中的密钥导入/迁移到 Azure 专用 HSM？

是。 请联系 Thales 代表以获取适当的 Thales 迁移指南。 

## <a name="using-your-hsm"></a>使用 HSM

### <a name="q-how-do-i-decide-whether-to-use-azure-key-vault-or-azure-dedicated-hsm"></a>问：如何确定是要使用 Azure Key Vault 还是 Azure 专用 HSM？

对于想要迁移到使用 HSM 的 Azure 本地应用程序的企业而言，Azure 专用 HSM 是适当的选择。 使用专用 HSM 提供的某个选项，只需进行极少量的更改即可迁移应用程序。 如果在 Azure VM 或 Web 应用上运行的应用程序代码中执行加密操作，则客户可以使用专用 HSM。 通常，在 IaaS 中运行的压缩包装软件 (基础结构即服务) 模型，作为密钥存储的支持，可以使用专用的 HSM，如应用程序网关或用于无键 TLS 的流量管理器，ADCS (Active Directory 证书服务) ，或类似的 PKI 工具、用于文档签名的工具/应用程序、用于文档签名的工具/应用程序、代码签名，或使用 EKM (可扩展密钥管理) 提供程序在 HSM 中使用主密钥 (的 SQL Server)  Azure Key Vault 适用于 "以云为云" 的应用程序，或用于在 PaaS (平台即服务) 或 SaaS (软件即服务) 方案（如 Office 365 客户密钥）处理客户数据的静态加密方案 Azure 信息保护，Azure 磁盘加密，Azure Data Lake Store 通过客户托管的密钥进行加密，通过客户托管密钥进行 Azure 存储加密，以及通过客户托管密钥进行 azure SQL。

### <a name="q-what-usage-scenarios-best-suit-azure-dedicated-hsm"></a>问：Azure 专用 HSM 最适合哪些使用方案？

Azure 专用 HSM 是最适合迁移方案。 即，将已在使用 HSM 的本地应用程序迁移到 Azure。 使用专用 HSM 提供的一个低冲突选项，只需对应用程序进行极少量的更改，即可迁移到 Azure。 如果在 Azure VM 或 Web 应用上运行的应用程序代码中执行加密操作，则可以使用专用 HSM。 一般而言，在支持使用 HSM 作为密钥存储的 IaaS（基础结构即服务）模型中运行的套装软件可以使用专用 HSM，例如：

* 应用程序网关或无键 TLS 的流量管理器
* ADCS（Active Directory 证书服务）
* 类似的 PKI 工具
* 用于文档签名的工具/应用程序
* 代码签名
* 配置为使用 EKM（可扩展密钥管理）提供程序通过 HSM 中的主密钥进行 TDE（透明数据库加密）的 SQL Server (IaaS)

### <a name="q-can-dedicated-hsm-be-used-with-office-365-customer-key-azure-information-protection-azure-data-lake-store-disk-encryption-azure-storage-encryption-azure-sql-tde"></a>问：专用 HSM 是否可与 Office 365 客户密钥、Azure 信息保护、Azure Data Lake Store、磁盘加密、Azure 存储加密和 Azure SQL TDE 一起使用？

错误。 专用 HSM 直接预配到客户的专用 IP 地址空间，因此它无法由其他 Azure 或 Microsoft 服务进行访问。

## <a name="administration-access-and-control"></a>管理、访问和控制

### <a name="q-does-the-customer-get-full-exclusive-control-over-the-hsms-with-dedicated-"></a>问：客户是否完全完全控制具有专用的 Hsm？

是。 每个 HSM 设备完全由一个客户专用，经预配或者更改管理员密码后，其他任何人都对它没有管理控制权。

### <a name="q-what-level-of-access-does-microsoft-have-to-my-hsm"></a>问：Microsoft 对我的 HSM 拥有哪种访问级别？

Microsoft 对 HSM 没有任何管理或加密控制权。 但 Microsoft 确实拥有监视级别的访问权限，它可以通过串行端口连接来检索基本的遥测数据，例如温度和组件运行状况。 这样，Microsoft 便可以针对运行状况问题提供主动通知。 如果需要，客户可以禁用此帐户。

### <a name="q-what-is-the-tenant-admin-account-microsoft-uses-i-am-used-to-the-admin-user-being-admin-on-thales-luna-hsms"></a>问： Microsoft 使用的是 "租户管理员" 帐户，我曾使用 Thales Luna Hsm 上的管理员用户作为 "管理员" 吗？

HSM 设备附带了默认的 "管理员" 用户及其默认密码。 当任何设备处于等待由客户设置的池中时，Microsoft 不希望使用默认密码。 这不能满足我们的严格安全要求。 出于此原因，我们设置了一个强密码，该密码会在预配时被丢弃。 此外，在设置时，我们会在名为 "租户管理员" 的管理员角色中创建一个新用户。 此用户具有默认密码，客户会将此作为首次登录到新设置的设备时的第一个操作。 此过程可确保高程度的安全性，并为我们的客户提供唯一的管理控制承诺。 应注意的是，如果客户首选使用该帐户，则可以使用 "租户管理员" 用户重置管理员用户密码。 

### <a name="q-can-microsoft-or-anyone-at-microsoft-access-keys-in-my-dedicated-hsm"></a>问：Microsoft 或 Microsoft 的任何员工是否可以访问我的专用 HSM 中的密钥？

错误。 Microsoft 对客户分配的专用 HSM 中存储的密钥没有任何访问权限。

### <a name="q-can-i-upgrade-softwarefirmware-on-hsms-allocated-to-me"></a>问：是否可以在分配给我的 HSM 上升级软件/固件？

如果不同固件版本需要特定功能，则客户具有完全管理控制，包括升级软件/固件。 在进行更改之前，请联系 Microsoft 了解你的升级 HSMRequest@microsoft.com  

### <a name="q-how-do-i-manage-dedicated-hsm"></a>问：如何管理专用 HSM？

可以使用 SSH 访问专用 HSM，然后对其进行管理。

### <a name="q-how-do-i-manage-partitions-on-the-dedicated-hsm"></a>问：如何管理专用 HSM 上的分区？

Thales HSM 客户端软件用于管理 Hsm 和分区。

### <a name="q-how-do-i-monitor-my-hsm"></a>问：如何监视 HSM？

客户可以通过 syslog 和 SNMP 全权访问 HSM 活动日志。 客户需要设置 syslog 服务器或 SNMP 服务器才能从 HSM 接收日志或事件。

### <a name="q-can-i-get-full-access-log-of-all-hsm-operations-from-dedicated-hsm"></a>问：是否可以全权访问专用 HSM 中所有 HSM 操作的日志？

是。 可将来自 HSM 设备的日志发送到 syslog 服务器

## <a name="high-availability"></a>高可用性

### <a name="q-is-it-possible-to-configure-high-availability-in-the-same-region-or-across-multiple-regions"></a>问：是否可以在同一区域或跨多个区域配置高可用性？

是。 高可用性配置和设置是在 Thales 提供的 HSM 客户端软件中执行的。 同一区域或跨区域的同一 VNET 中的 Hsm 或其他 Vnet 中的 Hsm，或者使用站点到站点或点到点 VPN 连接到 VNET 的本地 Hsm 可以添加到相同的高可用性配置。 应注意的是，这只同步密钥材料，而不同步特定的配置项目，如角色。

### <a name="q-can-i-add-hsms-from-my-on-premises-network-to-a-high-availability-group-with-azure-dedicated-hsm"></a>问：我是否可以使用 Azure 专用 HSM 将 Hsm 从我的本地网络添加到高可用性组？

是。 这些 HSM 必须符合 SafeNet Luna Network HSM 7 的高可用性要求。

### <a name="q-can-i-add-luna-56-hsms-from-on-premises-networks-to-a-high-availability-group-with-azure-dedicated-hsm"></a>问：我是否可以使用 Azure 专用 HSM 将 Luna 5/6 Hsm 从本地网络添加到高可用性组？

错误。

### <a name="q-how-many-hsms-can-i-add-to-the-same-high-availability-configuration-from-one-single-application"></a>问：在一个应用程序中可将多少个 HSM 添加到相同的高可用性配置？

高可用性组的16个成员的未通过、完全限制测试具有极佳的结果。

## <a name="support"></a>支持

### <a name="q-what-is-the-sla-for-dedicated-hsm-service"></a>问：专用 HSM 服务的 SLA 是什么？

没有为专用 HSM 服务提供特定的运行时间保证。 Microsoft 确保设备的网络级访问，因此标准 Azure 网络 SLA 适用。

### <a name="q-how-are-the-hsms-used-in-azure-dedicated-hsm-protected"></a>问：Azure 专用 HSM 中使用的 HSM 受到怎样的保护？

Azure 数据中心提供全面的物理和程序性安全控制。 除此之外，专用 HSM 托管在数据中心内进一步限制访问的区域。 这些区域装配了其他物理访问控制机制和监控摄像头，安全性得到进一步提高。

### <a name="q-what-happens-if-there-is-a-security-breach-or-hardware-tampering-event"></a>问：如果出现安全漏洞或硬件篡改事件，会发生什么情况？

专用 HSM 服务使用 Thales 网络 Luna HSM 7 设备。 这些设备支持物理和逻辑篡改检测。 如果出现篡改事件，HSM 将自动归零。

### <a name="q-how-do-i-ensure-that-keys-in-my-dedicated-hsms-are-not-lost-due-to-error-or-a-malicious-insider-attack"></a>问：如何确保错误或恶意内部攻击不会导致专用 HSM 中的密钥丢失？

我们强烈建议使用本地 HSM 备份设备定期备份 HSM，以实现灾难恢复。 需要与连接到 HSM 备份设备的本地工作站建立对等连接或站点到站点 VPN 连接。

### <a name="q-how-do-i-get-support-for-dedicated-hsm"></a>问：如何获取专用 HSM 的支持？

Microsoft 和 Thales 都提供支持。  如果你遇到了硬件或网络访问问题，请与 Microsoft 提出支持请求，如果你遇到 HSM 配置、软件和应用程序开发问题，请使用 Thales 发出支持请求。 如果有一个不确定的问题，请向 Microsoft 提出支持请求，然后可以根据需要执行 Thales。 

### <a name="q-how-do-i-get-the-client-software-documentation-and-access-to-integration-guidance-for-the-thales-network-luna-hsm-7"></a>问：如何实现获取客户端软件、文档和访问 Thales 网络 Luna HSM 7 的集成指南？

注册服务后，将提供 Thales 客户 ID，允许在 Thales 客户支持门户中注册。 这样，便可以访问所有软件和文档，还可以直接使用 Thales 启用支持请求。

### <a name="q-if-there-is-a-security-vulnerability-found-and-a-patch-is-released-by-thales-who-is-responsible-for-upgradingpatching-osfirmware"></a>问：如果发现了安全漏洞，而 Thales 发布了一个修补程序，由谁负责升级/修补操作系统/固件？

Microsoft 无法连接到分配给客户的 HSM。 客户必须自行升级和修补其 HSM。

### <a name="q-what-if-i-need-to-reboot-my-hsm"></a>问：如果我需要重新启动 HSM，该怎么办？

HSM 具有命令行重启选项，但是，在重新启动时，我们遇到的问题是间歇性地停止响应的，出于此原因，建议你向 Microsoft 提出支持请求，以便在物理上重新启动设备。 

## <a name="cryptography-and-standards"></a>加密和标准

### <a name="q-is-it-safe-to-store-encryption-keys-for-my-most-important-data-in-dedicated-hsm"></a>问：是否可以在专用 HSM 中安全存储最重要数据的加密密钥？

是的，专用 HSM 预配 Thales 网络 Luna HSM 7 设备，该设备使用 FIPS 140-2 第3级验证后的 Hsm。 

### <a name="q-what-cryptographic-keys-and-algorithms-are-supported-by-dedicated-hsm"></a>问：专用 HSM 支持哪些加密密钥和算法？

专用 HSM 服务预配 Thales 网络 Luna HSM 7 设备。 这些设备支持多种加密密钥类型和算法，包括完全支持 Suite B

* 非对称：
  * RSA
  * DSA
  * Diffie-Hellman
  * 椭圆曲线
  * 采用命名曲线、用户定义的曲线和 Brainpool 曲线的加密法（ECDSA、ECDH、Ed25519、ECIES）；KCDSA
* 对称：
  * AES-GCM
  * 三重 DES
  * DES
  * ARIA、SEED
  * RC2
  * RC4
  * RC5
  * CAST
  * 哈希/消息摘要/HMAC：SHA-1、SHA-2、SM3
  * 密钥派生： SP 800-108 计数器模式
  * 密钥包装： SP 800-38F
  * 随机数生成：FIPS 140-2 批准的 DRBG（SP 800-90 CTR 模式），符合 BSI DRG.4

### <a name="q-is-dedicated-hsm-fips-140-2-level-3-validated"></a>问：专用 HSM 是否已通过 FIPS 140-2 级别 3 验证？

是。 专用 HSM 服务预配 Thales 网络 Luna HSM 7 设备，使用 FIPS 140-2 第3级验证的 Hsm。

### <a name="q-what-do-i-need-to-do-to-make-sure-i-operate-dedicated-hsm-in-fips-140-2-level-3-validated-mode"></a>问：如何确保在 FIPS 140-2 级别 3 验证模式下运行专用 HSM？

专用 HSM 服务预配 Thales 网络 Luna HSM 7 设备。 这些设备使用 FIPS 140-2 级别 3 验证的 HSM。 默认部署的配置、操作系统和固件也已通过 FIPS 验证。 无需采取任何措施即可符合 FIPS 140-2 级别 3 的要求。

### <a name="q-how-does-a-customer-ensure-that-when-an-hsm-is-deprovisioned-all-the-key-material-is-wiped-out"></a>问：客户如何确保在 HSM 取消预配时，是否清除了所有密钥材料？

请求取消预配之前，客户必须使用 Thales 提供的 hsm 客户端工具 zeroized HSM。

## <a name="performance-and-scale"></a>性能和规模

### <a name="q-how-many-cryptographic-operations-are-supported-per-second-with-dedicated-hsm"></a>问：专用 HSM 支持每秒多少次加密操作？

专用 HSM 预配 Thales 网络 Luna HSM 7 Hsm。 下面是某些操作的最大性能摘要： 

* RSA-2048：每秒 10,000 个事务
* ECC P256：每秒 20,000 个事务
* AES-GCM：每秒 17,000 个事务

### <a name="q-how-many-partitions-can-be-created-in-dedicated-hsm"></a>问：在专用 HSM 中可以创建多少个分区？

使用的 SafeNet Luna HSM 7 型号 A790 包含10个分区的许可证，以服务为代价。 设备的分区限制为100个，添加分区达到此限制将产生额外的许可成本，并要求在设备上安装新的许可证文件。

### <a name="q-how-many-keys-can-be-supported-in-dedicated-hsm"></a>问：专用 HSM 支持多少个密钥？

最大键数是可用内存的函数。 使用的 SafeNet Luna 7 型号 A790 有 32 MB 内存。 如果使用非对称密钥，则以下数字也适用于密钥对。

* RSA-2048 - 19,000
* ECC-P256 - 91,000

容量根据密钥生成模板中设置的特定密钥属性和分区数而异。
