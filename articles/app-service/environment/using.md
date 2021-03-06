---
title: 使用应用服务环境
description: 了解如何使用您的应用服务环境来承载独立的应用程序。
author: ccompy
ms.assetid: 377fce0b-7dea-474a-b64b-7fbe78380554
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: ba06a0f9d520f445965329203aecd6c576666737
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209723"
---
# <a name="using-an-app-service-environment"></a>使用应用服务环境

应用服务环境 (ASE) 是 Azure App Service 的单个租户部署，它直接注入到所选的 Azure 虚拟网络 (VNet) 。 它是一种仅由一位客户使用的系统。 部署到 ASE 中的应用受应用到 ASE 子网的网络功能的限制。 在应用中，无需启用任何附加功能即可服从这些网络功能。 

## <a name="create-an-app-in-an-ase"></a>在 ASE 中创建应用

在 ASE 中创建应用的过程与一般情况下创建应用的过程大致相同，只存在几处细微的差别。 创建新的应用服务计划时：

- 不要选择某个地理位置来部署应用，而应该选择 ASE 作为位置。
- ASE 中创建的所有应用服务计划只能位于独立主机定价层中。

如果没有 ASE，可以根据[创建应用服务环境][MakeASE]中的说明创建一个。

在 ASE 中创建应用：

1. 选择“创建资源” > “Web + 移动” > “Web 应用”。

1. 选择一个订阅。

1. 输入新资源组的名称，或选择“使用现有”并从下拉列表中选择一个资源组。

1. 输入应用程序的名称。 如果已在 ASE 中选择了应用服务计划，则应用的域名会反映 ASE 的域名：

    ![在 ASE 中创建应用][1]

1. 选择发布类型、堆栈和操作系统。

1.  选择区域。 此处需要选择预先存在的应用服务环境 v3。  在应用程序创建过程中无法创建 ASEv3 

1. 在 ASE 中选择现有的应用服务计划，或创建新的应用服务计划。 如果要创建新应用，请选择应用服务计划所需的大小。 您可以为应用程序选择的唯一 SKU 是一个独立的 v2 定价 SKU。

    ![独立 v2 定价层][2]

    > [!NOTE]
    > Linux 应用和 Windows 应用不能处于同一应用服务计划中，但可以在同一应用服务环境中。
    >

1. 选择 " **下一步：监视**  "。如果想要使用应用程序启用 application Insights，可以在创建流程中执行此操作。 

1.  选择 **下一步：标记** 向应用添加所需的任何标记  

1. 选择“查看 + 创建”，确保信息正确，然后选择“创建”。 

## <a name="how-scale-works"></a>缩放的工作原理

每个应用服务应用在应用服务计划中运行。 应用服务环境保存应用服务计划，应用服务计划保存应用。 缩放某个应用时，也会缩放应用服务计划，以及同一计划中的所有应用。

缩放应用服务计划时，会自动添加所需的基础结构。 添加基础结构时，缩放操作存在一定的时间延迟。 缩放应用服务计划时，请求的任何其他规模操作都将等待，直到第一个操作完成。 阻止规模操作完成后，将同时处理所有排队的请求。 一个大小和 OS 的缩放操作不会阻止缩放大小和操作系统的其他组合。 例如，如果你缩放了 Windows I2v2 应用服务计划，则在该 ASE 中缩放 Windows I2v2 的任何其他请求将排入队列，直到完成。   

在多租户应用服务中，缩放是即时发生的，因为有现成可用的资源池用于支持。 ASE 中没有此类缓冲区，而会根据需要分配资源。

## <a name="app-access"></a>应用访问

在 ASE 中，用于创建应用的域后缀是 *。 &lt;asename &gt; . appserviceenvironment.net*。 如果 ASE 命名为 _my ase_ ，并在该 ase 中托管名为 _contoso_ 的应用，则可通过以下 url 访问该应用：

- contoso.my-ase.appserviceenvironment.net
- contoso.scm.my-ase.appserviceenvironment.net

有关如何创建 ASE 的信息，请参阅 [创建应用服务环境][MakeASE]。

SCM URL 用于访问 Kudu 控制台，也可用于通过 Web 部署发布应用。 有关 Kudu 控制台的信息，请参阅 [Azure 应用服务的 Kudu 控制台][Kudu]。 Kudu 控制台提供 Web UI，可在其中进行调试、上传文件、 编辑文件及执行其他许多操作。

### <a name="dns-configuration"></a>DNS 配置 

ASE 将专用终结点用于入站流量。 它不会自动配置 Azure DNS 专用区域。 如果要使用自己的 DNS 服务器，则需要添加以下记录：

