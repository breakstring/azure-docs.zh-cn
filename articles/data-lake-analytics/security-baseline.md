---
title: 适用于 Data Lake Analytics 的 Azure 安全基线
description: Data Lake Analytics 安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 56a04a7f689846fc8688d9a3144882fff3a809ad
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201308"
---
# <a name="azure-security-baseline-for-data-lake-analytics"></a>适用于 Data Lake Analytics 的 Azure 安全基线

适用于 Data Lake Analytics 的 Azure 安全基线包含有助于改进部署安全状况的建议。

此服务的基线摘自 [Azure 安全基准版本 1.0](../security/benchmarks/overview.md)，其中提供了有关如何根据我们的最佳做法指导保护 Azure 上的云解决方案的建议。

有关详细信息，请参阅 [Azure 安全基线概述](../security/benchmarks/security-baselines-overview.md)。

## <a name="network-security"></a>网络安全性

有关详细信息，请参阅[安全控制：网络安全](../security/benchmarks/security-control-network-security.md)。

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保护虚拟网络中的 Azure 资源

**指南**：使用 Data Lake Analytics 的防火墙设置来限制外部 IP 范围，以允许来自本地客户端和第三方服务的访问。 可以通过门户、REST Api 或 PowerShell 配置防火墙设置。

* [防火墙规则](/rest/api/datalakeanalytics/firewallrules)

