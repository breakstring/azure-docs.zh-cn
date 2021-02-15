---
title: 如何搜索、编辑和删除项目 - 自定义翻译
titleSuffix: Azure Cognitive Services
description: 自定义翻译提供了各种方法来高效地管理你的项目。 你可以创建多个项目，根据条件进行搜索，以及编辑项目。 还可以在自定义翻译中删除项目。
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 308ca25d35011c67ded7300177149cd590462952
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896437"
---
# <a name="search-edit-and-delete-projects"></a>搜索、编辑和删除项目

自定义翻译提供了多种方法来高效地管理你的项目。 你可以创建许多项目，根据条件进行搜索，以及编辑项目。 还可以在自定义翻译中删除项目。  

## <a name="search-and-filter-projects"></a>搜索和筛选项目

筛选工具可以根据不同的筛选条件搜索项目。 它可以根据项目的项目名称、状态、源和目标语言以及类别等进行筛选。

1. 单击筛选器按钮。

    ![搜索项目](media/how-to/how-to-search-project.png)

2. 可以按以下字段的任何 (或所有) 进行筛选：项目名称、源语言、目标语言、类别和项目的可用性。

3. 单击“应用”。

    ![搜索项目筛选器选项](media/how-to/how-to-search-project-filters.png)

4. 通过点击“清除”来清除筛选器以查看所有项目。

## <a name="edit-a-project"></a>编辑项目

自定义转换器使你能够编辑项目的名称和描述。 其他项目元数据（如类别、源语言和目标语言）不可用于编辑。 下面的步骤介绍如何编辑项目。

1. 单击将鼠标指针悬停在项目上时出现的铅笔图标。

    ![编辑项目](media/how-to/how-to-edit-project.png)

2. 在对话框中，您可以修改项目名称、项目说明、类别说明以及项目标签（如果没有部署模型）。 创建项目后，不能修改类别或语言对。

    ![“编辑项目”对话框](media/how-to/how-to-edit-project-dialog.png)

3. 单击 "保存" 按钮。

## <a name="delete-a-project"></a>删除项目

不再需要某个项目时可以将其删除。 使项目没有处于活动状态的模型，例如已部署、已提交培训、数据处理、部署等，否则删除操作将失败。 以下步骤介绍了如何删除项目。

1. 将鼠标指针悬停在任何项目记录上，并单击垃圾桶图标。

   ![删除项目](media/how-to/how-to-delete-project.png)

2. 确认删除。 删除某个项目将删除在该项目中创建的所有模型。 删除项目不会影响你的文档。

   ![删除确认对话框](media/how-to/how-to-delete-project-confirm.png)

## <a name="next-steps"></a>后续步骤

- [上传文档](how-to-upload-document.md)以开始生成自定义翻译模型。
