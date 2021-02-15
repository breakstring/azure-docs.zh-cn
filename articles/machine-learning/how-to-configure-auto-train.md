---
title: 创建自动化机器学习试验
titleSuffix: Azure Machine Learning
description: 了解如何为自动化机器学习试验定义数据源、计算和配置设置。
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 09/29/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python,contperf-fy21q1, automl
ms.openlocfilehash: 8ac69e6961af4991b250320b7af7cf5a345d3efb
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526460"
---
# <a name="configure-automated-ml-experiments-in-python"></a>使用 Python 配置自动化 ML 试验


本指南介绍如何通过 [Azure 机器学习 SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) 定义各种自动机器学习试验的配置设置。 自动化机器学习将自动选择算法和超参数，并生成随时可用于部署的模型。 可以使用多个选项来配置自动化机器学习试验。

若要查看自动化机器学习试验的示例，请参阅[教程：使用自动化机器学习训练分类模型](tutorial-auto-train-models.md)或[使用云中的自动化机器学习训练模型](how-to-auto-train-remote.md)。

自动化机器学习提供的配置选项：

* 选择试验类型：分类、回归或时序预测
* 数据源、格式和提取数据
* 选择计算目标：本地或远程
* 自动化机器学习试验设置
* 运行自动化机器学习试验
* 探索模型指标
* 注册和部署模型

如果你更喜欢无代码体验，还可以[在 Azure 机器学习工作室中创建自动化学习试验](how-to-use-automated-ml-for-ml-models.md)。

## <a name="prerequisites"></a>先决条件

在本文中，你需要： 
* Azure 机器学习工作区。 若要创建工作区，请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。

