---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 8076a6af2382ccec1ac832cd83c3946d8c7c6f57
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019119"
---
|参数名称| 类型 | 说明| 可能的值|
|-|-|-|-|
| /ServerMode|必需|指定是要同时安装配置服务器和进程服务器，还是只安装进程服务器|CS<br>PS|
|/InstallLocation|必需|用于安装组件的文件夹| 计算机上的任意文件夹|
|/MySQLCredsFilePath|必需|MySQL 服务器凭据存储到的文件路径|文件应采用以下指定格式|
|/VaultCredsFilePath|必需|保管库凭据文件的路径|有效的文件路径|
|/EnvType|必需|要保护的环境类型 |VMware<br>NonVMware|
|/PSIP|必需|要用于复制数据传输的 NIC 的 IP 地址| 任何有效的 IP 地址|
|/CSIP|必需|配置服务器侦听时所在的 NIC 的 IP 地址| 任何有效的 IP 地址|
|/PassphraseFilePath|必需|通行短语文件位置的完整路径|有效的文件路径|
|/BypassProxy|可选|指定配置服务器不使用代理连接到 Azure||
|/ProxySettingsFilePath|可选|代理设置（默认代理需要身份验证，或自定义代理）|该文件应采用以下指定格式|
|DataTransferSecurePort|可选|用于复制数据的 PSIP 上的端口号| 有效端口号（默认值为 9433）|
|/SkipSpaceCheck|可选|跳过缓存磁盘的空间检查| |
|/AcceptThirdpartyEULA|必需|该标志表示接受第三方 EULA| |
|/ShowThirdpartyEULA|可选|显示第三方 EULA。 如果作为输入提供，将忽略所有其他参数| |