* [使用 Azure PowerShell 管理 Azure Data Lake Analytics](./data-lake-analytics-manage-use-powershell.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2：监视并记录虚拟网络、子网和网络接口的配置与流量

**指南**：不适用;Azure Data Lake Analytics 不在虚拟网络中运行，并且在使用联合查询时，传出呼叫无法配置为通过客户虚拟网络进行路由。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="13-protect-critical-web-applications"></a>1.3：保护关键 Web 应用程序

**指南**：不适用;此控件适用于在 Azure App Service 或 IaaS 实例上运行的 web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知恶意的 IP 地址进行通信

**指南**：使用 Data Lake Analytics 的防火墙设置来限制外部 IP 范围，以允许来自本地客户端和第三方服务的访问。 可以通过门户、REST Api 或 PowerShell 配置防火墙设置。

* [防火墙规则](/rest/api/datalakeanalytics/firewallrules)

* [使用 Azure PowerShell 管理 Azure Data Lake Analytics](./data-lake-analytics-manage-use-powershell.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="15-record-network-packets"></a>1.5：记录网络数据包

**指南**：不适用;Data Lake Analytics 不会在客户虚拟网络中运行，并且不能使用网络安全组 (Nsg) 来记录网络流日志。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统 (IDS/IPS)

**指南**：不适用;Data Lake Analytics 是一个 PaaS 产品，它不会部署到客户网络。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理发往 Web 应用程序的流量

**指南**：不适用;此建议适用于在 Azure App Service 或计算资源上运行的 web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指南**：不适用;Data Lake Analytics 不会在客户虚拟网络中运行，并且不能使用网络安全组 (Nsg) 。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指南**：不适用;Data Lake Analytics 不会在客户虚拟网络中运行，并且不能使用网络安全组 (Nsg) 。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指南**：不适用;Data Lake Analytics 不会在客户虚拟网络中运行，并且不能使用网络安全组 (Nsg) 。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指南**：不适用;Data Lake Analytics 不会在客户虚拟网络中运行，并且不能使用网络安全组 (Nsg) 。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="logging-and-monitoring"></a>日志记录和监视

有关详细信息，请参阅[安全控制：日志记录和监视](../security/benchmarks/security-control-logging-monitoring.md)。

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用批准的时间同步源

**指南**：不适用;Microsoft 为 Data Lake Analytics 维护时间源。

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指南**：通过 Azure Monitor 引入日志来聚合安全数据，如 Data Lake Analytics "audit" 和 "请求" 诊断。 在 Azure Monitor 中，使用 Log Analytics 工作区来查询和执行分析，并将 Azure 存储帐户用于长期/存档存储，还可以选择使用诸如不可变存储和强制保留保留的安全功能。

或者，可以启用数据并将其加入 Azure Sentinel 或第三方 SIEM。

* [访问 Azure Data Lake Analytics 的诊断日志](./data-lake-analytics-diagnostic-logs.md)

* [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

* [如何使用 Azure Monitor 收集平台日志和指标](../azure-monitor/platform/diagnostic-settings.md)

* [如何使用 Azure Monitor 收集 Azure 虚拟机内部主机日志](../azure-monitor/learn/quick-collect-azurevm.md)

* [如何开始使用 Azure Monitor 和第三方 SIEM 集成](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指南**：启用 Data Lake Analytics 的诊断设置以访问审核和请求日志。 其中包括事件源、日期、用户、时间戳和其他有用元素等数据。

* [如何使用 Azure Monitor 收集平台日志和指标](../azure-monitor/platform/diagnostic-settings.md)

* [了解 Azure 中的日志记录和不同的日志类型](../azure-monitor/platform/platform-logs-overview.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：从操作系统收集安全日志

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指南**：在 Azure Monitor 中，根据组织的合规性规则设置 Log Analytics 工作区保持期。 将 Azure 存储帐户用于长期存储和存档存储。

* [更改 Log Analytics 中的数据保留期](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

* [如何为 Azure 存储帐户日志配置保留策略](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="26-monitor-and-review-logs"></a>2.6：监视和查看日志

**指南**：分析和监视日志中的异常行为，并定期查看 Data Lake Analytics 资源的结果。 使用 Azure Monitor 的 Log Analytics 工作区查看日志并对日志数据执行查询。 或者，可以启用数据并将其加入 Azure Sentinel 或第三方 SIEM。

* [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

* [有关 Log Analytics 工作区的详细信息](../azure-monitor/log-query/log-analytics-tutorial.md)

* [如何在 Azure Monitor 中执行自定义查询](../azure-monitor/log-query/get-started-queries.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

**指南**：启用 Data Lake Analytics 的诊断设置，并将日志发送到 Log Analytics 工作区。 将 Log Analytics 工作区加入 Azure Sentinel，因为它提供了安全业务流程自动化响应 (SOAR) 解决方案。 这样便可以创建 playbook（自动化解决方案）并将其用于修正安全问题。

* [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

* [如何针对 Log Analytics 日志数据发出警报](../azure-monitor/learn/tutorial-response.md)

* [访问 Azure Data Lake Analytics 的诊断日志](./data-lake-analytics-diagnostic-logs.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中管理反恶意软件日志记录

**指南**：不适用;Data Lake Analytics 不会处理或产生与反恶意软件相关的日志。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录

**指导**：根据组织的需求，从 Azure 市场实现 DNS 日志记录解决方案的第三方解决方案。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="210-enable-command-line-audit-logging"></a>2.10：启用命令行审核日志记录

**指南**：不适用;此控件适用于客户有权访问基础操作系统的计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="identity-and-access-control"></a>标识和访问控制

有关详细信息，请参阅[安全控制：标识和访问控制](../security/benchmarks/security-control-identity-access-control.md)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指南**：Azure AD 具有必须显式分配且可查询的内置角色。 使用 Azure AD PowerShell 模块执行即席查询，以发现属于管理组成员的帐户。

* [如何使用 PowerShell 获取 Azure AD 中的目录角色](/powershell/module/azuread/get-azureaddirectoryrole)

* [如何使用 PowerShell 获取 Azure AD 中目录角色的成员](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码

**指南**： Data Lake Analytics 没有默认密码的概念，因为使用 Azure Active Directory 提供身份验证，并通过 azure RBAC)  (azure 基于角色的访问控制对其进行保护。

* [Azure Data Lake Analytics 概述](./data-lake-analytics-overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指南**：围绕专用管理帐户的使用创建标准操作程序。

还可以通过使用 Azure AD Privileged Identity Management 和 Azure 资源管理器来启用即时访问权限。

* [详细了解 Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4：使用 Azure Active Directory 单一登录 (SSO)

**指导**：请尽可能使用 Azure Active Directory SSO，而不是为每个服务配置单个独立凭据。 请使用 Azure 安全中心标识和访问建议。

* [了解 Azure AD 的 SSO](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指南**：启用 Azure Active Directory 多重身份验证 (MFA) ，并遵循 Azure 安全中心的标识和访问管理建议来帮助保护你的 Data Lake Analytics 资源。

* [如何在 Azure 中启用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

* [如何在 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6：使用由 Azure 管理的安全工作站执行管理任务

**指导**：对于需要提升的权限的管理任务，请使用安全的 Azure 托管工作站（也称为特权访问工作站，简称 PAW）。

* [了解安全的 Azure 托管工作站](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [如何启用 Azure AD MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

**指导**：使用 Azure Active Directory 安全报告在环境中发生可疑活动或不安全的活动时生成日志和警报。 使用 Azure 安全中心监视标识和访问活动。

* [如何确定标记为存在风险活动的 Azure AD 用户](../active-directory/identity-protection/overview-identity-protection.md)

* [如何在 Azure 安全中心内监视用户的标识和访问活动](../security-center/security-center-identity-access.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指导**：使用 Azure AD 命名位置，仅允许从 IP 地址范围或国家/地区的特定逻辑分组进行访问。

* [如何配置 Azure AD 命名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指导**：使用 Azure Active Directory (Azure AD) 作为中心身份验证和授权系统。 Azure RBAC)  (基于角色的访问控制，可对客户端访问 Data Lake Analytics 资源提供精细的控制。

* [如何创建和配置 Azure AD 实例](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指南**：Azure AD 提供日志来帮助发现过时的帐户。 此外，请使用 Azure AD 标识和访问评审来有效管理组成员身份、对企业应用程序的访问以及角色分配。 可以定期评审用户的访问权限，确保只有适当的用户才持续拥有访问权限。

* [了解 Azure AD 报告](../active-directory/reports-monitoring/index.yml)

* [如何使用 Azure AD 标识和访问评审](../active-directory/governance/access-reviews-overview.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：监视尝试访问已停用凭据的行为

**指南**：对 Data Lake Analytics 和 Azure Active Directory 启用诊断设置，将所有日志发送到 Log Analytics 工作区。 在 Log Analytics 中配置所需警报（例如尝试访问禁用的机密）。

* [将 Azure AD 日志与 Azure Monitor 日志集成](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报

**指南**：使用 Azure Active Directory 的风险和标识保护功能来配置对检测到的与 Data Lake Analytics 资源相关的可疑操作的自动响应。 应通过 Azure Sentinel 启用自动响应，以实现组织的安全响应。

* [如何查看 Azure AD 风险登录](../active-directory/identity-protection/overview-identity-protection.md)

* [如何配置和启用标识保护风险策略](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持场合下为 Microsoft 提供对相关客户数据的访问权限

**指南**：不适用;Azure Data Lake Analytics 不支持客户密码箱。

* [公开上市中支持的服务和方案](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="data-protection"></a>数据保护

有关详细信息，请参阅[安全控制：数据保护](../security/benchmarks/security-control-data-protection.md)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指南**：使用标记帮助跟踪存储或处理敏感信息 Data Lake Analytics 资源。

* [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指南**：使用单独的订阅实现隔离，使用各个安全域（如环境）的管理组，数据敏感性。 可以限制 Data Lake Analytics，以控制对应用程序和企业环境所需的 Data Lake Analytics 资源的访问级别。 配置防火墙规则时，只有在指定网络集上请求数据的应用程序才能访问 Data Lake Analytics 资源。 可以通过 Azure RBAC 控制对 Azure Data Lake Analytics 的访问。

* [如何创建其他 Azure 订阅](../cost-management-billing/manage/create-subscription.md)

* [如何创建管理组](../governance/management-groups/create-management-group-portal.md)

* [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

* [管理 Azure 基于角色的访问控制](./data-lake-analytics-manage-use-portal.md#manage-azure-role-based-access-control)

* [防火墙规则](/rest/api/datalakeanalytics/firewallrules)

* [使用 Azure PowerShell 管理 Azure Data Lake Analytics](./data-lake-analytics-manage-use-powershell.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输

**指南**：尚无 Azure Data Lake Analytics 资源的数据丢失防护功能。 如果需要出于合规性目的使用这些功能，请实施第三方解决方案。

对于 Microsoft 管理的基础平台，Microsoft 会将所有客户内容视为敏感数据，并全方位防范客户数据丢失和泄露。 为了确保 Azure 中的客户数据保持安全，Microsoft 已实施并维护一套可靠的数据保护控制机制和功能。

* [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

* [如何保护 Azure 存储帐户](../storage/blobs/security-recommendations.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指南**：默认情况下，Microsoft Azure 资源将协商 TLS 1.2。 确保连接到 Data Lake Analytics 的任何客户端都可以使用 TLS 1.2 或更高版本进行协商。

* [操作列表示例](/rest/api/datalakeanalytics/operations/list)

**Azure 安全中心监视**：目前不可用

**责任**：共享

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指南**：对于 Azure Data Lake Analytics 资源，数据标识功能尚不可用。 如果需要出于合规性目的使用这些功能，请实施第三方解决方案。

* [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 控制对资源的访问

**指导**：使用 Azure 基于角色的访问控制 (Azure RBAC) 来控制用户与服务交互的方式。

* [管理 Azure RBAC](./data-lake-analytics-manage-use-portal.md#manage-azure-role-based-access-control)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用基于主机的数据丢失防护来强制实施访问控制

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指南**：数据存储在默认 Data Lake Storage Gen1 帐户中。 对于静态数据，Data Lake Storage Gen1 支持“默认启用”透明加密。

* [Azure Data Lake Storage Gen1 中的数据加密](../data-lake-store/data-lake-store-encryption.md)

**Azure 安全中心监视**：目前不可用

**责任**：共享

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指南**：使用 Azure Monitor 与 Azure 活动日志来创建 Azure Data Lake Analytics 资源的生产实例发生更改时发出的警报。

* [如何针对 Azure 活动日志事件创建警报](../azure-monitor/platform/alerts-activity-log.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="vulnerability-management"></a>漏洞管理

有关详细信息，请参阅[安全控制：漏洞管理](../security/benchmarks/security-control-vulnerability-management.md)。

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：运行自动漏洞扫描工具

**指南**：在保护 Azure Data Lake Analytics 资源时，请参阅 Azure 安全中心的建议。

Microsoft 对支持 Azure Data Lake Analytics 的基础系统执行漏洞管理。

* [了解 Azure 安全中心建议](../security-center/recommendations-reference.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自动操作系统修补管理解决方案

**指南**：不适用;此控件用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3：为第三方软件部署自动修补程序管理解决方案

**指南**：不适用;此控件用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比较连续进行的漏洞扫描

**指南**：不适用;此控件用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险评级过程来确定已发现漏洞的修正措施的优先级

**指南**：使用通用风险评分程序（例如通用漏洞评分系统）或第三方扫描工具提供的默认风险评级。

* [NIST 出版物 - 通用漏洞评分系统](https://www.nist.gov/publications/common-vulnerability-scoring-system)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="inventory-and-asset-management"></a>库存和资产管理

有关详细信息，请参阅[安全控制：清单和资产管理](../security/benchmarks/security-control-inventory-asset-management.md)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动化资产发现解决方案

**指导**：使用 Azure Resource Graph 来查询和发现订阅中的所有资源（例如计算、存储、网络、端口和协议等）。 确保租户中具有适当的（读取）权限，并枚举所有 Azure 订阅以及订阅中的资源。

尽管可以通过 Azure Resource Graph 浏览器发现经典 Azure 资源，但我们强烈建议你今后创建并使用 Azure 资源管理器资源。

* [如何使用 Azure Resource Graph 浏览器创建查询](../governance/resource-graph/first-query-portal.md)

* [如何查看 Azure 订阅](/powershell/module/az.accounts/get-azsubscription)

* [了解 Azure RBAC](../role-based-access-control/overview.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指导**：将标记应用到 Azure资源，以便有条理地将元数据组织成某种分类。

* [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指南**：使用标记、管理组和单独的订阅（如果适用）来组织和跟踪 Azure Data Lake Analytics 资源。 定期核对清单，确保及时地从订阅中删除未经授权的资源。

此外，使用 Azure Policy 对可使用以下内置策略定义在客户订阅中创建的资源类型施加限制：
- 不允许的资源类型
- 允许的资源类型

* [如何创建其他 Azure 订阅](../cost-management-billing/manage/create-subscription.md)

* [如何创建管理组](../governance/management-groups/create-management-group-portal.md)

* [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4：定义并维护已批准的 Azure 资源的清单

**指导**：根据组织需求，创建已获批 Azure 资源以及已获批用于计算资源的软件的清单。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指导**：在 Azure 策略中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：
- 不允许的资源类型
- 允许的资源类型

此外，请使用 Azure Resource Graph 来查询/发现订阅中的资源。

* [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [如何使用 Azure Graph 创建查询](../governance/resource-graph/first-query-portal.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：监视计算资源中未批准的软件应用程序

**指南**：不适用;此控件用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未批准的 Azure 资源和软件应用程序

**指南**：不适用;此控件用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="68-use-only-approved-applications"></a>6.8：仅使用已批准的应用程序

**指南**：不适用;此控件用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指南**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：
- 不允许的资源类型
- 允许的资源类型

* [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [如何使用 Azure Policy 拒绝特定的资源类型](../governance/policy/samples/index.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：维护已获批软件的清单

**指南**：不适用;此控件用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制用户与 Azure 资源管理器进行交互的能力

**指南**：配置 Azure 条件访问，使其通过为“Microsoft Azure 管理”应用配置“阻止访问”，来限制用户与 Azure 资源管理器进行交互的能力。

* [如何配置条件访问以阻止访问 ARM](../role-based-access-control/conditional-access-azure-management.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制用户在计算资源中执行脚本的功能

**指南**：不适用;此控件用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以物理或逻辑方式隔离高风险应用程序

**指导**：不适用；此建议适用于 Azure 应用服务或计算资源上运行的 Web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="secure-configuration"></a>安全配置

有关详细信息，请参阅[安全控制：安全配置](../security/benchmarks/security-control-secure-configuration.md)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指南**：使用 "DataLakeAnalytics" 命名空间中的 Azure 策略别名创建自定义策略，以便审核或强制实施 Azure Data Lake Analytics 的配置。 你还可以使用与 Azure Data Lake Analytics 相关的内置策略定义，例如：
- 应启用 Data Lake Analytics 中的诊断日志

* [如何查看可用的 Azure Policy 别名](/powershell/module/az.resources/get-azpolicyalias)

* [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的操作系统配置

**指南**：不适用;此控件用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指导**：使用 Azure 策略“[拒绝]”和“[不存在则部署]”对不同的 Azure 资源强制实施安全设置。

* [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [了解 Azure Policy 效果](../governance/policy/concepts/effects.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：维护安全的操作系统配置

**指南**：不适用;此控件用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指导**：使用 Azure Repos 安全地存储和管理代码，如自定义 Azure 策略、Azure 资源管理器模板、Desired State Configuration 脚本等。若要访问在 Azure DevOps 中管理的资源，可以向特定用户、内置安全组或 Azure Active Directory (Azure AD)（如果与 Azure DevOps 集成）中定义的组或 Active Directory（如果与 TFS 集成）授予或拒绝授予权限。

* [如何在 Azure DevOps 中存储代码](/azure/devops/repos/git/gitworkflow?view=azure-devops&preserve-view=true)

* [关于 Azure DevOps 中的权限和组](/azure/devops/organizations/security/about-permissions)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全存储自定义操作系统映像

**指南**：不适用;此控件用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署 Azure 资源的配置管理工具

**指南**：不适用;此控件用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8：部署操作系统的配置管理工具

**指南**：不适用;此控件用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：为 Azure 资源实施自动配置监视

**指南**：使用 "DataLakeAnalytics" 命名空间中的 Azure 策略别名创建自定义策略，以对系统配置进行警报、审核和强制执行。 使用 Azure 策略 [audit]、[拒绝] 和 [部署（如果不存在））自动强制实施 Azure Data Lake Analytics 资源的配置。

* [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：为操作系统实施自动配置监视

**指南**：不适用;此控件用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指南**：不适用;Data Lake Analytics 不公开客户需要管理的任何机密。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全自动管理标识

**指南**：不适用;Data Lake Analytics 不支持 Azure 托管标识。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指南**：实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。

* [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="malware-defense"></a>恶意软件防护

有关详细信息，请参阅[安全控制：恶意软件防护](../security/benchmarks/security-control-malware-defense.md)。

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反恶意软件

**指南**：不适用;此控件用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预先扫描要上传到非计算 Azure 资源的文件

**指南**：在支持 Azure 服务的底层主机上启用了 Microsoft 反恶意软件 (例如 Azure Data Lake Analytics) ，但它不会在客户内容上运行。

预先扫描要上传到 Azure 资源的任何内容，如应用服务、Data Lake Analytics、Blob 存储等。Microsoft 无法访问这些实例中的数据。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>步骤 8.3：确保反恶意软件和签名已更新

**指南**：不适用;此控件用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="data-recovery"></a>数据恢复

有关详细信息，请参阅[安全控制：数据恢复](../security/benchmarks/security-control-data-recovery.md)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

**指南**： Data Lake Analytics 作业日志和数据输出存储在底层 Data Lake Storage Gen1 服务中。 可以使用各种方法来复制数据，包括 ADLCopy、Azure PowerShell 或 Azure 数据工厂。 你还可以使用 Azure 自动化来定期自动备份数据。

* [使用存储资源管理器管理 Azure Data Lake Storage Gen1 资源](../data-lake-store/data-lake-store-in-storage-explorer.md)

* [将数据从 Azure 存储 Blob 复制到 Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md)

* [Azure 自动化概述](../automation/automation-intro.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥

**指南**： Data Lake Analytics 作业日志和数据输出存储在底层 Data Lake Storage Gen1 服务中。 可以使用各种方法来复制数据，包括 ADLCopy、Azure PowerShell 或 Azure 数据工厂。

* [使用存储资源管理器管理 Azure Data Lake Storage Gen1 资源](../data-lake-store/data-lake-store-in-storage-explorer.md)

* [将数据从 Azure 存储 Blob 复制到 Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指导**：定期对备份数据执行数据恢复，以测试数据的完整性。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指南**：默认情况下，存储在 Data Lake Storage Gen1 或 Azure 存储中的 Data Lake Analytics 备份支持加密，因此无法关闭。 你应将备份视为敏感数据，并应用相关的访问和数据保护控制作为此基线的一部分。

* [保护 Azure Data Lake Storage Gen1 中存储的数据](../data-lake-store/data-lake-store-secure-data.md)

* [授予访问 Azure 存储中的数据的权限](../storage/common/storage-auth.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="incident-response"></a>事件响应

有关详细信息，请参阅[安全控制：事件响应](../security/benchmarks/security-control-incident-response.md)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指南**：为组织制定事件响应指南。 确保在书面的事件响应计划中定义人员职责，以及事件处理/管理从检测到事件后审查的各个阶段。

* [关于建立自己的安全事件响应流程的指南](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft 安全响应中心事件分析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [客户还可以利用 NIST 的“计算机安全事件处理指南”来制定他们自己的事件响应计划](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指导**：安全中心为每条警报分配严重性，以帮助你优先处理应该最先调查的警报。 严重性取决于安全中心在发出警报时所依据的检测结果和分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。

此外，请用标记明确标记订阅（例如 生产、非生产）并创建命名系统来对 Azure 资源进行明确标识和分类，特别是处理敏感数据的资源。 你的责任是根据发生事件的 Azure 资源和环境的关键性确定修正警报的优先级。

* [Azure 安全中心中的安全警报](../security-center/security-center-alerts-overview.md)

* [使用标记整理 Azure 资源](../azure-resource-manager/management/tag-resources.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指导**：定期执行演练来测试系统的事件响应功能，以帮助保护 Azure 资源。 查明弱点和差距，并根据需要修改你的响应计划。

* [NIST 发布内容 - IT 计划和功能的测试、训练和演练计划指南](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指导**：如果 Microsoft 安全响应中心 (MSRC) 发现数据被某方非法访问或未经授权访问，Microsoft 会使用安全事件联系信息联系用户。 事后审查事件，确保问题得到解决。

* [如何设置 Azure 安全中心安全联系人](../security-center/security-center-provide-security-contact-details.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指南**：使用连续导出功能导出 Azure 安全中心警报和建议，以帮助确定 Azure 资源的风险。 使用连续导出可以手动导出或者持续导出警报和建议。 可以使用 Azure 安全中心数据连接器将警报流式传输到 Azure Sentinel。

* [如何配置连续导出](../security-center/continuous-export.md)

* [如何将警报流式传输到 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指导**：使用 Azure 安全中心内的工作流自动化功能，通过“逻辑应用”针对安全警报和建议自动触发响应，以保护 Azure 资源。

* [如何配置工作流自动化和逻辑应用](../security-center/workflow-automation.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

有关详细信息，请参阅[安全控制：渗透测试和红队演练](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：定期对 Azure 资源执行渗透测试，确保修正所有发现的关键安全问题

**指导**：请遵循 Microsoft 云渗透测试互动规则，确保你的渗透测试不违反 Microsoft 政策。 使用 Microsoft 红队演练策略和执行，以及针对 Microsoft 托管云基础结构、服务和应用程序执行现场渗透测试。

* [参与的渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="next-steps"></a>后续步骤

- 请参阅 [Azure 安全基准](../security/benchmarks/overview.md)
- 详细了解 [Azure 安全基线](../security/benchmarks/security-baselines-overview.md)