---
title: 集成行业标准模型
titleSuffix: Azure Digital Twins
description: 了解如何通过使用特殊的 DTDL 本体论或转换现有的本体论将行业标准模型集成到 DTDL for Azure 数字孪生
author: baanders
ms.author: baanders
ms.date: 11/04/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 3376f5d5e207a33ad39cd7506998e2ee90e084ad
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98051541"
---
# <a name="integrate-industry-standard-models-with-dtdl-for-azure-digital-twins"></a>将行业标准模型与 Azure 数字孪生的 DTDL 集成

在设计 Azure 数字孪生模型时，使用基于行业标准或使用标准 ontology 表示形式（如 RDF 或 OWL）的模型可提供丰富的起点。 使用行业模型还有助于标准化和信息共享。

要与 Azure 数字孪生一起使用，必须使用基于 JSON LD 的 [**数字孪生定义语言 (DTDL)**](concepts-models.md)来表示模型。 因此，本文介绍了如何在 DTDL 中表示行业标准模型，将现有行业概念与 DTDL 语义相集成，使 Azure 数字孪生可以使用它们。 然后，DTDL 模型充当 Azure 数字孪生中模型的真实来源。

可以通过三种途径将行业标准模型与 DTDL 集成：
* **采用**：你可以使用基于广泛采用的行业标准构建的开源 DTDL ontology 启动解决方案。 
* **转换**：如果已有现有模型，则需要将其转换为 DTDL。
* **作者**：始终可以从头开始开发自己的自定义 DTDL 模型，如 [*操作方法：管理自定义模型*](how-to-manage-model.md)中所述。

## <a name="adopt-an-open-source-dtdl-ontology"></a>采用开源 DTDL ontology

从空白页开始，使用开源 DTDL ontology 通常更容易。 

例如，智能建筑物解决方案可以利用 [**基于开源 DTDL 的 RealEstateCore ontology**](https://github.com/Azure/opendigitaltwins-building)，这为智能建筑建模提供了通用基础，同时利用行业标准来防止 reinvention。 

这些开源 DTDL 本体论还提供有关如何使用和正确扩展模型的最佳实践。 

## <a name="convert-existing-models-to-dtdl"></a>将现有模型转换为 DTDL

大多数行业模型 (也称为 **本体论**) 基于语义 web 标准，例如 [OWL](https://www.w3.org/OWL/)、 [RDF](https://www.w3.org/2001/sw/wiki/RDF)和 [RDFS](https://www.w3.org/2001/sw/wiki/RDFS)。 

若要将模型用于 Azure 数字孪生，它必须采用 DTDL 格式。 本部分以 **转换模式** 的形式介绍了如何将基于 RDF 的模型转换为 DTDL，以便可以将它们用于 Azure 数字孪生。 

它还包含 [用于 RDF 转换器的 **示例转换器代码**](#sample-converter-application)，它已针对程序 [块](https://brickschema.org/ontology/)架构进行了验证，可以为建筑行业中的其他架构进行扩展。

### <a name="conversion-pattern"></a>转换模式

可以使用多个第三方库，在将基于 RDF 的模型转换为 DTDL 时可以使用它们。 其中一些库允许您将模型文件加载到图形中。 您可以循环遍历图形查找特定的 RDFS 和 OWL 构造，并将其转换为 DTDL。   

下表举例说明了如何将 RDFS 和 OWL 构造映射到 DTDL。 

| RDFS/OWL 概念 | RDFS/OWL 构造 | DTDL 概念 | DTDL 构造 |
| --- | --- | --- | --- |
| 类 | `owl:Class`<br>IRI 后缀<br>``rdfs:label``<br>``rdfs:comment`` | 接口 | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment` 
| 对  进行子类化 | `owl:Class`<br>IRI 后缀<br>`rdfs:label`<br>`rdfs:comment`<br>`rdfs:subClassOf` | 接口 | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment`<br>`extends` 
| 数据类型属性 | `owl:DatatypeProperty`<br>`rdfs:label` 或 `INode`<br>`rdfs:label`<br>`rdfs:range` | 接口属性 | `@type:Property`<br>`name`<br>`displayName`<br>`schema` 
| 对象属性 | `owl:ObjectProperty`<br>`rdfs:label` 或 `INode`<br>`rdfs:range`<br>`rdfs:comment`<br>`rdfs:label` | Relationship | `type:Relationship`<br>`name`<br>`target`如果没有) ，则 (或省略 `rdfs:range`<br>`comment`<br>`displayName`<br>

下面的 c # 代码段演示如何使用 [**dotNetRDF**](https://www.dotnetrdf.org/) 库将 RDF 模型文件加载到图形并转换为 DTDL。 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/convertRDF.cs":::

### <a name="sample-converter-application"></a>示例转换器应用程序 

有一个可用的示例应用程序，它将基于 RDF 的模型文件转换为 [DTDL (版本 2) ](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) ，以供 Azure 数字孪生服务使用。 它已针对程序 [块](https://brickschema.org/ontology/) 架构进行了验证，可针对建筑行业 (中的其他架构进行扩展，如 [构建拓扑 Ontology (机器人) ](https://w3c-lbd-cg.github.io/bot/)、 [语义传感器网络](https://www.w3.org/TR/vocab-ssn/)或 [buildingSmart 行业基础类 (IFC) ](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/)) 。

该示例是一个名为 **RdfToDtdlConverter** 的 .net Core 命令行应用程序。

可在此处获取示例： [**RdfToDtdlConverter**](/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/)。 

若要将代码下载到你的计算机，请点击示例登录页上标题下面的 " *下载 ZIP* " 按钮。 这会下载名称 *RdfToDtdlConverter_sample_application_to_convert_RDF_to_DTDL.zip* 下的 *ZIP* 文件，然后你可以对其进行解压缩和浏览。

您可以使用此示例来查看上下文中的转换模式，还可以将其用作自己的应用程序的构建基块，根据自己的特定需要执行模型转换。

## <a name="validate-and-upload-dtdl-models"></a>验证并上传 DTDL 模型

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

转换并验证某一模型后，可以将 **其上传到 Azure 数字孪生实例**。 有关此过程的详细信息，请参阅 *操作方法：管理自定义模型* 的 "[*上载模型*](how-to-manage-model.md#upload-models)" 部分。

## <a name="next-steps"></a>后续步骤 

详细了解如何设计和管理数字克隆模型：
 
* [*概念：自定义模型*](concepts-models.md)
* [*操作说明：管理自定义模型*](how-to-manage-model.md)
* [*操作说明：分析和验证模型*](how-to-parse-models.md)