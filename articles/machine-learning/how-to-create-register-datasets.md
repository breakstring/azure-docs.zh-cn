---
title: 创建 Azure 机器学习数据集
titleSuffix: Azure Machine Learning
description: 了解如何创建 Azure 机器学习数据集以访问机器学习试验运行的数据。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1, data4ml
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.openlocfilehash: 39973fe8c15364dc214392985cecd8b8bc7834ed
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878199"
---
# <a name="create-azure-machine-learning-datasets"></a>创建 Azure 机器学习数据集

本文介绍如何使用 Azure 机器学习 Python SDK 创建 Azure 机器学习数据集，以访问本地或远程试验的数据。 若要了解在 Azure 机器学习总体数据访问工作流中的哪些位置使用数据集，请参阅[安全地访问数据](concept-data.md#data-workflow)一文。

通过创建数据集，可以创建对数据源位置的引用及其元数据的副本。 由于数据保留在其现有位置中，因此不会产生额外的存储成本，也不会损害数据源的完整性。 此外，还会对数据集进行延迟计算，从而有助于工作流性能速度。 可以从数据存储、公共 Url 和 [Azure 开放式数据集](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md)创建数据集。

对于低代码体验，请[使用 Azure 机器学习 Studio 创建 Azure 机器学习数据集。](how-to-connect-data-ui.md#create-datasets)

使用 Azure 机器学习数据集可以：

* 在存储中保留数据的单个副本，供数据集引用。

* 在模型训练期间无缝访问数据，而无需考虑连接字符串或数据路径。[详细了解如何使用数据集进行训练](how-to-train-with-datasets.md)。

* 与其他用户共享数据和展开协作。

## <a name="prerequisites"></a>先决条件

若要创建和使用数据集，需要做好以下准备：

* Azure 订阅。 如果没有订阅，请在开始之前创建一个免费帐户。 试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。

* 一个 [Azure 机器学习工作区](how-to-manage-workspace.md)。

* [已安装适用于 Python 的 Azure 机器学习 SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)，其中包含 azureml-datasets 包。

    * 创建一个 [Azure 机器学习计算实例](how-to-create-manage-compute-instance.md)，它是一个完全配置且托管的开发环境，其中包括集成的笔记本和已安装的 SDK。

    **或者**

    * 使用自己的 Jupyter 笔记本，按照[这些说明](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)自行安装 SDK。

> [!NOTE]
> 某些数据集类依赖于 [azureml-dataprep](/python/api/azureml-dataprep/?preserve-view=true&view=azure-ml-py) 包，此包仅兼容64位 Python。 对于 Linux 用户，只有以下分发版支持这些类：Red Hat Enterprise Linux（7、8）、Ubuntu（14.04、16.04、18.04）、Fedora（27、28）、Debian（8、9）和 CentOS (7)。 如果使用的是不受支持的发行版，请按照[此指南](/dotnet/core/install/linux)安装 .NET Core 2.1 以继续操作。 

## <a name="compute-size-guidance"></a>计算大小指南

创建数据集时，请检查计算处理能力和内存中的数据大小。 存储中的数据大小不同于数据帧中的数据大小。 例如，CSV 文件中的数据在数据帧中最多可以扩展到 10 倍，因此，1 GB 的 CSV 文件在数据帧中可能会变成 10 GB。 

如果数据已压缩，则可进一步让其扩展；以压缩 parquet 格式存储的 20 GB 相对稀疏的数据可以在内存中扩展到大约 800 GB。 由于 Parquet 文件以纵栏格式存储数据，因此，如果只需要一半的列，则只需在内存中加载大约 400 GB。

[详细了解如何优化 Azure 机器学习中的数据处理](concept-optimize-data-processing.md)。

## <a name="dataset-types"></a>数据集类型

存在两种数据集类型：FileDataset 和 TabularDataset，具体取决于用户在训练中使用它们的方式。 这两种类型均可用于涉及估算器、AutoML、hyperDrive 和管道的 Azure 机器学习训练工作流。 

### <a name="filedataset"></a>FileDataset

[FileDataset](/python/api/azureml-core/azureml.data.file_dataset.filedataset?preserve-view=true&view=azure-ml-py) 引用数据存储或公共 URL 中的单个或多个文件。 如果数据已经清理，并且可以在训练试验中使用，则可将文件作为 FileDataset 对象[下载或装载](how-to-train-with-datasets.md#mount-vs-download)到计算中。 

建议将 FileDataset 用于机器学习工作流，因为源文件可以采用任何格式，因此可以实现更广泛的机器学习方案，包括深度学习。

使用 [PYTHON SDK](#create-a-filedataset) 或 [Azure 机器学习 studio](how-to-connect-data-ui.md#create-datasets) 创建 FileDataset。
### <a name="tabulardataset"></a>TabularDataset

[TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py) 通过分析提供的文件或文件列表来以表格格式表示数据。 这样你就可以将数据具体化为 pandas 或 Spark 数据帧，以便进行熟悉的数据准备并训练库，不需离开笔记本。 可以从 .csv、.tsv、.parquet、.jsonl 文件以及从 [SQL 查询结果](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?preserve-view=true&view=azure-ml-py#&preserve-view=truefrom-sql-query-query--validate-true--set-column-types-none--query-timeout-30-)创建 `TabularDataset` 对象。

使用 TabularDataset 时，可以从数据中的列或者从数据所存储到的任何路径模式指定一个时间戳，以启用时序特征。 此规范允许按时间轻松有效地进行筛选。 有关示例，请参阅[使用 NOAA 天气数据的表格时序相关 API 演示](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb)。

使用 [Python SDK](#create-a-tabulardataset) 或 [Azure 机器学习工作室](how-to-connect-data-ui.md#create-datasets)创建 TabularDataset。

>[!NOTE]
> 通过 Azure 机器学习工作室生成的 AutoML 工作流目前仅支持 TabularDataset。 

## <a name="access-datasets-in-a-virtual-network"></a>访问虚拟网络中的数据集

如果工作区位于虚拟网络中，则必须将数据集配置为跳过验证。 有关如何在虚拟网络中使用数据存储和数据集的详细信息，请参阅[保护工作区和相关资源](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets)。

<a name="datasets-sdk"></a>

## <a name="create-datasets"></a>创建数据集

要使数据可供 Azure 机器学习访问，必须从 [Azure 数据存储](how-to-access-data.md)或公共 Web URL 中的路径创建数据集。 

若要使用 Python SDK 从 [Azure 数据存储](how-to-access-data.md)创建数据集，请执行以下步骤：

1. 验证是否对已注册的 Azure 数据存储拥有 `contributor` 或 `owner` 访问权限。

2. 通过引用数据存储中的路径创建数据集。 可以从多个数据存储中的多个路径创建数据集。 可以从其创建数据集的文件数量或数据大小没有硬性限制。 

> [!NOTE]
> 对于每个数据路径，会将几个请求发送到存储服务，以检查它是否指向文件或文件夹。 此开销可能会导致性能下降或故障。 数据集引用内含 1000 个文件的文件夹的行为被视为引用一个数据路径。 为了获得最佳性能，建议创建在数据存储中引用不到 100 个路径的数据集。

### <a name="create-a-filedataset"></a>创建 FileDataset

使用 `FileDatasetFactory` 类中的 [`from_files()`](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?preserve-view=true&view=azure-ml-py#&preserve-view=truefrom-files-path--validate-true-) 方法可以加载任意格式的文件并创建未注册的 FileDataset。 

如果存储位于虚拟网络或防火墙后面，请在 `from_files()` 方法中设置参数 `validate=False`。 这会绕过初始验证步骤，确保可以从这些安全文件创建数据集。 详细了解如何[使用虚拟网络中的数据存储和数据集](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets)。

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```
若要在工作区的不同试验中重用和共享数据集，请[注册数据集](#register-datasets)。 

> [!TIP] 
> 在一个方法中使用公共预览版方法 [upload_directory()](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?preserve-view=true&view=azure-ml-py#upload-directory-src-dir--target--pattern-none--overwrite-false--show-progress-true-) 从本地目录上传文件，并创建一个 FileDataset。 此方法是一个[试验性](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py#stable-vs-experimental)预览功能，可能会随时更改。 
> 
>  此方法会将数据上传到基础存储，因此会产生存储费用。 

### <a name="create-a-tabulardataset"></a>创建 TabularDataset

使用 `TabularDatasetFactory` 类中的 [`from_delimited_files()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory) 方法可以读取 .csv 或 .tsv 格式的文件，以及创建未注册的 TabularDataset。 如果从多个文件进行读取，结果将聚合为一种表格表示形式。 

如果存储位于虚拟网络或防火墙后面，请在 `from_delimited_files()` 方法中设置参数 `validate=False`。 这会绕过初始验证步骤，确保可以从这些安全文件创建数据集。 详细了解如何使用[虚拟网络中的数据存储和数据集](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets)。

以下代码按名称获取现有工作区和所需的数据存储。 然后将数据存储和文件位置传递给 `path` 参数以创建新的 TabularDataset `weather_ds`。

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
    
# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)

# create a TabularDataset from 3 file paths in datastore
datastore_paths = [(datastore, 'weather/2018/11.csv'),
                   (datastore, 'weather/2018/12.csv'),
                   (datastore, 'weather/2019/*.csv')]

weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```
### <a name="set-data-schema"></a>设置数据架构

默认情况下，在创建 TabularDataset 时，将自动推断列数据类型。 如果推断出的类型与预期不符，则可以通过使用以下代码指定列类型来更新数据集架构。 参数 `infer_column_type` 仅适用于从分隔文件创建的数据集。 [详细了解支持的数据类型](/python/api/azureml-core/azureml.data.dataset_factory.datatype?preserve-view=true&view=azure-ml-py)。


```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|（索引）|PassengerId|Survived|Pclass|名称|Sex|Age|SibSp|Parch|Ticket|Fare|Cabin|Embarked
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|False|3|Braund, Mr. Owen Harris|男|22.0|1|0|A/5 21171|7.2500||S
1|2|True|1|Cumings, Mrs. John Bradley (Florence Briggs Th...|女|38.0|1|0|PC 17599|71.2833|C85|C
2|3|True|3|Heikkinen, Miss. Laina|女|26.0|0|0|STON/O2. 3101282|7.9250||S

若要在工作区的不同试验中重用和共享数据集，请[注册数据集](#register-datasets)。

## <a name="explore-data"></a>浏览数据

创建并 [注册](#register-datasets) 数据集后，可以将其加载到笔记本中，以便在模型定型之前进行数据浏览。 如果不需要进行任何数据探索，请参阅如何在训练脚本中使用数据集，以便在对 [数据集进行训练时](how-to-train-with-datasets.md)提交 ML 试验。

对于 FileDatasets，可以 **装载** 或 **下载** 数据集，并应用通常用于数据浏览的 python 库。 [了解有关装载与下载的详细信息](how-to-train-with-datasets.md#mount-vs-download)。

```python
# download the dataset 
dataset.download(target_path='.', overwrite=False) 

# mount dataset to the temp directory at `mounted_path`

import tempfile
mounted_path = tempfile.mkdtemp()
mount_context = dataset.mount(mounted_path)

mount_context.start()
```

对于 TabularDatasets，请使用 [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) 方法查看数据帧中的数据。 

```python
# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|（索引）|PassengerId|Survived|Pclass|名称|Sex|Age|SibSp|Parch|Ticket|Fare|Cabin|Embarked
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|False|3|Braund, Mr. Owen Harris|男|22.0|1|0|A/5 21171|7.2500||S
1|2|True|1|Cumings, Mrs. John Bradley (Florence Briggs Th...|女|38.0|1|0|PC 17599|71.2833|C85|C
2|3|True|3|Heikkinen, Miss. Laina|女|26.0|0|0|STON/O2. 3101282|7.9250||S

## <a name="create-a-dataset-from-pandas-dataframe"></a>从 pandas 数据帧创建数据集

若要从内存中 pandas 数据帧创建 TabularDataset，请将数据写入本地文件（例如 csv），然后从该文件创建数据集。 下面的代码演示了此工作流。

```python
# azureml-core of version 1.0.72 or higher is required
# azureml-dataprep[pandas] of version 1.1.34 or higher is required

from azureml.core import Workspace, Dataset
local_path = 'data/prepared.csv'
dataframe.to_csv(local_path)

# upload the local file to a datastore on the cloud

subscription_id = 'xxxxxxxxxxxxxxxxxxxxx'
resource_group = 'xxxxxx'
workspace_name = 'xxxxxxxxxxxxxxxx'

workspace = Workspace(subscription_id, resource_group, workspace_name)

# get the datastore to upload prepared data
datastore = workspace.get_default_datastore()

# upload the local file from src_dir to the target_path in datastore
datastore.upload(src_dir='data', target_path='data')

# create a dataset referencing the cloud location
dataset = Dataset.Tabular.from_delimited_files(path = [(datastore, ('data/prepared.csv'))])
```

> [!TIP]
> 在一个方法中使用公共预览版方法 [`register_spark_dataframe()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?preserve-view=true&view=azure-ml-py#methods) 和 [`register_pandas_dataframe()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?preserve-view=true&view=azure-ml-py#methods) 从内存中 spark 或 pandas 数据帧创建并注册一个 TabularDataset。 这些注册方法属于[试验性](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py#stable-vs-experimental)预览功能，可能会随时更改。 
> 
>  这些方法会将数据上传到基础存储，因此会产生存储费用。 

## <a name="register-datasets"></a>注册数据集

若要完成创建过程，请将数据集注册到工作区。 使用 [`register()`](/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?preserve-view=true&view=azure-ml-py#&preserve-view=trueregister-workspace--name--description-none--tags-none--create-new-version-false-) 方法将数据集注册到工作区，以便与其他人共享，并在工作区中的实验中重复使用这些数据集：

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

## <a name="create-datasets-using-azure-resource-manager"></a>使用 Azure 资源管理器创建数据集

[https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-dataset-create-*](https://github.com/Azure/azure-quickstart-templates/tree/master/) 上有许多模板可用于创建数据集。

若要了解如何使用这些模板，请参阅[使用 Azure 资源管理器模板创建 Azure 机器学习的工作区](how-to-create-workspace-template.md)。


## <a name="create-datasets-with-azure-open-datasets"></a>使用 Azure 开放数据集创建数据集

[Azure 开放数据集](https://azure.microsoft.com/services/open-datasets/)是精选公共数据集，可用于将方案专属特征添加到机器学习解决方案，以提高模型的准确度。 数据集包括不受任何限制的天气、人口普查、节假日、公共安全和位置数据，有助于定型机器学习模型和扩充预测解决方案。 打开的数据集位于 Microsoft Azure 云中，同时包含在 SDK 和工作室中。

了解如何 [从 Azure 开放数据集创建 Azure 机器学习数据集](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md)。 

## <a name="train-with-datasets"></a>使用数据集进行训练

在机器学习试验中使用数据集来训练 ML 模型。 [详细了解如何使用数据集进行训练](how-to-train-with-datasets.md)

## <a name="version-datasets"></a>对数据集进行版本控制

可以通过创建新版本，以相同的名称注册新数据集。 数据集版本是为数据状态设置书签的一种方法，以便可以应用数据集的特定版本进行试验或者在将来重现该数据集。 详细了解[数据集版本](how-to-version-track-datasets.md)。
```Python
# create a TabularDataset from Titanic training data
web_paths = ['https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
             'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv']
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```

## <a name="next-steps"></a>后续步骤

* 了解[如何使用数据集进行训练](how-to-train-with-datasets.md)。
* 通过自动化机器学习[使用 TabularDataset 进行训练](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)。
* 有关更多数据集训练示例，请参阅[示例笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/)。
