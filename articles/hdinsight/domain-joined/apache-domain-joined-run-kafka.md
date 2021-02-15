---
title: 教程 - Apache Kafka 和企业安全性 - Azure HDInsight
description: 教程 - 了解如何使用企业安全性套餐为 Azure HDInsight 中的 Kafka 配置 Apache Ranger 策略。
ms.service: hdinsight
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: 552998de23bebd98f56ba28eb6ad581689e52e41
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98933692"
---
# <a name="tutorial-configure-apache-kafka-policies-in-hdinsight-with-enterprise-security-package-preview"></a>教程：使用企业安全性套餐（预览版）在 HDInsight 中配置 Apache Kafka 策略

了解如何为企业安全性套餐 (ESP) Apache Kafka 群集配置 Apache Ranger 策略。 将 ESP 群集连接到域，可允许用户使用域凭据进行身份验证。 本教程将创建两个 Ranger 策略来限制对 `sales` 和 `marketingspend` 主题的访问。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建域用户
> * 创建 Ranger 策略
> * 创建 Kafka 群集中的主题
> * 测试 Ranger 策略

## <a name="prerequisite"></a>先决条件

[具有企业安全性套餐的 HDInsight Kafka 群集](./apache-domain-joined-configure-using-azure-adds.md)。

## <a name="connect-to-apache-ranger-admin-ui"></a>连接到 Apache Ranger 管理 UI

1. 在浏览器中，使用 URL `https://ClusterName.azurehdinsight.net/Ranger/` 连接到 Ranger 管理用户界面。 请记住将 `ClusterName` 更改为 Kafka 群集的名称。 Ranger 凭据与 Hadoop 集群凭据不同。 若要防止浏览器使用缓存的 Hadoop 凭据，请使用新的 InPrivate 浏览器窗口连接到 Ranger 管理 UI。

2. 使用 Azure Active Directory (AD) 管理员凭据登录。 Azure AD 管理员凭据与 HDInsight 群集凭据或 Linux HDInsight 节点 SSH 凭据不同。

   ![HDInsight Apache Ranger 管理 UI](./media/apache-domain-joined-run-kafka/apache-ranger-admin-login.png)

## <a name="create-domain-users"></a>创建域用户

访问[使用企业安全性套餐创建 HDInsight 群集](./apache-domain-joined-configure-using-azure-adds.md)，以了解如何创建 sales_user 和 marketing_user 域用户。 在生产方案中，域用户来自你的 Active Directory·租户。

## <a name="create-ranger-policy"></a>创建 Ranger 策略

为 sales_user 和 marketing_user 创建 Ranger 策略。

1. 打开“Ranger 管理 UI”。

2. 在“Kafka”下选择“\<ClusterName>_kafka” 。 可以列出一个预先配置的策略。

3. 选择“添加新策略”，并输入以下值：

   |设置  |建议的值  |
   |---------|---------|
   |策略名称  |  hdi sales* 策略   |
   |主题   |  sales* |
   |选择用户  |  sales_user1 |
   |权限  | 发布、使用、创建 |

   主题名称中可以包含以下通配符：

   * “*”表示字符出现零次或多次。
   * “?”表示单个字符。

   ![Apache Ranger 管理 UI 创建策略 1](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy.png)

   如果“选择用户”中未自动填充域用户，请等待片刻时间让 Ranger 与 Azure AD 同步。

4. 选择“添加”以保存策略。

5. 选择“添加新策略”，然后输入以下值：

   |设置  |建议的值  |
   |---------|---------|
   |策略名称  |  hdi marketing 策略   |
   |主题   |  marketingspend |
   |选择用户  |  marketing_user1 |
   |权限  | 发布、使用、创建 |

   ![Apache Ranger 管理 UI 创建策略 2](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy-2.png)  

6. 选择“添加”以保存策略。

## <a name="create-topics-in-a-kafka-cluster-with-esp"></a>使用 ESP 创建 Kafka 群集中的主题

若要创建两个主题（`salesevents` 和 `marketingspend`），请执行以下操作：

1. 使用以下命令与群集建立 SSH 连接：

   ```cmd
   ssh DOMAINADMIN@CLUSTERNAME-ssh.azurehdinsight.net
   ```

   将 `DOMAINADMIN` 替换为在[创建群集](./apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)期间为你的群集配置的管理员用户，将 `CLUSTERNAME` 替换为你的群集的名称。 出现提示时，输入管理员用户帐户的密码。 有关在 HDInsight 中使用 `SSH` 的详细信息，请参阅[在 HDInsight 中使用 SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)。

2. 使用以下命令将群集名称保存到一个变量中并安装 JSON 分析实用工具 (`jq`)。 出现提示时，请输入 Kafka 群集名称。

   ```bash
   sudo apt -y install jq
   read -p 'Enter your Kafka cluster name:' CLUSTERNAME
   ```

3. 使用以下命令获取 Kafka 代理主机。 出现提示时，输入群集管理帐户的密码。

   ```bash
   export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
   ```

   在继续操作之前，可能需要设置开发环境（如果尚未这样做）。 需要 Java JDK、Apache Maven 以及包含 scp 的 SSH 客户端等组件。 有关详细信息，请参阅[设置说明](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer)。

