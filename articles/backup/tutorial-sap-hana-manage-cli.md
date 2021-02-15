---
title: 教程：使用 CLI 管理已备份的 SAP HANA 数据库
description: 在本教程中，了解如何使用 Azure CLI 管理 Azure VM 上运行的已备份的 SAP HANA 数据库。
ms.topic: tutorial
ms.date: 12/4/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: cb552c5a336c3c55652936b87a668b54cfdeb41e
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99507226"
---
# <a name="tutorial-manage-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>教程：使用 Azure CLI 管理 Azure VM 中的 SAP HANA 数据库

Azure CLI 用于从命令行或通过脚本创建和管理 Azure 资源。 本文档详细介绍如何在 Azure VM 上管理已备份的 SAP HANA 数据库 - 所有这些操作都使用 Azure CLI。 还可以使用 [Azure 门户](./sap-hana-db-manage.md)执行这些步骤。

使用 [Azure Cloud Shell](tutorial-sap-hana-backup-cli.md) 运行 CLI 命令。

完成本教程后，可执行以下操作：

> [!div class="checklist"]
>
> * 监视备份和还原作业
> * 保护添加到 SAP HANA 实例的新数据库
> * 更改策略
> * 停止保护
> * 恢复保护

如果你已按照[使用 CLI 在 Azure 中备份 SAP HANA 数据库](tutorial-sap-hana-backup-cli.md)中的说明备份 SAP HANA 数据库，则要使用以下资源：

* 名为 saphanaResourceGroup 的资源组 
* 名为 saphanaVault 的保管库 
* 名为 VMAppContainer;Compute;saphanaResourceGroup;saphanaVM 的受保护容器 
* 名为 saphanadatabase;hxe;hxe 的已备份数据库/项 
* westus2 区域中的资源 

借助 Azure CLI 可轻松管理使用 Azure 备份进行备份的 Azure VM 上运行的 SAP HANA 数据库。 本教程详细介绍了每个管理操作。

## <a name="monitor-backup-and-restore-jobs"></a>监视备份和还原作业

