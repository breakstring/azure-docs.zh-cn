---
title: include 文件
description: include 文件
author: lrtoyou1223
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: lle
ms.openlocfilehash: 24a541080b580eab967987fa7c92cea64b99d65c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100389481"
---
| 域名                                          | 出站端口 | 说明                |
| ----------------------------------------------------- | -------------- | ---------------------------|
| 公有云： `*.servicebus.windows.net` <br> Azure 政府版： `*.servicebus.usgovcloudapi.net` <br> 中国：`*.servicebus.chinacloudapi.cn`   | 443            | 自承载集成运行时需要用它来进行交互式创作。 |
| 公有云： `{datafactory}.{region}.datafactory.azure.net`<br> 或 `*.frontend.clouddatahub.net` <br> Azure 政府版： `{datafactory}.{region}.datafactory.azure.us` <br> 中国：`{datafactory}.{region}.datafactory.azure.cn` | 443            | 自承载集成运行时连接到数据工厂服务时需要此端口。 <br>对于公有云中的新创建数据工厂，请从你的自承载 Integration Runtime 密钥中找到 FQDN，格式为 {datafactory}。{region}. datafactory。 对于旧数据工厂，如果在自承载集成密钥中找不到 FQDN，请使用 *.frontend.clouddatahub.net。 |
| `download.microsoft.com`    | 443            | 自承载集成运行时下载更新时需要此端口。 如果已禁用自动更新，则可以跳过对此域的配置。 |
| 密钥保管库 URL | 443           | 如果将凭据存储在 Key Vault 中，则 Azure Key Vault 需要此端口。 |
