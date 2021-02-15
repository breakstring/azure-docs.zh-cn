---
title: 在 Azure 开发测试实验室中设置保留策略 |Microsoft Docs
description: 了解如何配置保留策略、清理工厂以及从开发测试实验室停用旧映像。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 115fdff215399a9a51171161191ecf5009e8e20e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85476047"
---
# <a name="set-up-retention-policy-in-azure-devtest-labs"></a>在 Azure 开发测试实验室中设置保留策略
本文介绍如何设置保留策略、清理工厂以及从组织中的其他所有开发测试实验室中停用旧映像。 

## <a name="prerequisites"></a>先决条件
继续下一步之前，请务必遵循以下文章：

- [创建映像工厂](image-factory-create.md)
- [从 Azure DevOps 运行映像工厂](image-factory-set-up-devops-lab.md)
- [保存自定义映像并分发到多个实验室](image-factory-save-distribute-custom-images.md)

应已准备好以下各项：

- Azure 开发测试实验室中的映像工厂的实验室
- 一个或多个目标 Azure 开发测试实验室，工厂将在其中分发黄金映像
- 用于自动执行映像工厂的 Azure DevOps 项目。
- 包含脚本和配置 (的源代码位置，本示例中使用的是上述相同的 DevOps 项目) 
- 用于协调 Azure Powershell 任务的生成定义
 
## <a name="setting-the-retention-policy"></a>设置保留策略
在配置清理步骤之前，请定义要在开发测试实验室中保留的历史映像数量。 按照 [从 Azure DevOps 运行映像工厂一](image-factory-set-up-devops-lab.md) 文中所述配置了各种生成变量。 其中一项已 **ImageRetention**。 将此变量设置为 `1` ，这意味着开发测试实验室不会维护自定义映像的历史记录。 只有最新的分布式映像才能使用。 如果将此变量更改为 `2` ，则将保留最新的分布式映像和上一个。 可以设置此值来定义要在开发测试实验室中维护的历史映像的数目。

## <a name="cleaning-up-the-factory"></a>清理工厂
清理工厂的第一步是从映像工厂中删除黄金映像 Vm。 就像前面的脚本一样，可以使用脚本来执行此任务。 第一步是将另一个 **Azure Powershell** 任务添加到生成定义，如下图所示：

![PowerShell 步骤](./media/set-retention-policy-cleanup/powershell-step.png)

在列表中具有新任务后，选择该项目，然后填写所有详细信息，如下图所示：

![清理旧映像 PowerShell 任务](./media/set-retention-policy-cleanup/configure-powershell-task.png)

脚本参数为： `-DevTestLabName $(devTestLabName)` 。

## <a name="retire-old-images"></a>停用旧映像 
此任务将删除任何旧的映像，仅保留与 **ImageRetention** 生成变量匹配的历史记录。 向生成定义添加其他 **Azure Powershell** 生成任务。 添加后，选择该任务，并填写详细信息，如下图所示： 

![停用旧映像 PowerShell 任务](./media/set-retention-policy-cleanup/retire-old-image-task.png)

脚本参数如下： `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(devTestLabName) -ImagesToSave $(ImageRetention)`

## <a name="queue-the-build"></a>对生成进行排队
现在，你已完成生成定义，请将新的生成排队，以确保一切正常。 成功完成生成后，新的自定义映像会显示在目标实验室中，如果你检查映像工厂实验室，你将看不到预配的 Vm。 此外，如果您将更多的生成进行排队，会看到清除任务从开发测试实验室中注销旧的自定义映像，并根据生成变量中设置的保留值。

> [!NOTE]
> 如果在系列的最后一篇文章末尾执行了生成管道，请在对新的生成进行排队之前，手动删除在映像工厂实验室中创建的虚拟机。  仅当我们设置一切并验证其工作时，才需要手动清理步骤。



## <a name="summary"></a>总结
现在，你有一个正在运行的映像工厂，可以根据需要生成自定义映像并将其分发到实验室。 此时，只需正确设置映像并识别目标实验室即可。 如前一篇文章中所述，位于**配置**文件夹中的**Labs.js**文件指定应在每个目标实验室中提供的映像。 将其他开发测试实验室添加到组织中时，只需在新实验室的 Labs.js中添加一个条目。

将新图像添加到工厂也很简单。 如果要在工厂中包含新映像，请打开 [Azure 门户](https://portal.azure.com)，导航到工厂开发测试实验室，选择按钮以添加 VM，然后选择所需的 marketplace 映像和项目。 选择 "**查看 Azure 资源管理器模板**"，而不是选择 "创建" 按钮来**创建**新的 VM，并将模板另存为存储库中**GoldenImages**文件夹内的一个 json 文件。 下次运行映像工厂时，它会创建自定义映像。


## <a name="next-steps"></a>后续步骤
1. [计划生成/发布](/azure/devops/pipelines/build/triggers?view=azure-devops&tabs=designer) 以便定期运行映像工厂。 它会定期刷新工厂生成的映像。
2. 为工厂创建更多黄金映像。 你还可以考虑 [创建项目](devtest-lab-artifact-author.md) 以编写 VM 安装任务的其他部分的脚本，并将项目包括在工厂映像中。
4. 创建 [单独的生成/发布](/azure/devops/pipelines/overview?view=azure-devops-2019) ，单独运行 **DistributeImages** 脚本。 当你对上的 Labs.js进行更改并使图像复制到目标实验室时，你可以运行此脚本，而无需重新创建所有映像。