* 已安装 Azure 机器学习 Python SDK。
    若要安装该 SDK，你可以： 
    * 创建一个计算实例，该实例将自动安装 SDK 并针对 ML 工作流进行预先配置。 有关详细信息，请参阅[创建和管理 Azure 机器学习计算实例](how-to-create-manage-compute-instance.md)。 

    * [自行安装 `automl` 包](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md#setup-using-a-local-conda-environment)，其中包括 SDK [默认安装](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py#default-install)。

## <a name="select-your-experiment-type"></a>选择试验类型

在开始试验之前，应确定要解决的机器学习问题类型。 自动化机器学习支持 `classification`、`regression` 和 `forecasting` 任务类型。 详细了解[任务类型](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast)。

下面的代码使用 `AutoMLConfig` 构造函数中的 `task` 参数将试验类型指定为 `classification`。

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>数据源和格式

自动化机器学习支持驻留在本地桌面上或云中（例如 Azure Blob 存储）的数据。 数据可以读入 Pandas 数据帧或 Azure 机器学习 TabularDataset 中 。 [了解有关数据集的详细信息](how-to-create-register-datasets.md)。

机器学习中对数据定型的要求：
- 数据必须为表格格式。
- 要预测的值（目标列）必须位于数据中。

**对于远程试验**，必须能够从远程计算访问训练数据。 AutoML 仅在处理远程计算时才接受 [Azure 机器学习 TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py)。 

Azure 机器学习数据集公开的功能可以：

* 轻松地将数据从静态文件或 URL 源传输到工作区。
* 在云计算资源上运行时，使数据可用于训练脚本。 有关使用 `Dataset` 类将数据装载到远程计算目标的示例，请参阅[如何使用数据集进行训练](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets)。

下面的代码从一个 Web URL 创建 TabularDataset。 有关从其他源（例如本地文件和数据存储）创建数据集的代码示例，请参阅[创建 TabularDataset](how-to-create-register-datasets.md#create-a-tabulardataset)。

```python
from azureml.core.dataset import Dataset
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"
dataset = Dataset.Tabular.from_delimited_files(data)
  ```
**对于本地计算试验**，我们建议使用 pandas 数据帧以提高处理速度。

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="training-validation-and-test-data"></a>训练、验证和测试数据

您可以直接在构造函数中指定单独的 **定型数据和验证数据集** `AutoMLConfig` 。 详细了解[如何配置数据拆分和交叉验证](how-to-configure-cross-validation-data-splits.md)（针对 AutoML 试验）。 

如果未显式指定 `validation_data` 或 `n_cross_validation` 参数，则自动 ML 会应用默认技术来确定验证的执行方式。 此决定依赖于分配给 `training_data` 参数的数据集中的行数。 

|训练数据大小| 验证技术 |
|---|-----|
|**大于 20,000 行**| 将应用训练/验证数据拆分。 默认行为是将初始训练数据集的 10% 用作验证集。 然后，该验证集将用于指标计算。
|**小于 20,000 行**| 将应用交叉验证方法。 默认折数取决于行数。 <br> **如果数据集小于 1,000 行**，则使用 10 折。 <br> **如果行数在 1,000 到 20,000 之间**，则使用 3 折。

此时，你需要提供自己的 **测试数据** 来进行模型评估。 如果需要通过代码示例来演示如何引入你自己的测试数据进行模型评估，请参阅 [此 Jupyter 笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)的 **Test** 节。

## <a name="compute-to-run-experiment"></a>用于运行试验的计算环境

接下来，确定要在何处训练模型。 自动化机器学习训练试验可根据以下计算选项运行。 了解[本地和远程计算选项的优缺点](concept-automated-ml.md#local-remote)。 

* **本地** 台式机或便携式计算机等本地计算机 – 如果数据集较小，并且你仍然处于探索阶段，则通常使用此选项。 有关本地计算示例，请参阅[此笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/local-run-classification-credit-card-fraud/auto-ml-classification-credit-card-fraud-local.ipynb)。 
 
* 云中的 **远程** 计算机 – [Azure 机器学习托管计算](concept-compute-target.md#amlcompute)是一个托管服务，可用于在 Azure 虚拟机的群集上训练机器学习模型。 

    有关使用 Azure 机器学习托管计算的远程示例，请参阅[此笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)。 

* Azure 订阅中的 **Azure Databricks 群集**。 有关更多详细信息，可参阅[为自动化 ML 设置 Azure Databricks 群集](how-to-configure-databricks-automl-environment.md)。 有关包含 Azure Databricks 的示例 Notebook，请参阅此 [GitHub 站点](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl)。

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>配置试验设置

可以使用多个选项来配置自动化机器学习试验。 通过实例化 `AutoMLConfig` 对象来设置这些参数。 有关参数的完整列表，请参阅 [AutoMLConfig 类](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)。

示例包括：

1. 使用 AUC 作为主要指标加权的分类实验，其中实验超时分钟数设置为 30 分钟，且包含 2 折交叉验证。

   ```python
       automl_classifier=AutoMLConfig(task='classification',
                                      primary_metric='AUC_weighted',
                                      experiment_timeout_minutes=30,
                                      blocked_models=['XGBoostClassifier'],
                                      training_data=train_data,
                                      label_column_name=label,
                                      n_cross_validations=2)
   ```
1. 下面是设置为 60 分钟后结束的回归试验示例，其中包含 5 折交叉验证。

   ```python
      automl_regressor = AutoMLConfig(task='regression',
                                      experiment_timeout_minutes=60,
                                      allowed_models=['KNN'],
                                      primary_metric='r2_score',
                                      training_data=train_data,
                                      label_column_name=label,
                                      n_cross_validations=5)
   ```


1. 预测任务需要额外的设置，请参阅[自动训练时序预测模型](how-to-auto-train-forecast.md)一文来了解更多详细信息。 

    ```python
    time_series_settings = {
        'time_column_name': time_column_name,
        'time_series_id_column_names': time_series_id_column_names,
        'forecast_horizon': n_test_periods
    }
    
    automl_config = AutoMLConfig(task = 'forecasting',
                                 debug_log='automl_oj_sales_errors.log',
                                 primary_metric='normalized_root_mean_squared_error',
                                 experiment_timeout_minutes=20,
                                 training_data=train_data,
                                 label_column_name=label,
                                 n_cross_validations=5,
                                 path=project_folder,
                                 verbosity=logging.INFO,
                                 **time_series_settings)
    ```
    
### <a name="supported-models"></a>支持的模型

在自动化和优化过程中，自动化机器学习会尝试各种模型和算法。 用户不需要指定算法。 

这三个不同的 `task` 参数值确定要应用的算法或模型的列表。 使用 `allowed_models` 或 `blocked_models` 参数通过要包含或排除的可用模型来进一步修改迭代。 

下表按任务类型汇总了支持的模型。 

> [!NOTE]
> 如果你计划将自动化 ML 创建的模型导出为 [ONNX 模型](concept-onnx.md)，只有标有 * 的算法才能转换为 ONNX 格式。 详细了解[如何将模型转换为 ONNX](concept-automated-ml.md#use-with-onnx)。 <br> <br> 另请注意，ONNX 目前只支持分类和回归任务。 

分类 | 回归 | 时序预测
|-- |-- |--
[逻辑回归](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)* | [弹性网络](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)* | [弹性网络](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)* |[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)*|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[梯度提升](https://scikit-learn.org/stable/modules/ensemble.html#classification)* |[梯度提升](https://scikit-learn.org/stable/modules/ensemble.html#regression)* |[渐进提升](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[决策树](https://scikit-learn.org/stable/modules/tree.html#decision-trees)* |[决策树](https://scikit-learn.org/stable/modules/tree.html#regression)* |[决策树](https://scikit-learn.org/stable/modules/tree.html#regression)
[K 最近的邻域](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K 最近的邻域](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K 近邻](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[线性 SVC](https://scikit-learn.org/stable/modules/svm.html#classification)* |[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)* |[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[支持矢量分类 (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)* |[随机梯度下降 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)* |[随机梯度下降 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[随机林](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[随机林](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[随机林](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[极端随机树](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[极端随机树](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[极端随机树](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* |[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* | [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[平均感知器分类器](/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)|[在线梯度下降回归量](/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?preserve-view=true&view=nimbusml-py-latest) |[Auto-ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[快速线性回归量](/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?preserve-view=true&view=nimbusml-py-latest)|[Prophet](https://facebook.github.io/prophet/docs/quick_start.html)
[随机梯度下降 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)* ||ForecastTCN
|[线性 SVM 分类器](/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)*||

### <a name="primary-metric"></a>主要指标
`primary metric` 参数决定了将在模型训练期间用于优化的指标。 你可选择的可用指标取决于所选择的任务类型，下表显示了每种任务类型的有效主要指标。

选择要优化的自动机器学习的主要指标取决于许多因素。 我们建议你在选择最能满足你的业务需求的指标时选择一个指标。 然后考虑指标是否适用于数据集配置文件 (的数据大小、范围、类分布等 ) 。

如需了解上述指标的具体定义，请参阅[了解自动化机器学习结果集](how-to-understand-automated-ml.md)。

|分类 | 回归 | 时序预测
|--|--|--
|`accuracy`| `spearman_correlation` | `spearman_correlation`
|`AUC_weighted` | `normalized_root_mean_squared_error` | `normalized_root_mean_squared_error`
|`average_precision_score_weighted` | `r2_score` | `r2_score`
|`norm_macro_recall` | `normalized_mean_absolute_error` | `normalized_mean_absolute_error`
|`precision_score_weighted` |

### <a name="primary-metrics-for-classification-scenarios"></a>分类方案的主要指标 

Post thresholded 指标（如 `accuracy` 、 `average_precision_score_weighted` 、 `norm_macro_recall` 和） `precision_score_weighted` 可能不会对非常小的数据集进行优化 (，因此) 的类不平衡，或预期的指标值非常接近0.0 或1.0。 在这些情况下， `AUC_weighted` 可能是主要指标的更好选择。 自动机器学习完成后，可以根据最适合您的业务需求的指标选择入选的模型。

| 指标 | 示例用例 (s)  |
| ------ | ------- |
| `accuracy` | 图像分类，情绪分析，流失预测 |
| `AUC_weighted` | 欺诈检测、图像分类、异常检测/垃圾邮件检测 |
| `average_precision_score_weighted` | 情绪分析 |
| `norm_macro_recall` | 改动预测 |
| `precision_score_weighted` |  |

### <a name="primary-metrics-for-regression-scenarios"></a>回归方案的主要指标

`r2_score` `spearman_correlation` 当值预测的规模涵盖许多数量级时，和和等指标可以更好地表示模型的质量。 例如，对于薪金估算，其中许多人的薪水为 $ 20k 到 $ 100k，但在 $ 100M 范围内的某些薪金中，规模会变得非常高。 

`normalized_mean_absolute_error``normalized_root_mean_squared_error`在这种情况下，这种情况下，将使用 $ 30000 薪金作为工作人员的 $ 20k 预测错误视为具有 $ 20M 的工作人员。 在现实情况下，只预测 $ 20M 薪金中的 $ 20k 是非常接近 (小0.1% 相对差异) ，而从 $ 30000 关闭 $ 20k 不会接近 (67% 相对差异) 。 `normalized_mean_absolute_error``normalized_root_mean_squared_error`当要预测的值采用类似的刻度时，和非常有用。

| 指标 | 示例用例 (s)  |
| ------ | ------- |
| `spearman_correlation` | |
| `normalized_root_mean_squared_error` | 价格预测 (房子/产品/tip) ，查看分数预测 |
| `r2_score` | 航空延迟，薪金估算，Bug 解决时间 |
| `normalized_mean_absolute_error` |  |

### <a name="primary-metrics-for-time-series-forecasting-scenarios"></a>时序预测方案的主要指标

请参阅上面的回归注释。

| 指标 | 示例用例 (s)  |
| ------ | ------- |
| `spearman_correlation` | |
| `normalized_root_mean_squared_error` | 价格预测 (预测) ，库存优化，需求预测 |
| `r2_score` | 价格预测 (预测) ，库存优化，需求预测 |
| `normalized_mean_absolute_error` | |

### <a name="data-featurization"></a>数据特征化

在每个自动化机器学习实验中，数据都是 *自动缩放和规范化*，以帮助对不同规模上的特征敏感的某些算法。 此缩放和规范化称为特征化。 有关更多详细信息和代码示例，请参阅 [AutoML 中的特征化](how-to-configure-auto-features.md#)。 

在 `AutoMLConfig` 对象中配置试验时，可以启用/禁用设置 `featurization`。 下表列出了 [AutoMLConfig 对象](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)中的特征化的已接受设置。 

|特征化配置 | 说明 |
| ------------- | ------------- |
|`"featurization": 'auto'`| 指示在处理过程中自动执行[数据护栏和特征化步骤](how-to-configure-auto-features.md#featurization)。 默认设置。|
|`"featurization": 'off'`| 表示不应自动执行特征化步骤。|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| 指示应当使用自定义特征化步骤。 [了解如何自定义特征化](how-to-configure-auto-features.md#customize-featurization)。|

> [!NOTE]
> 自动化机器学习特征化步骤（特征规范化、处理缺失数据，将文本转换为数字等）成为了基础模型的一部分。 使用模型进行预测时，将自动向输入数据应用在训练期间应用的相同特征化步骤。

<a name="ensemble"></a>

### <a name="ensemble-configuration"></a> 集成配置

集成模型默认启用，在 AutoML 运行中显示为最终的运行迭代次数。 目前支持 **VotingEnsemble** 和 **StackEnsemble**。 

投票实现软投票，这种投票使用加权平均值。 堆栈实现使用一个两层实现，其中的第一层具有与投票集成相同的模型，第二层模型用于从第一层中查找模型的最佳组合。 

如果使用 ONNX 模型，或启用了模型可解释性，则会禁用堆栈，仅使用投票。

可以通过使用 `enable_voting_ensemble` 和 `enable_stack_ensemble` 布尔参数来禁用集成训练。

```python
automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=data_train,
        label_column_name=label,
        n_cross_validations=5,
        enable_voting_ensemble=False,
        enable_stack_ensemble=False
        )
```

若要更改默认集成行为，可以将多个默认参数作为 `kwargs` 在 `AutoMLConfig` 对象中提供。

> [!IMPORTANT]
>  以下参数不是 AutoMLConfig 类的显式参数。 

* `ensemble_download_models_timeout_sec`：在 VotingEnsemble 和 StackEnsemble 模型生成期间，会下载来自先前子运行的多个拟合模型。 如果遇到此错误 `AutoMLEnsembleException: Could not find any models for running ensembling`，则可能需要为要下载的模型提供更多时间。 默认值为 300 秒并行下载这些模型，且没有最大超时限制。 如果需要更多时间，请将此参数配置为大于 300 秒的值。 

  > [!NOTE]
  >  如果已超时且下载了模型，则融合会使用它下载的多个模型继续执行。 并不需要下载所有模型才能在超时内完成。

以下参数只应用于 StackEnsemble 模型： 

* `stack_meta_learner_type`：元学习器是针对单个异类模型的输出而训练出来的模型。 默认的元学习器是用于分类任务的 `LogisticRegression`（或为 `LogisticRegressionCV`，如果启用了交叉验证的话），以及用于回归/预测任务的 `ElasticNet`（或为 `ElasticNetCV`，如果启用了交叉验证的话）。 此参数可以是下列字符串之一：`LogisticRegression`、`LogisticRegressionCV`、`LightGBMClassifier`、`ElasticNet`、`ElasticNetCV`、`LightGBMRegressor` 或 `LinearRegression`。

* `stack_meta_learner_train_percentage`：指定为训练元学习器而保留的训练集的比例（选择训练的训练和验证类型时）。 默认值为 `0.2`。 

* `stack_meta_learner_kwargs`：要传递给元学习器的初始值设定项的可选参数。 这些参数和参数类型对来自相应模型构造函数的参数和参数类型进行镜像，然后再转发到模型构造函数。

下面的代码示例展示了如何在 `AutoMLConfig` 对象中指定自定义融合行为。

```python
ensemble_settings = {
    "ensemble_download_models_timeout_sec": 600
    "stack_meta_learner_type": "LogisticRegressionCV",
    "stack_meta_learner_train_percentage": 0.3,
    "stack_meta_learner_kwargs": {
        "refit": True,
        "fit_intercept": False,
        "class_weight": "balanced",
        "multi_class": "auto",
        "n_jobs": -1
    }
}

automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=5,
        **ensemble_settings
        )
```

<a name="exit"></a> 

### <a name="exit-criteria"></a>退出条件

可以在 AutoMLConfig 中定义几个选项来结束试验。

|条件| description
|----|----
无条件 | 如果未定义任何退出参数，则试验将继续，直到主要指标不再需要执行其他步骤。
在一段时间后| 在设置中使用 `experiment_timeout_minutes` 来定义试验应继续运行多长时间（以分钟为单位）。 <br><br> 若要避免试验超时失败，最少需要 15 分钟，如果行数乘以列数的大小超过 10,000,000，则最少需要 60 分钟。
达到某个分数| 使用 `experiment_exit_score` 将在达到指定的主要指标分数后完成试验。

## <a name="run-experiment"></a>运行试验

对于自动化 ML，可以创建 `Experiment` 对象，这是 `Workspace` 中用于运行实验的命名对象。

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'Tutorial-automl'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

提交试验以运行和生成模型。 将 `AutoMLConfig` 传递给 `submit` 方法以生成模型。

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>首先在新的计算机上安装依赖项。  最长可能需要在 10 分钟后才会显示输出。
>将 `show_output` 设置为 `True` 可在控制台上显示输出。

### <a name="multiple-child-runs-on-clusters"></a>群集上的多个子运行

自动化 ML 试验子运行可以在已经运行另一个试验的群集上执行。 但是，计时取决于群集具有的节点数，以及这些节点是否可用于运行不同的试验。

群集中的每个节点充当单个可以完成一次训练运行的虚拟机 (VM)；对于自动化 ML，这意味着一个子运行。 如果所有节点都处于忙状态，则新的试验将排队。 但是，如果有空闲节点，新的试验将在可用节点/VM 中并行运行自动化 ML 子运行。

为了管理子运行及其执行时间，建议你为每个试验创建一个专用群集，使试验的 `max_concurrent_iterations` 数与群集中的节点数匹配。 这样就可以同时使用群集的所有节点以及所需数量的并发子运行/迭代。

在 `AutoMLConfig` 对象中配置 `max_concurrent_iterations`。 如果未进行配置，则默认情况下每个试验仅允许一个并发子运行/迭代。  

## <a name="explore-models-and-metrics"></a>探索模型和指标

如果在笔记本中操作，可以在小组件或内联单元中查看训练结果。 有关更多详细信息，请参阅[跟踪和评估模型](how-to-monitor-view-training-logs.md#monitor-automated-machine-learning-runs)。

请参阅[评估自动化机器学习试验结果](how-to-understand-automated-ml.md)，查看为每次运行提供的性能图表和指标的定义和示例。 

若要获取特征化摘要并了解哪些功能已添加到特定模型，请参阅[特征化透明度](how-to-configure-auto-features.md#featurization-transparency)。 

> [!NOTE]
> 自动化 ML 使用的算法本身具有随机性，这可能会导致建议的模型最终指标分数（如准确度）出现细微差异。 自动化 ML 还可在必要时对数据执行操作，例如训练-测试拆分、训练-验证拆分或交叉验证。 因此，如果多次使用相同的配置设置和主要指标运行一个试验，你可能会发现由于这些因素导致每个试验最终指标分数存在差异。 

## <a name="register-and-deploy-models"></a>注册和部署模型

有关如何下载或注册模型以便部署到 Web 服务的详细信息，请参阅[如何部署模型以及在何处部署模型](how-to-deploy-and-where.md)。

<a name="explain"></a>

## <a name="model-interpretability"></a>模型可解释性

模型可解释性让你可以了解模型进行预测的原因，以及基础特征重要性值。 SDK 包括各种包，这些包用于在训练和推理时间为本地和已部署的模型启用模型可解释性功能。

有关如何在自动化机器学习试验中启用可解释性功能的代码示例，请参阅[操作方法](how-to-machine-learning-interpretability-automl.md)。

有关如何在自动化机器学习之外的其他 SDK 区域中启用模型解释和特征重要性的基本信息，请参阅可解释性方面的[概念](how-to-machine-learning-interpretability.md)文章。

> [!NOTE]
> 解释客户端目前不支持 ForecastTCN 模型。 如果此模型作为最佳模型返回，则不会返回解释仪表板，并且不支持按需解释运行。

## <a name="troubleshooting"></a>疑难解答

* **`AutoML` 依赖项到新版本的最新升级将破坏兼容性**：从 SDK 1.13.0 版开始，模型将不加载到较旧的 SDK 中，这是因为在之前的包中固定的旧版本与现在固定的更新的版本不兼容。 你将看到错误，例如：
  * 找不到模块：例如 `No module named 'sklearn.decomposition._truncated_svd`
  * 导入错误：例如 `ImportError: cannot import name 'RollingOriginValidator'`
  * 属性错误：例如： `AttributeError: 'SimpleImputer' object has no attribute 'add_indicator`
  
  若要解决此问题，请执行下面两个步骤之一，具体取决于你的 `AutoML` SDK 训练版本：
    * 如果 `AutoML` SDK 训练版本高于 1.13.0，则需要 `pandas == 0.25.1` 和 `scikit-learn==0.22.1`。 如果版本不匹配，请将 scikit-learn 和/或 pandas 升级为正确的版本，如下所示：
      
      ```bash
         pip install --upgrade pandas==0.25.1
         pip install --upgrade scikit-learn==0.22.1
      ```
      
    * 如果 `AutoML` SDK 训练版本低于或等于 1.12.0，则需要 `pandas == 0.23.4` 和 `sckit-learn==0.20.3`。 如果版本不匹配，请将 scikit-learn 和/或 pandas 降级为正确的版本，如下所示：
  
      ```bash
        pip install --upgrade pandas==0.23.4
        pip install --upgrade scikit-learn==0.20.3
      ```

* **部署失败**：对于版本低于或等于 1.18.0 的 SDK，为部署创建的基本映像可能会失败，并出现以下错误：“导入错误: 无法从 `werkzeug` 中导入名称 `cached_property`”。 

  以下步骤可解决此问题：
  1. 下载模型包
  2. 将包解压缩
  3. 使用解压缩资产进行部署

* **预测 R2 评分始终为零**：如果提供的训练数据的时间序列包含的值与上一个 `n_cv_splits` + `forecasting_horizon` 数据点相同，则会出现此问题。 如果该模式在你的时间序列中是预期的，可将主要指标切换为标准均方根误差。
 
* **TensorFlow**：从 SDK 1.5.0 版开始，自动化机器学习默认不安装 TensorFlow 模型。 若要安装 TensorFlow 并将其用于自动化 ML 试验，请通过 CondaDependecies 安装 tensorflow==1.12.0。 
 
   ```python
   from azureml.core.runconfig import RunConfiguration
   from azureml.core.conda_dependencies import CondaDependencies
   run_config = RunConfiguration()
   run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['tensorflow==1.12.0'])
  ```

* **试验图表**：自 4 月 12 日以来，自动化 ML 试验迭代中显示的二元分类图表（精准率-召回率、ROC、增益曲线等）在用户界面中无法正常呈现。 绘制的图表目前显示相反的结果：表现更好的模型反而显示更低的结果。 我们研究解决方法。

* **Databricks 取消自动化机器学习运行**：在 Azure Databricks 上使用自动化机器学习功能时，若要取消某个运行并启动新的试验运行，请重启 Azure Databricks 群集。

* **Databricks 自动化机器学习的迭代数超过 10 个**：在自动化机器学习设置中，如果迭代数超过 10 个，请在提交运行时将 `show_output` 设置为 `False`。

* **Databricks Azure 机器学习 SDK 和自动化机器学习的小组件**：Databricks 笔记本不支持 Azure 机器学习 SDK 小组件，因为笔记本无法分析 HTML 小组件。 可以通过在 Azure Databricks 笔记本单元中使用以下 Python 代码，在门户中查看该小组件：

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```
* **automl_setup 失败**： 
    * 在 Windows 上，从 Anaconda 提示符运行 automl_setup。 点击此链接[安装 Miniconda](https://docs.conda.io/en/latest/miniconda.html)。
    * 通过运行 `conda info` 命令，确保已安装 conda 64 位而不是 32 位。 对于 Windows，`platform` 应为 `win-64`，对于 Mac，应为 `osx-64`。
    * 确保已安装 conda 4.4.10 或更高版本。 可以使用命令 `conda -V` 检查该版本。 如果安装了以前的版本，可以使用以下命令对其进行更新：`conda update conda`。
    * Linux - `gcc: error trying to exec 'cc1plus'`
      *  如果遇到 `gcc: error trying to exec 'cc1plus': execvp: No such file or directory` 错误，请使用命令 `sudo apt-get install build-essential` 安装版本要素。
      * 将新名称作为第一个参数传递给 automl_setup 以创建新的 conda 环境。 使用 `conda env list` 查看现有的 conda 环境，并使用 `conda env remove -n <environmentname>` 删除它们。
      
* **automl_setup_linux.sh 失败**：如果 automl_setup_linus.sh 在 Ubuntu Linux 上失败，并出现错误：`unable to execute 'gcc': No such file or directory`-
  1. 确保已启用出站端口 53 和 80。 在 Azure VM 上，可选择 VM 并单击“网络”，从 Azure 门户执行此操作。
  2. 运行命令 `sudo apt-get update`
  3. 运行命令 `sudo apt-get install build-essential --fix-missing`
  4. 再次运行 `automl_setup_linux.sh`

* **configuration.ipynb 失败**：
  * 对于本地 conda，请首先确保 automl_setup 已成功运行。
  * 确保 subscription_id 是正确的。 依次选择“所有服务”和“订阅”，在 Azure 门户中查找 subscription_id。 字符“<”和“>”不应包含在 subscription_id 值中。 例如，`subscription_id = "12345678-90ab-1234-5678-1234567890abcd"` 的格式有效。
  * 确保参与者或所有者有权访问“订阅”。
  * 检查该区域是否为受支持的区域之一：`eastus2`、`eastus`、`westcentralus`、`southeastasia`、`westeurope`、`australiaeast`、`westus2`、`southcentralus`。
  * 确保使用 Azure 门户访问该区域。
  
* **`import AutoMLConfig` 失败**：自动化机器学习版本 1.0.76 中存在包更改，这要求先卸载以前的版本，再更新到新版本。 如果从 v1.0.76 之前的 SDK 版本升级到 v1.0.76 或更高版本后遇到 `ImportError: cannot import name AutoMLConfig`，请先运行 `pip uninstall azureml-train automl` 再运行 `pip install azureml-train-auotml` 来解决该错误。 automl_setup.cmd 脚本会自动执行此操作。 

* **workspace.from_config 失败**：如果调用 ws = Workspace.from_config()' 失败 -
  1. 确保 configuration.ipynb 笔记本已成功运行。
  2. 如果正在从不在运行 `configuration.ipynb` 的文件夹下的文件夹中运行笔记本，则将文件夹 aml_config 及其包含的文件 config.json 复制到新文件夹中。 Workspace.from_config 读取笔记本文件夹或其父文件夹的 config.json。
  3. 如果正在使用新的订阅、资源组、工作区或区域，请确保再次运行 `configuration.ipynb` 笔记本。 仅当指定订阅下的指定资源组中已存在工作区时，直接更改 config.json 才会生效。
  4. 如果要更改区域，请更改工作区、资源组或订阅。 即使指定的区域不同，`Workspace.create` 也不会创建或更新工作区（如果已存在）。
  
* **示例笔记本失败**：如果示例笔记本失败，并出现属性、方法或库不存在的错误：
  * 确保在 Jupyter Notebook 中选择了正确的内核。 内核显示在笔记本页面的右上方。 默认值为 azure_automl。 内核作为笔记本的一部分进行保存。 因此，如果切换到新的 conda 环境，则必须在笔记本中选择新内核。
      * 对于 Azure Notebooks，它应为 Python 3.6。 
      * 对于本地 conda 环境，它应为在 automl_setup 中指定的 conda 环境名称。
  * 确保笔记本适用于正在使用的 SDK 版本。 可在 Jupyter Notebook 单元中执行 `azureml.core.VERSION` 来检查 SDK 版本。 通过单击 `Branch` 按钮，选择 `Tags` 选项卡，然后选择版本，可以从 GitHub 下载以前版本的示例笔记本。

* **`import numpy` 在 Windows 中失败**：在某些 Windows 环境中，最新的 Python 3.6.8 版本加载 numpy 时会出现错误。 如果出现此问题，请尝试使用 Python 3.6.7 版本。

* **`import numpy` 失败**：在自动化 ML conda 环境中检查 TensorFlow 版本。 支持的版本为 <1.13 的版本。 如果版本不低于 1.13，请从环境中卸载 TensorFlow。 可以按下面的方式检查 TensorFlow 的版本并卸载：
  1. 启动命令 shell，激活安装了自动化 ML 包的 conda 环境。
  2. 输入 `pip freeze` 并查找 `tensorflow`，如果找到，则列出的版本应 <1.13
  3. 如果列出的版本不受支持，请在命令行界面中使用 `pip uninstall tensorflow`，并输入 y 进行确认。
  
 * 运行失败并出现 `jwt.exceptions.DecodeError`：确切的错误消息：`jwt.exceptions.DecodeError: It is required that you pass in a value for the "algorithms" argument when calling decode()`。

    对于版本不高于 1.17.0 的 SDK，安装可能会导致 PyJWT 的版本不受支持。 在自动化 ML conda 环境中检查 PyJWT 版本。 支持的版本为低于 2.0.0 的版本。 可以按下面的方式检查 PyJWT 的版本：
    1. 启动命令 shell，激活安装了自动化 ML 包的 conda 环境。
    2. 输入 `pip freeze` 并查找 `PyJWT`，如果找到，则列出的版本应低于 2.0.0

    如果列出的版本不是受支持的版本：
    1. 请考虑升级到 AutoML SDK 的最新版本：`pip install -U azureml-sdk[automl]`。
    2. 如果这不可行，请从环境中卸载 PyJWT，并安装正确的版本，如下所示：
        - 在命令行界面中输入 `pip uninstall PyJWT`，然后输入 `y` 进行确认。
        - 使用 `pip install 'PyJWT<2.0.0'` 进行安装。

## <a name="next-steps"></a>后续步骤

+ 详细了解[如何以及在何处部署模型](how-to-deploy-and-where.md)。

+ 详细了解[如何使用自动化机器学习训练回归模型](tutorial-auto-train-models.md)或[如何使用自动化机器学习对远程资源进行训练](how-to-auto-train-remote.md)。

+ 了解如何在[多模型解决方案加速器](https://aka.ms/many-models)中使用 AutoML 训练多个模型。
