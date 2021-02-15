---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 71460af42b4da97a578ae5a3e23a714577e71867
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86218295"
---
| 端口号。| 入或出 | 端口范围| 必须|   注释 |
|--------|-----|-----|-----------|----------|
| TCP 80 (HTTP)|出|WAN |否|出站端口用于 Internet 访问以检索更新。 <br>出站 Web 代理可由用户配置。 |
| TCP 443 (HTTPS)|出|WAN|是|出站端口用于访问云中的数据。<br>出站 Web 代理可由用户配置。|
| UDP 123 (NTP)|出|WAN|某些情况下<br>请参阅说明|仅当使用基于 Internet 的 NTP 服务器时，才需要此端口。  |   
| UDP 53 (DNS)|出|WAN|某些情况下<br>请参阅说明|仅当使用基于 Internet 的 DNS 服务器时，才需要此端口。<br>我们建议使用本地 DNS 服务器。 |
| TCP 5985 (WinRM)|出/入|LAN|某些情况下<br>请参阅说明|通过基于 HTTP 的远程 PowerShell 连接到设备时，需要此端口。  |
| UDP 67 (DHCP)|出|LAN|某些情况下<br>请参阅说明|仅当使用本地 DHCP 服务器时，才需要此端口。  |
| TCP 80 (HTTP)|出/入|LAN|是|此端口是设备上用于本地管理的本地 UI 的入站端口。 <br>通过 HTTP 访问本地 UI 会自动重定向到 HTTPS。  |
| TCP 443 (HTTPS)|出/入|LAN|是|此端口是设备上用于本地管理的本地 UI 的入站端口。 |
| TCP 445 (SMB)|在|LAN|某些情况下<br>请参阅说明|仅当通过 SMB 连接时，才需要此端口。 |
| TCP 2049 (NFS)|在|LAN|某些情况下<br>请参阅说明|仅当通过 NFS 连接时，才需要此端口。 |
