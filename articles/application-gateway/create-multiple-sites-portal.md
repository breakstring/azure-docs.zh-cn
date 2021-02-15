---
title: 教程：使用 Azure 门户托管多个网站
titleSuffix: Azure Application Gateway
description: 本教程介绍了如何使用 Azure 门户创建托管多个网站的应用程序网关。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 08/21/2020
ms.author: victorh
ms.openlocfilehash: 16f55dc88ed2d2d019a2fed355a14741263c20af
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397597"
---
# <a name="tutorial-create-and-configure-an-application-gateway-to-host-multiple-web-sites-using-the-azure-portal"></a>教程：使用 Azure 门户创建和配置托管多个网站的应用程序网关

创建[应用程序网关](overview.md)时，可以使用 Azure 门户[配置多个网站的托管](multiple-site-overview.md)。 本教程使用虚拟机定义后端地址池。 然后，基于所拥有的域配置侦听器和规则，以确保 Web 流量可到达池中的相应服务器。 本教程假定你拥有多个域，并使用示例 *www.contoso.com* 和 *www.fabrikam.com* 。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建应用程序网关
> * 为后端服务器创建虚拟机
> * 使用后端服务器创建后端池
> * 创建后端侦听器
> * 创建路由规则
> * 在域中创建 CNAME 记录

:::image type="content" source="./media/create-multiple-sites-portal/scenario.png" alt-text="多站点应用程序网关":::

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="create-an-application-gateway"></a>创建应用程序网关

1. 选择 Azure 门户左侧菜单上的“创建资源”  。 此时会显示“新建”窗口。

2. 选择“网络”  ，然后在“特色”列表中选择“应用程序网关”   。

### <a name="basics-tab"></a>“基本信息”选项卡

1. 在“基本信息”选项卡上，输入这些值作为以下应用程序网关设置  ：

   - **资源组** ：选择 **myResourceGroupAG** 作为资源组。 如果该资源组不存在，请选择“新建”，创建一个新的  。
   - **应用程序网关名称** ：输入 *myAppGateway* 作为应用程序网关的名称。

     :::image type="content" source="./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png" alt-text="创建应用程序网关":::

2.  Azure 需要一个虚拟网络才能在创建的资源之间通信。 可以创建新的虚拟网络，也可以使用现有的虚拟网络。 在此示例中，将在创建应用程序网关的同时创建新的虚拟网络。 在不同的子网中创建应用程序网关实例。 在本示例中创建两个子网：一个用于应用程序网关，另一个用于后端服务器。

    在“配置虚拟网络”下，选择“新建”以创建新的虚拟网络   。 在打开的“创建虚拟网络”窗口中，输入以下值以创建虚拟网络和两个子网  ：

    - **Name** ：输入 *myVNet* 作为虚拟网络的名称。

    - **子网名称** （应用程序网关子网）：子网网关将显示名为“默认值”的子网   。 将此子网的名称更改为 myAGSubnet  。<br>应用程序网关子网只能包含应用程序网关。 不允许其他资源。

    - **子网名称** （后端服务器子网）：在子网网关的第二行中，在“子网名称”列输入“myBackendSubnet”    。

    - **地址范围** （后端服务器子网）：在子网网格的第二行中，输入不会与 myAGSubnet 的地址范围重叠的地址范围   。 例如，如果 myAGSubnet 的地址范围为 10.0.0.0/24，则为 myBackendSubnet 的地址范围输入 10.0.1.0/24    。

    选择“确定”以关闭“创建虚拟网络”窗口，并保存虚拟网络设置   。

     :::image type="content" source="./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png" alt-text="创建 VNet":::
    
3. 在“基本信息”  选项卡上，接受其他设置的默认值，然后选择“下一步:  前端”。

### <a name="frontends-tab"></a>“前端”选项卡

1. 在“前端”选项卡上，验证“IP 地址类型”是否设置为“公共”    。 <br>可以根据用例将前端 IP 配置为公共或专用。 本示例将选择公共前端 IP。
   > [!NOTE]
   > 对于应用程序网关 v2 SKU，只能选择 **公共** 前端 IP 配置。 目前尚未为此 v2 SKU 启用专用前端 IP 配置。

2. 为“公共 IP 地址”选择“新建”，输入“myAGPublicIPAddress”作为公共 IP 地址名称，然后选择“确定”     。 

     :::image type="content" source="./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png" alt-text="创建另一个 VNet":::

