---
title: include 文件
description: include 文件
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: e7968f4ab01706aa5f8d7d016d93a1b9de2e74b6
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96325278"
---
## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Java 开发工具包 (JDK)](/java/azure/jdk/?preserve-view=true&view=azure-java-stable) 8 或更高版本。
- [Apache Maven](https://maven.apache.org/download.cgi)。
- 已部署的通信服务资源和连接字符串。 [创建通信服务资源](../create-communication-resource.md)。

## <a name="setting-up"></a>设置

### <a name="create-a-new-java-application"></a>创建新的 Java 应用程序

打开终端或命令窗口。 导航到要在其中创建 Java 应用程序的目录。 运行以下命令以从 maven-archetype-quickstart 模板生成 Java 项目。

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

你会注意到，“生成”任务创建了与 `artifactId` 名称相同的目录。 在此目录下，src/main/java 目录包含项目源代码，`src/test/java directory` 包含测试源，`pom.xml` 文件是项目的项目对象模型 (POM)。

### <a name="install-the-package"></a>安装包

在文本编辑器中打开 pom.xml 文件。 将以下依赖项元素添加到依赖项组。

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-administration</artifactId>
    <version>1.0.0-beta.3</version> 
</dependency>
```

### <a name="set-up-the-app-framework"></a>设置应用框架

从项目目录中执行以下操作：

1. 导航到 /src/main/java/com/communication/quickstart 目录
1. 在编辑器中打开 App.java 文件
1. 替换 `System.out.println("Hello world!");` 语句
1. 添加 `import` 指令

使用以下代码以开始执行以下操作：

```java
import com.azure.communication.administration.*;
import com.azure.communication.common.*;
import java.io.*;
import java.util.*;
import java.time.*;

import com.azure.core.http.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Access Tokens Quickstart");
        // Quickstart code goes here
    }
}
```

## <a name="authenticate-the-client"></a>验证客户端

使用资源的访问密钥和终结点将 `CommunicationIdentityClient` 实例化。 了解如何[管理资源的连接字符串](../create-communication-resource.md#store-your-connection-string)。

将以下代码添加到 `main` 方法中：

```java
// Your can find your endpoint and access key from your resource in the Azure Portal
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";
String accessKey = "SECRET";

// Create an HttpClient builder of your choice and customize it
// Use com.azure.core.http.netty.NettyAsyncHttpClientBuilder if that suits your needs
// -> Add "import com.azure.core.http.netty.*;"
// -> Add azure-core-http-netty dependency to file pom.xml

HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
    .endpoint(endpoint)
    .accessKey(accessKey)
    .httpClient(httpClient)
    .buildClient();
```

可以用任何实现 `com.azure.core.http.HttpClient` 接口的自定义 HTTP 客户端来初始化客户端。 上面的代码演示了如何使用 `azure-core` 提供的 [Azure Core Netty HTTP 客户端](/java/api/overview/azure/core-http-netty-readme?preserve-view=true&view=azure-java-stable)。

你还可以使用 connectionString() 函数提供整个连接字符串，而不是提供终结点和访问密钥。 
```java
// Your can find your connection string from your resource in the Azure Portal
String connectionString = "<connection_string>";
CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
    .connectionString(connectionString)
    .httpClient(httpClient)
    .buildClient();
```

## <a name="create-an-identity"></a>创建标识

Azure 通信服务维护轻量级标识目录。 使用 `createUser` 方法可在目录中创建具有唯一 `Id` 的新项。 存储收到的标识，并映射到应用程序的用户。 例如，将它们存储在应用程序服务器的数据库中。 稍后颁发访问令牌时需要该标识。

```java
CommunicationUser identity = communicationIdentityClient.createUser();
System.out.println("\nCreated an identity with ID: " + identity.getId());
```

## <a name="issue-access-tokens"></a>颁发访问令牌

使用 `issueToken` 方法为已存在的通信服务标识颁发访问令牌。 参数 `scopes` 定义一组基元，用于授权此访问令牌。 请参阅[受支持的操作列表](../../concepts/authentication.md)。 参数 `user` 的新实例可以基于 Azure 通信服务标识的字符串表示形式构造。

```java
// Issue an access token with the "voip" scope for an identity
List<String> scopes = new ArrayList<>(Arrays.asList("voip"));
CommunicationUserToken response = communicationIdentityClient.issueToken(identity, scopes);
OffsetDateTime expiresOn = response.getExpiresOn();
String token = response.getToken();
System.out.println("\nIssued an access token with 'voip' scope that expires at: " + expiresOn + ": " + token);
```

访问令牌是短期凭据，需要重新颁发。 如果不重新颁发，可能会导致应用程序用户的体验中断。 `expiresAt` 响应属性指示访问令牌的生存期。

## <a name="refresh-access-tokens"></a>刷新访问令牌

若要刷新访问令牌，请使用 `CommunicationUser` 对象重新颁发：

```java  
// Value existingIdentity represents identity of Azure Communication Services stored during identity creation
CommunicationUser identity = new CommunicationUser(existingIdentity);
response = communicationIdentityClient.issueToken(identity, scopes);
```

## <a name="revoke-access-tokens"></a>撤销访问令牌

在某些情况下可能会显式撤销访问令牌。 例如，当应用程序的用户更改在向服务进行身份验证时所使用的密码时。 `revokeTokens` 方法使颁发给标识的所有活动访问令牌无效。

```java  
communicationIdentityClient.revokeTokens(identity, OffsetDateTime.now());
System.out.println("\nSuccessfully revoked all access tokens for identity with ID: " + identity.getId());
```

## <a name="delete-an-identity"></a>删除标识

删除标识将撤销所有活动访问令牌，并阻止你为标识颁发访问令牌。 它还会删除与标识关联的所有保存的内容。

```java
communicationIdentityClient.deleteUser(identity);
System.out.println("\nDeleted the identity with ID: " + identity.getId());
```

## <a name="run-the-code"></a>运行代码

导航到包含 pom.xml 文件的目录，并使用以下 `mvn` 命令编译该项目。

```console
mvn compile
```

然后，生成包。

```console
mvn package
```

运行以下 `mvn` 命令以执行应用。

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```