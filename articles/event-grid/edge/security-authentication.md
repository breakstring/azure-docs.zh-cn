---
title: 安全性和身份验证-Azure 事件网格 IoT Edge |Microsoft Docs
description: IoT Edge 上的事件网格中的安全性和身份验证。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 42f6107ff79d6262cdc0a35cf972cf65d3a9a802
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171374"
---
# <a name="security-and-authentication"></a>安全性和身份验证

安全性和身份验证是一种高级概念，需要首先熟悉事件网格基础知识。 如果你不熟悉 IoT Edge 上的事件网格，请从 [此处](concepts.md) 开始。 事件网格模块在 IoT Edge 上的现有安全基础结构上构建。 有关详细信息和设置，请参阅 [此文档](../../iot-edge/security.md) 。

以下部分详细介绍了如何保护和验证这些设置：

* TLS 配置
* 入站客户端身份验证
* 出站服务器身份验证
* 出站客户端身份验证

>[!IMPORTANT]
>事件网格模块安全性和身份验证利用了 IoT Edge 上可用的现有基础结构。 假设 IoT Edge 子系统是安全的。

>[!IMPORTANT]
>**默认情况下**，事件网格配置是安全的。 以下小节介绍了可用于替代身份验证的各个方面的所有选项和可能的值 (s) 。 在进行任何更改之前了解影响。 要使更改生效，需要重新部署事件网格模块。

## <a name="tls-configuration-aka-server-authentication"></a>TLS 配置 (服务器身份验证) 

事件网格模块承载 HTTP 和 HTTPS 终结点。 IoT Edge 的安全守护程序为每个 IoT Edge 模块分配一个服务器证书。 我们使用服务器证书来保护终结点。 过期后，模块将自动使用来自 IoT Edge security daemon 的新证书进行刷新。

默认情况下，仅允许 HTTPS 通信。 可以通过  **inbound__serverAuth__tlsPolicy** 配置来覆盖此行为。 下表捕获此属性 (s) 的可能值。

| 可能值 | 描述 |
| ---------------- | ------------ |
| 严格 | 默认。 仅启用 HTTPS
| 已启用 | 同时启用 HTTP 和 HTTPS
| 已禁用 | 仅启用 HTTP

## <a name="inbound-client-authentication"></a>入站客户端身份验证

客户端是执行管理和/或运行时操作的实体。 客户端可以是其他 IoT Edge 模块，非 IoT 应用程序。

事件网格模块支持两种类型的客户端身份验证：

*  (SAS) 基于密钥的共享访问签名
* 基于证书

默认情况下，事件网格模块配置为仅接受基于证书的身份验证。 在启动时，事件网格模块从 IoT Edge 安全守护程序检索 "TrustBundle"，并使用它来验证任何客户端证书。 将拒绝未解析到此链的客户端证书 `UnAuthorized` 。

### <a name="certificate-based-client-authentication"></a>基于证书的客户端身份验证

