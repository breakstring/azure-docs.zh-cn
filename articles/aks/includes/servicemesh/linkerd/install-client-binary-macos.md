---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 876e05d7b18ac193edbc9cf842ea2c1bf0555d54
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "77593698"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>下载并安装 Linkerd linkerd 客户端二进制文件

在 MacOS 上基于 bash 的 shell 中，使用 `curl` 下载 Linkerd 发行版，如下所示：

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-darwin"
```

`linkerd` 客户端二进制文件在客户端计算机上运行，用来与 Linkerd 服务网格交互。 在 MacOS 上基于 bash 的 shell 中使用以下命令安装 Linkerd `linkerd` 客户端二进制文件。 这些命令可将 `linkerd` 客户端二进制文件复制到 `PATH` 中的标准用户程序位置。

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-darwin /usr/local/bin/linkerd
sudo chmod +x /usr/local/bin/linkerd
```

如果想要通过命令行完成 Linkerd `linkerd` 客户端二进制文件的下载和安装，则按如下所示进行设置：

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && linkerd completion bash > ~/completions/linkerd.bash
source ~/completions/linkerd.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/linkerd.bash" >> ~/.bashrc
```
