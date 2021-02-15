---
title: Azure Database for MySQL 的证书轮换
description: 了解即将推出的会影响 Azure Database for MySQL 的根证书更改
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2021
ms.openlocfilehash: a65ac8d52c17a288447193fb8c0fba2c6e6c5554
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201257"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-mysql"></a>了解 Azure Database for MySQL 的根 CA 更改中的更改

Azure Database for MySQL 将更改通过 SSL 启用的客户端应用程序/驱动程序的根证书，该证书用于[连接到数据库服务器](concepts-connectivity-architecture.md)。 根据标准维护和安全最佳做法的要求，当前可用的根证书已设置为在 2021 年 2 月 15 日 (2021/02/15) 过期。 本文更详细地介绍了即将推出的更改、会受影响的资源，以及确保应用程序始终与数据库服务器连接所需执行的步骤。

>[!NOTE]
> 根据客户的反馈，我们已扩展了2020年2月15日到到2021的现有巴尔的摩根 CA 的根证书弃用。 如果用户受到影响，我们希望此次延长能为他们提供足够的提前期来实施客户端更改。

> [!NOTE]
> 无偏差通信
>
> Microsoft 支持多样化的包容性环境。 本文包含对关键字 _master_ 和 _从属_ 的引用。 [用于偏置通信的 Microsoft 风格指南](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md)识别为 exclusionary 词。 本文中使用的词是为了保持一致，因为它们目前是软件中出现的单词。 当软件更新为删除字词时，本文将更新为对齐。
>

## <a name="what-update-is-going-to-happen"></a>打算进行什么样的更新？