默认情况下启用基于证书的身份验证。 你可以选择通过属性 **inbound__clientAuth__clientCert__enabled**禁用基于证书的身份验证。 下表捕获了)  (的可能值。

| 可能值 | 描述 |
| ----------------  | ------------ |
| true | 默认。 要求所有请求进入事件网格模块，以显示客户端证书。 此外，您还需要配置 **inbound__clientAuth__clientCert__source**。
| false | 不要强制客户端提供证书。

下表捕获了可能的值 (s) 用于 **inbound__clientAuth__clientCert__source**

| 可能值 | 描述 |
| ---------------- | ------------ |
| IoT Edge | 默认。 使用 IoT Edge 的 Trustbundle 验证所有客户端证书。

如果客户端提供自签名，则默认情况下，事件网格模块将拒绝此类请求。 可以通过 **inbound__clientAuth__clientCert__allowUnknownCA** 属性选择允许自签名的客户端证书。 下表捕获了)  (的可能值。

| 可能值 | 描述 |
| ----------------  | ------------|
| true | 默认。 允许成功提供自签名证书。
| false | 如果提供自签名证书，则将失败请求。

>[!IMPORTANT]
>在生产方案中，可能需要将 **inbound__clientAuth__clientCert__allowUnknownCA** 设置为 **false**。

### <a name="sas-key-based-client-authentication"></a>基于 SAS 密钥的客户端身份验证

除了基于证书的身份验证外，事件网格模块还可以执行基于 SAS 密钥的身份验证。 SAS 密钥类似于事件网格模块中配置的机密，它应用于验证所有传入调用。 客户端需要指定 HTTP 标头 "aeg" 中的机密。 如果不匹配，请求将被拒绝 `UnAuthorized` 。

用于控制基于 SAS 密钥的身份验证的配置是 **inbound__clientAuth__sasKeys__enabled**。

| 可能值 | 描述  |
| ----------------  | ------------ |
| true | 允许基于 SAS 密钥的身份验证。 需要 **inbound__clientAuth__sasKeys__key1** 或 **inbound__clientAuth__sasKeys__key2**
| false | 默认。 禁用基于 SAS 密钥的身份验证。

 **inbound__clientAuth__sasKeys__key1** 和 **Inbound__clientAuth__sasKeys__key2** 是配置事件网格模块以对照传入请求进行检查的键。 至少需要配置一个密钥。 发出请求的客户端需要将密钥显示为请求标头 "**aeg**" 的一部分。 如果同时配置了这两个密钥，则客户端可以显示其中一个密钥。

> [!NOTE]
>可以同时配置这两种身份验证方法。 在这种情况下，将首先检查 SAS 密钥，如果失败，则执行基于证书的身份验证。 若要成功执行请求，只需其中一种身份验证方法。

## <a name="outbound-client-authentication"></a>出站客户端身份验证

出站上下文中的客户端是指事件网格模块。 正在进行的操作是将事件传递给订阅服务器。 订阅模块被视为服务器。

IoT Edge 的安全守护程序为每个 IoT Edge 模块分配一个标识证书。 我们将标识证书用于传出呼叫。 过期后，模块将自动使用来自 IoT Edge security daemon 的新证书进行刷新。

**Outbound__clientAuth__clientCert__enabled**控制出站客户端身份验证的配置。

| 可能值 | 描述 |
| ----------------  | ------------ |
| true | 默认。 要求来自事件网格模块的所有传出请求显示证书。 需要配置 **outbound__clientAuth__clientCert__source**。
| false | 不要求事件网格模块提供其证书。

控制证书源的配置是 **outbound__clientAuth__clientCert__source**。

| 可能值 | 描述 |
| ---------------- | ------------ |
| IoT Edge | 默认。 使用 IoT Edge 安全守护程序配置的模块的标识证书。

### <a name="outbound-server-authentication"></a>出站服务器身份验证

事件网格订阅服务器的目标类型之一是 "Webhook"。 默认情况下，此类订阅服务器仅接受 HTTPS 终结点。

用于控制 webhook 目标策略 **outbound__webhook__httpsOnly**的配置。

| 可能值 | 描述 |
| ----------------  | ------------ |
| true | 默认。 仅允许具有 HTTPS 终结点的订阅服务器。
| false | 允许具有 HTTP 或 HTTPS 终结点的订阅服务器。

默认情况下，事件网格模块将验证订阅服务器的服务器证书。 可以通过重写 **outbound__webhook__skipServerCertValidation**来跳过验证。 可能的值包括：

| 可能值 | 描述 |
| ----------------  | ------------ |
| true | 不要验证订阅服务器的服务器证书。
| false | 默认。 验证订阅服务器的服务器证书。

如果订阅服务器的证书是自签名证书，则默认情况下，事件网格模块将拒绝此类订阅服务器。 若要允许自签名证书，可以重写 **outbound__webhook__allowUnknownCA**。 下表捕获了)  (的可能值。

| 可能值 | 描述 |
| ----------------  | ------------ |
| true | 默认。 允许成功提供自签名证书。
| false | 如果提供自签名证书，则将失败请求。

>[!IMPORTANT]
>在生产方案中，需要将 **outbound__webhook__allowUnknownCA** 设置为 **false**。

> [!NOTE]
>IoT Edge 环境生成自签名证书。 建议为生产工作负荷生成授权 Ca 颁发的证书，并将入站和出站上的 **allowUnknownCA** 属性设置为 **false**。

## <a name="summary"></a>总结

事件网格模块在 **默认情况下是安全**的。 建议保留这些默认值用于生产部署。

下面是配置时要使用的指导原则：

* 仅允许在模块中执行 HTTPS 请求。
* 仅允许基于证书的客户端身份验证。 仅允许众所周知的 Ca 颁发的证书。 不允许自签名证书。
* 禁止基于 SASKey 的客户端身份验证。
* 传出调用上始终显示事件网格模块的标识证书。
* 仅允许 HTTPS 订阅服务器用于 Webhook 目标类型。
* 始终验证订阅服务器的 Webhook 目标类型的服务器证书。 仅允许众所周知的 Ca 颁发的证书。 不允许自签名证书。

默认情况下，使用以下配置部署事件网格模块：

 ```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge",
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true",
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge",
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```
