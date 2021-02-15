---
title: Service Fabric 的 Azure 安全基线
description: Service Fabric 安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: service-fabric
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 175c00440fe9723bbd389db39616f525c2b7f436
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2021
ms.locfileid: "99805472"
---
# <a name="azure-security-baseline-for-service-fabric"></a>Service Fabric 的 Azure 安全基线

Service Fabric 的 Azure 安全基线包含可帮助你改善部署安全状况的建议。

此服务的基线摘自 [Azure 安全基准版本 1.0](../security/benchmarks/overview.md)，其中提供了有关如何根据我们的最佳做法指导保护 Azure 上的云解决方案的建议。

有关详细信息，请参阅 [Azure 安全基线概述](../security/benchmarks/security-baselines-overview.md)。



## <a name="network-security"></a>网络安全性

有关详细信息，请参阅[安全控制：网络安全](../security/benchmarks/security-control-network-security.md)。

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保护虚拟网络中的 Azure 资源

**指导**：确保所有虚拟网络子网部署都应用了网络安全组，且具有针对应用程序受信任端口和源的网络访问控制。

* [使用模板部署 Azure 防火墙](../firewall/deploy-template.md)

* [使用 Azure 网络安全组 (NSG) 创建外围网络](../security/fundamentals/service-fabric-best-practices.md#use-network-isolation-and-security-with-azure-service-fabric)

* [如何将 Azure Service Fabric 群集与现有虚拟网络集成](./service-fabric-patterns-networking.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2：监视和记录虚拟网络、子网和 NIC 的配置与流量

**指导**：使用 Azure 安全中心，并修正有关虚拟网络、子网以及用于保护 Azure Service Fabric 群集的网络安全组的网络保护建议。 启用网络安全组 (NSG) 流日志，并将日志发送到 Azure 存储帐户以进行流量审核。 还可以将 NSG 流日志发送到 Azure Log Analytics 工作区，并使用 Azure 流量分析来洞察 Azure 云中的通信流。 Azure 流量分析的优势包括能够可视化网络活动、识别热点、识别安全威胁、了解流量流模式，以及查明网络不当配置。

* [如何启用 NSG 流日志](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [如何启用和使用 Azure 流量分析](../network-watcher/traffic-analytics.md)

* [了解 Azure 安全中心提供的网络安全](../security-center/security-center-network-recommendations.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="13-protect-critical-web-applications"></a>1.3：保护关键 Web 应用程序

**指导**：提供前端网关，为用户、设备或其他应用程序提供单一入口点。 Azure API Management 直接与 Service Fabric 集成，可保护对后端服务的访问、防止 DOS 攻击，还可以验证 API 密钥、JWT 令牌、证书和其他凭据。

请考虑在关键 Web 应用程序前部署 Azure Web 应用程序防火墙 (WAF)，以对传入的流量进行额外的检查。 启用 WAF 的诊断设置，并将日志引入存储帐户、事件中心或 Log Analytics 工作区。

* [有关 Service Fabric 与 Azure API 管理的概述](./service-fabric-api-management-overview.md)

* [在包含应用程序网关的内部 VNET 中集成 API 管理](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)

* [如何部署 Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知恶意的 IP 地址进行通信

**指导**：为了防范 DDoS 攻击，请在部署 Azure Service Fabric 群集的虚拟网络中启用 Azure DDoS 标准防护。 使用 Azure 安全中心集成的威胁情报来拒绝与已知恶意的或未使用过的 Internet IP 地址进行通信。

* [如何配置 DDoS 防护](../ddos-protection/manage-ddos-protection.md)

* [了解 Azure 安全中心集成的威胁情报](../security-center/azure-defender.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="15-record-network-packets"></a>1.5：记录网络数据包

**指导**：针对附加到子网的、用于保护 Azure Service Fabric 群集的网络安全组 (NSG) 启用 NSG 流日志。 将 NSG 流日志记录到 Azure 存储帐户中，以生成流记录。 如果需要调查异常活动，请启用 Azure 网络观察程序数据包捕获。

* [如何启用 NSG 流日志](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [如何启用网络观察程序](../network-watcher/network-watcher-create.md)

* [使用流量分析可视化 NSG 流日志](../network-watcher/traffic-analytics.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统 (IDS/IPS)

**指导**：从 Azure 市场中选择一种产品/服务，该产品/服务应支持包含有效负载检查功能的 ID/IPS 功能。 如果不需要基于有效负载检查的入侵检测和/或防护，则可以使用包含威胁情报功能的 Azure 防火墙。 基于 Azure 防火墙威胁情报的筛选功能可以发出警报，并拒绝传入和传出已知恶意 IP 地址和域的流量。 IP 地址和域源自 Microsoft 威胁智能源。

在组织的每个网络边界上部署所选的防火墙解决方案，以检测和/或拒绝恶意流量。

* [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

* [如何部署 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)

* [如何配置 Azure 防火墙警报](../firewall/threat-intel.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理发往 Web 应用程序的流量

**指导**：为 Web 应用程序部署 Azure 应用程序网关，并为受信任的证书启用 HTTPS/SSL。

* [如何部署应用程序网关](../application-gateway/quick-create-portal.md)

* [如何将应用程序网关配置为使用 HTTPS](../application-gateway/create-ssl-portal.md)

* [了解 Azure Web 应用程序网关的第七层负载均衡](../application-gateway/overview.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指导**：使用虚拟网络服务标记，在附加到部署 Azure Service Fabric 群集的子网的网络安全组 (NSG) 中定义网络访问控制。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 在规则的相应源或目标字段中指定服务标记名称（例如 ApiManagement），可以允许或拒绝相应服务的流量。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

* [虚拟网络服务标记](../virtual-network/service-tags-overview.md)

* [Service Fabric 网络最佳做法](./service-fabric-best-practices-networking.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指导**：定义并实施与 Azure Service Fabric 群集相关的网络资源的标准安全配置。 在“Microsoft.ServiceFabric”和“Microsoft.Network”命名空间中使用 Azure Policy 别名创建自定义策略，以审核或强制实施 Azure Service Fabric 群集的网络配置。

你还可以使用 Azure 蓝图通过在单个蓝图定义中打包关键环境项目（例如 Azure 资源管理器模板、Azure RBAC 控件和策略）来简化大规模的 Azure 部署。 轻松将蓝图应用到新的订阅和环境，并通过版本控制来微调控制措施和管理。

* [如何查看可用的 Azure Policy 别名](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [如何创建 Azure 蓝图](../governance/blueprints/create-blueprint-portal.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指导**：对与 Azure Service Fabric 群集关联的网络安全性和流量流相关的网络安全组 (NSG) 及其他资源使用标记。 对于单个 NSG 规则，请使用“说明”字段针对允许流量传入/传出网络的任何规则指定业务需求和/或持续时间等。

使用标记相关的任何内置 Azure Policy 定义（例如“需要标记及其值”）来确保使用标记创建所有资源，并在有现有资源不带标记时发出通知。

可以使用 Azure PowerShell 或 Azure 命令行接口 (CLI) 根据资源的标记查找资源或对其执行操作。

* [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

* [如何创建虚拟网络](../virtual-network/quick-create-portal.md)

* [如何创建采用安全配置的 NSG](../virtual-network/tutorial-filter-network-traffic.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指导**：使用 Azure 活动日志监视网络资源配置，并检测与 Azure Service Fabric 部署相关的网络资源的更改。 在 Azure Monitor 中创建当关键网络资源发生更改时触发的警报。

* [如何查看和检索 Azure 活动日志事件](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [如何在 Azure Monitor 中创建警报](../azure-monitor/platform/alerts-activity-log.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="logging-and-monitoring"></a>日志记录和监视

有关详细信息，请参阅[安全控制：日志记录和监视](../security/benchmarks/security-control-logging-monitoring.md)。

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用批准的时间同步源

**指南**： Microsoft 维护 Azure Service Fabric 群集组件的时间源，你可以为计算部署更新时间同步。

* [如何为 Azure 计算资源配置时间同步](../virtual-machines/windows/time-sync.md)

**Azure 安全中心监视**：目前不可用

**责任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指导**：可将 Azure Service Fabric 群集加入 Azure Monitor，以聚合群集生成的安全数据。 查看 Service Fabric 的示例诊断问题和解决方案。

* [配置 Azure Monitor 日志与 Service Fabric 的集成](./service-fabric-diagnostics-oms-setup.md)

* [在 Azure 中设置监视容器 Azure Monitor 日志 Service Fabric](./service-fabric-tutorial-monitoring-wincontainers.md)

* [诊断常见的 Service Fabric 方案](./service-fabric-diagnostics-common-scenarios.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指导**：为 Service Fabric 群集启用 Azure Monitor，并将日志定向到 Log Analytics 工作区。 这会记录所有 Azure Service Fabric 群集节点的相关群集信息和 OS 指标。

* [配置 Azure Monitor 日志与 Service Fabric 的集成](./service-fabric-diagnostics-oms-setup.md)

* [在 Azure 中设置监视容器 Azure Monitor 日志 Service Fabric](./service-fabric-tutorial-monitoring-wincontainers.md)

* [如何将 Log Analytics 代理部署到节点上](./service-fabric-diagnostics-oms-agent.md)

* [Log Analytics 日志搜索](../azure-monitor/log-query/log-query-overview.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：从操作系统收集安全日志

**指导**：将 Azure Service Fabric 群集加入 Azure Monitor。 确保根据组织的合规性规章为使用的 Log Analytics 工作区设置日志保留期。

* [配置 Azure Monitor 日志与 Service Fabric 的集成](./service-fabric-diagnostics-oms-setup.md)

* [在 Azure 中设置监视容器 Azure Monitor 日志 Service Fabric](./service-fabric-tutorial-monitoring-wincontainers.md)

* [如何将 Log Analytics 代理部署到节点上](./service-fabric-diagnostics-oms-agent.md)

* [如何配置 Log Analytics 工作区保留期](../azure-monitor/platform/manage-cost-storage.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指导**：将 Azure Service Fabric 群集加入 Azure Monitor。 确保根据组织的合规性规章为使用的 Log Analytics 工作区设置日志保留期。

* [配置 Azure Monitor 日志与 Service Fabric 的集成](./service-fabric-diagnostics-oms-setup.md)

* [在 Azure 中设置监视容器 Azure Monitor 日志 Service Fabric](./service-fabric-tutorial-monitoring-wincontainers.md)

* [如何将 Log Analytics 代理部署到节点上](./service-fabric-diagnostics-oms-agent.md)

* [如何配置 Log Analytics 工作区保留期](../azure-monitor/platform/manage-cost-storage.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="26-monitor-and-review-logs"></a>2.6：监视和查看日志

**指导**：使用 Azure Log Analytics 工作区查询来查询 Azure Service Fabric 日志。

* [Log Analytics 日志搜索](../azure-monitor/log-query/log-query-overview.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

**指导**：使用 Azure Log Analytics 工作区在与 Azure Service Fabric 群集相关的安全日志和事件中监视异常活动并对其发出警报。

* [如何在 Azure 安全中心管理警报](../security-center/security-center-managing-and-responding-alerts.md)

* [如何针对 Log Analytics 日志数据发出警报](../azure-monitor/learn/tutorial-response.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中管理反恶意软件日志记录

**指导**：默认情况下，Windows Defender 安装在 Windows Server 2016 上。 如果不使用 Windows Defender，请参阅有关配置规则的反恶意软件文档。 Linux 不支持 Windows Defender。

* [有关详细信息，请参阅 Windows Server 2016 上的 Windows Defender 防病毒](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录

**指导**：实施用于 DNS 日志记录的第三方解决方案。

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="210-enable-command-line-audit-logging"></a>2.10：启用命令行审核日志记录

**指导**：为每个节点手动配置控制台日志记录。

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="identity-and-access-control"></a>标识和访问控制

有关详细信息，请参阅[安全控制：标识和访问控制](../security/benchmarks/security-control-identity-access-control.md)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指导**：维护预配 Azure Service Fabric 群集期间创建的本地管理帐户以及创建的任何其他帐户的记录。 此外，如果使用了 Azure AD 集成，必须显式分配（因此可查询）Azure AD 的内置角色。 使用 Azure AD PowerShell 模块执行即席查询，以发现属于管理组的成员的帐户。

此外，可以使用 Azure 安全中心标识和访问管理建议。

* [如何使用 PowerShell 获取 Azure AD 中的目录角色](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [如何使用 PowerShell 获取 Azure AD 中目录角色的成员](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

* [如何使用 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码

**指导**：预配群集时，Azure 会要求创建新的密码用于访问 Web 门户。 没有要更改的默认密码，但是，可以指定不同的密码用于访问 Web 门户。

* [在 Azure 门户中创建](./service-fabric-cluster-creation-via-portal.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指导**：将 Azure Service Fabric 身份验证与 Azure Active Directory 集成。 围绕专用管理帐户的使用创建策略和过程。

此外，可以使用 Azure 安全中心标识和访问管理建议。

* [设置 Azure Active Directory 客户端身份验证](./service-fabric-tutorial-create-vnet-and-windows-cluster.md#set-up-azure-active-directory-client-authentication)

* [如何使用 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：将单一登录 (SSO) 与 Azure Active Directory 配合使用

**指导**：请尽可能使用 Azure Active Directory SSO，而不是为每个服务配置单个独立凭据。 请使用 Azure 安全中心标识和访问管理建议。

* [了解 Azure AD 的 SSO](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指导**：启用 Azure AD MFA，并遵循 Azure 安全中心标识和访问管理建议。

* [如何在 Azure 中启用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

* [如何在 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：对所有管理任务使用专用计算机（特权访问工作站）

**指导**：使用配置了多重身份验证 (MFA) 的 PAW（特权访问工作站）来登录和配置 Azure Service Fabric 群集与相关资源。

* [了解特权访问工作站](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [如何在 Azure 中启用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

**指导**：当环境中出现可疑或不安全的活动时，可使用 Azure Active Directory (AD) Privileged Identity Management (PIM) 生成日志和警报。 此外，还可使用 Azure AD 风险检测来查看警报和报告有风险的用户行为。

* [如何部署 Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [了解 Azure AD 风险检测](../active-directory/identity-protection/overview-identity-protection.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指南**：使用条件访问命名位置，仅允许从 IP 地址范围或国家/地区的特定逻辑分组进行访问。

* [如何在 Azure 中配置命名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指导**：使用 Azure Active Directory (AAD) 作为中心身份验证和授权系统，以安全访问 Azure Service Fabric 群集的管理终结点。 AAD 通过对静态数据和传输中数据使用强加密来保护数据。 AAD 还会对用户凭据进行加盐、哈希处理和安全存储。

* [如何创建和配置 AAD 实例](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* [为 Service Fabric 客户端身份验证设置 Azure Active Directory](./service-fabric-cluster-creation-setup-aad.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指导**：对 Azure Service Fabric 群集使用 Azure Active Directory (AAD) 身份验证。 AAD 提供日志来帮助发现过时的帐户。 此外，请使用 Azure 标识访问评审来有效管理组成员身份、对企业应用程序的访问和角色分配。 可以定期评审用户的访问权限，确保只有适当的用户才持续拥有访问权限。

* [如何使用 Azure 标识访问评审](../active-directory/governance/access-reviews-overview.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="311-alert-on-account-login-behavior-deviation"></a>3.11：针对帐户登录行为偏差发出警报

**指导**：使用 Azure Active Directory (AAD) 登录和审核日志来监视访问已停用帐户的企图；可将这些日志集成到任何第三方 SIEM/监视工具中。

可以通过为 AAD 用户帐户创建诊断设置，并将审核日志和登录日志发送到 Azure Log Analytics 工作区，来简化此过程。 在 Azure Log Analytics 工作区中配置所需的警报。

* [如何将 Azure 活动日志集成到 Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报

**指南**：可使用 Azure AD 风险和标识保护功能来配置对检测到的与用户标识相关的可疑操作的自动响应。 还可以将数据引入 Azure Sentinel 中以便进一步调查。

* [如何查看 Azure AD 风险登录](../active-directory/identity-protection/overview-identity-protection.md)

* [如何配置和启用标识保护风险策略](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持场合下为 Microsoft 提供对相关客户数据的访问权限

**指导**：不可用；Azure Service Fabric 尚不支持客户密码箱。

* [支持客户密码箱的服务列表](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="data-protection"></a>数据保护

有关详细信息，请参阅[安全控制：数据保护](../security/benchmarks/security-control-data-protection.md)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指导**：对与 Azure Service Fabric 部署相关的资源使用标记，以帮助跟踪存储或处理敏感信息的 Azure 资源。

* [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指导**：为开发、测试和生产实现单独的订阅和/或管理组。 资源应当按 VNet/子网进行分隔，相应地进行标记，并由 NSG 或 Azure 防火墙提供保护。 存储或处理敏感数据的资源应当充分隔离。 对于存储或处理敏感数据的虚拟机，请实施相应的策略和过程，以在不使用这些虚拟机时将其关闭。

* [如何创建其他 Azure 订阅](../cost-management-billing/manage/create-subscription.md)

* [如何创建管理组](../governance/management-groups/create-management-group-portal.md)

* [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

* [如何创建虚拟网络](../virtual-network/quick-create-portal.md)

* [如何创建采用安全配置的 NSG](../virtual-network/tutorial-filter-network-traffic.md)

* [如何部署 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)

* [如何通过 Azure 防火墙配置“警报”或“发出警报并拒绝”](../firewall/threat-intel.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输

**指导**：在网络外围部署一个自动化工具，用于监视敏感信息的未授权传输，并阻止此类传输，同时提醒信息安全专业人员。

对于 Microsoft 管理的底层平台，Microsoft 会将所有客户内容视为敏感数据，并会全方位地防范客户数据丢失和遭到透露。 为了确保 Azure 中的客户数据保持安全，Microsoft 实现并维护了一套可靠的数据保护控件和

和功能。

* [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视**：目前不可用

**责任**：共享

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指导**：加密传输中的所有敏感信息。 确保连接到 Azure 资源的任何客户端能够协商 TLS 1.2 或更高版本。

请按照 Azure 安全中心的建议，了解静态加密和传输中的加密（如果适用）。

* [了解 Azure 传输中的加密](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

* [Service Fabric 群集安全方案](./service-fabric-cluster-security.md)

* [有关 TLS 配置的 Service Fabric 故障排除指南](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/TLS%20Configuration.md)

**Azure 安全中心监视**：目前不可用

**责任**：共享

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指导**：数据标识、分类和丢失防护功能尚不适用于 Azure 存储或计算资源。 如果需要出于合规性目的使用这些功能，请实施第三方解决方案。

对于 Microsoft 管理的底层平台，Microsoft 会将所有客户内容视为敏感数据，并会全方位地防范客户数据丢失和遭到透露。 为了确保 Azure 中的客户数据保持安全，Microsoft 实施并维护了一套可靠的数据保护控制措施和功能。

* [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视**：目前不可用

**责任**：共享

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 控制对资源的访问

**指南**：不适用；此建议适用于设计用于存储数据的非计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用基于主机的数据丢失防护来强制实施访问控制

**指导**：对于存储或处理敏感信息的 Azure Service Fabric 群集，请使用标记将该群集和相关资源标记为敏感。 数据标识、分类和丢失防护功能尚不适用于 Azure 存储或计算资源。 如果需要出于合规性目的使用这些功能，请实施第三方解决方案。

对于 Microsoft 管理的底层平台，Microsoft 会将所有客户内容视为敏感数据，并会全方位地防范客户数据丢失和遭到透露。 为了确保 Azure 中的客户数据保持安全，Microsoft 实施并维护了一套可靠的数据保护控制措施和功能。

* [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视**：目前不可用

**责任**：共享

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指导**：在所有 Azure 资源上使用静态加密。 Microsoft 建议允许 Azure 管理加密密钥，但在某些情况下，你可以选择管理自己的密钥。

* [了解 Azure 中的静态加密](../security/fundamentals/encryption-atrest.md)

* [如何配置客户管理的加密密钥](../storage/common/customer-managed-keys-configure-key-vault.md)

* [为 Windows 中的 Azure Service Fabric 群集节点启用磁盘加密](./service-fabric-enable-azure-disk-encryption-windows.md)

* [为 Linux 中的 Azure Service Fabric 群集节点启用磁盘加密](./service-fabric-enable-azure-disk-encryption-linux.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指导**：将 Azure Monitor 与 Azure 活动日志结合使用，以创建在关键 Azure 资源发生更改时发出的警报。

* [如何针对 Azure 活动日志事件创建警报](../azure-monitor/platform/alerts-activity-log.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="vulnerability-management"></a>漏洞管理

有关详细信息，请参阅[安全控制：漏洞管理](../security/benchmarks/security-control-vulnerability-management.md)。

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：运行自动漏洞扫描工具

**指导**：定期运行 Service Fabric 故障分析服务和混沌服务，以模拟整个群集中的故障，从而评估服务的稳定性和可靠性。

遵循 Azure 安全中心关于在 Azure 虚拟机和容器映像上执行漏洞评估的建议。

使用第三方解决方案对网络设备和 Web 应用程序执行漏洞评估。 执行远程扫描时，不要使用单个永久管理帐户。 请考虑为扫描帐户实现 JIT 预配方法。 扫描帐户的凭据应受到保护、监视，并且仅用于漏洞扫描。

* [Service Fabric 故障分析服务简介](./service-fabric-testability-overview.md)

* [在 Service Fabric 群集中引入受控的混沌测试](./service-fabric-controlled-chaos.md)

* [如何实现 Azure 安全中心漏洞评估建议](../security-center/deploy-vulnerability-assessment-vm.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自动操作系统修补管理解决方案

**指导**：在 Azure Service Fabric 群集的虚拟机规模集上启用自动 OS 映像升级。

或者，若要在投入生产之前先测试 OS 修补程序，请使用手动触发器对规模集进行 OS 映像升级。 请注意，手动触发器选项不提供内置回滚。 使用 Azure 自动化中的更新管理监视 OS 修补程序。

* [Service Fabric 群集节点的修补程序管理](./service-fabric-best-practices-infrastructure-as-code.md#azure-virtual-machine-operating-system-automatic-upgrade-configuration)

* [Azure 虚拟机规模集的自动 OS 映像升级](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)

* [如何使用最新的规模集模型对 VM 进行更新](../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)

* [Azure 自动化更新管理概述](../automation/update-management/overview.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3：为第三方软件部署自动修补程序管理解决方案

**指导**：在 Azure Service Fabric 群集的虚拟机规模集上启用自动 OS 映像升级。 修补业务流程应用程序 (POA) 是一种替代解决方案，适用于 Azure 外部托管的 Service Fabric 群集。 可结合使用 POA 和 Azure 群集，但需要一些额外的托管开销。

* [Service Fabric 群集节点的修补程序管理](./service-fabric-best-practices-infrastructure-as-code.md#azure-virtual-machine-operating-system-automatic-upgrade-configuration)

* [Azure 虚拟机规模集的自动 OS 映像升级](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)

* [如何为 Service Fabric 群集配置 OS 修补计划](./service-fabric-patch-orchestration-application.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比较连续进行的漏洞扫描

**指导**：以一致的间隔导出扫描结果，并比较结果以验证漏洞是否已修复。 使用 Azure 安全中心建议的漏洞管理建议时，可以转到选定解决方案的门户查看历史扫描数据。

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险评级过程来确定已发现漏洞的修正措施的优先级

**指导**：使用常用的风险评分程序（例如通用漏洞评分系统）或第三方扫描工具提供的默认风险评级。

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="inventory-and-asset-management"></a>库存和资产管理

有关详细信息，请参阅[安全控制：清单和资产管理](../security/benchmarks/security-control-inventory-asset-management.md)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动化资产发现解决方案

**指导**：使用 Azure Resource Graph 查询/发现订阅中的所有资源（例如计算、存储、网络、端口和协议等）。 确保租户中具有适当的（读取）权限，并枚举所有 Azure 订阅以及订阅中的资源。

尽管可以通过 Resource Graph 发现经典 Azure 资源，但我们强烈建议你今后还是创建并使用 Azure 资源管理器资源。

* [如何使用 Azure Resource Graph 创建查询](../governance/resource-graph/first-query-portal.md)

* [如何查看 Azure 订阅](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [了解 Azure RBAC](../role-based-access-control/overview.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指导**：将标记应用于 Azure 资源，从而将元数据按逻辑组织到分类中。

* [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指导**：在适用的情况下，请使用标记、管理组和单独的订阅来组织和跟踪资产。 定期核对清单，确保及时地从订阅中删除未经授权的资源。

* [如何创建其他 Azure 订阅](../cost-management-billing/manage/create-subscription.md)

* [如何创建管理组](../governance/management-groups/create-management-group-portal.md)

* [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4：定义并维护已批准的 Azure 资源的清单

**指导**：为计算资源定义已批准的 Azure 资源和软件。

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指南**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：

不允许的资源类型

允许的资源类型

使用 Azure Resource Graph 查询/发现订阅中的资源。 确保环境中存在的所有 Azure 资源已获得批准。

* [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [如何使用 Azure Graph 创建查询](../governance/resource-graph/first-query-portal.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：监视计算资源中未批准的软件应用程序

**指导**：实施第三方解决方案以监视群集节点中未批准的软件应用程序。

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未批准的 Azure 资源和软件应用程序

**指导**：使用 Azure Resource Graph 查询/发现订阅中的所有资源（例如计算、存储、网络、端口和协议等），包括 Azure Service Fabric 群集。 删除发现的任何未批准 Azure 资源。 对于 Azure Service Fabric 群集节点，请实施第三方解决方案来删除未批准的软件或对其发出警报。

* [如何使用 Azure Graph 创建查询](../governance/resource-graph/first-query-portal.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="68-use-only-approved-applications"></a>6.8：仅使用已批准的应用程序

**指导**：对于 Azure Service Fabric 群集节点，请实施第三方解决方案，以防止执行未经授权的软件。

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指导**：使用 Azure Policy 限制可在环境中预配的服务。

* [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [如何使用 Azure Policy 拒绝特定的资源类型](../governance/policy/samples/index.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：维护已获批软件的清单

**指导**：对于 Azure Service Fabric 群集节点，请实施第三方解决方案，以防止执行未经授权的文件类型。

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制用户与 Azure 资源管理器进行交互的能力

**指导**：通过为“Microsoft Azure 管理”应用配置“阻止访问”，使用 Azure 条件访问来限制用户与 Azure 资源管理器交互的能力。

* [如何配置条件访问来阻止对 Azure 资源管理器的访问](../role-based-access-control/conditional-access-azure-management.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制用户在计算资源中执行脚本的功能

**指导**：使用特定于操作系统的配置或第三方资源来限制用户在 Azure 计算资源中执行脚本的能力。

* [例如，如何在 Windows 环境中控制 PowerShell 脚本执行](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以物理或逻辑方式隔离高风险应用程序

**指导**：业务运营所需的软件可能会给组织带来更高的风险，应将其隔离在自己的虚拟机和/或虚拟网络中，并通过 Azure 防火墙或网络安全组进行充分的保护。

* [如何创建虚拟网络](../virtual-network/quick-create-portal.md)

* [如何创建采用安全配置的 NSG](../virtual-network/tutorial-filter-network-traffic.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="secure-configuration"></a>安全配置

有关详细信息，请参阅[安全控制：安全配置](../security/benchmarks/security-control-secure-configuration.md)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指导**：在“Microsoft.ServiceFabric”命名空间中使用 Azure Policy 别名创建自定义策略，以审核或强制实施 Service Fabric 群集的网络配置。

* [如何查看可用的 Azure Policy 别名](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的操作系统配置

**指导**：Azure Service Fabric 操作系统映像由 Microsoft 管理和维护。 客户负责为群集节点的操作系统实施安全配置。

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指导**：使用 Azure 策略“[拒绝]”和“[不存在则部署]”来对 Azure Service Fabric 群集和相关资源强制实施安全设置。

* [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [了解 Azure Policy 效果](../governance/policy/concepts/effects.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：维护安全的操作系统配置

**指导**：Azure Service Fabric 群集操作系统映像由 Microsoft 管理和维护。 客户负责实施 OS 级别的状态配置。

**Azure 安全中心监视**：目前不可用

**责任**：共享

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指导**：如果使用自定义的 Azure Policy 定义，请使用 Azure DevOps 或 Azure Repos 安全地存储和管理代码。

* [如何在 Azure DevOps 中存储代码](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure Repos 文档](/azure/devops/repos/index?view=azure-devops)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全存储自定义操作系统映像

**指导**：如果使用自定义映像，请使用 Azure 基于角色的访问控制 (Azure RBAC) 来确保只有授权用户才能访问映像。 对于容器映像，请将其存储在 Azure 容器注册表中，并利用 Azure RBAC 确保只有授权用户才能访问这些映像。

* [了解 Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md)

* [了解容器注册表的 Azure RBAC](../container-registry/container-registry-roles.md)

* [如何配置 Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署 Azure 资源的配置管理工具

**指导**：在“Microsoft.ServiceFabric”命名空间中使用 Azure Policy 别名创建自定义策略，以审核、强制实施系统配置并对其发出警报。 另外，开发一个用于管理策略例外的流程和管道。

* [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8：部署操作系统的配置管理工具

**指导**：不适用；此项指导适用于 IaaS 计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：为 Azure 资源实施自动配置监视

**指导**：在“Microsoft.ServiceFabric”命名空间中使用 Azure Policy 别名创建自定义策略，以审核或强制实施 Service Fabric 群集的配置。

* [如何查看可用的 Azure Policy 别名](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：为操作系统实施自动配置监视

**指导**：使用 Azure 安全中心对 OS 和容器的 Docker 设置执行基线扫描。

* [了解 Azure 安全中心容器建议](../security-center/container-security.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指导**：将托管服务标识与 Azure Key Vault 结合使用，以便简化和保护云应用程序的机密管理。

* [通过 Service Fabric 使用 Azure 的托管标识](./concepts-managed-identity.md)

* [为新的 Service Fabric 群集配置托管标识支持](./configure-new-azure-service-fabric-enable-managed-identity.md)

* [将托管标识用于 Service Fabric 应用程序](./how-to-managed-identity-service-fabric-app-code.md)

* [Service Fabric 应用程序的 KeyVaultReference 支持](./service-fabric-keyvault-references.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全自动管理标识

**指导**：托管标识可用于 Azure 部署的 Service Fabric 群集，也可用于部署为 Azure 资源的应用程序。 使用托管标识可以向支持 Azure AD 身份验证的任何服务（包括 Key Vault）进行身份验证，无需在代码中放入任何凭据。

* [通过 Service Fabric 使用 Azure 的托管标识](./concepts-managed-identity.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指导**：如果使用与 Azure Service Fabric 部署相关的任何代码，可以实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。

使用 Azure Key Vault 自动轮换 Service Fabric 群集证书。

* [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)

* [Service Fabric 群集中的证书管理](./cluster-security-certificate-management.md#certificate-rotation)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="malware-defense"></a>恶意软件防护

有关详细信息，请参阅[安全控制：恶意软件防护](../security/benchmarks/security-control-malware-defense.md)。

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反恶意软件

**指导**：默认情况下，Windows Defender 防病毒安装在 Windows Server 2016 上。 用户界面默认安装在某些 SKU 上，但不是必需的。

如果不使用 Windows Defender，请参阅有关配置规则的反恶意软件文档。 Linux 不支持 Windows Defender。

* [了解 Windows Server 2016 上的 Windows Defender 防病毒](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预先扫描要上传到非计算 Azure 资源的文件

**指南**：不适用；此建议适用于设计用于存储数据的非计算资源。 支持 Azure 服务的基础主机上启用了 Microsoft 反恶意软件 (例如 Service Fabric) ，但它不会在客户内容上运行。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>步骤 8.3：确保反恶意软件和签名已更新

**指南**：不适用；此建议适用于设计用于存储数据的非计算资源。 支持 Azure 服务的基础主机上启用了 Microsoft 反恶意软件 (例如 Service Fabric) ，但它不会在客户内容上运行。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="data-recovery"></a>数据恢复

有关详细信息，请参阅[安全控制：数据恢复](../security/benchmarks/security-control-data-recovery.md)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

**指导**：Service Fabric 中的备份和还原服务可以轻松自动备份存储在有状态服务中的信息。 定期备份应用程序数据是防止数据丢失和服务不可用的基础。 Service Fabric 提供可选的备份和还原服务，因此无需编写任何其他代码，便可配置有状态可靠服务（包括角色服务）的定期备份。 它还有助于还原以前执行的备份。

* [在 Azure Service Fabric 群集中定期备份和还原](./service-fabric-backuprestoreservice-quickstart-azurecluster.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥

**指导**：在 Service Fabric 群集中启用备份还原服务，并创建备份策略以定期和按需备份有状态服务。 在 Azure Key Vault 中备份客户管理的密钥。

* [在 Azure Service Fabric 群集中定期备份和还原](./service-fabric-backuprestoreservice-quickstart-azurecluster.md)

* [了解 Azure Service Fabric 中的定期备份配置](./service-fabric-backuprestoreservice-configure-periodic-backup.md)

* [如何在 Azure 中备份密钥保管库密钥](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指导**：通过定期查看备份配置信息和可用备份，确保能够从备份还原服务执行还原。 测试对备份的客户管理的密钥进行还原。

* [了解 Azure Service Fabric 中的定期备份配置](./service-fabric-backuprestoreservice-configure-periodic-backup.md)

* [在 Azure Service Fabric 中还原备份](./service-fabric-backup-restore-service-trigger-restore.md)

* [如何在 Azure 中还原密钥保管库密钥](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指导**：Service Fabric 备份还原服务中的备份使用订阅中的 Azure 存储帐户。 Azure 存储对静态存储帐户中的所有数据进行加密。 默认情况下，数据使用 Microsoft 管理的密钥进行加密。 为了更进一步控制加密密钥，可以提供客户管理的密钥用于对存储数据进行加密。

如果使用的是客户管理的密钥，请确保在 Key Vault 中启用软删除，以防止意外或恶意删除密钥。

* [Azure 存储静态加密](../storage/common/storage-service-encryption.md)

* [如何在 Key Vault 中启用“软删除”](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="incident-response"></a>事件响应

有关详细信息，请参阅[安全控制：事件响应](../security/benchmarks/security-control-incident-response.md)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指导**：为组织制定事件响应指南。 确保在书面的事件响应计划中定义人员职责，以及事件处理和管理从检测到事件后审查的各个阶段。

* [关于建立自己的安全事件响应流程的指南](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft 安全响应中心事件分析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [使用 NIST 的“计算机安全事件处理指南”，帮助制定自己的事件响应计划](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指导**：Azure 安全中心为每条警报分配严重性，方便你根据优先级来确定应该最先调查的警报。 严重性取决于安全中心在发出警报时所依据的检测结果和分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。

此外，使用标记来标记订阅，并创建命名系统来对 Azure 资源进行标识和分类，特别是处理敏感数据的资源。 你的责任是根据发生事件的 Azure 资源和环境的关键性确定修正警报的优先级。

* [Azure 安全中心中的安全警报](../security-center/security-center-alerts-overview.md)

* [使用标记整理 Azure 资源](../azure-resource-manager/management/tag-resources.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指导**：定期执行演练来测试系统的事件响应功能。 识别弱点和差距，并根据需要修改计划。

* [T 计划和功能的测试、培训和练习计划 NIST 指南](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指导**：如果 Microsoft 安全响应中心 (MSRC) 发现数据被某方非法访问或未经授权访问，Microsoft 会使用安全事件联系信息联系用户。 事后审查事件，确保问题得到解决。

* [如何设置 Azure 安全中心安全联系人](../security-center/security-center-provide-security-contact-details.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指导**：使用连续导出功能导出 Azure 安全中心警报和建议。 使用连续导出可以手动导出或者持续导出警报和建议。 可以使用 Azure 安全中心数据连接器将警报流式传输到 Azure Sentinel。

* [如何配置连续导出](../security-center/continuous-export.md)

* [如何将警报流式传输到 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指导**：使用 Azure 安全中心内的工作流自动化功能可以通过“逻辑应用”针对安全警报和建议自动触发响应。

* [如何配置工作流自动化和逻辑应用](../security-center/workflow-automation.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

有关详细信息，请参阅[安全控制：渗透测试和红队演练](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：定期对 Azure 资源执行渗透测试，确保修正所有发现的关键安全问题

**指导**：请遵循 Microsoft 云渗透测试互动规则，确保你的渗透测试不违反 Microsoft 政策。 使用 Microsoft 红队演练策略和执行，以及针对 Microsoft 托管云基础结构、服务和应用程序执行现场渗透测试。

* [参与的渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视**：目前不可用

**责任**：共享

## <a name="next-steps"></a>后续步骤

- 请参阅 [Azure 安全基准](../security/benchmarks/overview.md)
- 详细了解 [Azure 安全基线](../security/benchmarks/security-baselines-overview.md)