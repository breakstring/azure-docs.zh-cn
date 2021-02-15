---
title: Linux VM 的应用程序一致性备份
description: 创建 Linux 虚拟机到 Azure 的应用程序一致性备份。 本文介绍如何配置脚本框架以备份 Azure 部署的 Linux VM。 本文还包括故障排除信息。
ms.topic: conceptual
ms.date: 01/12/2018
ms.openlocfilehash: 22053004026a2dd8976027359f11d50a5663b334
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88999234"
---
# <a name="application-consistent-backup-of-azure-linux-vms"></a>Azure Linux VM 的应用程序一致性备份

创建 VM 的备份快照时，应用程序一致性意味着 VM 在还原后启动时启动应用程序。 正如你所想象，应用程序一致性非常重要。 若要确保 Linux VM 是应用程序一致的，可以使用 Linux 前脚本和后脚本框架执行应用程序一致性备份。 前脚本和后脚本框架支持 Azure 资源管理器部署的 Linux 虚拟机。 应用程序一致性脚本不支持 Service Manager 部署的虚拟机或 Windows 虚拟机。

## <a name="how-the-framework-works"></a>框架工作原理

该框架提供一个选项，用于在创建 VM 快照时运行自定义的操作前脚本和操作后脚本。 前脚本在创建 VM 快照前的那一刻运行，后脚本在创建 VM 快照后立即运行。 使用前脚本和后脚本可在创建 VM 快照时灵活控制应用程序和环境。

前脚本可以调用本机应用程序 API 来使 IO 处于静默状态并将内存中内容刷新到磁盘。 这些操作可确保快照是应用程序一致的。 后脚本使用本机应用程序 API 来解冻 IO，使应用程序能够在创建 VM 快照后恢复正常操作。

## <a name="steps-to-configure-pre-script-and-post-script"></a>配置操作前脚本和操作后脚本的步骤

1. 以 root 用户身份登录到要备份的 Linux VM。

2. 从 [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) 下载 **VMSnapshotScriptPluginConfig.json**，并将其复制到要备份的所有 VM 的 **/etc/azure** 文件夹。 如果 **/etc/azure** 文件夹不存在，则创建它。

3. 将应用程序的前脚本和后脚本复制到计划备份的所有 VM 上。 可将脚本复制到 VM 上的任意位置。 确保在 VMSnapshotScriptPluginConfig.json 文件中更新脚本文件的完整路径。 

4. 确保分配对这些文件的以下权限：

   - **VMSnapshotScriptPluginConfig.json**：权限“600”。 例如，只有“root”用户才对此文件拥有“读取”和“写入”权限，任何用户都不得拥有其“执行”权限。

   - **操作前脚本文件：** 权限“700”。  例如，只有“root”用户才对此文件拥有“读取”、“写入”和“执行”权限。

   - 操作后脚本：权限“700”。  例如，只有“root”用户才对此文件拥有“读取”、“写入”和“执行”权限。

   > [!IMPORTANT]
   > 该框架为用户提供强大功能。 保护该框架，并确保只有“root”用户可以访问关键 JSON 和脚本文件。
   > 如果不符合要求，该脚本将不会运行，从而导致文件系统崩溃和生成不一致性备份。
   >

5. 根据下述信息配置 VMSnapshotScriptPluginConfig.json： 
    - **pluginName**：将此字段保留原样，否则脚本可能无法按预期工作。

    - **preScriptLocation**：提供要备份的 VM 上的操作前脚本的完整路径。

    - **postScriptLocation**：提供要备份的 VM 上的操作后脚本的完整路径。

    - **preScriptParams**：提供需传递给操作前脚本的可选参数。 所有参数都应当用引号引起来。 如果使用多个参数，请用逗号分隔参数。

    - **postScriptParams**：提供需传递给操作后脚本的可选参数。 所有参数都应当用引号引起来。 如果使用多个参数，请用逗号分隔参数。

    - **preScriptNoOfRetries**：设置在终止之前出现任何错误时应重试操作前脚本的次数。 如果为0，则表示只尝试一次，不重试。

    - **postScriptNoOfRetries**：设置在终止后出现任何错误时应重试操作后脚本的次数。 如果为0，则表示只尝试一次，不重试。

    - **timeoutInSeconds**：指定操作前脚本和操作后脚本的单次超时（最大值可以为 1800）。

    - **continueBackupOnFailure**：如果希望在操作前脚本或操作后脚本失败时 Azure 备份故障回复到文件系统/崩溃一致性备份，请将此值设置为 true。  如果将此选项设置为 " **false** "，则在出现脚本 (失败时，备份将失败，除非你有一个单磁盘 VM，该 VM 将回退到崩溃一致的备份，而不考虑此设置) 。 当 **continueBackupOnFailure** 值设置为 false 时，如果备份失败，则将根据服务 (中的重试逻辑尝试) 的尝试次数。

    - fsFreezeEnabled：指定为了确保文件系统一致性，在创建 VM 快照时是否应调用 Linux fsfreeze。**** 建议将此设置保留为 true，除非必须禁用 fsfreeze 才能让应用程序正常工作。****

    - **ScriptsExecutionPollTimeSeconds**：设置扩展在每次轮询到脚本执行之间必须休眠的时间。 例如，如果值为 2，则扩展会每 2 秒检查一次前/后脚本执行是否完成。 它可以采用的最小值和最大值分别为 1 和 5。 该值应严格为一个整数。

