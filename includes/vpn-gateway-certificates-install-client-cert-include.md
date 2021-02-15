---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 393b29245141b2970e7c1a227d6e8b1b131c445c
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061586"
---
如果想要从另一台客户端计算机（而不是用于生成客户端证书的计算机）创建 P2S 连接，需要安装客户端证书。 安装客户端证书时，需要使用导出客户端证书时创建的密码。

1. 找到 *.pfx* 文件并将其复制到客户端计算机。 在客户端计算机上，双击 *.pfx* 文件以进行安装。 将 " **存储位置** " 保留为 " **当前用户** "，然后选择 " **下一步** "。
1. 在“要导入的 **文件** ”页上，不要进行任何更改。 选择“下一步”。
1. 在 " **私钥保护** " 页上，输入证书的密码，或验证安全主体是否正确，然后选择 " **下一步** "。
1. 在 " **证书存储** " 页上，保留默认位置，然后选择 " **下一步** "。
1. 选择“完成”。 在证书安装的 " **安全警告** " 上，选择 **"是"** 。 你可以轻松地选择 "是" 以获取此安全警告，因为你生成了证书。
1. 现已成功导入证书。
