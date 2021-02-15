---
title: 快速入门：创建 Unity iOS 应用
description: 在此快速入门中，将了解如何通过空间定位点使用 Unity 生成 iOS 应用。
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: b278ac6c824b1583e90cfc9152264f61357dd228
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2020
ms.locfileid: "95971485"
---
# <a name="quickstart-create-a-unity-ios-app-with-azure-spatial-anchors"></a>快速入门：使用 Azure 空间定位点创建 Unity iOS 应用

本快速入门介绍如何使用 [Azure 空间定位点](../overview.md)创建 Unity iOS 应用。 Azure 空间定位点是一种跨平台开发人员服务。通过该服务，可以使用能够随时间推移跨设备保存自己位置的对象创建混合的现实体验。 完成后，将获得一个使用 Unity生成的 ARKit iOS 应用，该应用可以保存和重新调用空间定位点。

将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建空间定位点帐户
> * 准备 Unity 生成设置
> * 配置空间定位点帐户标识符和帐户密钥
> * 导出 XCode 项目
> * 在 iOS 设备上部署和运行

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，请确保具备以下项：

- 已安装 <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.4 (LTS)</a>、最新版 <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode</a> 的 macOS 计算机。
- 通过 HomeBrew 安装的 Git。 在终端的一行中输入以下命令：`/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`。 然后，运行 `brew install git` 和 `brew install git-lfs`。
- 支持开发人员的 <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">ARKit 兼容</a> iOS 设备。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>下载并打开 Unity 示例项目

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [iOS Unity Build Settings](../../../includes/spatial-anchors-unity-ios-build-settings.md)]

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

## <a name="export-the-xcode-project"></a>导出 XCode 项目

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

[!INCLUDE [Configure Xcode](../../../includes/spatial-anchors-unity-ios-xcode.md)]

在应用中，使用箭头选择“BasicDemo”，然后按“开始!” 按钮以运行演示。 按说明放置并重新调用定位点。

![屏幕截图 1](./media/get-started-unity-ios/screenshot-1.jpg)
![屏幕截图 2](./media/get-started-unity-ios/screenshot-2.jpg)
![屏幕截图 3](./media/get-started-unity-ios/screenshot-3.jpg)

完成后，通过在 Xcode 中按“停止”来停止应用。

## <a name="troubleshooting"></a>疑难解答

### <a name="rendering-issues"></a>呈现问题

运行应用时，如果没看到作为背景的照相机（例如，你看到的是空白、蓝色或其他纹理），则可能需要重新导入 Unity 中的资产。 停止应用。 在 Unity 中的顶部菜单中，选择“资产”->“重新导入全部”。 然后再次运行应用。

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [教程：跨设备共享空间定位点](../tutorials/tutorial-share-anchors-across-devices.md)

> [!div class="nextstepaction"]
> [如何：在 Unity 项目中配置 Azure 空间定位点](../how-tos/setup-unity-project.md)
