---
title: Azure 安全中心中的文件完整性监视 |Microsoft Docs
description: 了解如何在 Azure 安全中心使用本演练 (FIM) 配置文件完整性监视。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 90f0cd913a191f345afd8acc3f3449b87e4cbfaf
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98918600"
---
# <a name="file-integrity-monitoring-in-azure-security-center"></a>Azure 安全中心中的文件完整性监视
了解如何在 Azure 安全中心使用本演练 (FIM) 配置文件完整性监视。


## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|正式发布版 (GA)|
|定价：|需要 [用于服务器的 Azure Defender](defender-for-servers-introduction.md)。<br>FIM 将数据上传到 Log Analytics 工作区。 需要根据上传的数据量支付数据费用。 请参阅 [Log Analytics 定价](https://azure.microsoft.com/pricing/details/log-analytics/)了解详细信息。|
|所需角色和权限：|**工作区所有者** 可以启用/禁用 FIM (有关详细信息，请参阅 [Log Analytics) 的 Azure 角色](/services-hub/health/azure-roles#azure-roles) 。<br>**读者** 可以查看结果。|
|云：|![是](./media/icons/yes-icon.png) 商业云<br>![是 ](./media/icons/yes-icon.png) 国家/主权 (US Gov、中国 Gov、其他 Gov) <br>仅支持 Azure 自动化的更改跟踪解决方案的区域。<br>请参阅 [链接 Log Analytics 工作区的受支持区域](../automation/how-to/region-mappings.md)。<br>[了解有关更改跟踪的详细信息](../automation/change-tracking/overview.md)。|
|||

## <a name="what-is-fim-in-security-center"></a>安全中心内的 FIM 是什么？
文件完整性监视 (FIM) （也称为更改监视、检查操作系统文件、Windows 注册表、应用程序软件、Linux 系统文件等），了解可能指示攻击的更改。 

安全中心建议用 FIM 监视实体，还可以定义自己的 FIM 策略或实体进行监视。 FIM 会提醒你以下可疑活动：

- 文件和注册表项的创建或删除
- 文件修改（文件大小、访问控制列表和内容哈希的更改）
- 注册表修改（大小、访问控制列表、类型和内容的更改）

在本教程中，将了解如何：

> [!div class="checklist"]
> * 查看要用 FIM 监视的建议实体的列表
> * 定义你自己的自定义 FIM 规则
> * 审核对所监视实体的更改
> * 使用通配符简化跨目录的跟踪


## <a name="how-does-fim-work"></a>FIM 如何工作？

通过将这些项的当前状态与上一次扫描过程中的状态进行比较，FIM 会在进行可疑修改时发出警报。

FIM 使用 Azure 更改跟踪解决方案来跟踪和识别环境中发生的更改。 启用文件完整性监视时，你具有类型为 "**解决方案**" 的 **更改跟踪** 资源。 有关数据收集频率的详细信息，请参阅 [更改跟踪数据收集详细信息](../automation/change-tracking/overview.md#change-tracking-and-inventory-data-collection)。

> [!NOTE]
> 如果删除 **更改跟踪** 资源，还将禁用安全中心中的文件完整性监视功能。

## <a name="which-files-should-i-monitor"></a>应监视哪些文件？
选择要监视的文件时，请考虑哪些文件对您的系统和应用程序至关重要。 监视不需要进行任何更改的文件。 如果选择应用程序或操作系统经常更改的文件 (如日志文件和文本文件) 它会产生大量干扰，这使得难以识别攻击。

安全中心提供了以下建议项列表，用于根据已知攻击模式进行监视。

|Linux 文件|Windows 文件|Windows 注册表项 (HKLM\ = HKEY_LOCAL_MACHINE) |
|:----|:----|:----|
|/bin/login|C:\autoexec.bat|HKLM\SOFTWARE\Microsoft\Cryptography\OID\EncodingType 0\CryptSIPDllRemoveSignedDataMsg\{C689AAB8-8E78-11D0-8C47-00C04FC295EE}|
|/bin/passwd|C:\boot.ini|HKLM\SOFTWARE\Microsoft\Cryptography\OID\EncodingType 0\CryptSIPDllRemoveSignedDataMsg\{603BCC1F-4B59-4E08-B724-D2C6297EF351}|
|/etc/*.conf|C:\config.sys|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\IniFileMapping\SYSTEM.ini\boot|
|/usr/bin|C:\Windows\system.ini|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Windows|
|/usr/sbin|C:\Windows\win.ini|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon|
|/bin|C:\Windows\regedit.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Shell Folders|
|/sbin|C:\Windows\System32\userinit.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell 文件夹|
|/boot|C:\Windows\explorer.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run|
|/usr/local/bin|C:\Program Files\Microsoft Security Client\msseces.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce|
|/usr/local/sbin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnceEx|
|/opt/bin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunServices|
|/opt/sbin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunServicesOnce|
|/etc/crontab||HKLM\SOFTWARE\WOW6432Node\Microsoft\Cryptography\OID\EncodingType 0\CryptSIPDllRemoveSignedDataMsg\{C689AAB8-8E78-11D0-8C47-00C04FC295EE}|
|/etc/init.d||HKLM\SOFTWARE\WOW6432Node\Microsoft\Cryptography\OID\EncodingType 0\CryptSIPDllRemoveSignedDataMsg\{603BCC1F-4B59-4E08-B724-D2C6297EF351}|
|/etc/cron.hourly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\IniFileMapping\system.ini\boot|
|/etc/cron.daily||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\Windows|
|/etc/cron.weekly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\Winlogon|
|/etc/cron.monthly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Explorer\Shell Folders|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Run|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunOnce|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunOnceEx|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunServices|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunServicesOnce|
|||HKLM\SYSTEM\CurrentControlSet\Control\hivelist|
|||HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\KnownDLLs|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\DomainProfile|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\PublicProfile|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\StandardProfile|


## <a name="enable-file-integrity-monitoring"></a>启用文件完整性监视 

FIM 仅在 Azure 门户的安全中心页面中提供。 当前没有用于使用 FIM 的 REST API。

1. 在 **Azure Defender** 仪表板的 **高级保护** 区域中，选择 " **文件完整性监视**"。

   :::image type="content" source="./media/security-center-file-integrity-monitoring/open-file-integrity-monitoring.png" alt-text="启动 FIM" lightbox="./media/security-center-file-integrity-monitoring/open-file-integrity-monitoring.png":::

    此时将打开 " **文件完整性监视** " 配置页。

    为每个工作区提供了以下信息：

    - 过去一周发生的更改总数（如果未对该工作区启用 FIM，可能会看到短划线“-”）
    - 向工作区报告的计算机和 VM 总数
    - 工作区的地理位置
    - 工作区所在的 Azure 订阅

1. 使用此页可以：

    - 访问和查看每个工作区的状态和设置

    - ![升级计划图标 ][4] 升级工作区以使用 Azure Defender。 此图标表示工作区或订阅不受 Azure Defender 保护。 若要使用 FIM 功能，你的订阅必须受 Azure Defender 保护。 [了解详细信息](security-center-pricing.md)。

    - ![启用图标][3] 在工作区下的所有计算机上启用 FIM，并配置 FIM 选项。 此图标指示未对工作区启用 FIM。

        :::image type="content" source="./media/security-center-file-integrity-monitoring/workspace-list-fim.png" alt-text="为特定工作区启用 FIM":::


    > [!TIP]
    > 如果没有 "启用" 或 "升级" 按钮，并且空间为空白，则表示已在工作区中启用 FIM。


1. 选择 " **启用**"。 将显示工作区的详细信息（包括工作区下的 Windows 和 Linux 计算机的数目）。

    :::image type="content" source="./media/security-center-file-integrity-monitoring/workspace-fim-status.png" alt-text="FIM 工作区详细信息页":::

   此外，还会列出适用于 Windows 和 Linux 的建议设置。  展开“Windows 文件”、“注册表”和“Linux 文件”查看建议项的完整列表。  

1. 清除不希望 FIM 监视的任何建议实体的复选框。

1. 选择“应用文件完整性监视”以启用 FIM。

> [!NOTE]
> 随时可以更改设置。 请参阅下面的 [编辑监视的实体](#edit-monitored-entities) 以了解详细信息。



## <a name="audit-monitored-workspaces"></a>审核监视的工作区 

“文件完整性监视”仪表板显示已启用 FIM 的工作区。 在工作区上启用 FIM 后，或在已启用 FIM 的 " **文件完整性监视** " 窗口中选择工作区后，将打开 fim 面板。

:::image type="content" source="./media/security-center-file-integrity-monitoring/fim-dashboard.png" alt-text="FIM 面板及其各种信息面板":::

工作区的 FIM 仪表板显示以下详细信息：

- 连接到该工作区的计算机总数
- 所选时间段内发生的更改总数
- 更改类型（文件、注册表）的细分
- 更改类别的（修改、添加、删除）细分

选择仪表板顶部的 " **筛选器** " 可更改显示更改的时间段。

:::image type="content" source="./media/security-center-file-integrity-monitoring/dashboard-filter.png" alt-text="FIM 仪表板的时间段筛选器":::

" **服务器** " 选项卡列出向此工作区报告的计算机。 对于每台计算机，仪表板将会列出：

- 所选时间段内发生的更改总数
- 更改总数的细分，以文件更改数或注册表更改数的形式列出

在搜索字段中输入计算机名，或选择“计算机”选项卡下列出的计算机时，“日志搜索”将会打开。日志搜索结果显示所选时间段内对该计算机所做的所有更改。 可以展开某项更改以查看其详细信息。

![日志搜索][8]

“更改”选项卡（如下所示）列出所选时间段内对该工作区所做的所有更改。 对于更改的每个实体，仪表板将会列出：

- 发生更改的计算机
- 更改类型（注册表或文件）
- 更改类别（修改、添加、删除）。
- 更改日期和时间

![工作区的更改][9]

在搜索字段中输入某项更改，或选择“更改”选项卡下列出的某个实体时，“更改详细信息”将会打开。 

![更改详细信息][10]

## <a name="edit-monitored-entities"></a>编辑受监视的实体

1. 返回到 **文件完整性监视仪表板** ，并选择 " **设置**"。

   ![设置][11]

   此时将打开“工作区配置”，其中显示三个选项卡：**Windows 注册表**、**Windows 文件** 和 **Linux 文件**。 每个选项卡列出可在该类别中编辑的实体。 对于列出的每个实体，安全中心会指出是已启用 (true) 还是未启用 (false) FIM。  编辑实体可以启用或禁用 FIM。

   ![工作区配置][12]

2. 选择标识保护。 在此示例中，我们选择了“Windows 注册表”下的某个项。 此时会打开“更改跟踪的编辑”。

   ![编辑或更改跟踪][13]

在“更改跟踪的编辑”下，可以：

- 启用 (True) 或禁用 (False) 文件完整性监视
- 提供或更改实体名称
- 提供或更改值或路径
- 删除实体、丢弃更改或保存更改

## <a name="add-a-new-entity-to-monitor"></a>添加要监视的新实体
1. 返回到“文件完整性监视”仪表板并选择顶部的“设置”。  此时会打开“工作区配置”。
2. 在“工作区配置”下，选择要添加的实体类型对应的选项卡：“Windows 注册表”、“Windows 文件”或“Linux 文件”。 在此示例中，我们选择了“Linux 文件”。

   ![添加要监视的新项][14]

3. 选择“添加”  。 此时会打开“更改跟踪的添加”。

   ![输入请求的信息][15]

4. 在“添加”页上，键入请求的信息并选择“保存”。 

## <a name="disable-monitored-entities"></a>禁用受监视的实体
1. 返回到 **文件完整性监视** 仪表板。
2. 选择当前已启用 FIM 的工作区。 如果某个工作区缺少“启用”按钮或“升级计划”按钮，则表示该工作区已启用 FIM。

   ![选择已启用 FIM 的工作区][16]

3. 在 "文件完整性监视" 下，选择 " **设置**"。

   ![选择设置][17]

4. 在“工作区配置”下，选择“已启用”设置为 true 的某个组。 

   ![工作区配置][18]

5. 在“更改跟踪的编辑”窗口中，将“已启用”设置为 False。 

   ![将“已启用”设置为 false][19]

6. 选择“保存” 。

## <a name="folder-and-path-monitoring-using-wildcards"></a>使用通配符监视文件夹和路径

可使用通配符简化跨目录的跟踪。 使用通配符配置文件夹监视时，以下规则适用：
-   跟踪多个文件需要使用通配符。
-   只能在路径的最后一段中使用通配符，例如 C:\folder\file 或 /etc/*.conf
-   如果环境变量包含无效的路径，验证将成功，但运行清单时，该路径将失败。
-   设置路径时，请避免使用 c:\*.* 等常规路径，因为这会导致遍历过多的文件夹。

## <a name="disable-fim"></a>禁用 FIM
可以禁用 FIM。 FIM 使用 Azure 更改跟踪解决方案来跟踪和识别环境中发生的更改。 禁用 FIM 会从所选工作区中删除“更改跟踪”解决方案。

1. 若要禁用 FIM，请返回到 **文件完整性监视** 仪表板。
2. 选择工作区。
3. 在 " **文件完整性监视**" 下，选择 " **禁用**"。

   ![禁用 FIM][20]

4. 选择“删除”以禁用 FIM。

## <a name="next-steps"></a>后续步骤
本文介绍了如何在安全中心 (FIM) 使用文件完整性监视。 若要了解有关安全中心的详细信息，请参阅以下页面：

* [设置安全策略](tutorial-security-policy.md) -- 了解如何为 Azure 订阅和资源组配置安全策略。
* [管理安全建议](security-center-recommendations.md) -- 了解建议如何帮助你保护 Azure 资源。
* [Azure 安全博客](/archive/blogs/azuresecurity/) - 获取最新的 Azure 安全新闻和信息。

<!--Image references-->
[1]: ./media/security-center-file-integrity-monitoring/security-center-dashboard.png
[3]: ./media/security-center-file-integrity-monitoring/enable.png
[4]: ./media/security-center-file-integrity-monitoring/upgrade-plan.png
[5]: ./media/security-center-file-integrity-monitoring/enable-fim.png
[7]: ./media/security-center-file-integrity-monitoring/filter.png
[8]: ./media/security-center-file-integrity-monitoring/log-search.png
[9]: ./media/security-center-file-integrity-monitoring/changes-tab.png
[10]: ./media/security-center-file-integrity-monitoring/change-details.png
[11]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings.png
[12]: ./media/security-center-file-integrity-monitoring/workspace-config.png
[13]: ./media/security-center-file-integrity-monitoring/edit.png
[14]: ./media/security-center-file-integrity-monitoring/add.png
[15]: ./media/security-center-file-integrity-monitoring/add-item.png
[16]: ./media/security-center-file-integrity-monitoring/fim-dashboard-disable.png
[17]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings-disabled.png
[18]: ./media/security-center-file-integrity-monitoring/workspace-config-disable.png
[19]: ./media/security-center-file-integrity-monitoring/edit-disable.png
[20]: ./media/security-center-file-integrity-monitoring/disable-fim.png