6. 现已配置脚本框架。 如果已配置 VM 备份，则下一次备份将调用这些脚本，并触发应用程序一致性备份。 如果未配置 VM 备份，请使用将[Azure 虚拟机备份到恢复服务保管库](./backup-azure-vms-first-look-arm.md)进行配置。

## <a name="troubleshooting"></a>疑难解答

编写操作前脚本和操作后脚本时，请确保添加相应的日志记录，并查看脚本日志来解决任何脚本问题。 如果运行脚本时仍然遇到问题，请参阅下表中的详细信息。

| 错误 | 错误消息 | 建议的操作 |
| ------------------------ | -------------- | ------------------ |
| Pre-ScriptExecutionFailed |操作前脚本返回错误，因此备份可能不具有应用程序一致性。| 查看脚本的失败日志来解决问题。|  
|Post-ScriptExecutionFailed |操作后脚本返回了可能影响应用程序状态的错误。 |查看脚本的失败日志来解决问题，并检查应用程序状态。 |
| Pre-ScriptNotFound |在 VMSnapshotScriptPluginConfig.json 配置文件中指定的位置找不到操作前脚本。**** |确保配置文件中指定的路径处存在操作前脚本，以保证应用程序一致性备份。|
| Post-ScriptNotFound |在 VMSnapshotScriptPluginConfig.json 配置文件中指定的位置找不到操作后脚本。**** |确保配置文件中指定的路径处存在操作后脚本，以保证应用程序一致性备份。|
| IncorrectPluginhostFile |VmSnapshotLinux 扩展随附的 Pluginhost 文件已损坏，因此操作前脚本和操作后脚本无法运行，且不会创建应用程序一致性的备份。****| 卸载 VmSnapshotLinux 扩展，下一次备份时会自动重新安装它，这样即可解决问题。**** |
| IncorrectJSONConfigFile | VMSnapshotScriptPluginConfig.json 文件不正确，因此操作前脚本和操作后脚本无法运行，且不会创建应用程序一致的备份。**** | 从 [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) 下载副本并重新配置该文件。 |
| InsufficientPermissionforPre-Script | 对于正在运行的脚本，“root”用户应是该文件的所有者，并且应对文件设置“700”权限（即只有“所有者”才拥有“读取”、“写入”和“执行”权限）。 | 确保“root”用户是脚本文件的“所有者”，只有“所有者”才拥有“读取”、“写入”和“执行”权限。 |
| InsufficientPermissionforPost-Script | 对于正在运行的脚本，root 用户应是该文件的所有者，并且应该对文件设置“700”权限（即，只有“所有者”才拥有“读取”、“写入”和“执行”权限）。 | 确保“root”用户是脚本文件的“所有者”，只有“所有者”才拥有“读取”、“写入”和“执行”权限。 |
| Pre-ScriptTimeout | 执行应用程序一致性备份时操作前脚本超时。 | 检查脚本，并在 /etc/azure 中的 VMSnapshotScriptPluginConfig.json 文件中增加超时时间。******** |
| Post-ScriptTimeout | 执行应用程序一致性备份时操作后脚本超时。 | 检查脚本，并在 /etc/azure 中的 VMSnapshotScriptPluginConfig.json 文件中增加超时时间。******** |

## <a name="next-steps"></a>后续步骤

[配置 VM 备份到恢复服务保管库](./backup-azure-vms-first-look-arm.md)
