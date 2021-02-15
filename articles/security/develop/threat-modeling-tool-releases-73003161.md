---
title: Microsoft Threat Modeling Tool 版本 2020/03/22 - Azure
description: 阐述 Threat Modeling Tool 7.3.00316.1 版的发行说明。
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 03/22/2020
ms.openlocfilehash: e182d40d20529b5743fa9105c68108a8ae55d943
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/11/2020
ms.locfileid: "94516894"
---
# <a name="threat-modeling-tool-update-release-73003161---03222020"></a>Threat Modeling Tool 更新版 7.3.00316.1 - 2020/03/22

Microsoft Threat Modeling Tool (TMT) 的版本 7.3.00316.1 已于 2020 年 3 月 22 日发布，其中包含以下更改：

- 辅助功能改进
- Bug 修复
- 新增 DiagramReader 功能

## <a name="notable-bug-fixes"></a>重要的 bug 修复

### <a name="exporting-the-threat-list-to-csv"></a>将威胁列表导出到 CSV

“导出到 CSV”功能在选择要从威胁列表中导出的具体字段时不一致。 现在，威胁列表中的所有字段都将导出到 CSV 文件中。 

### <a name="ux-bugs"></a>UX bug

- 主工作流中的帮助菜单（创建/打开/分析）与模板编辑器体验现在具有一致的菜单选项。
- “模具”窗格中的搜索栏现在有一个标准光标，并添加了相应标签。

## <a name="new-features"></a>新增功能

### <a name="diagramreader-feature-has-been-added"></a>添加了 DiagramReader 功能

当模型打开时，主菜单中添加了新的 DiagramReader 功能。 此功能会将模型的图形表示形式转换为基于文本的叙述。 

## <a name="system-requirements"></a>系统要求

- 支持的操作系统：
  - [Microsoft Windows 10 周年更新](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97)或更高版本
- 所需的 .NET 版本：
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) 或更高版本
- 其他要求：
  - 一个 Internet 连接，用于接收工具和模板的更新

## <a name="documentation-and-feedback"></a>文档和反馈

- Threat Modeling Tool 的文档位于 [docs.microsoft.com](./threat-modeling-tool.md)，其中包含[有关如何使用该工具](./threat-modeling-tool-getting-started.md)的信息。

## <a name="next-steps"></a>后续步骤

下载最新版本的 [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)。