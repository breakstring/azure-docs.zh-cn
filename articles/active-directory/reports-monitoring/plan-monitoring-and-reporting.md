---
title: '& 监视部署规划报表-Azure AD'
description: 介绍如何计划和执行报表和监视的实现。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: baselden
ms.reviewer: plenzke
ms.collection: M365-identity-device-management
ms.openlocfilehash: 094ecd88c8b493d44b756d03d700b43cbcba1ee9
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/22/2020
ms.locfileid: "92362393"
---
# <a name="plan-an-azure-active-directory-reporting-and-monitoring-deployment"></a>规划 Azure Active Directory 报告和监视部署

) 报表和监视解决方案的 Azure Active Directory (Azure AD 取决于你的法律、安全和运营要求以及你的现有环境和过程。 本文介绍各种设计选项，并指导您正确部署策略。

### <a name="benefits-of-azure-ad-reporting-and-monitoring"></a>Azure AD 报告和监视的优点

Azure AD 报表提供环境中 Azure AD 活动的综合视图和日志，包括登录事件、审核事件和对目录所做的更改。

可以将提供的数据用于：

* 确定如何使用你的应用和服务。

* 检测影响环境运行状况的潜在风险。

* 排查阻止用户完成其工作的问题。

* 查看对 Azure AD 目录的更改的审核事件获取见解。

> [!IMPORTANT]
> Azure AD 监视使你能够将 Azure AD 报告生成的日志路由到不同的目标系统。 然后，可以将其保存以供长期使用，或者将其与第三方安全信息和事件管理 (SIEM) 工具集成，以便获取有关环境的见解。

利用 Azure AD 监视，你可以将日志路由到：

* 用于存档的 Azure 存储帐户。
* Azure Monitor 日志（以前称为 Azure Log Analytics 工作区），您可以在其中分析数据、创建仪表板，并对特定事件发出警报。
* 一个 Azure 事件中心，你可以在其中与现有的 SIEM 工具（如 Splunk、Sumologic 或 QRadar）集成。

> [!NOTE]
最近开始使用术语 Azure Monitor 日志，而不是 Log Analytics。 日志数据仍然存储在 Log Analytics 工作区中，并仍然由同一 Log Analytics 服务收集并分析。 我们正在更新术语，以便更好地反映 [Azure Monitor 中的日志](../../azure-monitor/platform/data-platform.md)的角色。 有关详细信息，请参阅 [Azure Monitor 术语更改](../../azure-monitor/terminology.md)。

[详细了解报告保留策略](./reference-reports-data-retention.md)。

### <a name="licensing-and-prerequisites-for-azure-ad-reporting-and-monitoring"></a>Azure AD 报告和监视的许可和先决条件

需要 Azure AD premium 许可证才能访问 Azure AD 登录日志。

[Azure Active Directory 定价指南](https://azure.microsoft.com/pricing/details/active-directory/)中的详细功能和许可信息。

若要部署 Azure AD 监视和报告，你将需要成为 Azure AD 租户的全局管理员或安全管理员的用户。

根据日志数据的最终目标，需要以下内容之一：

* 你对其拥有 ListKeys 权限的 Azure 存储帐户。 建议使用常规存储帐户而非 Blob 存储帐户。 有关存储定价信息，请参阅 [Azure 存储定价计算器](https://azure.microsoft.com/pricing/calculator/?service=storage)。

* 与第三方 SIEM 解决方案集成的 Azure 事件中心命名空间。

* 用于将日志发送到 Azure Monitor 日志的 Azure Log Analytics 工作区。

## <a name="plan-an-azure-reporting-and-monitoring-deployment-project"></a>规划 Azure 报表和监视部署项目

在此项目中，你将定义将使用和监视报表的受众，并定义 Azure AD 监视体系结构。

### <a name="engage-the-right-stakeholders"></a>让合适的利益干系人参与

当技术项目失败时，失败的原因往往是对影响、结果和责任的预期不符。 若要避免这些问题，请 [确保您参与到了正确的利益干系人](../fundamentals/active-directory-deployment-plans.md)。 还要确保通过记录利益干系人及其项目输入和责任，充分了解项目中的利益干系人角色。

### <a name="plan-communications"></a>规划沟通

沟通对于任何新服务的成功都至关重要。 主动与用户交流他们的体验将如何变化、何时会变化以及在遇到问题时如何获取支持。

### <a name="document-your-current-infrastructure-and-policies"></a>记录你当前的基础结构和策略

你当前的基础结构和策略将推动你的报表和监视设计。 确保你知道

* 你使用的 SIEM 工具（如果有）。

* 你的 Azure 基础结构，包括现有的存储帐户和正在使用的监视。

* 针对日志的组织保留策略，包括所需的任何适用的符合性框架。 

## <a name="plan-an-azure-ad-reporting-and-monitoring-deployment"></a>规划 Azure AD 报告和监视部署

报告和监视用于满足你的业务需求，深入了解使用模式，并提高组织的安全状况。

### <a name="business-use-cases"></a>业务用例

* 满足业务需求的解决方案需要
* 需要满足业务需求
* 不适用

|区域 |说明 |
|-|-|
|保留| **日志保留期超过30天**。 由于法律或业务要求，需要将审核日志和登录日志 Azure AD 超过30天。 |
|Analytics| **日志需要可搜索**。 存储的日志需要能够与分析工具一起搜索。 |
| 操作见解| **各种团队的见解**。 需要为不同的用户提供访问权限，以获取 operational insights，如应用程序使用情况、登录错误、自助服务使用、趋势等。 |
| 安全见解| **各种团队的见解**。 需要为不同的用户提供访问权限，以获取 operational insights，如应用程序使用情况、登录错误、自助服务使用情况、趋势等。 |
| SIEM 系统中的集成      | **SIEM 集成**。 需要将 Azure AD 登录日志和审核日志集成并流式传输到现有的 SIEM 系统。 |

### <a name="choose-a-monitoring-solution-architecture"></a>选择监视解决方案体系结构

使用 Azure AD 监视，你可以将 Azure AD 活动日志路由到最能满足你的业务需求的系统。 然后，你可以保留它们用于长期报告和分析，以深入了解你的环境，并将其与 SIEM 工具集成。

#### <a name="decision-flow-chartan-image-showing-what-is-described-in-subsequent-sections"></a>决策流程图![显示后续部分中所述内容的图像](media/reporting-deployment-plan/deploy-reporting-flow-diagram.png)

#### <a name="archive-logs-in-a-storage-account"></a>在存储帐户中存档日志

通过将日志路由到 Azure 存储帐户，你可以将其保留的时间超过 [保留策略](./reference-reports-data-retention.md)中所述的默认保留期。 如果需要对日志进行存档，但不需要将其与 SIEM 系统集成，则可以使用此方法，并且无需进行持续的查询和分析。 你仍可以按需搜索。

了解如何[将数据路由到存储帐户](./quickstart-azure-monitor-route-logs-to-storage-account.md)。

#### <a name="send-logs-to-azure-monitor-logs"></a>将日志发送到 Azure Monitor 日志

[Azure Monitor 日志](../../azure-monitor/log-query/log-query-overview.md) 合并了不同源中的监视数据。 它还提供了查询语言和分析引擎，可让你深入了解应用程序的操作和资源的使用情况。 通过将 Azure AD 活动日志发送到 Azure Monitor 日志，可以快速检索、监视和警报收集的数据。 如果没有要直接将数据发送到的现有 SIEM 解决方案，但又想要查询和分析，请使用此方法。 数据 Azure Monitor 日志中后，可以将其发送到事件中心，并将其发送到 SIEM （如果需要）。

了解如何[将数据发送到 Azure Monitor 日志](./howto-integrate-activity-logs-with-log-analytics.md)。

你还可以为 Azure AD 活动日志安装预生成的视图，以监视涉及登录和审核事件的常见方案。

了解如何[安装和使用用于 Azure AD 活动日志的 Log Analytics 视图](./howto-install-use-log-analytics-views.md)。

#### <a name="stream-logs-to-your-azure-event-hub"></a>将日志流式传输到 Azure 事件中心

通过将日志路由到 Azure 事件中心，可与第三方 SIEM 工具集成。 可以通过此集成将 Azure AD 活动日志数据与其他由 SIEM 托管的数据组合起来，获取更丰富的有关环境的见解。 

了解如何[将日志流式传输到事件中心](./tutorial-azure-monitor-stream-logs-to-event-hub.md)。

## <a name="plan-operations-and-security-for-azure-ad-reporting-and-monitoring"></a>规划 Azure AD 报告和监视的操作和安全性

利益干系人需要访问 Azure AD 日志以获取 operational insights。 可能的用户包括安全团队成员、内部或外部审计员以及标识和访问管理操作团队。

Azure AD 角色使你能够根据你的角色委托配置和查看 Azure AD 报表的功能。 确定组织中的哪些人员需要读取 Azure AD 报表的权限以及适用于这些报表的角色。 

以下角色可以读取 Azure AD 报表：

* 全局管理员

* 安全管理员

* 安全读取者

* 报表读取者

详细了解 [Azure AD 管理角色](../roles/permissions-reference.md)。

*始终应用最小特权的概念以降低帐户泄露的风险*。 请考虑实施 [Privileged Identity Management](../privileged-identity-management/pim-configure.md) ，进一步保护你的组织。

##  

## <a name="deploy-azure-ad-reporting-and-monitoring"></a>部署 Azure AD 报告和监视

根据之前使用以上设计指南做出的决策，本部分将指导你了解不同的部署选项。

### <a name="consume-and-archive-azure-ad-logs"></a>使用和存档 Azure AD 日志

[在 Azure 门户中查找活动报告](./howto-find-activity-reports.md)

[将 Azure AD 日志存档到 Azure 存储帐户](./quickstart-azure-monitor-route-logs-to-storage-account.md)

### <a name="implement-monitoring-and-analytics"></a>实现监视和分析

[将日志发送到 Azure Monitor](./howto-integrate-activity-logs-with-log-analytics.md)

[安装和使用用于 Azure Active Directory 的日志分析视图](./howto-install-use-log-analytics-views.md)

[使用 Azure Monitor 日志分析 Azure AD 活动日志](./howto-analyze-activity-logs-log-analytics.md)

* [解释 Azure Monitor 中的审核日志架构](./reference-azure-monitor-audit-log-schema.md)

* [解释 Azure Monitor 中的登录日志架构](./reference-azure-monitor-sign-ins-log-schema.md)

 * [将 Azure AD 日志流式传输到 Azure 事件中心](./tutorial-azure-monitor-stream-logs-to-event-hub.md)

* [使用 Azure Monitor 将 Azure AD 日志与 Splunk 集成](./howto-integrate-activity-logs-with-splunk.md)

* [使用 Azure Monitor 将 Azure AD 日志与 SumoLogic 集成](./howto-integrate-activity-logs-with-sumologic.md)

 

 

## <a name="next-steps"></a>后续步骤

考虑实现 [Privileged Identity Management](../privileged-identity-management/pim-configure.md) 

请考虑实现 azure [RBAC)  (基于角色的访问控制 ](../../role-based-access-control/overview.md)