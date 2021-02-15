---
title: 用于 Azure VM 的安全功能
titleSuffix: Azure security
description: 本文概述了可用于 Azure 虚拟机的核心 Azure 安全功能。
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: 467b2c83-0352-4e9d-9788-c77fb400fe54
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/2/2019
ms.author: terrylan
ms.openlocfilehash: ed2cb967b24ce5abdebadfe29847ae1a16b4e745
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498738"
---
# <a name="azure-virtual-machines-security-overview"></a>Azure 虚拟机安全概述
本文概述了可用于虚拟机的核心 Azure 安全功能。

可使用 Azure 虚拟机灵活地部署各种计算解决方案。 该服务支持 Microsoft Windows、Linux、Microsoft SQL Server、Oracle、IBM、SAP 和 Azure BizTalk Services。 因此，几乎可在任何操作系统上部署任何工作负载和任何语言。

Azure 虚拟机让你能够灵活地进行虚拟化，而无需购买和维护运行虚拟机的物理硬件。 可生成并部署应用程序，并保证数据在高度安全的数据中心受到保护且是安全的。

使用 Azure 可以构建安全增强且符合法规的解决方案：

* 保护虚拟机不受病毒和恶意软件的侵害。
* 加密敏感数据。
* 保护网络流量的安全。
* 识别和检测威胁。
* 满足符合性要求。  

## <a name="antimalware"></a>反恶意软件

通过 Azure，可使用安全供应商（例如 Microsoft、Symantec、Trend Micro 和 Kaspersky）提供的反恶意软件。 此软件可帮助保护虚拟机免受恶意文件、广告程序和其他威胁的侵害。

适用于 Azure 云服务和虚拟机的 Microsoft 反恶意软件是一种实时保护功能，可帮助识别并移除病毒、间谍软件和其他恶意软件。  适用于 Azure 的 Microsoft 反恶意软件提供可配置警报，能在已知恶意软件或不需要的软件试图自行安装或在 Azure 系统上运行时进行警报通知。

适用于 Azure 的 Microsoft 反恶意软件是针对应用程序和租户环境的单一代理解决方案。 它旨在后台运行，且无需人工干预。 可以根据应用程序工作负荷的需求，选择默认的基本安全性或高级的自定义配置（包括反恶意软件监视）来部署保护。

详细了解 [Microsoft Antimalware for Azure](antimalware.md) 和可用的核心功能。

了解有关反恶意软件的详细信息以保护虚拟机：

