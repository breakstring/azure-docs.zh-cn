---
title: 快速入门 - 在门户中创建注册表
description: 快速了解如何通过 Azure 门户创建专用 Azure 容器注册表。
ms.topic: quickstart
ms.date: 08/04/2020
ms.custom: seodec18, mvc
ms.openlocfilehash: ff74232edf5c96f091082f8486c44fed6125b0b1
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/30/2020
ms.locfileid: "97825835"
---
# <a name="quickstart-create-an-azure-container-registry-using-the-azure-portal"></a>快速入门：通过 Azure 门户创建 Azure 容器注册表

Azure 容器注册表是 Azure 中的专用 Docker 注册表，你可在其中存储和管理专用 Docker 容器映像和相关的项目。 在本快速入门教程中，你会使用 Azure 门户创建容器注册表。 然后，使用 Docker 命令将容器映像推送到注册表中，最终从注册表提取并运行该映像。

若要登录到注册表以使用容器映像，本快速入门要求运行 Azure CLI（建议使用 2.0.55 或更高版本）。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli]。

还必须在本地安装 Docker。 Docker 提供的包可在任何 [Mac][docker-mac]、[Windows][docker-windows] 或 [Linux][docker-linux] 系统上轻松配置 Docker。

## <a name="sign-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="create-a-container-registry"></a>创建容器注册表

选择“创建资源” > “容器” > “容器注册表”。  

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-01.png" alt-text="在门户中导航到容器注册表":::

在“基本信息”选项卡中，输入“资源组”和“注册表名称”的值 。 注册表名称在 Azure 中必须唯一，并且包含 5-50 个字母数字字符。 对于本快速入门，在 `West US` 位置创建名为 `myResourceGroup` 的新资源组，对于 **SKU**，选择“基本”。

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-03.png" alt-text="在门户中创建容器注册表":::

对于剩余的设置，请接受默认值。 然后选择“查看 + 创建”。 查看设置后，选择“创建”。

本快速入门将创建一个“基本”注册表。该注册表已针对成本进行优化，是可供开发人员了解 Azure 容器注册表的选项。 有关可用服务层级 (SKU) 的详细信息，请参阅[容器注册表服务层级][container-registry-skus]。

显示“部署成功”消息时，请在门户中选择容器注册表。 

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-05.png" alt-text="门户中的容器注册表概述":::

记下登录服务器的注册表名称和值。 使用 Docker 推送和拉取映像时，请在以下步骤中使用这些值。

## <a name="log-in-to-registry"></a>登录到注册表

必须登录到注册表实例才可推送和拉取容器映像。 在本地计算机上[登录到 Azure CLI][get-started-with-azure-cli]，然后运行 [az acr login][az-acr-login] 命令。 使用 Azure CLI 登录时仅指定注册表名称。 不要使用登录服务器名称，其中包括类似 `azurecr.io` 的域后缀。

```azurecli
az acr login --name <registry-name>
```

示例：

```azurecli
az acr login --name mycontainerregistry
```

该命令在完成后返回 `Login Succeeded`。 

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>列出容器映像

若要列出注册表中的映像，请在门户中导航到注册表并选择“存储库”，然后选择使用 `docker push` 创建的 hello-world 存储库 。

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-09.png" alt-text="在门户中列出容器映像":::

通过选择 hello-world 存储库，可以在“标记”下看到 `v1` 标记的映像 。

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>清理资源

若要清理资源，请在门户中导航到 **myResourceGroup** 资源组。 加载该资源组后，单击“删除资源组”，删除该资源组、容器注册表以及其中存储的容器映像。

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-08.png" alt-text="在门户中删除资源组":::


## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何使用 Azure 门户创建 Azure 容器注册表、推送容器映像，以及提取和运行注册表中的映像。 请继续阅读 Azure 容器注册表教程，以更深入地了解 ACR。

> [!div class="nextstepaction"]
> [Azure 容器注册表教程][container-registry-tutorial-prepare-registry]

> [!div class="nextstepaction"]
> [Azure 容器注册表任务教程][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[container-registry-tutorial-prepare-registry]: container-registry-tutorial-prepare-registry.md
[container-registry-skus]: container-registry-skus.md
[azure-cli]: /cli/azure/install-azure-cli
[get-started-with-azure-cli]: /cli/azure/get-started-with-azure-cli
[az-acr-login]: /cli/azure/acr#az-acr-login
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
