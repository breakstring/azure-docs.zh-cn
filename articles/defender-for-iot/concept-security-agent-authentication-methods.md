---
title: 安全代理身份验证方法
description: 了解在使用 Defender for IoT 服务时可用的不同身份验证方法。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: shhazam
ms.openlocfilehash: aa5a8a842a750e44d4beeb915008f86c6b5bbb70
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809246"
---
# <a name="security-agent-authentication-methods"></a>安全代理身份验证方法

本文介绍了可与 AzureIoTSecurity 代理一起用来向 IoT 中心进行身份验证的不同身份验证方法。

加入 IoT 中心内 Defender for IoT 的每个设备都需要一个安全模块。 要对设备进行身份验证，Defender for IoT 可以使用以下两种方法之一。 请选择最适合于现有 IoT 解决方案的方法。

> [!div class="checklist"]
> * SecurityModule 选项
> * Device 选项

## <a name="authentication-methods"></a>身份验证方法

用于 IoT AzureIoTSecurity agent 的两种方法执行身份验证：

- SecurityModule 身份验证模式<br>
将使用独立于设备标识的安全模块标识对代理进行身份验证。
如果希望安全代理通过安全模块使用专用身份验证方法（仅限对称密钥），请使用此身份验证类型。

- Device 身份验证模式<br>
在此方法中，安全代理首先用设备标识进行身份验证。 初始身份验证完成后，Defender for IoT 代理会将 REST API 与设备的身份验证数据配合使用来执行对 IoT 中心的 REST 调用。 然后，Defender for IoT 代理将从 IoT 中心请求安全模块身份验证方法和数据。 在最后一步中，Defender for IoT 代理将针对 Defender for IoT 模块执行身份验证。

如果希望安全代理重新使用现有设备身份验证方法（自签名证书或对称密钥），请使用此身份验证类型。

请参阅[安全代理安装参数](#security-agent-installation-parameters)来了解如何配置。

## <a name="authentication-methods-known-limitations"></a>身份验证方法的已知限制

- SecurityModule 身份验证模式仅支持对称密钥身份验证。
- Device 身份验证模式不支持 CA 签名的证书。

## <a name="security-agent-installation-parameters"></a>安全代理安装参数

[部署安全代理](how-to-deploy-agent.md)时，必须以参数形式提供身份验证详细信息。
下表中介绍了这些参数。

|Linux 参数名称 | Windows 参数名称 | 简写参数 |说明|选项|
|---------------------|---------------|---------|---------------|---------------|
|authentication-identity|AuthenticationIdentity|aui|身份验证标识| SecurityModule 或 Device|
|authentication-method|AuthenticationMethod|aum|身份验证方法|SymmetricKey 或 SelfSignedCertificate|
|file-path|文件路径|F|包含证书或对称密钥的文件的绝对完整路径| |
|host-name|HostName|hn|IoT 中心的 FQDN|示例：ContosoIotHub.azure-devices.net|
|device-id|DeviceId|di|设备 ID|示例：MyDevice1|
|certificate-location-kind|CertificateLocationKind|cl|证书存储位置|LocalFile 或 Store|
|

使用“安装安全代理”脚本时，系统会自动执行以下配置。 若要手动编辑安全代理身份验证，请编辑该配置文件。

## <a name="change-authentication-method-after-deployment"></a>在部署后更改身份验证方法

使用安装脚本部署安全代理时，系统会自动创建一个配置文件。

若要在部署后更改身份验证方法，需要手动编辑该配置文件。

### <a name="c-based-security-agent"></a>基于 C# 的安全代理

编辑带有以下参数的 Authentication.config：

```xml
<Authentication>
  <add key="deviceId" value=""/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value=""/>
  <add key="type" value=""/>
  <add key="identity" value=""/>
  <add key="certificateLocationKind" value="" />
</Authentication>
```

### <a name="c-based-security-agent"></a>基于 C 的安全代理

编辑带有以下参数的 LocalConfiguration.json：

```json
"Authentication" : {
    "Identity" : "",
    "AuthenticationMethod" : "",
    "FilePath" : "",
    "DeviceId" : "",
    "HostName" : ""
}
```

## <a name="see-also"></a>请参阅

- [安全代理概述](security-agent-architecture.md)
- [部署安全代理](how-to-deploy-agent.md)
- [访问原始安全数据](how-to-security-data-access.md)