* [在 Azure 虚拟机上部署反恶意软件解决方案](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [如何在 Windows VM 上安装和配置服务型 Trend Micro Deep Security](../../virtual-machines/extensions/trend.md)
* [如何在 Windows VM 上安装和配置 Symantec Endpoint Protection](../../virtual-machines/extensions/symantec.md)
* [Azure 市场中的安全解决方案](https://azure.microsoft.com/marketplace/?term=security)

若要实现更强大的保护，请考虑使用 [Windows Defender 高级威胁防护](/windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection)。 使用 Windows Defender ATP，可以实现：

* [攻击面减小](/windows/security/threat-protection/windows-defender-atp/overview-attack-surface-reduction)  
* [下一代保护](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)  
* [终结点保护和响应](/windows/security/threat-protection/windows-defender-atp/overview-endpoint-detection-response)
* [自动调查和补救](/windows/security/threat-protection/windows-defender-atp/automated-investigations-windows-defender-advanced-threat-protection)
* [安全评分](/windows/security/threat-protection/microsoft-defender-atp/tvm-microsoft-secure-score-devices)
* [高级追寻](/windows/security/threat-protection/windows-defender-atp/overview-hunting-windows-defender-advanced-threat-protection)
* [管理和 API](/windows/security/threat-protection/windows-defender-atp/management-apis)
* [Microsoft 威胁防护](/windows/security/threat-protection/windows-defender-atp/threat-protection-integration)

了解详细信息：

* [WDATP 入门](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)  
* [WDATP 功能概述](/windows/security/threat-protection/windows-defender-atp/overview)  

## <a name="hardware-security-module"></a>硬件安全模块

提高密钥安全性可增强加密和身份验证保护。 通过将关键密码和密钥存储在 Azure 密钥保管库中，可以简化此类密码和密钥的管理和保护。

密钥保管库提供将你的密钥存储在已通过 FIPS 140-2 Level 2 标准认证的硬件安全性模块 (HSM) 中的选项。 用于备份或 [透明数据加密](/sql/relational-databases/security/encryption/transparent-data-encryption) 的 SQL Server 加密密钥可以存储在密钥保管库中，此外还可存储应用程序中的任意密钥或机密。 对这些受保护项的权限和访问权限通过 [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)进行管理。

了解详细信息：

* [什么是 Azure 密钥保管库？](../../key-vault/general/overview.md)
* [Azure 密钥保管库博客](/archive/blogs/kv/)

## <a name="virtual-machine-disk-encryption"></a>虚拟机磁盘加密

Azure 磁盘加密是用于加密 Windows 和 Linux 虚拟机磁盘的新功能。 Azure 磁盘加密利用 Windows 的行业标准 [BitLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) 功能和 Linux 的 [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) 功能，为 OS 和数据磁盘提供卷加密。

该解决方案与 Azure Key Vault 集成，帮助用户控制和管理 Key Vault 订阅中的磁盘加密密钥和机密。 它可确保虚拟机磁盘上的所有数据在 Azure 存储中静态加密。

了解详细信息：

* [适用于 IaaS VM 的 Azure 磁盘加密](./azure-disk-encryption-vms-vmss.md)
* [快速入门：Encrypt a Windows IaaS VM with Azure PowerShell](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)（快速入门：使用 Azure PowerShell 加密 Windows IaaS VM）

## <a name="virtual-machine-backup"></a>虚拟机备份

Azure 备份是一种可缩放的解决方案，无需资本投资便可帮助保护应用程序数据，从而最大限度降低运营成本。 应用程序错误可能会损坏数据，人为错误可能会将 bug 引入应用程序。 使用 Azure 备份可以保护运行 Windows 和 Linux 的虚拟机。

了解详细信息：

* [什么是 Azure 备份？](../../backup/backup-overview.md)
* [Azure 备份服务 - 常见问题解答](../../backup/backup-azure-backup-faq.md)

## <a name="azure-site-recovery"></a>Azure Site Recovery

组织的 BCDR 策略的其中一个重要部分是，找出在发生计划的和非计划的中断时让企业工作负荷和应用保持运行的方法。 Azure Site Recovery 可帮助协调工作负荷和应用的复制、故障转移及恢复，因此能够在主要位置发生故障时通过辅助位置来提供工作负荷和应用。

Site Recovery：

* **简化 BCDR 策略**：通过 Site Recovery 可从一个位置轻松处理多个业务工作负荷和应用的复制、故障转移及恢复。 Site Recovery 会协调复制和故障转移，但不会拦截应用程序数据或拥有任何相关信息。
* **提供灵活的复制**：借助 Site Recovery，可以复制 Hyper-V 虚拟机、VMware 虚拟机和 Windows/Linux 物理服务器上运行的工作负荷。
* **支持故障转移和恢复**：Site Recovery 提供测试故障转移，既能支持灾难恢复练习，又不会影响生产环境。 还可针对预期会出现的中断运行计划内故障转移，确保不丢失任何数据；或者针对意外灾难运行计划外故障转移，尽量减少数据丢失（具体取决于复制频率）。 故障转移之后，可故障回复到主站点。 Site Recovery 提供包含脚本和 Azure 自动化工作簿的恢复计划，以供你自定义多层应用程序的故障转移和恢复。
* **消除辅助数据中心**：可复制到辅助本地站点，或复制到 Azure。 使用 Azure 作为灾难恢复的目标可以消除维护辅助站点所带来的成本和复杂性。 复制的数据存储在 Azure 存储中。
* **与现有 BCDR 技术集成**：Site Recovery 能够与其他应用程序的 BCDR 功能结合使用。 例如，可使用 Site Recovery 来帮助保护公司工作负荷的 SQL Server 后端。 这包括对 SQL Server AlwaysOn 的本机支持以管理可用性组的故障转移。

了解详细信息：

* [什么是 Azure Site Recovery？](../../site-recovery/site-recovery-overview.md)
* [Azure Site Recovery 的工作原理是什么？](../../site-recovery/azure-to-azure-architecture.md)
* [哪些工作负荷受 Azure Site Recovery 保护？](../../site-recovery/site-recovery-workload.md)

## <a name="virtual-networking"></a>虚拟网络

虚拟机需要网络连接。 若要支持该需求，Azure 要求将虚拟机连接到 Azure 虚拟网络。

Azure 虚拟网络是一个构建于物理 Azure 网络结构之上的逻辑构造。 每个逻辑 Azure 虚拟网络都独立于所有其他 Azure 虚拟网络。 这种隔离有助于确保其他 Microsoft Azure 客户无法访问部署中的网络流量。

了解详细信息：

* [Azure 网络安全概述](network-overview.md)
* [虚拟网络概述](../../virtual-network/virtual-networks-overview.md)
* [Networking features and partnerships for Enterprise scenarios](https://azure.microsoft.com/blog/networking-enterprise/)（网络功能和用于企业方案的合作关系）

## <a name="security-policy-management-and-reporting"></a>安全策略管理和报告

Azure 安全中心可帮助防范、检测和应对威胁。 通过安全中心可提高对 Azure 资源安全性的可见性和控制力度。 它为 Azure 订阅提供集成的安全监控和策略管理。 它有助于检测可能会被忽视的威胁，适用于各种安全解决方案生态系统。

安全中心通过以下方式帮助优化和监视虚拟机的安全：

* 为虚拟机提供[安全建议](../../security-center/security-center-recommendations.md)。 示例建议包括：应用系统更新、配置 ACL 终结点、启用反恶意软件、启用网络安全组和应用磁盘加密。
* 监视虚拟机的状态。

了解详细信息：

* [Azure 安全中心简介](../../security-center/security-center-introduction.md)
* [Azure 安全中心常见问题解答](../../security-center/faq-general.md)
* [Azure 安全中心规划和操作](../../security-center/security-center-planning-and-operations-guide.md)

## <a name="compliance"></a>合规性

Azure 虚拟机已针对 FISMA、FedRAMP、HIPAA、PCI DSS Level 1 和其他关键合规性计划进行了认证。 此认证使自己的 Azure 应用程序更容易满足合规性要求，并使企业更容易应对各种国内和国际法规要求。

了解详细信息：

* [Microsoft Trust Center:Compliance](https://www.microsoft.com/en-us/trustcenter/compliance)（Microsoft 信任中心：合规性）
* [Trusted Cloud:Microsoft Azure Security, Privacy, and Compliance](https://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)（可信云：Microsoft Azure 安全性、隐私和合规性）

## <a name="confidential-computing"></a>机密计算

虽然机密计算在技术方面不是虚拟机安全性的一部分，但是虚拟机安全性的主题属于“计算”安全性的更高级别的主题。 机密计算属于“计算”安全性类别。

当数据“采用明文”（这是进行高效处理所必需的）时，机密计算可确保数据在可信执行环境 https://en.wikipedia.org/wiki/Trusted_execution_environment （TEE - 也称为飞地）中受到保护，下图显示了一个这样的示例。  

TEE 可以确保无法从外部查看数据或执行操作，即使通过调试程序也不可以。 它们甚至可以确保只有经过授权的代码才能访问数据。 如果代码被更改或篡改，则会拒绝操作并禁用环境。 TEE 会在代码在它中执行的整个过程中实施这些保护。

了解详细信息：

* [Azure 机密计算介绍](https://azure.microsoft.com/blog/introducing-azure-confidential-computing/)  
* [Azure 机密计算](https://azure.microsoft.com/blog/azure-confidential-computing/)  

## <a name="next-steps"></a>后续步骤

了解 VM 和操作系统的[安全最佳做法](iaas.md)。