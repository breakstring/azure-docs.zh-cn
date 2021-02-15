---
title: 按国家/地区限制 Azure CDN 内容
description: 了解如何使用异地筛选功能将国家/地区的访问限制到 Azure CDN 内容。
services: cdn
documentationcenter: ''
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 01/16/2021
ms.author: allensu
ms.openlocfilehash: 8901dffb752409acd7fb08a2025bed9a4cc70132
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539515"
---
# <a name="restrict-azure-cdn-content-by-countryregion"></a>按国家/地区限制 Azure CDN 内容

## <a name="overview"></a>概述
当用户请求内容时，将为所有位置中的用户提供内容。 你可能想要按国家/地区限制对内容的访问。 

利用 *地域筛选* 功能，你可以在 CDN 终结点上创建特定路径上的规则。 你可以设置规则以允许或阻止所选国家/地区中的内容。

> [!IMPORTANT]
> **Microsoft 的 Azure CDN 标准版** 配置文件不支持基于路径的地区筛选。
> 

## <a name="standard-profiles"></a>标准版配置文件

这些说明适用于 **来自 Akamai 的标准 Azure CDN 标准** Azure CDN 和 Verizon 配置文件 **中的标准** 。

对于 **来自 Verizon 的高级 Azure CDN** 配置文件，必须使用 **管理** 门户来激活地区筛选。 有关详细信息，请参阅[来自 Verizon 的高级 Azure CDN 配置文件](#azure-cdn-premium-from-verizon-profiles)。

### <a name="define-the-directory-path"></a>定义目录路径
若要访问地区筛选功能，请在门户中选择你的 CDN 终结点，然后在左侧菜单中的“设置”下选择“地区筛选”。 

![显示在终结点的菜单中选择的异地筛选的屏幕截图。](./media/cdn-filtering/cdn-geo-filtering-standard.png)

从“路径”框中，指定将允许或拒绝用户访问的位置的相对路径。 

可使用正斜杠 (/) 对所有文件应用地区筛选，也可通过指定目录路径（例如 */pictures/*）对选定文件夹应用该筛选。 也可以向单个文件应用地区筛选（例如 */pictures/city.png*）。 允许使用多个规则。 输入规则后，将显示一个空白行，用于输入下一个规则。

例如，以下所有目录路径筛选器都是有效的：   
*/*                                 
*拍照*     
*/Photos/Strasbourg/*     
*/Photos/Strasbourg/city.png*

### <a name="define-the-type-of-action"></a>定义操作的类型

从“操作”列表中，选择 **“允许”** 或 **“阻止”**： 

- **允许**：仅允许来自指定国家/地区的用户访问从递归路径请求的资产。

- **阻止**：来自指定国家/地区的用户将被拒绝访问从递归路径请求的资产。 如果没有为该位置配置其他国家/地区筛选选项，则允许所有其他用户进行访问。

例如，用于阻止路径 */Photos/Strasbourg/* 的地区筛选规则将筛选以下文件：     
*http： \/ / \<endpoint> . azureedge.net/Photos/Strasbourg/1000.jpg* 
 *http： \/ / \<endpoint> . azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg*

### <a name="define-the-countriesregions"></a>定义国家/地区

从 " **国家/地区代码** " 列表中，选择要阻止或允许路径的国家/地区。 

选择完国家/地区后，选择 " **保存** " 以激活新的异地筛选规则。 

![屏幕截图显示了用于阻止或允许国家或地区的国家/地区代码。](./media/cdn-filtering/cdn-geo-filtering-rules.png)

### <a name="clean-up-resources"></a>清理资源

若要删除某个规则，请从“地区筛选”页面上的列表中选择它，然后选择“删除”。

## <a name="azure-cdn-premium-from-verizon-profiles"></a>来自 Verizon 的高级 Azure CDN 配置文件

对于 **从 Verizon 配置文件 Azure CDN 高级** 功能，用于创建异地筛选规则的用户界面有所不同：

1. 从 Azure CDN 配置文件的顶部菜单中，选择“管理”。

2. 从 Verizon 门户中，选择“HTTP 大型”，然后选择“国家/地区筛选”。

    :::image type="content" source="./media/cdn-filtering/cdn-geo-filtering-premium.png" alt-text="屏幕截图显示如何在 Azure CDN 中选择国家/地区筛选" border="true":::
  
3. 选择“添加国家/地区筛选器”。

4. 在 " **第一步：**" 中，输入目录路径。 选择 " **阻止** " 或 " **添加**"，然后选择 " **下一步**"。

    > [!IMPORTANT]
    > 终结点名称必须在路径中。  例如： **/myendpoint8675/myfolder**。  将 **myendpoint8675** 替换为你的终结点的名称。
    > 
    
5. 在 **步骤 2** 中，从列表中选择一个或多个国家/地区。 选择 " **完成** " 以激活规则。 
    
    新规则将显示在“国家/地区筛选”页面上的表中。
    
    :::image type="content" source="./media/cdn-filtering/cdn-geo-filtering-premium-rules.png" alt-text="屏幕截图显示规则在 &quot;国家/地区筛选&quot; 中出现的位置。" border="true":::
 
### <a name="clean-up-resources"></a>清理资源
在 "国家/地区筛选规则" 表中，选择规则旁边的 "删除" 图标，将其删除或编辑图标进行修改。

## <a name="considerations"></a>注意事项
* 对异地筛选配置的更改不会立即生效：
   * 对于 **Microsoft 推出的 Azure CDN 标准版** 配置文件，传播通常可在 10 分钟内完成。 
   * 对于 **Akamai 的 Azure CDN 标准版** 配置文件，传播通常可在一分钟内完成。 
   * 对于“Verizon 提供的 Azure CDN 标准版”  和“Verizon 提供的 Azure CDN 高级版”  配置文件，传播通常在 10 分钟内完成。 
 
* 此功能不支持通配符 (例如，* ) 。

* 与相对路径关联的地区筛选配置以递归方式应用于该路径。

* 只能对相同相对路径应用一个规则。 也就是说，您不能创建多个指向同一相对路径的国家/地区筛选器。 但是，因为国家/地区筛选器是递归的，文件夹可以有多个国家/地区筛选器。 换句话说，可以为以前配置的文件夹的子文件夹分配不同的国家/地区筛选器。

* 地域筛选功能使用国家/地区代码来定义允许或阻止某个受保护目录的请求的国家/地区。 虽然 Akamai 和 Verizon 配置文件支持的大多数国家/地区代码相同，但还是有一些差别。 有关详细信息，请参阅 [Azure CDN 国家/地区代码](/previous-versions/azure/mt761717(v=azure.100))。 

