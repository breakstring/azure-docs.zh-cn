---
title: 排查 Azure 付款问题
description: 解决在 Microsoft Azure 门户或帐户中心更新付款信息帐户时遇到的问题。
author: v-miegge
ms.reviewerr: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 08/20/2020
ms.author: jaserano
ms.openlocfilehash: 77ca8e8425326cec31fce345600bdd0174ab5f4e
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131847"
---
# <a name="troubleshoot-azure-payment-issues"></a>排查 Azure 付款问题

尝试在 Microsoft Azure 门户或 Azure 帐户中心更新付款信息帐户时，可能会遇到问题或错误。

若要解决问题，请在下面选择最接近错误的主题。

## <a name="my-credit-card-was-declined-when-i-tried-to-sign-up-for-azure"></a>尝试注册 Azure 时，信用卡被拒绝了

若要排查卡被拒绝的问题，请参阅[排查注册 Azure 时卡被拒的问题](troubleshoot-declined-card.md)。

## <a name="unable-to-see-subscriptions-under-my-account-to-update-the-payment-method"></a>在我的帐户下看不到需更新付款方式的订阅

你使用的电子邮件 ID 可能不同于用于订阅的电子邮件 ID。

若要排查此问题，请参阅[在 Azure 门户或 Azure 帐户中心注册时出现“找不到任何订阅”错误](no-subscriptions-found.md)。

## <a name="unable-to-use-a-virtual-or-prepaid-credit-or-debit-card-as-a-payment-method"></a>无法使用虚拟或预付信用卡或借记卡作为付款方式。

*   不接受虚拟或预付信用卡作为 Azure 订阅的付款方式。
*   不接受借记卡作为 Azure 订阅的付款方式。

有关详细信息，请参阅[排查注册 Azure 时卡被拒的问题](troubleshoot-declined-card.md)。

## <a name="unable-to-remove-a-credit-card-from-a-saved-billing-payment-method"></a>无法从保存的账单付款方式中删除信用卡

按照设计，不能从有效的订阅中删除信用卡。

如果必须删除现有的卡，则必须向订阅添加新卡，这样才能成功删除旧的付款方式，否则必须取消该订阅。 这样会永久删除订阅，并且会删除卡。

## <a name="unable-to-delete-an-old-payment-method-after-adding-a-new-payment-method"></a>在添加新付款方式后无法删除旧付款方式

新付款方式可能未与订阅关联。 若要帮助将付款方式与订阅相关联，请参阅[添加、更新或删除用于 Azure 的信用卡](change-credit-card.md)。

## <a name="unable-to-delete-a-payment-method-because-of-cannot-delete-payment-method-error"></a>因存在“无法删除付款方式”错误，无法删除付款方式 

发生此错误是由于存在未清余额。 请在删除付款方式之前结清任何未清余额。

## <a name="unable-to-make-payment-for-a-subscription"></a>无法为订阅付款

如果收到错误消息：“付款已过期。  你的付款方式有问题”或“很抱歉，无法保存信息。  请关闭浏览器，然后再试。”则可能是因为卡上仍有待付款，因为卡被金融机构拒绝过。

请验证信用卡是否有足够的余额来付款。 如果没有足够的余额，请使用其他卡来付款，或者联系你的金融机构解决此问题。

请与银行联系以解决以下问题：

- 未启用国际交易。
- 卡存在信用额度限制，余额必须结清。
- 在卡上启用了定期付款功能。

## <a name="unable-to-change-payment-method-because-of-browser-issues-browser-does-not-respond-does-not-load-and-so-on"></a>因浏览器问题（浏览器不响应、无法加载，等等）而无法更改付款方式

注销所有活动的 Azure 会话，然后按[在 Microsoft Edge 中浏览 InPrivate](https://support.microsoft.com/help/4026200/microsoft-edge-browse-inprivate) 一文中的步骤操作，在 Microsoft Edge 或 Internet Explorer 中启动 InPrivate 会话。

在专用会话中，按[如何更改信用卡](change-credit-card.md)中的步骤操作，更新或更改信用卡信息。

也可尝试执行以下操作：

- 刷新浏览器
- 使用另一浏览器
- 删除缓存的 Cookie

## <a name="my-subscription-is-still-disabled-after-updating-the-payment-method"></a>我的订阅在更新付款方式以后仍处于禁用状态。

发生此问题是由于存在未清余额。 请在删除付款方式之前结清任何未清余额。

## <a name="unable-to-change-payment-method-because-of-an-xml-error-response-page"></a>因存在 XML 错误响应页而无法更改付款方式

如果使用 [Azure 门户](https://portal.azure.com/)添加新的信用卡，可能会收到此消息。

若要添加信用卡详细信息，请使用帐户管理员的电子邮件地址登录到 Azure 帐户门户。

## <a name="additional-help-resources"></a>其他帮助资源

Azure 计费和订阅的其他疑难解答文章

- [银行卡被拒绝](troubleshoot-declined-card.md)
- [订阅登录问题](troubleshoot-sign-in-issue.md)
- [找不到任何订阅](no-subscriptions-found.md)
- [企业成本视图已禁用](enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>联系我们以获取帮助

如有任何疑问或需要帮助，请[创建支持请求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤

- [Azure 计费文档](../index.yml)