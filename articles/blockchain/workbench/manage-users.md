---
title: 在 Azure 区块链工作台中管理用户
description: 如何在 Azure Blockchain Workbench 中管理用户。
ms.date: 07/15/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: be078e7149ba008ab8b7ad44a016a0d1cf82df0e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91263005"
---
# <a name="manage-users-in-azure-blockchain-workbench"></a>在 Azure Blockchain Workbench 中管理用户

Azure Blockchain Workbench 包含的用户管理功能适用于隶属于联盟的人员和组织。

## <a name="prerequisites"></a>必备条件

需部署 Blockchain Workbench。 有关部署详情，请参阅 [Azure Blockchain Workbench 部署](deploy.md)。

## <a name="add-azure-ad-users"></a>添加 Azure AD 用户

Azure Blockchain Workbench 将 Azure Active Directory (Azure AD) 用于身份验证、访问控制和角色。 Blockchain Workbench Azure AD 租户中的用户可以在进行身份验证后使用 Blockchain Workbench。 向“管理员”应用程序角色添加可以进行交互并执行操作的用户。

在向应用程序和角色分配 Blockchain Workbench 用户之前，该用户必须存在于 Azure AD 租户中。 若要向 Azure AD 添加用户，请执行以下步骤：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在右上角选择帐户，然后切换到与 Blockchain Workbench 关联的 Azure AD 租户。
1. 选择“Azure Active Directory”>“用户”。**** 此时会看到目录中用户的列表。
1. 若要向目录添加用户，请选择“新用户”。**** 对于外部用户，请选择“新来宾用户”。****
1. 完成新用户的必填字段。 选择“创建”  。

若要更详细地了解如何管理 Azure AD 中的用户，请访问 [Azure AD](../../active-directory/fundamentals/add-users-azure-active-directory.md) 文档。

## <a name="manage-blockchain-workbench-administrators"></a>管理 Blockchain Workbench 管理员

将用户添加到目录以后，下一步是选择充当 Blockchain Workbench 管理员的用户。 “管理员”组中的用户与 Blockchain Workbench 中的“管理员”应用程序角色**** 相关联。**** 管理员可以添加或删除用户、为特定方案分配用户，以及创建新应用程序。

若要向**** Azure AD 目录中的“管理员”组添加用户，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在右上角选择帐户，验证自己是否存在于与 Blockchain Workbench 关联的 Azure AD 租户中。
1. 选择 **Azure Active Directory > 企业应用程序**。
1. 将 " **应用程序类型** " 下拉筛选器更改为 " **所有应用程序** " 并选择 " **应用**"。
1. 选择适用于 Azure 区块链工作台的 Azure AD 客户端应用程序

    ![所有企业应用程序注册](./media/manage-users/select-blockchain-client-app.png)

1. 选择“用户和组”>“添加用户”。****
1. 在“添加分配”中，选择“用户”。 选择或搜索需将其作为管理员添加的用户。 选择完以后，单击“选择”。****

    ![添加分配](./media/manage-users/add-user-assignment.png)

1. 验证是否已将“角色”设置为“管理员”********
1. 选择“分配”。**** 添加的用户显示在列表中，已向其分配管理员角色。

    ![区块链客户端应用用户](./media/manage-users/blockchain-admin-list.png)

## <a name="managing-blockchain-workbench-members"></a>管理 Blockchain Workbench 成员

请使用 Blockchain Workbench 应用程序来管理隶属于联盟的用户和组织。 可以添加或删除应用程序和角色的用户。

1. 在浏览器中[打开 Blockchain Workbench](deploy.md#blockchain-workbench-web-url)，以管理员身份登录。

    ![Blockchain Workbench](./media/manage-users/blockchain-workbench-applications.png)

    成员将添加到每个应用程序。 成员可以通过一个或多个应用程序角色来启动合同或执行操作。

1. 若要管理应用程序的成员，请在“应用程序”窗格中选择应用程序磁贴。****

    与所选应用程序关联的成员数反映在成员磁贴中。

    ![选择应用程序](./media/manage-users/blockchain-workbench-select-application.png)


#### <a name="add-member-to-application"></a>将成员添加到应用程序

1. 选择成员磁贴，以便显示当前成员的列表。
1. 选择“添加成员”。

    ![屏幕截图显示 "应用程序成员身份" 窗口，其中突出显示了 "添加成员" 按钮。](./media/manage-users/application-add-members.png)

1. 搜索用户的名称。  只会列出 Blockchain Workbench 租户中存在的 Azure AD 用户。 如果找不到用户，则需要 [添加 Azure AD 用户](#add-azure-ad-users)。

    ![添加成员](./media/manage-users/find-user.png)

1. 从下拉列表中选择一个**角色**。

    ![选择角色成员](./media/manage-users/application-select-role.png)

1. 选择“添加”，将具有关联角色的成员添加到应用程序。 

#### <a name="remove-member-from-application"></a>从应用程序中删除成员

1. 选择成员磁贴，以便显示当前成员的列表。
1. 对于要删除的用户，请从角色下拉列表中选择“删除”。****

    ![移除成员](./media/manage-users/application-remove-member.png)

#### <a name="change-or-add-role"></a>更改或添加角色

1. 选择成员磁贴，以便显示当前成员的列表。
1. 对于要更改的用户，请单击下拉列表，然后选择新角色。

    ![更改角色](./media/manage-users/application-change-role.png)

## <a name="next-steps"></a>后续步骤

本操作指南文章介绍了如何管理 Azure Blockchain Workbench 的用户。 若要了解如何创建区块链应用程序，请继续学习下一篇操作指南文章。

> [!div class="nextstepaction"]
> [在 Azure Blockchain Workbench 中创建区块链应用程序](create-app.md)
