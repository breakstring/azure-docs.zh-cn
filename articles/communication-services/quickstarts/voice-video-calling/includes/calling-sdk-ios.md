---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: a8cfdc76694d52acee70cde0e3f1697cd8129d06
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2020
ms.locfileid: "97692017"
---
## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- 已部署的通信服务资源。 [创建通信服务资源](../../create-communication-resource.md)。
- 用于启用呼叫客户端的`User Access Token`。 详细了解[如何获取`User Access Token`](../../access-tokens.md)
- 可选：完成快速入门[添加对应用程序调用](../getting-started-with-calling.md)的入门教程

## <a name="setting-up"></a>设置

### <a name="creating-the-xcode-project"></a>创建 Xcode 项目

在 Xcode 中，创建新的 iOS 项目，并选择“单视图应用”模板。 此快速入门使用 [SwiftUI 框架](https://developer.apple.com/xcode/swiftui/)，因此应将 **语言** 设置为 **Swift** ，并将 **用户界面** 设置为 **SwiftUI**。 在本快速入门过程中，不会创建单元测试或 UI 测试。 可以随意取消选中 " **包括单元测试** "，也可以取消选中 " **包括 UI 测试**"。

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="显示 Xcode 中的创建新项目窗口的屏幕截图。":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>使用 CocoaPods 安装包和依赖项

1. 为应用程序创建 Podfile，如下所示：

   ```
   platform :ios, '13.0'
   use_frameworks!
   target 'AzureCommunicationCallingSample' do
     pod 'AzureCommunicationCalling', '~> 1.0.0-beta.5'
     pod 'AzureCommunication', '~> 1.0.0-beta.5'
     pod 'AzureCore', '~> 1.0.0-beta.5'
   end
   ```

2. 运行 `pod install`。
3. 打开 `.xcworkspace` With XCode。

### <a name="request-access-to-the-microphone"></a>请求访问麦克风

若要访问设备的麦克风，需要使用 `NSMicrophoneUsageDescription` 更新应用的信息属性列表。 将关联值设置为将包含在系统用于向用户请求访问权限的对话框中的 `string`。

右键单击项目树的 `Info.plist` 条目，然后选择“打开为” > “源代码” 。 将以下代码行添加到顶层 `<dict>` 节，然后保存文件。

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>设置应用框架

打开项目的 ContentView.swift 文件，然后将 `import` 声明添加到文件顶部以导入 `AzureCommunicationCalling library`。 此外，导入还 `AVFoundation` 需要在代码中为音频权限请求提供此项。

```swift
import AzureCommunicationCalling
import AVFoundation
```

## <a name="object-model"></a>对象模型

下面的类和接口处理调用适用于 iOS 的客户端库的 Azure 通信服务的一些主要功能。


| 名称                                  | 说明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ACSCallClient | ACSCallClient 是调用客户端库的主入口点。|
| ACSCallAgent | ACSCallAgent 用于启动和管理调用。 |
| CommunicationUserCredential | CommunicationUserCredential 用作实例化 CallAgent 的令牌凭据。| 
| CommunicationIndentifier | CommunicationIndentifier 用于表示用户的标识，可以是以下项之一：CommunicationUser/PhoneNumber/CallingApplication。 |

> [!NOTE]
> 实现事件委托时，应用程序必须持有对需要事件订阅的对象的强引用。 例如， `ACSRemoteParticipant` 在调用方法时返回对象， `call.addParticipant` 而应用程序将委托设置为侦听时 `ACSRemoteParticipantDelegate` ，应用程序必须持有对对象的强引用 `ACSRemoteParticipant` 。 否则，如果收集此对象，则当调用 SDK 尝试调用对象时，委托将引发严重异常。

## <a name="initialize-the-acscallagent"></a>初始化 ACSCallAgent

若要创建 `ACSCallAgent` 实例， `ACSCallClient` 你必须使用 `callClient.createCallAgent` 方法，该方法在 `ACSCallAgent` 初始化后返回对象

若要创建调用客户端，你必须传递 `CommunicationUserCredential` 对象。

```swift

import AzureCommunication

let tokenString = "token_string"
var userCredential: CommunicationUserCredential?
do {
    userCredential = try CommunicationUserCredential(
        initialToken: tokenString, refreshProactively: true,
        tokenRefresher: self.fetchTokenSync
    )
} catch {
    print("Failed to create CommunicationCredential object")
}

// tokenProvider needs to be implemented by contoso which fetches new token
public func fetchTokenSync(then onCompletion: TokenRefreshOnCompletion) {
    let newToken = self.tokenProvider!.fetchNewToken()
    onCompletion(newToken, nil)
}
```

将上面创建的 CommunicationUserCredential 对象传递给 ACSCallClient 并设置显示名称。

```swift

callClient = CallClient()
let callAgentOptions:CallAgentOptions = CallAgentOptions()
options.displayName = "ACS iOS User"

callClient?.createCallAgent(userCredential: userCredential!,
    options: callAgentOptions,
    completionHandler: { (callAgent, error) in
        if error == nil {
            print("Create agent succeeded")
            self.callAgent = callAgent
        } else {
            print("Create agent failed")
        }
})

```

## <a name="place-an-outgoing-call"></a>发出传出呼叫

若要创建并启动呼叫，你需要调用上的一个 Api `ACSCallAgent` ，并提供已使用通信服务管理客户端库预配的用户的通信服务标识。

调用创建和启动是同步的。 你将收到调用实例，允许你订阅调用上的所有事件。

### <a name="place-a-11-call-to-a-user-or-a-1n-call-with-users-and-pstn"></a>使用用户和 PSTN 向用户或1： n 发出1:1 调用

```swift

let callees = [CommunicationUser(identifier: 'acsUserId')]
let oneToOneCall = self.callAgent.call(participants: callees, options: StartCallOptions())

```

### <a name="place-a-1n-call-with-users-and-pstn"></a>将1： n 调用与用户和 PSTN 一起使用
若要对 PSTN 进行调用，必须指定通过通信服务获取的电话号码
```swift

let pstnCallee = PhoneNumber('+1999999999')
let callee = CommunicationUser(identifier: 'acsUserId')
let groupCall = self.callAgent.call(participants: [pstnCallee, callee], options: StartCallOptions())

```

### <a name="place-a-11-call-with-with-video"></a>使用 with video 发出1:1 调用
若要获取设备管理器实例，请参阅 [此处](#device-management)

```swift

let camera = self.deviceManager!.getCameraList()![0]
let localVideoStream = LocalVideoStream(camera: camera)
let videoOptions = VideoOptions(localVideoStream: localVideoStream)

let startCallOptions = StartCallOptions()
startCallOptions?.videoOptions = videoOptions

let callee = CommunicationUser(identifier: 'acsUserId')
let call = self.callAgent?.call(participants: [callee], options: startCallOptions)

```

### <a name="join-a-group-call"></a>加入群组通话
若要加入呼叫，需要在 *CallAgent* 上调用其中一个 api

```swift

let groupCallContext = GroupCallContext()
groupCallContext?.groupId = UUID(uuidString: "uuid_string")!
let call = self.callAgent?.join(with: groupCallContext, joinCallOptions: JoinCallOptions())

```

### <a name="accept-an-incoming-call"></a>接受传入呼叫
若要接受调用，请对调用对象调用 "accept" 方法。
将委托设置为 CallAgent 
```swift
final class CallHandler: NSObject, CallAgentDelegate
{
    public var incomingCall: Call?
 
    public func onCallsUpdated(_ callAgent: CallAgent!, args: CallsUpdatedEventArgs!) {
        if let incomingCall = args.addedCalls?.first(where: { $0.isIncoming }) {
            self.incomingCall = incomingCall
        }
    }
}

let firstCamera: VideoDeviceInfo? = self.deviceManager?.getCameraList()![0]
let localVideoStream = LocalVideoStream(camera: firstCamera)
let acceptCallOptions = AcceptCallOptions()
acceptCallOptions!.videoOptions = VideoOptions(localVideoStream:localVideoStream!)
if let incomingCall = CallHandler().incomingCall {
   incomingCall.accept(options: acceptCallOptions,
                          completionHandler: { (error) in
                           if error == nil {
                               print("Incoming call accepted")
                           } else {
                               print("Failed to accept incoming call")
                           }
                       })
} else {
   print("No incoming call found to accept")
}
```

## <a name="push-notification"></a>推送通知

移动推送通知是在移动设备中获得的弹出通知。 对于调用，我们将重点介绍 VoIP (通过 Internet 协议) 推送通知。 我们将为你提供注册推送通知的功能，处理推送通知和注销推送通知。

### <a name="prerequisite"></a>先决条件

- 步骤1： Xcode-> 签名 & 功能-> 添加功能 > "推送通知"
- 步骤2： Xcode & 功能 > 签名-> 添加功能 > "后台模式"
- 步骤3： "背景模式"-> 选择 "语音 over IP" 和 "远程通知"

:::image type="content" source="../media/ios/xcode-push-notification.png" alt-text="显示如何在 Xcode 中添加功能的屏幕截图。" lightbox="../media/ios/xcode-push-notification.png":::

#### <a name="register-for-push-notifications"></a>注册推送通知

若要注册推送通知，请在具有设备注册令牌的 *CallAgent* 实例上调用 registerPushNotification ( # A1。

注册推送通知需要在成功初始化之后调用。 `callAgent`销毁对象时， `logout` 将调用，这会自动取消注册推送通知。


```swift

let deviceToken: Data = pushRegistry?.pushToken(for: PKPushType.voIP)
callAgent.registerPushNotifications(deviceToken: deviceToken,
                completionHandler: { (error) in
    if(error == nil) {
        print("Successfully registered to push notification.")
    } else {
        print("Failed to register push notification.")
    }
})

```

#### <a name="push-notification-handling"></a>推送通知处理
若要接收传入调用推送通知，请使用字典有效负载在 *CallAgent* 实例上调用 *HandlePushNotification ( # B1* 。

```swift

let dictionaryPayload = pushPayload?.dictionaryPayload
callAgent.handlePushNotification(payload: dictionaryPayload, completionHandler: { (error) in
    if (error != nil) {
        print("Handling of push notification failed")
    } else {
        print("Handling of push notification was successful")
    }
})

```
#### <a name="unregister-push-notification"></a>注销推送通知

应用程序可以随时取消注册推送通知。 只需 `unRegisterPushNotification` 在 *CallAgent* 上调用方法。
> [!NOTE]
> 注销时，应用程序不会自动从推送通知取消注册。

```swift

callAgent.unRegisterPushNotifications(completionHandler: { (error) in
    if (error != nil) {
        print("Unregister of push notification failed, please try again")
    } else {
        print("Unregister of push notification was successful")
    }
})

```

## <a name="mid-call-operations"></a>Mid 调用操作

可以在调用过程中执行各种操作来管理与视频和音频相关的设置。

### <a name="mute-and-unmute"></a>静音和取消静音

若要使本地终结点静音或取消静音，你可以使用 `mute` 和 `unmute` 异步 api：

```swift
call!.mute(completionHandler: { (error) in
    if error == nil {
        print("Successfully muted")
    } else {
        print("Failed to mute")
    }
})

```

异步本地取消静音

```swift
call!.unmute(completionHandler:{ (error) in
    if error == nil {
        print("Successfully un-muted")
    } else {
        print("Failed to unmute")
    }
})
```

### <a name="start-and-stop-sending-local-video"></a>开始和停止发送本地视频

若要开始将本地视频发送到呼叫中的其他参与者，请使用 `startVideo` API 并将其传递给 `localVideoStream``camera`

```swift

let firstCamera: VideoDeviceInfo? = self.deviceManager?.getCameraList()![0]
let localVideoStream = LocalVideoStream(camera: firstCamera)

call!.startVideo(stream: localVideoStream) { (error) in
    if (error == nil) {
        print("Local video started successfully")
    }
    else {
        print("Local video failed to start")
    }
}

```

开始发送视频后， `ACSLocalVideoStream` 实例将添加到 `localVideoStreams` 调用实例上：

```swift

call.localVideoStreams[0]

```

异步若要停止本地视频，请 `localVideoStream` 从的调用传递返回的 `call.startVideo` ：

```swift

call!.stopVideo(stream: localVideoStream) { (error) in
    if (error == nil) {
        print("Local video stopped successfully")
    } else {
        print("Local video failed to stop")
    }
}

```

## <a name="remote-participants-management"></a>远程参与者管理

所有远程参与者均由类型表示 `ACSRemoteParticipant` ，可通过 `remoteParticipants` 调用实例上的集合获得：

### <a name="list-participants-in-a-call"></a>列出调用中的参与者

```swift

call.remoteParticipants

```

### <a name="remote-participant-properties"></a>远程参与者属性

```swift

// [ACSRemoteParticipantDelegate] delegate - an object you provide to receive events from this ACSRemoteParticipant instance
var remoteParticipantDelegate = remoteParticipant.delegate

// [CommunicationIdentifier] identity - same as the one used to provision token for another user
var identity = remoteParticipant.identity

// ACSParticipantStateIdle = 0, ACSParticipantStateEarlyMedia = 1, ACSParticipantStateConnecting = 2, ACSParticipantStateConnected = 3, ACSParticipantStateOnHold = 4, ACSParticipantStateInLobby = 5, ACSParticipantStateDisconnected = 6
var state = remoteParticipant.state

// [ACSError] callEndReason - reason why participant left the call, contains code/subcode/message
var callEndReason = remoteParticipant.callEndReason

// [Bool] isMuted - indicating if participant is muted
var isMuted = remoteParticipant.isMuted

// [Bool] isSpeaking - indicating if participant is currently speaking
var isSpeaking = remoteParticipant.isSpeaking

// ACSRemoteVideoStream[] - collection of video streams this participants has
var videoStreams = remoteParticipant.videoStreams // [ACSRemoteVideoStream, ACSRemoteVideoStream, ...]

```

### <a name="add-a-participant-to-a-call"></a>向呼叫添加参与者

若要将参与者添加到呼叫 (可以调用的用户或电话号码) `addParticipant` 。 这会以同步方式返回远程参与者实例。

```swift

let remoteParticipantAdded: RemoteParticipant = call.add(participant: CommunicationUser(identifier: "userId"))

```

### <a name="remove-a-participant-from-a-call"></a>从调用中删除参与者
若要从调用中删除参与者 (用户或电话号码) 可以调用  `removeParticipant` API。 这会以异步方式解析。

```swift

call!.remove(participant: remoteParticipantAdded) { (error) in
    if (error == nil) {
        print("Successfully removed participant")
    } else {
        print("Failed to remove participant")
    }
}

```

## <a name="render-remote-participant-video-streams"></a>呈现远程参与者视频流

远程参与者可能会在呼叫期间启动视频或屏幕共享。

### <a name="handle-remote-participant-videoscreen-sharing-streams"></a>处理远程参与者视频/屏幕共享流

若要列出远程参与者的流，请检查 `videoStreams` 集合：

```swift

var remoteParticipantVideoStream = call.remoteParticipants[0].videoStreams[0]

```

### <a name="remote-video-stream-properties"></a>远程视频流属性

```swift

var type: MediaStreamType = remoteParticipantVideoStream.type // 'ACSMediaStreamTypeVideo'

var isAvailable: Bool = remoteParticipantVideoStream.isAvailable // indicates if remote stream is available

var id: Int = remoteParticipantVideoStream.id // id of remoteParticipantStream

```

### <a name="render-remote-participant-stream"></a>呈现远程参与者流

若要开始呈现远程参与者流：

```swift

let renderer: Renderer? = Renderer(remoteVideoStream: remoteParticipantVideoStream)
let targetRemoteParticipantView: RendererView? = renderer?.createView(with: RenderingOptions(scalingMode: ScalingMode.crop))
// To update the scaling mode later
targetRemoteParticipantView.update(scalingMode: ScalingMode.fit)

```

### <a name="remote-video-renderer-methods-and-properties"></a>远程视频呈现器方法和属性

```swift
// [Bool] isRendering - indicating if stream is being rendered
remoteVideoRenderer.isRendering()
// [Synchronous] dispose() - dispose renderer and all `RendererView` associated with this renderer. To be called when you have removed all associated views from the UI.
remoteVideoRenderer.dispose()
```

## <a name="device-management"></a>设备管理

`DeviceManager` 允许你枚举可用于发送音频/视频流的调用中的本地设备。 它还允许您请求用户访问麦克风/照相机的权限。 可以访问 `deviceManager` `callClient` 对象：

```swift

self.callClient!.getDeviceManager(
    completionHandler: { (deviceManager, error) in
        if (error == nil) {
            print("Got device manager instance")
            self.deviceManager = deviceManager
        } else {
            print("Failed to get device manager instance")
        }
    })
```

### <a name="enumerate-local-devices"></a>枚举本地设备

若要访问本地设备，可以在设备管理器上使用枚举方法。 枚举是同步操作。

```swift
// enumerate local cameras
var localCameras = deviceManager.getCameraList() // [ACSVideoDeviceInfo, ACSVideoDeviceInfo...]
// enumerate local cameras
var localMicrophones = deviceManager.getMicrophoneList() // [ACSAudioDeviceInfo, ACSAudioDeviceInfo...]
// enumerate local cameras
var localSpeakers = deviceManager.getSpeakerList() // [ACSAudioDeviceInfo, ACSAudioDeviceInfo...]
``` 

### <a name="set-default-microphonespeaker"></a>设置默认麦克风/发言人

使用设备管理器，可以设置一个默认设备，该设备将在启动呼叫时使用。 如果未设置堆栈默认值，则通信服务将回退到 OS 默认值。

```swift
// get first microphone
var firstMicrophone = self.deviceManager!.getMicrophoneList()![0]
// [Synchronous] set microphone
deviceManager.setMicrophone(microphoneDevice: firstMicrophone)
// get first speaker
var firstSpeaker = self.deviceManager!.getSpeakerList()![0]
// [Synchronous] set speaker
deviceManager.setSpeaker(speakerDevice: firstSpeaker)
```

### <a name="local-camera-preview"></a>本地相机预览

你可以使用 `ACSRenderer` 从本地相机开始呈现流。 此流不会发送给其他参与者;这是一个本地预览源。 这是一个异步操作。

```swift

let camera: VideoDeviceInfo = self.deviceManager!.getCameraList()![0]
let localVideoStream: LocalVideoStream = LocalVideoStream(camera: camera)
let renderer: Renderer = Renderer(localVideoStream: localVideoStream)
self.view = renderer!.createView()

```

### <a name="local-camera-preview-properties"></a>本地相机预览属性

呈现器包含一组属性和方法，使你可以控制呈现：

```swift

// Constructor can take in ACSLocalVideoStream or ACSRemoteVideoStream
let localRenderer = Renderer(localVideoStream:localVideoStream)
let remoteRenderer = Renderer(remoteVideoStream:remoteVideoStream)

// [ACSStreamSize] size of the rendering view
localRenderer.size

// [ACSRendererDelegate] an object you provide to receive events from this ACSRenderer instance
localRenderer.delegate

// [Synchronous] create view
try! localRenderer.createView()

// [Synchronous] create view with rendering options
try! localRenderer.createView(with: RenderingOptions(scalingMode: ScalingMode.fit))

// [Synchronous] dispose rendering view
localRenderer.dispose()

```

## <a name="eventing-model"></a>事件模型

您可以订阅大多数属性和集合，以便在值发生更改时收到通知。

### <a name="properties"></a>属性
订阅 `property changed` 事件：

```swift
call.delegate = self
// Get the property of the call state by doing get on the call's state member
public func onCallStateChanged(_ call: Call!,
                               args: PropertyChangedEventArgs!)
{
    print("Callback from SDK when the call state changes, current state: " + call.state.rawValue)
}

 // to unsubscribe
 self.call.delegate = nil

```

### <a name="collections"></a>集合
订阅 `collection updated` 事件：

```swift
call.delegate = self
// Collection contains the streams that were added or removed only
public func onLocalVideoStreamsChanged(_ call: Call!,
                                       args: LocalVideoStreamsUpdatedEventArgs!)
{
    print(args.addedStreams.count)
    print(args.removedStreams.count)
}
// to unsubscribe
self.call.delegate = nil
```
