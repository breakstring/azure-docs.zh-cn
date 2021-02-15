---
title: 管理录制策略 - Azure
description: 本主题介绍如何管理录制策略。
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: ec72f28496c1392b9d95134c343e1892998a0c28
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2021
ms.locfileid: "99224983"
---
# <a name="manage-recording-policy"></a>管理录制策略

可以使用 IoT Edge 上的实时视频分析进行[连续视频录制](continuous-video-recording-concept.md)，持续数周或数月将视频录制到云中。 可以使用 Azure 存储中内置的[生命周期管理工具](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal)来管理云存档的时间长度（天）。  

媒体服务帐户已链接到 Azure 存储帐户，将视频录制到云中时，内容会写入媒体服务[资产](../latest/assets-concept.md)。 每个资产都映射到存储帐户中的一个容器。 生命周期管理使你可以为存储帐户定义 [策略](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#policy) ，你可以在其中指定如下 [规则](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#rules) 。

```
{
  "rules": [
    {
      "name": "NinetyDayRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "delete": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

上述规则：

* 应用于存储帐户中的所有块 blob。
* 指定当 blob 期限超过 30 天时，它们将从[热访问层移动到冷访问层](../../storage/blobs/storage-blob-storage-tiers.md?tabs=azure-portal)。
* 如果 blob 期限超过 90 天，则将其删除。

使用实时视频分析记录到资产时，指定一个 `segmentLength` 属性，该属性指示模块在将视频 (的最短持续时间（以秒为单位）聚合到云之前) 。 你的资产将包含一系列段，其中每个段的创建时间戳 `segmentLength` 比上一个更新。 当生命周期管理策略开始时，它会删除早于指定阈值的段。 但是，你将继续能够通过媒体服务 Api 访问和播放其余段。 有关详细信息，请参阅 [播放录制](playback-recordings-how-to.md)。 

## <a name="limitations"></a>限制

以下是生命周期管理的一些已知限制：

* 策略中最多可以有 100 个规则，每个规则最多可以指定 10 个容器。 因此，如果你需要具有不同的录制策略（例如，正对停车场的摄像头为 3 天存档、装货码头的摄像头为 30 天、收银台后面的摄像头为 180 天），请使用媒体服务帐户，为最多 1000 个摄像头自定义规则。
* 生命周期管理策略更新不是即时的。 有关更多详细信息，请参阅[常见问题解答部分](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#faq)。
* 如果选择应用将 blob 移动到冷访问层的策略，则该存档部分的播放可能会受到影响。 你可能会看到其他延迟，或偶尔发生的错误。 媒体服务不支持在存档层中播放内容。

## <a name="next-steps"></a>后续步骤

[播放录制内容](playback-recordings-how-to.md)
