---
title: 连接到 SAP 系统
description: 使用 Azure 逻辑应用将工作流自动化，以访问和管理 SAP 资源
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, daviburg, logicappspm
ms.topic: article
ms.date: 02/01/2021
tags: connectors
ms.openlocfilehash: cbbc0edf710b8823c1a36daa66bc01d89acf63da
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575477"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>从 Azure 逻辑应用连接到 SAP 系统

本文介绍如何使用 [sap 连接器](https://docs.microsoft.com/connectors/sap/)从逻辑应用访问 SAP 资源。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 要从中访问 SAP 资源的逻辑应用。 如果你不熟悉逻辑应用，请参阅 [逻辑应用服务概述](../logic-apps/logic-apps-overview.md) 和在 [Azure 门户中创建第一个逻辑应用的快速入门](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

    * 如果你使用的是以前版本的 SAP 连接器，则必须先 [迁移到当前连接器](#migrate-to-current-connector) ，然后才能连接到 sap 服务器。

    * 如果要在多租户 Azure 中运行逻辑应用，请参阅 [多租户必备组件](#multi-tenant-azure-prerequisites)。

    * 如果你正在[ (ise) 的高级集成服务环境 ](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中运行逻辑应用，请参阅 [ise 必备组件](#ise-prerequisites)。

* 要从逻辑应用访问的 [sap 应用程序服务器](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) 或 [sap 消息服务器](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm) 。 有关可用于连接器的 SAP 服务器和 SAP 操作的详细信息，请参阅 [sap 兼容性](#sap-compatibility)。

* 要发送到 SAP 服务器的消息内容，如示例 IDoc 文件。 此内容必须为 XML 格式，并包括要使用的 SAP 操作的命名空间。 可以 [通过将 idoc 包装在 XML 信封中来向其发送平面文件架构](#send-flat-file-idocs)。

* 如果要在 **从 SAP 触发器收到消息时** 使用，则还必须执行以下操作：

    * 通过此设置设置 SAP 网关安全权限： `"TP=Microsoft.PowerBI.EnterpriseGateway HOST=<gateway-server-IP-address> ACCESS=*"`

    * 设置 SAP 网关安全性日志记录，以帮助查找 (ACL) 的访问控制列表。 有关详细信息，请参阅 [设置网关日志记录的 SAP 帮助主题](https://help.sap.com/erp_hcm_ias2_2015_02/helpdata/en/48/b2a710ca1c3079e10000000a42189b/frameset.htm)。 否则，你将收到此错误： `"Registration of tp Microsoft.PowerBI.EnterpriseGateway from host <host-name> not allowed"`

    > [!NOTE]
    > 此触发器使用同一 URI 位置来续订和取消订阅 webhook 订阅。 续订操作使用 HTTP `PATCH` 方法，而取消订阅操作使用 http `DELETE` 方法。 此行为可能会使续订操作在触发器历史记录中显示为取消订阅操作，但该操作仍是续订，因为触发器使用 `PATCH` 作为 HTTP 方法，而不是 `DELETE` 。

### <a name="sap-compatibility"></a>SAP 兼容性

SAP 连接器与以下类型的 SAP 系统兼容：

* 本地和基于云的 HANA SAP 系统，例如 S/4 HANA。

* 经典本地 SAP 系统，例如 R/3 和 ECC。

SAP 连接器支持基于 SAP NetWeaver 的系统中的以下消息和数据集成类型：

* 中间文档 (IDoc) 

*  (BAPI 的业务应用程序编程接口) 

* 远程函数调用 (RFC) 和事务 RFC (tRFC) 

SAP 连接器使用 [ (NCo) 库的 sap .Net 连接器](https://support.sap.com/en/product/connectors/msnet.html)。 你可以使用以下 SAP 操作并使用连接器触发：

* 向 **SAP 发送消息**，以 [通过 TRFC 操作发送 idoc](#send-idoc-action) ，可以使用该操作执行以下操作：

    * [调用 BAPI 函数 over RFC](#call-bapi-action)

    * 在 SAP 系统中调用 RFC/tRFC

    * 创建或关闭有状态会话

    * 提交或回滚 BAPI 事务

    * 确认事务标识符

    * 发送 Idoc，从其编号获取 IDoc 的状态，并获取事务的 Idoc 列表

    * 读取 SAP 表

* **从 SAP 触发器收到消息时** ，可以使用该消息执行以下操作：

    * 通过 tRFC 接收 Idoc

    * 通过 tRFC 调用 BAPI 函数

    * 在 SAP 系统中调用 RFC/tRFC

* "**生成架构**" 操作，可用于为 IDOC、BAPI 或 RFC 的 SAP 项目生成架构。

若要使用这些 SAP 操作，需要首先使用用户名和密码对连接进行身份验证。 SAP 连接器还支持 [ (SNC) 的安全网络通信 ](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true)。 你可以使用 SNC for SAP NetWeaver 单一登录 (SSO) 或外部产品的其他安全功能。 如果使用 SNC，请参阅 [SNC 必备组件](#snc-prerequisites)。

### <a name="migrate-to-current-connector"></a>迁移到最新连接器

以前的 SAP 应用程序服务器和 SAP 消息服务器连接器已不推荐使用2020年2月29日。 若要迁移到当前 SAP 连接器，请执行以下步骤：

1. 将 [本地数据网关](https://www.microsoft.com/download/details.aspx?id=53127) 更新到最新版本。 有关详细信息，请参阅[为 Azure 逻辑应用安装本地数据网关](../logic-apps/logic-apps-gateway-install.md)。

1. 在使用已弃用的 SAP 连接器的逻辑应用中，删除 " **发送到 SAP** " 操作。

1. 从当前 SAP 连接器向 SAP 操作添加 **发送消息** 。

1. 重新连接到新操作中的 SAP 系统。

1. 保存逻辑应用。

### <a name="multi-tenant-azure-prerequisites"></a>多租户 Azure 先决条件

如果逻辑应用在多租户 Azure 中运行，则这些必备组件适用。 托管 SAP 连接器不会以本机方式在 [ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中运行。

> [!TIP]
> 如果你使用的是高级 ISE，则可以使用 SAP ISE 连接器，而不是托管 SAP 连接器。 有关详细信息，请参阅 [ISE 必备组件](#ise-prerequisites)。

托管 SAP 连接器通过 [本地数据网关](../logic-apps/logic-apps-gateway-connection.md)与 SAP 系统集成。 例如，在 "发送消息" 方案中，将消息从逻辑应用发送到 SAP 系统时，数据网关充当 RFC 客户端，并将从逻辑应用接收的请求转发到 SAP。 同样，在接收消息的情况下，数据网关将充当接收来自 SAP 的请求并将其转发给逻辑应用的 RFC 服务器。

* [下载本地数据网关并](../logic-apps/logic-apps-gateway-install.md) 将其安装到与要连接到的 SAP 系统位于同一虚拟网络中的主计算机或虚拟机上。

* 在 Azure 门户中为本地数据网关[创建 Azure 网关资源](../logic-apps/logic-apps-gateway-connection.md#create-azure-gateway-resource)。 此网关可帮助你安全地访问本地数据和资源。 请确保使用支持的网关版本。

    * 如果网关遇到问题，请尝试 [升级到最新版本](https://aka.ms/on-premises-data-gateway-installer)，其中可能包括用于解决问题的更新。

* [下载并安装最新的 SAP 客户端库，并](#sap-client-library-prerequisites) 将其安装在本地数据网关所在的本地计算机上。

### <a name="ise-prerequisites"></a>ISE 必备组件

如果在高级 ISE ISE 中运行逻辑应用，则会应用这些先决条件。 但是，它们不适用于开发人员级 ISE 中运行的逻辑应用。 ISE 提供对受 Azure 虚拟网络保护的资源的访问权限，并提供其他 ISE 本地连接器，让逻辑应用可以直接访问本地资源，而无需使用本地数据网关。

> [!NOTE]
> 尽管 SAP ISE 连接器在开发人员级 ISE 内可见，但尝试安装连接器不会成功。

1. 如果还没有 Azure 存储帐户和 blob 容器，请使用 [Azure 门户](../storage/blobs/storage-quickstart-blobs-portal.md) 或 [Azure 存储资源管理器](../storage/blobs/storage-quickstart-blobs-storage-explorer.md)创建容器。

1. 在本地计算机上[下载并安装最新的 SAP 客户端库](#sap-client-library-prerequisites)。 应具有以下程序集文件：

   * libicudecnumber.dll

   * rscp4n.dll

   * sapnco.dll

   * sapnco_utils.dll

1. 创建一个包含这些程序集文件的 .zip 文件。 将包上传到 Azure 存储中的 blob 容器。

1. 在 Azure 门户或 Azure 存储资源管理器中，浏览到你上传 .zip 文件的容器位置。

1. 复制容器位置的 URL。 请确保 (SAS) 令牌中包含共享访问签名，因此会授权 SAS 令牌。 否则，SAP ISE 连接器的部署将失败。

1. 在 ISE 中安装并部署 SAP 连接器。 有关详细信息，请参阅 [添加 ISE 连接器](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment)。

   1. 在 [Azure 门户](https://portal.azure.com)中，找到并打开 ISE。

   1. 在 ISE 菜单上，选择 " **托管连接器**" " &gt; **添加**"。 在 "连接器" 列表中，找到并选择 " **SAP**"。

   1. 在 " **添加新的托管连接器** " 窗格中的 " **sap 包** " 框中，粘贴包含 SAP 程序集的 .zip 文件的 URL。 同样，请确保包含 SAS 令牌。
 
  1. 选择 " **创建** " 以完成创建 ISE 连接器。

1. 如果 SAP 实例和 ISE 位于不同的虚拟网络中，则还需要 [对这些网络对等](../virtual-network/tutorial-connect-virtual-networks-portal.md) 互连。

### <a name="sap-client-library-prerequisites"></a>SAP 客户端库必备组件

下面是与连接器一起使用的 SAP 客户端库的先决条件。

* 请确保安装了最新版本， [SAP 连接器 (NCo 3.0) ，用于 Microsoft .NET 用 .NET Framework 4.0-Windows 64 位 (x64) 编译的 3.0.22.0 ](https://support.sap.com/en/product/connectors/msnet.html)。 当同时发送多个 IDoc 消息时，SAP NCo 的早期版本可能会遇到问题。 此条件会阻止发送到 SAP 目标的所有后续消息，这会导致消息超时。

* 你必须安装了64位版本的 SAP 客户端库，因为 data gateway 仅在64位系统上运行。 安装不受支持的32位版本会导致 "错误映像" 错误。

* 根据你的方案，将默认安装文件夹中的程序集文件复制到另一个位置，如下所示。

    * 对于在 ISE 中运行的逻辑应用，请改用 [ise 必备组件](#ise-prerequisites) 。

    * 对于在多租户 Azure 中运行的逻辑应用和使用本地数据网关，请将程序集文件复制到数据网关安装文件夹中。 

        
        * 如果 SAP 连接失败并出现错误消息， **请检查你的帐户信息和/或权限，然后重试**，确保已将程序集文件复制到数据网关安装文件夹中。
        
        * 使用 [.net 程序集绑定日志查看器](/dotnet/framework/tools/fuslogvw-exe-assembly-binding-log-viewer)排除进一步的问题。 此工具可让你检查程序集文件是否在正确的位置。 
        
        * 或者，在安装 SAP 客户端库时选择 " **全局程序集缓存注册** " 选项。

请注意 SAP 客户端库、.NET Framework、.NET 运行时和网关之间的以下关系：

* Microsoft SAP 适配器和网关主机服务都使用 .NET Framework 4.7.2。

* 适用于 .NET Framework 4.0 的 SAP NCo 适用于使用 .NET 运行时4.0 到4.8 的进程。

* 适用于 .NET Framework 2.0 的 SAP NCo 适用于使用 .NET 运行时2.0 到3.5 的进程，但不再适用于最新的网关。

### <a name="snc-prerequisites"></a>SNC 先决条件

如果将本地数据网关与可选的 SNC （仅在多租户 Azure 中受支持）一起使用，则必须配置这些附加设置。

如果将 SNC 与 SSO 结合使用，请确保将数据网关服务作为映射到 SAP 用户的用户运行。 若要更改默认帐户，请选择“更改帐户”并输入用户凭据。 

![Azure 门户中的本地数据网关设置的屏幕截图，其中显示了 "服务设置" 页，其中包含用于更改所选网关服务帐户的按钮。](./media/logic-apps-using-sap-connector/gateway-account.png)

如果要通过外部安全产品启用 SNC，请将 SNC 库或文件复制到安装数据网关的同一台计算机上。 SNC 产品的部分示例包括 [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm)、Kerberos 和 NTLM。 有关为数据网关启用 SNC 的详细信息，请参阅 [启用安全网络通信](#enable-secure-network-communications)。

## <a name="send-idoc-messages-to-sap-server"></a>将 IDoc 消息发送到 SAP 服务器

按照以下示例创建一个逻辑应用，以便将 IDoc 消息发送到 SAP 服务器并返回响应：

1. [创建由 HTTP 请求触发的逻辑应用。](#create-http-request-trigger)

1. [在工作流中创建操作，以将消息发送到 SAP。](#create-sap-action-to-send-message)

1. [在工作流中创建 HTTP 响应操作。](#create-http-response-action)

1. [如果要使用 RFC 接收来自 SAP ABAP 的回复，请 (RFC) 请求-响应模式创建远程函数调用。](#create-rfc-request-response)

1. [测试逻辑应用。](#test-logic-app)

### <a name="create-http-request-trigger"></a>创建 HTTP 请求触发器

> [!NOTE]
> 逻辑应用从 SAP 接收到 Idoc 时， [请求触发器](../connectors/connectors-native-reqres.md) 现在支持 SAP 纯 XML 格式。 若要以纯 XML 的形式接收 Idoc，请 **在从 SAP 接收到消息时** 使用触发器。 将参数 **IDOC 的格式** 设置为 **SapPlainXml**。

首先，在 Azure 中创建包含终结点的逻辑应用，将 *HTTP POST* 请求发送到逻辑应用。 逻辑应用收到这些 HTTP 请求时， [触发器](../logic-apps/logic-apps-overview.md#logic-app-concepts) 将触发，并在工作流中运行下一步。

1. 在 [Azure 门户](https://portal.azure.com)中，创建一个空白逻辑应用，用于打开 **逻辑应用设计器**。

1. 在搜索框中，输入 `http request` 作为筛选器。 在触发器列表中，选择“当收到 HTTP 请求时”。  

   ![逻辑应用设计器的屏幕截图，显示要添加到逻辑应用的新 HTTP 请求触发器。](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. 保存逻辑应用，以便可以为逻辑应用生成终结点 URL。 在设计器工具栏上选择“保存”。  终结点 URL 现在会出现在触发器中。 

   ![逻辑应用设计器的屏幕截图，其中显示了复制生成的 POST URL 时发出 HTTP 请求触发器。](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="create-sap-action-to-send-message"></a>创建 SAP 操作以发送消息

接下来，创建一个操作，以便在 [HTTP 请求触发器](#create-http-request-trigger) 触发时向 SAP 发送 IDoc 消息。

1. 在逻辑应用设计器中的触发器下，选择 " **新建步骤**"。

   ![逻辑应用设计器的屏幕截图，显示要编辑的逻辑应用，以添加新步骤。](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. 在搜索框中，输入 `sap` 作为筛选器。 在“操作”列表中选择“将消息发送到 SAP”。  
  
   ![逻辑应用设计器的屏幕截图，显示 "向 SAP 发送消息" 操作的选择。](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   也可以选择“企业”选项卡，然后选择 SAP 操作。 

   ![逻辑应用设计器的屏幕截图，显示 "企业" 选项卡下的 "向 SAP 发送消息" 操作的选择。](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. 如果连接已存在，则继续执行下一步。 如果系统提示你创建新的连接，请提供以下信息以连接到你的本地 SAP 服务器。

   1. 为连接提供一个名称。

   1. 如果使用的是数据网关，请执行以下步骤：
   
      1. 在 " **数据网关** " 部分中，在 " **订阅**" 下，先为你在数据网关安装的 Azure 门户中创建的 "数据网关" 资源选择 Azure 订阅。
   
      1. 在 " **连接网关**" 下，在 Azure 中选择数据网关资源。

   1. 继续提供连接信息。 对于“登录类型”属性，请根据该属性是设置为“应用程序服务器”还是“组”来执行相关步骤：   
   
      * 对于“应用程序服务器”，必须指定以下属性（通常显示为可选）： 

        ![创建 SAP 应用程序服务器连接](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * 对于“组”，必须指定以下属性（通常显示为可选）： 

        ![创建 SAP 消息服务器连接](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      默认会使用强类型化通过针对架构执行 XML 验证来检查无效值。 此行为可帮助提前检测问题。 “安全类型化”选项用于实现后向兼容，它只会检查字符串长度。  详细了解[“安全类型化”选项](#safe-typing)。

   1. 完成后，选择“创建”  。

      逻辑应用会设置并测试连接，确保连接正常工作。

    > [!NOTE]

    > 如果收到以下错误，则说明 SAP NCo 客户端库的安装有问题： 
    >
    > **测试连接失败。错误 "处理请求失败。错误详细信息：无法加载文件或程序集 "sapnco，Version = 3.0.0.42，Culture = 中立，PublicKeyToken 50436dca5c7f7d23" 或其依赖项之一。系统找不到指定的文件。**
    >
    > 请确保 [安装所需版本的 SAP NCo 客户端库，并满足所有其他先决条件](#sap-client-library-prerequisites)。

1. 现在，请找到并选择 SAP 服务器中的某个操作。

   1. 在“SAP 操作”框中，选择文件夹图标。  在文件列表中，找到并选择要使用的 SAP 消息。 使用箭头可在列表中导航。

      此示例选择了类型为“订单”的 IDoc。 

      ![找到并选择 IDoc 操作](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      如果找不到所需的操作，可以手动输入路径，例如：

      ![手动提供 IDoc 操作的路径](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > 通过表达式编辑器提供“SAP 操作”的值。  这样，便可以对不同的消息类型使用相同的操作。

      有关 IDoc 操作的详细信息，请参阅 [IDoc 操作的消息架构](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)。

   1. 在“输入消息”框中单击，以显示动态内容列表。  在该列表中的“收到 HTTP 请求时”下面，选择“正文”字段。  

      此步骤包括 HTTP 请求触发器的正文内容，并将输出发送到 SAP 服务器。

      ![从触发器中选择 "Body" 属性](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      完成后，SAP 操作如以下示例所示：

      ![完成 SAP 操作](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. 保存逻辑应用。 在设计器工具栏上选择“保存”。 

#### <a name="send-flat-file-idocs"></a>发送平面文件 Idoc

如果将 Idoc 包装在 XML 信封中，则可以将其与平面文件架构一起使用。 若要发送平面文件 IDoc，请使用一般说明 [创建 SAP 操作，以便使用以下更改发送 IDoc 消息](#create-sap-action-to-send-message) 。

1. 对于 "向 **Sap 发送消息** " 操作，请使用 SAP 操作 URI `http://microsoft.lobservices.sap/2007/03/Idoc/SendIdoc` 。

1. 使用 XML 信封设置输入消息的格式。 有关示例，请参阅下面的示例消息：

```xml
<?xml version="1.0" encoding="utf-8"?>
<SendIdoc xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/">
  <idocData>EDI_DC    300                      ORDERS052SAPMSS    LIMSFTABCSWI                                                                                           ED  93AORDERSOLP     VLTRFC    KUMSFTABCSWI                                                                                           13561                       231054476                                                                           20190523085430ORDERSORDERS05          US
E2EDK01005300                1     E2EDK010050     1       USD                                                                        Z4O14506907554
E2EDK03   300                2     E2EDK03   0     2   02220190523
E2EDKA1   300                3     E2EDKA1   0     2   RE                  MSFTASWI
E2EDKA1   300                4     E2EDKA1   0     2   US                  MSFTASWI
E2EDKA1   300                5     E2EDKA1   0     2   WE                  MSFTASWILIC
E2EDKA1   300                6     E2EDKA1   0     2   Z1 KKKKKKK                           ABC YYYYYYYYYYY ZZ                                                                                                                          BBBBBBBBBBBBBBBB 11                                                                                      ttttttttttt                                 6666              US                                                                                                999 999 99 99                                                                                                                SSSSSSS SSS SSSSSS                                                                                                                                SSSSSSS SSS SSSSSS
E2EDKA1   300                7     E2EDKA1   0     2   Z2 KKKKKKK                           BBBBBBBBBBBBBBBB DDDDDDDD ZZ                                                                                                                EEEEEEEEEEE 86                                                                                           rrrrrrrr                                    8888              US                                                                                                999 999 99 99                                                                                                                NNNNNN NNNNNN                                                                                                                                     NNNNNN NNNNNN
E2EDK02   300                8     E2EDK02   0     2   901Z
E2EDK02   300                9     E2EDK02   0     2   90399680096ZZS2002
E2EDK02   300                10    E2EDK02   0     2   902S
E2EDKT1   300                11    E2EDKT1   0     2   Z1EME
E2EDKT2   300                12    E2EDKT2   0     3   xxx@xxx-xx.xx
E2EDKT1   300                13    E2EDKT1   0     2   Z2EME
E2EDKT2   300                14    E2EDKT2   0     3   x.xxxxxx@xxxxxxxx-xxxxxxxxxx.xx
E2EDP01001300                15    E2EDP010010     2   10         1              EA                          999.9
E2EDP19   300                16    E2EDP19   0     3   00AAAA-11111</idocData>
</SendIdoc>
```



### <a name="create-http-response-action"></a>创建 HTTP 响应操作

现在，请将响应操作添加到逻辑应用的工作流，并包括来自 SAP 操作的输出。 这样，逻辑应用便可将来自 SAP 服务器的结果返回到原始请求方。

1. 在逻辑应用设计器中的 "SAP 操作" 下，选择 " **新建步骤**"。

1. 在搜索框中，输入 `response` 作为筛选器。 在“操作”列表中选择“响应”。  

1. 在“正文”框中单击，以显示动态内容列表。  在该列表中的“将消息发送到 SAP”下面，选择“正文”字段。  

   ![完成 SAP 操作](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. 保存逻辑应用。

#### <a name="create-rfc-request-response"></a>创建 RFC 请求-响应

> [!NOTE]
> SAP 触发器通过 tRFC 接收 Idoc，这不会有设计的响应参数。 

如果需要使用远程函数调用接收答复，则必须创建请求和响应模式， (RFC) 使用 SAP ABAP 中的逻辑应用。 若要在逻辑应用中接收 Idoc，应使其第一个操作为 [HTTP 请求](../connectors/connectors-native-reqres.md#add-a-response-action) ，状态代码为 `200 OK` ，不包含任何内容。 此建议步骤会立即通过 tRFC 完成 SAP LUW 异步传输，这会使 SAP CPIC 对话再次可用。 然后，你可以在逻辑应用中添加更多操作来处理收到的 IDoc，而不会阻止进一步的传输。

若要实现请求和响应模式，必须首先使用[ `generate schema` 命令](#generate-schemas-for-artifacts-in-sap)发现 RFC 架构。 生成的架构包含两个可能的根节点： 

1. 请求节点，它是从 SAP 接收的呼叫。

1. 响应节点，可回复到 SAP。

在下面的示例中，将从 RFC 模块生成请求和响应模式 `STFC_CONNECTION` 。 解析请求 XML 以提取 SAP 请求的节点值 `<ECHOTEXT>` 。 响应将当前时间戳插入为动态值。 从逻辑应用向 SAP 发送 RFC 时，会收到类似的响应 `STFC_CONNECTION` 。

```http

<STFC_CONNECTIONResponse xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <ECHOTEXT>@{first(xpath(xml(triggerBody()?['Content']), '/*[local-name()="STFC_CONNECTION"]/*[local-name()="REQUTEXT"]/text()'))}</ECHOTEXT>
  <RESPTEXT>Azure Logic Apps @{utcNow()}</RESPTEXT>


```

### <a name="test-logic-app"></a>测试逻辑应用

1. 如果尚未启用逻辑应用，请在逻辑应用菜单中选择“概览”。  在工具栏中选择“启用”。 

1. 在设计器工具栏上选择“运行”。  此步骤会手动启动逻辑应用。

1. 通过将 HTTP POST 请求发送到 HTTP 请求触发器中的 URL 来触发逻辑应用。
在请求中包括消息内容。 若要发送请求，可以使用 [Postman](https://www.getpostman.com/apps) 等工具。

   在本文中，请求会发送 IDoc 文件，该文件必须采用 XML 格式，并且包含你所用 SAP 操作的命名空间，例如：

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

1. 发送 HTTP 请求之后，等待逻辑应用返回响应。

   > [!NOTE]
   > 如果未在[请求超时限制](./logic-apps-limits-and-config.md)所规定的时间内完成响应所需的所有步骤，逻辑应用可能会超时。 如果发生这种情况，请求可能会被阻止。 为了诊断问题，请了解如何[检查和监视逻辑应用](../logic-apps/monitor-logic-apps.md)。

现已创建一个可与 SAP 服务器通信的逻辑应用。 为逻辑应用设置 SAP 连接后，可以探索其他可用的 SAP 操作，例如 BAPI 和 RFC。

## <a name="receive-message-from-sap"></a>从 SAP 接收消息

此示例使用当应用从 SAP 系统收到消息时触发的逻辑应用。

### <a name="add-an-sap-trigger"></a>添加 SAP 触发器

1. 在 Azure 门户中，创建一个空白逻辑应用，用于打开逻辑应用设计器。

1. 在搜索框中，输入 `sap` 作为筛选器。 在“触发器”列表中选择“从 SAP 收到消息时”。  

   ![添加 SAP 触发器](./media/logic-apps-using-sap-connector/add-sap-trigger-logic-app.png)

   也可选择“企业”选项卡，然后选择触发器： 

   ![在“企业”选项卡中添加 SAP 触发器](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. 如果连接已存在，请继续下一步，以便可以设置 SAP 操作。 但是，如果系统提示输入连接详细信息，请提供该信息，以便立即创建到本地 SAP 服务器的连接。

   1. 为连接提供一个名称。

   1. 如果使用的是数据网关，请执行以下步骤：

      1. 在 " **数据网关** " 部分中，在 " **订阅**" 下，先为你在数据网关安装的 Azure 门户中创建的 "数据网关" 资源选择 Azure 订阅。

      1. 在 " **连接网关**" 下，在 Azure 中选择数据网关资源。

   1. 继续提供有关连接的信息。 对于“登录类型”属性，请根据该属性是设置为“应用程序服务器”还是“组”来执行相关步骤：   

      * 对于“应用程序服务器”，必须指定以下属性（通常显示为可选）： 

        ![创建 SAP 应用程序服务器连接](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * 对于“组”，必须指定以下属性（通常显示为可选）： 

        ![创建 SAP 消息服务器连接](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      默认会使用强类型化通过针对架构执行 XML 验证来检查无效值。 此行为可帮助提前检测问题。 “安全类型化”选项用于实现后向兼容，它只会检查字符串长度。  详细了解[“安全类型化”选项](#safe-typing)。

   1. 完成后，选择“创建”  。

      逻辑应用会设置并测试连接，确保连接正常工作。

1. 根据 SAP 系统配置提供 [所需的参数](#parameters) 。 

   可以 [通过指定 sap 操作的列表来筛选从 sap 服务器接收的消息](#filter-with-sap-actions)。

   可以通过文件选取器选择 SAP 操作：

   ![向逻辑应用添加 SAP 操作](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   或者，你可以手动指定一个操作：

   ![手动输入要使用的 SAP 操作](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png)

   以下示例演示将触发器设置为接收多个消息时操作的显示方式。

   ![接收多个消息的触发器示例](media/logic-apps-using-sap-connector/example-trigger.png)

   有关 SAP 操作的详细信息，请参阅 [IDoc 操作的消息架构](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

1. 现在请保存逻辑应用，以便可以开始从 SAP 系统接收消息。 在设计器工具栏上选择“保存”。 

现在，逻辑应用已准备好从 SAP 系统接收消息。

> [!NOTE]
> SAP 触发器不是轮询触发器，而是基于 Webhook 的触发器。 如果使用的是数据网关，则只有当消息存在时，才从数据网关中调用触发器，因此不需要进行轮询。

#### <a name="parameters"></a>参数

SAP 连接器除了简单的字符串和数字输入以外，还接受以下表参数 (`Type=ITAB` 输入) ：

* 适用于较早 SAP 版本的表方向参数（输入和输出）。

* 更改参数，用于替换更新的 SAP 版本的表方向参数。

* 分层表参数

#### <a name="filter-with-sap-actions"></a>筛选 SAP 操作

可以通过提供包含单个或多个 SAP 操作的列表或数组，来筛选逻辑应用从 SAP 服务器接收的消息。 默认情况下，此数组为空，这意味着逻辑应用从 SAP 服务器接收所有消息，而不进行筛选。 

设置阵列筛选器时，触发器仅接收来自指定 SAP 操作类型的消息，并拒绝来自 SAP 服务器的所有其他消息。 但是，此筛选器不会影响输入的有效负载是否为弱或强类型。

任何 SAP 操作筛选均发生在本地数据网关的 SAP 适配器级别。 有关详细信息，请参阅 [如何将 Test idoc 发送到 SAP 中的逻辑应用](#test-sending-idocs-from-sap)。

如果无法将 IDoc 数据包从 SAP 发送到逻辑应用的触发器，请参阅 "SAP tRFC" 对话框中的 "事务 RFC (tRFC) 调用拒绝消息" (T-sql SM58) 。 在 SAP 接口中，你可能会收到以下错误消息，这些错误消息是由于 " **状态" 文本** 字段的子字符串限制引起的。

* `The RequestContext on the IReplyChannel was closed without a reply being`：当通道的全部捕获处理程序由于错误而终止通道时出现意外失败，并重建通道以处理其他消息。

  * 若要确认逻辑应用收到 IDoc，请 [添加一个](../connectors/connectors-native-reqres.md#add-a-response-action) 返回状态代码的响应操作 `200 OK` 。 通过 tRFC 传输 IDoc，这不允许响应负载。

  * 如果需要拒绝 IDoc，请改用除之外的任何 HTTP 状态代码， `200 OK` 以使 Sap 适配器代表您返回到 sap。 

* `The segment or group definition E2EDK36001 was not found in the IDoc meta`：预期的失败与其他错误发生，例如由于 SAP 未释放 IDoc XML 负载而导致无法生成该负载，因此缺少转换所需的段类型元数据。 

  * 若要将这些段发布到 SAP，请联系 SAP 系统的 ABAP 工程师。
### <a name="asynchronous-request-reply-for-triggers"></a>异步请求应答触发器

SAP 连接器支持逻辑应用触发器的 Azure [异步请求-答复模式](/azure/architecture/patterns/async-request-reply) 。 您可以使用此模式创建成功的请求，否则会失败，并使用默认的同步请求-答复模式。 

> [!TIP]
> 在具有多个响应操作的逻辑应用中，所有响应操作必须使用相同的请求-答复模式。 例如，如果逻辑应用使用具有多个可能的响应操作的 switch 控件，则必须将所有响应操作配置为使用相同的请求-答复模式，无论是同步还是异步。 

启用响应操作的异步响应后，逻辑应用就可以 `202 Accepted` 在请求被接受处理时，使用答复来响应。 回复包含位置标头，可用于检索请求的最终状态。

使用 SAP 连接器为逻辑应用配置异步请求-答复模式：

1. 在 **逻辑应用设计器** 中打开逻辑应用。

1. 确认 SAP 连接器是逻辑应用的触发器。

1. 打开逻辑应用的 **响应** 操作。 在操作的标题栏中，选择 " (**...** " 菜单) " &gt; **设置**"。

1. 在 "响应" 操作的 " **设置** " 中，启用 " **异步响应**" 下的切换。 选择 "完成"。

1. 保存对逻辑应用所做的更改。

## <a name="find-extended-error-logs"></a>查找扩展错误日志

有关完整的错误消息，请检查 SAP 适配器的扩展日志。 你还可以 [为 SAP 连接器启用扩展日志文件](#extended-sap-logging-in-on-premises-data-gateway)。

对于本地数据网关2020年6月版和更高版本，你可以 [在应用程序设置中启用网关日志](/data-integration/gateway/service-gateway-tshoot#collect-logs-from-the-on-premises-data-gateway-app)。 

* 默认日志记录级别为 **Warning**。

* 如果在本地数据网关应用的 **诊断** 设置中启用 **其他日志记录**，则会将日志记录级别提升为 "**信息**"。

* 若要将日志记录级别提高到 **详细**，请在配置文件中更新以下设置。 通常，配置文件位于 `C:\Program Files\On-premises data gateway\Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config` 。

```json
<setting name="SapTraceLevel" serializeAs="String">
   <value>Verbose</value>
</setting>
```

对于本地数据网关版本2020及更早版本，默认禁用日志。

### <a name="extended-sap-logging-in-on-premises-data-gateway"></a>本地数据网关上的扩展 SAP 日志记录

如果 [为逻辑应用使用本地数据网关](../logic-apps/logic-apps-gateway-install.md)，则可以为 SAP 连接器配置扩展日志文件。 你可以使用本地数据网关将 Windows (ETW) 事件的事件跟踪重定向到你的网关的日志记录 .zip 文件中包含的循环日志文件。 

你可以从网关应用的设置中将 [所有网关的配置和服务日志导出](https://docs.microsoft.com/data-integration/gateway/service-gateway-tshoot#collect-logs-from-the-on-premises-data-gateway-app) 到中的 .zip 文件。

> [!NOTE]
> 当始终启用时，扩展日志记录可能会影响逻辑应用的性能。 完成分析并解决问题后，最佳做法是关闭扩展日志文件。

#### <a name="capture-etw-events"></a>捕获 ETW 事件

或者，高级用户可以直接捕获 ETW 事件。 然后，你可以 [在事件中心的 Azure 诊断中使用数据](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-stream-event-hubs) ，或将 [数据收集到 Azure Monitor 日志](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-logs)。 有关详细信息，请参阅 [收集和存储数据的最佳做法](https://docs.microsoft.com/azure/architecture/best-practices/monitoring#collecting-and-storing-data)。 可以使用 [PerfView](https://github.com/Microsoft/perfview/blob/master/README.md) 来处理生成的 ETL 文件，也可以编写自己的程序。 本演练使用 PerfView：

1. 在 PerfView 菜单中，选择 " **收集** &gt; **收集** " 以捕获事件。

1. 在 " **其他提供程序** " 字段中，输入 `*Microsoft-LobAdapter` 以指定 sap 提供程序来捕获 sap 适配器事件。 如果未指定此信息，则跟踪只包含常规 ETW 事件。

1. 保留其他默认设置。 如果需要，可以更改 " **数据文件** " 字段中的文件名或位置。

1. 选择 " **开始收集** " 以开始跟踪。

1. 重现问题或收集了足够的分析数据后，选择 " **停止收集**"。

若要与另一方（如 Azure 支持工程师）共享你的数据，请压缩 ETL 文件。

查看跟踪的内容：

1. 在 PerfView 中，选择 " **文件** &gt; " " **打开** "，然后选择刚刚生成的 ETL 文件。

1. 在 PerfView 侧栏中，ETL 文件下的 " **事件** " 部分。

1. 在 " **筛选**" 下，按筛选， `Microsoft-LobAdapter` 仅查看相关事件和网关进程。

### <a name="test-your-logic-app"></a>测试逻辑应用

1. 若要触发逻辑应用，请从 SAP 系统发送一条消息。

1. 在逻辑应用菜单中，选择“概述”  。 查看逻辑应用的所有新运行的“运行历史记录”。 

1. 打开最近的运行，触发器输出部分会显示从 SAP 系统发送的消息。

### <a name="test-sending-idocs-from-sap"></a>测试从 SAP 发送 Idoc

若要将 Idoc 从 SAP 发送到逻辑应用，需要以下最低配置：

> [!IMPORTANT]
> 仅当使用逻辑应用测试 SAP 配置时，才使用这些步骤。 生产环境需要其他配置。

1. [在 SAP 中配置 RFC 目标](#create-rfc-destination)

1. [创建与 RFC 目标的 ABAP 连接](#create-abap-connection)

1. [创建接收方端口](#create-receiver-port)

1. [创建发送方端口](#create-sender-port)

1. [创建逻辑系统伙伴](#create-logical-system-partner)

1. [创建合作伙伴配置文件](#create-partner-profiles)

1. [测试发送消息](#test-sending-messages)

#### <a name="create-rfc-destination"></a>创建 RFC 目标

1. 若要打开 **RFC 连接** 设置的配置，请在 SAP 接口中使用 **sm59** 事务代码 (包含 **/n** 前缀的 T 代码) 。

1. 选择 " **tcp/ip 连接**" "  >  **创建**"。

1. 使用以下设置创建新的 RFC 目标：
    
    * 对于 **RFC 目标**，输入名称。
    
    * 在 " **技术设置** " 选项卡上，对于 " **激活类型**"，选择 " **已注册服务器程序**"。 对于 " **程序 ID**"，请输入一个值。 在 SAP 中，将使用此标识符来注册逻辑应用的触发器。
    
    * 在 " **unicode** " 选项卡上，对于 " **与目标系统的通信类型**"，选择 " **Unicode**"。

1. 保存所做更改。

1. 将新 **程序 ID** 注册到 Azure 逻辑应用。

1. 若要测试连接，请在 SAP 接口中的新 **RFC 目标** 下，选择 " **连接测试**"。

#### <a name="create-abap-connection"></a>创建 ABAP 连接

1. 若要打开 **RFC 连接** 设置的配置，请在 SAP 接口中使用 **sm59** _ transaction 代码 (包含 _ */n** 前缀的 T 代码) 。

1. 选择 " **ABAP 连接**" "  >  **创建**"。

1. 对于 " **RFC 目标**"，请输入 [测试 SAP 系统](#create-rfc-destination)的标识符。

1. 保存所做更改。

1. 若要测试连接，请选择 " **连接测试**"。

#### <a name="create-receiver-port"></a>创建接收方端口

1. 若要在 **IDOC 处理** 设置中打开端口，请在 SAP 接口中使用具有 **/N** 前缀 (T 代码) 的 **we21** 事务代码。

1. 选择 **"**  >  **事务 RFC**  >  **创建**"。

1. 在打开的 "设置" 框中，选择 " **拥有端口名称**"。 对于测试端口，请输入 **名称**。 保存所做更改。

1. 在新接收方端口的 "设置" 中，为 " **rfc 目标**" 输入 [测试 rfc 目标](#create-rfc-destination)的标识符。

1. 保存所做更改。

#### <a name="create-sender-port"></a>创建发送方端口

1.  若要在 **IDOC 处理** 设置中打开端口，请在 SAP 接口中使用具有 **/N** 前缀 (T 代码) 的 **we21** 事务代码。

1. 选择 **"**  >  **事务 RFC**  >  **创建**"。

1. 在打开的 "设置" 框中，选择 " **拥有端口名称**"。 对于测试端口，请输入一个以 **SAP** 开头的 **名称**。 所有发送程序端口名称必须以字母 **SAP** 开头，例如， **SAPTEST**。 保存所做更改。

1. 在新发送方端口的 "设置" 中，为 " **RFC 目标**" 输入 [ABAP 连接](#create-abap-connection)的标识符。

1. 保存所做更改。

#### <a name="create-logical-system-partner"></a>创建逻辑系统伙伴

1. 若要打开 **更改视图 "逻辑系统"：概述** 设置，请在 SAP 接口中使用 **bd54** 事务代码 (T 代码) 。

1. 接受显示的警告消息： **警告：该表是跨客户端**

1. 在显示现有逻辑系统的列表的上方，选择 " **新项**"。

1. 对于新逻辑系统，输入 **Log.System** 标识符和短 **名称** 说明。 保存所做更改。

1. 出现 **工作台提示** 时，请通过提供说明创建新请求，或者，如果已创建请求，请跳过此步骤。

1. 创建工作台请求后，将该请求链接到表更新请求。 若要确认表已更新，请保存所做的更改。

#### <a name="create-partner-profiles"></a>创建合作伙伴配置文件

对于生产环境，必须创建两个合作伙伴配置文件。 第一个配置文件用于发送方，即你的组织和 SAP 系统。 第二个配置文件用于接收方，这是逻辑应用。

1. 若要打开 " **合作伙伴配置文件** " 设置，请在 SAP 接口中使用 **we20** 事务代码 (包含 **/n** 前缀的 T 代码) 。

1. 在 "**合作伙伴配置文件**" 下，选择 "**合作伙伴类型 LS**  >  **创建**"。

1. 使用以下设置创建新的合作伙伴配置文件：

    * 对于 " **伙伴编号**"，请输入 [逻辑系统伙伴的标识符](#create-logical-system-partner)。

    * 对于 **Partn。键入**，然后输入 **LS**。

    * 对于 " **代理**"，输入在为 Azure 逻辑应用或其他非 SAP 系统注册程序标识符时要使用的 SAP 用户帐户的标识符。

1. 保存所做更改。 如果尚未 [创建逻辑系统伙伴](#create-logical-system-partner)，则会出现错误，请 **输入有效的合作伙伴编号**。

1. 在合作伙伴配置文件的设置中的 " **出站 parmtrs**" 下，选择 " **创建出站参数**"。

1. 使用以下设置创建新的出站参数：

    * 输入 **消息类型**，例如 " **CREMAS**"。

    * 输入 [接收方端口的标识符](#create-receiver-port)。

    * 输入 Pack 的 IDoc 大小 **。大小**。 或者，若要 [从 SAP 一次发送 idoc](#receive-idoc-packets-from-sap)，请选择 " **立即传递 IDoc**"。

1. 保存所做更改。

#### <a name="test-sending-messages"></a>测试发送消息

1. 若要打开 **IDoc 处理设置的测试工具** ，请在 SAP 接口中将 **we19** 事务代码与 **/N** 前缀一起使用 (T 代码) 。

1. 在 " **测试模板**" 下，选择 " **Via 消息类型**"，然后输入消息类型，例如 " **CREMAS**"。 选择“创建”。

1. 通过选择 "**继续**" 确认 "**哪个 IDoc 类型？"** 消息。

1. 选择 **EDIDC** 节点。 为接收方和发送方端口输入合适的值。 选择“继续”。 

1. 选择 **标准出站处理**。

1. 若要启动出站 IDoc 处理，请选择 " **继续**"。 处理完成后，将显示 **发送到 SAP 系统或外部程序** 消息的 IDoc。

1.  若要检查处理错误，请使用 **sm58** 事务代码 (包含 **/N** 前缀的 T 代码) 。

## <a name="receive-idoc-packets-from-sap"></a>接收来自 SAP 的 IDoc 数据包

你可以设置 SAP 以 [在数据包中发送 idoc](https://help.sap.com/viewer/8f3819b0c24149b5959ab31070b64058/7.4.16/4ab38886549a6d8ce10000000a42189c.html)，这些数据包是 idoc 的批处理或组。 若要接收 IDoc 数据包，SAP 连接器，特别是触发器，无需额外配置。 但是，若要在触发器收到数据包后处理 IDoc 数据包中的每个项，则需要执行一些额外的步骤来将数据包拆分为单个 Idoc。

下面是一个示例，演示如何使用[ `xpath()` 函数](./workflow-definition-language-functions-reference.md#xpath)从包中提取单个 idoc：

1. 在开始之前，需要一个带 SAP 触发器的逻辑应用。 如果还没有此逻辑应用，请按照本主题中前面的步骤 [使用 SAP 触发器设置逻辑应用](#receive-message-from-sap)。

   例如：

   ![向逻辑应用添加 SAP 触发器](./media/logic-apps-using-sap-connector/first-step-trigger.png)

1. 从你的逻辑应用从 SAP 接收的 XML IDoc 获取根命名空间。 若要从 XML 文档提取该命名空间，请添加一个步骤，使用 `xpath()` 表达式创建本地字符串变量并存储该命名空间：

   `xpath(xml(triggerBody()?['Content']), 'namespace-uri(/*)')`

   ![从 IDoc 获取根命名空间](./media/logic-apps-using-sap-connector/get-namespace.png)

1. 若要提取单个 IDoc，请添加一个步骤，该步骤创建一个数组变量，并使用另一个表达式存储 IDoc 集合 `xpath()` ：

   `xpath(xml(triggerBody()?['Content']), '/*[local-name()="Receive"]/*[local-name()="idocData"]')`

   ![获取项的数组](./media/logic-apps-using-sap-connector/get-array.png)

   数组变量使每个 IDoc 可供逻辑应用通过枚举集合来单独处理。 在此示例中，逻辑应用使用循环将每个 IDoc 传输到 SFTP 服务器：

   ![将 IDoc 发送到 SFTP 服务器](./media/logic-apps-using-sap-connector/loop-batch.png)

   每个 IDoc 都必须包含根命名空间，这是 `<Receive></Receive` 在这种情况下，在将 IDoc 发送到下游应用或 SFTP 服务器之前，文件内容与根命名空间一起包装在元素内的原因。

创建新的逻辑应用时，可以通过在逻辑应用设计器中选择此模板来使用此模式的快速入门模板。

![选择批处理逻辑应用模板](./media/logic-apps-using-sap-connector/select-batch-logic-app-template.png)

## <a name="generate-schemas-for-artifacts-in-sap"></a>为 SAP 中的项目生成架构

本示例使用可通过 HTTP 请求触发的逻辑应用。 若要为指定的 IDoc 和 BAPI 生成架构，SAP 操作 **生成架构** 会将请求发送到 sap 系统。

此 SAP 操作返回 [xml 架构](#sample-xml-schemas)，而不是 xml 文档本身的内容或数据。 在响应中返回的架构使用 Azure 资源管理器连接器上传到集成帐户。 架构包含以下部分：

* 请求消息的结构。 使用此信息来形成你的 BAPI `get` 列表。

* 响应消息的结构。 使用此信息来分析响应。 

若要发送请求消息，请使用一般 SAP 操作向 **SAP 发送消息**，或使用目标 **调用 BAPI** 操作。

### <a name="sample-xml-schemas"></a>示例 XML 架构

如果要了解如何生成用于创建示例文档的 XML 架构，请参阅以下示例。 这些示例演示了如何使用多种类型的负载，包括：

* [RFC 请求](#xml-samples-for-rfc-requests)

* [BAPI 请求](#xml-samples-for-bapi-requests)

* [IDoc 请求](#xml-samples-for-idoc-requests)

* 简单或复杂的 XML 架构数据类型

* 表参数

* 可选的 XML 行为

可以使用可选的 XML 序言开始 XML 架构。 SAP 连接器适用于或不使用 XML prolog。

```xml

<?xml version="1.0" encoding="utf-8">

```

#### <a name="xml-samples-for-rfc-requests"></a>RFC 请求的 XML 示例

下面的示例是一个基本的 RFC 调用。 RFC 名称是 `STFC_CONNECTION` 。 此请求使用默认命名空间 `xmlns=` ，但是，可以分配并使用命名空间别名，如 `xmmlns:exampleAlias=` 。 命名空间值是适用于 Microsoft 服务的 SAP 中所有 Rfc 的命名空间。 请求中有一个简单的输入参数 `<REQUTEXT>` 。

```xml

<STFC_CONNECTION xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <REQUTEXT>exampleInput</REQUTEXT>
</STFC_CONNECTION>

```

下面的示例是一个带有表参数的 RFC 调用。 此示例调用及其测试 Rfc 组作为所有 SAP 系统的一部分提供。 表参数的名称为 `TCPICDAT` 。 表行类型为 `ABAPTEXT` ，此元素对于表中的每一行重复。 此示例包含一个名为的行 `LINE` 。 带有表参数的请求可以包含任意数目的字段，其中，数值为正整数 (*n*) 。 

```xml

<STFC_WRITE_TO_TCPIC xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <RESTART_QNAME>exampleQName</RESTART_QNAME>
    <TCPICDAT>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
        <LINE>exampleFieldInput1</LINE>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
        <LINE>exampleFieldInput2</LINE>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
        <LINE>exampleFieldInput3</LINE>
      </ABAPTEXT>
    </TCPICDAT>
</STFC_WRITE_TO_TCPIC>

```

下面的示例是一个 RFC 调用，其中包含具有匿名字段的表参数。 匿名字段是指未分配名称的字段。 复杂类型在单独的命名空间中声明，在该命名空间中声明为当前节点及其所有子元素设置新的默认值。 此示例使用十六进制代码 `x002F` 作为符号的转义符 */* ，因为在 SAP 字段名称中保留了此符号。

```xml

<RFC_XML_TEST_1 xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <IM_XML_TABLE>
    <RFC_XMLCNT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
      <_x002F_AnonymousField>exampleFieldInput</_x002F_AnonymousField>
    </RFC_XMLCNT>
  </IM_XML_TABLE>
</RFC_XML_TEST_1>

```

下面的示例包含命名空间的前缀。 可以一次声明所有前缀，也可以声明任意数量的前缀作为节点的属性。 RFC 命名空间别名用作 `ns0` 基本类型的根和参数。

> [!NOTE]
> 对于带有别名的 RFC 类型， `ns3` 而不是使用别名的常规 rfc 命名空间，在不同的命名空间中声明复杂类型 `ns0` 。

```xml

<ns0:BBP_RFC_READ_TABLE xmlns:ns0="http://Microsoft.LobServices.Sap/2007/03/Rfc/" xmlns:ns3="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc/">
  <ns0:DELIMITER>0</ns0:DELIMITER>
  <ns0:QUERY_TABLE>KNA1</ns0:QUERY_TABLE>
  <ns0:ROWCOUNT>250</ns0:ROWCOUNT>
  <ns0:ROWSKIPS>0</ns0:ROWSKIPS>
  <ns0:FIELDS>
    <ns3:RFC_DB_FLD>
      <ns3:FIELDNAME>KUNNR</ns3:FIELDNAME>
    </ns3:RFC_DB_FLD>
  </ns0:FIELDS>
</ns0:BBP_RFC_READ_TABLE>

```

#### <a name="xml-samples-for-bapi-requests"></a>BAPI 请求的 XML 示例

下面的 XML 示例是用于 [调用 BAPI 方法的](#call-bapi-action)示例请求。

> [!NOTE]
> SAP 使业务对象可用于外部系统，方法是对其进行描述，以响应 RFC `RPY_BOR_TREE_INIT` ，哪些逻辑应用在没有输入筛选器时出现问题。 逻辑应用检查输出表 `BOR_TREE` 。 此 `SHORT_TEXT` 字段用于业务对象的名称。 逻辑应用无法访问输出表中 SAP 返回的业务对象。
> 
> 如果使用自定义业务对象，则必须确保在 SAP 中发布和发布这些业务对象。 否则，SAP 不会在输出表中列出自定义业务对象 `BOR_TREE` 。 在从 SAP 公开业务对象之前，无法访问逻辑应用中的自定义业务对象。 

下面的示例使用 BAPI 方法获取银行列表 `GETLIST` 。 此示例包含银行的业务对象 `BUS1011` 。 

```xml

<GETLIST xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_CTRY>US</BANK_CTRY>
  <MAX_ROWS>10</MAX_ROWS>
</GETLIST>

```

下面的示例使用方法创建一个 bank 对象 `CREATE` 。 此示例使用与前面的示例相同的业务对象 `BUS1011` 。 使用 `CREATE` 方法创建银行时，请确保提交所做的更改，因为默认情况下不会提交此方法。

> [!TIP]
> 确保你的 XML 文档遵循 SAP 系统中配置的任何验证规则。 例如，在此示例文档中，银行密钥 (`<BANK_KEY>`) 需要是银行银行代号，在美国也称为 ABA 号码。

```xml

<CREATE xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_ADDRESS>
    <BANK_NAME xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleBankName</BANK_NAME>
    <REGION xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleRegionName</REGION>
    <STREET xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleStreetAddress</STREET>
    <CITY xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">Redmond</CITY>
  </BANK_ADDRESS>
  <BANK_COUNTRY>US</BANK_COUNTRY>
  <BANK_KEY>123456789</BANK_KEY>
</CREATE>

```

下面的示例使用银行电汇号获取银行的详细信息，为的值 `<BANK_KEY>` 。 

```xml

<GETDETAIL xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_COUNTRY>US</BANK_COUNTRY>
  <BANK_KEY>123456789</BANK_KEY>
</GETDETAIL>

```

#### <a name="xml-samples-for-idoc-requests"></a>IDoc 请求的 XML 示例

若要生成无格式的 SAP IDoc XML 架构，请使用 **Sap 登录** 应用程序和 T 代码 `WE-60` 。 通过 GUI 访问 SAP 文档，并以 XSD 格式为 IDoc 类型和扩展生成 XML 架构。 有关一般 SAP 格式和负载的说明及其内置对话框，请参阅 [SAP 文档](https://help.sap.com/viewer/index)。

此示例声明根节点和命名空间。 示例代码中的 URI `http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700/Send` 声明以下配置：

* `/IDoc` 是所有 Idoc 的根注释
* `/3` 常见段定义的记录类型版本
* `/ORDERS05` 是 IDoc 类型
* `//` 为空段，因为没有 IDoc 扩展
* `/700` SAP 版本
* `/Send` 用于将信息发送到 SAP 的操作

```xml

<ns0:Send xmlns:ns0="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700/Send" xmlns:ns3="http://schemas.microsoft.com/2003/10/Serialization" xmlns:ns1="http://Microsoft.LobServices.Sap/2007/03/Types/Idoc/Common/" xmlns:ns2="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700">
  <ns0:idocData>

```

可以重复该 `idocData` 节点，通过一次调用发送一批 idoc。 在下面的示例中，有一个控件记录、 `EDI_DC40` 和多个数据记录。

```xml

<...>
  <ns0:idocData>
    <ns2:EDI_DC40>
      <ns1:TABNAM>EDI_DC40</ns1:TABNAM>
<...>
      <ns1:ARCKEY>Cor1908207-5</ns1:ARCKEY>
    </ns2:EDI_DC40>
    <ns2:E2EDK01005>
      <ns2:DATAHEADERCOLUMN_SEGNAM>E23DK01005</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:CURCY>USD</ns2:CURCY>
    </ns2:E2EDK01005>
    <ns2:E2EDK03>
<...>
  </ns0:idocData>

```

下面的示例是一个示例 IDoc 控件记录，它使用前缀 `EDI_DC` 。 必须更新值才能匹配 SAP 安装和 IDoc 类型。 例如，你的 IDoc 客户端代码可能不是 `800` 。 请与 SAP 团队联系，确保为 SAP 安装使用正确的值。

```xml

<ns2:EDI_DC40>
  <ns:TABNAM>EDI_DC40</ns1:TABNAM>
  <ns:MANDT>800</ns1:MANDT>
  <ns:DIRECT>2</ns1:DIRECT>
  <ns:IDOCTYP>ORDERS05</ns1:IDOCTYP>
  <ns:CIMTYP></ns1:CIMTYP>
  <ns:MESTYP>ORDERS</ns1:MESTYP>
  <ns:STD>X</ns1:STD>
  <ns:STDVRS>004010</ns1:STDVRS>
  <ns:STDMES></ns1:STDMES>
  <ns:SNDPOR>SAPENI</ns1:SNDPOR>
  <ns:SNDPRT>LS</ns1:SNDPRT>
  <ns:SNDPFC>AG</ns1:SNDPFC>
  <ns:SNDPRN>ABAP1PXP1</ns1:SNDPRN>
  <ns:SNDLAD></ns1:SNDLAD>
  <ns:RCVPOR>BTSFILE</ns1:RCVPOR>
  <ns:RCVPRT>LI</ns1:RCVPRT>

```

下面的示例是一个包含普通段的示例数据记录。 此示例使用 SAP 日期格式。 强类型文档可以使用本机 XML 日期格式，如 `2020-12-31 23:59:59` 。

```xml

<ns2:E2EDK01005>
  <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDK01005</ns2:DATAHEADERCOLUMN_SEGNAM>
    <ns2:CURCY>USD</ns2:CURCY>
    <ns2:BSART>OR</ns2:BSART>
    <ns2:BELNR>1908207-5</ns2:BELNR>
    <ns2:ABLAD>CC</ns2:ABLAD>
  </ns2>
  <ns2:E2EDK03>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDK03</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:IDDAT>002</ns2:IDDAT>
      <ns2:DATUM>20160611</ns2:DATUM>
  </ns2:E2EDK03>

```

下面的示例是包含分组段的数据记录。 记录包含组父节点、 `E2EDKT1002GRP` 和多个子节点，包括 `E2EDKT1002` 和 `E2EDKT2001` 。 

```xml

<ns2:E2EDKT1002GRP>
  <ns2:E2EDKT1002>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDKT1002</ns2:DATAHEADERCOLUMN_SEGNAM>
      <NS2:TDID>ZONE</ns2:TDID>
  </ns2:E2EDKT1002>
  <ns2:E2EDKT2001>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDKT2001</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:TDLINE>CRSD</ns2:TDLINE>
  </ns2:E2EDKT2001>
</ns2:E2EDKT1002GRP>

```

推荐的方法是创建 IDoc 标识符以用于 tRFC。 您可以 `tid` 使用 SAP 连接器 API 中的 [Send IDoc 操作](/connectors/sap/#send-idoc) 来设置此事务标识符。

下面的示例是用于设置事务标识符或的另一种方法 `tid` 。 在此示例中，最后一个数据记录段节点和 IDoc 数据节点已关闭。 然后， `guid` 使用 GUID 作为 tRFC 标识符来检测重复项。 

```xml

    </E2STZUM002GRP>
  </idocData>
  <guid>8820ea40-5825-4b2f-ac3c-b83adc34321c</guid>
</Send>

```

### <a name="add-an-http-request-trigger"></a>添加 HTTP 请求触发器

1. 在 Azure 门户中，创建一个空白逻辑应用，用于打开逻辑应用设计器。

1. 在搜索框中，输入 `http request` 作为筛选器。 在触发器列表中，选择“当收到 HTTP 请求时”。  

   ![添加 HTTP 请求触发器](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. 现在请保存逻辑应用，以便为逻辑应用生成终结点 URL。
在设计器工具栏上选择“保存”。 

   终结点 URL 现在会显示在触发器中，例如：

   ![生成终结点的 URL](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-an-sap-action-to-generate-schemas"></a>添加 SAP 操作以生成架构

1. 在逻辑应用设计器中的触发器下，选择 " **新建步骤**"。

   ![向逻辑应用添加新步骤](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. 在搜索框中，输入 `sap` 作为筛选器。 在“操作”列表中选择“生成架构”。  
  
   ![向逻辑应用添加 "生成架构" 操作](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   也可以选择“企业”选项卡，然后选择 SAP 操作。 

   ![在“企业”选项卡中选择 SAP 发送操作](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. 如果连接已存在，请继续下一步，以便可以设置 SAP 操作。 但是，如果系统提示输入连接详细信息，请提供该信息，以便立即创建到本地 SAP 服务器的连接。

   1. 为连接提供一个名称。

   1. 在 " **数据网关** " 部分中，在 " **订阅**" 下，先为你在数据网关安装的 Azure 门户中创建的 "数据网关" 资源选择 Azure 订阅。 
   
   1. 在 " **连接网关**" 下，在 Azure 中选择数据网关资源。

   1. 继续提供有关连接的信息。 对于“登录类型”属性，请根据该属性是设置为“应用程序服务器”还是“组”来执行相关步骤：   
   
      * 对于“应用程序服务器”，必须指定以下属性（通常显示为可选）： 

        ![创建 SAP 应用程序服务器连接](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * 对于“组”，必须指定以下属性（通常显示为可选）： 

        ![创建 SAP 消息服务器连接](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      默认会使用强类型化通过针对架构执行 XML 验证来检查无效值。 此行为可帮助提前检测问题。 “安全类型化”选项用于实现后向兼容，它只会检查字符串长度。  详细了解[“安全类型化”选项](#safe-typing)。

   1. 完成后，选择“创建”  。

      逻辑应用会设置并测试连接，确保连接正常工作。

1. 提供要为其生成架构的项目的路径。

   可以通过文件选取器选择 SAP 操作：

   ![选择 SAP 操作](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   或者，可以手动输入操作：

   ![手动输入 SAP 操作](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png)

   若要为多个项目生成架构，请提供每个项目的 SAP 操作详细信息，例如：

   ![选择“添加新项”](media/logic-apps-using-sap-connector/schema-generator-array-pick.png)

   ![显示两个项](media/logic-apps-using-sap-connector/schema-generator-example.png)

   有关 SAP 操作的详细信息，请参阅 [IDoc 操作的消息架构](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)。

1. 保存逻辑应用。 在设计器工具栏上选择“保存”。 

### <a name="test-your-logic-app"></a>测试逻辑应用

1. 在设计器工具栏上，选择“运行”以触发逻辑应用的运行。 

1. 打开该运行，并检查“生成架构”操作的输出。 

   输出中会显示针对指定的消息列表生成的架构。

### <a name="upload-schemas-to-an-integration-account"></a>将架构上传到集成帐户

（可选）可以下载生成的架构，或将其存储在 Blob、存储或集成帐户等存储库中。 集成帐户为其他 XML 操作提供一流的体验。本示例演示如何使用 Azure 资源管理器连接器将架构上传到同一逻辑应用的集成帐户。

1. 在逻辑应用设计器中的触发器下，选择 " **新建步骤**"。

1. 在搜索框中，输入 `Resource Manager` 作为筛选器。 选择“创建或更新资源”。 

   ![选择 Azure 资源管理器操作](media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. 输入操作的详细信息，包括 Azure 订阅、Azure 资源组和集成帐户。 若要将 SAP 令牌添加到字段，请在这些字段对应的框中单击，然后从显示的动态内容列表中选择。

   1. 打开“添加新参数”列表，然后选择“位置”和“属性”字段。   

   1. 按此示例所示，提供这些新字段的详细信息。

      ![输入 Azure 资源管理器操作的详细信息](media/logic-apps-using-sap-connector/azure-resource-manager-action.png)

   SAP 的“生成架构”操作会生成集合形式的架构，因此，设计器会自动将一个 **For each** 循环添加到该操作。  以下示例演示此操作的显示方式：

   ![包含“for each”循环的 Azure 资源管理器操作](media/logic-apps-using-sap-connector/azure-resource-manager-action-foreach.png)

   > [!NOTE]
   > 架构使用 base64 编码格式。 若要将架构上传到集成帐户，必须使用 `base64ToString()` 函数将其解码。 以下示例演示了 `"properties"` 元素的代码：
   >
   > ```json
   > "properties": {
   >    "Content": "@base64ToString(items('For_each')?['Content'])",
   >    "ContentType": "application/xml",
   >    "SchemaType": "Xml"
   > }
   > ```

1. 保存逻辑应用。 在设计器工具栏上选择“保存”。 

### <a name="test-your-logic-app"></a>测试逻辑应用

1. 在设计器工具栏上，选择“运行”以手动触发逻辑应用。 

1. 成功运行后，转到集成帐户，并检查生成的架构是否存在。

## <a name="enable-secure-network-communications"></a>启用安全网络通信

在开始之前，请确保满足前面列出的 [先决条件](#prerequisites)，这仅适用于使用数据网关和逻辑应用在多租户 Azure 中运行的情况：

* 请确保将本地数据网关安装在与 SAP 系统位于同一网络中的计算机上。

* 对于 SSO，数据网关以映射到 SAP 用户的用户身份运行。

* 提供其他安全功能的 SNC 库已安装在数据网关所在的同一台计算机上。 部分示例包括 [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm)、Kerberos 和 NTLM。

   若要对传入和传出 SAP 系统的请求启用 SNC，请在 SAP 连接中选中“使用 SNC”复选框，并提供以下属性： 

   ![在连接中配置 SAP SNC](media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | 属性 | 说明 |
   |----------| ------------|
   | **SNC 库路径** | SNC 库的名称、相对于 NCo 安装位置的路径或绝对路径。 例如 `sapsnc.dll`、`.\security\sapsnc.dll` 或 `c:\security\sapsnc.dll`。 |
   | **SNC SSO** | 通过 SNC 进行连接时，SNC 标识通常用于对调用方进行身份验证。 另一个选项是重写，以便可以使用用户和密码信息对调用方进行身份验证，但该行仍会加密。 |
   | **我的 SNC 名称** | 在大多数情况下可以省略此属性。 安装的 SNC 解决方案通常知道自己的 SNC 名称。 只有对于支持“多个标识”的解决方案，才需要指定用于此特定目标或服务器的标识。 |
   | **SNC 合作伙伴名称** | 后端 SNC 的名称。 |
   | **SNC 保护质量** | 此特定目标或服务器的 SNC 通信使用的服务质量。 默认值由后端系统定义。 最大值由 SNC 使用的安全产品定义。 |
   |||

   > [!NOTE]
   > 不要在装有数据网关和 SNC 库的计算机上设置 SNC_LIB 和 SNC_LIB_64 环境变量。 如果已设置这些环境变量，它们会优先于通过连接器传递的 SNC 库值。

## <a name="safe-typing"></a>安全类型化

创建 SAP 连接时，默认会使用强类型化通过针对架构执行 XML 验证来检查无效值。 此行为可帮助提前检测问题。 “安全类型化”选项用于实现后向兼容，它只会检查字符串长度。  如果选择“安全类型化”，则 SAP 中的 DATS 类型和 TIMS 类型将被视为字符串而不是其 XML 等效形式 `xs:date` 和 `xs:time`，其中 `xmlns:xs="http://www.w3.org/2001/XMLSchema"`。  安全类型化会影响所有架构生成操作、“被发送”有效负载和“被接收”响应的发送消息，以及触发器的行为。 

使用强类型化时（未启用 **安全类型化**），架构会将 DATS 和 TIMS 类型映射到更简单的 XML 类型：

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

使用强类型化发送消息时，DATS 和 TIMS 响应符合匹配的 XML 类型格式：

```xml
<DATE>9999-12-31</DATE>
<TIME>23:59:59</TIME>
```

启用 **安全类型化** 时，架构会将 DATS 和 TIMS 类型映射到只施加长度限制的 XML 字符串字段，例如：

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="8" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="6" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
```

在启用 **安全类型化** 的情况下发送消息时，DATS 和 TIMS 响应如以下示例所示：

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```

## <a name="advanced-scenarios"></a>高级方案

### <a name="change-language-headers"></a>更改语言标头

从逻辑应用连接到 SAP 时，连接的默认语言为英语。 可以通过将[标准 HTTP 标头 `Accept-Language` ](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.4)与入站请求一起使用，为连接设置语言。

> [!TIP]
> 大多数 web 浏览器会根据 `Accept-Language` 用户的设置来添加标头。 在逻辑应用设计器中创建新的 SAP 连接时，web 浏览器会应用此标头。 如果不想在 web 浏览器的首选语言中创建 SAP 连接，请更新 web 浏览器的设置以使用首选语言，或使用 Azure 资源管理器而不是逻辑应用设计器创建 SAP 连接。 

例如，可以 `Accept-Language` 通过使用 **HTTP 请求** 触发器，将带标头的请求发送到逻辑应用。 逻辑应用中的所有操作都将接收该标头。 然后，SAP 在其系统消息中使用指定的语言，如 BAPI 错误消息。

逻辑应用的 SAP 连接参数没有 language 属性。 如果使用 `Accept-Language` 标题，则可能会收到以下错误： **请检查你的帐户信息和/或权限，然后重试。** 在这种情况下，请改为检查 SAP 组件的错误日志。 此错误实际上发生在使用标头的 SAP 组件中，因此你可能会收到以下错误消息之一：

* `"SAP.Middleware.Connector.RfcLogonException: Select one of the installed languages"`
* `"SAP.Middleware.Connector.RfcAbapMessageException: Select one of the installed languages"`

### <a name="confirm-transaction-explicitly"></a>显式确认事务

将事务从逻辑应用发送到 SAP 时，此交换以两步方式完成，详见 SAP 文档：[Transactional RFC Server Programs](https://help.sap.com/doc/saphelp_nwpi71/7.1/22/042ad7488911d189490000e829fbbd/content.htm?no_cache=true)（事务性 RFC 服务器程序）。 默认情况下，“发送到 SAP”操作  在单个调用中处理函数传输步骤和事务确认步骤。 SAP 连接器提供将这两个步骤分离的选项。 您可以发送 IDoc，而不是自动确认该事务，您可以使用显式 **确认事务 ID** 操作。

不想在 SAP 中复制事务时（例如，在由于网络问题之类的原因而导致故障的情况下），可以使用这个分离事务 ID 确认的功能。 采用单独确认事务 ID 的方式，事务只在 SAP 系统中完成一次。

下面以示例方式展示了此模式：

1. 创建空的逻辑应用并添加 HTTP 触发器。

1. 在 SAP 连接器中，添加“发送 IDOC”操作。  提供发送到 SAP 系统的 IDoc 的详细信息。

1. 若要在单独的步骤中显式确认事务 ID，请在“确认 TID”字段中选择“否”。   对于可选的“事务 ID GUID”字段，  可以手动指定值，也可以让连接器自动生成该 GUID 并在“发送 IDOC”操作的响应中将其返回。

   ![“发送 IDOC”操作的属性](./media/logic-apps-using-sap-connector/send-idoc-action-details.png)

1. 若要显式确认事务 ID，请添加 " **确认事务 id** " 操作，以确保 [避免向 SAP 发送重复的 idoc](#avoid-sending-duplicate-idocs)。 在“事务 ID”框中单击，以显示动态内容列表。  在该列表中选择从“发送 IDOC”操作返回的“事务 ID”值。  

   ![“确认事务 ID”操作](./media/logic-apps-using-sap-connector/explicit-transaction-id.png)

   此步骤完成以后，会在两端（SAP 连接器端和 SAP 系统端）将当前的事务标记为完成。

#### <a name="avoid-sending-duplicate-idocs"></a>避免发送重复的 Idoc

如果从逻辑应用向 SAP 发送重复的 Idoc 时遇到问题，请按照以下步骤创建一个字符串变量，用作 IDoc 事务标识符。 如果存在问题（如暂时停机、网络问题或确认丢失），则创建此事务标识符可帮助防止重复的网络传输。

> [!NOTE]
> SAP 系统在指定时间之后忘记了事务标识符，默认情况下，则为24小时。 因此，如果 ID 或 GUID 未知，SAP 将无法确认事务标识符。
> 如果事务标识符的确认失败，则表明与 SAP 系统之间的通信失败，SAP 才能确认确认。

1. 在逻辑应用设计器中，将操作 **Initialize 变量** 添加到逻辑应用。 

1. 在 "操作 **初始化变量**" 的编辑器中，配置以下设置。 然后，保存所做的更改。

    1. 对于 " **名称**"，请输入变量的名称。 例如 `IDOCtransferID`。

    1. 对于 " **类型**"，选择 " **字符串** " 作为变量类型。

    1. 对于 " **值**"，请选择 " **输入初始值** " 文本框，以打开 "动态内容" 菜单。 选择 " **表达式** " 选项卡。在函数列表中，输入函数 `guid()` 。 然后，选择 **"确定"** 保存更改。 现在，" **值** " 字段设置为 `guid()` 函数，该函数将生成 GUID。

1. 在 " **初始化变量** " 操作后，添加 " **发送 IDOC**" 操作。

1. 在操作 **发送 IDOC** 的编辑器中，配置以下设置。 然后，保存所做的更改。

    1. 对于 " **IDOC 类型** "，选择 "消息类型"，并为 " **输入 IDOC 消息**" 指定消息。

    1. 对于 " **sap 发行版本**"，请选择 sap 配置的值。

    1. 对于 " **记录类型" 版本**，请选择 SAP 配置的值。

    1. 对于 " **确认 TID**"，请选择 " **否**"。

    1. 选择 "**添加新参数列表**  >  **事务 ID GUID**"。 选择该文本框以打开 "动态内容" 菜单。 在 " **变量** " 选项卡下，选择所创建的变量的名称。 例如 `IDOCtransferID`。

1. 在操作的标题栏上 **，选择**   >  "发送 IDOC"**设置**。 对于 **重试策略**，建议选择 " **默认** &gt; **完成**"。 不过，你可以根据自己的特定需求来配置自定义策略。 对于自定义策略，建议至少配置一次重试，以克服暂时的网络故障。

1. 在操作 **发送 IDOC** 后，添加操作 **确认事务 ID**。

1. 在 "操作 **确认事务 ID**" 的编辑器中，配置以下设置。 然后，保存所做的更改。

    1. 对于 " **事务 ID**"，请再次输入变量的名称。 例如 `IDOCtransferID`。

1. （可选）在测试环境中验证重复数据删除。 使用在上一步中使用的相同 **事务 ID** GUID 重复 **Send IDOC** 操作。 当你同时发送相同的 IDoc 时，可以验证 SAP 是否能够识别 tRFC 调用的重复项，并将两次调用解析为单个入站 IDoc 消息。

## <a name="known-issues-and-limitations"></a>已知问题和限制

下面是托管 (非 ISE) SAP 连接器的当前已知问题和限制：

* SAP 触发器不支持数据网关群集。 在某些故障转移案例中，与 SAP 系统通信的数据网关节点可能不同于主动节点，从而导致意外的行为。 对于“发送”方案，支持使用数据网关群集。

* SAP 连接器目前不支持 SAP 路由器字符串。 本地数据网关必须位于要连接的 SAP 系统所在的同一 LAN 中。

## <a name="connector-reference"></a>连接器参考

有关此连接器的更多技术详细信息，例如触发器、操作和限制（如此连接器的 Swagger 文件所述），请参阅[连接器的参考页](/connectors/sap/)。 为以下操作提供了适用于逻辑应用的其他文档：

* [调用 BAPI](#call-bapi-action)

* [发送 IDOC](#send-idoc-action)

> [!NOTE]
> 对于 [integration service 环境 ](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中的逻辑应用 (ISE) ，此连接器的基于 ise 标记的版本改为使用 [ise 消息限制](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) 。

### <a name="call-bapi-action"></a>调用 BAPI 操作

[ `CallBapi`) 操作 (调用 BAPI](
https://docs.microsoft.com/connectors/sap/#call-bapi-(preview))在 SAP 服务器上调用 BAPI 方法。 

你必须在调用中使用以下参数： 

* **Business Object** (`businessObject`) ，它是一个可搜索的下拉菜单。

* **方法** (`method`) ，它在你选择 **业务对象** 之后填充可用方法。 可用的方法根据所选 **业务对象** 的不同而异。

* **输入 BAPI 参数** (`body`) ，在这种情况下，你可以调用包含调用的 BAPI 方法输入参数值的 XML 文档，或包含 BAPI 参数的存储 blob 的 URI。

有关如何使用 Call BAPI 操作的详细示例，请参阅 [BAPI 请求的 XML 示例](#xml-samples-for-bapi-requests)。

> [!TIP]
> 如果使用逻辑应用设计器编辑 BAPI 请求，则可以使用以下搜索功能： 
> 
> * 在设计器中选择一个对象可查看可用方法的下拉菜单。
> * 使用 BAPI API 调用提供的可搜索列表按关键字筛选业务对象类型。

### <a name="send-idoc-action"></a>发送 IDoc 操作

[Send IDoc (`SendIDoc`) ](https://docs.microsoft.com/connectors/sap/#send-idoc-(preview))操作会将 IDoc 消息发送到 SAP 服务器。

你必须在调用中使用以下参数： 

* **具有可选扩展的 IDOC 类型** (`idocType`) ，这是一个可搜索的下拉菜单。

    * 可选参数 **SAP release 版本** (`releaseVersion`) 在选择 IDoc 类型后填充值，并依赖于所选的 IDoc 类型。

* **输入 IDOC message** (`body`) ，你可以在其中调用包含 IDOC 负载的 XML 文档或包含 IDOC XML 文档的存储 blob 的 URI。 本文档必须符合 WE60 IDoc 文档中的 SAP IDOC XML 架构或匹配的 SAP IDoc 操作 URI 生成的架构。

有关如何使用 Send IDoc 操作的详细示例，请参阅将 [IDoc 消息发送到 SAP 服务器的演练](#send-idoc-messages-to-sap-server)。

有关如何使用可选参数 **确认 TID** () 的详细说明 `confirmTid` ，请参阅 [显式确认事务的演练](#confirm-transaction-explicitly)。

## <a name="next-steps"></a>后续步骤

* 从 Azure 逻辑应用[连接到本地系统](../logic-apps/logic-apps-gateway-connection.md)。

* 了解如何使用 [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)验证、转换和使用其他消息操作。

* 了解其他 [逻辑应用连接器](../connectors/apis-list.md)。
