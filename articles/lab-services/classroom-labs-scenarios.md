---
title: 使用实验室培训-Azure 实验室服务
description: 本文介绍了如何使用 Azure 开发测试实验室在 Azure 上创建用于培训方案的实验室。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: f9eb3d44d470a978e676f6268cd693b283dd2703
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96433558"
---
# <a name="use-labs-for-trainings"></a>使用实验室进行培训
借助 Azure 实验室服务，教师（老师、教授、培训师或助教等）可以快速、轻松地创建在线实验室，为学员提供预配置的学习环境。 每个学员都能够使用完全相同的独立环境接受培训。 可以应用策略来确保培训环境只有在每个学员都需要时才对他们可用，并包含培训所需的足够资源（如虚拟机）。 

![课堂实验室](./media/classroom-labs-scenarios/classroom.png)

实验室满足在任何虚拟环境中进行培训所需的以下要求： 

- 学员可快速设置其培训环境
- 每台培训计算机都应相同
- 学员无法看到由其他学员创建的 VM
- 通过确保学员无法获取多于培训所需的资源和在不使用时关闭虚拟机来控制成本
- 轻松与学员共享培训实验室
- 反复重新使用培训实验室

本文介绍了可用于满足上述培训要求的各种 Azure 实验室服务功能，以及创建用于培训的实验室所需遵循的详细步骤。  

## <a name="create-the-lab-account-as-a-lab-account-administrator"></a>以实验室帐户管理员的身份创建实验室帐户
使用 Azure 实验室服务的第一步是，在 Azure 门户中创建实验室帐户。 在实验室帐户管理员创建实验室帐户后，管理员将要创建实验室的用户添加到“实验室创建者”角色中。 教师创建包含虚拟机的实验室，让学生为他们正在教授的课程做练习。 有关详细信息，请参阅[创建和管理实验室帐户](how-to-manage-lab-accounts.md)。

## <a name="create-and-manage-labs"></a>创建和管理实验室
教师是实验室帐户中实验室创建者角色的成员，可以在实验室帐户中创建一个或多个实验室。 创建并配置一个模板 VM，其中包含完成课程练习所需的全部软件。 从可用于创建课堂实验室的映像中选择一个现成映像，然后通过安装实验室所需的软件对它进行自定义。 有关详细信息，请参阅 [创建和管理实验室](how-to-manage-classroom-labs.md)。

## <a name="configure-usage-settings-and-policies"></a>配置使用设置和策略
实验室创建者可以在实验室中添加或删除用户、获取要发送给实验室用户的注册链接、设置策略（如设置每个用户的个人配额）、更新实验室中的可用 VM 数等。 有关详细信息，请参阅[配置使用设置和策略](how-to-configure-student-usage.md)。

## <a name="create-and-manage-schedules"></a>创建并管理计划
借助日程安排，可以配置课堂实验室，这样实验室中的 VM 就能在指定时间自动启动和关闭。 可以定义一次性日程安排，也可以定义定期日程安排。 有关详细信息，请参阅 [创建和管理实验室计划](how-to-create-schedules.md)。

## <a name="set-up-and-publish-a-template-vm"></a>创建并发布模板 VM
实验室中的模板是基础虚拟机映像，用户的所有虚拟机均依据其创建。 创建模板 VM，使其配置与要提供给受训学员的内容完全一致。 你可以提供实验室用户看到的模板的名称和描述。 然后发布此模板，让模板 VM 的实例可供实验室用户使用。 发布模板时，Azure 实验室服务会使用该模板在实验室中创建 VM。 在此过程中创建的 VM 数等于允许进入实验室的最大用户数，这可以在实验室的使用策略中设置。 所有虚拟机的配置都与模板相同。 有关详细信息，请参阅[创建并发布模板虚拟机](how-to-create-manage-template.md)。 

## <a name="use-vms-in-the-classroom-lab"></a>使用课堂实验室中的 VM
学生或受训学员注册实验室，并连接到 VM 来完成课程练习。 有关详细信息，请参阅[如何访问课堂实验室](how-to-use-classroom-lab.md)。

## <a name="next-steps"></a>后续步骤
按照文章： [教程：使用 Azure 实验室服务设置实验室帐户](tutorial-setup-lab-account.md)中的说明开始，在实验室中创建实验室帐户。