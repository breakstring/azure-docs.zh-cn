---
title: 在 Azure Monitor 中使用适用于 Linux 的 Log Analytics 代理收集自定义 JSON 数据源
description: 可以使用适用于 Linux 的 Log Analytics 代理将自定义 JSON 数据源收集到 Azure Monitor 中。  这些自定义数据源可以是返回 JSON 的简单脚本，例如 curl 或 FluentD 的 300 多个插件之一。 本文介绍此数据收集所需的配置。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: aa6931fc91838173a856ef500145fa49f2606271
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2020
ms.locfileid: "97655197"
---
# <a name="collecting-custom-json-data-sources-with-the-log-analytics-agent-for-linux-in-azure-monitor"></a>在 Azure Monitor 中使用适用于 Linux 的 Log Analytics 代理收集自定义 JSON 数据源
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

可以使用适用于 Linux 的 Log Analytics 代理将自定义 JSON 数据源收集到 [Azure Monitor](data-platform.md) 中。  这些自定义数据源可以是返回 JSON 的简单脚本，例如 [curl](https://curl.haxx.se/) 或 [FluentD 的 300 多个插件](https://www.fluentd.org/plugins/all)之一。 本文介绍此数据收集所需的配置。


> [!NOTE]
> Log Analytics Linux v1.1.0-217+ 代理是自定义 JSON 数据所必需的

## <a name="configuration"></a>配置

### <a name="configure-input-plugin"></a>配置输入插件

要在 Azure Monitor 中收集 JSON 数据，请将 `oms.api.` 添加到输入插件中 FluentD 标记的起始位置。

例如，下面是 `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` 中一个单独的配置文件 `exec-json.conf`。  此配置文件使用 FluentD 插件 `exec` 每隔 30 秒运行一个 curl 命令。  此命令的输出由 JSON 输出插件收集。

```xml
<source>
  type exec
  command 'curl localhost/json.output'
  format json
  tag oms.api.httpresponse
  run_interval 30s
</source>

<match oms.api.httpresponse>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api_httpresponse*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```

在 `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` 下添加的配置文件需要使用以下命令更改其所有权。

`sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/exec-json.conf`

### <a name="configure-output-plugin"></a>配置输出插件 
将下面的输出插件配置添加到 `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` 中的主配置，或者作为单独的配置文件放置在 `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` 中

```xml
<match oms.api.**>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```

### <a name="restart-log-analytics-agent-for-linux"></a>重新启动 Log Analytics Linux 代理
使用以下命令重启 Log Analytics Linux 代理服务。

```console
sudo /opt/microsoft/omsagent/bin/service_control restart 
```

## <a name="output"></a>输出
数据将以记录类型 `<FLUENTD_TAG>_CL` 收集到 Azure Monitor 中。

例如，Azure Monitor 中具有 `tomcat_CL` 记录类型的自定义标记 `tag oms.api.tomcat`。  可以使用以下日志查询检索此类型的所有记录。

```console
Type=tomcat_CL
```

支持嵌套 JSON 数据源，但基于父字段编制索引。 例如，下面的 JSON 数据是以 `tag_s : "[{ "a":"1", "b":"2" }]` 的形式从日志查询返回的。

```json
{
    "tag": [{
      "a":"1",
      "b":"2"
    }]
}
```


## <a name="next-steps"></a>后续步骤
* 了解[日志查询](../log-query/log-query-overview.md)以便分析从数据源和解决方案中收集的数据。 
