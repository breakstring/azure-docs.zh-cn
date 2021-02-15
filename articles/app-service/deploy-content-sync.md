---
title: 同步云文件夹中的内容
description: 了解如何通过内容同步将你的应用部署到 Azure App Service 云文件夹，包括 OneDrive 或 Dropbox。
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.topic: article
ms.date: 12/03/2018
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 880edff95bb548ec5328c543a542ea5dfcfc362f
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150291"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>将内容从云文件夹同步到 Azure 应用服务
本文介绍如何将内容同步到 Dropbox 和 OneDrive 上的 [Azure 应用服务](./overview.md)。 

按需内容同步部署由应用服务的 [Kudu 部署引擎](https://github.com/projectkudu/kudu/wiki)提供支持。 你可以在指定的云文件夹中使用应用代码和内容，然后通过单击按钮同步到应用服务。 内容同步使用 Kudu 生成服务器。 

## <a name="enable-content-sync-deployment"></a>启用内容同步部署

若要启用内容同步，请导航至 [Azure 门户](https://portal.azure.com)中的“应用服务应用”页。

在左侧菜单中，单击 "**部署中心**  >  **OneDrive**或**Dropbox**  >  **授权**"。 按照授权提示进行操作。 

![演示如何在 Azure 门户的部署中心授权 OneDrive 或 Dropbox。](media/app-service-deploy-content-sync/choose-source.png)

只需使用 OneDrive 或 Dropbox 授权一次。 如果已获得授权，只需单击“继续”**** 即可。 可以通过单击“更改帐户”**** 来更改已获得授权的 OneDrive 或 Dropbox 帐户。

![演示如何在 Azure 门户的 "部署中心" 中更改授权的 OneDrive 或 Dropbox 帐户。](media/app-service-deploy-content-sync/continue.png)

在“配置”**** 页上，选择要同步的文件夹。 将在 OneDrive 或 Dropbox 中的以下指定内容路径下创建此文件夹。 
   
* **OneDrive**： `Apps\Azure Web Apps`
* **Dropbox**： `Apps\Azure`

完成后，单击“继续”。****

在“摘要”**** 页中，确认选项，然后单击“完成”****。

## <a name="synchronize-content"></a>同步内容

如果想要使用应用服务同步云文件夹中的内容，请返回到“部署中心”**** 页，单击“同步”****。

![演示如何将云文件夹与应用服务进行同步。](media/app-service-deploy-content-sync/synchronize.png)
   
   > [!NOTE]
   > 由于 API 中存在的基础差异，目前不支持 **OneDrive for Business**。 
   > 
   > 

## <a name="disable-content-sync-deployment"></a>禁用内容同步部署

若要禁用内容同步，请导航至 [Azure 门户](https://portal.azure.com)中的“应用服务应用”页。

在左侧菜单中，单击 "**部署中心**  >  **断开连接**"。

![演示如何将云文件夹同步与 Azure 门户中的应用服务应用断开连接。](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [从本地 Git 存储库进行部署](deploy-local-git.md)