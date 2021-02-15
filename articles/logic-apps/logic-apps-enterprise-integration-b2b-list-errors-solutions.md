---
title: B2B 方案中常见错误和问题的解决方案
description: 在 Azure 逻辑应用中对 B2B 方案进行故障排除时，针对常见错误和问题查找解决方案
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/02/2017
ms.openlocfilehash: 6400cfe7e524dcc16e08c2bba7dfba4a62d00b2e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86232553"
---
# <a name="b2b-errors-and-solutions-for-azure-logic-apps"></a>Azure 逻辑应用的 B2B 错误和解决方案

本文可帮助你解决可能会出现在逻辑应用 B2B 方案中的错误，并提供更正这些错误的适当操作。

## <a name="agreement-resolution"></a>协议解析

### <a name="no-agreement-found"></a>没有找到协议 

**错误说明**：找不到协议解析参数的协议。

**用户操作**：应将协议添加到具有议定的业务标识的集成帐户。 业务标识应与输入的消息 ID 相匹配。

### <a name="no-agreement-found-with-identities"></a>没有找到具有标识的协议

**错误说明**：找不到标识为 "AS2Identity"：： "Partner1" and'AS2Identity "：：" Partner3 "的协议

**用户操作**：为协议配置的 AS2-From 或 AS2-To 无效。 更正 AS2 消息和“AS2-From”或“AS2-To”标头或协议，以匹配具有协议配置的 AS2 消息标头中的 AS2 ID。

## <a name="as2"></a>AS2

### <a name="missing-as2-message-headers"></a>缺少 AS2 消息标头  

**错误说明**： AS2 标头无效。 “AS2-To”或“AS2-From”中的一个标头为空。

**用户操作**：收到的 AS2 消息不包含 AS2-From 或 AS2-To 或两个标头。 检查 AS2 消息的 AS2-From 或 AS2-To 标头并基于协议配置进行更正。

### <a name="missing-as2-message-body-and-headers"></a>缺少 AS2 消息正文和标头    

**错误说明**：请求内容为 null 或为空。

**用户操作**：收到的 AS2 消息不包含消息正文。

### <a name="as2-message-decryption-failure"></a>AS2 消息解密失败

**错误说明**： [已处理/错误：解密-失败]

**用户操作**：将添加到 @base64ToBinary AS2Message，然后再发送到合作伙伴。

例如：

```json
"HTTP": {
   "inputs": {
   "body": "@base64ToBinary(body('Encode_to_AS2_message')?['AS2Message']?['Content'])",
   "headers": "@body('Encode_to_AS2_message')?['AS2Message']?['OutboundHeaders']",
   "method": "POST",
   "uri": "xxxxx.xxx"
},
``` 

### <a name="mdn-decryption-failure"></a>MDN 解密失败

**错误说明**： [已处理/错误：解密-失败]

**用户操作**：将添加到 MDN，然后将其 @base64ToBinary 发送到合作伙伴。

例如：

```json
"Response": {
   "inputs": {
   "body": "@base64ToBinary(body('Decode_AS2_message')?['OutgoingMDN']?['Content'])",
   "headers": "@body('Decode_AS2_message')?['OutgoingMDN']?['OutboundHeaders']",
   "statusCode": 200
},               
``` 

### <a name="missing-signing-certificate"></a>缺少签名证书

**错误说明**：尚未为 AS2 参与方配置签名证书。 AS2-From：partner1 AS2-To：partner2

**用户操作**：配置具有正确的签名证书的 AS2 协议设置。

## <a name="x12-and-edifact"></a>X12 和 EDIFACT

### <a name="leading-or-trailing-space-found"></a>找到前导或尾部空格    

**错误说明**：在分析过程中遇到错误。 由于以下错误，将挂起 ID 为 "123456"、发送方 ID 为 "Partner1"、接收方 ID 为 "Partner2"、接收方 id 为 "" 的交换 987654)  (中包含的 ID 为 "" 的 EDIFACT 事务集： "前导尾随分隔符"

**用户操作**：要配置为允许前导空格和尾随空格的协议设置。 将协议设置编辑为允许前导和尾部空格。

![允许空格](./media/logic-apps-enterprise-integration-b2b-list-errors-solutions/leadingandtrailing.png)

### <a name="duplicate-check-has-enabled-in-the-agreement"></a>协议已启用重复项检查

**错误说明**：重复控制编号

**用户操作**：此错误表明收到的消息具有重复的控制编号。 更正控制编号并重新发送消息。

### <a name="missing-schema-in-the-agreement"></a>协议中缺少架构

**错误说明**：在分析过程中遇到错误。 Id 为 "564220001"、id 为 "000056422"、发送方 ID 为 "12345678"、接收方 ID 为 "87654321" 的交换中56422包含的 id 为 "" 的 X12 事务集被挂起，出现以下错误： "该消息具有未知的文档类型，无法解析为协议中配置的任何现有架构"

**用户操作**：在协议设置中配置架构。

### <a name="incorrect-schema-in-the-agreement"></a>协议中架构不正确

**错误说明**：消息具有未知的文档类型，无法解析为协议中配置的任何现有架构。

**用户操作**：在协议设置中配置正确的架构。

## <a name="flat-file"></a>平面文件

### <a name="input-message-with-no-body"></a>输入的消息没有正文

**错误说明**： InvalidTemplate。 无法处理操作 "Flat_File_Decoding" 输入中的行 "1" 和列 "1902" 处的模板语言表达式：所需属性“内容”应该有一个值，但为空。 路径 ''.'.

**用户操作**：此错误表明输入的消息不包含正文。
