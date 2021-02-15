---
title: 在 Azure 仪表板上使用自定义 Markdown 磁贴
description: 了解如何向 Azure 仪表板添加 Markdown 磁贴，以便显示静态内容
ms.date: 01/08/2020
ms.topic: how-to
ms.custom: devx-track-js
ms.openlocfilehash: 5121142ecf568aa1ac9a7ec19f7211c6f9a6253f
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745751"
---
# <a name="use-a-markdown-tile-on-azure-dashboards-to-show-custom-content"></a>在 Azure 仪表板上使用 Markdown 磁贴显示自定义内容

可以向 Azure 仪表板添加 Markdown 磁贴，以便显示自定义的静态内容。 例如，可以在 Markdown 磁贴上显示基本的说明、图像或一组超链接。

## <a name="add-a-markdown-tile-to-your-dashboard"></a>向仪表板添加 Markdown 磁贴

1. 在 Azure 门户边栏中选择“仪表板”。 

   ![屏幕截图，显示门户边栏](./media/azure-portal-markdown-tile/azure-portal-nav.png)

1. 如果已创建任何自定义仪表板，请在仪表板视图中通过下拉列表选择会显示自定义 Markdown 磁贴的仪表板。 选择用于打开 **磁贴库** 的编辑图标。

   ![屏幕截图，显示仪表板编辑视图](./media/azure-portal-markdown-tile/azure-portal-dashboard-edit.png)

1. 在“磁贴库”中找到名为 **Markdown** 的磁贴，然后选择“添加”。   此时会将磁贴添加到仪表板，而“编辑 Markdown”窗格也会打开。 

1. 输入“标题”和“副标题”的值，这些值会在你转到另一字段后显示在磁贴上。  

   ![屏幕截图，显示输入标题和副标题后的结果](./media/azure-portal-markdown-tile/azure-portal-dashboard-enter-title.png)

1. 选择用于添加 Markdown 内容的选项之一：“内联编辑”或“使用 URL 插入内容”。  

   - 如果要直接输入 Markdown，请选择“内联编辑”。 

      ![屏幕截图，显示输入内联内容](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-inline-content.png)

   - 如果要使用以联机方式托管的现有 Markdown 内容，请选择“使用 URL 插入内容”  。

      ![屏幕截图，显示输入 URL](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-url.png)

      > [!NOTE]
      > 为了增加安全性，可以创建一个 Markdown 文件，将其存储在[启用了加密的 Azure 存储帐户 Blob](../storage/common/storage-service-encryption.md) 中，然后使用 URL 选项指向该文件。 通过存储帐户的加密选项来加密 Markdown 内容。 只有有权访问文件的用户才能查看仪表板上的 Markdown 内容。 你可能需要在存储帐户上设置 [跨域资源共享 (CORS)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) 规则，以便 Azure 门户 (_https://portal.azure.com/_) 可以访问 blob 中的 markdown 文件。

1. 选择“完成”，关闭“编辑 Markdown”窗格。 内容会显示在 Markdown 磁贴上，该磁贴可以通过在右下角拖动图柄来重设大小。

   ![屏幕截图，显示自定义 Markdown 磁贴](./media/azure-portal-markdown-tile/azure-portal-custom-markdown-tile.png)

## <a name="markdown-content-capabilities-and-limitations"></a>Markdown 内容功能和限制

可以在 Markdown 磁贴上使用纯文本、Markdown 语法和 HTML 内容的任意组合。 Azure 门户使用名为 _marked_ 的开源库将内容转换为在磁贴上显示的 HTML。 通过 _marked_ 生成的 HTML 在呈现之前先由门户进行预处理。 此步骤有助于确保自定义不会影响门户的安全性或布局。 在该预处理期间，HTML 中任何带有潜在威胁的内容都会被删除。 门户不允许以下类型的内容：

* JavaScript - `<script>` 标记以及内联的 JavaScript 评估会被删除。
* iframe - `<iframe>` 标记会被删除。
* 样式 - `<style>` 标记会被删除。 HTML 元素上的内联样式属性没有获得正式支持。 你可能会发现，某些内联样式属性可以使用，但如果它们干扰门户的布局，系统可能会随时停用它们。 Markdown 磁贴适用于基本的静态内容，该内容使用门户的默认样式。

## <a name="next-steps"></a>后续步骤

* 若要创建自定义仪表板，请参阅[如何在 Azure 门户中创建和共享仪表板](../azure-portal/azure-portal-dashboards.md)
