---
title: 适用于 Azure 存储的 Azure 安全基线
description: 适用于 Azure 存储的 Azure 安全基线
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: f6825fee4cfb78ab54d782cd4d942bb994bedae9
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98195715"
---
# <a name="azure-security-baseline-for-azure-storage"></a>适用于 Azure 存储的 Azure 安全基线

适用于 Azure 存储的 Azure 安全基线包含有助于改进部署安全状况的建议。

此服务的基线摘自 [Azure 安全基准版本 1.0](../../security/benchmarks/overview.md)，其中提供了有关如何根据我们的最佳做法指导保护 Azure 上的云解决方案的建议。

有关详细信息，请参阅 [Azure 安全基线概述](../../security/benchmarks/security-baselines-overview.md)。

## <a name="network-security"></a>网络安全

有关详细信息，请参阅[安全控制：网络安全性](../../security/benchmarks/security-control-network-security.md)。

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：在虚拟网络上使用网络安全组或 Azure 防火墙来保护资源

**指导**：通过限制从特定的公共 IP 地址范围、Azure 上的特定虚拟网络 (VNet) 访问客户端，或者限制为只能访问特定的 Azure 资源，对存储帐户的防火墙进行配置。 还可以配置专用终结点，以便从企业到存储服务的流量仅通过专用网络进行传输。

注意：经典存储帐户不支持防火墙和虚拟网络。

