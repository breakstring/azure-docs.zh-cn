---
title: 使用功能筛选器启用条件功能标志
titleSuffix: Azure App Configuration
description: 了解如何使用功能筛选器启用条件功能标志
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
author: AlexandraKemperMS
ms.author: alkemper
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 39455c4bc193cce036bd169c702b5c020d53d2f6
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602285"
---
# <a name="use-feature-filters-to-enable-conditional-feature-flags"></a>使用功能筛选器启用条件功能标志

使用功能标志可以在应用程序中激活或停用功能。 简单的功能标志为“开”或“关”。 应用程序会始终采用相同的行为方式。 例如，你可以在一个功能标志后面推出新功能。 启用该功能标志后，所有用户都将看到新功能。 禁用功能标志将隐藏新功能。

相比之下，使用“条件功能标志”可以动态启用或禁用功能标志。 应用程序的行为可能会有所不同，具体取决于功能标志条件。 假设要首先向小部分用户显示新功能。 使用条件功能标志可以为某些用户启用功能标志，同时为其他用户禁用该标志。 功能筛选器在每次计算出其结果时确定功能标志的状态。

`Microsoft.FeatureManagement` 库包括三个功能筛选器：

- `PercentageFilter` 基于百分比启用功能标志。
- `TimeWindowFilter` 在指定时间段内启用功能标志。
- `TargetingFilter` 为指定的用户和组启用功能标志。

还可以创建自己的功能筛选器来实现 [Microsoft.FeatureManagement.IFeatureFilter 接口](/dotnet/api/microsoft.featuremanagement.ifeaturefilter)。

## <a name="registering-a-feature-filter"></a>注册功能筛选器

您可以通过调用方法来注册功能筛选器 `AddFeatureFilter` ，并指定所需功能筛选器的类型名称。 例如，以下代码将注册 `PercentageFilter`：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddFeatureManagement().AddFeatureFilter<PercentageFilter>();
}
```

## <a name="configuring-a-feature-filter-in-azure-app-configuration"></a>在 Azure 应用配置中配置功能筛选器

某些功能筛选器具有其他设置。 例如，`PercentageFilter` 基于百分比激活某个功能。 它有一个设置用于定义要使用的百分比。

可以为 Azure 应用配置中定义的功能标志配置这些设置。 例如，执行以下步骤可使用 `PercentageFilter` 为针对某个 Web 应用的 50% 请求启用功能标志：

1. 遵循[快速入门：将功能标志添加到 ASP.NET Core 应用](./quickstart-feature-flag-aspnet-core.md)中的说明，以创建具有功能标志的 Web 应用。

1. 在 Azure 门户中，转到配置存储并单击“功能管理器”。

1. 单击在快速入门中创建的 Beta 功能标志的上下文菜单。 单击 **“编辑”** 。

    > [!div class="mx-imgBorder"]
    > ![编辑 Beta 功能标志](./media/edit-beta-feature-flag.png)

1. 在 **编辑** 屏幕中，选中 " **启用功能标志** " 复选框（如果尚未启用）。 然后选中 " **使用功能筛选器** " 复选框，并选择 " **自定义**"。 

1. 在 " **名称** " 字段中，选择 " *Microsoft. 百分比*"。

    > [!div class="mx-imgBorder"]
    > ![添加功能筛选器](./media/feature-flag-add-filter.png)

1. 单击功能筛选器名称旁边的上下文菜单。 单击 " **编辑筛选器参数**"。

    > [!div class="mx-imgBorder"]
    > ![编辑功能筛选器参数](./media/feature-flags-edit-filter-parameters.png)

1. 输入“值”作为“名称”，并输入“50”作为“值”。 “值”字段指示要启用功能筛选器的请求的百分比。

    > [!div class="mx-imgBorder"]
    > ![设置功能筛选器参数](./media/feature-flag-set-filter-parameters.png)

1. 单击“应用”以返回到“编辑功能标志”屏幕。 然后再次单击“应用”以保存功能标志设置。

1. 在 "**功能管理器**" 页上，功能标志现在具有 "*自定义*"**功能筛选器** 值。 

    > [!div class="mx-imgBorder"]
    > ![功能标志与功能筛选器值 "Custom" 一起列出](./media/feature-flag-filter-custom.png)

## <a name="feature-filters-in-action"></a>正在起作用的功能筛选器

若要查看此功能标志的效果，请在浏览器中启动该应用程序，并多次点击“刷新”按钮。 你会看到 Beta 项在大约 50% 的时间中出现在工具栏上。 它在其余时间隐藏，因为 `PercentageFilter` 会为一部分请求停用 Beta 功能。 以下视频显示了正在起作用的这一行为。

> [!div class="mx-imgBorder"]
> ![TargetingFilter 的实际运用](./media/feature-flags-percentagefilter.gif)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [为目标受众启用分阶段推出功能](./howto-targetingfilter-aspnet-core.md)
