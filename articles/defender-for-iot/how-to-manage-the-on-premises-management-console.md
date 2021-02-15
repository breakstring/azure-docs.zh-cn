---
title: 管理本地管理控制台
description: 了解本地管理控制台选项，如备份和还原、定义主机名和设置传感器的代理。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/12/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: d76db6830839902a46aaf6515f816fdcc36d0df5
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523934"
---
# <a name="manage-the-on-premises-management-console"></a>管理本地管理控制台

本文介绍本地管理控制台选项，如备份和还原、下载委员会设备激活文件、更新证书以及设置传感器的代理。

你从 Azure 门户中载入了本地管理控制台。

## <a name="upload-an-activation-file"></a>上传激活文件

首次登录时，会下载本地管理控制台的激活文件。 此文件包含在载入过程中定义的已提交的聚合设备。 此列表包括与多个订阅关联的传感器。

初始激活后，受监视的设备数可能会超过在载入期间定义的已提交设备数。 例如，如果将更多的传感器连接到管理控制台，则可能发生此事件。 如果监视的设备数和已提交设备数之间存在差异，则管理控制台中会出现警告。 如果发生此事件，则应该上载新的激活文件。

上载激活文件：

1. 请参阅 Azure Defender for IoT **定价** 页。
1. 选择 " **下载管理控制台** " 选项卡的激活文件。下载激活文件。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/cloud_download_opm_activation_file.png" alt-text="下载激活文件。":::

1. 从管理控制台中选择 " **系统设置** "。
1. 选择 " **激活**"。
1. 选择 " **选择文件** "，然后选择已保存的文件。

## <a name="manage-certificates"></a>管理证书

安装本地管理控制台后，将生成一个本地自签名证书，并使用它来访问管理控制台的 web 应用程序。 当管理员用户首次登录到管理控制台时，系统将提示他们提供 SSL/TLS 证书。 有关首次设置的详细信息，请参阅 [激活和设置本地管理控制台](how-to-activate-and-set-up-your-on-premises-management-console.md)。

以下各节提供有关更新证书、使用证书 CLI 命令以及支持的证书和证书参数的信息。

### <a name="about-certificates"></a>关于证书

Azure Defender for IoT 使用 SSL 和 TLS 证书来执行以下操作：

- 通过上传 CA 签名证书来满足组织要求的特定证书和加密要求。

- 允许在管理控制台和连接的传感器之间以及管理控制台和高可用性管理控制台之间进行验证。 对照证书吊销列表和证书到期日期对验证进行评估。 *如果验证失败，则会停止管理控制台和传感器之间的通信，并在控制台中显示验证错误*。 默认情况下，安装后会启用此选项。

不验证第三方转发规则。 示例是发送到 SYSLOG、Splunk 或 ServiceNow 的警报信息;与 Active Directory 通信。

#### <a name="ssl-certificates"></a>SSL 证书数

用于 IoT 传感器和本地管理控制台的 Defender 用于以下功能的 SSL 和 TLS 证书： 

 - 保护用户之间的通信和设备的 web 控制台。 
 
 - 保护与传感器和本地管理控制台上的 REST API 的通信。
 
 - 保护传感器与本地管理控制台之间的通信。 

