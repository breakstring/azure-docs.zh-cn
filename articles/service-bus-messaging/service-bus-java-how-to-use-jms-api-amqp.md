---
title: 结合使用 AMQP 和 Java 消息服务 API 和 Azure 服务总线
description: 使用 Java 消息服务 (与 Azure 服务总线的 JMS) 和高级消息队列协议 (AMQP) 1.0。
ms.topic: article
ms.date: 06/23/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 97031abaedaa3e5595e290fa0292646feb744d47
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90086685"
---
# <a name="use-the-java-message-service-with-azure-service-bus-and-amqp-10"></a>在 Azure 服务总线和 AMQP 1.0 中使用 Java 消息服务

> [!WARNING]
> 本文适用于了对 Java 消息服务 (JMS) 1.1 API 的 *有限支持* ，仅适用于 Azure 服务总线标准层。
>
> 仅在 [预览版中的 Azure 服务总线高级层](how-to-use-java-message-service-20.md)上提供对 Java 消息服务 2.0 API 的完全支持。 建议使用此层。
>

本文介绍如何通过使用常用 JMS API 标准，从 Java 应用程序使用服务总线消息传送功能。 这些消息传送功能包括队列和发布或订阅主题。 [随附文章](service-bus-amqp-dotnet.md)介绍了如何使用 Azure 服务总线 .net API 来执行相同的操作。 您可以使用这两篇文章来了解如何使用高级消息队列协议 (AMQP) 1.0 的跨平台消息传送。

AMQP 1.0 是一种高效、可靠的线级消息传递协议，可用于构建可靠的跨平台消息传送应用程序。

支持服务总线中的 AMQP 1.0 意味着可以通过使用有效的二进制协议，从一系列平台使用排队和发布或订阅中转消息传送功能。 你还可以生成由使用多种语言、框架和操作系统构建的组件组成的应用程序。

## <a name="get-started-with-service-bus"></a>服务总线入门