- [如何配置 Azure 存储防火墙](./storage-network-security.md#change-the-default-network-access-rule)

- [如何配置 Azure 存储的专用终结点](./storage-private-endpoints.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="12-monitor-and-log-vnet-subnet-and-nic-configuration-and-traffic"></a>1.2：监视和记录 Vnet、子网和 NIC 配置与流量

**指导**：Azure 存储提供分层安全模型。 可将存储帐户的访问权限限制给源自指定的 IP 地址、IP 范围，或 Azure 虚拟网络 (VNet) 中某个子网列表的请求。 可以使用 Azure 安全中心并遵循网络保护建议来帮助保护 Azure 中的网络资源。 此外，请为已通过存储帐户防火墙为存储帐户配置的虚拟网络/子网启用 NSG 流日志，并将日志发送到存储帐户进行流量审核。 

请注意，如果你的专用终结点已附加到存储帐户，则无法为子网配置网络安全组 (NSG) 规则。 

- [配置 Azure 存储防火墙和虚拟网络](./storage-network-security.md)

- [如何启用 NSG 流日志](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [了解 Azure 安全中心提供的网络安全](../../security-center/security-center-network-recommendations.md)

- [了解 Azure 存储的专用终结点](./storage-private-endpoints.md#known-issues)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="13-protect-critical-web-applications"></a>1.3：保护关键 Web 应用程序

**指导**：不适用；建议适用于 Azure 应用服务或计算资源上运行的 Web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知的恶意 IP 地址进行通信

**指南**：对 Azure 存储帐户启用高级威胁防护。 适用于 Azure 存储的高级威胁防护提供额外的安全智能层，用于检测访问或利用存储帐户的异常和潜在有害尝试。 Azure 安全中心集成警报基于与已成功解析的 IP 地址关联的网络通信的活动，无论该 IP 地址是否为已知的危险 IP 地址 (例如，已知的 cryptominer) 或以前无法识别为有风险的 IP 地址。 当活动出现异常时，会触发安全警报。 

- [如何启用高级威胁防护](./azure-defender-storage-configure.md?tabs=azure-portal)

- [了解 Azure 安全中心集成的威胁情报](../../security-center/azure-defender.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="15-record-network-packets-and-flow-logs"></a>1.5：记录网络数据包和流日志

**指导**：使用网络观察程序数据包捕获，可以创建捕获会话以跟踪存储帐户和虚拟机之间的流量。 为捕获会话提供了筛选器以确保仅捕获所需的流量。 数据包捕获有助于以主动和被动方式诊断网络异常。 其他用途包括收集网络统计信息，获得网络入侵信息，调试客户端与服务器之间的通信，等等。 由于能够远程触发数据包捕获，此功能可减轻在所需虚拟机上手动运行数据包捕获的负担，从而节省宝贵的时间。 

- [在门户中使用 Azure 网络观察程序管理数据包捕获](../../network-watcher/network-watcher-packet-capture-manage-portal.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1.6：部署基于网络的入侵检测/入侵防护系统

**指南**： Azure 存储的高级威胁防护提供额外的安全智能层，用于检测访问或利用存储帐户的异常和潜在有害尝试。 当活动出现异常时，会触发安全警报。 这些安全警报与 Azure 安全中心集成，并通过电子邮件发送给订阅管理员，内容包括可疑活动的详细信息以及如何调查和修正威胁的建议。 

- [配置 Azure 存储的高级威胁防护](./azure-defender-storage-configure.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="17-manage-traffic-to-your-web-applications"></a>1.7：管理发往 Web 应用程序的流量

**指导**：不适用；建议适用于 Azure 应用服务或计算资源上运行的 Web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指导**：对于虚拟网络中需要访问存储帐户的资源，请在网络安全组或 Azure 防火墙上使用已配置的虚拟网络的虚拟网络服务标记来定义网络访问控制。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 通过在规则的相应“源”或“目标”字段中指定服务标记名称（例如 Azure 存储），可允许或拒绝相应服务的流量。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。 

如果需要将网络访问的范围限定为特定的存储帐户，请使用虚拟网络服务终结点策略。

- [有关使用服务标记的详细信息](../../virtual-network/service-tags-overview.md)

- [有关 Azure 存储的虚拟网络服务终结点策略的详细信息](../../virtual-network/virtual-network-service-endpoint-policies-overview.md)

**Azure 安全中心监视**：目前不可用

**责任**：服务

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指导**：使用 Azure Policy 为与 Azure 存储帐户关联的网络资源定义和实施标准安全配置。 在“Microsoft.Storage”和“Microsoft.Network”命名空间中使用 Azure Policy 别名创建自定义策略，以审核或强制实施存储帐户资源的网络配置。 

还可以利用与存储帐户相关的内置策略定义，例如：存储帐户应使用虚拟网络服务终结点 

- [如何配置和管理 Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [用于 Azure 存储的 Azure Policy 示例](../../governance/policy/samples/built-in-policies.md#storage)

- [用于网络的 Azure Policy 示例](../../governance/policy/samples/built-in-policies.md#network)

- [如何创建 Azure 蓝图](../../governance/blueprints/create-blueprint-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="110-document-traffic-configuration-rules"></a>1.10：记录流量配置规则

**指导**：将标记用于网络安全组 (NSG) 以及其他与网络安全和通信流有关的资源。 对于单个 NSG 规则，请使用“说明”字段针对允许流量传入/传出网络的任何规则指定业务需求和/或持续时间等。 使用标记相关的任何内置 Azure Policy 定义（例如“需要标记及其值”）来确保使用标记创建所有资源，并在有现有资源不带标记时发出通知。 可以使用 Azure PowerShell 或 Azure CLI 基于其标记对资源进行查找或执行操作。 

- [如何创建和使用标记](../../azure-resource-manager/management/tag-resources.md)

- [如何创建虚拟网络](../../virtual-network/quick-create-portal.md)

- [如何创建采用安全配置的 NSG](../../virtual-network/tutorial-filter-network-traffic.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指导**：使用 Azure Policy 来记录网络资源的配置更改。 在 Azure Monitor 中创建当关键网络资源发生更改时触发的警报。 

- [如何配置和管理 Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [如何在 Azure Monitor 中创建警报](../../azure-monitor/platform/alerts-activity-log.md)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="logging-and-monitoring"></a>日志记录和监视

有关详细信息，请参阅[安全控制：日志记录和监视](../../security/benchmarks/security-control-logging-monitoring.md)。

### <a name="21-use-approved-time-synchronization-resource"></a>2.1：使用批准的时间同步资源

**指南**：不适用;Microsoft 维护 Azure 存储帐户的时间源。

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指导**：通过 Azure Monitor 引入日志，以聚合终结点设备、网络资源和其他安全系统生成的安全数据。 在 Azure Monitor 中，使用 Log Analytics 工作区查询和执行分析，并使用 Azure 存储帐户进行长期/存档存储（可以选择使用不可变存储和强制保留等安全功能）。

- [如何使用 Azure Monitor 收集平台日志和指标](../../azure-monitor/platform/diagnostic-settings.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指导**：Azure 存储分析提供 Blob、队列和表的日志。 可以使用 Azure 门户来配置要为帐户记录哪些日志。 

- [如何为 Azure 存储帐户配置监视](./storage-monitor-storage-account.md#configure-monitoring-for-a-storage-account)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：从操作系统收集安全日志

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指导**：在 Azure 存储帐户或 Log Analytics 工作区中存储安全事件日志时，可以根据组织的需求设置保留策略。 

- [如何为 Azure 存储帐户日志配置保留策略](./storage-monitor-storage-account.md#configure-logging)

- [更改 Log Analytics 中的数据保留期](../../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="26-monitor-and-review-logs"></a>2.6：监视和审查日志

**指导**：若要查看 Azure 存储日志，可以使用通过 Log Analytics 产品/服务进行查询之类的常用选项，也可以使用唯一用于直接查看日志文件的选项。 在 Azure 存储中，日志存储在 `http://accountname.blob.core.windows.net/$logs` 默认情况下隐藏 (日志记录文件夹的 blob 中，因此你将需要直接导航。 它不会显示在 List 命令中） 

同时，为你的 Azure 存储帐户启用高级威胁防护。 适用于 Azure 存储的高级威胁防护提供额外的安全智能层，用于检测访问或利用存储帐户的异常和潜在有害尝试。 当活动出现异常时，会触发安全警报。 这些安全警报与 Azure 安全中心集成，并通过电子邮件发送给订阅管理员，内容包括可疑活动的详细信息以及如何调查和修正威胁的建议。 

- [记录和查看数据](./storage-analytics-logging.md#how-logs-are-stored)

- [如何启用高级威胁防护](./azure-defender-storage-configure.md?tabs=azure-portal)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7：启用针对异常活动的警报

**指南**：在 Azure 安全中心中，为存储帐户启用高级威胁防护。 启用存储帐户的诊断设置，并将日志发送到 Log Analytics 工作区。 将 Log Analytics 工作区加入 Azure Sentinel，因为它提供了安全业务流程自动化响应 (SOAR) 解决方案。 这样便可以创建 playbook（自动化解决方案）并将其用于修正安全问题。 

- [如何加入 Azure Sentinel](../../sentinel/quickstart-onboard.md)

- [如何在 Azure 安全中心管理警报](../../security-center/security-center-managing-and-responding-alerts.md)

- [如何针对 Log Analytics 日志数据发出警报](../../azure-monitor/learn/tutorial-response.md)

- [Azure 存储分析日志记录](./storage-analytics-logging.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中管理反恶意软件日志记录

**指南**：使用 azure 安全中心并为 azure 存储启用威胁防护，使用哈希信誉分析来检测恶意软件上传到 azure 存储，并从活动 Tor 出口节点 (匿名 proxy) 进行可疑访问。 

- [配置 Azure 存储的高级威胁防护](./azure-defender-storage-configure.md?tabs=azure-portal)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录

**指导**：Azure Monitor 中的 Azure DNS Analytics（预览）解决方案收集有关 DNS 基础结构安全性、性能和操作的见解。 此功能当前不支持 Azure 存储帐户，但你可以使用第三方 dns 日志记录解决方案。 

- [使用 DNS Analytics 预览解决方案收集有关 DNS 基础结构的见解](../../azure-monitor/insights/dns-analytics.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="210-enable-command-line-audit-logging"></a>2.10：启用命令行审核日志记录

**指导**：不适用；基准适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="identity-and-access-control"></a>标识和访问控制

有关详细信息，请参阅[安全控制：标识和访问控制](../../security/benchmarks/security-control-identity-access-control.md)。

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指南**：Azure AD 具有必须显式分配且可查询的内置角色。 使用 Azure AD PowerShell 模块执行即席查询，以发现属于管理组成员的帐户。 

- [如何使用 PowerShell 获取 Azure AD 中的目录角色](/powershell/module/azuread/get-azureaddirectoryrole)

- [如何使用 PowerShell 获取 Azure AD 中目录角色的成员](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码

**指导**：Azure 存储帐户和 Azure Active Directory 都没有默认密码或空密码的概念。 Azure 存储实现了一个访问控制模型，该模型支持 Azure 基于角色的访问控制 (Azure RBAC) 以及共享密钥和共享访问签名 (SAS)。 共享密钥和 SAS 身份验证的特点是没有与调用方关联的标识，因此不能执行基于安全主体权限的授权。 

- [授予访问 Azure 存储中的数据的权限](./storage-auth.md)

- [了解 Azure 存储帐户的安全主体和访问控制](./storage-introduction.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指导**：围绕可以访问存储帐户的专用管理帐户的使用，创建标准操作过程。 使用 Azure 安全中心标识和访问管理来监视管理帐户的数量。 

你还可以通过 Microsoft 服务的 Azure AD Privileged Identity Management 特权角色和 Azure ARM 来启用实时/恰好足够的访问权限。 

- [了解 Azure 安全中心标识和访问](../../security-center/security-center-identity-access.md)

- [Privileged Identity Management 概述](../../active-directory/privileged-identity-management/index.yml)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4：使用 Azure Active Directory 单个 Sign-On (SSO) 

**指南**：尽可能使用 Azure Active Directory SSO，而不是为每个服务配置单独的独立凭据。 请使用 Azure 安全中心标识和访问管理建议。 

- [了解 Azure AD 的 SSO](../../active-directory/manage-apps/what-is-single-sign-on.md)

- [授予访问 Azure 存储中的数据的权限](./storage-auth.md)

- [使用 Azure Active Directory 授予对 Blob 和队列的访问权限](./storage-auth-aad.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证。

**指导**：启用 Azure Active Directory 多重身份验证，并遵循 Azure 安全中心标识和访问管理建议，以帮助保护 Azure 存储帐户资源。 

- [如何在 Azure 中启用 MFA](../../active-directory/authentication/howto-mfa-getstarted.md)

- [如何在 Azure 安全中心监视标识和访问](../../security-center/security-center-identity-access.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：对所有管理任务使用专用计算机（特权访问工作站）

**指导**：使用配置了 MFA 的 PAW（特权访问工作站）来登录并配置存储帐户资源。 

- [了解特权访问工作站](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [如何在 Azure 中启用 MFA](../../active-directory/authentication/howto-mfa-getstarted.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

**指南**：将 Azure 安全中心风险检测警报发送到 Azure Monitor，并使用操作组配置自定义警报/通知。 为 Azure 存储帐户启用高级威胁防护，以生成可疑活动的警报。 此外，使用 Azure AD 风险检测来查看警报和报告有风险的用户行为。 

- [如何设置 Azure 存储帐户的高级威胁防护](./azure-defender-storage-configure.md)

- [了解 Azure AD 风险检测](../../active-directory/identity-protection/overview-identity-protection.md)

- [如何为自定义警报和通知配置操作组](../../azure-monitor/platform/action-groups.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指导**：使用条件访问命名位置，仅允许从 IP 地址范围或国家/地区的特定逻辑分组进行访问。 

- [如何在 Azure 中配置命名位置](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指导**：使用 Azure Active Directory (Azure AD) 作为中心身份验证和授权系统。 Azure 提供 Azure 基于角色的访问控制 (Azure RBAC)，用于精细地控制客户端对存储帐户中资源的访问。  请尽可能地使用 Azure AD 凭据（而不要使用更容易泄露的帐户密钥），这是安全方面的最佳做法。 当应用程序设计需要共享访问签名以访问 Blob 存储时，请尽可能使用 Azure AD 凭据创建用户委托共享访问签名 (SAS) 以提高安全性。

- [如何创建和配置 Azure AD 实例](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [使用 Azure 存储资源提供程序访问管理资源](./authorization-resource-provider.md)

- [如何在 Azure 门户中使用 Azure RBAC 配置对 Azure Blob 和队列数据的访问权限](./storage-auth-aad-rbac-portal.md)

- [授予访问 Azure 存储中的数据的权限](./storage-auth.md)

- [使用共享访问签名 (SAS) 授予对 Azure 存储资源的有限访问权限](./storage-sas-overview.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指导**：检查 Azure Active Directory 日志，以帮助发现可能包含具有存储帐户管理角色的陈旧帐户。 此外，使用 Azure 标识访问评审可高效地管理组成员身份、对可用于访问 Azure 存储帐户资源的企业应用程序的访问权限以及角色分配。 应定期评审用户的访问权限，确保只有适当的用户才持续拥有访问权限。  

也可使用共享访问签名 (SAS) 来对存储帐户中的资源进行安全的委托访问，而不会损害数据的安全性。 你可以控制客户端可以访问哪些资源、客户端对这些资源拥有哪些权限、SAS 的有效期，以及其他参数。

另外，请评审对容器和 Blob 的匿名读取访问权限。 默认情况下，容器和其中的任何 Blob 只能由已获得适当权限的用户访问。 你可以使用 Azure Monitor，在出现针对存储帐户进行的匿名访问时根据匿名身份验证条件发出警报。

若要降低未受怀疑的用户帐户访问权限的风险，一种有效的方法是限制授予用户的访问权限的持续时间。 受时间限制的 SAS URI 是自动终止用户对存储帐户的访问权限的一种有效方式。 此外，可以经常轮换存储帐户密钥，以确保通过存储帐户密钥进行非正常访问的持续时间有限。

- [了解 Azure AD 报告](../../active-directory/reports-monitoring/index.yml)

- [如何查看和更改 Azure 存储帐户级别的访问权限](./storage-auth-aad-rbac-portal.md)

- [使用共享访问签名 (SAS) 授予对 Azure 存储资源的有限访问权限](./storage-sas-overview.md)

- [管理对容器和 Blob 的匿名读取访问](../blobs/anonymous-read-access-configure.md)

- [在 Azure 门户中监视存储帐户](./storage-monitor-storage-account.md)

- [管理存储帐户访问密钥](./storage-account-keys-manage.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11：监视尝试访问已停用帐户的行为

**指导**：使用存储分析来记录成功和失败的存储服务请求的详细信息。 所有日志以块 Blob 的形式存储在一个名为 $logs 的容器中，为存储帐户启用存储分析时会自动创建该容器。

为 Azure Active Directory 用户帐户创建诊断设置，将审核日志和登录日志发送到 Log Analytics 工作区。 你可以在 Log Analytics 工作区中配置所需的警报。 若要监视针对 Azure 存储帐户进行的身份验证的失败情况，可以创建警报，以便在达到存储资源指标的某些阈值时收到通知。 也可使用 Azure Monitor，在出现针对存储帐户进行的匿名访问时根据匿名身份验证条件发出警报。

- [Azure 存储分析日志记录](./storage-analytics-logging.md)

- [如何将 Azure 活动日志集成到 Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [如何为 Azure 存储帐户配置指标警报](./storage-monitor-storage-account.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：帐户登录行为偏差的警报

**指南**：使用 Azure Active Directory 的风险和标识保护功能配置对检测到的与存储帐户资源相关的可疑操作的自动响应。 应通过 Azure Sentinel 启用自动响应，以实现组织的安全响应。 

- [如何查看 Azure AD 风险登录](../../active-directory/identity-protection/overview-identity-protection.md)

- [如何配置和启用标识保护风险策略](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [如何加入 Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持场合下为 Microsoft 提供对相关客户数据的访问权限

**指南**：在 Microsoft 需要访问客户数据的支持方案中，客户密码箱 (预览版用于存储帐户) 提供了一个界面，供客户查看和批准或拒绝客户数据访问请求。 Microsoft 不需要，也不会请求访问存储帐户中存储的组织的机密。

- [了解客户密码箱](../../security/fundamentals/customer-lockbox-overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="data-protection"></a>数据保护

有关详细信息，请参阅[安全控制：数据保护](../../security/benchmarks/security-control-data-protection.md)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指导**：使用标记可以帮助跟踪那些存储或处理敏感信息的 Azure 存储帐户资源。 

- [如何创建和使用标记](../../azure-resource-manager/management/tag-resources.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指导**：使用单独的订阅、管理组和存储帐户对各个安全域（例如环境、数据敏感度）实施隔离。  可以对存储帐户进行限制，以便根据所用网络的类型和子集来控制应用程序与企业环境所需的存储帐户访问级别。 配置网络规则后，仅通过指定网络组请求数据的应用程序才能访问存储帐户。 可以通过 Azure RBAC 来控制对 Azure 存储的访问。 你还可以配置专用终结点以提高安全性，因为虚拟网络与服务之间的流量将通过 Microsoft 主干网络，因此不会从公共 Internet 泄露。 

- [如何创建其他 Azure 订阅](../../cost-management-billing/manage/create-subscription.md)

- [如何创建管理组](../../governance/management-groups/create-management-group-portal.md)

- [如何创建和使用标记](../../azure-resource-manager/management/tag-resources.md)

- [配置 Azure 存储防火墙和虚拟网络](./storage-network-security.md)

- [虚拟网络服务终结点](../../virtual-network/virtual-network-service-endpoints-overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输。

**指导**：对于存储或处理敏感信息的存储帐户资源，请使用标记将这些资源标记为敏感。 为了减少通过外泄丢失数据的风险，请使用 Azure 防火墙限制 Azure 存储帐户的出站网络流量。 

另外，使用虚拟网络服务终结点策略可以通过服务终结点筛选发往 Azure 存储帐户的出口虚拟网络流量，让数据只能外泄到特定 Azure 存储帐户。

- [配置 Azure 存储防火墙和虚拟网络](../../virtual-network/virtual-network-service-endpoint-policies-overview.md)

- [Azure 存储的虚拟网络服务终结点策略](../../private-link/tutorial-private-endpoint-storage-portal.md)

- [了解 Azure 中的客户数据保护](../../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指导**：可以通过为存储帐户启用“需要安全传输”来强制使用 HTTPS。 启用此设置后，使用 HTTP 的连接将被拒绝。 另外，请使用 Azure 安全中心和 Azure Policy 为存储帐户强制实施安全传输。

- [如何在 Azure 存储中要求安全传输](./storage-require-secure-transfer.md)

- [通过安全中心监视的 Azure 安全策略](../../security-center/policy-reference.md)

**Azure 安全中心监视**：是

**责任**：共享

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指导**：数据标识功能尚不可用于 Azure 存储帐户和相关的资源。 如果需要出于合规性目的使用这些功能，请实施第三方解决方案。 

- [了解 Azure 中的客户数据保护](../../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 控制对资源的访问

**指导**：Azure Active Directory (Azure AD) 通过 Azure 基于角色的访问控制 (Azure RBAC) 授予对受保护资源的访问权限。 Azure 存储定义了一组内置的 Azure 角色，它们包含用于访问 Blob 或队列数据的通用权限集。 

- [如何为 Azure 存储帐户分配 Azure 角色](./storage-auth-aad-rbac-portal.md#assign-azure-roles-using-the-azure-portal)

- [使用 Azure 存储资源提供程序访问管理资源](./authorization-resource-provider.md)

- [如何在 Azure 门户中使用 Azure RBAC 配置对 Azure Blob 和队列数据的访问权限](./storage-auth-aad-rbac-portal.md)

- [如何创建和配置 AAD 实例](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [授予访问 Azure 存储中的数据的权限](./storage-auth.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用基于主机的数据丢失防护功能来强制实施访问控制

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指导**：将针对所有存储帐户启用 Azure 存储加密，但不能禁用加密。 在数据保存到云时，Azure 存储会自动加密数据。 从 Azure 存储读取数据时，Azure 存储会在返回数据之前将其解密。 使用 Azure 存储加密，无需修改代码或将代码添加到任何应用程序，就可以保护静态数据。 

- [了解 Azure 存储静态加密](./storage-service-encryption.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指导**：将 Azure Monitor 与 Azure 活动日志结合使用，以创建在存储帐户资源发生更改时发出的警报。 也可启用 Azure 存储日志记录以跟踪对 Azure 存储发出的每个请求的授权方式。 日志可指示请求是匿名提出的，还是使用 OAuth 2.0 令牌、共享密钥或共享访问签名 (SAS) 提出的。 也可使用 Azure Monitor，在出现针对存储帐户进行的匿名访问时根据匿名身份验证条件发出警报。

- [如何针对 Azure 活动日志事件创建警报](../../azure-monitor/platform/alerts-activity-log.md)

- [Azure 存储分析日志记录](./storage-analytics-logging.md)

- [如何为 Azure 存储帐户配置指标警报](./storage-monitor-storage-account.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="vulnerability-management"></a>漏洞管理

有关详细信息，请参阅[安全控制：漏洞管理。](../../security/benchmarks/security-control-vulnerability-management.md)

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：运行自动漏洞扫描工具

**指导**：按照 Azure 安全中心提供的建议，持续审核和监视存储帐户的配置。 

- [安全建议 - 参考指南](../../security-center/recommendations-reference.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自动操作系统修补管理解决方案

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署第三方自动软件修补管理解决方案

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比较连续进行的漏洞扫描

**指导**：不适用；Microsoft 对支持存储帐户的底层系统执行漏洞管理。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险分级流程确定所发现漏洞的修正优先级。

**指导**：使用 Azure 安全中心提供的默认风险评级（安全功能分数）。 

- [了解 Azure 安全中心安全功能分数](../../security-center/secure-score-security-controls.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="inventory-and-asset-management"></a>清单和资产管理

有关详细信息，请参阅[安全控制：清单和资产管理](../../security/benchmarks/security-control-inventory-asset-management.md)。

### <a name="61-use-azure-asset-discovery"></a>6.1：使用 Azure 资产发现

**指导**：使用 Azure Resource Graph 查询和发现订阅中的所有资源（包括存储帐户）。 确保你在租户中拥有适当的（读取）权限，并且可以枚举所有 Azure 订阅，以及订阅中的资源。 

- [如何使用 Azure Graph 创建查询](../../governance/resource-graph/first-query-portal.md)

- [如何查看 Azure 订阅](/powershell/module/az.accounts/get-azsubscription)

- [了解 Azure RBAC](../../role-based-access-control/overview.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指导**：将标记应用到存储帐户资源，以便有条理地将元数据组织成某种分类。 

- [如何创建和使用标记](../../azure-resource-manager/management/tag-resources.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指导**：使用标记、管理组和单独订阅（如果适用）来组织和跟踪 Azure 存储帐户和相关资源。 定期核对清单，确保及时地从订阅中删除未经授权的资源。 

此外，请使用 Azure 存储的高级威胁防护来检测未经授权的 Azure 资源。 

- [如何创建其他 Azure 订阅](../../cost-management-billing/manage/create-subscription.md)

- [如何创建管理组](../../governance/management-groups/create-management-group-portal.md)

- [如何创建和使用标记](../../azure-resource-manager/management/tag-resources.md)

- [配置 Azure 存储的高级威胁防护](./azure-defender-storage-configure.md?tabs=azure-portal)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4：维护已批准的 Azure 资源和软件标题的清单。

**指导**：你需要根据组织需求创建已批准的 Azure 资源的清单。 


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指南**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制： 

 - 不允许的资源类型 
 - 允许的资源类型 

此外，请使用 Azure Resource Graph 来查询/发现订阅中的资源。 这可以在基于高安全性的环境（例如具有存储帐户的环境）中提供帮助。 

- [如何配置和管理 Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Graph 创建查询](../../governance/resource-graph/first-query-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：监视计算资源中未批准的软件应用程序

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未批准的 Azure 资源和软件应用程序

**指导**：根据客户的公司政策，客户可以阻止通过 Azure Policy 来创建或使用资源的操作。 

- [如何配置和管理 Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="68-use-only-approved-applications"></a>6.8：仅使用已批准的应用程序

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指南**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制： 

- 不允许的资源类型 
- 允许的资源类型 

- [如何配置和管理 Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Policy 拒绝特定的资源类型](../../governance/policy/samples/index.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="610-implement-approved-application-list"></a>6.10：实施已批准的应用程序列表

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager-via-scripts"></a>6.11：限制用户通过脚本与 Azure 资源管理器进行交互的能力

**指导**：使用 Azure 条件访问，通过为“Microsoft Azure 管理”应用配置“阻止访问”，限制用户与 Azure 资源管理器进行交互的能力。 这可防止在高度安全的环境（例如，使用 Azure 存储帐户的环境）中创建和更改资源。 

- [如何配置条件访问以阻止访问 ARM](../../role-based-access-control/conditional-access-azure-management.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制用户在计算资源中执行脚本的能力

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以物理或逻辑方式隔离高风险应用程序

**指导**：不适用；此建议适用于 Azure 应用服务或计算资源上运行的 Web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="secure-configuration"></a>安全配置

有关详细信息，请参阅[安全控制：安全配置](../../security/benchmarks/security-control-secure-configuration.md)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指导**：在“Microsoft.Storage”命名空间中使用 Azure Policy 别名创建自定义策略，以审核或强制实施 Azure 存储帐户实例的配置。 你还可以使用内置的用于 Azure 存储帐户的 Azure Policy 定义，例如： 

审核对存储帐户的不受限的网络访问  
在存储帐户上部署高级威胁防护  
应将存储帐户迁移到新 Azure 资源管理器资源  
应该启用安全传输到存储帐户  

使用来自 Azure 安全中心的建议作为 Azure 存储帐户的安全配置基线。 

- [如何查看可用的 Azure Policy 别名](/powershell/module/az.resources/get-azpolicyalias)

- [如何配置和管理 Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7.2：为操作系统建立安全配置

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7.3：为所有 Azure 资源维护安全配置

**指导**：使用 Azure Policy“[拒绝]”和“[不存在则部署]”，对不同的 Azure 存储帐户资源强制实施安全设置。 

- [如何配置和管理 Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [了解 Azure Policy 效果](../../governance/policy/concepts/effects.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7.4：维护操作系统安全配置

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指导**：使用 Azure Repos 安全地存储和管理代码，如自定义 Azure 策略、Azure 资源管理器模板、Desired State Configuration 脚本等。若要访问在 Azure DevOps 中管理的资源，可以向特定用户、内置安全组或 Azure Active Directory (Azure AD)（如果与 Azure DevOps 集成）中定义的组或 Active Directory（如果与 TFS 集成）授予或拒绝授予权限。

- [如何在 Azure DevOps 中存储代码](/azure/devops/repos/git/gitworkflow)

- [关于 Azure DevOps 中的权限和组](/azure/devops/organizations/security/about-permissions)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全存储自定义操作系统映像

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="77-deploy-system-configuration-management-tools"></a>7.7：部署系统配置管理工具

**指导**：利用 Azure Policy 发出警报、执行审核和强制执行 Azure 存储帐户的系统配置。 另外，开发一个用于管理策略例外的流程和管道。 

- [如何配置和管理 Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：为操作系统部署系统配置管理工具

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：为 Azure 服务实现自动配置监视

**指导**：利用 Azure 安全中心对 Azure 存储帐户资源执行基线扫描。 

- [如何在 Azure 安全中心修正建议](../../security-center/security-center-remediate-recommendations.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：为操作系统实现自动配置监视

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="711-securely-manage-azure-secrets"></a>7.11：安全管理 Azure 机密

**指导**：Azure 存储在将数据保存到云时会自动加密数据。 可以使用 Microsoft 管理的密钥来加密存储帐户，也可使用自己的密钥来管理加密。 如果你使用客户提供的密钥，则可利用 Azure Key Vault 安全地存储密钥。 

此外，可以经常轮换存储帐户密钥，以限制丢失或泄露存储帐户密钥的影响。

- [适用于静态数据的 Azure 存储加密](./storage-service-encryption.md)

- [管理存储帐户访问密钥](./storage-account-keys-manage.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="712-securely-and-automatically-manage-identities"></a>7.12：安全自动管理标识

**指导**：使用 Azure Active Directory 和托管标识授权对 Azure 存储帐户中 Blob 和队列的访问权限。 Azure Blob 和队列存储支持使用 Azure 资源的托管标识进行 Azure Active Directory (Azure AD) 身份验证。 Azure 资源的托管标识可以从 Azure 虚拟机 (VM)、函数应用、虚拟机规模集和其他服务中运行的应用程序使用 Azure AD 凭据授权对 Blob 和队列数据的访问权限。 将 Azure 资源的托管标识与 Azure AD 身份验证结合使用，可避免将凭据随在云中运行的应用程序一起存储。 

- [如何使用托管标识授予对 Azure Blob 和队列数据的访问权限](./storage-auth-aad-rbac-portal.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指南**：实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。 

- [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="malware-defense"></a>恶意软件防护

有关详细信息，请参阅[安全控制：恶意软件防护](../../security/benchmarks/security-control-malware-defense.md)。

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反恶意软件

**指导**：不适用；此建议适用于计算资源。 Microsoft 会处理基础平台的反恶意软件。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预先扫描要上传到非计算 Azure 资源的文件

**指南**：对 azure 存储使用威胁防护，使用哈希信誉分析来检测恶意软件上传到 Azure 存储，并从活动 Tor 出口节点 (匿名 proxy) 进行可疑访问。 

你还可以在上传到非计算 Azure 资源（如应用服务、Data Lake Storage、Blob 存储等）之前预扫描恶意软件的任何内容，以满足组织的要求。

- [配置 Azure 存储的高级威胁防护](./azure-defender-storage-configure.md?tabs=azure-portal)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>步骤 8.3：确保反恶意软件和签名已更新

**指导**：不适用；此建议适用于计算资源。 Microsoft 会处理基础平台的反恶意软件。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="data-recovery"></a>数据恢复

有关详细信息，请参阅[安全控制：数据恢复](../../security/benchmarks/security-control-data-recovery.md)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

**指南**：始终自动复制 Microsoft Azure 存储帐户中的数据以确保持久性和高可用性。 Azure 存储功能会复制数据，以防范各种计划内和计划外的事件，包括暂时性的硬件故障、网络中断或断电、大范围自然灾害等。 可以选择在同一数据中心中、跨同一区域中的局域数据中心或跨地理上隔离的区域复制数据。 

还可以启用 Azure 自动化，以定期拍摄 blob 快照。

- [了解 Azure 存储冗余和服务级别协议](./storage-redundancy.md)

- [创建 blob 的快照](/rest/api/storageservices/creating-a-snapshot-of-a-blob)

- [Azure 自动化概述](../../automation/automation-intro.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥

**指导**：若要从 Azure 存储帐户支持的服务备份数据，可以使用多种方法，包括使用 azcopy 或第三方工具。 Azure Blob 存储的不可变存储可让用户以 WORM（一次写入，多次读取）状态存储业务关键型数据对象。 此状态可以根据用户指定的时间间隔使数据保持不可擦除且不可修改的状态。

- [AzCopy 入门](./storage-use-azcopy-v10.md)

- [为 Blob 存储设置和管理不可变性策略](../blobs/storage-blob-immutability-policies-manage.md?tabs=azure-portal)

可以使用 Azure CLI 或 PowerShell 在 Azure Key Vault 中备份客户管理/提供的密钥。 

- [如何在 Azure 中备份密钥保管库密钥](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指导**：使用以下 PowerShell 命令定期执行 Key Vault 证书、密钥、托管存储帐户和机密的数据还原： 

Restore-AzKeyVaultCertificate Restore-AzKeyVaultKey Restore-AzKeyVaultManagedStorageAccount Restore-AzKeyVaultSecret 

- [如何还原 Key Vault 证书](/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate)

- [如何还原 Key Vault 密钥](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

- [如何还原 Key Vault 托管存储帐户](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [如何还原 Key Vault 机密](/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret)

- [AzCopy 是一个命令行实用工具，可用于向/从存储帐户复制 blob、文件和表数据](./storage-use-azcopy-v10.md)

注意：若要从/向 Azure 表存储服务复制数据，请安装 AzCopy 7.3 版。


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指导**：若要在存储帐户上启用客户管理的密钥，必须使用 Azure 密钥保管库来存储密钥。 必须同时启用密钥保管库上的“软删除”和“不清除”属性 。 Key Vault 的软删除功能允许恢复已删除的保管库和保管库对象，例如密钥、机密和证书。 如果将 Azure 存储帐户数据备份到 Azure 存储 blob，请启用软删除以在 blob 或 blob 快照被删除时保存和恢复数据。 你应将备份视为敏感数据，并应用相关的访问和数据保护控制作为此基线的一部分。 另外，为了加强保护，可以 WORM（一次写入，多次读取）状态存储业务关键型数据对象。

- [如何使用 Azure Key Vault 软删除](../../key-vault/general/key-vault-recovery.md)

- [Azure 存储 Blob 的软删除](../blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [使用不可变的存储来存储业务关键型 Blob 数据](../blobs/storage-blob-immutable-storage.md)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="incident-response"></a>事件响应

有关详细信息，请参阅[安全控制：事件响应](../../security/benchmarks/security-control-incident-response.md)。

### <a name="101-create-incident-response-guide"></a>10.1：创建事件响应指南

**指导**：为组织制定事件响应指南。 确保在书面的事件响应计划中定义人员职责，以及事件处理/管理从检测到事件后审查的各个阶段。

- [关于建立自己的安全事件响应流程的指南](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 安全响应中心事件分析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [客户还可以利用 NIST 的“计算机安全事件处理指南”来制定他们自己的事件响应计划](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级确定过程

**指导**：安全中心为每条警报分配严重性，以帮助你优先处理应该最先调查的警报。 严重性取决于安全中心在发出警报时所依据的检测结果和分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。 

此外，请用标记明确标记订阅（例如 生产、非生产）并创建命名系统来对 Azure 资源进行明确标识和分类，特别是处理敏感数据的资源。 你的责任是根据发生事件的 Azure 资源和环境的关键性确定修正警报的优先级。

- [Azure 安全中心中的安全警报](../../security-center/security-center-alerts-overview.md)

- [使用标记整理 Azure 资源](../../azure-resource-manager/management/tag-resources.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指南**：定期执行演练来测试系统的事件响应功能，以帮助保护 Azure 资源。 识别弱点和差距，并根据需要修改计划。

- [NIST 发布 - IT 计划和功能的测试、训练和演练计划指南](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指导**：如果 Microsoft 安全响应中心 (MSRC) 发现数据被某方非法访问或未经授权访问，Microsoft 会使用安全事件联系信息联系用户。 事后审查事件，确保问题得到解决。

- [如何设置 Azure 安全中心安全联系人](../../security-center/security-center-provide-security-contact-details.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指南**：使用连续导出功能导出 Azure 安全中心警报和建议，以帮助确定 Azure 资源的风险。 使用连续导出可以手动导出或者持续导出警报和建议。 可以使用 Azure 安全中心数据连接器将警报流式传输到 Azure Sentinel。

- [如何配置连续导出](../../security-center/continuous-export.md)

- [如何将警报流式传输到 Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指导**：使用 Azure 安全中心内的工作流自动化功能，通过“逻辑应用”针对安全警报和建议自动触发响应，以保护 Azure 资源。

- [如何配置工作流自动化和逻辑应用](../../security-center/workflow-automation.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

有关详细信息，请参阅[安全控制：渗透测试和红队演练](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources"></a>11.1：定期对 Azure 资源执行渗透测试

**指导**：请遵循 Microsoft 互动规则，确保你的渗透测试不违反 Microsoft 政策。 使用 Microsoft 红队演练策略和执行，以及针对 Microsoft 托管云基础结构、服务和应用程序执行的现场渗透测试。

- [参与的渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="next-steps"></a>后续步骤

- 参阅 [Azure 安全基准](../../security/benchmarks/overview.md)
- 详细了解 [Azure 安全基线](../../security/benchmarks/security-baselines-overview.md)