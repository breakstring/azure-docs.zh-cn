---
title: 在 Azure 应用服务中为 .NET 应用启用 Snapshot Debugger | Microsoft Docs
description: 在 Azure 应用服务中为 .NET 应用启用快照调试器
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 03/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 421f80493a9cb88e8bbbddc06aa9a24042b64b17
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/19/2020
ms.locfileid: "97695469"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>在 Azure 应用服务中为 .NET 应用启用快照调试器

快照调试器当前适用于按 Windows 服务计划在 Azure 应用服务上运行的 ASP.NET 和 ASP.NET Core 应用。 建议在使用快照调试程序时，在基本服务层或更高级别上运行应用程序。 对于大多数应用程序，免费和共享服务层没有足够的内存或磁盘空间来保存快照。

## <a name="enable-snapshot-debugger"></a><a id="installation"></a> 启用 Snapshot Debugger
若要为应用启用快照调试器，请遵循下面的说明。

如果运行的是其他类型的 Azure 服务，请参阅以下说明，了解如何在其他受支持的平台上启用 Snapshot Debugger：
* [Azure Function](snapshot-debugger-function-app.md?toc=/azure/azure-monitor/toc.json)
* [Azure 云服务](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric 服务](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure 虚拟机和虚拟机规模集](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [本地虚拟机或物理计算机](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

> [!NOTE]
> 如果使用的是 .NET Core 预览版本或应用程序引用 Application Insights SDK 直接或间接通过依赖程序集引用，请先按照为 [其他环境启用 Snapshot Debugger](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) 中的说明进行操作，以在应用程序中添加 [microsoft.applicationinsights.snapshotcollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 包，然后完成下面的说明。 

Snapshot Debugger 作为应用服务运行时的一部分预安装，但你需要将其打开，以便为你的应用服务应用获取快照。

部署应用后，请按照以下步骤启用快照调试程序：

1. 导航到应用服务的 Azure 控制面板。
2. 转到“设置”>“Application Insights”页面。

   ![在应用服务门户上启用 App Insights](./media/snapshot-debugger/applicationinsights-appservices.png)

3. 按页面中的说明创建新资源，或者选择现有 App Insights 资源，以便监视应用。 另外，请确保快照调试器的两个开关都为“开”  。

   ![添加 App Insights 站点扩展][Enablement UI]

4. 现已使用应用服务应用设置启用了快照调试器。

    ![快照调试器的应用设置][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>禁用快照调试器

执行与 **启用快照调试器** 相同的步骤，但将快照调试器的两个开关都切换到“关”  。

建议你在所有应用上都启用了 Snapshot Debugger，以方便诊断应用程序异常。

## <a name="azure-resource-manager-template"></a>Azure Resource Manager 模板

对于 Azure App Service，可以在 Azure 资源管理器模板中设置应用设置，以启用 Snapshot Debugger 和探查器，请参阅以下模板代码片段：

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('webSiteName')]",
  "type": "Microsoft.Web/sites",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  "tags": { 
    "[concat('hidden-related:', resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName')))]": "empty",
    "displayName": "Website"
  },
  "properties": {
    "name": "[parameters('webSiteName')]",
    "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
  },
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "name": "appsettings",
      "type": "config",
      "dependsOn": [
        "[parameters('webSiteName')]",
        "[concat('AppInsights', parameters('webSiteName'))]"
      ],
      "properties": {
        "APPINSIGHTS_INSTRUMENTATIONKEY": "[reference(resourceId('Microsoft.Insights/components', concat('AppInsights', parameters('webSiteName'))), '2014-04-01').InstrumentationKey]",
        "APPINSIGHTS_PROFILERFEATURE_VERSION": "1.0.0",
        "APPINSIGHTS_SNAPSHOTFEATURE_VERSION": "1.0.0",
        "DiagnosticServices_EXTENSION_VERSION": "~3",
        "ApplicationInsightsAgent_EXTENSION_VERSION": "~2"
      }
    }
  ]
},
```

## <a name="next-steps"></a>后续步骤

- 为应用程序生成可触发异常的流量。 然后等待 10 到 15 分钟，这样快照就会发送到 Application Insights 实例。
- 请参见 Azure 门户中的[快照](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal)。
- 排查 Snapshot Debugger 问题时如需帮助，请参阅 [ Snapshot Debugger 故障排除](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)。

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

