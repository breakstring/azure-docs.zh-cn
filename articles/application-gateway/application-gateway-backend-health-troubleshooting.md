---
title: 在 Azure 应用程序网关中排查后端运行状况问题
description: 介绍如何排查 Azure 应用程序网关的后端运行状况问题
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 06/09/2020
ms.author: surmb
ms.openlocfilehash: 95b74e5fc6c5d2c09ff04b3f14e920ae675ab6e1
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592749"
---
<a name="troubleshoot-backend-health-issues-in-application-gateway"></a>排查应用程序网关中的后端运行状况问题
==================================================

<a name="overview"></a>概述
--------

默认情况下，Azure 应用程序网关会探测后端服务器，以检查其运行状态，以及它们是否已准备好为请求提供服务。 用户还可以创建自定义探测，并指定主机名、要探测的路径，以及表示正常的状态代码。 在每种情况下，如果后端服务器未成功响应，则应用程序网关会将该服务器标记为“不正常”，并停止向其转发请求。 服务器成功开始响应后，应用程序网关将继续转发请求。

### <a name="how-to-check-backend-health"></a>如何检查后端运行状况

若要检查后端池的运行状况，可以使用 Azure 门户中的“后端运行状况”页。 或者，可以使用 [Azure PowerShell](/powershell/module/az.network/get-azapplicationgatewaybackendhealth)、[CLI](/cli/azure/network/application-gateway#az-network-application-gateway-show-backend-health) 或 [REST API](/rest/api/application-gateway/applicationgateways/backendhealth)。

所述任意方法检索到的状态可能为下列其中一项：

- 正常

- 不正常

- 未知

如果服务器的后端运行状态为“正常”，则表示应用程序网关会将请求转发到该服务器。 但是，如果后端池中所有服务器的后端运行状况为“不正常”或未知，则在尝试访问应用程序时，你可能会遇到问题。 本文将描述显示的每个错误的症状、原因和解决方法。

<a name="backend-health-status-unhealthy"></a>后端运行状态：不正常
-------------------------------

如果后端运行状态为“不正常”，门户视图将如以下屏幕截图所示：

![应用程序网关后端运行状况 - 不正常](./media/application-gateway-backend-health-troubleshooting/appgwunhealthy.png)

或者，如果你使用 Azure PowerShell、CLI 或 Azure REST API 查询，则会看到如下所示的响应：
```azurepowershell
PS C:\Users\testuser\> Get-AzApplicationGatewayBackendHealth -Name "appgw1" -ResourceGroupName "rgOne"
BackendAddressPools :
{Microsoft.Azure.Commands.Network.Models.PSApplicationGatewayBackendHealthPool}
BackendAddressPoolsText : [
{
                              "BackendAddressPool": {
                                "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appgw1/b
                          ackendAddressPools/appGatewayBackendPool"
                              },
                              "BackendHttpSettingsCollection": [
                                {
                                  "BackendHttpSettings": {
                                    "TrustedRootCertificates": [],
                                    "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appg
                          w1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
                                  },
                                  "Servers": [
                                    {
                                      "Address": "10.0.0.5",
                                      "Health": "Healthy"
                                    },
                                    {
                                      "Address": "10.0.0.6",
                                      "Health": "Unhealthy"
                                    }
                                  ]
                                }
                              ]
                            }
                        ]
```
后端池中所有服务器的后端服务器状态都为“不正常”后，请求将不会转发到这些服务器，并且应用程序网关会向请求方客户端返回“502 错误的网关”错误。 若要排查此问题，请检查“后端运行状况”选项卡的“详细信息”列。 

“详细信息”列中显示的消息提供有关问题的更详细见解，根据这些信息，可以着手排查问题。

> [!NOTE]
> 默认探测请求将以“\<protocol\>://127.0.0.1:\<port\>/”格式发送。 例如，对于端口 80 上的 HTTP 探测，格式为 http://127.0.0.1:80 。 只将 HTTP 状态代码 200 至 399 视为正常。 协议和目标端口继承自 HTTP 设置。 如果你希望应用程序网关探测不同的协议、主机名或路径，并识别其他状态代码为正常，请配置一个自定义探测，并将其关联到 HTTP 设置。

<a name="error-messages"></a>错误消息
------------------------
#### <a name="backend-server-timeout"></a>后端服务器超时

**消息：** 后端响应应用程序网关运行状况探测所花费的时间超过了探测设置中的超时阈值。

**原因：** 应用程序网关将 HTTP(S) 探测请求发送到后端服务器后，它会根据配置的期限等待后端服务器的响应。 如果后端服务器在配置的期限（超时值）内未做出响应，则系统会将其标记为“不正常”，直到它再次在超时期限内开始响应。

**解决方法：** 检查后端服务器或应用程序为何无法在配置的超时期限内做出响应，并检查应用程序依赖项。 例如，检查数据库是否存在可能触发响应延迟的任何问题。 如果你了解应用程序的行为，并且它应该只在超时值之后做出响应，请增大自定义探测设置中的超时值。 必须使用一个自定义探测来更改超时值。 有关如何配置自定义探测的信息，请[参阅文档页](./application-gateway-create-probe-portal.md)。

若要增大超时值，请执行以下步骤：

1.  直接访问后端服务器，并在该页面上查看服务器响应所需时间。 可以使用任何工具来访问后端服务器，包括使用开发人员工具的浏览器。

1.  确定应用程序做出响应所需的时间后，选择“运行状况探测”选项卡，然后选择与 HTTP 设置关联的探测。

1.  输入大于应用程序响应时间的任何超时值（以秒为单位）。

1.  保存自定义探测设置，检查后端运行状况现在是否显示为“正常”。

#### <a name="dns-resolution-error"></a>DNS 解析错误

**消息：** 应用程序网关无法为此后端创建探测。 如果未正确输入后端的 FQDN，则往往会发生这种情况。 

**原因：** 如果后端池的类型为“IP 地址/FQDN”或“应用服务”，则应用程序网关将解析为通过域名系统 (DNS)（自定义或 Azure 默认）输入的 FQDN 的 IP 地址，并尝试连接到 HTTP 设置中指定的 TCP 端口上的服务器。 但如果显示此消息，则表示应用程序网关无法成功解析输入的 FQDN 的 IP 地址。

**解决方法：**

1.  验证后端池中输入的 FQDN 是否正确以及它是否为公共域，并尝试从本地计算机解析它。

1.  如果可以解析 IP 地址，则表示虚拟网络中的 DNS 配置可能有错误。

1.  检查是否在虚拟网络中配置了自定义 DNS 服务器。 如果是，请检查 DNS 服务器，确定它为何无法解析为指定的 FQDN 的 IP 地址。

1.  如果使用 Azure 默认 DNS，请与域名注册机构确认是否完成了正确的 A 记录或 CNAME 记录映射。

1.  如果域是专用或内部域，请尝试从同一虚拟网络中的 VM 解析它。 如果能够解析，请重启应用程序网关，然后再次检查。 若要重启应用程序网关，需要使用相关链接资源中所述的 PowerShell 命令[停止](/powershell/module/azurerm.network/stop-azurermapplicationgateway)再[启动](/powershell/module/azurerm.network/start-azurermapplicationgateway)它。

#### <a name="tcp-connect-error"></a>TCP 连接错误

**消息：** 应用程序网关无法连接到后端。
请检查后端是否在用于探测的端口上作出响应。
同时，请检查是否有任何 NSG/UDR/防火墙正在阻止访问此后端的 IP 和端口

**原因：** DNS 解析阶段完成后，应用程序网关会尝试连接到 HTTP 设置中配置的 TCP 端口上的后端服务器。 如果应用程序网关无法在指定的端口上建立 TCP 会话，则会将探测标记为不正常并显示此消息。

**解决方案：** 如果收到此错误，请执行以下步骤：

1.  检查是否可以使用浏览器或 PowerShell 连接到 HTTP 设置中所述端口上的后端服务器。 例如，运行以下命令：`Test-NetConnection -ComputerName
    www.bing.com -Port 443`

1.  如果所述端口不是所需端口，请输入正确端口号，以便应用程序网关连接到后端服务器

1.  如果在本地计算机的端口上也无法进行连接，请进行以下检查：

    a.  检查后端服务器的网络适配器和子网的网络安全组 (NSG) 设置，并检查是否允许与配置的端口建立入站连接。 如果不允许，请创建新规则来允许连接。 若要了解如何创建 NSG 规则，请[参阅文档页](../virtual-network/tutorial-filter-network-traffic.md#create-security-rules)。

    b.  检查应用程序网关子网的 NSG 设置是否允许出站公共和专用流量，以便可以建立连接。 查看步骤 3a 中提供的文档页来详细了解如何创建 NSG 规则。
    ```azurepowershell
            $vnet = Get-AzVirtualNetwork -Name "vnetName" -ResourceGroupName "rgName"
            Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
    ```

    c.  检查应用程序网关和后端服务器子网的用户定义的路由 (UDR) 设置中是否存在任何路由异常。 确保 UDR 未将流量引离后端子网。 例如，检查到网络虚拟设备的路由或通过 Azure ExpressRoute 和/或 VPN 播发到应用程序网关子网的默认路由。

    d.  若要检查网络适配器的有效路由和规则，可使用以下 PowerShell 命令。
    ```azurepowershell
            Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
            Get-AzEffectiveRouteTable -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
    ```
1.  如果找不到 NSG 或 UDR 的任何问题，请检查后端服务器上是否存在应用程序相关的问题，从而导致客户端无法在配置的端口上建立 TCP 会话。 需要检查的若干事项：

    a.  打开命令提示符 (Win+R -\> cmd)，输入 `netstat` 并按 Enter。

    b.  检查服务器是否正在侦听配置的端口。 例如：
    ```
            Proto Local Address Foreign Address State PID
            TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 4
    ```
    c.  如果服务器未侦听配置的端口，请检查 Web 服务器设置。 例如：IIS 中的站点绑定、NGINX 中的服务器块，以及 Apache 中的虚拟主机。

    d.  检查 OS 防火墙设置，确保允许传入的流量进入端口。

#### <a name="http-status-code-mismatch"></a>HTTP 状态代码不匹配

**消息：** 后端 HTTP 响应的状态代码与探测设置不匹配。 预期值: {HTTPStatusCode0}，收到的值: {HTTPStatusCode1}.

**原因：** 建立 TCP 连接并完成 TLS 握手（如果启用了 TLS）后，应用程序网关会将探测作为 HTTP GET 请求发送到后端服务器。 如前文所述，默认探测将发送到“\<protocol\>://127.0.0.1:\<port\>/”，并且它会将 200 至 399 范围内的响应状态代码视为“正常”。 如果服务器返回任何其他状态代码，则将服务器标记为“不正常”并显示此消息。

**解决方案：** 根据后端服务器的响应代码，可以执行以下步骤。 下面列出了一些常见的状态代码：

| **错误** | **操作** |
| --- | --- |
| 探测状态代码不匹配：收到 401 | 检查后端服务器是否要求身份验证。 应用程序网关探测无法传递用于身份验证的凭据。 允许探测状态代码匹配中出现 \"HTTP 401\"，或探测其中的服务器不需要身份验证的路径。 | |
| 探测状态代码不匹配：收到 403 | 访问被禁止。 检查后端服务器上是否允许访问该路径。 | |
| 探测状态代码不匹配：收到 404 | 找不到页面。 检查是否可在后端服务器上访问主机名路径。 将主机名或路径参数更改为可访问的值。 | |
| 探测状态代码不匹配：收到 405 | 应用程序网关的探测请求使用 HTTP GET 方法。 检查服务器是否允许此方法。 | |
| 探测状态代码不匹配：收到 500 | 内部服务器错误。 检查后端服务器的运行状况，以及服务是否正在运行。 | |
| 探测状态代码不匹配：收到 503 | 服务不可用。 检查后端服务器的运行状况，以及服务是否正在运行。 | |

或者，如果你认为响应合法，并且你希望应用程序网关接受其他状态代码为“正常”，可以创建自定义探测。 如果后端网站需要身份验证，则此方法很有作用。 由于探测请求不携带任何用户凭据，因此它们将会失败，并且后端服务器将返回 HTTP 401 状态代码。

若要创建自定义探测，请遵循[这些步骤](./application-gateway-create-probe-portal.md)。

#### <a name="http-response-body-mismatch"></a>HTTP 响应正文不匹配

**消息：** 后端 HTTP 响应的正文与探测设置不匹配。 收到的响应正文不包含 {string}。

**原因：** 创建自定义探测时，可以选择通过匹配响应正文中的字符串将后端服务器标记为正常。 例如，可以将应用程序网关配置为接受“unauthorized”作为要匹配的字符串。 如果探测请求的后端服务器响应包含字符串 **unauthorized**，则会将该服务器标记为“正常”。 否则，会将其标记为“不正常”并显示此消息。

**解决方案：** 若要解决此问题，请执行以下步骤：

1.  在本地或者通过探测路径上的某台客户端计算机访问后端服务器，并检查响应正文。

1.  验证应用程序网关自定义探测配置中的响应正文是否与配置的内容相匹配。

1.  如果不匹配，请更改探测配置，使其包含可接受的正确字符串值。

详细了解[应用程序网关探测匹配](./application-gateway-probe-overview.md#probe-matching)。

>[!NOTE]
> 对于与 TLS 相关的所有错误消息，若要详细了解 SNI 行为以及 v1 与 v2 SKU 之间的差异，请参阅 [TLS 概述](ssl-overview.md)页。


#### <a name="backend-server-certificate-invalid-ca"></a>后端服务器证书无效的 CA

**消息：** 后端使用的服务器证书未由已知的证书颁发机构 (CA) 签名。 通过上传后端使用的服务器证书的根证书，允许应用程序网关上的后端。

**原因：** 带应用程序网关 v2 的端到端 SSL 必须验证后端服务器的证书，才能断定服务器是否正常运行。
要信任某个 TLS/SSL 证书，后端服务器的证书必须由应用程序网关受信任存储中包含的 CA 颁发。 如果证书不是由受信任的 CA 颁发的（例如，使用自签名证书），则用户应将证书颁发者的证书上传到应用程序网关。

**解决方案：** 遵循以下步骤导出受信任的根证书并将其上传到应用程序网关。 （这些步骤适用于 Windows 客户端。）

1.  登录到托管应用程序的计算机。

1.  按 Win+R，或右键单击“开始”按钮并选择“运行”。 

1.  输入 `certmgr.msc` 并按 Enter。 也可以在“开始”菜单中搜索“证书管理器”。

1.  找到该证书（通常位于 `\Certificates - Current User\\Personal\\Certificates\` 中）并将其打开。

1.  选择根证书，然后选择“查看证书”。

1.  在“证书属性”中，选择“详细信息”选项卡。

1.  在“详细信息”选项卡中，选择“复制到文件”选项，并以 Base-64 编码的 X.509 (.CER) 格式保存文件。 

1.  在 Azure 门户中打开“应用程序网关 HTTP 设置”页。

1. 打开“HTTP 设置”，选择“添加证书”并找到刚刚保存的证书文件。

1. 选择“保存”以保存 HTTP 设置。

或者，可以从客户端计算机导出根证书，方法是通过浏览器直接访问服务器（绕过应用程序网关），然后从浏览器导出根证书。

有关在应用程序网关中提取和上传受信任的根证书的详细信息，请参阅[导出受信任的根证书（适用于 v2 SKU）](./certificates-for-backend-authentication.md#export-trusted-root-certificate-for-v2-sku)。

#### <a name="trusted-root-certificate-mismatch"></a>受信任的根证书不匹配

**消息：** 后端使用的服务器证书的根证书与添加到应用程序网关的受信任根证书不匹配。 确保将正确的根证书添加到后端允许列表。

**原因：** 带应用程序网关 v2 的端到端 SSL 必须验证后端服务器的证书，才能断定服务器是否正常运行。
要信任某个 TLS/SSL 证书，后端服务器证书必须由应用程序网关受信任存储中包含的 CA 颁发。 如果证书不是由受信任的 CA 颁发的（例如，使用自签名证书），则用户应将证书颁发者的证书上传到应用程序网关。

已上传到应用程序网关 HTTP 设置的证书必须与后端服务器证书的根证书相匹配。

**解决方案：** 如果收到此错误消息，则表示已上传到应用程序网关的证书与上传到后端服务器的证书不匹配。

遵循上述方法中的步骤 1-11 将正确的受信任根证书上传到应用程序网关。

有关在应用程序网关中提取和上传受信任的根证书的详细信息，请参阅[导出受信任的根证书（适用于 v2 SKU）](./certificates-for-backend-authentication.md#export-trusted-root-certificate-for-v2-sku)。
> [!NOTE]
> 如果后端服务器在 TLS 握手期间未交换完整的证书链（包括“根”->“中间”（如果适用）->“叶”），则也可能会出现此错误。 若要验证，可在任何客户端中使用 OpenSSL 命令，并使用应用程序网关探测中配置的设置连接到后端服务器。

例如：
```
OpenSSL> s_client -connect 10.0.0.4:443 -servername www.example.com -showcerts
```
如果输出未显示要返回的完整证书链，请使用完整链（包括根证书）再次导出证书。 在后端服务器中配置该证书。 

```
  CONNECTED(00000188)\
  depth=0 OU = Domain Control Validated, CN = \*.example.com\
  verify error:num=20:unable to get local issuer certificate\
  verify return:1\
  depth=0 OU = Domain Control Validated, CN = \*.example.com\
  verify error:num=21:unable to verify the first certificate\
  verify return:1\
  \-\-\-\
  Certificate chain\
   0 s:/OU=Domain Control Validated/CN=*.example.com\
     i:/C=US/ST=Arizona/L=Scottsdale/O=GoDaddy.com, Inc./OU=http://certs.godaddy.com/repository//CN=Go Daddy Secure Certificate Authority - G2\
  \-----BEGIN CERTIFICATE-----\
  xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx\
  \-----END CERTIFICATE-----
```

#### <a name="backend-certificate-invalid-common-name-cn"></a>后端证书的公用名 (CN) 无效

**消息：** 后端证书的公用名 (CN) 与探测的主机标头不匹配。

**原因：** 应用程序网关检查后端 HTTP 设置中指定的主机名是否与后端服务器的 TLS/SSL 证书提供的 CN 相匹配。 这是 Standard_v2 和 WAF_v2 SKU (V2) 行为。 Standard 和 WAF SKU 的 (v1) 服务器名称指示 (SNI) 在后端池地址中设置为 FQDN。 若要详细了解 SNI 行为以及 v1 和 v2 SKU 之间的差异，请参阅[应用程序网关中的 TLS 终止以及端到端 TLS 的概述](ssl-overview.md)。

在 v2 SKU 中，如果存在默认探测（未配置和关联自定义探测），则将根据 HTTP 设置中所述的主机名设置 SNI。 或者，如果 HTTP 设置中指定了“从后端地址中选取主机名”，并且后端地址池包含有效的 FQDN，则会应用此设置。

如果某个自定义探测已关联到 HTTP 设置，则会根据自定义探测配置中指定的主机名设置 SNI。 或者，如果在自定义探测中选择了“从后端 HTTP 设置中选取主机名”，则会根据 HTTP 设置中指定的主机名设置 SNI。

如果在 HTTP 设置中设置了“从后端地址中选取主机名”，则后端地址池必须包含有效的 FQDN。

如果收到此错误消息，表示后端证书的 CN 与自定义探测或 HTTP 设置中配置的主机名不匹配（如果选择了“从后端 HTTP 设置中选取主机名”）。 如果使用默认探测，主机名将设置为 **127.0.0.1**。 如果这不是所需的值，应创建一个自定义探测，并将其关联到 HTTP 设置。

**解决方案：**

若要解决该问题，请执行以下步骤。

对于 Windows：

1.  登录到托管应用程序的计算机。

1.  按 Win+R，或右键单击“开始”按钮并选择“运行”。 

1.  输入 **certmgr.msc** 并按 Enter。 也可以在“开始”菜单中搜索“证书管理器”。

1.  找到该证书（通常位于 `\Certificates - Current User\\Personal\\Certificates` 中）并将其打开。

1.  在“详细信息”选项卡中检查证书的“使用者”。 

1.  验证详细信息中的证书的 CN，并在自定义探测或 HTTP 设置的主机名字段中输入相同的 CN（如果选择了“从后端 HTTP 设置中选取主机名”）。 如果这不是网站所需的主机名，则必须获取该域的证书，或者在自定义探测或 HTTP 设置配置中输入正确的主机名。

对于使用 OpenSSL 的 Linux：

1.  在 OpenSSL 中运行此命令：
    ```
    openssl x509 -in certificate.crt -text -noout
    ```

2.  在显示的属性中找到证书的 CN，并在 HTTP 设置的主机名字段中输入相同的 CN。 如果这不是网站所需的主机名，则必须获取该域的证书，或者在自定义探测或 HTTP 设置配置中输入正确的主机名。

#### <a name="backend-certificate-is-invalid"></a>后端证书无效

**消息：** 后端证书无效。 当前日期不在证书上的“生效”和“失效”日期范围内。\"\"\"\"

**原因：** 每个证书均附带有效范围，除非服务器的 TLS/SSL 证书有效，否则 HTTPS 连接不安全。 当前数据必须在“有效期开始时间”和“有效期结束时间”范围内 。 否则，证书将被视为无效，并成为一个安全隐患。在这种情况下，应用程序网关会将后端服务器标记为“不正常”。

**解决方案：** 如果 TLS/SSL 证书已过期，请向供应商续订证书，并使用新证书更新服务器设置。 如果是自签名证书，则必须生成有效证书，并将根证书上传到应用程序网关 HTTP 设置。 为此，请执行以下步骤：

1.  在门户中打开应用程序网关 HTTP 设置。

1.  选择包含已过期证书的设置，选择“添加证书”并打开新证书文件。

1.  使用证书旁边的“删除”图标删除旧证书，然后选择“保存”。 

#### <a name="certificate-verification-failed"></a>证书验证失败

**消息：** 无法验证后端证书的有效性。 若要找出原因，请在 OpenSSL 诊断信息中检查与错误代码 {errorCode} 相关的消息

**原因：** 当应用程序网关无法验证证书的有效性时，将会发生此错误。

**解决方案：** 若要解决此问题，请验证是否正确创建了服务器上的证书。 例如，可以使用 [OpenSSL](https://www.openssl.org/docs/man1.0.2/man1/verify.html) 来验证证书及其属性，并尝试将证书重新上传到应用程序网关的 HTTP 设置。

<a name="backend-health-status-unknown"></a>后端运行状态：未知
-------------------------------
如果后端运行状况显示为“未知”，门户视图将如以下屏幕截图所示：

![应用程序网关后端运行状况 - 未知](./media/application-gateway-backend-health-troubleshooting/appgwunknown.png)

以下一种或多种原因可能会导致此行为：

1.  应用程序网关子网中的 NSG 正在阻止从“Internet”对端口 65503-65534 (v1 SKU) 或 65200-65535 (v2 SKU) 进行入站访问。
1.  应用程序网关子网中的 UDR 设置为默认路由 (0.0.0.0/0)，下一跃点未指定为“Internet”。
1.  默认路由是由通过 BGP 与虚拟网络建立的 ExpressRoute/VPN 连接播发的。
1.  自定义 DNS 服务器是在无法解析公共域名的虚拟网络中配置的。
1.  应用程序网关处于“不正常”状态。

**解决方案：**

1.  检查 NSG 是否正在阻止从 **Internet** 对端口 65503-65534 (v1 SKU) 或 65200-65535 (v2 SKU) 进行访问。

    a.  在应用程序网关“概述”选项卡中，选择“虚拟网络/子网”链接。 

    b.  在虚拟网络的“子网”选项卡中，选择已将应用程序网关部署到的子网。

    c.  检查是否配置了任何 NSG。

    d.  如果配置了 NSG，请在“搜索”选项卡中或者在“所有资源”下搜索该 NSG 资源。 

    e.  在“入站规则”部分添加一个入站规则，以允许目标端口范围 65503-65534 (v1 SKU) 或 65200-65535 (v2 SKU)，并将“源”设置为“任何”或“Internet”。   

    f.  选择“保存”，并验证是否可以查看正常的后端。 或者，可以通过 [PowerShell/CLI](../virtual-network/manage-network-security-group.md) 执行此操作。

1.  检查 UDR 是否包含下一跃点不是设置为“Internet”的默认路由 (0.0.0.0/0)：
    
    a.  执行步骤 1a 和 1b 来确定子网。

    b.  检查是否配置了任何 UDR。 如果已配置，请在搜索栏中或者在“所有资源”下搜索该资源。

    c.  检查是否存在下一跃点未设置为“Internet”的任何默认路由 (0.0.0.0/0)。 如果设置为“虚拟设备”或“虚拟网络网关”，则必须确保虚拟设备或本地设备能够正确地将数据包路由回到 Internet 目标，且无需修改数据包。 

    d.  否则，请将下一跃点更改为“Internet”，选择“保存”，并验证后端运行状况。 

1.  通过 BGP 与虚拟网络建立的 ExpressRoute/VPN 连接播发的默认路由：

    a.  如果 ExpressRoute/VPN 通过 BGP 连接到虚拟网络，并且你正在播发默认路由，则必须确保数据包在不修改的情况下可路由回到 Internet 目标。 可以使用应用程序网关门户中的“连接故障排除”选项进行验证。

    b.  手动选择任何可通过 Internet 路由的 IP 地址（例如 1.1.1.1）作为目标。 将目标端口设置为任意端口，然后验证连接。

    c.  如果下一跃点是虚拟网络网关，则可能存在通过 ExpressRoute 或 VPN 播发的默认路由。

1.  如果在虚拟网络中配置了自定义 DNS 服务器，请验证服务器是否可以解析公共域。 在应用程序网关必须连接到外部域（例如 OCSP 服务器）或检查证书的吊销状态的情况下，可能需要进行公共域名解析。

1.  若要验证应用程序网关是否正常且正在运行，请在门户中转到“资源运行状况”选项，并验证状态是否为“正常”。  如果看到“不正常”或“降级”状态，请[联系支持人员](https://azure.microsoft.com/support/options/)。 

<a name="next-steps"></a>后续步骤
----------

详细了解[应用程序网关诊断和日志记录](./application-gateway-diagnostics.md)。
