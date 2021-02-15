---
title: 在 ADF 中排查 CI CD、Azure DevOps 和 GitHub 问题
description: 使用不同的方法对 ADF 中的 CI CD 问题进行故障排除。
author: ssabat
ms.author: susabat
ms.reviewer: susabat
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 12/03/2020
ms.openlocfilehash: 091c0cb20877090453f38ab922cc2bd277e90093
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393745"
---
# <a name="troubleshoot-ci-cd-azure-devops-and-github-issues-in-adf"></a>在 ADF 中排查 CI CD、Azure DevOps 和 GitHub 问题 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文探讨了在 Azure 数据工厂中进行连续 Integration-Continuous 部署 (CI CD) 、Azure DevOps 和 GitHub 问题的常见故障排除方法。

如果你在使用源代码管理或 DevOps 技术方面有疑问或问题，下面是一些可能有用的文章：

- 请参阅 [adf 中的源代码管理](source-control.md) ，了解如何在 adf 中练习源代码管理。 
- 请参阅  [adf 中的 CI cd](continuous-integration-deployment.md) ，详细了解如何在 adf 中进行 DevOps CI。
 
## <a name="common-errors-and-messages"></a>常见错误和消息

### <a name="connect-to-git-repository-failed-due-to-different-tenant"></a>由于不同的租户，连接到 Git 存储库失败

#### <a name="issue"></a>问题
    
有时会遇到 HTTP 状态401等身份验证问题。 特别是在有多个具有 guest 帐户的租户的情况下，可能会变得更加复杂。

#### <a name="cause"></a>原因

我们看到的是，令牌是从原始租户获得的，但 ADF 在来宾租户中，并且尝试使用令牌来访问来宾租户中的 DevOps。 这不是预期的行为。

#### <a name="recommendation"></a>建议

应改为使用从来宾租户颁发的令牌。 例如，你必须将同一 Azure Active Directory 分配为你的来宾租户以及你的 DevOps，因此它可以正确设置令牌行为并使用正确的租户。

### <a name="template-parameters-in-the-parameters-file-are-not-valid"></a>参数文件中的模板参数无效

#### <a name="issue"></a>问题

如果我们删除开发分支中的触发器，该触发器已在具有 **相同** 配置 (如 frequency 和 interval) 的测试或生产分支中可用，则发布管道部署将成功，并且相应的触发器将在各自环境中删除。 但是，如果在测试/生产环境中使用 **不同** 的配置 (如频率和间隔) 用于触发器，并且如果在 Dev 中删除相同的触发器，则部署将失败并出现错误。

#### <a name="cause"></a>原因

CI/CD 管道失败，出现以下错误：

`
2020-07-20T11:19:02.1276769Z ##[error]Deployment template validation failed: 'The template parameters 'Trigger_Salesforce_properties_typeProperties_recurrence_frequency, Trigger_Salesforce_properties_typeProperties_recurrence_interval, Trigger_Salesforce_properties_typeProperties_recurrence_startTime, Trigger_Salesforce_properties_typeProperties_recurrence_timeZone' in the parameters file are not valid; they are not present in the original template and can therefore not be provided at deployment time. The only supported parameters for this template are 'factoryName, PlanonDWH_connectionString, PlanonKeyVault_properties_typeProperties_baseUrl
`

#### <a name="recommendation"></a>建议

