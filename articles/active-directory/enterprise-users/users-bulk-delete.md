---
title: 在 Azure Active Directory 门户中批量删除用户 | Microsoft Docs
description: 在 Azure Active Directory 中的 Azure 管理中心批量删除用户
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 12/02/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: b57d675003c1ebeb29927b86338f95cf7dd68090
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96574457"
---
# <a name="bulk-delete-users-in-azure-active-directory"></a>批量删除 Azure Active Directory 中的用户

在 Azure Active Directory (Azure AD) 门户中，可使用逗号分隔值 (CSV) 文件批量删除用户，从而将大量成员从组中删除。

## <a name="understand-the-csv-template"></a>了解 CSV 模板

下载并填写 CSV 模板，该模板可帮助你成功批量删除 Azure AD 用户。 下载的 CSV 模板可能如下例所示：

![用于上传和调出的电子表格，说明了每一行和每一列的用途和值](./media/users-bulk-delete/understand-template.png)

### <a name="csv-template-structure"></a>CSV 模板结构

下载的 CSV 模板中的行如下所示：

- **版本号**：包含版本号的第一行必须包含在上传的 CSV 中。
- 列标题：列标题的格式为：&lt;项名称&gt; [PropertyName] &lt;必需或空白&gt;。 例如，`User name [userPrincipalName] Required`。 某些较旧版本的模板可能会略有不同。
- 示例行：我们已经在模板中包含了一行示例，说明每列可接受的值。 必须删除示例行并将其替换为你自己的项。

### <a name="additional-guidance"></a>其他指南

- 不得删除或修改上传模板的前两行，否则无法处理上传。
- 所需的列会先列出。
- 建议不要将新列添加到模板。 所添加的任何其他列都会被忽略，不进行处理。
- 建议尽可能经常下载 CSV 模板的最新版本。

## <a name="to-bulk-delete-users"></a>批量删除用户

1. 使用组织中的用户管理员帐户[登录到 Azure AD 组织](https://aad.portal.azure.com)。
1. 在 Azure AD 中，选择“用户” > “批量删除”。
1. 在“批量删除用户”页上，选择“下载”以接收一个有效的包含用户属性的 CSV 文件。

   ![选择可在其中列出要删除的用户的本地 CSV 文件](./media/users-bulk-delete/bulk-delete.png)

1. 打开 CSV 文件，并为每个要删除的用户添加一行。 唯一需要的值为“用户主体名称”。 然后保存文件。

   ![CSV 文件包含要删除的用户的名称和 ID](./media/users-bulk-delete/delete-csv-file.png)

1. 在“批量删除用户”页的“上传 csv 文件”下，浏览到该文件。 选择该文件并单击“提交”后，将启动对 CSV 文件的验证。
1. 验证文件内容后，会看到“文件上传成功”消息。 如果有错误，必须修正错误，然后才能提交作业。
1. 文件通过验证后，请选择“提交”，以启动用于删除用户的 Azure 批量操作。
1. 删除操作完成后，会显示一条通知，指出批量操作成功。

如果有错误，可以在“批量操作结果”页下载并查看结果文件。 该文件包含每个错误的原因。

## <a name="check-status"></a>查看状态

可在“批量操作结果”页中查看所有挂起的批量请求的状态。

   [![在“批量操作结果”页中查看删除状态。](./media/users-bulk-delete/bulk-center.png)](./media/users-bulk-delete/bulk-center.png#lightbox)

接下来，可通过 Azure 门或 PowerShell 查看已删除的用户是否存在于 Azure AD 组织中。

## <a name="verify-deleted-users-in-the-azure-portal"></a>在 Azure 门户中验证已删除的用户

1. 使用组织中的用户管理员的帐户登录到 Azure 门户。
1. 在导航窗格中选择“Azure Active Directory”。
1. 在“管理”下，选择“用户” 。
1. 在“显示”下仅选择“所有用户”，验证已删除的用户是否不再列出。

### <a name="verify-deleted-users-with-powershell"></a>使用 PowerShell 验证已删除的用户

运行以下命令：

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

验证已删除的用户是否不再列出。

## <a name="next-steps"></a>后续步骤

- [批量添加用户](users-bulk-add.md)
- [下载用户列表](users-bulk-download.md)
- [批量还原用户](users-bulk-restore.md)
