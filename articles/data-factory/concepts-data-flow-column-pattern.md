---
title: Azure 数据工厂映射数据流中的列模式
description: 使用 Azure 数据工厂中的列模式映射数据流创建通用数据转换模式
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 68c211608cfceedaa9d13a595be6d1e5de17f1d5
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844994"
---
# <a name="using-column-patterns-in-mapping-data-flow"></a>使用映射数据流中的列模式

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

多个映射数据流转换允许您基于模式而不是硬编码的列名称引用模板列。 此匹配称为 *列模式*。 您可以根据名称、数据类型、流、源或位置定义模式来匹配列，而不需要精确的字段名。 在以下两种情况下，列模式很有用：

* 如果传入源字段经常更改，例如更改文本文件或 NoSQL 数据库中的列的大小写。 此方案称为 [架构偏差](concepts-data-flow-schema-drift.md)。
* 如果要对一大组列执行常见操作， 例如，想要将其列名中包含 "total" 的每个列转换为 double。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4Iui1]

## <a name="column-patterns-in-derived-column-and-aggregate"></a>派生列和聚合中的列模式

若要在派生列、聚合或窗口转换中添加列模式，请单击列列表上方的 " **添加** " 或现有派生列旁边的加号图标。 选择 " **添加列模式**"。

![屏幕截图显示添加列模式的加号图标。](media/data-flow/add-column-pattern.png "列模式")

使用 " [表达式生成器](concepts-data-flow-expression-builder.md) " 输入匹配条件。 基于列的、、、和创建与列匹配的布尔表达式 `name` `type` `stream` `origin` `position` 。 此模式将影响任何偏移或定义的列，其中条件返回 true。

Match 条件下的两个表达式框指定受影响的列的新名称和值。 用于 `$$` 引用匹配字段的现有值。 左侧表达式框定义名称，右侧表达式框定义值。

![屏幕截图显示了派生列的 "设置" 选项卡。](media/data-flow/edit-column-pattern.png "列模式")

以上列模式匹配类型为 double 的每一列，并为每个匹配项创建一个派生列。 声明 `$$` 为列名称字段时，将使用相同的名称更新每个匹配的列。 每个列的值为现有值舍入为两个小数点。

若要验证匹配条件是否正确，可以在 " **检查** " 选项卡中验证已定义列的输出架构，或在 " **数据预览** " 选项卡中获取数据的快照。 

![屏幕截图显示 "输出架构" 选项卡。](media/data-flow/columnpattern3.png "列模式")

### <a name="hierarchical-pattern-matching"></a>分层模式匹配

您还可以在复杂的层次结构内生成模式匹配。 展开 `Each MoviesStruct that matches` 要在其中提示你输入数据流中的每个层次结构的部分。 然后，你可以为所选层次结构中的属性生成匹配模式。

![屏幕截图显示层次结构列模式。](media/data-flow/patterns-hierarchy.png "层次结构中的列模式")

## <a name="rule-based-mapping-in-select-and-sink"></a>Select 和 sink 中基于规则的映射

映射源中的列并选择转换时，可以添加固定映射或基于规则的映射。 根据列的、、、 `name` `type` `stream` `origin` 和进行匹配 `position` 。 可以有固定和基于规则的映射的任意组合。 默认情况下，每列大于50的所有投影都将默认为基于规则的映射，该映射在每个列上都匹配并输出输入名称。 

若要添加基于规则的映射，请单击 " **添加映射** "，然后选择 " **基于规则的映射**"。

![屏幕截图显示从添加映射中选择的基于规则的映射。](media/data-flow/rule2.png "基于规则的映射")

每个基于规则的映射都需要两个输入：要匹配的条件以及每个映射列的名称。 这两个值都是通过 [表达式生成器](concepts-data-flow-expression-builder.md)输入的。 在左侧的 "表达式" 框中，输入您的布尔匹配条件。 在右侧的 "表达式" 框中，指定匹配的列将映射到的列。

![屏幕截图显示了一个映射。](media/data-flow/rule-based-mapping.png "基于规则的映射")

使用 `$$` 语法来引用匹配列的输入名称。 使用上述图像作为示例，假设用户想要匹配名称长度少于6个字符的所有字符串列。 如果一个传入列已命名 `test` ，则表达式 `$$ + '_short'` 将重命名该列 `test_short` 。 如果这是唯一存在的映射，则将从输出的数据中删除所有不符合条件的列。

模式匹配偏移和已定义的列。 若要查看规则所映射的定义列，请单击该规则旁边的眼镜图标。 使用数据预览来验证输出。

### <a name="regex-mapping"></a>Regex 映射

如果单击向下 v 形图标，则可以指定 regex 映射条件。 Regex 映射条件与与指定的正则表达式匹配的所有列名称匹配。 这可以与基于标准规则的映射结合使用。

![屏幕截图显示了具有层次结构级别和名称匹配的正则表达式映射条件。](media/data-flow/regex-matching.png "基于规则的映射")

上面的示例匹配正则表达式模式 `(r)` 或任何包含小写字母的列名称。 与基于标准规则的映射类似，所有匹配的列都通过使用语法在右侧的条件下进行更改 `$$` 。

### <a name="rule-based-hierarchies"></a>基于规则的层次结构

如果您定义的投影具有层次结构，则可以使用基于规则的映射来映射层次结构个子列。 指定匹配条件以及要映射其个子列的复杂列。 将使用右侧指定的 "名称为" 规则输出每个匹配的 subcolumn。

![屏幕截图显示了使用的层次结构的基于规则的映射。](media/data-flow/rule-based-hierarchy.png "基于规则的映射")

上面的示例匹配复杂列的所有个子列 `a` 。 `a` 包含两个个子列 `b` 和 `c` 。 输出架构将包括两个列 `b` ，并且 `c` "名称为" 条件为 `$$` 。

## <a name="pattern-matching-expression-values"></a>模式匹配表达式的值。

* `$$` 在运行时转换为每个匹配项的名称或值。 `$$`将视为等效于 `this` 。
* `name` 表示每个传入列的名称
* `type` 表示每个传入列的数据类型
* `stream` 表示与流中的每个流或转换相关联的名称。
* `position` 是数据流中列的序号位置
* `origin` 用于生成或上次更新列的转换

## <a name="next-steps"></a>后续步骤
* 了解有关数据转换的映射数据流 [表达式语言](data-flow-expression-functions.md) 的详细信息
* 在[接收器转换](data-flow-sink.md)中使用列模式，并使用基于规则的映射[选择转换](data-flow-select.md)
