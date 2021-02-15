---
title: 转发警报信息
description: 可以通过使用转发规则将警报信息发送给合作伙伴系统。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/02/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 528ea5a6b05dea59cb397bf32297f05b6cdc9be2
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522727"
---
# <a name="forward-alert-information"></a>转发警报信息

可以将警报信息发送给与 Azure Defender for IoT 集成的合作伙伴、syslog 服务器、电子邮件地址等。 使用转发规则可以快速将警报信息传递给安全利益干系人。  

系统会提供 Syslog 和其他默认转发操作。 与合作伙伴供应商（如 Microsoft Azure Sentinel、ServiceNow 或 Splunk）集成时，可能会出现更多转发操作。

:::image type="content" source="media/how-to-work-with-alerts-sensor/alert-information-screen.png" alt-text="警报信息。":::

用于 IoT 管理员的 Defender 有权使用转发规则。

## <a name="about-forwarded-alert-information"></a>关于转发的警报信息

警报提供了有关大量安全和操作事件的信息。 例如：

  - 警报的日期和时间

  - 检测到事件的引擎

  - 警报标题和描述性消息

  - 警报严重性

  - 源和目标的名称和 IP 地址

  - 检测到可疑流量

:::image type="content" source="media/how-to-work-with-alerts-sensor/address-scan-detected-screen.png" alt-text="检测到地址扫描。":::

创建转发规则时，会将相关信息发送到合作伙伴系统。

## <a name="create-forwarding-rules"></a>创建转发规则

若要创建新转发规则：

1. 选择侧边菜单上的 " **转发** "。

   ：： image type = "content" source = "media/工作方式-警报-传感器/create-forwarding-rule-screen.png" alt-text = "创建转发规则图标。"：：：

2. 选择 " **创建转发规则**"。

   :::image type="content" source="media/how-to-work-with-alerts-sensor/create-a-forwardong-rule.png" alt-text="创建新转发规则。":::

3. 输入转发规则的名称。

### <a name="forwarding-rule-criteria"></a>转发规则条件 

定义触发转发规则所依据的条件。 使用转发规则条件可帮助确定和管理从传感器发送到外部系统的信息量。 提供了以下选项：

**协议**：如果检测到的流量在特定协议上运行，则仅触发转发规则。 从下拉列表中选择所需的协议，或选择 "全部"。

**引擎**：选择所需的引擎或选择 "全部"。 将发送所选引擎发出的警报。

**严重性级别**：这是在严重性级别方面要转发的最小事件。 例如，如果选择 " **次要**"、"次要警报"，则将转发此严重级别以上的任何警报。 级别是预定义的。

### <a name="forwarding-rule-actions"></a>转发规则操作

转发规则操作指示传感器将警报信息转发给合作伙伴供应商或服务器。 可以为每个转发规则创建多个操作。

除了随系统提供的转发操作之外，在与合作伙伴供应商集成时，其他操作可能会变为可用。 

#### <a name="email-address-action"></a>电子邮件地址操作

发送包含警报信息的邮件。 可以为每个规则输入一个电子邮件地址。

为转发规则定义电子邮件：

1. 输入单个电子邮件地址。 如果需要发送多个邮件，请创建另一个操作。

2. 在 SIEM 上输入警报检测的时间戳的时区。

3. 选择“提交”。

#### <a name="syslog-server-actions"></a>Syslog 服务器操作

支持以下格式：

- 短信

- CEF 消息

- LEEF 消息

- 对象消息

:::image type="content" source="media/how-to-work-with-alerts-sensor/create-actions-rule.png" alt-text="创建转发规则操作。":::

输入以下参数：

- Syslog 主机名称和端口。

- 协议 TCP 和 UDP。

- SIEM 上警报检测的时间戳的时区。

- TLS 加密证书文件和 CEF 服务器的密钥文件 (可选) 。
    
:::image type="content" source="media/how-to-work-with-alerts-sensor/configure-encryption.png" alt-text="为转发规则配置加密。":::

| Syslog 文本消息输出字段 | 说明 |
|--|--|
| 日期和时间 | Syslog 服务器计算机接收信息的日期和时间。 |
| 优先级 | User. 警报 |
| 主机名 | 传感器 IP 地址 |
| 协议 | TCP 或 UDP |
| Message | 传感器：传感器名称。<br /> 警报：警报的标题。<br /> 类型：警报的类型。 可能 **违反协议**、 **策略冲突**、 **恶意软件**、 **异常** 或 **操作**。<br /> 严重性：警报的严重性。 可以是 " **警告**"、" **次要**"、" **主要**" 或 " **严重**"。<br /> 源：源设备名称。<br /> 源 IP：源设备 IP 地址。<br /> Destination：目标设备名称。<br /> 目标 IP：目标设备的 IP 地址。<br /> 消息：警报的消息。<br /> 警报组：与警报关联的警报组。 |


