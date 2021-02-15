---
title: '在 Azure SQL Edge 中使用 ONNX 实现机器学习和 AI '
description: Azure SQL Edge 中的机器学习支持开放式神经网络 Exchange (ONNX) 格式的模型。 ONNX 是一种开放的格式，可用于在不同的机器学习框架和工具之间交换模型。
keywords: 部署 SQL Edge
services: sql-edge
ms.service: sql-edge
ms.subservice: machine-learning
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.date: 05/19/2020
ms.openlocfilehash: 5dc3d44ac4396897fd43831d51ee628bb06048cb
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2020
ms.locfileid: "93392055"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-edge"></a>在 SQL Edge 中通过 ONNX 进行机器学习和 AI

Azure SQL Edge 中的机器学习支持 [开放式神经网络 Exchange (ONNX) ](https://onnx.ai/) 格式的模型。 ONNX 是一种开放的格式，可用于在不同的[机器学习框架和工具](https://onnx.ai/supported-tools)之间交换模型。

## <a name="overview"></a>概述

若要在 Azure SQL Edge 中推理机器学习模型，首先需要获取模型。 这可以是预训练模型，也可以是使用所选框架训练的自定义模型。 Azure SQL Edge 支持 ONNX 格式，你需要将模型转换为此格式。 这应该不会影响模型准确度，在获取 ONNX 模型后，就可以在 Azure SQL Edge 中部署模型，然后利用[使用 PREDICT T-SQL 函数的本机评分](/sql/advanced-analytics/sql-native-scoring/)。

## <a name="get-onnx-models"></a>获取 ONNX 模型

获取 ONNX 格式模型的途径：

- 模型生成服务：[Azure 机器学习中的自动化机器学习](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)和 [Azure 自定义视觉服务](../cognitive-services/custom-vision-service/getting-started-build-a-classifier.md)等服务支持直接导出 ONNX 格式的已训练模型。

- [转换和/或导出现有模型](https://github.com/onnx/tutorials#converting-to-onnx-format)：多个训练框架（例如 [PyTorch](https://pytorch.org/docs/stable/onnx.html)、Chainer 和 Caffe2）支持对 ONNX 的本机导出功能，这样你就可以将已训练模型保存为特定版本的 ONNX 格式。 对于不支持本机导出的框架，有独立的 ONNX 转换器可安装包，可便于你将已训练的模型从不同的机器学习框架转换为 ONNX 格式。

     支持的框架
   * [PyTorch](http://pytorch.org/docs/master/onnx.html)
   * [Tensorflow](https://github.com/onnx/tensorflow-onnx)
   * [Keras](https://github.com/onnx/keras-onnx)
   * [Scikit-learn](https://github.com/onnx/sklearn-onnx)
   * [CoreML](https://github.com/onnx/onnxmltools)
    
    有关支持的框架和示例的完整列表，请参阅[转换为 ONNX 格式](https://github.com/onnx/tutorials#converting-to-onnx-format)。

## <a name="limitations"></a>限制

目前，并非所有的 ONNX 模型都受到 Azure SQL Edge 的支持。 此支持仅限于具有以下数值数据类型的模型：

- [int 和 bigint](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)
- [real 和 float](/sql/t-sql/data-types/float-and-real-transact-sql)。
  
通过使用 [CAST 和 CONVERT](/sql/t-sql/functions/cast-and-convert-transact-sql)，可以将其他数值类型转换为支持的类型。

应对模型输入进行结构化处理，以便模型的每个输入都对应于表中的一列。 例如，如果使用 pandas 数据帧来训练模型，那么每个输入都应该是模型中的单独一列。

## <a name="next-steps"></a>后续步骤

- [通过 Azure 门户部署 SQL Edge 模型](deploy-portal.md)
- [在 Azure SQL Edge 上部署 ONNX 模型 ](deploy-onnx.md)