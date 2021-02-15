---
title: 测试驱动器技术配置，Microsoft 商业市场
description: 了解测试驱动器。 在提交购买之前，测试驱动器允许新客户测试您的产品/服务。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 08/13/2019
author: trkeya
ms.author: trkeya
ms.openlocfilehash: 553eba3898a5ea42e5d478603e35e82c68abcab1
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462896"
---
# <a name="test-drive-technical-configuration"></a>体验版技术配置

使用 Microsoft 商业应用商店中的 "测试驱动器" 选项，您可以配置您的产品关键功能的动手自学指导教程。 使用测试驱动器，新客户可以在提交之前试用你的产品/服务。 有关详细信息，请参阅[什么是体验版？](what-is-test-drive.md)

如果你不再希望为你的产品/服务提供一个测试驱动器，请返回 " **产品/服务设置** " 页，然后清除 " **启用测试驱动器** " 复选框。 并非所有产品/服务类型都有可用的测试驱动器。

## <a name="azure-resource-manager-test-drive"></a>Azure 资源管理器测试驱动器

这是适用于虚拟机或 Azure 应用的唯一测试驱动器选项，需要进行相当详细的设置。 请阅读以下部分，了解 [部署订阅详细信息](#deployment-subscription-details) 和 [测试驱动器列表](#test-drive-listings)，然后继续阅读 [Azure 资源管理器](azure-resource-manager-test-drive.md)的单独主题。

## <a name="hosted-test-drive"></a>托管的测试驱动器

Microsoft 可以通过托管和维护服务预配和取消设置，消除设置测试驱动器的复杂性。 无论测试驱动器面向的是 Dynamics 365 Customer Engagement 还是 Dynamics 365 操作受众，此类测试驱动器的配置都是相同的。

- **最大并发体验版数**（必需）- 设置可同时使用体验版的最大客户数。 在测试驱动器处于活动状态时，每个并发用户都将使用 Dynamics 365 许可证，因此请确保你有足够的可用许可证来支持最大限制集。 建议值为 3-5。

-  (所需的 **测试驱动器持续时间**) –输入测试驱动器将为每个客户保持活动状态的小时数。 在此时间段后，该会话将结束，不再消耗你的许可证之一。 建议根据产品/服务的复杂度将值设为 2-24 小时。 此持续时间只能设置为整小时 (例如，"2 小时" 有效;不) "1.5 小时"。 如果用户已耗尽时间，但想再次访问体验版，该用户可请求新会话。

- **实例 URL**（必需）- 客户将在其中开始使用体验版的 URL。 通常，这是正在运行你的应用且安装了示例数据的 Dynamics 365 实例的 URL（例如 `https://testdrive.crm.dynamics.com`）。

- **实例 Web API URL** (必需的) –通过登录到 Microsoft 365 帐户并导航到 "**设置**"  >  "**自定义**  >  **开发人员资源**  >  **实例 Web api (服务根 url)**" 来检索 Dynamics 365 实例的 Web api url (，复制此处找到的 URL) 例如 `https://testdrive.crm.dynamics.com/api/data/v9.0` 。

- **角色名称**（必需）- 提供你在自定义 Dynamics 365 体验版中定义的安全角色名称，该名称将在用户体验版分配给用户（例如，名称为 test-drive-role）。

若要获得有关如何为测试驱动器设置 Dynamics 365 环境并授予 AppSource 权限来设置和取消设置租户中的测试驱动器用户的帮助，请遵循 [这些说明](./test-drive-azure-subscription-setup.md)。

有关列出和配置托管测试驱动器的分步说明，请访问 " [托管测试驱动器的详细配置](./test-drive-hosted-detailed-config.md) " 页。

## <a name="logic-app-test-drive"></a>逻辑应用测试驱动器

此类型的测试驱动器不由 Microsoft 托管。 使用它连接 Dynamics 365 产品/服务或其他自定义资源，其中包含各种复杂的解决方案体系结构。 有关设置逻辑应用体验版的详细信息，请访问 GitHub 上的 [Operations](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) 和 [Customer Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app)。

- **区域**（必需，单选下拉列表）- 当前存在 26 个 Azure 支持的区域，可在这些区域提供你的体验版。 逻辑应用的资源将部署在所选区域中。 如果逻辑应用在特定区域中存储了任何自定义资源，请务必在此处选择该区域。 最佳方式是在作出此选择之前，先在门户中将逻辑应用完全部署到 Azure 订阅本地，并验证它是否正常工作。

- **最大并发体验版**（必需）- 设置可同时使用体验版的最大用户数。 这些体验版已部署完毕，客户无需等待部署便可立即访问它们。

- **体验版持续时间**（必需）- 输入体验版保持活动状态的时间长度，以小时为单位。 此时间段结束后，体验版会自动终止。

- **Azure 资源组名称** (必需) –输入用于保存逻辑应用测试驱动器的 [azure 资源组](../azure-resource-manager/management/overview.md#resource-groups)) 名称。

- **Azure 逻辑应用名称**（必需）- 输入将体验版分配给用户的逻辑应用的名称。 此逻辑应用必须保存在上面的 Azure 资源组中。

- 取消预 **配逻辑应用名称** (必需) –输入在客户完成后取消预配测试驱动器的逻辑应用的名称。 此逻辑应用必须保存在上面的 Azure 资源组中。

## <a name="power-bi-test-drive"></a>Power BI 体验版

希望演示交互式 Power BI 视觉对象的产品可以使用嵌入式链接来共享定制仪表板作为其体验版，无需进行进一步的技术配置。 你只需上载嵌入的 Power BI URL 即可。

有关设置 Power BI 应用的详细信息，请参阅 [什么是 Power BI 应用？](/power-bi/service-template-apps-overview)

## <a name="deployment-subscription-details"></a>部署订阅详细信息

若要允许 Microsoft 代表你部署体验版，请创建并提供单独的唯一 Azure 订阅（Power BI 体验版不需要）。

- **Azure 订阅 ID**（Azure 资源管理器和逻辑应用需要）- 输入订阅 ID 以授予对 Azure 帐户服务的访问权限，以便进行资源使用情况报告和计费。 建议考虑[创建单独的 Azure 订阅](../cost-management-billing/manage/create-subscription.md)以用于体验版（如果尚未创建）。 登录 [Azure 门户](https://portal.azure.com/)并导航到左侧菜单的“订阅”选项卡，即可找到 Azure 订阅 ID。 选择该选项卡将显示订阅 ID （例如“a83645ac-1234-5ab6-6789-1h234g764ghty”）。

- **Azure AD 租户 ID** (必需) –输入 AZURE ACTIVE DIRECTORY (AD) [租户 ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)) 。 若要查找此 ID，请登录 [Azure 门户](https://portal.azure.com/)，依次选择左侧菜单中的“Active Directory”选项卡和“属性”，然后查找列出的“目录 ID”编号（例如，50c464d3-4930-494c-963c-1e951d15360e）。 还可以使用 [https://www.whatismytenantid.com](https://www.whatismytenantid.com) 的域名地址来查找组织的租户 ID。

- Azure AD 租户名称（Dynamic 365 需要）- 输入 Azure Active Directory (AD) 名称。 若要找到此名称，请登录 [Azure 门户](https://portal.azure.com/)，租户名称将在右上角的帐户名称下列出。

- **Azure AD 应用 ID** (必需) –输入 Azure Active Directory () AD) [应用程序 id](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) 。 若要查找此 ID，请登录到 [Azure 门户](https://portal.azure.com/)，在左侧菜单中选择 "Active Directory" 选项卡，选择 " **应用注册**"，然后查找 (所列出的 **应用程序 ID** 号，如 " `50c464d3-4930-494c-963c-1e951d15360e`) "。

- **Azure AD 应用客户端机密** (必需) –输入 Azure AD 应用程序 [客户端密钥](../active-directory/develop/howto-create-service-principal-portal.md#option-2-create-a-new-application-secret)) 。 若要找到此值，请登录 [Azure 门户](https://portal.azure.com/)。 选择左侧菜单中的 " **Azure Active Directory** " 选项卡，选择 " **应用注册**"，然后选择你的测试驱动器应用。 接下来，选择 "**证书和密码**"，选择 "**新建客户端密码**"，输入描述，选择 "**永不****过期**"，然后选择 "**添加**"。 请务必复制该值。 在复制值之前，请勿导航离开页面。

## <a name="test-drive-listings"></a>测试驱动器列表

"合作伙伴中心" 中的 "**测试驱动器**" 选项卡下的 "**测试驱动器列表**" 选项显示你的测试驱动器可用 (和市场) 的语言，当前的英语 (美国) 是唯一可用的位置。 此外，此页还显示特定于语言的列表的状态及其添加日期/时间。 需要为每种语言/市场 (说明、用户手册、视频等 ) 定义测试驱动器详细信息。

- **描述** (必需的) ：描述您的测试驱动器、要演示的目标、要浏览的功能，以及帮助用户确定是否获取您的产品/服务的相关信息。 最多可在此字段中输入 3,000 个字符的文本。

- Azure 资源管理器和逻辑测试驱动器)  (需要 **访问信息**：说明了客户需要知道哪些内容才能访问和使用此试用版。 演练使用产品/服务的方案，以及客户在整个体验版中访问功能时应了解的确切内容。 最多可在此字段中输入 10,000 个字符的文本。

- 需要 **用户手动** () ：深入了解你的测试驱动器体验。 用户手册应确切地涵盖希望客户通过体验体验版而获得的好处，并用作他们可能遇到的任何问题的参考。 该文件必须采用 PDF 格式，并在上传之后命名（最多 255 个字符）。

- **视频：** (可选) 中添加视频：此处托管的视频可在此处引用 (533 x 324) 像素的链接和缩略图，因此，客户可以查看信息，以帮助他们更好地了解测试驱动器，包括如何成功使用产品/服务的功能并了解突出显示其优势的方案。
  - **名称**（必需）
  - 仅限 (YouTube 或 Vimeo 的 **URL** ;必需) 
  - **缩略图** (533 x 324 像素) –图像必须为 PNG 格式。

如果你当前在合作伙伴中心创建了你的测试驱动器，请选择 " **保存草稿** "，然后继续。

有关列出和配置托管测试驱动器的分步说明，请访问 " [托管测试驱动器的详细配置](./test-drive-hosted-detailed-config.md) " 页。

## <a name="additional-resources"></a>其他资源

## <a name="next-steps"></a>后续步骤

- [试用版驱动最佳做法](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [概述](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)（PDF；确保弹出窗口阻止程序处于禁用状态）
- [更新商业市场中的现有产品/服务](partner-center-portal/update-existing-offer.md)