| Syslog 对象输出 | 说明 |
|--|--|
| 日期和时间 |   Syslog 服务器计算机接收信息的日期和时间。 |  
| 优先级 |    User. 警报 | 
| 主机名 |    传感器 IP | 
| Message | 传感器名称：设备的名称。 <br /> 警报时间：检测到警报的时间：可能与 syslog 服务器计算机的时间不同，具体取决于转发规则的时区配置。 <br /> 警报标题：警报的标题。 <br /> 警报消息：警报的消息。 <br /> 警报严重性：警报的严重性： **Warning**、 **小调**、 **主编** 或 **严重**。 <br /> 警报类型： **协议冲突**、 **策略冲突**、 **恶意软件**、 **异常** 或 **操作**。 <br /> 协议：警报的协议。  <br /> **Source_MAC**：源设备的 IP 地址、名称、供应商或操作系统。 <br /> Destination_MAC：目标的 IP 地址、名称、供应商或 OS。 如果缺少数据，则该值将为 **N/A**。 <br /> alert_group：与警报关联的警报组。 |


| Syslog CEF 输出格式 | 说明 |
|--|--|
| 日期和时间 | Syslog 服务器计算机接收信息的日期和时间。 |
| 优先级 | User. 警报 | 
| 主机名 | 传感器 IP 地址 |
| Message | CEF：0 <br />适用于 IoT 的 Azure Defender <br />传感器名称：传感器设备的名称。 <br />传感器版本 <br />警报标题：警报的标题。 <br />消息：警报的消息。 <br />协议：警报的协议。 <br />严重性： **Warning**、 **小调**、 **主编** 或 **严重**。 <br />类型： **协议冲突**、 **策略冲突**、 **恶意软件**、 **异常** 或 **操作**。 <br /> 开始时间：检测到警报的时间。 <br />可能不同于 syslog 服务器计算机的时间，具体取决于转发规则的时区配置。 <br />src_ip：源设备的 IP 地址。  <br />dst_ip：目标设备的 IP 地址。<br />cat：与警报关联的警报组。  |

| Syslog LEEF 输出格式 | 说明 |
|--|--|
| 日期和时间 |   Syslog 服务器计算机接收信息的日期和时间。 |  
| 优先级 |    User. 警报 | 
| 主机名 |    传感器 IP |
| Message | 传感器名称： Azure Defender for IoT 设备的名称。 <br />LEEF：1。0 <br />适用于 IoT 的 Azure Defender <br />传感器  <br />传感器版本 <br />Azure Defender IoT 警报 <br />title：警报的标题。 <br />消息：警报的消息。 <br />协议：警报的协议。<br />严重性： **Warning**、 **小调**、 **主编** 或 **严重**。 <br />类型：警报的类型： **协议冲突**、 **策略冲突**、 **恶意软件**、 **异常** 或 **操作**。 <br />start：警报的时间。请注意，它可能不同于 syslog 服务器计算机的时间。  (这取决于时区配置。 )  <br />src_ip：源设备的 IP 地址。<br />dst_ip：目标设备的 IP 地址。 <br />cat：与警报关联的警报组。 |

输入所有信息后，选择 " **提交**"。

#### <a name="netwitness-action"></a>NetWitness 操作

将警报信息发送到 NetWitness 服务器。

定义 NetWitness 转发参数：

1. 输入 NetWitness **主机名** 和 **端口** 信息。

2. 在 SIEM 上输入警报检测的时间戳的时区。

   :::image type="content" source="media/how-to-work-with-alerts-sensor/add-timezone.png" alt-text="将时区添加到转发规则。":::

3. 选择“提交”。

#### <a name="integrated-vendor-actions"></a>集成供应商操作

你可能已将系统与安全、设备管理或其他行业供应商集成。 这些集成使你能够：

  - 发送警报信息。

  - 发送设备清单信息。

  - 与供应商端防火墙通信。

集成有助于桥接以前孤立的安全解决方案，增强设备的可见性，并加速系统范围内的响应，从而更快地降低风险。

使用 "操作" 部分输入与集成供应商进行通信所需的凭据和其他信息。

有关为集成设置转发规则的详细信息，请参阅相关的合作伙伴集成文章。

### <a name="test-forwarding-rules"></a>测试转发规则

测试转发规则中定义的传感器和伙伴服务器之间的连接：

1. 从 " **转发规则** " 对话框中选择规则。

2. 选择 " **更多** " 框。

3. 选择 " **发送测试消息**"。

4. 请前往你的合作伙伴系统，验证是否已收到该传感器发送的信息。

### <a name="edit-and-delete-forwarding-rules"></a>编辑和删除转发规则 

编辑转发规则：

- 在 "**转发规则**" 屏幕上，在 "**更多**" 下拉菜单中选择 "**编辑**"。 进行所需的更改，然后选择 " **提交**"。

删除转发规则：

- 在 "**转发规则**" 屏幕上，在 "**更多**" 下拉菜单中选择 "**删除**"。 在 **警告** 对话框中，选择 **"确定"**。

### <a name="forwarding-rules-and-alert-exclusion-rules"></a>转发规则和警报排除规则

管理员可能已经定义了警报排除规则。 这些规则通过指示传感器忽略基于各种参数的警报事件，帮助管理员更精细地控制警报触发。 这些参数可能包括设备地址、警报名称或特定传感器。

这意味着，根据管理员创建的排除规则，可能会忽略您定义的转发规则。 在本地管理控制台中定义了排除规则。

## <a name="next-steps"></a>后续步骤

[加速警报工作流](how-to-accelerate-alert-incident-response.md)