3. 在完成时选择“下一步: **后端** 。

### <a name="backends-tab"></a>“后端”选项卡

后端池用于将请求路由到为请求提供服务的后端服务器。 后端池可以包括 NIC、虚拟机规模集、公共 IP、内部 IP、完全限定的域名 (FQDN) 和多租户后端（例如 Azure 应用服务）。 在此示例中，将使用应用程序网关创建空的后端池，然后将后端目标添加到后端池。

1. 在“后端”选项卡上，选择“+添加后端池”   。

2. 在打开的“添加后端池”窗口中，输入以下值以创建空的后端池  ：

    - **Name** ：输入“contosoPool”  作为后端池的名称。
    - **添加不包含目标的后端池** ：选择“是”以创建不包含目标的后端池  。 你将在创建应用程序网关之后添加后端目标。

3. 在“添加后端池”窗口中，选择“添加”以保存后端池配置并返回到“后端”选项卡    。
4. 现在添加另一个名为“fabrikamPool”  的后端池。

    :::image type="content" source="./media/create-multiple-sites-portal/backend-pools.png" alt-text="创建后端":::

4. 在“后端”  选项卡上，选择“下一步:  配置”。

### <a name="configuration-tab"></a>配置选项卡

在“配置”选项卡上，将连接使用传递规则创建的前端和后端池  。

1. 选择“传递规则”列中的“添加规则”   。

2. 在打开的“添加传递规则”窗口中，输入“contosoRule”作为规则名称    。

3. 传递规则需要侦听器。 在“添加传递规则”窗口中的“侦听器”选项卡上，输入侦听器的以下值   ：

    - **侦听器名称** ：输入“contosoListener”  作为侦听器的名称。
    - **前端 IP** ：选择“公共”，以选择为前端创建的公共 IP  。

   在“其他设置”  下：
   - **侦听器类型** ：多个站点
   - **主机名** ： **www.contoso.com**

   接受“侦听器”选项卡上其他设置的默认值，然后选择“后端目标”选项卡以配置剩余的传递规则   。

   :::image type="content" source="./media/create-multiple-sites-portal/routing-rule.png" alt-text="创建传递规则":::

4. 在“后端目标”选项卡上，选择“contosoPool”为“后端目标”    。

5. 对于“HTTP 设置”，选择“新建”以创建新的 HTTP 设置   。 HTTP 设置将决定传递规则的行为。 在打开的“添加 HTTP 设置”窗口中，”输入“contosoHTTPSetting”作为“HTTP 设置名称”    。 接受“添加 HTTP 设置”窗口中其他设置的默认值，然后选择“添加”以返回到“添加传递规则”窗口    。 

6. 在“添加传递规则”窗口上，选择“添加”以保存传递规则并返回到“配置”选项卡    。
7. 选择“添加规则”  ，并为 Fabrikam 添加类似的规则、侦听器、后端目标和 HTTP 设置。

     :::image type="content" source="./media/create-multiple-sites-portal/fabrikam-rule.png" alt-text="Fabrikam 规则":::

7. 在完成时选择“下一步:  标记”，然后选择“下一步:  查看 + 创建”。

### <a name="review--create-tab"></a>“查看 + 创建”选项卡

复查“查看 + 创建”选项卡上的设置，然后选择“创建”以创建虚拟网络、公共 IP 地址和应用程序网关   。 Azure 可能需要数分钟时间来创建应用程序网关。

请等待部署成功完成，然后再前进到下一部分。

## <a name="add-backend-targets"></a>添加后端目标

本示例将使用虚拟机作为目标后端。 可以使用现有的虚拟机，或创建新的虚拟机。 将创建两个虚拟机，供 Azure 用作应用程序网关的后端服务器。

若要添加后端目标，需要：

1. 创建两个新的 VM（contosoVM 和 fabrikamVM），用作后端服务器   。
2. 可以在虚拟机上安装 IIS，以验证是否已成功创建了应用程序网关。
3. 将后端服务器添加到后端池。

### <a name="create-a-virtual-machine"></a>创建虚拟机

