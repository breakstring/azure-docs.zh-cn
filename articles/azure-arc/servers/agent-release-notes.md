---
title: 启用 Azure Arc 服务器代理的新增功能
description: 本文提供了适用于 Azure Arc 的服务器代理的发行说明。 对于许多汇总问题，还提供了更多详细信息的链接。
ms.topic: conceptual
ms.date: 12/21/2020
ms.openlocfilehash: 4b7fd97d5b7e1cddc1d78e0d24205890e74c45c1
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/22/2020
ms.locfileid: "97722515"
---
# <a name="whats-new-with-azure-arc-enabled-servers-agent"></a>启用 Azure Arc 服务器代理的新增功能

已启用 Azure Arc 的服务器连接的计算机代理不断地获得改进。 为了让大家随时了解最新的开发成果，本文将提供以下方面的信息：

- 最新版本
- 已知问题
- Bug 修复

## <a name="december-2020"></a>2020 年 12 月

版本：1。3

### <a name="new-feature"></a>新功能

添加了对 Windows Server 2008 R2 的支持

### <a name="fixed"></a>固定

已解决的问题导致无法成功安装 Linux 上的自定义脚本扩展。

## <a name="november-2020"></a>2020 年 11 月

版本：1。2

### <a name="fixed"></a>固定

解决了在基于 RPM 的分发上升级后，代理配置可能丢失的问题。

## <a name="october-2020"></a>2020 年 10 月

版本：1.1

### <a name="fixed"></a>固定

- 修复了用于处理备用 GC daemon 单元文件位置的代理脚本。
- GuestConfig 代理可靠性更改。
- US Gov 弗吉尼亚州区域的 GuestConfig 代理支持。
- 在出现故障时，GuestConfig 代理扩展报告消息更详细。

## <a name="september-2020"></a>2020 年 9 月

版本： 1.0 (公开上市) 

### <a name="plan-for-change"></a>更改计划

- 在将来的服务更新中，支持预览代理 (低于 1.0) 的所有版本。
- 已删除对回退终结点的支持 `.azure-automation.net` 。 如果有代理，则需要允许使用终结点 `*.his.arc.azure.com` 。
- 如果在 Azure 中托管的虚拟机上安装了已连接的计算机代理，则无法从启用了 Arc 的服务器资源安装或修改 VM 扩展。 这是为了避免从虚拟机的 **HybridCompute** 资源 **执行的扩展** 操作冲突。 对于所有扩展操作，请使用计算机的 **计算** 资源。
- 来宾配置过程的名称已更改，从 *gcd* 到 *gcad* on Linux， *gcservice* 到 *gcarcservice* on Windows。

### <a name="new-feature"></a>新功能

- 添加 `azcmagent logs` 了用于收集支持信息的选项。
- 添加了 `azcmagent license` 显示 EULA 的选项。
- 添加了 `azcmagent show --json` 以轻松不可解析格式输出代理状态的选项。
- 在输出中添加了标志 `azcmagent show` ，用于指示服务器是否在托管在 Azure 中的虚拟机上。
- 添加了 `azcmagent disconnect --force-local-only` 允许在无法访问 Azure 服务时重置本地代理状态的选项。
- 添加了 `azcmagent connect --cloud` 支持其他云的选项。 在此版本中，在代理发布时，服务仅支持 Azure。
- 代理已本地化为 Azure 支持的语言。

### <a name="fixed"></a>固定

- 对连接检查的改进。
- 更正了在 Linux 上升级代理时丢失的代理服务器设置问题。
- 尝试在运行 Windows Server 2012 R2 的服务器上安装代理时解决了问题。
- 扩展安装可靠性改进

## <a name="august-2020"></a>2020 年 8 月

版本：0.11

- 此版本以前发布了对 Ubuntu 20.04 的支持。 由于某些 Azure VM 扩展不支持 Ubuntu 20.04，因此将删除对此版本 Ubuntu 的支持。

- 扩展部署的可靠性改进。

### <a name="known-issues"></a>已知问题

如果你使用的是较旧版本的 Linux 代理并且其配置为使用代理服务器，则需要在升级后重新配置代理服务器设置。 要执行此操作，请运行 `sudo azcmagent_proxy add http://proxyserver.local:83`。

## <a name="next-steps"></a>后续步骤

在为多台混合计算机评估或启用已启用 Arc 的服务器之前，请查看[连接的计算机代理概述](agent-overview.md)，了解要求、有关代理的技术详细信息以及部署方法。