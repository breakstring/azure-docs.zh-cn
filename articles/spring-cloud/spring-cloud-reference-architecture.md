---
ms.date: 01/19/2021
ms.topic: reference-architecture
author: kriation
title: Azure 春季云参考体系结构
ms.author: akaleshian
ms.service: spring-cloud
description: 此参考体系结构是使用适用于 Azure 春季云的典型企业中心和辐射设计的基础。
ms.openlocfilehash: 8376cbb2a0f1a7c082b9fdb1c6d606236e162600
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417148"
---
# <a name="azure-spring-cloud-reference-architecture"></a>Azure 春季云参考体系结构

此参考体系结构是使用适用于 Azure 春季云的典型企业中心和辐射设计的基础。 在设计中，Azure 春季云部署在一个分支中，该分支依赖于中心托管的共享服务。 该体系结构是通过组件生成的，用于实现 [Microsoft Azure Well-Architected 框架][16]中的原则。

有关此体系结构的实现，请参阅 GitHub 上的 [Azure 春季云参考体系结构][10] 存储库。

此体系结构的部署选项包括 Azure 资源管理器 (ARM) 、Terraform 和 Azure CLI。 此存储库中的项目提供了可为环境自定义的基础。 可以将 Azure 防火墙或应用程序网关等资源分组到不同的资源组或订阅中。 这种分组有助于区分不同的功能，如 IT 基础结构、安全性、业务应用程序团队等。

## <a name="planning-the-address-space"></a>规划地址空间

Azure 春季云需要两个专用子网：

* 服务运行时
* 春季 Boot 应用程序

其中每个子网都需要一个专用群集。 多个群集不能共享相同的子网。 每个子网的最小大小为/28。 Azure 春季 Cloud 可支持的应用程序实例数因子网大小而异。 可以在[虚拟网络中部署 Azure 春季 Cloud][17]的 "[虚拟网络要求][11]" 部分中找到详细的虚拟网络 (VNET) 要求。

> [!WARNING]
> 所选的子网大小不能与现有的 VNET 地址空间重叠，也不应与任何对等互连或本地子网地址范围重叠。

## <a name="use-cases"></a>用例

此体系结构的典型用途包括：

* 在混合云环境中部署的内部应用程序
* 面向外部的应用程序

这些用例的安全性和网络流量规则除外。 此体系结构旨在支持每个的细微差别。

## <a name="private-applications"></a>私有应用程序

以下列表描述了专用应用程序的基础结构要求。 这些要求在高度管控环境中是典型的。

* 除了控制平面流量外，不会直接出口到公共 Internet。
* 出口流量应通过中央网络虚拟设备传播 (NVA)  (例如，Azure 防火墙) 。
* 应加密静态数据。
* 应加密传输中的数据。
* DevOps 部署管道可用于 (例如，Azure DevOps) 并要求与 Azure 春季云建立网络连接。
* Microsoft 的 "零信任" 安全方法要求将机密、证书和凭据存储在安全的保管库中。 建议的服务 Azure Key Vault。
* 应用程序主机域名服务 (DNS) 记录应存储在 Azure 专用 DNS 中。
* 本地和云中的主机名称解析应是双向的。
* 应至少遵循一个安全基准检验。
* Azure 服务依赖关系应通过服务终结点或专用链接进行通信。
* 不能修改由 Azure 春季云部署管理的资源组。
* 不能修改由 Azure 春季云部署管理的子网。
* 子网必须只有一个 Azure 春季云实例。
* 如果使用 [Azure 弹簧 Cloud Config Server][8] 从存储库加载配置属性，则该存储库必须是专用的。

以下列表显示组成设计的组件：

* 本地网络
  * 域名服务 (DNS) 
  * 网关
* 中心订阅
  * Azure 防火墙子网
  * 应用程序网关子网
  * 共享服务子网
* 已连接订阅
  * 虚拟网络对等
  * Azure 堡垒子网

以下列表描述了此参考体系结构中的 Azure 服务：

* [Azure 春季云][1]：一种托管服务，专为基于 Java 的弹簧启动应用程序和进行设计和优化。基于网络的 [Steeltoe][9] 应用程序。

* [Azure Key Vault][2]：与 Microsoft 标识服务和计算资源紧密集成的硬件支持的凭据管理服务。

* [Azure Monitor][3]：一套全面的监视服务，适用于在 Azure 和本地部署的应用程序。