1. 下载 [Apache Kafka 域加入生成者使用者示例](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer)。

1. 按照以下文章的 **生成并部署示例** 下的步骤 2 和步骤 3 进行操作：[教程：使用 Apache Kafka 生成者和使用者 API](../kafka/apache-kafka-producer-consumer-api.md#build-and-deploy-the-example)

1. 运行以下命令：

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/conf/kafka_client_jaas.conf kafka-producer-consumer.jar create salesevents $KAFKABROKERS
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/conf/kafka_client_jaas.conf kafka-producer-consumer.jar create marketingspend $KAFKABROKERS
   ```

## <a name="test-the-ranger-policies"></a>测试 Ranger 策略

根据所配置的 Ranger 策略，**sales_user** 可以生成/使用主题 `salesevents`，但不能生成/使用主题 `marketingspend`。 相反，**marketing_user** 可以生成/使用主题 `marketingspend`，但不能生成/使用主题 `salesevents`。

1. 打开与群集的新 SSH 连接。 使用以下命令来以 sales_user1 身份登录：

   ```bash
   ssh sales_user1@CLUSTERNAME-ssh.azurehdinsight.net
   ```

2. 使用上一部分中的代理名称来设置以下环境变量：

   ```bash
   export KAFKABROKERS=<brokerlist>:9092
   ```

   示例： `export KAFKABROKERS=wn0-khdicl.contoso.com:9092,wn1-khdicl.contoso.com:9092`

3. 按照以下文章的 **生成并部署示例** 下的步骤 3 进行操作：[教程：使用 Apache Kafka 生成者和使用者 API](../kafka/apache-kafka-producer-consumer-api.md#build-and-deploy-the-example) 来确保 `kafka-producer-consumer.jar` 也可供 **sales_user** 使用。

   > [!NOTE]  
   > 对于本教程，请使用“DomainJoined-Producer-Consumer”项目下的 kafka-producer-consumer.jar（而不是 Producer-Consumer 项目下的那个文件，它适用于非加入域方案）。

4. 执行以下命令来验证 **sales_user1** 可以生成主题 `salesevents`：

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/conf/kafka_client_jaas.conf kafka-producer-consumer.jar producer salesevents $KAFKABROKERS
   ```

5. 执行以下命令来使用主题 `salesevents`：

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/conf/kafka_client_jaas.conf kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   验证你能够读取消息。

6. 在同一 ssh 窗口中执行以下命令来验证 **sales_user1** 无法生成主题 `marketingspend`：

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/conf/kafka_client_jaas.conf kafka-producer-consumer.jar producer marketingspend $KAFKABROKERS
   ```

   发生授权错误，但可以忽略。

7. 请注意，**marketing_user1** 无法使用主题 `salesevents`。

   重复上面的步骤 1-3，但这次以 marketing_user1 身份登录。

   执行以下命令来使用主题 `salesevents`：

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/conf/kafka_client_jaas.conf kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   无法查看以前的消息。

8. 从 Ranger UI 查看审核访问事件。

   ![Ranger UI 策略审核访问事件 ](./media/apache-domain-joined-run-kafka/apache-ranger-admin-audit.png)
   
## <a name="produce-and-consume-topics-in-esp-kafka-by-using-the-console"></a>使用控制台生成并使用 ESP Kafka 中的主题

> [!NOTE]
> 不能使用控制台命令来创建主题。 相反，必须使用上一部分中演示的 Java 代码。 有关详细信息，请参阅[使用 ESP 创建 Kafka 群集中的主题](#create-topics-in-a-kafka-cluster-with-esp)。

若要使用控制台生成和使用 ESP Kafka 中的主题，请执行以下操作：

1. 将 `kinit` 与用户的用户名一起使用。 出现提示时，输入密码。

   ```bash
   kinit sales_user1
   ```

2. 设置环境变量：

   ```bash
   export KAFKA_OPTS="-Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/conf/kafka_client_jaas.conf"
   export KAFKABROKERS=<brokerlist>:9092
   ```

3. 生成 `salesevents` 主题的消息：

   ```bash
   /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --topic salesevents --broker-list $KAFKABROKERS --security-protocol SASL_PLAINTEXT
   ```

4. 使用主题 `salesevents` 中的消息：

   ```bash
   /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --topic salesevents --from-beginning --bootstrap-server $KAFKABROKERS --security-protocol SASL_PLAINTEXT
   ```

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此应用程序，请使用以下步骤删除创建的 Kafka 群集：

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 在顶部的“搜索”框中，键入 **HDInsight**。
1. 选择“服务”下的“HDInsight 群集” 。
1. 在显示的 HDInsight 群集列表中，单击为本教程创建的群集旁边的“...”。 
1. 单击 **“删除”** 。 单击 **“是”** 。

## <a name="troubleshooting"></a>疑难解答
如果 kafka-producer-consumer.jar 在已加入域的群集中不起作用，请确保使用“DomainJoined-Producer-Consumer”项目下的 kafka-producer-consumer.jar（而不是 Producer-Consumer 项目下的那个文件，它适用于非加入域方案）。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [客户管理的密钥磁盘加密](../disk-encryption.md)