1. 为 &lt;ASE 名称&gt;.appserviceenvironment.net 创建一个区域
1. 在该区域中创建 A 记录，将 * 指向 ASE 专用终结点使用的入站 IP 地址
1. 在该区域中创建 A 记录，将 @ 指向 ASE 专用终结点使用的入站 IP 地址
1. 在 &lt;ASE 名称&gt;.appserviceenvironment.net 中创建名为 scm 的区域
1. 在 scm 区域中创建 A 记录，将该记录指向 ASE 专用终结点使用的 IP 地址

在 Azure DNS 专用区域中配置 DNS：

1. 创建名为 <ASE name>.appserviceenvironment.net 的 Azure DNS 专用区域
1. 在该区域中创建一条指向* ILB IP 地址的 A 记录
1. 在该区域中创建一条指向 @ ILB IP 地址的 A 记录
1. 在该区域中创建一条将 *.scm 指向 ILB IP 地址的 A 记录

ASE 默认域后缀的 DNS 设置不会将应用限制为仅可通过这些名称访问。 可以设置自定义域名，无需对 ASE 中的应用进行任何验证。 如果随后想要创建名为 *contoso.net* 的区域，则可以将其指向入站 IP 地址。 自定义域名适用于应用请求，但不适用于 scm 站点。 scm 站点仅在 &lt;appname&gt;.scm.&lt;asename&gt;.appserviceenvironment.net 中可用。 

## <a name="publishing"></a>发布

与多租户应用服务一样，在 ASE 中，可以使用以下方法发布应用：

- Web 部署
- 持续集成 (CI)
- 在 Kudu 控制台中拖放
- Visual Studio、Eclipse 或 IntelliJ IDEA 等 IDE

对于 ASE，只能通过专用终结点使用的入站地址使用发布终结点。 如果你没有对专用终结点地址的网络访问权限，则无法在该 ASE 上发布任何应用。  IDE 还必须能够通过网络访问 ILB 才能直接向其发布。

在不进行额外更改的情况下，基于 Internet 的 CI 系统（例如 GitHub 和 Azure DevOps）不支持 ILB ASE，因为发布终结点不可通过 Internet 进行访问。 可以通过在包含 ILB ASE 的虚拟网络中安装自承载发布代理，来实现从 Azure DevOps 发布到 ILB ASE。 

## <a name="storage"></a>存储

ASE 为其中的所有应用提供 1 TB 存储空间。 “独立”定价 SKU 中的一个应用服务计划限制为 250 GB。 在 ASE 中，每个应用服务计划增加了 250 GB 的存储空间，最大限制为 1 TB。 你可以有四个以上的应用服务计划，但没有超过 1 TB 限制的额外存储空间。

## <a name="logging"></a>日志记录

可将 ASE 与 Azure Monitor 相集成，以将有关 ASE 的日志发送到 Azure 存储、Azure 事件中心或 Log Analytics。 当前会记录以下项：

| 场景 | Message |
|---------|----------|
| ASE 运行不正常 | 由于虚拟网络配置无效，指定的 ASE 运行不正常。 如果持续出现不正常状态，ASE 将会挂起。 请确保遵循此处定义的准则： https://docs.microsoft.com/azure/app-service/environment/network-info 。 |
| ASE 子网空间几乎已耗尽 | 指定的 ASE 位于一个几乎耗尽了空间的子网中。 还剩下 {0} 个地址。 一旦这些地址耗尽，ASE 就无法缩放。  |
| ASE 即将达到实例总数限制 | 指定的 ASE 即将达到 ASE 的实例总数限制。 它目前包含 {0} 个应用服务计划实例，最多可以包含 201 个实例。 |
| ASE 无法访问某个依赖项 | 指定的 ASE 无法访问 {0}。  请确保遵循此处定义的准则： https://docs.microsoft.com/azure/app-service/environment/network-info 。 |
| ASE 已挂起 | 指定的 ASE 已挂起。 ASE 挂起的可能原因是帐户不足，或虚拟网络配置无效。 解决根本原因并恢复 ASE，以继续为流量提供服务。 |
| ASE 升级已启动 | 已开始对指定的 ASE 进行平台升级。 预期缩放操作会出现延迟。 |
| ASE 升级已完成 | 对指定的 ASE 进行平台升级已完成。 |
| 缩放操作已启动 | 应用服务计划 ({0}) 已开始缩放。 所需状态：{1} I{2} 辅助角色。
| 缩放操作已完成 | 应用服务计划 ({0}) 已完成缩放。 当前状态：{1} I{2} 辅助角色。 |
| 缩放操作失败 | 应用服务计划 ({0}) 无法缩放。 当前状态：{1} I{2} 辅助角色。 |

