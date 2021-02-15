---
title: Azure VPN 网关：排查点到站点连接问题：Mac OS X 客户端
description: 了解如何使用本机 VPN 客户端和 IKEv2 排查 Mac OS X 的点到站点连接性问题。
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 02/10/2021
ms.author: alzam
ms.openlocfilehash: 4c8657f8a40084a726280efe471c0791931294f7
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100366630"
---
# <a name="troubleshoot-point-to-site-vpn-connections-from-mac-os-x-vpn-clients"></a>对 Mac OS X VPN 客户端的点到站点 VPN 连接进行故障排除

本文使用本机 VPN 客户端和 IKEv2 帮助排查 Mac OS X 的点到站点连接问题。 Mac 中用于 IKEv2 的 VPN 客户端是非常基本的客户端，不允许存在大量自定义内容。 只需检查四项设置：

* 服务器地址
* 远程 ID
* 本地 ID
* 身份验证设置
* OS 版本（10.11 或更高版本）


## <a name="troubleshoot-certificate-based-authentication"></a><a name="VPNClient"></a> 对基于证书的身份验证进行故障排除
1. 检查 VPN 客户端设置。 通过按 Command + Shift 转到“网络设置”  ，然后键入“VPN”检查 VPN 客户端设置。 从列表中，单击需要调查的 VPN 条目。

   ![IKEv2 基于证书的身份验证](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2cert1.jpg)
2. 验证 **服务器地址** 是否是完整的 FQDN 并包括 cloudapp.net。
3. **远程 ID** 应与服务器地址（网关 FQDN）相同。
4. **本地 ID** 应与客户端证书的 **使用者** 相同。
5. 单击“身份验证设置”  以打开“身份验证设置”页。

   ![屏幕截图显示了已选择“证书”的“身份验证设置”对话框。](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth2.jpg)
6. 确认已从下拉列表中选择了“证书”  。
7. 单击“选择”  按钮，并确认已选择正确的证书。 单击“确定”  保存所有更改。

## <a name="troubleshoot-username-and-password-authentication"></a><a name="ikev2"></a>对用户名和密码身份验证进行故障排除

1. 检查 VPN 客户端设置。 通过按 Command + Shift 转到“网络设置”  ，然后键入“VPN”检查 VPN 客户端设置。 从列表中，单击需要调查的 VPN 条目。

   ![IKEv2 用户名密码](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2user3.jpg)
2. 验证 **服务器地址** 是否是完整的 FQDN 并包括 cloudapp.net。
3. **远程 ID** 应与服务器地址（网关 FQDN）相同。
4. **本地 ID** 可以为空。
5. 单击“身份验证设置”  按钮，并确认已从下拉列表中选择了“用户名”。

   ![屏幕截图显示了已选择“用户名”的“身份验证设置”对话框。](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth4.png)
6. 请确保输入了正确的凭据。

## <a name="additional-steps"></a><a name="additional"></a>其他步骤

如果你尝试前面的步骤，并且所有内容均配置正确，请下载 [Wireshark](https://www.wireshark.org/#download) 并执行数据包捕获。

1. 按 iskmp 筛选并查看 IKE_SA 数据包   。 应该能够在 **有效负载：安全关联** 下查看 SA 方案详细信息。 
2. 验证客户端和服务器是否具有公用集。

   ![数据包](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/packet5.jpg) 
  
3. 如果没有关于网络跟踪的任何服务器响应，请在 Azure 门户网站的 Azure 网关配置页中验证是否已启用 IKEv2 协议。

## <a name="next-steps"></a>后续步骤
有关更多帮助，请参阅 [Microsoft 支持部门](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
