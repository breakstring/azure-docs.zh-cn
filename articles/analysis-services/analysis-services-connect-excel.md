---
title: 使用 Excel 连接到 Azure Analysis Services | Microsoft Docs
description: 了解如何使用 Excel 连接到 Azure Analysis Services 服务器。 连接后，用户可以创建数据透视表以浏览数据。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: fccb919a7eac79080fefeac70326b8aa7a967622
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499464"
---
# <a name="connect-with-excel"></a>使用 Excel 进行连接

你创建服务器并向其部署表格模型后，客户端便可以连接并开始浏览数据。 

## <a name="before-you-begin"></a>开始之前

用于登录的帐户必须属于具有至少读取权限的模型数据库角色。 若要了解详细信息，请参阅[身份验证和用户权限](analysis-services-manage-users.md)。 

## <a name="connect-in-excel"></a>在 Excel 中连接

可通过使用 Excel 2016 及更高版本中的“获取数据”在 Excel 中连接到服务器。 不支持使用 Power Pivot 中的“导入表向导”进行连接。 

1. 在 Excel 的 "**数据**" 功能区上，单击 "从数据库 **获取数据**"  >  **From Database**  >  **Analysis Services**。

2. 在“数据连接向导”的“服务器名称”中，输入服务器名称，包括协议和 URI。  例如，asazure://westcentralus.asazure.windows.net/advworks。 然后，在“登录凭据”  中，选择“使用以下用户名和密码”  ，并键入组织的用户名（例如 nancy@adventureworks.com）和密码。

    > [!IMPORTANT]
    > 如果使用 Microsoft 帐户、Live ID、Yahoo、Gmail 等登录或被要求使用多重身份验证登录，请将密码字段留空。 单击“下一步”后，系统会提示输入密码。 

    ![“从 Excel 中连接”登录](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. 在“选择数据库和表”  中，选择数据库和模型或视角，并单击“完成”  。
   
    ![“从 Excel 中连接”选择模型](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>另请参阅

[客户端库](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true)   
[管理服务器](analysis-services-manage.md)
