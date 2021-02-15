---
title: 通过门户发布托管应用
description: 演示如何使用 Azure 门户创建适用于组织成员的 Azure 托管应用程序。
author: tfitzmac
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: tomfitz
ms.openlocfilehash: 05302d92f2304be35a7b88fac6fabfc17b13c63e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "75651717"
---
# <a name="publish-a-service-catalog-application-through-azure-portal"></a>通过 Azure 门户发布服务目录应用程序

可以使用 Azure 门户发布适用于组织成员的[托管应用程序](overview.md)。 例如，IT 部门可发布确保符合组织标准的托管应用程序。 这些托管应用程序通过服务目录（而不是 Azure 市场）提供。

## <a name="prerequisites"></a>必备条件

发布托管应用程序时，请指定用于管理资源的标识。 我们建议你指定 Azure Active Directory 用户组。 若要创建 Azure Active Directory 用户组，请参阅[在 Azure Active Directory 中创建组并添加成员](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。 

必须通过 URI 获取包含托管应用程序定义的 .zip 文件。 我们建议将 .zip 文件上传到存储 blob。 

## <a name="create-managed-application-with-portal"></a>使用门户创建托管应用程序

1. 在左上角，选择“+ 新建”  。

   ![新建服务](./media/publish-portal/new.png)

1. 搜索**服务目录**。

1. 在结果中，向下滚动直至找到“服务目录托管应用程序定义”  。 选择它。

   ![搜索托管应用程序定义](./media/publish-portal/select-managed-apps-definition.png)

1. 选择“创建”以启动创建托管应用程序定义的过程。 

   ![创建托管应用程序定义](./media/publish-portal/create-definition.png)

1. 为名称、显示名称、说明、位置、订阅和资源组提供值。 对于包文件 URI，请提供你创建的 zip 文件的路径。

   ![提供值](./media/publish-portal/fill-application-values.png)

1. 到达“授权和锁定级别”部分时，选择“添加授权”。 

   ![添加授权](./media/publish-portal/add-authorization.png)

1. 选择一个 Azure Active Directory 组来管理资源，然后选择“确定”。 

   ![添加授权组](./media/publish-portal/add-auth-group.png)

1. 在提供所有值后，选择“创建”。 

   ![创建托管应用程序](./media/publish-portal/create-app.png)

## <a name="next-steps"></a>后续步骤

* 有关托管应用程序的简介，请参阅[托管应用程序概述](overview.md)。
* 有关托管应用程序示例，请参阅 [Azure 托管应用程序的示例项目](sample-projects.md)。
* 若要了解如何为托管应用程序创建 UI 定义文件，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。