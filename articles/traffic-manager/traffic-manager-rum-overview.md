---
title: Azure 流量管理器中的真实用户度量
description: 在本简介中，了解 Azure 流量管理器真实用户度量的工作方式。
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: duau
ms.custom: ''
ms.openlocfilehash: 618f8fff532da0f6ae315ad9e4cda35a289949d1
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98183704"
---
# <a name="traffic-manager-real-user-measurements-overview"></a>流量管理器的实际用户度量概述

将流量管理器配置文件设置为使用性能路由方法时，该服务将查看 DNS 查询请求的来源，做出路由决策将这些请求者定向到可为其提供最低延迟的 Azure 区域。 这是通过利用流量管理器为不同最终用户网络维护的网络延迟智能实现的。

使用实际用户度量，可通过最终用户使用的客户端应用程序度量 Azure 区域的网络延迟，让流量管理器在做出路由决策时也考虑该信息。 选择使用“实际用户度量”，可以提高来自最终用户所在这些网络的请求的路由准确性。 

## <a name="how-real-user-measurements-work"></a>实际用户度量的工作原理

真实用户度量的工作原理是让客户端应用程序度量 Azure 区域的延迟，因为从使用这些应用程序的最终用户网络可以看到此延迟。 例如，如果你的网页被不同位置（例如，在北美区域中）的用户访问，那么你可以使用带有性能路由方法的真实用户度量来将其带到服务器应用程序所在的最佳 Azure 区域。

首先，将 Azure 提供的 JavaScript（其中包含唯一密钥）嵌入到网页中。 完成该操作后，每当用户访问该网页时，JavaScript 都会查询流量管理器以获取有关它应度量的 Azure 区域的信息。 服务将一组终结点返回给脚本，然后脚本将连续度量这些区域，方法是下载这些 Azure 区域中托管的单像素图像，并记下发送请求的时间与收到第一个字节的时间之间的延迟。 然后将这些度量报告回流量管理器服务。

在一段时间内，这种情况在多个网络上多次发生使流量管理器可以获取有关最终用户所在网络的延迟特征的更准确信息。 此信息开始包括在流量管理器做出的路由决策中。 因此，提高了基于发送的实际用户度量做出的这些决策的准确性。

使用实际用户度量时，将基于发送到流量管理器的度量数进行计费。 有关定价的详细信息，请访问[流量管理器定价页](https://azure.microsoft.com/pricing/details/traffic-manager/)。

## <a name="faqs"></a>常见问题解答

* [使用真实用户度量的好处是什么？](./traffic-manager-faqs.md#what-are-the-benefits-of-using-real-user-measurements)

* [是否可以在非 Azure 区域使用真实用户度量？](./traffic-manager-faqs.md#can-i-use-real-user-measurements-with-non-azure-regions)

* [哪种路由方法受益于真实用户度量？](./traffic-manager-faqs.md#which-routing-method-benefits-from-real-user-measurements)

* [是否需要单独启用每个配置文件的真实用户度量？](./traffic-manager-faqs.md#do-i-need-to-enable-real-user-measurements-each-profile-separately)

* [如何关闭我的订阅的真实用户度量？](./traffic-manager-faqs.md#how-do-i-turn-off-real-user-measurements-for-my-subscription)

* [除了 Web 页面以外，是否可以在客户端应用程序中使用真实用户度量？](./traffic-manager-faqs.md#can-i-use-real-user-measurements-with-client-applications-other-than-web-pages)

* [每次呈现启用真实用户度量的 Web 页面会生成多少度量？](./traffic-manager-faqs.md#how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered)

* [真实用户度量脚本在我的网页中运行之前是否有延迟？](./traffic-manager-faqs.md#is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage)

* [是否可以只在想要度量的 Azure 区域使用真实用户度量？](./traffic-manager-faqs.md#can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure)

* [是否可以将执行的度量数限制在一个特定数值内？](./traffic-manager-faqs.md#can-i-limit-the-number-of-measurements-made-to-a-specific-number)

* [是否可以查看我的客户端应用程序作为真实用户度量的一部分所执行的度量？](./traffic-manager-faqs.md#can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements)

* [是否可以修改流量管理器提供的度量脚本？](./traffic-manager-faqs.md#can-i-modify-the-measurement-script-provided-by-traffic-manager)

* [其他人是否有可能看到我的真实用户度量的钥？](./traffic-manager-faqs.md#will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements)

* [其他人是否可以滥用我的 RUM 密钥？](./traffic-manager-faqs.md#can-others-abuse-my-rum-key)

* [是否需要将度量 JavaScript 放入我的所有网页中？](./traffic-manager-faqs.md#do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages)

* [如果我使用真实用户度量，有关我的最终用户的信息是否会被流量管理器识别？](./traffic-manager-faqs.md#can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements)

* [测量真实用户度量的网页是否需要使用流量器以供路由使用？](./traffic-manager-faqs.md#does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing)

* [是否需要在 Azure 区域托管任何服务才能使用真实用户度量？](./traffic-manager-faqs.md#do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements)

* [使用真实用户度量时，我的 Azure 带宽使用率是否会增加？](./traffic-manager-faqs.md#will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements)

## <a name="next-steps"></a>后续步骤
- 了解如何[将实际用户度量用于网页](traffic-manager-create-rum-web-pages.md)
- 了解[流量管理器的工作原理](traffic-manager-overview.md)
- 详细了解 [Mobile Center](/mobile-center/)
- 详细了解流量管理器支持的[流量路由方法](traffic-manager-routing-methods.md)
- 了解如何[创建流量管理器配置文件](./quickstart-create-traffic-manager-profile.md)