之所以发生此错误，是因为我们经常删除已参数化的触发器，因此，这些参数将不会在 ARM 模板 (中提供，因为) 不再存在该触发器。 由于参数不再位于 ARM 模板中，因此必须更新 DevOps 管道中的重写参数。 否则，每次更改 ARM 模板中的参数时，必须在部署任务) 中更新 DevOps 管道中的重写参数 (。

### <a name="updating-property-type-is-not-supported"></a>不支持更新属性类型

#### <a name="issue"></a>问题

CI/CD 发布管道失败，出现以下错误：

`
2020-07-06T09:50:50.8716614Z There were errors in your deployment. Error code: DeploymentFailed.
2020-07-06T09:50:50.8760242Z ##[error]At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.
2020-07-06T09:50:50.8771655Z ##[error]Details:
2020-07-06T09:50:50.8772837Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8774148Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8775530Z ##[error]Check out the troubleshooting guide to see if your issue is addressed: https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment#troubleshooting
2020-07-06T09:50:50.8776801Z ##[error]Task failed while creating or updating the template deployment.
`

#### <a name="cause"></a>原因

这是因为目标工厂中具有相同名称但具有不同类型的集成运行时。 部署时，Integration Runtime 需要具有相同的类型。

#### <a name="recommendation"></a>建议

- 请参阅下面的 CI/CD 最佳方案：

    https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd 
- 集成运行时不经常更改，并且在 CI/CD 中的所有阶段都是类似的，因此，数据工厂要求在 CI/CD 的所有阶段都具有相同的集成运行时类型。 如果名称和类型 & 属性不同，请确保匹配源和目标集成运行时配置，然后部署发布管道。
- 若要在所有阶段中共享集成运行时，请考虑使用三元工厂，这只是为了包含共享的集成运行时。 可以在所有环境中将此共享工厂用作链接的集成运行时类型。

### <a name="document-creation-or-update-failed-because-of-invalid-reference"></a>由于无效引用，文档创建或更新失败

#### <a name="issue"></a>问题

尝试将更改发布到数据工厂时，会收到以下错误消息：

`
"error": {
        "code": "BadRequest",
        "message": "The document creation or update failed because of invalid reference '<entity>'.",
        "target": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<rgname>/providers/Microsoft.DataFactory/factories/<datafactory>/pipelines/<pipeline>",
        "details": null
    }
`

#### <a name="symptom"></a>症状

已断开 Git 配置，并再次使用选中的 "导入资源" 标志来进行设置，这会将数据工厂设置为 "同步"。 这意味着没有要发布的更改。

#### <a name="resolution"></a>解决方法

分离 Git 配置并再次进行设置，并确保不选中 "导入现有资源" 复选框。

### <a name="data-factory-move-failing-from-one-resource-group-to-another"></a>数据工厂从一个资源组移动到另一个资源组失败

#### <a name="issue"></a>问题

无法将数据工厂从一个资源组移到另一个资源组，但由于以下错误而失败：

`
{
    "code": "ResourceMoveProviderValidationFailed",
    "message": "Resource move validation failed. Please see details. Diagnostic information: timestamp 'xxxxxxxxxxxxZ', subscription id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', tracking id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', request correlation id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'.",
    "details": [
        {
            "code": "BadRequest",
            "target": "Microsoft.DataFactory/factories",
            "message": "One of the resources contain integration runtimes that are either SSIS-IRs in starting/started/stopping state, or Self-Hosted IRs which are shared with other resources. Resource move is not supported for those resources."
        }
    ]
}
`

#### <a name="resolution"></a>解决方法

需要删除 SSIS-IR 和 Shared IRs 才能允许移动操作。 如果不想删除集成运行时，最好的方法是遵循复制和克隆文档来完成复制，完成后，删除旧的数据工厂。

###  <a name="unable-to-export-and-import-arm-template"></a>无法导出和导入 ARM 模板

#### <a name="issue"></a>问题

无法导出和导入 ARM 模板。 门户上没有错误，但在浏览器跟踪中，你可能会看到以下错误：

`Failed to load resource: the server responded with a status code of 401 (Unauthorized)`

#### <a name="cause"></a>原因

您已经创建了用户角色，但该角色没有必要的权限。 在 UI 中加载工厂时，将检查工厂的一系列公开控制值。 在这种情况下，用户的访问角色无权访问 *queryFeaturesValue* API。 若要访问此 API，请关闭全局参数功能。 ARM 导出代码路径部分依赖于全局参数功能。

#### <a name="resolution"></a>解决方法

为了解决此问题，你需要将以下权限添加到你的角色： *DataFactory/工厂/queryFeaturesValue/action*。 默认情况下，此权限应包含在 "数据工厂参与者" 角色中。

###  <a name="automatic-publishing-for-cicd-without-clicking-publish-button"></a>不单击 "发布" 按钮自动发布 CI/CD  

#### <a name="issue"></a>问题

在 ADF 门户中单击 "手动发布" 按钮时，不会启用自动 CI/CD 操作。

#### <a name="cause"></a>原因

直到最近，只为部署发布 ADF 管道的方法是使用 ADF 门户按钮单击。 现在，您可以自动执行此过程。 

#### <a name="resolution"></a>解决方法

CI/CD 进程已增强。 **自动发布** 功能从 ADF UX 中验证和导出所有 Azure 资源管理器 (ARM) 模板功能。 它通过公开提供的 npm 包实现逻辑 [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities) 。 这允许你以编程方式触发这些操作，而无需转到 ADF UI 并单击按钮。 这为 CI/CD 管道提供了 **真正** 的持续集成体验。 有关详细信息，请遵循 [ADF CI/CD 发布改进](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment-improvements) 。 

###  <a name="cannot-publish-because-of-4mb-arm-template-limit"></a>无法发布，因为有4mb 个 ARM 模板限制  

#### <a name="issue"></a>问题

你无法部署，因为你已达到 Azure 资源管理器大小为4mb 的总模板大小限制。 你需要在超过限制后部署解决方案。 

#### <a name="cause"></a>原因

Azure 资源管理器将模板大小限制为4mb。 将模板大小限制为 4 MB 以内，每个参数文件大小限制为 64 KB 以内。 4-MB 限制适用于模板使用迭代资源定义以及变量和参数值进行扩展后的最终状态。 不过，您已经超出了限制。 

#### <a name="resolution"></a>解决方法

对于中小型解决方案，单个模板更易于理解和维护。 可以查看单个文件中的所有资源和值。 对于高级方案，使用链接模板可将解决方案分解为目标组件。 请遵循 [使用链接模板和嵌套模板](https://docs.microsoft.com/azure/azure-resource-manager/templates/linked-templates?tabs=azure-powershell)的最佳做法。

## <a name="next-steps"></a>后续步骤

有关故障排除的更多帮助，请尝试以下资源：

*  [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [数据工厂功能请求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [数据工厂 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [关于数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)
