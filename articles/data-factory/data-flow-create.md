---
title: 创建映射数据流
description: 如何创建 Azure 数据工厂映射数据流
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/12/2019
ms.openlocfilehash: eaf36cc2690b3c0f8922c05432b3197b4ff30d9a
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2020
ms.locfileid: "93026048"
---
# <a name="create-azure-data-factory-data-flow"></a>创建 Azure 数据工厂数据流

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

在 ADF 中映射数据流提供了一种无需任何编码即可大规模转换数据的方法，。 通过构造一系列转换，可以在数据流设计器中设计数据转换作业。 从任意数量的源转换开始，然后是数据转换步骤。 然后，使用接收器完成数据流，以将结果放在目标提供程序中。

首先，从 Azure 门户创建新的 V2 数据工厂。 创建新工厂后，单击“创作和监视”磁贴以启动数据工厂 UI。

![屏幕截图显示 "新建数据工厂" 窗格，并为版本选择 V2。](media/data-flow/v2portal.png "数据流创建")

进入数据工厂 UI 后，你可以使用示例数据流。 示例可从 ADF 模板库中获得。 在 ADF 中，创建“来自模板的管道”并从模板库中选择“数据流”类别。

![屏幕截图显示具有所选数据流的 "数据流" 选项卡和转换数据。](media/data-flow/template.png "数据流创建")

系统将提示输入 Azure Blob 存储帐户信息。

![屏幕截图显示 "使用数据流转换数据" 窗格，你可以在其中输入用户输入。](media/data-flow/template2.png "数据流创建2")

[可在此处找到用于这些示例的数据](https://github.com/kromerm/adfdataflowdocs/tree/master/sampledata)。 下载示例数据并将文件存储在 Azure Blob 存储帐户中，以便执行示例。

## <a name="create-new-data-flow"></a>创建新数据流

使用 ADF UI 中的 "创建资源" 加号按钮创建数据流。

![屏幕截图显示从工厂资源菜单中选择的数据流。](media/data-flow/newresource.png "新资源")

## <a name="next-steps"></a>后续步骤

开始使用 [源转换](data-flow-source.md)生成数据转换。