在某些情况下，应用程序使用基于受信任的证书颁发机构 (CA) 证书文件生成的本地证书文件才能安全地进行连接。 目前，客户只能使用预定义的证书来连接位于[此处](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)的 Azure Database for MySQL 服务器。 但是，[证书颁发机构 (CA) 浏览器论坛](https://cabforum.org/) 最近发布的报告表明，由 CA 供应商颁发的多个证书不符合标准。

根据行业的合规性要求，CA 供应商已开始吊销不符合标准的 CA 的 CA 证书，要求服务器使用由符合标准的 CA 颁发的证书，并由这些符合标准的 CA 颁发的 CA 证书进行签名。 由于 Azure Database for MySQL 当前使用其中一个不符合的证书，客户端应用程序使用这些证书来验证其 SSL 连接，因此，我们需要确保在本主题后面的部分中将相应的操作 () ，以最大程度地降低对 MySQL 服务器的潜在影响。

新证书将从 2021 年 2 月 15 日 (2021/02/15) 开始使用。 如果在从 MySQL 客户端进行连接时使用 CA 验证或完全验证服务器证书 (sslmode = verify-CA 或 sslmode = verify-full) ，则需要在2021年2月15日之前更新应用程序配置 (03/15/2021) 。

## <a name="how-do-i-know-if-my-database-is-going-to-be-affected"></a>如何知道我的数据库是否会受影响？

使用 SSL/TLS 并验证根证书的所有应用程序都需要更新根证书。 通过查看连接字符串，可以确定连接是否验证根证书。

* 如果你的连接字符串包含 `sslmode=verify-ca` 或 `sslmode=verify-identity`，则你需要更新证书。
* 如果连接字符串包括 `sslmode=disable` 、 `sslmode=allow` 、 `sslmode=prefer` 或 `sslmode=require` ，则无需更新证书。
* 如果使用 Java 连接器，并且连接字符串包含 useSSL = false 或 requireSSL = false，则无需更新证书。
* 如果连接字符串未指定 sslmode，则无需更新证书。

如果你使用的是将连接字符串抽象化的客户端，请查看客户端的文档，以了解其是否验证证书。
要了解 Azure Database for MySQL sslmode，请查看 [SSL 模式说明](concepts-ssl-connection-security.md#ssl-default-settings)。

若要避免应用程序的可用性因为证书被意外撤销而被中断，或要更新已吊销的证书，请参阅 [**"维护连接需要执行哪些操作"**](concepts-certificate-rotation.md#what-do-i-need-to-do-to-maintain-connectivity) 部分。

## <a name="what-do-i-need-to-do-to-maintain-connectivity"></a>我需要做什么来维护连接

若要避免应用程序因意外撤销证书而被中断，或要更新已吊销的证书，请使用以下步骤。 其思路是创建一个新的 *pem* 文件，该文件结合当前证书和新证书，在 SSL 证书验证期间，将使用其中一个允许值。 请参阅以下步骤：

* 从以下链接下载 Baltimorecybertrustroot.crt.pem & DigiCertGlobalRootG2 根 CA：

  * [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)
  * [https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)

* 生成组合的 CA 证书存储，其中同时包含 **BaltimoreCyberTrustRoot** 和 **DigiCertGlobalRootG2** 证书。

  * 对于 Java (MySQL Connector/J) 用户，请执行以下命令：

    ```console
    keytool -importcert -alias MySQLServerCACert -file D:\BaltimoreCyberTrustRoot.crt.pem -keystore truststore -storepass password -noprompt
    ```

    ```console
    keytool -importcert -alias MySQLServerCACert2 -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password -noprompt
    ```

    然后，将原始密钥存储文件替换为新生成的密钥存储文件：

    * System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
    * System.setProperty("javax.net.ssl.trustStorePassword","password");

  * 对于 .NET (MySQL Connector/NET、MySQLConnector) users，请确保 **baltimorecybertrustroot.crt.pem** 和 **DigiCertGlobalRootG2** 都存在于 Windows 证书存储和受信任的根证书颁发机构中。 如果不存在任何证书，则导入缺少的证书。

        ![Azure Database for MySQL .net cert](media/overview/netconnecter-cert.png)

  * 对于使用 SSL_CERT_DIR 的 Linux 上的 .NET 用户，请确保 SSL_CERT_DIR 中的 **baltimorecybertrustroot.crt.pem** 和 **DigiCertGlobalRootG2** 都存在于所指示的目录中。 如果任何证书不存在，请创建缺少的证书文件。

  * 对于其他 (MySQL Client/MySQL 工作台/C/c + +/Go/Python/Ruby/PHP/NodeJS/Perl/Swift) 用户，你可以将两个 CA 证书文件合并为以下格式：</b>

     </br>-----开始证书-----  </br> (根 CA1： Baltimorecybertrustroot.crt.pem)  </br>-----结束证书-----  </br>-----开始证书-----  </br> (根 CA2： DigiCertGlobalRootG2)  </br>-----END CERTIFICATE-----

* 将原始根 CA pem 文件替换为组合的根 CA 文件，然后重启应用程序/客户端。
* 将来，在服务器端部署新证书后，可以将 CA pem 文件更改为 DigiCertGlobalRootG2.crt.pem。

## <a name="what-can-be-the-impact-of-not-updating-the-certificate"></a>不更新证书会产生什么影响？

如果使用此处所述的 Azure Database for MySQL 颁发的证书，则可能会中断应用程序的可用性，因为数据库将无法访问。 根据你的应用程序，你可能会收到各种错误消息，其中包括但不限于：

* 证书/已吊销的证书无效
* 连接超时

> [!NOTE]
> 在进行证书更改之前，请不要删除或更改 **巴尔的摩证书** 。 在完成更改后，我们将发送一条通信，在此之后，他们可以安全地删除巴尔的摩证书。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="1-if-im-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>1. 如果我不使用 SSL/TLS，是否仍需要更新根 CA？

  如果不使用 SSL/TLS，则无需执行任何操作。

### <a name="2-if-im-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>2. 如果我在使用 SSL/TLS，是否需要重新启动数据库服务器来更新根 CA？

不需要，你无需重新启动数据库服务器即可开始使用新证书。 此根证书是客户端更改，传入的客户端连接需要使用新证书来确保它们可以连接到数据库服务器。

### <a name="3-what-will-happen-if-i-dont-update-the-root-certificate-before-february-15-2021-02152021"></a>3. 如果在2021年2月15日之前未更新根证书， (02/15/2021) 会发生什么情况？

如果在2021年2月15日之前未更新根证书 (02/15/2021) ，通过 SSL/TLS 连接并对根证书进行验证的应用程序将无法与 MySQL 数据库服务器通信，应用程序将遇到 MySQL 数据库服务器的连接问题。

### <a name="4-what-is-the-impact-if-using-app-service-with-azure-database-for-mysql"></a>4.如果将应用服务与 Azure Database for MySQL 一起使用，会产生什么影响？

对于连接到 Azure Database for MySQL 的 Azure 应用服务，有两种可能的方案，具体取决于你在应用程序中使用 SSL 的方式。

* 此新证书已在平台级别添加到应用服务。 如果要在应用程序中使用应用服务平台上包含的 SSL 证书，则无需执行任何操作。
* 如果你在代码中显式包含 SSL 证书文件的路径，则需要下载新证书，并更新代码以使用新证书。此方案的一个很好的示例是，在应用服务中使用自定义容器，如[应用服务文档](../app-service/tutorial-multi-container-app.md#configure-database-variables-in-wordpress)中所述

### <a name="5-what-is-the-impact-if-using-azure-kubernetes-services-aks-with-azure-database-for-mysql"></a>5.如果将 Azure Kubernetes 服务 (AKS) 与 Azure Database for MySQL 一起使用，会产生什么影响？

如果尝试使用 Azure Kubernetes Services (AKS) 连接到 Azure Database for MySQL，这类似于从专用客户主机环境进行访问。 请参考[此处](../aks/ingress-own-tls.md)的步骤。

### <a name="6-what-is-the-impact-if-using-azure-data-factory-to-connect-to-azure-database-for-mysql"></a>6.使用 Azure 数据工厂连接到 Azure Database for MySQL 会产生什么影响？

对于使用 Azure Integration Runtime 的连接器，连接器在 Azure 托管的环境中利用 Windows 证书存储中的证书。 这些证书已与新应用的证书兼容，因此不需要执行任何操作。

对于使用自承载 Integration Runtime 的连接器，其中在连接字符串中显式包含 SSL 证书文件的路径，则需要下载 [新证书](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) ，并更新连接字符串以使用该证书。

### <a name="7-do-i-need-to-plan-a-database-server-maintenance-downtime-for-this-change"></a>7.是否需要为此更改计划数据库服务器维护停机时间？

否。 由于此处的更改仅在客户端连接到数据库服务器，因此数据库服务器不需要任何维护停机时间即可进行此更改。

### <a name="8--what-if-i-cannot-get-a-scheduled-downtime-for-this-change-before-february-15-2021-02152021"></a>8.如果在 2021 年 2 月 15 日 (2021/02/15) 之前无法安排停机时间来进行此更改，该怎么办？

由于用于连接到服务器的客户端需要更新证书信息（如 [此处](./concepts-certificate-rotation.md#what-do-i-need-to-do-to-maintain-connectivity)的修复部分所述），因此在这种情况下，服务器不需要停机。

### <a name="9-if-i-create-a-new-server-after-february-15-2021-02152021-will-i-be-impacted"></a>9.如果我在 2021 年 2 月 15 日 (2021/02/15) 之后创建新的服务器，我是否会受影响？

对于在 2021 年 2 月 15 日 (2021/02/15) 之后创建的服务器，可以使用应用程序的新颁发的证书通过 SSL 进行连接。

### <a name="10-how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>10.Microsoft 更新其证书的频率是多少？或者说过期策略是怎样的？

Azure Database for MySQL 使用的这些证书是由受信任的证书颁发机构 (CA) 提供的。 因此，Azure Database for MySQL 对这些证书的支持与 CA 对这些证书的支持相关联。 但是，这些预定义证书中可能存在无法预料的 bug，这些 bug 需要尽早进行修复，正如此示例一样。

### <a name="11-if-im-using-read-replicas-do-i-need-to-perform-this-update-only-on-source-server-or-the-read-replicas"></a>11. 如果我使用的是读取副本，是否只需在源服务器或读取副本上执行此更新？

由于此更新是客户端更改，因此，如果客户端用于从副本服务器读取数据，则还需要对这些客户端应用更改。

### <a name="12-if-im-using-data-in-replication-do-i-need-to-perform-any-action"></a>12. 如果我使用的是数据复制，是否需要执行任何操作？

如果使用 [数据复制](concepts-data-in-replication.md) 来连接到 Azure Database for MySQL，则需要考虑以下两个事项：

* 如果数据复制是从虚拟机（本地或 Azure 虚拟机）到 Azure Database for MySQL，则需要检查是否使用了 SSL 来创建副本。 运行 **SHOW SLAVE STATUS** 并检查以下设置。  

    ```azurecli-interactive
    Master_SSL_Allowed            : Yes
    Master_SSL_CA_File            : ~\azure_mysqlservice.pem
    Master_SSL_CA_Path            :
    Master_SSL_Cert               : ~\azure_mysqlclient_cert.pem
    Master_SSL_Cipher             :
    Master_SSL_Key                : ~\azure_mysqlclient_key.pem
    ```

    如果看不到为 CA_file、SSL_Cert 和 SSL_Key 提供证书，则需要通过添加 [新证书](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)更新文件。

* 如果数据复制在两个 Azure Database for MySQL 之间，则需要通过执行 **调用 mysql.az_replication_change_master** 来重置副本，并提供新的双重根证书作为最后一个参数 [master_ssl_ca](howto-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication)

### <a name="13-do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>13.是否有服务器端查询可用来验证是否正在使用 SSL？

若要验证是否正在使用 SSL 连接连接到服务器，请参阅 [ssl 验证](howto-configure-ssl.md#step-4-verify-the-ssl-connection)。

### <a name="14-is-there-an-action-needed-if-i-already-have-the-digicertglobalrootg2-in-my-certificate-file"></a>14.如果我的证书文件中已有 DigiCertGlobalRootG2，是否需要执行任何操作？

否。 如果你的证书文件已有 **DigiCertGlobalRootG2**，则无需执行任何操作。

###    <a name="15-what-if-i-have-further-questions"></a>15.如果我有其他问题，该怎么办？

如果有疑问，请从 [Microsoft Q&的](mailto:AzureDatabaseforMySQL@service.microsoft.com)社区专家那里获取答案。 如果你有支持计划并需要技术帮助，请[联系我们](mailto:AzureDatabaseforMySQL@service.microsoft.com)。
