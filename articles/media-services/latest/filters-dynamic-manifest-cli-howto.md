---
title: 使用 CLI 通过 Azure 媒体服务创建筛选器
description: 本文介绍如何通过 CLI 使用 Azure 媒体服务 v3 创建筛选器。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 75df01ff65a0910dab140942b520978cd7728775
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897571"
---
# <a name="creating-filters-with-cli"></a>使用 CLI 创建筛选器

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

将内容传送到客户（直播流活动或点播视频）时，客户端所需的灵活性可能比默认资产的清单文件中描述的灵活性更高。 使用 Azure 媒体服务可为内容定义帐户筛选器和资产筛选器。 

有关此功能及其使用方案的详细说明，请参阅[动态清单](filters-dynamic-manifest-overview.md)和[筛选器](filters-concept.md)。

本主题说明如何为点播视频资产配置筛选器，并使用适用于媒体服务 v3 的 CLI 创建[帐户筛选器](/cli/azure/ams/account-filter?view=azure-cli-latest)和[资产筛选器](/cli/azure/ams/asset-filter?view=azure-cli-latest)。 

> [!NOTE]
> 请确保查看 [presentationTimeRange](filters-concept.md#presentationtimerange)。

## <a name="prerequisites"></a>必备条件 

- [创建媒体服务帐户](./create-account-howto.md)。 请务必记住资源组名称和媒体服务帐户名称。 

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-filter"></a>定义筛选器 

以下示例定义添加到最终清单的曲目择条件。 此筛选器包括属于 EC-3 的任何音频曲目和比特率在 0-1000000 范围内的任何视频曲目。

> [!TIP]
> 如果计划在 REST 中定义 **筛选器**，请注意需要包括“Properties”包装器 JSON 对象。  

```json
[
    {
        "trackSelections": [
            {
                "property": "Type",
                "value": "Audio",
                "operation": "Equal"
            },
            {
                "property": "FourCC",
                "value": "EC-3",
                "operation": "NotEqual"
            }
        ]
    },
    {
        "trackSelections": [
            {
                "property": "Type",
                "value": "Video",
                "operation": "Equal"
            },
            {
                "property": "Bitrate",
                "value": "0-1000000",
                "operation": "Equal"
            }
        ]
    }
]
```

## <a name="create-account-filters"></a>创建帐户筛选器

以下 [az ams account-filter](/cli/azure/ams/account-filter?view=azure-cli-latest) 命令创建帐户筛选器，其中包含[之前定义的](#define-a-filter)筛选器曲目选项。 

该命令允许你传递一个可选 `--tracks` 参数，该参数包含表示曲目选择的 JSON。  使用 @{file} 从文件加载 JSON。 如果在本地使用 Azure CLI，请指定整个文件路径：

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

此外，请参阅[筛选器的 JSON 示例](/rest/api/media/accountfilters/createorupdate#create-an-account-filter)。

## <a name="create-asset-filters"></a>创建资产筛选器

以下 [az ams asset-filter](/cli/azure/ams/asset-filter?view=azure-cli-latest) 命令创建资产筛选器，其中包含[之前定义的](#define-a-filter)筛选器曲目选项。 

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

此外，请参阅[筛选器的 JSON 示例](/rest/api/media/assetfilters/createorupdate#create-an-asset-filter)。

## <a name="associate-filters-with-streaming-locator"></a>将筛选器与流定位器关联

可以指定资产或帐户筛选器的列表，这些筛选器将应用于流定位器。 [动态打包程序（流式处理终结点）](dynamic-packaging-overview.md)将此筛选器列表与客户端在 URL 中指定的筛选器一起应用。 此组合将生成[动态清单](filters-dynamic-manifest-overview.md)，该清单基于你在流定位器上指定的“URL + 筛选器”中的筛选器。 如果要应用筛选器，但不希望在 URL 中公开筛选器名称，建议使用此功能。

以下 CLI 代码显示了如何创建流定位器并指定 `filters`。 这是一个可选属性，它采用以空格分隔的资产筛选器名称和/或帐户筛选器名称的列表。

```azurecli
az ams streaming-locator create -a amsAccount -g resourceGroup -n streamingLocatorName \
                                --asset-name assetName \                               
                                --streaming-policy-name policyName \
                                --filters filterName1 filterName2
                                
```

## <a name="stream-using-filters"></a>使用筛选器的流

定义筛选器后，客户端可以在流式 URL 中使用它们。 筛选器可应用到自适应比特率流式处理协议：Apple HTTP Live Streaming (HLS)、MPEG-DASH 和平滑流式处理。

下表显示了一些包含筛选器的 URL 示例：

|协议|示例|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|平滑流|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-step"></a>后续步骤

[流视频](stream-files-tutorial-with-api.md) 

## <a name="see-also"></a>另请参阅

[Azure CLI](/cli/azure/ams?view=azure-cli-latest)