安装完成后，设备会生成一个本地自签名证书，以允许对 web 控制台进行初步访问。 可以使用命令行工具安装 Enterprise SSL 和 TLS 证书 [`cyberx-xsense-certificate-import`](#cli-commands) 。 

 > [!NOTE]
 > 对于集成和转发规则（其中设备是会话的客户端和发起方），会使用特定证书，并且与系统证书无关。  
 >
 >在这些情况下，通常会从服务器接收证书，或使用非对称加密，其中提供了特定的证书来设置集成。 

### <a name="update-certificates"></a>更新证书

本地管理控制台的管理员用户可以更新证书。

若要更新证书：  

1. 选择 " **系统设置**"。

1. 选择 " **SSL/TLS 证书**"。
1. 删除或编辑证书，然后添加一个新证书。
   
   - 添加证书名称。
   
   - 上传 CRT 文件和密钥文件，并输入通行短语。
   - 如有必要，请上传 PEM 文件。

若要更改验证设置：

1. 打开或关闭 " **启用证书验证** " 切换。

1. 选择“保存” 。

如果启用了该选项并且验证失败，则会停止管理控制台和传感器之间的通信，并在控制台中显示验证错误。

### <a name="certificate-support"></a>证书支持

支持以下证书：

- 专用 PKI 和企业密钥基础结构 (专用 PKI) 
 
- 公共密钥基础结构 (公共 PKI)  

- 在设备上本地生成 (本地自签名)  

  > [!IMPORTANT]
  > 不建议使用自签名证书。 这种类型的连接是不安全的，只应用于测试环境。 由于无法验证证书的所有者，并且无法维护系统的安全，因此不应在生产网络中使用自签名证书。

### <a name="supported-ssl-certificates"></a>支持的 SSL 证书 

支持以下参数： 

**证书 CRT**

- 域名的主证书文件

- 签名算法 = SHA256RSA
- 签名哈希算法 = SHA256
- 有效起始日期 = 有效的过去日期
- 有效截止日期 = 有效的将来日期
- 公钥 = (最小) 或4096位的 RSA 2048 位
- CRL 分发点 = 指向 .crl 文件的 URL
- Subject CN = URL，可以是通配符证书;例如，"contoso"。 <span>com 或 * .com。 <span>com
- 主题 (C) ountry = 已定义，例如 US
- 主题 (OU) Org Unit = 已定义;例如 Contoso 实验室
- 主题 (O) 组织 = 已定义;例如 Contoso Inc。

**密钥文件**

- 创建 CSR 时生成的密钥文件

- RSA 2048 位 (最小) 或4096位

 > [!Note]
 > 使用4096bits 的密钥长度：
 > - 每次连接开始时的 SSL 握手都将变慢。  
 > - 握手期间的 CPU 使用率增加。 

**证书链**

- 如果 CA 提供的任何) ，中间证书文件 (。

- 颁发服务器证书的 CA 证书应位于文件中，然后是所有其他证书，然后是根。 
- 链可以包含包属性。

**密码**

- 支持一个密钥。

- 在导入证书时进行设置。

具有其他参数的证书可能有效，但 Microsoft 不支持。

#### <a name="encryption-key-artifacts"></a>加密密钥项目

**pem：证书容器文件**

隐私增强邮件 (PEM) 文件是用于保护电子邮件的常规文件类型。 如今、PEM 文件与证书一起使用，并使用 x509 ASN1 项。  

此容器文件在 Rfc 1421 到1424中定义，这种容器格式只能包括公共证书。 例如，Apache 安装，CA 证书，文件，等等，SSL 或证书。 这可以包括整个证书链，包括公钥、私钥和根证书。  

它还可以将 CSR 编码为 PKCS10 格式，可将其转换为 PEM。

**cert .cer：证书容器文件**

`.pem` `.der` 具有不同扩展名的或带格式的文件。 Windows 资源管理器将文件识别为证书。 `.pem`   Windows 资源管理器不能识别该文件。

**。密钥：私钥文件**

密钥文件的格式与 PEM 文件的格式相同，但它具有不同的扩展名。 

#### <a name="additional-commonly-available-key-artifacts"></a>其他常用的关键项目

**。 csr-证书签名请求**。  

此文件用于提交到证书颁发机构。 实际格式为 PKCS10，它是在 RFC 2986 中定义的，并且可能包括请求的证书的部分或全部重要详细信息。 例如，使用者、组织和省/市/自治区。 它是证书的公钥，由 CA 签名，并接收返回的证书。  

返回的证书是公共证书，其中包括公钥，而不包括私钥。 

**pkcs12 – password 容器**。 

最初由 RSA 在 (PKCS) 的 Public-Key 加密标准中定义，这是由 Microsoft 最初增强的 12-变体，并将在以后提交为 RFC 7292。  

此容器格式需要包含公用和专用证书对的密码。 与 `.pem`   文件不同，此容器是完全加密的。  

可以使用 OpenSSL 将该文件转换为 `.pem`   具有公钥和私钥的文件： `openssl pkcs12 -in file-to-convert.p12 -out converted-file.pem -nodes`  

**der –二进制编码的 PEM**。

以二进制格式对 node.js 语法进行编码的方式是通过 `.pem`   文件，该文件只是一个 Base64 编码的 `.der` 文件。 

OpenSSL 可以将这些文件转换为 `.pem` ：  `openssl x509 -inform der -in to-convert.der -out converted.pem` 。  

Windows 会将这些文件识别为证书文件。 默认情况下，Windows 会将证书导出为 `.der` 具有不同扩展名的格式化文件。

**.crl-证书吊销列表**。  

证书颁发机构生成这些证书是一种在证书过期前取消授权证书的方式。 

#### <a name="cli-commands"></a>CLI 命令

使用 `cyberx-xsense-certificate-import` CLI 命令导入证书。 若要使用此工具，需要使用 WinSCP 或 Wget 等工具将证书文件上传到设备。

此命令支持以下输入标志：

- `-h`：显示命令行帮助语法。

- `--crt`：指向)  ( 的证书文件的路径。

- `--key`：  \* 密钥文件。 密钥长度应至少为2048位。

- `--chain`：证书链文件的路径 (可选) 。

- `--pass`：用于加密证书的密码 (可选) 。

- `--passphrase-set`：默认值 = `False` 、未使用的。 设置为 `True` 以使用上一个证书提供的以前的密码 (可选) 。

使用 CLI 命令时：

- 验证证书文件在设备上是否可读。

- 验证证书中的域名和 IP 是否与 IT 部门计划的配置相匹配。

### <a name="use-openssl-to-manage-certificates"></a>使用 OpenSSL 管理证书

通过以下命令管理证书：

| 说明 | CLI 命令 |
|--|--|
| 生成新的私钥和证书签名请求 | `openssl req -out CSR.csr -new -newkey rsa:2048 -nodes -keyout privateKey.key` |
| 生成自签名证书 | `openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out certificate.crt` |
| 为现有私钥 (CSR) 生成证书签名请求 | `openssl req -out CSR.csr -key privateKey.key -new` |
| 基于现有证书生成证书签名请求 | `openssl x509 -x509toreq -in certificate.crt -out CSR.csr -signkey privateKey.key` |
| 从私钥中删除密码 | `openssl rsa -in privateKey.pem -out newPrivateKey.pem` |

如果需要检查证书、CSR 或私钥中的信息，请使用以下命令：

| 说明 | CLI 命令 |
|--|--|
|  (CSR 检查证书签名请求)  | `openssl req -text -noout -verify -in CSR.csr` |
| 检查私钥 | `openssl rsa -in privateKey.key -check` |
| 检查证书 | `openssl x509 -in certificate.crt -text -noout`  |

如果收到一条错误消息，指出私钥与证书不符，或者你安装到站点的证书不受信任，请使用以下命令来修复此错误：

| 说明 | CLI 命令 |
|--|--|
| 检查公钥的 MD5 哈希，以确保它与 CSR 或私钥中的内容相匹配 | 2. `openssl x509 -noout -modulus -in certificate.crt | openssl md5` <br /> pps-2. `openssl rsa -noout -modulus -in privateKey.key | openssl md5` <br /> 3. `openssl req -noout -modulus -in CSR.csr | openssl md5 ` |

若要将证书和密钥转换为不同的格式，以使它们与特定类型的服务器（或软件）兼容，请使用以下命令：

| 说明 | CLI 命令 |
|--|--|
| 将 DER 文件 () 转换为 PEM  | `openssl x509 -inform der -in certificate.cer -out certificate.pem`  |
| 将 PEM 文件转换为 DER | `openssl x509 -outform der -in certificate.pem -out certificate.der`  |
| 将包含私钥和证书的 PKCS # 12 文件 () 转换为 PEM | `openssl pkcs12 -in keyStore.pfx -out keyStore.pem -nodes` <br />你可以 `-nocerts` 仅添加到输出私钥，或添加 `-nokeys` 以仅输出证书。 |
| 将 PEM 证书文件和私钥转换为 PKCS # 12 ()  | `openssl pkcs12 -export -out certificate.pfx -inkey privateKey.key -in certificate.crt -certfile CACert.crt` |

## <a name="define-backup-and-restore-settings"></a>定义备份和还原设置

本地管理控制台系统备份会在每天自动执行。 数据保存在不同的磁盘上。 默认位置为 `/var/cyberx/backups`。 

可以自动将此文件传输到内部网络。 

> [!NOTE]
> 只能在同一版本上执行备份和还原过程。 

备份本地管理控制台计算机： 

- 登录到管理帐户，然后输入 `sudo cyberx-management-backup -full` 。

还原最新备份文件：

- 登录到管理帐户，然后输入 `$ sudo cyberx-management-system-restore` 。

若要将备份保存到外部 SMB 服务器：

1. 在外部 SMB 服务器中创建共享文件夹。  

   获取访问 SMB 服务器所需的文件夹路径、用户名和密码。 

2. 在用于 IoT 的 Defender 中，为备份创建目录：

   - `sudo mkdir /<backup_folder_name_on_ server>` 

   - `sudo chmod 777 /<backup_folder_name_on_c_server>/` 

3. 编辑 fstab：  

   - `sudo nano /etc/fstab` 

   - `add - //<server_IP>/<folder_path> /<backup_folder_name_on_server> cifs rw,credentials=/etc/samba/user,vers=3.0,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0` 

4. 编辑或创建要共享的 SMB 服务器的凭据： 

   - `sudo nano /etc/samba/user` 

5. 添加： 

   - `username=<user name>`

   - `password=<password>` 

6. 装载目录： 

   - `sudo mount -a` 

7. 为 IoT 本地管理控制台的 Defender 上的共享文件夹配置备份目录：  

   - `sudo nano /var/cyberx/properties/backup.properties` 

   - `set Backup.shared_location to <backup_folder_name_on_server>`

## <a name="edit-the-host-name"></a>编辑主机名

编辑组织 DNS 服务器中配置的管理控制台的主机名：

1. 在管理控制台的左窗格中，选择 " **系统设置**"。  

2. 在控制台的 "网络" 部分，选择 " **网络**"。 

3. 输入在组织 DNS 服务器中配置的主机名。 

4. 选择“保存” 。

## <a name="define-vlan-names"></a>定义 VLAN 名称

VLAN 名称在传感器和管理控制台之间不同步。 应在组件上定义相同的名称。

在 "网络" 区域中，选择 " **vlan** " 并将名称添加到发现的 VLAN id。 再选择“保存”。

## <a name="define-a-proxy-to-sensors"></a>定义传感器的代理

通过阻止用户直接登录传感器来增强系统安全性。 请改用代理隧道，让用户使用单个防火墙规则从本地管理控制台访问传感器。 此增强功能缩小了对传感器以外的网络环境进行未经授权的访问的可能性。

在不直接连接到传感器的环境中使用代理。

:::image type="content" source="media/how-to-access-sensors-using-a-proxy/proxy-diagram.png" alt-text="用户.":::

以下过程将传感器连接到本地管理控制台，并在该控制台上启用隧道：

1. 登录到具有管理凭据的本地管理控制台设备 CLI。

2. 键入 `sudo cyberx-management-tunnel-enable` 并选择 **Enter**。

4. 键入 `--port 10000` 并选择 **Enter**。

## <a name="adjust-system-properties"></a>调整系统属性

系统属性控制管理控制台中的各种操作和设置。 编辑或修改它们可能会损坏管理控制台的操作。 更改设置之前，请参考 [Microsoft 支持部门](https://support.microsoft.com) 。

访问系统属性： 

1. 登录到本地管理控制台或传感器。

2. 选择 " **系统设置**"。

3. 从 "**常规**" 部分中选择 "**系统属性**"。

## <a name="change-the-name-of-the-on-premises-management-console"></a>更改本地管理控制台的名称

你可以更改本地管理控制台的名称。 新名称显示在控制台 web 浏览器、各种控制台窗口和疑难解答日志中。 默认名称是 **管理控制台**。

若要更改名称：

1. 在左窗格的底部，选择当前名称。

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/console-name.png" alt-text="本地管理控制台版本的屏幕截图。":::

2. 在 " **编辑管理控制台配置** " 对话框中，输入新名称。 名称长度不能超过25个字符。

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/edit-management-console-configuration.png" alt-text="编辑用于 IoT 平台配置的 Defender 的屏幕截图。":::

3. 选择“保存” 。 新名称将被应用。

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/name-changed.png" alt-text="显示控制台更改名称的屏幕截图。":::

## <a name="password-recovery"></a>密码恢复

本地管理控制台的密码恢复与设备附加到的订阅相关联。 如果不知道设备附加到的订阅，则无法恢复密码。

重置密码：

1. 请参阅本地管理控制台的登录页。
1. 选择 " **密码恢复**"。
1. 复制唯一标识符。
1. 在 "用于 IoT **网站和传感器** 的 Defender" 页上，选择 " **恢复我的密码** " 选项卡。
1. 输入唯一标识符，然后选择 " **恢复**"。 下载激活文件。
1. 请在 " **密码恢复** " 页上，上载激活文件。
1. 选择“下一步”。
 
   现在提供了用户名和系统生成的新密码。

> [!NOTE]
> 该传感器链接到它最初连接到的订阅。 只能使用其附加到的订阅来恢复密码。

## <a name="update-the-software-version"></a>更新软件版本

以下过程描述了如何更新本地管理控制台软件版本。 更新过程大约需30分钟。

1. 转到 [Azure 门户](https://portal.azure.com/)。

1. 中转到用于 IoT 的 Defender。

1. 请参阅 " **更新** " 页面。

1. 从本地管理控制台部分中选择一个版本。

1. 选择“下载”并保存文件。

1. 登录到本地管理控制台，并从侧菜单中选择 " **系统设置** "。

1. 在 " **版本更新** " 窗格中，选择 " **更新**"。

1. 选择从 "用于 IoT **更新** 的 Defender" 页面下载的文件。

## <a name="next-steps"></a>后续步骤

[通过管理控制台管理传感器](how-to-manage-sensors-from-the-on-premises-management-console.md)

[管理单个传感器](how-to-manage-individual-sensors.md)
