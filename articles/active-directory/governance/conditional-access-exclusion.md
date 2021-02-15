---
title: 管理从条件访问中排除的用户 - Azure AD
description: 了解如何使用 Azure Active Directory (Azure AD) 访问评审管理已从条件访问策略中排除的用户
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 426e28048ae370919529ea710717a3a3867d999d
ms.sourcegitcommit: 6e2d37afd50ec5ee148f98f2325943bafb2f4993
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/23/2020
ms.locfileid: "97746246"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-excluded-from-conditional-access-policies"></a>使用 Azure AD 访问评审管理已从条件访问策略中排除的用户

在理想情况下，所有用户都会遵循访问策略来保护对组织资源的访问。 但是，有时，某些业务案例要求例外处理。 本文提供了一些可能需要排除项的示例。 IT 管理员可以管理此任务，避免监督从策略中排除的用户，并向审核员证明已使用 Azure Active Directory (Azure AD) 访问评审定期评审这些已排除的用户。

>[!NOTE]
> 使用 Azure AD 访问评审需要有效的 Azure AD Premium P2、企业移动性 + 安全性 E5 付费版或试用版许可证。 有关详细信息，请参阅 [Azure Active Directory 版本](../fundamentals/active-directory-whatis.md)。

## <a name="why-would-you-exclude-users-from-policies"></a>为何要从策略中排除用户？

假设你是管理员并决定使用 [Azure AD 条件访问](../conditional-access/concept-conditional-access-policy-common.md)来要求进行多重身份验证 (MFA) 并将身份验证请求限制到特定的网络或设备。 在部署计划期间，你认识到并非所有用户都满足这些要求。 例如，你的用户可能从远程办公室办公，不在内部网络中。 你还可能需要在等待更换不受支持的设备时允许用户使用这些设备进行连接。 简而言之，企业需要这些用户登录并执行其作业，因此需要将他们从条件访问策略中排除。

另举一例，你可能在条件访问中使用[命名位置](../conditional-access/location-condition.md)来配置一组国家和地区，你不希望该国家/地区的用户访问他们的租户。

![条件访问中的命名位置](./media/conditional-access-exclusion/named-locations.png)

遗憾的是，某些用户可能仍有正当理由从这些已阻止的国家/地区登录。 例如，用户因公事出差，并需要访问公司资源。 在这种情况下，阻止这些国家/地区的条件访问策略可对已从策略中排除的用户使用云安全组。 在旅行期间需要进行访问的用户可以使用 [Azure AD 自助服务组管理](../enterprise-users/groups-self-service-management.md)将自己添加到该组。

另举一例，某个条件访问策略可能会[阻止大多数用户的旧式身份验证](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/)。 但是，如果某些用户需要使用旧式身份验证方法通过 Office 2010 或基于 IMAP/SMTP/POP 的客户端来访问你的资源，则你可以从阻止旧式身份验证方法的策略中排除这些用户。

>[!NOTE]
>Microsoft 强烈建议在租户中阻止使用旧式协议，以提高安全状态。

## <a name="why-are-exclusions-challenging"></a>为何排除项会带来挑战？

在 Azure AD 中，可将条件访问策略限定为一组用户。 还可以通过选择 Azure AD 角色、单个用户或来宾来配置排除项。 请记住，在配置排除项后，不能对已排除的用户强制执行策略意向。 如果使用用户列表或旧式本地安全组来配置排除项，则不会看到排除项。 因此：

- 用户可能不知道他们已被排除。

- 用户可以通过加入安全组来绕过策略。

- 已排除的用户可能在之前需要排除，但现在不再需要排除。

通常，在首次配置排除项时，绕过策略的用户列表很短。 随着时间的推移，越来越多的用户将添加到排除项中，列表也会增长。 有时，你需要审查该列表，并确认是否仍然应该排除其中的每个用户。 从技术角度讲，管理排除列表可能相对容易，但谁是业务决策人？如何确保该列表完全可审核？ 但是，如果使用 Azure AD 组配置排除项，则可以使用访问评审作为互补性的控制措施来提高可见性，并减少已排除用户的数量。

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>如何在条件访问策略中创建排除组

请遵循以下步骤创建新的 Azure AD 组，以及不会应用到该组的条件访问策略。

### <a name="create-an-exclusion-group"></a>创建排除组

1. 登录到 Azure 门户。

2. 在左侧导航栏中，依次单击“Azure Active Directory”、“组” 。

3. 在顶部菜单中，单击“新建组”打开“组”窗格。