若要监视已完成或当前正在运行的作业（备份或还原），请使用 [az backup job list](/cli/azure/backup/job#az-backup-job-list) cmdlet。 通过 CLI，还可[暂停当前正在运行的作业](/cli/azure/backup/job#az-backup-job-stop)或[等待作业完成](/cli/azure/backup/job#az-backup-job-wait)。

```azurecli-interactive
az backup job list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --output table
```

输出将如下所示：

```output
Name                                  Operation              Status      Item Name       Start Time UTC
------------------------------------  ---------------        ---------   ----------      -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup        Completed   hxe             2019-12-03T03:09:210831+00:00  
ccdb4dce-8b15-47c5-8c46-b0985352238f  Backup (Full)          Completed   hxe [hxehost]   2019-12-01T10:30:58.867489+00:00
4980af91-1090-49a6-ab96-13bc905a5282  Backup (Differential)  Completed   hxe [hxehost]   2019-12-01T10:36:00.563909+00:00
F7c68818-039f-4a0f-8d73-e0747e68a813  Restore (Log)          Completed   hxe [hxehost]   2019-12-03T05:44:51.081607+00:00
```

## <a name="change-policy"></a>更改策略

若要更改 SAP HANA 备份配置基础上的策略，请使用 [az backup policy set](/cli/azure/backup/policy#az-backup-policy-set) cmdlet。 此 cmdlet 中的 name 参数是指要更改其策略的备份项。 对于本教程，我们会将 SAP HANA 数据库 saphanadatabase;hxe;hxe 的策略替换为新策略 newsaphanaPolicy   。 可使用 [az backup policy create](/cli/azure/backup/policy#az-backup-policy-create) cmdlet 创建新策略。

```azurecli-interactive
az backup item set policy --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --policy-name newsaphanaPolicy \
    --name saphanadatabase;hxe;hxe \
```

输出应如下所示：

```output
Name                                  Resource Group
------------------------------------- --------------
cb110094-9b15-4c55-ad45-6899200eb8dd  SAPHANA
```

## <a name="create-incremental-backup-policy"></a>创建增量备份策略

若要创建增量备份策略，请使用以下参数执行 [az backup policy create](https://docs.microsoft.com/cli/azure/backup/policy#az_backup_policy_create) 命令：

* **--backup-management-type** - Azure 工作负载
* **--workload-type** - SAPHana
* **--name** - 策略的名称
* **--policy** - JSON 文件，其中包含有关计划和保留的相应详细信息
* **--resource-group** - 保管库的资源组
* **--vault-name** - 保管库的名称

例如：

```azurecli
az backup policy create --resource-group saphanaResourceGroup --vault-name saphanaVault --name sappolicy --backup-management-type AzureWorkload --policy sappolicy.json --workload-type SAPHana
```

示例 JSON (sappolicy.json) 输出：

```json
  "eTag": null,
  "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/saphanaResourceGroup/providers/Microsoft.RecoveryServices/vaults/saphanaVault/backupPolicies/sappolicy",
  "location": null,
  "name": "sappolicy",
  "properties": {
    "backupManagementType": "AzureWorkload",
    "makePolicyConsistent": null,
    "protectedItemsCount": 0,
    "settings": {
      "isCompression": false,
      "issqlcompression": false,
      "timeZone": "UTC"
    },
    "subProtectionPolicy": [
      {
        "policyType": "Full",
        "retentionPolicy": {
          "dailySchedule": null,
          "monthlySchedule": {
            "retentionDuration": {
              "count": 60,
              "durationType": "Months"
            },
            "retentionScheduleDaily": null,
            "retentionScheduleFormatType": "Weekly",
            "retentionScheduleWeekly": {
              "daysOfTheWeek": [
                "Sunday"
              ],
              "weeksOfTheMonth": [
                "First"
              ]
            },
            "retentionTimes": [
              "2021-01-19T00:30:00+00:00"
            ]
          },
          "retentionPolicyType": "LongTermRetentionPolicy",
          "weeklySchedule": {
            "daysOfTheWeek": [
              "Sunday"
            ],
            "retentionDuration": {
              "count": 104,
              "durationType": "Weeks"
            },
            "retentionTimes": [
              "2021-01-19T00:30:00+00:00"
            ]
          },
          "yearlySchedule": {
            "monthsOfYear": [
              "January"
            ],
            "retentionDuration": {
              "count": 10,
              "durationType": "Years"
            },
            "retentionScheduleDaily": null,
            "retentionScheduleFormatType": "Weekly",
            "retentionScheduleWeekly": {
              "daysOfTheWeek": [
                "Sunday"
              ],
              "weeksOfTheMonth": [
                "First"
              ]
            },
            "retentionTimes": [
              "2021-01-19T00:30:00+00:00"
            ]
          }
        },
        "schedulePolicy": {
          "schedulePolicyType": "SimpleSchedulePolicy",
          "scheduleRunDays": [
            "Sunday"
          ],
          "scheduleRunFrequency": "Weekly",
          "scheduleRunTimes": [
            "2021-01-19T00:30:00+00:00"
          ],
          "scheduleWeeklyFrequency": 0
        }
      },
      {
        "policyType": "Incremental",
        "retentionPolicy": {
          "retentionDuration": {
            "count": 30,
            "durationType": "Days"
          },
          "retentionPolicyType": "SimpleRetentionPolicy"
        },
        "schedulePolicy": {
          "schedulePolicyType": "SimpleSchedulePolicy",
          "scheduleRunDays": [
            "Monday",
            "Tuesday",
            "Wednesday",
            "Thursday",
            "Friday",
            "Saturday"
          ],
          "scheduleRunFrequency": "Weekly",
          "scheduleRunTimes": [
            "2017-03-07T02:00:00+00:00"
          ],
          "scheduleWeeklyFrequency": 0
        }
      },
      {
        "policyType": "Log",
        "retentionPolicy": {
          "retentionDuration": {
            "count": 15,
            "durationType": "Days"
          },
          "retentionPolicyType": "SimpleRetentionPolicy"
        },
        "schedulePolicy": {
          "scheduleFrequencyInMins": 120,
          "schedulePolicyType": "LogSchedulePolicy"
        }
      }
    ],
    "workLoadType": "SAPHanaDatabase"
  },
  "resourceGroup": "azurefiles",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupPolicies"
} 
```

可以修改策略的以下部分，以指定所需的备份频率和增量备份的保留时间。

例如：

```json
{
  "policyType": "Incremental",
  "retentionPolicy": {
    "retentionDuration": {
      "count": 30,
      "durationType": "Days"
    },
    "retentionPolicyType": "SimpleRetentionPolicy"
  },
  "schedulePolicy": {
    "schedulePolicyType": "SimpleSchedulePolicy",
    "scheduleRunDays": [
      "Monday",
      "Tuesday",
      "Wednesday",
      "Thursday",
      "Friday",
      "Saturday"
    ],
    "scheduleRunFrequency": "Weekly",
    "scheduleRunTimes": [
      "2017-03-07T02:00:00+00:00"
    ],
    "scheduleWeeklyFrequency": 0
  }
}
```

例如：

如果要仅在星期六进行增量备份并将其保留 60 天，请在策略中进行以下更改：

* 将 retentionDuration 计数更新为 60 天
* 仅将周六指定为 ScheduleRunDays

```json
 {
  "policyType": "Incremental",
  "retentionPolicy": {
    "retentionDuration": {
      "count": 60,
      "durationType": "Days"
    },
    "retentionPolicyType": "SimpleRetentionPolicy"
  },
  "schedulePolicy": {
    "schedulePolicyType": "SimpleSchedulePolicy",
    "scheduleRunDays": [
      "Saturday"
    ],
    "scheduleRunFrequency": "Weekly",
    "scheduleRunTimes": [
      "2017-03-07T02:00:00+00:00"
    ],
    "scheduleWeeklyFrequency": 0
  }
}
```

## <a name="protect-new-databases-added-to-an-sap-hana-instance"></a>保护添加到 SAP HANA 实例的新数据库

[使用恢复服务保管库注册 SAP HANA 实例](tutorial-sap-hana-backup-cli.md#register-and-protect-the-sap-hana-instance)会自动发现该实例上的所有数据库。

但是，如果以后将新数据库添加到 SAP HANA 实例，请使用 [az backup protectable-item initialize](/cli/azure/backup/protectable-item#az-backup-protectable-item-initialize) cmdlet。 此 cmdlet 会发现添加的新数据库。

```azurecli-interactive
az backup protectable-item initialize --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --workload-type SAPHANA
```

然后，使用 [az backup protectable-item list](/cli/azure/backup/protectable-item#az-backup-protectable-item-list) cmdlet 列出已在 SAP HANA 实例上发现的所有数据库。 但是，此列表会排除已配置备份的数据库。 发现要备份的数据库后，请参阅[在 SAP HANA 数据库上启用备份](tutorial-sap-hana-backup-cli.md#enable-backup-on-sap-hana-database)。

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

要备份的新数据库将显示在此列表中，如下所示：

```output
Name                            Protectable Item Type    ParentName    ServerName    IsProtected
---------------------------     ----------------------   ------------  -----------   ------------
saphanasystem;hxe               SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb    SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;newhxe      SAPHanaDatabase          HXE           hxehost       NotProtected
```

## <a name="stop-protection-for-an-sap-hana-database"></a>停止对 SAP HANA 数据库的保护

可以通过以下几种方式停止保护 SAP HANA 数据库：

* 停止所有将来的备份作业并删除所有恢复点。
* 停止所有将来的备份作业，且恢复点保留不变。

如果选择保留恢复点，请记住以下详细内容：

* 所有恢复点都将永久保持不变，所有删除操作都将在停止保护时停止，并保留数据。
* 你将为受保护的实例和使用的存储付费。
* 如果在不停止备份的情况下删除数据源，则新备份会失败。

让我们进一步了解停止保护的方法。

### <a name="stop-protection-with-retain-data"></a>停止保护并保留数据

若要停止保护并保留数据，请使用 [az backup protection disable](/cli/azure/backup/protection#az-backup-protection-disable) cmdlet。

```azurecli-interactive
az backup protection disable --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --workload-type SAPHANA \
    --output table
```

输出应如下所示：

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
g0f15dae-7cac-4475-d833-f52c50e5b6c3  saphanaResourceGroup
```

若要检查此操作的状态，请使用 [az backup job show](/cli/azure/backup/job#az-backup-job-show) cmdlet。

### <a name="stop-protection-without-retain-data"></a>停止保护且不保留数据

若要停止保护且不保留数据，请使用 [az backup protection disable](/cli/azure/backup/protection#az-backup-protection-disable) cmdlet。

```azurecli-interactive
az backup protection disable --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --workload-type SAPHANA \
    --delete-backup-data true \
    --output table
```

输出应如下所示：

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
g0f15dae-7cac-4475-d833-f52c50e5b6c3  saphanaResourceGroup
```

若要检查此操作的状态，请使用 [az backup job show](/cli/azure/backup/job#az-backup-job-show) cmdlet。

## <a name="resume-protection"></a>恢复保护

如果停止对 SAP HANA 数据库的保护且保留数据，可在稍后恢复保护。 如果不保留备份的数据，则无法恢复保护。

若要恢复保护，请使用 [az backup protection resume](/cli/azure/backup/protection#az-backup-protection-resume) cmdlet。

```azurecli-interactive
az backup protection resume --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --policy-name saphanaPolicy \
    --output table
```

输出应如下所示：

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
b2a7f108-1020-4529-870f-6c4c43e2bb9e  saphanaResourceGroup
```

若要检查此操作的状态，请使用 [az backup job show](/cli/azure/backup/job#az-backup-job-show) cmdlet。

## <a name="next-steps"></a>后续步骤

* 若要了解如何使用 Azure 门户备份 Azure VM 上运行的 SAP HANA 数据库，请参阅[在 Azure VM 上备份 SAP HANA 数据库](./backup-azure-sap-hana-database.md)

* 若要了解如何使用 Azure 门户管理 Azure VM 上运行的已备份 SAP HANA 数据库，请参阅[在 Azure VM 上管理备份的 SAP HANA 数据库](./sap-hana-db-manage.md)
