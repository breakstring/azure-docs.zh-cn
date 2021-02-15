---
title: 管理版本 - LUIS
titleSuffix: Azure Cognitive Services
description: 版本允许你构建和发布不同的模型。 较好的做法是在对模型进行更改之前将当前的活动模型复制到一个不同的应用版本。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.openlocfilehash: b93e9fd4047ee3731ef6fe4652bb92ce5145987d
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018814"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>使用版本进行编辑和测试，而不会影响暂存应用或生产应用

版本允许你构建和发布不同的模型。 较好的做法是在对模型进行更改之前将当前的活动模型复制到一个不同的应用[版本](./luis-concept-app-iteration.md)。

活动版本是你要在 LUIS 门户“构建”部分中编辑的版本，其中带有意图、实体、特征和模式。 使用创作 API 时，无需设置活动版本，因为特定于版本的 REST API 调用包含了路由中的版本。

若要处理版本，请在“我的应用”页面上通过选择你的应用名称来将其打开，在顶栏中选择“管理”，然后在左侧导航栏中选择“版本”。

版本列表显示哪些版本已发布，它们发布在何处，以及哪个版本当前处于活动状态。

## <a name="clone-a-version"></a>克隆版本

1. 选择要克隆的版本，然后从工具栏中选择“克隆”。

2. 在“克隆版本”对话框中，键入新版本的名称，如“0.2”。

   ![“克隆版本”对话框](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)

     > [!NOTE]
     > 版本 ID 可以仅包含字符、数字或“.”，长度不得超过 10 个字符。

   此时，指定名称的新版本已创建，且设置为活动版本。

## <a name="set-active-version"></a>设置活动版本

从列表中选择一个版本，然后从工具栏中选择“激活”。

## <a name="import-version"></a>导入版本

可以导入应用程序的 `.json` 或 `.lu` 版本。

1. 从工具栏中选择“导入”  ，然后选择格式。

2. 在“导入新版本”  弹出窗口中，输入新的由十个字符构成的版本名称。 仅当应用中已存在文件中的版本时，才需要设置版本 ID。

    ![在“管理”部分的“版本”页，导入新版本](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    导入后，新版本就会成为活动版本。

### <a name="import-errors"></a>导入错误

* Tokenizer 错误：如果在导入时收到 **tokenizer 错误**，则表示你尝试导入的版本使用的 [tokenizer](luis-language-support.md#custom-tokenizer-versions) 与应用当前使用的不同。 若要解决此问题，请参阅[在 tokenizer 版本之间迁移](luis-language-support.md#migrating-between-tokenizer-versions)。

<a name = "export-version"></a>

## <a name="other-actions"></a>其他操作

* 若要 **删除** 某个版本，请从列表中选择一个版本，然后从工具栏中选择“删除”。  选择“确定”  。
* 若要 **重命名** 某个版本，请从列表中选择一个版本，然后从工具栏中选择“重命名”。  输入新名称并选择“完成”  。
* 若要 **导出** 某个版本，请从列表中选择一个版本，然后从工具栏中选择“导出应用”。  选择 JSON 或 LU 导出以备份或保存在源代码管理中，选择“为容器导出”以[在 LUIS 容器中使用此应用](luis-container-howto.md)。