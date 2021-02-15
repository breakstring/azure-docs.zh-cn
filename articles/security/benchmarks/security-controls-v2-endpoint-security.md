---
title: Azure 安全基准 V2 - 终结点安全性
description: Azure 安全基准 V2 终结点安全性
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 937d0b379c4f669e5b57e5053d5e3bffeb091e78
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368947"
---
# <a name="security-control-v2-endpoint-security"></a>安全控制 V2：终结点安全性

终结点安全性涵盖终结点检测和响应中的多个控制。 这包括在 Azure 环境中对终结点使用终结点检测和响应 (EDR) 和反恶意软件服务。

## <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1：使用终结点检测和响应 (EDR)

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID (s)  |
|--|--|--|--|
| ES-1 | 8.1 | SI-2、SI-3、SC-3 |

对服务器和客户端启用终结点检测和响应 (EDR) 功能，并与 SIEM 和安全操作进程集成。

Microsoft Defender 高级威胁防护在企业终结点安全平台中提供了 EDR 功能，以防止、检测、调查和响应高级威胁。 

- [Microsoft Defender 高级威胁防护概述](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [适用于 Windows Server 的 Microsoft Defender ATP 服务](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [适用于非 Windows Server 的 Microsoft Defender ATP 服务](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [基础结构和终结点安全性](/azure/cloud-adoption-framework/organize/cloud-security)

- [威胁情报](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [安全合规性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [状况管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2：使用集中管理的新式反恶意软件

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID (s)  |
|--|--|--|--|
| ES-2 | 8.1 | SI-2、SI-3、SC-3 |

使用集中管理的终结点反恶意软件解决方案，可实时和定期扫描

Azure 安全中心可以自动识别针对虚拟机的多种常用反恶意软件解决方案的使用情况，并报告终结点保护运行状态和提出建议。 

适用于 Azure 云服务的 Microsoft Antimalware 是适用于 Windows 虚拟机 (VM) 的默认反恶意软件。 对于 Linux VM，请使用第三方反恶意软件解决方案。  还可使用 Azure 安全中心的数据服务威胁检测来检测上传到 Azure 存储帐户的恶意软件。 

- [如何为云服务和虚拟机配置 Microsoft Antimalware](../fundamentals/antimalware.md)

- [支持的终结点保护解决方案](../../security-center/security-center-services.md?tabs=features-windows#supported-endpoint-protection-solutions-)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [基础结构和终结点安全性](/azure/cloud-adoption-framework/organize/cloud-security)

- [威胁情报](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [安全合规性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [状况管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="es-3-ensure-anti-malware-software-and-signatures-are-updated"></a>ES-3：确保反恶意软件和签名已更新

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID (s)  |
|--|--|--|--|
| ES-3 | 8.2 | SI-2、SI-3 |

确保反恶意软件签名快速且一致地进行更新。 

请遵循 Azure 安全中心中的建议：“计算和应用”用于确保所有终结点都具有最新的签名。 默认情况下，Microsoft Antimalware 将自动安装最新的签名和引擎更新。 对于 Linux，请使用第三方反恶意软件解决方案。

- [如何为 Azure 云服务和虚拟机部署 Microsoft Antimalware](../fundamentals/antimalware.md)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [基础结构和终结点安全性](/azure/cloud-adoption-framework/organize/cloud-security)

- [威胁情报](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [安全合规性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [状况管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Azure 安全中心中的 Endpoint Protection 评估和建议](../../security-center/security-center-endpoint-protection.md)