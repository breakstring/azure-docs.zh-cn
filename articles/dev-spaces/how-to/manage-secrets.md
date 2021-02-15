---
title: 使用 Azure Dev Space 时如何管理机密
services: azure-dev-spaces
ms.date: 12/03/2019
ms.topic: conceptual
description: 了解如何在使用 Azure Dev Spaces 开发应用程序时，在运行或生成时使用 Kubernetes 机密
keywords: Docker, Kubernetes, Azure, AKS, Azure 容器服务, 容器
ms.custom: devx-track-js
ms.openlocfilehash: 8791480f420dfd76d5291ce82e8ebf7412a41326
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91972962"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>使用 Azure Dev Space 时如何管理机密

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

你的服务可能需要适用于某些服务（例如数据库或其他安全的 Azure 服务）的特定密码、连接字符串和其他机密。 在配置文件中设置这些机密的值即可让其以环境变量的形式在代码中使用。  必须谨慎处理这些配置文件，以避免危及机密的安全性。

## <a name="storing-and-using-runtime-secrets"></a>存储和使用运行时机密

Azure Dev Spaces 提供了两个建议的简化选项，用于在 Azure Dev Spaces 客户端工具生成的 Helm 图表中存储机密：在 `values.dev.yaml` 文件中，并且直接内联 `azds.yaml` 。 不建议在中存储机密 `values.yaml` 。

> [!NOTE]
> 以下方法说明了如何存储和使用客户端工具生成的 Helm 图表的机密。 如果创建自己的 Helm 图表，则可以直接使用 Helm 图表来管理和存储机密。

### <a name="using-valuesdevyaml"></a>使用 yaml

在已准备好 Azure Dev Spaces 的项目中，在与 `values.dev.yaml` 相同的文件夹中创建文件 `azds.yaml` 以定义机密密钥和值。 例如：

```yaml
secrets:
  redis:
    port: "6380"
    host: "contosodevredis.redis.cache.windows.net"
    key: "secretkeyhere"
```

使用验证 `azds.yaml` 文件引用是否 `values.dev.yaml` 为可选 `?` 。 例如：

```yaml
install:
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
```

如果有其他机密文件，还可以在此处添加它们。

更新或验证你的服务是否引用你的机密作为环境变量。 例如：

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
使用运行更新后 `azds up` 的服务。

```console
azds up
```
 
使用 `kubectl` 验证是否已创建你的机密。

```console
kubectl get secret --namespace default -o yaml 
```

> [!IMPORTANT]
> 不建议在源代码管理中存储机密。 如果使用 Git，请将添加 `values.dev.yaml` 到 `.gitignore` 文件中，以避免在源代码管理中提交机密。

### <a name="using-azdsyaml"></a>使用 azds. yaml

在已准备好 Azure Dev Spaces 的项目中，使用 *$PLACEHOLDER*语法在 "*配置*" 下添加 "密钥和值"。在中设置。 `azds.yaml` 例如：

```yaml
configurations:
  develop:
    ...
    install:
      set:
        secrets:
          redis:
            port: "$REDIS_PORT"
            host: "$REDIS_HOST"
            key: "$REDIS_KEY"
```

> [!NOTE]
> 你可以直接输入机密值，而无需在中使用 *$PLACEHOLDER* 语法 `azds.yaml` 。 但是，不建议使用此方法，因为 `azds.yaml` 它存储在源代码管理中。
     
在与 `.env` `azds.yaml` 定义 *$PLACEHOLDER* 值相同的文件夹中创建文件。 例如：

```
REDIS_PORT=3333
REDIS_HOST=myredishost
REDIS_KEY=myrediskey
```

> [!IMPORTANT]
> 不建议在源代码管理中存储机密。 如果使用 Git，请将添加 `.env` 到 `.gitignore` 文件中，以避免在源代码管理中提交机密。

更新或验证你的服务是否引用你的机密作为环境变量。 例如：

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
使用运行更新后 `azds up` 的服务。

```console
azds up
```
 
使用 `kubectl` 验证是否已创建你的机密。

```console
kubectl get secret --namespace default -o yaml 
```

## <a name="using-secrets-as-build-arguments"></a>使用机密作为生成参数

上一部分介绍了如何在容器运行时存储和使用机密。 你还可以在容器构建时使用任何机密，如专用 NuGet 的密码，使用 `azds.yaml` 。

在中 `azds.yaml` ，使用语法在 *配置* 中设置生成时间机密。使用 `<variable name>: ${secret.<secret name>.<secret key>}` 语法。 例如：

```yaml
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
        MYTOKEN: ${secret.mynugetsecret.pattoken}
```

在上面的示例中， *mynugetsecret* 是一个现有的机密， *pattoken* 是一个现有密钥。

>[!NOTE]
> 机密名称和密钥可能包含 `.` 字符。 `\` `.` 在将机密作为生成参数传递时，使用进行转义。 例如，若要传递名为 *foo* 的机密，请使用 *令牌*的密钥： `MYTOKEN: ${secret.foo\.bar.token}` 。 此外，还可以通过前缀和后缀文本来评估机密。 例如，`MYURL: eus-${secret.foo\.bar.token}-version1`。 此外，可以将父代和祖父空间中提供的机密作为生成参数进行传递。

在 Dockerfile 中，使用 *ARG* 指令来使用机密，并稍后在 Dockerfile 中使用该相同的变量。 例如：

```dockerfile
...
ARG MYTOKEN
...
ARG NUGET_EXTERNAL_FEED_ENDPOINTS="{'endpointCredentials': [{'endpoint':'PRIVATE_NUGET_ENDPOINT', 'password':'${MYTOKEN}'}]}"
...
```

使用这些更改更新在群集中运行的服务。 在命令行上运行以下命令：

```
azds up
```

## <a name="next-steps"></a>后续步骤

通过这些方法，现在可以安全地连接到数据库、Azure Redis 缓存，或者访问安全的 Azure 服务。
 
