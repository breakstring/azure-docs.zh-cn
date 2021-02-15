---
title: ServicePrincipalSelector UI 元素
description: 介绍 Azure 门户的 Microsoft.Common.ServicePrincipalSelector UI 元素。 提供一个控件，用于选择应用程序和文本框，以输入密码或证书指纹。
author: tfitzmac
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: tomfitz
ms.openlocfilehash: 2fdbbad467d8c762db485fc7935e9cef78313fd0
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184444"
---
# <a name="microsoftcommonserviceprincipalselector-ui-element"></a>Microsoft.Common.ServicePrincipalSelector UI 元素

允许用户选择现有 [服务主体](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) 或注册新应用程序的控件。 选择 " **新建**" 时，请按照步骤注册新的应用程序。 选择现有应用程序时，此控件将提供一个文本框，用于输入密码或证书指纹。

## <a name="ui-samples"></a>UI 示例

可以使用默认应用程序、创建新的应用程序或使用现有的应用程序。

### <a name="use-default-application-or-create-new"></a>使用默认应用程序或创建新的应用程序

默认视图取决于属性中的值 `defaultValue` ， **服务主体类型** 设置为 " **新建**"。 如果 `principalId` 属性包含有效的全局唯一标识符 (GUID) ，则控件会搜索应用程序的 `objectId` 。 如果用户不从控件中进行选择，则会应用默认值。

如果要注册新应用程序，请选择 " **更改选择** " 并显示 " **注册应用程序** " 对话框。 输入 " **名称**"、" **支持的帐户类型**"，然后选择 " **注册** " 按钮。

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-default.png" alt-text="ServicePrincipalSelector 初始视图。":::

注册新应用程序后，请使用 **身份验证类型** 输入密码或证书指纹。

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-authenticate.png" alt-text="ServicePrincipalSelector authentication。":::

### <a name="use-existing-application"></a>使用现有应用程序

若要使用现有应用程序，请选择 "**选择现有**"，然后选择 **"选择"。** 使用 " **选择应用程序** " 对话框可以搜索应用程序的名称。 在结果中，选择应用程序，然后选择 " **选择** " 按钮。 选择应用程序后，该控件将显示 " **身份验证类型** " 以输入密码或证书指纹。

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-existing.png" alt-text="ServicePrincipalSelector 选择现有应用程序。":::

## <a name="schema"></a>架构

```json
{
  "name": "ServicePrincipal",
  "type": "Microsoft.Common.ServicePrincipalSelector",
  "label": {
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type",
    "sectionHeader": "Service Principal"
  },
  "toolTip": {
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type"
  },
  "defaultValue": {
    "principalId": "<default guid>",
    "name": "(New) default App Id"
  },
  "constraints": {
    "required": true,
    "regex": "^[a-zA-Z0-9]{8,}$",
    "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
  },
  "options": {
    "hideCertificate": false
  },
  "visible": true
}
```

## <a name="remarks"></a>备注

- 必需的属性如下所示：
  - `name`
  - `type`
  - `label`
  - `defaultValue`：指定默认的 `principalId` 和 `name`。

- 可选属性如下所示：
  - `toolTip`：将工具提示 `infoBalloon` 附加到每个标签。
  - `visible`：隐藏或显示控件。
  - `options`：指定是否应使证书指纹选项可用。
  - `constraints`：用于密码验证的正则表达式约束。

## <a name="example"></a>示例

下面是 `Microsoft.Common.ServicePrincipalSelector` 控件的一个示例。 `defaultValue` 属性将 `principalId` 设置为 `<default guid>`（默认应用程序标识符 GUID 的占位符）。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [],
    "steps": [
      {
        "name": "SPNcontrol",
        "label": "SPNcontrol",
        "elements": [
          {
            "name": "ServicePrincipal",
            "type": "Microsoft.Common.ServicePrincipalSelector",
            "label": {
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type",
              "sectionHeader": "Service Principal"
            },
            "toolTip": {
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type"
            },
            "defaultValue": {
              "principalId": "<default guid>",
              "name": "(New) default App Id"
            },
            "constraints": {
              "required": true,
              "regex": "^[a-zA-Z0-9]{8,}$",
              "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
            },
            "options": {
              "hideCertificate": false
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "appId": "[steps('SPNcontrol').ServicePrincipal.appId]",
      "objectId": "[steps('SPNcontrol').ServicePrincipal.objectId]",
      "password": "[steps('SPNcontrol').ServicePrincipal.password]",
      "certificateThumbprint": "[steps('SPNcontrol').ServicePrincipal.certificateThumbprint]",
      "newOrExisting": "[steps('SPNcontrol').ServicePrincipal.newOrExisting]",
      "authenticationType": "[steps('SPNcontrol').ServicePrincipal.authenticationType]"
    }
  }
}
```

## <a name="example-output"></a>示例输出

`appId` 是所选的或所创建的应用程序注册的 ID。 `objectId`是为所选应用程序注册配置的服务主体的对象 id 的数组。

如果没有从控件中进行选择，则 `newOrExisting` 属性值为 **new**：

```json
{
  "appId": {
    "value": "<default guid>"
  },
  "objectId": {
    "value": ["<default guid>"]
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "new"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

当从控件中选择 " **新建** " 或 "现有应用程序" 时， `newOrExisting` 属性值为 " **现有**"：

```json
{
  "appId": {
    "value": "<guid>"
  },
  "objectId": {
    "value": ["<guid>"]
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "existing"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

## <a name="next-steps"></a>后续步骤

- 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。
- 有关 UI 元素中的公用属性的说明，请参阅 [CreateUiDefinition 元素](create-uidefinition-elements.md)。