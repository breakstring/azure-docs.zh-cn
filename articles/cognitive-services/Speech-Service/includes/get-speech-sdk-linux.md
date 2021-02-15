---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: d0298bcd675b1b94999dab3a1ad1c40a6feb7438
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93135766"
---
:::row:::
    :::column span="3":::
        语音 SDK 仅支持 **Ubuntu 16.04/18.04/20.04** 、 **Debian 9/10** ， **Red Hat Enterprise Linux (RHEL) 7/8** ，并在与 Linux 一起使用时在以下目标体系结构上进行 **CentOS 7/8** ：
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Linux" src="https://docs.microsoft.com/media/logos/logo_linux-color.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

- 用于 C++ 开发的 x86 (Debian/Ubuntu)、x64、ARM32 (Debian/Ubuntu) 和 ARM64 (Debian/Ubuntu)
- 用于 Java 的 x64、ARM32 (Debian/Ubuntu) 和 ARM64 (Debian/Ubuntu)
- 用于 .NET Core 的 x64、ARM32 (Debian/Ubuntu) 和 ARM64 (Debian/Ubuntu)
- 适用于 Python 的 x64

> [!IMPORTANT]
> 对于 Linux ARM64 上的 C#，需要 .NET Core 3.x（dotnet-sdk-3.x 包）。

### <a name="system-requirements"></a>系统要求

对于本机应用程序，语音 SDK 依赖于 `libMicrosoft.CognitiveServices.Speech.core.so`。 请确保目标体系结构（x86、x64）与应用程序匹配。 可能需要其他依赖项，具体取决于 Linux 版本。

- GNU C 库的共享库（包括 POSIX 线程编程库 `libpthreads`）
- OpenSSL 库（`libssl.so.1.0.0` 或 `libssl.so.1.0.2`）
- ALSA 应用程序的共享库 (`libasound.so.2`)

# <a name="ubuntu-160418042004"></a>[Ubuntu 16.04/18.04/20.04](#tab/ubuntu)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2
```

> [!NOTE]
> 如果 libssl1.0.0 1.0 版不可用，请改为安装 libssl1.0.0 1.1。

# <a name="debian-910"></a>[Debian 9/10](#tab/debian)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.2 libasound2
```

> [!NOTE]
> 如果 libssl1.0.0 1.0 版不可用，请改为安装 libssl1.0.0 1.1。

# <a name="rhel-78-and-centos-78"></a>[RHEL 7/8 和 CentOS 7/8](#tab/rhel-centos)

```Bash
sudo yum update
sudo yum install alsa-lib openssl
```

> [!IMPORTANT]
> - 在 RHEL/CentOS 7 上，按照[如何为语音 SDK 配置 RHEL/CentOS 7](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md) 上的说明进行操作。
> - 在 RHEL/CentOS 8 上，按照[如何配置 OpenSSL for Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md) 上的说明进行操作。

---

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