若要在 ASE 中启用日志记录：

1. 在门户中转到“诊断设置”。
1. 选择“添加诊断设置”。
1. 提供日志集成的名称。
1. 选择并配置所需的日志目标。
1. 选择“AppServiceEnvironmentPlatformLogs”。

![ASE 诊断日志设置][4]

如果与 Log Analytics 集成，可通过以下方式查看日志：在 ASE 门户中选择“日志”，并针对“AppServiceEnvironmentPlatformLogs”创建查询。  仅当 ASE 具有将触发日志的事件时，才发出日志。 如果 ASE 没有此类事件，则不会有任何日志。 若要快速查看 Log Analytics 工作区中的日志示例，请使用 ASE 中的某个应用服务计划执行缩放操作。 然后，你可以针对 AppServiceEnvironmentPlatformLogs 运行查询来查看这些日志。 

**创建警报**

若要针对日志创建警报，请按[使用 Azure Monitor 创建、查看和管理日志警报](../../azure-monitor/platform/alerts-log.md)中的说明操作。 简单地说：

* 在 ASE 门户中打开“警报”页面
* 选择“新建警报规则”
* 选择资源作为 Log Analytics 工作区
* 使用自定义日志搜索设置条件，以使用类似于“AppServiceEnvironmentPlatformLogs”的查询，其中，ResultDescription 包含“已开始缩放”或任何所需的内容。 根据需要设置阈值。 
* 根据需要添加或创建操作组。 你可以在操作组中定义对警报的响应，如发送电子邮件或短信
* 为警报命名并保存。

## <a name="internal-encryption"></a>内部加密

应用服务环境作为一个黑框系统运行，你将看不到系统中的内部组件或通信。 为了实现更高的吞吐量，默认情况下，在内部组件之间不启用加密。 系统很安全，因为流量完全无法访问，不管你是要监视流量还是要访问流量。 如果你的符合性要求需要从端到端加密对数据路径进行完全加密，则可以在 ASE **配置** UI 中启用此项。

![启用内部加密][5]

这会对前端和辅助角色之间的 ASE 中的内部网络流量进行加密，还会对页面文件和辅助角色磁盘进行加密。 启用 InternalEncryption clusterSetting 后，可能会影响系统性能。 进行更改以启用 InternalEncryption 后，ASE 会处于不稳定状态，直到更改传播完毕。 更改的传播可能需要几个小时才能完成，具体取决于 ASE 中有多少实例。 强烈建议不要在它仍处于使用状态的情况下在 ASE 上启用它。 如果需要对主动使用的 ASE 启用此操作，强烈建议将流量转移到备份环境，直到操作完成。

## <a name="upgrade-preference"></a>升级首选项

如果你有多个 ASE，你可能希望先升级某些 ASE，再升级其他 ASE。 在 ASE“HostingEnvironment 资源管理器”对象中，可以设置 upgradePreference 的值。  可以使用模板、ARMClient 或 https://resources.azure.com 配置 upgradePreference 设置。 三个可能的值为：

- **无**：Azure 将在非特定的批次中升级 ASE。 此值为默认值。
- **Early**：ASE 将在应用服务升级过程的上半阶段升级。
- **Late**：ASE 将在应用服务升级过程的下半阶段升级。

若要配置升级首选项，请参阅 ASE **配置** UI。 

当你有多个 Ase 时， **upgradePreferences** 功能最有意义，因为你的 "早期" ase 将在 "延迟" ase 之前升级。 当你有多个 Ase 时，应将你的开发和测试 Ase 设置为 "提前"，并将生产 Ase 设置为 "延迟"。

## <a name="delete-an-ase"></a>删除 ASE

若要删除 ASE，请执行以下操作：

1. 选择“应用服务环境”窗格顶部的“删除”。 

1. 输入 ASE 的名称，确认想要将它删除。 删除 ASE 时，会同时删除它包含的所有内容。

    ![ASE 删除][3]

1. 选择“确定” 。

<!--Image references-->
[1]: ./media/using/using-appcreate.png
[2]: ./media/using/using-appcreate-skus.png
[3]: ./media/using/using-delete.png
[4]: ./media/using/using-logsetup.png
[4]: ./media/using/using-logs.png
[5]: ./media/using/using-configuration.png

<!--Links-->
[Intro]: ./overview.md
[MakeASE]: ./creation.md
[ASENetwork]: ./networking.md
[UsingASE]: ./using.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/platform/alerts-log.md
