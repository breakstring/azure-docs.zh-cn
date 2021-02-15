---
title: Azure 安全控制 - 日志记录和监视
description: Azure 安全控制 - 日志记录和监视
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 8492e986b378ea19f8c3c4cb7f94fcb0f155b427
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96487841"
---
# <a name="security-control-logging-and-monitoring"></a>安全控制：日志记录和监视

安全日志记录和监视侧重于与为 Azure 服务启用、获取和存储审核日志相关的活动。

## <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用批准的时间同步源

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 2.1 | 6.1 | Microsoft |

Microsoft 维护 Azure 资源的时间源，但是，你可以选择管理计算资源的时间同步设置。

- [如何为 Azure Windows 计算资源配置时间同步](../../virtual-machines/windows/time-sync.md)

- [如何为 Azure Linux 计算资源配置时间同步](../../virtual-machines/linux/time-sync.md)

## <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 2.2 | 6.5、6.6 | 客户 |

通过 Azure Monitor 引入日志，以聚合终结点设备、网络资源和其他安全系统生成的安全数据。 在 Azure Monitor 中，使用 Log Analytics 工作区来查询和执行分析，并使用 Azure 存储帐户进行长期/存档存储。

或者，可以启用将数据加入 Azure Sentinel 或第三方 SIEM 的功能。 

- [如何加入 Azure Sentinel](../../sentinel/quickstart-onboard.md)

- [如何使用 Azure Monitor 收集平台日志和指标](../../azure-monitor/platform/diagnostic-settings.md)

- [如何使用 Azure Monitor 收集 Azure 虚拟机内部主机日志](../../azure-monitor/learn/quick-collect-azurevm.md)

- [如何开始使用 Azure Monitor 和第三方 SIEM 集成](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

## <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 2.3 | 6.2、6.3 | 客户 |

在 Azure 资源上启用诊断设置，以访问审核、安全和诊断日志。 活动日志自动可用，包括事件源、日期、用户、时间戳、源地址、目标地址和其他有用元素。

- [如何使用 Azure Monitor 收集平台日志和指标](../../azure-monitor/platform/diagnostic-settings.md)

- [了解 Azure 中的日志记录和不同的日志类型](../../azure-monitor/platform/platform-logs-overview.md)

## <a name="24-collect-security-logs-from-operating-systems"></a>2.4：从操作系统收集安全日志

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 2.4 | 6.2、6.3 | 客户 |

如果计算资源归 Microsoft 所有，则 Microsoft 负责监视它。 如果计算资源归你的组织所有，则由你负责监视。 可以使用 Azure 安全中心监视 OS。 安全中心从操作系统收集的数据包括 OS 类型和版本、OS（Windows 事件日志）、正在运行的进程、计算机名称、IP 地址和登录用户。 Log Analytics 代理还会收集故障转储文件。

- [如何使用 Azure Monitor 收集 Azure 虚拟机内部主机日志](../../azure-monitor/learn/quick-collect-azurevm.md)

- [了解 Azure 安全中心数据收集](../../security-center/security-center-enable-data-collection.md)

## <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 2.5 | 6.4 | 客户 |

在 Azure Monitor 中，根据组织的合规性规章设置 Log Analytics 工作区保留期。 使用 Azure 存储帐户进行长期/存档存储。

- [更改 Log Analytics 中的数据保留期](../../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [如何为 Azure 存储帐户日志配置保留策略](../../storage/common/storage-monitor-storage-account.md#configure-logging)

## <a name="26-monitor-and-review-logs"></a>2.6：监视和审查日志

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 2.6 | 6.7 | 客户 |

分析和监视日志中的异常行为，并定期查看结果。 使用 Azure Monitor 的 Log Analytics 工作区查看日志并对日志数据执行查询。

或者，可以启用数据并将其加入 Azure Sentinel 或第三方 SIEM。 

- [如何加入 Azure Sentinel](../../sentinel/quickstart-onboard.md)

- [了解 Log Analytics 工作区](../../azure-monitor/log-query/log-analytics-tutorial.md)

- [如何在 Azure Monitor 中执行自定义查询](../../azure-monitor/log-query/get-started-queries.md)

## <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 2.7 | 6.8 | 客户 |

使用 Azure 安全中心和 Log Analytics 工作区监视安全日志和事件中的异常活动并发出警报。

或者，可以启用数据并将其载入 Azure Sentinel。

- [如何加入 Azure Sentinel](../../sentinel/quickstart-onboard.md)

- [如何在 Azure 安全中心管理警报](../../security-center/security-center-managing-and-responding-alerts.md)

- [如何针对 Log Analytics 日志数据发出警报](../../azure-monitor/learn/tutorial-response.md)

## <a name="28-centralize-anti-malware-logging"></a>2.8：集中管理反恶意软件日志记录

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 2.8 | 8.6 | 客户 |

为 Azure 虚拟机和云服务启用反恶意软件事件集合。

- [如何为虚拟机配置 Microsoft Antimalware](/powershell/module/servicemanagement/azure.service/set-azurevmmicrosoftantimalwareextension?view=azuresmps-4.0.0)

- [如何为云服务配置 Microsoft Antimalware](/powershell/module/servicemanagement/azure.service/set-azureserviceantimalwareextension?view=azuresmps-4.0.0)

- [了解 Microsoft Antimalware](../fundamentals/antimalware.md)

## <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 2.9 | 8.7 | 客户 |

根据组织的需求，从 Azure 市场实现 DNS 日志记录解决方案的第三方解决方案。  

## <a name="210-enable-command-line-audit-logging"></a>2.10：启用命令行审核日志记录

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 2.10 | 8.8 | 客户 |

在所有受支持的 Azure Windows 虚拟机上使用 Microsoft Monitoring Agent 来记录进程创建事件和命令行字段。   对于受支持的 Azure Linux 虚拟机，可以手动配置每个节点的控制台日志记录，并使用 Syslog 来存储数据。  同时，使用 Azure Monitor 的 Log Analytics 工作区查看日志并对 Azure 虚拟机中记录的数据执行查询。 

- [Azure 安全中心中的数据收集](../../security-center/security-center-enable-data-collection.md#data-collection-tier)

- [如何在 Azure Monitor 中执行自定义查询](../../azure-monitor/log-query/get-started-queries.md)

- [Azure Monitor 中的 Syslog 数据源](../../azure-monitor/platform/data-sources-syslog.md)


## <a name="next-steps"></a>后续步骤

- 请参阅下一个安全控制：[标识和访问控制](security-control-identity-access-control.md)