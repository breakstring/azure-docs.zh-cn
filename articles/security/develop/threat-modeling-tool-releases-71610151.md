---
title: Microsoft Threat Modeling Tool 版本 2019/10/16
description: 阐述 Threat Modeling Tool 7.1.61015.1 版的发行说明。
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 10/16/2019
ms.openlocfilehash: 761ad45496e799a555b60480ff575b9d8f30e984
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/11/2020
ms.locfileid: "94516979"
---
# <a name="threat-modeling-tool-update-release-71610151---10162019"></a>Threat Modeling Tool 更新版 7.1.61015.1 - 2019/10/16

Microsoft Threat Modeling Tool (TMT) 的版本 7.1.61015.1 已于 2019 年 10 月 16 日发布，其中包含以下更改：

- 辅助功能改进
- Bug 修复
- 适用于 Azure 逻辑应用和 Azure 数据资源管理器的新模具

## <a name="notable-bug-fixes"></a>重要的 bug 修复

### <a name="improved-backward-compatibility-with-files-created-in-threat-modeling-tool-2016"></a>提高了与 Threat Modeling Tool 2016 中创建的文件的后向兼容性

修复了与在 Threat Modeling Tool 2016 中创建的威胁模型文件的打开或显示相关的几个 bug。

## <a name="feature-enhancements"></a>功能增强

### <a name="new-stencils-for-azure-logic-apps-and-azure-data-explorer"></a>适用于 Azure 逻辑应用和 Azure 数据资源管理器的新模具

已将适用于 Azure 逻辑应用和 Azure 数据资源管理器的新模具及其相关威胁和缓解措施添加到 Azure Stencil。

![Azure 逻辑应用和Azure 数据资源管理器模具](./media/threat-modeling-tool-releases-71610151/tmt-logic-apps.png)

## <a name="known-issues"></a>已知问题

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>与预期范围外的优先级值相关的错误

一些客户反馈说，打开 Threat Modeling Tool 2016 或自定义模板中创建的文件时收到以下错误消息：

```output
System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()

System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)
```

我们正在调查此问题

## <a name="system-requirements"></a>系统要求

- 支持的操作系统
  - [Microsoft Windows 10 周年更新](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97)或更高版本
- 所需的 .NET 版本
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) 或更高版本
- 其他要求
  - 需要建立 Internet 连接才能接收工具和模板的更新。

## <a name="documentation-and-feedback"></a>文档和反馈

- Threat Modeling Tool 的文档位于 [docs.microsoft.com](./threat-modeling-tool.md)，其中包含[有关如何使用该工具](./threat-modeling-tool-getting-started.md)的信息。

## <a name="next-steps"></a>后续步骤

下载最新版本的 [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)。