* [Azure 安全中心][4]：跨本地、多个云和 Azure 的工作负荷提供统一的安全管理和威胁防护系统。

* [Azure Pipelines][5]：可自动将更新的春季 Boot 应用部署到 Azure 春季云的功能齐全的持续集成/持续开发 (CI/CD) 服务。

下图表示一种结构良好的中心和辐射设计，用于满足上述要求：

![私有应用程序的参考体系结构关系图](./media/spring-cloud-reference-architecture/architecture-private.png)

## <a name="public-applications"></a>公共应用程序

以下列表描述了公共应用程序的基础结构要求。 这些要求在高度管控环境中是典型的。

* 入口流量应至少由应用程序网关或 Azure 前门管理。
* 应启用 Azure DDoS 保护标准。
* 除了控制平面流量外，不会直接出口到公共 Internet。
* 出口流量应遍历中央网络虚拟设备 (NVA)  (例如，Azure 防火墙) 。
* 应加密静态数据。
* 应加密传输中的数据。
* DevOps 部署管道可用于 (例如，Azure DevOps) 并要求与 Azure 春季云建立网络连接。
* Microsoft 的 "零信任" 安全方法要求将机密、证书和凭据存储在安全的保管库中。 建议的服务 Azure Key Vault。
* 应用程序主机 DNS 记录应存储在 Azure 专用 DNS 中。
* Internet 可路由地址应存储在 Azure 公共 DNS 中。
* 本地和云中的主机名称解析应是双向的。
* 应至少遵循一个安全基准检验。
* Azure 服务依赖关系应通过服务终结点或专用链接进行通信。
* 不能修改由 Azure 春季云部署管理的资源组。
* 不能修改由 Azure 春季云部署管理的子网。
* 子网必须只有一个 Azure 春季云实例。

以下列表显示组成设计的组件：

* 本地网络
  * 域名服务 (DNS) 
  * 网关
* 中心订阅
  * Azure 防火墙子网
  * 应用程序网关子网
  * 共享服务子网
* 已连接订阅
  * 虚拟网络对等
  * Azure 堡垒子网

以下列表描述了此参考体系结构中的 Azure 服务：

* [Azure 春季云][1]：一种托管服务，专为基于 Java 的弹簧启动应用程序和进行设计和优化。基于网络的 [Steeltoe][9] 应用程序。

* [Azure Key Vault][2]：与 Microsoft 标识服务和计算资源紧密集成的硬件支持的凭据管理服务。

* [Azure Monitor][3]：一套全面的监视服务，适用于在 Azure 和本地部署的应用程序。

* [Azure 安全中心][4]：跨本地、多个云和 Azure 的工作负荷提供统一的安全管理和威胁防护系统。

* [Azure Pipelines][5]：可自动将更新的春季 Boot 应用部署到 Azure 春季云的功能齐全的持续集成/持续开发 (CI/CD) 服务。

* [Azure 应用程序网关][6]：一个负载均衡器，负责使用传输层安全性 (TLS) 卸载在第7层上运行的应用程序流量。

* [Azure 应用程序防火墙][7]： Azure 应用程序网关的一项功能，可提供对应用程序的集中保护，并防范常见的漏洞。

下图表示一种结构良好的中心和辐射设计，用于满足上述要求：

![公共应用程序的参考体系结构关系图](./media/spring-cloud-reference-architecture/architecture-public.png)

## <a name="azure-spring-cloud-on-premises-connectivity"></a>Azure 春季 Cloud 本地连接

在 Azure 春季云中运行的应用程序可以与各种 Azure、本地和外部资源通信。 通过使用中心辐射型设计，应用程序可以使用 Express Route 或站点到站点虚拟专用网络 (VPN) ，将流量从外部路由到本地网络。

## <a name="azure-well-architected-framework-considerations"></a>Azure Well-Architected 框架注意事项

[Azure Well-Architected 框架][16]是建立强大的基础结构基础时要遵循的一系列指导原则。 此框架包含以下类别：成本优化、卓越运营、性能效率、可靠性和安全性。

### <a name="cost-optimization"></a>成本优化

由于分布式系统设计的性质，基础结构的激增是现实。 这种现实会导致意外的不可控成本。 Azure 春季云是使用扩展的组件构建的，以便能够满足需求并优化成本。 此体系结构的核心是 Azure Kubernetes 服务 (AKS) 。 此服务旨在降低管理 Kubernetes 的复杂性和运营开销，从而提高群集的运营成本效率。