1. 在 Azure 门户中，选择“创建资源”。 此时会显示“新建”窗口。
2. 选择“计算”，然后在“常用”列表中选择“Windows Server 2016 Datacenter”    。 此时会显示“创建虚拟机”页。<br>应用程序网关可以将流量路由到其后端池中使用的任何类型的虚拟机。 在此示例中，可以使用 Windows Server 2016 Datacenter。
3. 对于以下虚拟机设置，请在“基本信息”选项卡中输入相应值：

    - **资源组** ：选择 **myResourceGroupAG** 作为资源组名称。
    - **虚拟机名称** ：输入“contosoVM”  作为虚拟机的名称。
    - **用户名** ：为管理员用户名输入一个名称。
    - 密码：输入管理员的密码。
1. 接受其他默认值，然后选择“下一步: **磁盘”** 。  
2. 接受“磁盘” **选项卡的默认值** ，然后选择“下一步: **网络”** 。
3. 在“网络”  选项卡上，验证是否已选择 **myVNet** 作为 **虚拟网络** ，以及是否已将“子网”  设置为 **myBackendSubnet** 。 接受其他默认值，然后选择“下一步: **管理”** 。<br>应用程序网关可与其所在的虚拟网络外部的实例进行通信，但需要确保已建立 IP 连接。
4. 在“管理”  选项卡上，将“启动诊断”  设置为“关闭”。 接受其他默认值，然后选择“复查 + 创建”。
5. 在“复查 + 创建”选项卡上复查设置，更正任何验证错误，然后选择“创建”。
6. 等待虚拟机创建完成，然后再继续操作。

### <a name="install-iis-for-testing"></a>安装 IIS 进行测试

本示例在虚拟机上安装 IIS，只为验证 Azure 是否已成功创建应用程序网关。

1. 打开 [Azure PowerShell](../cloud-shell/quickstart-powershell.md)。 为此，请在 Azure 门户的顶部导航栏中选择“Cloud Shell”，然后从下拉列表中选择“PowerShell”。 

    ![安装自定义扩展](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. 运行以下命令以在虚拟机上安装 IIS，并将 <location\> 替换为自己的资源组区域： 

    ```azurepowershell-interactive
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName contosoVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location <location>
    ```

3. 使用以前完成的步骤创建第二个虚拟机并安装 IIS。 使用“fabrikamVM”  作为虚拟机名称和 **Set-AzVMExtension** cmdlet 的 **VMName** 设置。

### <a name="add-backend-servers-to-backend-pools"></a>将后端服务器添加到后端池

1. 选择“所有资源”，然后选择“myAppGateway”。

2. 从左侧菜单中选择“后端池”。

3. 选择 **contosoPool** 。

4. 在“目标”  下，从下拉列表中选择“虚拟机”。

5. 在“虚拟机”  和“网络接口”  下，从下拉列表中选择“contosoVM”  虚拟机及其关联的网络接口。

    ![添加后端服务器](./media/create-multiple-sites-portal/edit-backend-pool.png)

6. 选择“保存”。
7. 重复此步骤，将 fabrikamVM  和接口添加到 fabrikamPool  。

等待部署完成之后再继续下一步。

## <a name="create-a-www-a-record-in-your-domains"></a>在域中创建 www A 记录

使用其公共 IP 地址创建应用程序网关后，可以获取 IP 地址并使用它在域中创建 A 记录。 

1. 单击“所有资源”  ，然后单击“myAGPublicIPAddress”  。

    ![记下应用程序网关的 DNS 地址](./media/create-multiple-sites-portal/public-ip.png)

2. 复制 IP 地址并将其用作域中新 www  A 记录的值。

## <a name="test-the-application-gateway"></a>测试应用程序网关

1. 在浏览器的地址栏中输入域名。 例如 `http://www.contoso.com`。

    ![在应用程序网关中测试 contoso 站点](./media/create-multiple-sites-portal/application-gateway-iistest.png)

2. 将地址更改为其他域，应看到类似下例所示的内容：

    ![在应用程序网关中测试 fabrikam 站点](./media/create-multiple-sites-portal/application-gateway-iistest2.png)

## <a name="clean-up-resources"></a>清理资源

如果不再需要通过应用程序网关创建的资源，请删除资源组。 删除资源组时，也会删除应用程序网关和及其所有的相关资源。

若要删除资源组，请执行以下操作：

1. 在 Azure 门户的左侧菜单上选择“资源组”  。
2. 在“资源组”页的列表中搜索“myResourceGroupAG”，然后将其选中。
3. 在“资源组”页上，选择“删除资源组”   。
4. 在“键入资源组名称”字段中输入“myResourceGroupAG”，然后选择“删除”。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解 Azure 应用程序网关的作用](./overview.md)