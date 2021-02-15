---
title: 创建和浏览带标签的数据集
titleSuffix: Azure Machine Learning
description: 了解如何从 Azure 机器学习标记项目中导出数据标签，并将其用于机器学习任务。
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, data4ml
ms.date: 05/14/2020
ms.openlocfilehash: a4f15a1a0911e5a33da8b5f9445709cb42e7e10e
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2021
ms.locfileid: "99981504"
---
# <a name="create-and-explore-azure-machine-learning-dataset-with-labels"></a>创建和浏览带标签的 Azure 机器学习数据集

在本文中，你将学习如何从 Azure 机器学习数据标记项目中导出数据标签，并将其加载为常用格式，例如，加载为 Pandas 数据帧以用于浏览数据，或者加载为 Torchvision 数据集以用于转换图像。 

## <a name="what-are-datasets-with-labels"></a>什么是带标签的数据集 

我们将带标签的 Azure 机器学习数据集称为带标签的数据集。 这些特定数据集类型的带标签数据集只能创建为 Azure 机器学习数据标记项目的输出。 可以使用[这些步骤](how-to-create-labeling-projects.md)创建数据标记项目。 机器学习支持用于图像分类的数据标记项目（无论是多标签的还是多类的），以及带边界框的对象标识。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://aka.ms/AMLFree)。
* [适用于 Python 的 Azure 机器学习 SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)，或 [Azure 机器学习工作室](https://ml.azure.com/)的访问权限。
    * 安装 [azure-contrib-dataset](/python/api/azureml-contrib-dataset/?preserve-view=true&view=azure-ml-py) 包
* 机器学习工作区。 请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。
* 对 Azure 机器学习数据标记项目的访问权限。 如果没有标记项目，请使用[这些步骤](how-to-create-labeling-projects.md)创建一个。

## <a name="export-data-labels"></a>导出数据标签 

完成数据标记项目后，可以从标记项目中导出标签数据。 这样，便可以捕获对数据及其标签的引用，并将其导出为 [COCO 格式](http://cocodataset.org/#format-data)或 Azure 机器学习数据集。 使用标记项目的“项目详细信息”页上的“导出”按钮。 

### <a name="coco"></a>COCO 

 COCO 文件是在 Azure 机器学习工作区的默认 Blob 存储中创建的，该存储位于 *export/coco* 内的某个文件夹中。 
 
>[!NOTE]
>在对象检测项目中，COCO 文件中导出的 "bbox"： [x，y，width，height] "值已规范化。 它们将扩展为1。 示例：一个边界框位于 (10，10) 位置，0.015625 像素的宽度为30像素，60像素高，将批注为 (。 0.02083、0.046875、0.125) 。 由于 coordintes 是规范化的，因此，所有图像的 "width" 和 "height" 将显示为 "0.0"。 可以使用 Python 库（如 OpenCV 或 Pillow (PIL) ）获取实际的宽度和高度。

### <a name="azure-machine-learning-dataset"></a>Azure 机器学习数据集

可以在 Azure 机器学习工作室的“数据集”部分中访问导出的 Azure 机器学习数据集。 数据集“详细信息”页还提供了演示如何从 Python 访问标签的示例代码。

![导出的数据集](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="explore-labeled-datasets"></a>浏览带标签的数据集

将带标签的数据集加载到 pandas 数据帧或 Torchvision 数据集中以利用常见的开源库来浏览数据，以及利用 PyTorch 提供的库进行图像转换和训练。

### <a name="pandas-dataframe"></a>Pandas 数据帧

可以使用 `azureml-contrib-dataset` 类的 [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py#&preserve-view=trueto-pandas-dataframe-on-error--null---out-of-range-datetime--null--) 方法将带标签的数据集加载到 pandas 数据帧。 可以使用以下 shell 命令安装此类： 

```shell
pip install azureml-contrib-dataset
```

>[!NOTE]
>azureml.contrib 命名空间会频繁更改，因为我们正在改进服务。 因此，此命名空间中的任何内容都应被视为预览版，Microsoft 并不完全支持。

转换为 pandas 数据帧时，Azure 机器学习针对文件流提供以下文件处理选项。
* 下载：将数据文件下载到本地路径。
* 装载：将数据文件装载到装入点。 装载仅适用于基于 Linux 的计算，包括 Azure 机器学习笔记本 VM 和 Azure 机器学习计算。

在以下代码中，`animal_labels` 数据集是之前保存到工作区的标签项目的输出。

```Python
import azureml.core
import azureml.contrib.dataset
from azureml.core import Dataset, Workspace
from azureml.contrib.dataset import FileHandlingOption

# get animal_labels dataset from the workspace
animal_labels = Dataset.get_by_name(workspace, 'animal_labels')
animal_pd = animal_labels.to_pandas_dataframe(file_handling_option=FileHandlingOption.DOWNLOAD, target_path='./download/', overwrite_download=True)

import matplotlib.pyplot as plt
import matplotlib.image as mpimg

#read images from downloaded path
img = mpimg.imread(animal_pd.loc[0,'image_url'])
imgplot = plt.imshow(img)
```

### <a name="torchvision-datasets"></a>Torchvision 数据集

还可以使用 `azureml-contrib-dataset` 类中的 [to_torchvision()](/python/api/azureml-contrib-dataset/azureml.contrib.dataset.tabulardataset?preserve-view=true&view=azure-ml-py#&preserve-view=trueto-torchvision--) 方法将带标签的数据集加载到 Torchvision 数据集中。 若要使用此方法，需要安装 [PyTorch](https://pytorch.org/)。 

在以下代码中，`animal_labels` 数据集是之前保存到工作区的标签项目的输出。

```python
import azureml.core
import azureml.contrib.dataset
from azureml.core import Dataset, Workspace
from azureml.contrib.dataset import FileHandlingOption

from torchvision.transforms import functional as F

# get animal_labels dataset from the workspace
animal_labels = Dataset.get_by_name(workspace, 'animal_labels')

# load animal_labels dataset into torchvision dataset
pytorch_dataset = animal_labels.to_torchvision()
img = pytorch_dataset[0][0]
print(type(img))

# use methods from torchvision to transform the img into grayscale
pil_image = F.to_pil_image(img)
gray_image = F.to_grayscale(pil_image, num_output_channels=3)

imgplot = plt.imshow(gray_image)
```

## <a name="next-steps"></a>后续步骤

* 有关完整的训练示例，请参阅[带标签的数据集笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb)。
