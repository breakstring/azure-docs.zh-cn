---
title: 在本地调试 Azure Data Lake Analytics 代码
description: 了解如何在本地工作站上使用针对 Visual Studio 的 Azure Data Lake 工具来调试 U-SQL 作业。
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 07/03/2018
ms.openlocfilehash: 1c7218deac9efba6df6c1284f2578a744e768284
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/20/2020
ms.locfileid: "92221020"
---
# <a name="debug-azure-data-lake-analytics-code-locally"></a>在本地调试 Azure Data Lake Analytics 代码

与在 Azure Data Lake Analytics 服务中一样，可以使用针对 Visual Studio 的 Azure Data Lake 工具在本地工作站上运行和调试 Azure Data Lake Analytics 代码。

了解如何 [在本地计算机上运行 SQL 脚本](data-lake-analytics-data-lake-tools-local-run.md)。

## <a name="debug-scripts-and-c-assemblies-locally"></a>在本地调试脚本和 C# 程序集

无需将 C# 程序集提交并注册到 Azure Data Lake Analytics 服务即可对其进行调试。 可以同时在代码隐藏文件和引用的 C# 项目中设置断点。

### <a name="debug-local-code-in-a-code-behind-file"></a>在代码隐藏文件中调试本地代码

1. 在代码隐藏文件中设置断点。
2. 选择 F5**** 在本地调试脚本。

> [!NOTE]
   > 以下过程仅适用于 Visual Studio 2015。 在旧版 Visual Studio 中，可能需要手动添加 PDB**** 文件。  
   >
   >

### <a name="debug-local-code-in-a-referenced-c-project"></a>在引用的 C# 项目中调试本地代码

1. 创建 C# 程序集项目，构建该项目以生成输出 DLL**** 文件。
2. 使用 U-SQL 语句注册该 DLL**** 文件：

   ```sql
   CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
   ```
   
3. 在 C# 代码中设置断点。
4. 选择 F5****，通过在本地引用 C# DLL**** 文件来调试脚本。


## <a name="next-steps"></a>后续步骤

- 有关更复杂的查询示例，请参阅[使用 Azure Data Lake Analytics 分析网站日志](data-lake-analytics-analyze-weblogs.md)。
- 若要查看作业详细信息，请参阅 [使用作业浏览器和作业视图 Azure Data Lake Analytics 作业](data-lake-analytics-data-lake-tools-view-jobs.md)。
- 若要使用顶点执行视图，请参阅 [在 Data Lake Tools For Visual Studio 中使用顶点执行视图](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)。
