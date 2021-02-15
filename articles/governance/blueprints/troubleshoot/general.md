---
title: 排查常见错误
description: 了解如何排查创建、分配和删除蓝图等问题，例如，策略违规和蓝图参数函数。
ms.date: 01/27/2021
ms.topic: troubleshooting
ms.openlocfilehash: 65cf8ef9a5dcba0165aad8522f91ff1eb2c963a8
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98918838"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>排查使用 Azure 蓝图时出现的错误

创建、分配或删除蓝图时可能会遇到错误。 本文描述可能会发生的各种错误及其解决方法。

## <a name="finding-error-details"></a>查找错误详细信息

将蓝图分配到作用域是许多错误产生的原因。 分配失败时，蓝图会提供失败部署的详细信息。 此信息会指出存在的问题，以便可以修复问题并确保后续部署成功进行。

1. 在左侧窗格中，选择“所有服务”。 搜索并选择“蓝图”。

1. 从左侧页面中选择 " **分配的蓝图** "，然后使用 "搜索" 框筛选蓝图分配，查找失败的分配。 还可以按“预配状态”列对分配表进行排序，集中查看失败的分配项。

1. 选择状态为 " _失败_ " 的蓝图，或右键单击并选择 " **查看分配详细信息**"。

1. 蓝图分配页面顶部有一个红色横幅警告，指出此分配已失败。 选择标题上的任何位置以获取更多详细信息。

错误通常是由某个项目导致的，而不是由蓝图整体导致的。 如果某个项目创建密钥保管库但是 Azure Policy 阻止密钥保管库创建，则整个分配将失败。

## <a name="general-errors"></a>常规错误

### <a name="scenario-policy-violation"></a><a name="policy-violation"></a>场景：策略冲突

#### <a name="issue"></a>问题

由于策略冲突而导致模板部署失败。

#### <a name="cause"></a>原因

如下多个原因可能会导致策略与部署相冲突：

- 正在创建的资源受到策略的限制（通常为 SKU 或位置限制）
- 部署是由策略配置的设置字段（通常带有标记）

#### <a name="resolution"></a>解决方法

更改蓝图，使其不与错误详细信息中的策略冲突。 如果无法进行此更改，替代方法是更改策略分配的作用域，以使蓝图不再与策略冲突。

### <a name="scenario-blueprint-parameter-is-a-function"></a><a name="escape-function-parameter"></a>方案：蓝图参数是一个函数

#### <a name="issue"></a>问题

作为函数的蓝图参数在传递到项目之前处理。

#### <a name="cause"></a>原因

将使用函数的蓝图参数（例如 `[resourceGroup().tags.myTag]`）传递到项目会导致在项目上设置的函数的处理结果而不是动态函数。

#### <a name="resolution"></a>解决方法

若要将函数作为参数传递，请使用 `[` 转义整个字符串，使蓝图参数如 `[[resourceGroup().tags.myTag]`。 转义字符会导致蓝图在处理蓝图时将值视为字符串。 然后，蓝图服务将函数放置在项目上，使其能够按预期方式动态进行。 有关详细信息，请参阅 [Azure 资源管理器模板中的语法和表达式](../../../azure-resource-manager/templates/template-expressions.md)。

## <a name="delete-errors"></a>删除错误

### <a name="scenario-assignment-deletion-timeout"></a><a name="assign-delete-timeout"></a>方案：分配删除超时

#### <a name="issue"></a>问题

无法完成蓝图分配的删除。

#### <a name="cause"></a>原因

删除时，蓝图分配可能会停滞为非终止状态。 如果蓝图分配创建的资源仍处于挂起状态，或者不会将状态代码返回到 Azure 蓝图，则会导致此状态。

#### <a name="resolution"></a>解决方法

处于非终端状态的蓝图分配在 _六小时_ 超时后会自动标记为 "**失败**"。 超时调整了蓝图分配的状态后，可以重试删除。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

- 请通过 [Azure 论坛](https://azure.microsoft.com/support/forums/)获取 Azure 专家的解答。
- 与 [@AzureSupport](https://twitter.com/azuresupport)（Microsoft Azure 官方帐户）联系，它可以将 Azure 社区引导至适当的资源来改进客户体验：提供解答、支持和专业化服务。
- 如需更多帮助，可以提交 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **获取支持**。