可以将不同的应用程序和应用程序类型部署到 Azure 春季云中的单个实例。 该服务支持由指标或计划触发的应用程序的自动缩放，可提高利用率和成本效益。

你还可以使用 Application Insights 和 Azure Monitor 降低运营成本。 利用综合性日志记录解决方案提供的可见性，你可以实现自动化以实时缩放系统的组件。 你还可以分析日志数据，以显示应用程序代码中的低效情况，你可以解决此情况以提高系统的整体成本和性能。

### <a name="operational-excellence"></a>卓越运营

Azure 春季云解决了卓越运营的多个方面。 你可以将这些方面结合起来，以确保服务在生产环境中有效运行，如以下列表中所述：

* 你可以使用 Azure Pipelines 确保部署的可靠性和一致性，同时帮助你避免人为错误。
* 您可以使用 Azure Monitor 和 Application Insights 来存储日志和遥测数据。
  你可以评估收集的日志和指标数据，以确保应用程序的运行状况和性能。 应用程序性能监视 (APM) 通过 Java 代理完全集成到服务中。 此代理可让你了解所有已部署的应用程序和依赖关系，而无需额外的代码。 有关详细信息，请参阅博客文章 [在 Azure 春季云中轻松监视应用程序和依赖项][15]。
* 可以使用 Azure 安全中心，通过提供用于分析和评估所提供数据的平台来确保应用程序的安全性。
* 该服务支持各种部署模式。 有关详细信息，请参阅[在 Azure Spring Cloud 中设置过渡环境][14]。

### <a name="reliability"></a>可靠性

Azure 春季 Cloud 使用 AKS 作为基础组件来设计。 尽管 AKS 通过群集提供了一个复原级别，但此参考体系结构包含了服务和体系结构注意事项，以便在出现组件故障时提高应用程序的可用性。

通过在定义完善的中心和辐射设计之上构建，此体系结构的基础可确保你可以将其部署到多个区域。 对于专用应用程序用例，该体系结构使用 Azure 专用 DNS 确保在发生地理故障时继续可用。 对于公共应用程序用例，Azure 前门和 Azure 应用程序关确保可用性。

### <a name="security"></a>安全性

此体系结构的安全性通过遵守行业定义的控制和基准得到解决。 此体系结构中的控件来自云安全联盟 (CCM) 的 [云控制矩阵][19] ，后者由 [云安全联盟][18] (CSA) 和 [Microsoft Azure 地基基准][20] ()  (CIS) 的 [中心][21] 。 在应用的控件中，重点是管理、网络和应用程序安全的主要安全设计原则。 你负责处理标识、访问管理和存储的设计原则，因为它们与目标基础结构相关。

#### <a name="governance"></a>调控

此体系结构寻址的主要方面是通过隔离网络资源来隔离的。 在 CCM 中，DCS-08 建议为数据中心提供入口和出口控制。 为了满足控件的需要，该体系结构使用网络安全组 (Nsg) 的中心和分支设计来筛选资源之间的东部流量。 该体系结构还筛选中心内的中心服务和辐射中的资源之间的流量。 该体系结构使用 Azure 防火墙的实例管理 Internet 和体系结构中资源之间的流量。

以下列表显示了此引用中用于解决数据中心安全的控件：

| CSA CCM 控件 ID | CSA CCM 控制域 |
| :----------------- | :----------------------|
| DCS-08 | 数据中心安全未授权人员条目 |

#### <a name="network"></a>网络

支持此体系结构的网络设计从传统的中心和辐射模型派生而来。 此决定确保网络隔离是基础结构。 CCM control IVS 建议在受信任和不受信任的环境之间限制和监视网络和虚拟机之间的流量。 此体系结构通过实现东-西流量的 Nsg 和用于北南部流量的 Azure 防火墙来实现控制。 CCM control IPY-04 建议基础结构应使用安全的网络协议在服务之间交换数据。 支持此体系结构的 Azure 服务都使用标准安全协议，如 HTTP 和 SQL 的 TLS。

以下列表显示了在此引用中满足网络安全的 CCM 控件：

| CSA CCM 控件 ID | CSA CCM 控制域 |
| :----------------- | :----------------------|
| IPY-04             | 网络协议      |
| IVS-06             | 网络安全       |

