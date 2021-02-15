---
title: 检索产品/服务状态-Azure Marketplace
description: 用于检索产品/服务的当前状态的 API。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 336f23f83c33bcee1887d0e41710e686b794a663
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87272005"
---
# <a name="retrieve-offer-status"></a>检索产品/服务状态

> [!NOTE]
> 云合作伙伴门户 Api 与集成，并将在合作伙伴中心继续工作。 转换引入了少量更改。 查看 [云合作伙伴门户 API 参考](./cloud-partner-portal-api-overview.md) 中列出的更改，确保你的代码在转换到合作伙伴中心后继续工作。 CPP Api 仅适用于过渡到合作伙伴中心之前已集成的现有产品;新产品应使用合作伙伴中心提交 Api。

检索产品/服务的当前状态。

  `GET  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/status?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI 参数

|  **名称**       |   **描述**                            |  **Data type** |
|  -------------  |  ------------------------------------------  |  ------------  |
|  publisherId    | 发布者标识符，例如 `Contoso`  |     字符串     |
|  offerId        | 唯一标识产品/服务的 GUID      |     字符串     |
|  api-version    | API 的最新版本                        |     Date       |
|  |  |

## <a name="header"></a>Header


|  名称           |  值               |
|  -------------  | -------------------  |
|  Content-Type   |  `application/json`  |
|  授权  | `Bearer YOUR_TOKEN`  |
|  |  |

## <a name="body-example"></a>正文示例

### <a name="response"></a>响应

``` json
  {
      "status": "succeeded",
      "messages": [],
      "steps": [
      {
          "estimatedTimeFrame": "< 15 min",
          "id": "displaydummycertify",
          "stepName": "Validate Pre-Requisites",
          "description": "Offer settings provided are validated.",
          "status": "complete",
          "messages": [
              {
                  "messageHtml": "Step completed.",
                  "level": "information",
                  "timestamp": "2018-03-16T17:50:45.7215661Z"
              }
          ],       
          "progressPercentage": 100
      },
      {
          "estimatedTimeFrame": "~2-3 days",
          "id": "displaycertify",
          "stepName": "Certification",
          "description": "Your offer is analyzed by our certification systems for issues.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 day",
          "id": "displayprovision",
          "stepName": "Provisioning",
          "description": "Your virtual machine is being replicated in our production systems.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 hour",
          "id": "displaypackage",
          "stepName": "Packaging and Lead Generation Registration",
          "description": "Your virtual machine is being packaged for customers. Additionally, lead systems are being configured and set up.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 hour",
          "id": "publisher-signoff",
          "stepName": "Publisher signoff",
          "description": "Offer is available to preview. Ensure that everything looks good before making your offer live.",
          "status": "complete",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "~2-5 days",
          "id": "live",
          "stepName": "Live",
          "description": "Offer is publicly visible and is available for purchase.",
          "status": "complete",
          "messages": [],
          "progressPercentage": 0
      }
      ],
      "previewLinks": [],
      liveLinks": [],
  }
```

### <a name="response-body-properties"></a>响应正文属性

|  **名称**             |    **描述**                                                                             |
| --------------------  |   -------------------------------------------------------------------------------------------- |
|  status               | 产品/服务的状态。 有关可能值的列表，请参阅下面的[产品/服务状态](#offer-status)。 |
|  messages             | 与产品/服务关联的消息数组                                                    |
|  steps                | 产品/服务在发布期间所经历的一系列步骤                      |
|  estimatedTimeFrame   | 以友好的格式估算完成此步骤所需的时间                       |
|  id                   | 步骤的标识符                                                                         |
|  stepName             | 步骤的名称                                                                               |
|  description          | 步骤的说明                                                                        |
|  status               | 步骤的状态。 有关可能值的列表，请参阅下面的[步骤状态](#step-status)。    |
|  messages             | 与步骤相关的消息数组                                                          |
|  processPercentage    | 步骤的完成百分比                                                              |
|  previewLinks         | *当前未实现*                                                                    |
|  liveLinks            | *当前未实现*                                                                    |
|  notificationEmails   | 对于迁移到合作伙伴中心的产品/服务已弃用。 已迁移产品的通知电子邮件将发送到 "帐户设置" 中 "卖方联系人信息" 下指定的电子邮件。<br><br>对于未迁移的产品/服务，则以逗号分隔的电子邮件地址列表，通知操作的进度        |
|  |  |

### <a name="response-status-codes"></a>响应状态代码

| **代码** |   **描述**                                                                                 |
| -------  |   ----------------------------------------------------------------------------------------------- |
|  200     |  `OK` - 请求已成功处理，并且已返回产品/服务的当前状态。 |
|  400     | `Bad/Malformed request` - 错误响应正文可能包含更多信息。                 |
|  404     | `Not found` - 指定的实体不存在。                                                |
|  |  |

### <a name="offer-status"></a>产品/服务状态

|  **名称**                    |    **描述**                                       |
|  --------------------------  |  ------------------------------------------------------  |
|  NeverPublished              | 产品/服务从未发布过。                          |
|  NotStarted                  | 产品/服务是新的，且未启动。                            |
|  WaitingForPublisherReview   | 产品/服务正在等待发布者批准。                 |
|  运行                     | 正在处理产品/服务提交。                     |
|  已成功                   | 产品/服务提交已完成处理。               |
|  已取消                    | 产品/服务提交已取消。                           |
|  已失败                      | 产品/服务提交失败。                                 |
|  |  |

### <a name="step-status"></a>步骤状态

|  **名称**                    |    **描述**                           |
|  -------------------------   |  ------------------------------------------  |
|  NotStarted                  | 步骤尚未开始。                        |
|  正在进行                  | 步骤正在运行。                             |
|  WaitingForPublisherReview   | 步骤正在等待发布者批准。      |
|  WaitingForApproval          | 步骤正在等待流程审批。        |
|  已阻止                     | 步骤被阻止。                             |
|  已拒绝                    | 步骤被拒绝。                            |
|  完成                    | 步骤已完成。                            |
|  已取消                    | 步骤已取消。                           |
|  |  |