4. 在“组类型”列表中，选择“安全性”。  指定名称和说明。

5. 请务必将“成员身份”类型设置为“已分配”。 

6. 选择应包含在此排除组中的用户，然后单击“创建”。

![Azure Active Directory 中的“新建组”窗格](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>创建排除该组的条件访问策略

现在，可以创建使用此排除组的条件访问策略。

1. 在左侧导航栏中，单击 Azure Active Directory，然后单击“条件访问”，打开“策略”边栏选项卡  。

2. 单击“新建策略”打开“新建”窗格。 

3. 指定名称。

4. 在“分配”下，单击“用户和组”。

5. 在“包括”选项卡上，选择“所有用户”。 

6. 在“排除”选项卡上，勾选“用户和组”，然后单击“选择要排除的用户”  。

7. 选择创建的排除组。

   > [!NOTE] 
   > 作为最佳做法，我们建议在测试时至少从策略中排除一个管理员帐户，以确保不会将你锁在租户之外。

8. 根据组织的要求继续设置条件访问策略。

![条件访问中的“选择要排除的用户”窗格](./media/conditional-access-exclusion/select-excluded-users.png)
  
让我们通过两个示例来了解可在哪种情况下使用访问评审管理条件访问策略中的排除项。

## <a name="example-1-access-review-for-users-accessing-from-blocked-countriesregions"></a>示例 1：对从已阻止国家/地区访问的用户进行访问评审

假设某个条件访问策略会阻止来自特定国家/地区的访问。 该策略排除了某个组。 下面是评审该组成员的建议访问评审方法。

> [!NOTE] 
> 创建访问评审需要全局管理员或用户管理员角色。

1. 评审每周进行一次。

2. 评审永远不会结束，以确保将此排除组保持最新状态。

3. 此组的所有成员在评审范围内。

4. 每个用户必须自我证明他们仍需从这些已阻止国家/地区进行访问，因此他们仍需是该组的成员。

5. 如果用户未对评审请求作出响应，则会自动将他们从该组中删除，因此，在这些国家/地区出差时，他们不再能够访问租户。

6. 启用邮件通知，让用户知道访问评审的开始和完成时间。

    ![示例 1 的“创建访问评审”窗格](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>示例 2：对使用旧式身份验证进行访问的用户进行访问评审

假设某个条件访问策略会阻止使用旧式身份验证和旧客户端版本的用户进行访问，并包括已从策略中排除的组。 下面是评审该组成员的建议访问评审方法。

1. 此项评审需是定期评审。

2. 该组中的每个人都需要接受评审。

3. 评审可配置为将业务部门主管列作选定的评审者。

4. 自动应用结果并删除未经批准的用户，让他们继续使用旧式身份验证方法。

5. 启用建议可以帮助大型组的评审者轻松做出决策。

6. 启用邮件通知，让用户知道访问评审的开始和完成时间。

    ![示例 2 的“创建访问评审”窗格](./media/conditional-access-exclusion/create-access-review-2.png)

>[!IMPORTANT] 
>如果你有许多的排除组，因此需要创建多个访问评审，现在可以使用 Microsoft Graph 测试版终结点中的某个 API 以编程方式创建和管理访问评审。 若要开始，请参阅 [Azure AD 访问评审 API 参考](/graph/api/resources/accessreviews-root?view=graph-rest-beta)和[通过 Microsoft Graph 检索 Azure AD 访问评审的示例](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096)。

## <a name="access-review-results-and-audit-logs"></a>访问评审结果和审核日志

做好组、条件访问策略和访问评审方面的一切准备工作后，可以监视和跟踪这些评审的结果。

1. 在 Azure 门户中，打开“访问评审”边栏选项卡。

2. 打开创建用于管理排除组的控制措施和程序。

3. 单击“结果”查看已批准哪些人保留在该列表中，以及删除了哪些人。

    ![访问评审结果，显示谁已获批准](./media/conditional-access-exclusion/access-reviews-results.png)

4. 然后单击“审核日志”查看评审期间执行的操作。

    ![访问评审审核日志列表操作](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

IT 管理员知道，管理策略的排除组有时不可避免。 但是，如果使用 Azure AD 访问评审，则业务主管或用户自己可以更轻松维护这些组、定期评审这些组以及审核所做的更改。

## <a name="next-steps"></a>后续步骤

- [创建组或应用程序的访问评审](create-access-review.md)
- [什么是 Azure Active Directory 中的条件访问？](../conditional-access/overview.md)