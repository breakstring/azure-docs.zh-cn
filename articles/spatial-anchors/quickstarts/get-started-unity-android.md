---
title: 快速入门：创建 Unity Android 应用
description: 在此快速入门中，将了解如何通过空间定位点使用 Unity 生成 Android 应用。
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 3c17eadfad71f58b4557f8115a0d1d3de392b4b9
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2020
ms.locfileid: "96015122"
---
# <a name="quickstart-create-a-unity-android-app-with-azure-spatial-anchors"></a>快速入门：使用 Azure 空间定位点创建 Unity Android 应用

本快速入门介绍如何使用 [Azure 空间定位点](../overview.md)创建 Unity Android 应用。 Azure 空间定位点是一种跨平台开发人员服务。通过该服务，可以使用能够随时间推移跨设备保存自己位置的对象创建混合的现实体验。 完成后，将获得一个使用 Unity生成的 ARCore Android 应用，该应用可以保存和重新调用空间定位点。

将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建空间定位点帐户
> * 准备 Unity 生成设置
> * 配置空间定位点帐户标识符和帐户密钥
> * 导出 Android Studio 项目
> * 在 Android 设备上部署和运行

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，请确保具备以下项：

- 已安装 <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.4 (LTS)</a>（包括 Android Build Support、Android SDK & NDK Tools 和 OpenJDK 模块）的 Windows 或 macOS 计算机  。
  - 如果在 Windows 上运行，则还需要 <a href="https://git-scm.com/download/win" target="_blank">Git for Windows</a> 和 <a href="https://git-lfs.github.com/">Git LFS</a>。
  - 如果在 macOS 上运行，请通过 HomeBrew 安装 Git。 在终端的一行中输入以下命令：`/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`。 然后，运行 `brew install git` 和 `brew install git-lfs`。
- <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">支持开发人员</a>和 <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore 功能</a>的 Android 设备。
  - 你的计算机可能需要其他设备驱动程序才能与 Android 设备通信。 有关其他详细信息和说明，请参阅[此处](https://developer.android.com/studio/run/device.html)。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>下载并打开 Unity 示例项目

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

## <a name="export-the-android-studio-project"></a>导出 Android Studio 项目

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

在“运行设备”中选择你的设备，然后选择“生成并运行” 。 系统将要求你保存 `.apk` 文件，你可为它选取任何名称。

在应用中，使用箭头选择“BasicDemo”，然后按“开始!” 按钮以运行演示。 按说明放置并重新调用定位点。

![屏幕截图 1](./media/get-started-unity-android/screenshot-1.jpg)
![屏幕截图 2](./media/get-started-unity-android/screenshot-2.jpg)
![屏幕截图 3](./media/get-started-unity-android/screenshot-3.jpg)

按照应用中的说明，放置并重新调用定位点。

## <a name="troubleshooting"></a>疑难解答

### <a name="rendering-issues"></a>呈现问题

运行应用时，如果没看到作为背景的照相机（例如，你看到的是空白、蓝色或其他纹理），则可能需要重新导入 Unity 中的资产。 停止应用。 在 Unity 中的顶部菜单中，选择“资产”->“重新导入全部”。 然后再次运行应用。

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [教程：跨设备共享空间定位点](../tutorials/tutorial-share-anchors-across-devices.md)

> [!div class="nextstepaction"]
> [如何：在 Unity 项目中配置 Azure 空间定位点](../how-tos/setup-unity-project.md)
