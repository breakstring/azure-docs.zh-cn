---
title: 使用 Purview Studio
description: 此概念文章介绍了如何使用 Azure 监控范围 Studio。
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: d8e6c4b2addf9745b2ddabe8f6fdad9d82dce59f
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/15/2020
ms.locfileid: "97503944"
---
# <a name="use-purview-studio"></a>使用 Purview Studio

本文概述了 Azure 监控范围的一些主要功能。

## <a name="prerequisites"></a>必备条件

* 已在 Azure 门户中创建了一个活动的监控范围帐户，并且该用户有权访问监控范围 Studio。

## <a name="launch-purview-account"></a>启动监控范围帐户

* 若要启动你的监控范围帐户，请在 Azure 门户中中转到监控范围帐户，选择要启动的帐户，然后启动该帐户。

   :::image type="content" source="./media/use-purview-studio/launch-from-portal.png" alt-text="用于启动 Azure 监控范围帐户目录的选择屏幕截图。":::

* 启动监控范围帐户的另一种方法是 `https://web.purview.azure.com` ：选择 " **Azure Active Directory** " 和 "帐户名称" 以启动帐户。

## <a name="home-page"></a>主页

**Home** 是 Azure 监控范围客户端的起始页。

 :::image type="content" source="./media/use-purview-studio/purview-homepage.png" alt-text="主页的屏幕截图。":::

下面的列表总结了 **主页** 的主要功能。 列表中的每个数字对应于前面屏幕截图中突出显示的数字。

1. 目录的友好名称。 你可以在 **管理中心** > "*帐户信息*" 中设置目录名称。

2. 目录分析显示以下内容的数目：
    - 用户、组和应用程序
    - 数据源
    - 资产
    - 术语表术语

3. 在 "搜索" 框中，可以搜索数据目录中的数据资产。

4. 快速访问按钮用于访问应用程序的常用功能。 显示的按钮取决于分配给用户帐户的角色。

    - 对于 *data 陈列*，按钮是 **知识中心**、 **浏览资产**、 **管理术语表** 和 **查看见解**。
    - 对于 *数据读取器*，特色按钮是 **知识中心**、 **浏览资产**、 **查看术语表** 和 **查看见解**。
    - 对于 *数据源管理员*  +  *数据陈列*，特色按钮是 **知识中心**、**注册数据源**、**浏览资产** 和 **管理词汇表**。
    - 对于 *数据源管理*  +  *器数据读取器*，特色按钮是 **知识中心**、**注册数据源**、**浏览资产** 和 **查看词汇表**。

5. 左侧导航栏可帮助您找到应用程序的主页面。 显示的按钮取决于分配给用户帐户的角色。

    - 对于 *data 陈列*，按钮为 **Home**、 **词汇表**、 **Insights** 和 **管理中心**。
    - 对于 *数据读取器*，这些按钮为 **Home**、 **词汇表**、 **Insights** 和 **管理中心**。
    - 对于 *数据源管理员*  +  *数据陈列/读取器*，这些按钮为 **Home**、source、**词汇表**、 **Insights** 和 **管理中心**。 
  
6. " **最近访问** " 选项卡显示最近访问过的数据资产的列表。 有关访问资产的信息，请参阅 [搜索数据目录](how-to-search-catalog.md) 和 [按资产类型浏览](how-to-browse-catalog.md#browse-experience)。  "**我的项目**" 选项卡是已登录用户拥有的数据资产的列表。
7. **有用的链接** 包含指向区域状态、文档、定价、概述和监控范围状态的链接
8. 顶部导航栏包含有关发行说明/更新、更改监控范围帐户、通知、帮助和反馈部分的信息。

## <a name="knowledge-center"></a>知识中心

您可以在 "知识中心" 中找到与监控范围相关的所有视频和教程。

## <a name="guided-tours"></a>引导式演示

Azure 监控范围 Studio 中的每个 UX 都具有指导教程来概述页面。 若要启动指导教程，请在顶部栏中选择 " **帮助** "，然后选择 " **引导式教程**"。

:::image type="content" source="./media/use-purview-studio/guided-tour.png" alt-text="指导教程的屏幕截图。":::

> [!Important]
   > 数据源管理员角色本身不具有对监控范围 Studio 的访问权限。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [添加安全主体](tutorial-scan-data.md)