本文假定你已有一个包含名为的队列的服务总线命名空间 `basicqueue` 。 否则，可以使用[Azure 门户](https://portal.azure.com)[创建命名空间和队列](service-bus-create-namespace-portal.md)。 有关如何创建服务总线命名空间和队列的详细信息，请参阅[服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)。

> [!NOTE]
> 分区队列和主题也支持 AMQP。 有关详细信息，请参阅[分区消息实体](service-bus-partitioning.md)和[针对服务总线分区队列和主题的 AMQP 1.0 支持](./service-bus-amqp-protocol-guide.md)。
> 
> 

## <a name="download-the-amqp-10-jms-client-library"></a>下载 AMQP 1.0 JMS 客户端库

有关在何处下载 Apache Qpid JMS AMQP 1.0 客户端库的最新版本的信息，请参阅 [Apache Qpid 下载站点](https://qpid.apache.org/download.html)。

在通过服务总线构建和运行 JMS 应用程序时，必须将以下 JAR 文件从 Apache Qpid JMS AMQP 1.0 分发存档添加到 Java 类路径环境变量：

* geronimo-jms\_1.1\_spec-1.0.jar
* qpid-jms-client-[version].jar

> [!NOTE]
> JMS JAR 名称和版本可能已更改。 有关详细信息，请参阅 [QPID JMS AMQP 1.0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10)。

## <a name="code-java-applications"></a>代码 Java 应用程序

### <a name="java-naming-and-directory-interface"></a>Java 命名和目录接口

JMS 使用 Java 命名和目录接口 (JNDI) 创建逻辑名称和物理名称之间的分隔。 使用 JNDI 解析以下两种类型的 JMS 对象： **ConnectionFactory** 和 **Destination**。 JNDI 使用一个提供程序模型，可以在其中插入不同目录服务来处理名称解析任务。 Apache Qpid JMS AMQP 1.0 库附带了一个基于属性文件的简单 JNDI 提供程序，该提供程序是使用以下格式的属性文件配置的：

```TEXT
# servicebus.properties - sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### <a name="set-up-jndi-context-and-configure-the-connectionfactory-object"></a>设置 JNDI 上下文并配置 ConnectionFactory 对象

引用的连接字符串是在 "**主连接字符串**" 下的 " [Azure 门户](https://portal.azure.com)中的" 共享访问策略 "中可用的连接字符串。

```java
// The connection string builder is the only part of the azure-servicebus SDK library
// we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
// connection string. 
ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
// Set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");

// Look up queue
Destination queue = (Destination) context.lookup("QUEUE");
```

#### <a name="configure-producer-and-consumer-destination-queues"></a>配置制造者和使用者目标队列

用于在 Qpid 属性文件 JNDI 提供程序中定义目标的条目的格式如下所示。

为制造者创建目标队列：
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create producer
MessageProducer producer = session.createProducer(queue);
```

为使用者创建目标队列：
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create consumer
MessageConsumer consumer = session.createConsumer(queue);
```

### <a name="write-the-jms-application"></a>编写 JMS 应用程序

将 JMS 用于服务总线时不需要特殊的 Api 或选项。 稍后会介绍一些限制。 与任何 JMS 应用程序一样，首先需要配置 JNDI 环境以解析 **ConnectionFactory** 对象和目标。

#### <a name="configure-the-jndi-initialcontext-object"></a>配置 JNDI InitialContext 对象

通过将配置信息的哈希表传递到 javax.naming.InitialContext 类的构造函数中来配置 JNDI 环境。 哈希表中的两个必需元素是初始上下文工厂的类名称和提供程序 URL。 下面的代码演示如何将 JNDI 环境配置为将基于 Qpid 属性文件的 JNDI 提供程序用于名为的属性**文件。**

```java
// Set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + \
"?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);
``` 

### <a name="a-simple-jms-application-that-uses-a-service-bus-queue"></a>使用服务总线队列的简单 JMS 应用程序

以下示例程序使用 QUEUE 逻辑名称 QUEUE 将 JMS 文本消息发送到服务总线队列，并接收消息。

你可以从 [Azure 服务总线示例 JMS 队列快速入门](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client/JmsQueueQuickstart)中访问所有源代码和配置信息。

```java
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE file in the project root for full license information.

package com.microsoft.azure.servicebus.samples.jmsqueuequickstart;

import com.microsoft.azure.servicebus.primitives.ConnectionStringBuilder;
import org.apache.commons.cli.*;
import org.apache.log4j.*;

import javax.jms.*;
import javax.naming.Context;
import javax.naming.InitialContext;
import java.util.Hashtable;
import java.util.concurrent.atomic.AtomicInteger;
import java.util.function.Function;

/**
 * This sample demonstrates how to send messages from a JMS queue producer into
 * an Azure Service Bus queue and receive them with a JMS message consumer.
 * JMS queue. 
 */
public class JmsQueueQuickstart {

    // Number of messages to send
    private static int totalSend = 10;
    //Tracking counter for how many messages have been received; used as termination condition
    private static AtomicInteger totalReceived = new AtomicInteger(0);
    // log4j logger 
    private static Logger logger = Logger.getRootLogger();

    public void run(String connectionString) throws Exception {

        // The connection string builder is the only part of the azure-servicebus SDK library
        // we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
        // connection string. 
        ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
        // Set up JNDI context
        Hashtable<String, String> hashtable = new Hashtable<>();
        hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
        hashtable.put("queue.QUEUE", "BasicQueue");
        hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
        Context context = new InitialContext(hashtable);
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        
        // Look up queue
        Destination queue = (Destination) context.lookup("QUEUE");

        // We create a scope here so we can use the same set of local variables cleanly 
        // again to show the receive side separately with minimal clutter.
        {
            // Create connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            // Create session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

            // Create producer
            MessageProducer producer = session.createProducer(queue);

            // Send messages
            for (int i = 0; i < totalSend; i++) {
                BytesMessage message = session.createBytesMessage();
                message.writeBytes(String.valueOf(i).getBytes());
                producer.send(message);
                System.out.printf("Sent message %d.\n", i + 1);
            }

            producer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        {
            // Create connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            connection.start();
            // Create session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            // Create consumer
            MessageConsumer consumer = session.createConsumer(queue);
            // Create a listener callback to receive the messages
            consumer.setMessageListener(message -> {
                try {
                    // Received message is passed to callback
                    System.out.printf("Received message %d with sq#: %s\n",
                            totalReceived.incrementAndGet(), // increments the tracking counter
                            message.getJMSMessageID());
                    message.acknowledge();
                } catch (Exception e) {
                    logger.error(e);
                }
            });

            // Wait on the main thread until all sent messages have been received
            while (totalReceived.get() < totalSend) {
                Thread.sleep(1000);
            }
            consumer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        System.out.printf("Received all messages, exiting the sample.\n");
        System.out.printf("Closing queue client.\n");
    }

    public static void main(String[] args) {

        System.exit(runApp(args, (connectionString) -> {
            JmsQueueQuickstart app = new JmsQueueQuickstart();
            try {
                app.run(connectionString);
                return 0;
            } catch (Exception e) {
                System.out.printf("%s", e.toString());
                return 1;
            }
        }));
    }

    static final String SB_SAMPLES_CONNECTIONSTRING = "SB_SAMPLES_CONNECTIONSTRING";

    public static int runApp(String[] args, Function<String, Integer> run) {
        try {

            String connectionString = null;

            // Parse connection string from command line
            Options options = new Options();
            options.addOption(new Option("c", true, "Connection string"));
            CommandLineParser clp = new DefaultParser();
            CommandLine cl = clp.parse(options, args);
            if (cl.getOptionValue("c") != null) {
                connectionString = cl.getOptionValue("c");
            }

            // Get overrides from the environment
            String env = System.getenv(SB_SAMPLES_CONNECTIONSTRING);
            if (env != null) {
                connectionString = env;
            }

            if (connectionString == null) {
                HelpFormatter formatter = new HelpFormatter();
                formatter.printHelp("run jar with", "", options, "", true);
                return 2;
            }
            return run.apply(connectionString);
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
            return 3;
        }
    }
}
```

### <a name="run-the-application"></a>运行应用程序

传递共享访问策略中的“连接字符串”，以运行应用程序。
下面是运行应用程序的格式的输出：

```Output
> mvn clean package
>java -jar ./target/jmsqueuequickstart-1.0.0-jar-with-dependencies.jar -c "<CONNECTION_STRING>"

Sent message 1.
Sent message 2.
Sent message 3.
Sent message 4.
Sent message 5.
Sent message 6.
Sent message 7.
Sent message 8.
Sent message 9.
Sent message 10.
Received message 1 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-1
Received message 2 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-2
Received message 3 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-3
Received message 4 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-4
Received message 5 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-5
Received message 6 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-6
Received message 7 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-7
Received message 8 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-8
Received message 9 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-9
Received message 10 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-10
Received all messages, exiting the sample.
Closing queue client.

```

## <a name="amqp-disposition-and-service-bus-operation-mapping"></a>AMQP 处置和服务总线操作映射

下面是 AMQP 处置如何转换为服务总线操作：

```Output
ACCEPTED = 1; -> Complete()
REJECTED = 2; -> DeadLetter()
RELEASED = 3; (just unlock the message in service bus, will then get redelivered)
MODIFIED_FAILED = 4; -> Abandon() which increases delivery count
MODIFIED_FAILED_UNDELIVERABLE = 5; -> Defer()
```

## <a name="jms-topics-vs-service-bus-topics"></a>JMS 主题与 Service Bus 主题

通过 JMS API 使用服务总线主题和订阅提供了基本的发送和接收功能。 即使服务总线主题不同于 JMS 主题并需要进行少量调整，但当你使用与 JMS 兼容的 Api 从其他消息代理移植应用程序时，这是一个方便的选择。

服务总线主题将消息路由到通过 Azure 资源管理接口、Azure 命令行工具或 Azure 门户管理的已命名、共享和持久订阅。 每个订阅最多允许2000个选择规则，其中每个都有一个筛选条件，而对于 SQL 筛选器，也可以是一个元数据转换操作。 每次出现筛选器条件匹配的情况时，系统就会选择将要复制到订阅中的输入消息。  

从订阅接收消息与从队列接收消息完全相同。 每个订阅都有一个关联的死信队列，并且可以将消息自动转发给其他队列或主题。

JMS 主题允许客户端动态创建非持久和持久订阅服务器，可选择允许通过消息选择器筛选消息。 服务总线不支持这些非共享的实体。 服务总线的 SQL 筛选规则语法类似于 JMS 支持的消息选择器语法。

JMS 主题发布方与服务总线兼容（如本示例中所示），但动态订阅服务器不能。 不支持将下述与拓扑相关的 JMS API 与服务总线配合使用。

## <a name="unsupported-features-and-restrictions"></a>不受支持的功能和限制

在 AMQP 1.0 上通过服务总线使用 JMS 时存在以下限制，即：

* 每个会话只允许一个 **MessageProducer** 或 **MessageConsumer** 对象。 如果需要在应用程序中创建多个 **MessageProducer** 或 **MessageConsumer** 对象，请为每个对象创建专用会话。
* 当前不支持易失性主题订阅。
* 当前不支持**MessageSelector**对象。
* 不支持分布式事务，但支持事务处理会话。

服务总线从数据平面拆分控制平面，因此它不支持多个 JMS 动态拓扑函数。

| 不支持的方法          | 替换为                                                                             |
|-----------------------------|------------------------------------------------------------------------------------------|
| createDurableSubscriber     | 创建端口选择器端口的主题订阅。                                |
| createDurableConsumer       | 创建端口选择器端口的主题订阅。                                |
| createSharedConsumer        | 服务总线主题始终是可共享的。 请参阅 "JMS 主题和服务总线主题" 一节。                                    |
| createSharedDurableConsumer | 服务总线主题始终是可共享的。 请参阅 "JMS 主题和服务总线主题" 一节。                                      |
| createTemporaryTopic        | 通过管理 API、工具或门户创建主题，并将 *AutoDeleteOnIdle* 设置为到期期限。 |
| createTopic                 | 通过管理 API、工具或门户创建主题。                                         |
| unsubscribe                 | 删除主题 "管理 API"、"工具" 或 "门户"。                                            |
| createBrowser               | 不支持。 使用服务总线 API 的 "速览 ( # A1" 功能。                         |
| createQueue                 | 通过管理 API、工具或门户创建队列。                                           | 
| createTemporaryQueue        | 通过管理 API、工具或门户创建队列，并将 *AutoDeleteOnIdle* 设置为到期期限。 |
| receiveNoWait               | 使用服务总线 SDK 提供的 receive ( # A1 方法，并指定非常低或零的超时。 |

## <a name="summary"></a>总结

本文介绍了如何通过使用常用 JMS API 和 AMQP 1.0，通过 Java 使用服务总线中转消息传送功能，如队列和发布或订阅主题。

还可以从其他语言（如 .NET、C、Python 和 PHP）使用服务总线 AMQP 1.0。 使用这些不同语言构建的组件可以使用服务总线中的 AMQP 1.0 支持可靠且完全保真地交换消息。

## <a name="next-steps"></a>后续步骤

* [Azure 服务总线中的 AMQP 1.0 支持](service-bus-amqp-overview.md)
* [将 AMQP 1.0 与 Service Bus .NET API 配合使用](./service-bus-amqp-dotnet.md)
* [Service Bus AMQP 1.0 开发人员指南](service-bus-amqp-dotnet.md)
* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)
* [Java 开发人员中心](https://azure.microsoft.com/develop/java/)