通过从 MAFB 定义控件，进一步保护网络实现。 控件可确保从公共 Internet 访问环境中的流量。

以下列表显示了在此引用中满足网络安全的 CIS 控件：

| CIS 控制 ID | CIS 控件说明 |
| :------------- | :---------------------- |
| 6.2 | 确保从 internet 限制 SSH 访问。 |
| 6.3 | 确保没有任何 SQL 数据库允许将 0.0.0.0/0 (任意 IP) 。 |
| 6.5 | 确保网络观察程序为 "已启用"。 |
| 6.6 | 确保使用 UDP 的入口受到限制。 |

部署到受保护的环境中时，azure 春季云要求管理流量从 Azure 传出。 若要实现此目的，必须允许客户责任中列出的网络和应用程序规则在 [VNET 中运行 Azure 春季 Cloud](/azure/spring-cloud/spring-cloud-vnet-customer-responsibilities)。

#### <a name="application-security"></a>应用程序安全性

此设计主体涵盖标识、数据保护、密钥管理和应用程序配置的基本组件。 按照设计，在 Azure 春季云中部署的应用程序将以正常运行所需的最低权限运行。 使用该服务时，授权控制集直接与数据保护相关。 密钥管理增强了这一分层应用程序安全方法。

以下列表显示了此引用中用于处理密钥管理的 CCM 控件：

| CSA CCM 控件 ID | CSA CCM 控制域 |
| :----------------- | :--------------------- |
| EKM-01 | 加密和密钥管理权限 |
| EKM-02 | 加密和密钥管理密钥生成 |
| EKM-03 | 加密和密钥管理的敏感数据保护 |
| EKM-04 | 加密和密钥管理存储和访问 |

从 CCM、EKM-02 和 EKM-03 建议策略和过程来管理密钥并使用加密协议来保护敏感数据。 EKM-01 建议所有加密密钥都具有可识别的所有者，以便可以对其进行管理。 EKM-04 建议使用标准算法。

以下列表显示了此引用中用于处理密钥管理的 CIS 控件：

| CIS 控制 ID | CIS 控件说明 |
| :------------- | :---------------------- |
| 8.1 | 确保已对所有密钥设置过期日期。 |
| 8.2 | 确保为所有机密设置过期日期。 |
| 8.4 | 确保密钥保管库可恢复。 |

CI 控件8.1 和8.2 建议为凭据设置过期日期，以确保强制执行旋转。 CIS 控制8.4 确保可以还原密钥保管库的内容，以保持业务连续性。

应用程序安全的各个方面为使用此参考体系结构以支持 Azure 中的弹簧工作负荷奠定了基础。

## <a name="next-steps"></a>后续步骤

通过 [Azure 春季云参考体系结构][10] 存储库中提供的 ARM、Terraform 和 Azure CLI 部署探索此参考体系结构。

<!-- Reference links in article -->
[1]: /azure/spring-cloud/
[2]: /azure/key-vault/
[3]: /azure/azure-monitor/
[4]: /azure/security-center/
[5]: /azure/devops/pipelines/
[6]: /azure/application-gateway/
[7]: /azure/web-application-firewall/
[8]: /azure/spring-cloud/spring-cloud-tutorial-config-server/
[9]: https://steeltoe.io/
[10]: https://github.com/Azure/azure-spring-cloud-reference-architecture
[11]: /azure/spring-cloud/spring-cloud-tutorial-deploy-in-azure-virtual-network#virtual-network-requirements
[12]: /azure/spring-cloud/spring-cloud-vnet-customer-responsibilities#azure-spring-cloud-network-requirements
[13]: /azure/spring-cloud/spring-cloud-vnet-customer-responsibilities#azure-spring-cloud-fqdn-requirements--application-rules
[14]: /azure/spring-cloud/spring-cloud-howto-staging-environment
[15]: https://devblogs.microsoft.com/java/monitor-applications-and-dependencies-in-azure-spring-cloud/
[16]: /azure/architecture/framework/
[17]: /azure/spring-cloud/spring-cloud-tutorial-deploy-in-azure-virtual-network#virtual-network-requirements
[18]: https://cloudsecurityalliance.org/
[19]: https://cloudsecurityalliance.org/research/working-groups/cloud-controls-matrix
[20]: https://azure.microsoft.com/resources/cis-microsoft-azure-foundations-security-benchmark/
[21]: https://www.cisecurity.org/
