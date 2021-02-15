---
title: 机密更改 Key Vault 状态时发送电子邮件
description: 使用逻辑应用响应 Key Vault 机密更改的指南
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 11/11/2019
ms.author: mbaldwin
ms.openlocfilehash: 9c522d870a25b3df34ab6a0cf1c1e944a6462685
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013983"
---
# <a name="use-logic-apps-to-receive-email-about-status-changes-of-key-vault-secrets"></a>使用逻辑应用接收有关 Key Vault 机密状态更改的电子邮件

本指南介绍如何使用 [Azure 逻辑应用](../../logic-apps/index.yml)对通过 [Azure 事件网格](../../event-grid/index.yml)收到的 Azure Key Vault 事件做出响应。 本文结束时，我们会完成设置一个 Azure 逻辑应用。每次在 Azure Key Vault 中创建机密时，该应用就会发送一封通知电子邮件。

有关 Azure Key Vault/Azure 事件网格集成的概述，请参阅[使用 Azure 事件网格监视 Key Vault](event-grid-overview.md)。

## <a name="prerequisites"></a>先决条件

- 受 Azure 逻辑应用支持的任何电子邮件提供程序（如 Office 365 Outlook）中的电子邮件帐户。 此电子邮件帐户用于发送事件通知。 有关支持的逻辑应用连接器的完整列表，请参阅[连接器概述](/connectors)
- Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- Azure 订阅中的 Key Vault。 可以按照[使用 Azure CLI 在 Azure Key Vault 中设置和检索机密](../secrets/quick-create-cli.md)中的步骤来快速创建新的 Key Vault。
- 有关将事件网格注册为资源提供程序，请参阅[资源提供程序注册](../../azure-resource-manager/management/resource-providers-and-types.md)

## <a name="create-a-logic-app-via-event-grid"></a>通过事件网格创建逻辑应用

首先，通过事件网格处理程序创建逻辑应用，并订阅 Azure Key Vault“SecretNewVersionCreated”事件。

若要创建 Azure 事件网格订阅，请执行以下操作：

1. 在 Azure 门户中，转到 Key Vault，选择“事件”>“入门”并单击“逻辑应用”

    
    ![Key Vault - 事件页](../media/eventgrid-logicapps-kvsubs.png)

1. 在“逻辑应用设计器”上，验证连接，然后单击“继续” 
 
    ![逻辑应用设计器 - 连接](../media/eventgrid-logicappdesigner1.png)

1. 在“当资源事件发生时”屏幕上执行以下步骤：
    - 默认保留“订阅”和“资源名称”。
    - 为“资源类型”选择“Microsoft.KeyVault.vaults”。
    - 为“Event Type Item - 1”选择“Microsoft.KeyVault.SecretNewVersionCreated”。

    ![逻辑应用设计器 - 事件处理程序](../media/eventgrid-logicappdesigner2.png)

1. 选择“+ 新步骤”。此时会打开一个窗口，供用户“选择操作”。
1. 搜索“电子邮件”。 根据你的电子邮件提供程序，找到并选择匹配的连接器。 本教程使用 **Office 365 Outlook**。 使用其他电子邮件提供程序时执行的步骤类似。
1. 选择“发送电子邮件 (V2)”操作。

   ![逻辑应用设计器 - 发送电子邮件](../media/eventgrid-logicappdesigner3.png)

1. 生成电子邮件模板：
    - **发件人：** 输入接收通知电子邮件的电子邮件地址。 对于本教程，请使用你可以访问的电子邮件帐户进行测试。
    - **主题** 和 **正文**：编写电子邮件的文本。 从选择器工具中选择 JSON 属性，以添加基于事件数据的动态内容。 可以使用 `@{triggerBody()?['Data']}` 检索事件的数据。

    电子邮件模板可能如以下示例所示。

    ![逻辑应用设计器 - 电子邮件正文](../media/eventgrid-logicappdesigner4.png)

8. 单击“另存为”。
9. 为新的逻辑应用输入“名称”，并单击“创建”。
    
    ![逻辑应用设计器 - 创建](../media/eventgrid-logicappdesigner5.png)

## <a name="test-and-verify"></a>测试和验证

1.  在 Azure 门户上，转到 Key Vault，然后选择“事件”>“事件订阅”。  验证是否已创建新的订阅
    
    ![逻辑应用设计器 - 测试和验证](../media/eventgrid-logicapps-kvnewsubs.png)

1.  转到 Key Vault，依次选择“机密”和“+ 生成/导入”。 创建用于测试的新机密，命名密钥并将其余参数保留为默认设置。

    ![Key Vault - 创建机密](../media/eventgrid-logicapps-kv-create-secret.png)

1. 在“创建机密”屏幕上提供任意名称和值，并选择“创建”。

创建机密后，会在配置的地址收到电子邮件。

## <a name="next-steps"></a>后续步骤

- 概述：[通过 Azure 事件网格监视 Key Vault](event-grid-overview.md)
- 如何：[将 Key Vault 通知路由到 Azure 自动化](event-grid-tutorial.md)。
- [Azure Key Vault 的 Azure 事件网格事件架构](../../event-grid/event-schema-key-vault.md)
- 详细了解 [Azure 事件网格](../../event-grid/index.yml)。
- 详细了解 [Azure 应用服务的逻辑应用功能](../../logic-apps/index.yml)。