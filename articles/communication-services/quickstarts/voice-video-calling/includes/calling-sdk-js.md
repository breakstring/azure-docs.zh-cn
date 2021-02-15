---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: 7d391998e7f20cff0f77f6aab7938bc375f75c9e
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99616489"
---
## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- 已部署的通信服务资源。 [创建通信服务资源](../../create-communication-resource.md)。
- 用于启用呼叫客户端的`User Access Token`。 详细了解[如何获取`User Access Token`](../../access-tokens.md)
- 可选：完成快速入门[添加对应用程序调用](../getting-started-with-calling.md)的入门教程

## <a name="setting-up"></a>设置

### <a name="install-the-client-library"></a>安装客户端库

使用 `npm install` 命令安装适用于 JavaScript 的 Azure 通信服务调用和常见客户端库。

```console
npm install @azure/communication-calling --save
```

## <a name="object-model"></a>对象模型

以下类和接口处理 Azure 通信服务呼叫客户端库的某些主要功能：

| 名称                             | 说明                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | CallClient 是呼叫客户端库的主入口点。                                                                       |
| CallAgent                        | CallAgent 用于启动和管理呼叫。                                                                                            |
| DeviceManager                    | DeviceManager 用于管理媒体设备                                                                                           |
| AzureCommunicationTokenCredential | AzureCommunicationTokenCredential 类实现 CommunicationTokenCredential 接口，该接口用于实例化 CallAgent。 |


## <a name="initialize-the-callclient-create-callagent-and-access-devicemanager"></a>初始化 CallClient、create CallAgent 和 access DeviceManager

实例化一个新 `CallClient` 实例。 您可以使用记录器实例等自定义选项对其进行配置。
实例 `CallClient` 化后，可以 `CallAgent` 通过对实例调用方法来创建实例 `createCallAgent` `CallClient` 。 这会以异步方式返回 `CallAgent` 实例对象。
`createCallAgent`方法采用 `CommunicationTokenCredential` 作为参数，该参数接受[用户访问令牌](https://docs.microsoft.com/azure/communication-services/quickstarts/access-tokens)。
若要访问 `DeviceManager` callAgent 实例，必须先创建。 然后，可以 `getDeviceManager` 在实例上使用方法 `CallClient` 获取 DeviceManager。

```js
const userToken = '<user token>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationTokenCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'optional ACS user name'});
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-an-outgoing-call"></a>发出传出呼叫

若要创建并启动呼叫，需要在 CallAgent 上使用其中一个 Api，并提供通过通信服务管理客户端库创建的用户。

调用创建和启动是同步的。 调用实例允许您订阅调用事件。

## <a name="place-a-call"></a>拨打电话

### <a name="place-a-11-call-to-a-user-or-pstn"></a>向用户或 PSTN 发出1:1 调用
若要调用另一个通信服务用户，请 `call` 在上调用方法 `callAgent` 并传递被调用方的 CommunicationUserIdentifier：

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const oneToOneCall = callAgent.call([userCallee]);
```

若要对 PSTN 进行调用，请 `call` 在上调用方法 `callAgent` 并传递被调用方的 PhoneNumberIdentifier。
必须将通信服务资源配置为允许 PSTN 调用。
调用 PSTN 号码时，必须指定备用呼叫方 ID。
```js
const pstnCalee = { phoneNumber: '<ACS_USER_ID>' }
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const oneToOneCall = callAgent.call([pstnCallee], {alternateCallerId});
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>将1： n 调用与用户和 PSTN 一起使用
```js
const userCallee = { communicationUserId: <ACS_USER_ID> }
const pstnCallee = { phoneNumber: <PHONE_NUMBER>};
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const groupCall = callAgent.call([userCallee, pstnCallee], {alternateCallerId});
```

### <a name="place-a-11-call-with-video-camera"></a>使用摄像机发出1:1 呼叫
> [!WARNING]
> 当前可以有一个以上的传出本地视频流。
若要进行视频呼叫，必须使用 deviceManager API 枚举本地相机 `getCameraList` 。
选择所需的照相机后，使用它来构造 `LocalVideoStream` 实例，并将其 `videoOptions` 作为数组中的项传入 `localVideoStream` 到 `call` 方法。
呼叫连接后，它会自动开始将视频流从所选照相机发送到其他 () 的参与者。 这也适用于调用。接受 ( # A1 视频选项和 CallAgent ( # A3 视频选项。
```js
const deviceManager = await callClient.getDeviceManager();
const videoDeviceInfo = deviceManager.getCameraList()[0];
localVideoStream = new LocalVideoStream(videoDeviceInfo);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const call = callAgent.call(['acsUserId'], placeCallOptions);

```

### <a name="join-a-group-call"></a>加入群组通话
若要启动新组调用或加入正在进行的组调用，请使用 "join" 方法并向属性传递对象 `groupId` 。 该值必须是 GUID。
```js

const context = { groupId: <GUID>}
const call = callAgent.join(context);

```
### <a name="join-a-teams-meeting"></a>加入团队会议
若要加入团队会议，请使用 "联接" 方法并传递会议链接或会议的坐标
```js
// Join using meeting link
const locator = { meetingLink: <meeting link>}
const call = callAgent.join(locator);

// Join using meeting coordinates
const locator = {
    threadId: <thread id>,
    organizerId: <organizer id>,
    tenantId: <tenant id>,
    messageId: <message id>
}
const call = callAgent.join(locator);
```

## <a name="receiving-an-incoming-call"></a>接收传入呼叫

`CallAgent` `incomingCall` 当登录标识正在接收传入呼叫时，实例将发出事件。 若要侦听此事件，请按以下方式进行订阅：

```js
const incomingCallHander = async (args: { incomingCall: IncomingCall }) => {
    //accept the call
    var call = await incomingCall.accept();

    //reject the call
    incomingCall.reject();
};
callAgentInstance.on('incomingCall', incomingCallHander);
```

此 `incomingCall` 事件将提供 `IncomingCall` 可接受或拒绝调用的实例。


## <a name="call-management"></a>呼叫管理

你可以访问调用属性并在调用以管理与视频和音频相关的设置期间执行各种操作。

### <a name="call-properties"></a>调用属性
* 获取此调用 (字符串) 的唯一 ID。
```js

const callId: string = call.id;

```

* 若要了解调用中的其他参与者，请检查 `remoteParticipant` 该实例上的集合 `call` 。 数组包含 list `RemoteParticipant` 对象
```js
const remoteParticipants = call.remoteParticipants;
```

* 如果调用是传入的，则为调用方的标识。 标识是 `CommunicationIdentifier` 类型之一
```js

const callerIdentity = call.callerInfo.identity;

```

* 获取调用的状态。
```js

const callState = call.state;

```
这会返回表示调用的当前状态的字符串：
* "无"-初始调用状态
* "传入"-表示调用是传入的，它必须接受或拒绝
* "正在连接"-在调用或接受调用后初始转换状态
* "震铃"-对于传出呼叫，指示呼叫正在拨打远程参与者，并在其两侧 "传入"
* "EarlyMedia"-表示在连接呼叫之前播放公告的状态
* "已连接"-调用已连接
* "保持"-呼叫处于暂挂状态，本地终结点与远程参与者之间没有媒体流动 (s) 
* 在调用进入 "断开连接" 状态之前，"断开"-转换状态
* "Disconnected"-最终调用状态
  * 如果网络连接丢失，在大约2分钟后状态将变为 "已断开连接"。

* 若要查看给定调用结束的原因，请检查 `callEndReason` 属性。
```js

const callEndReason = call.callEndReason;
// callEndReason.code (number) code associated with the reason
// callEndReason.subCode (number) subCode associated with the reason
```

* 若要了解当前调用是否为传入或传出调用，请检查 `direction` 属性，返回 `CallDirection` 。
```js
const isIncoming = call.direction == 'Incoming';
const isOutgoing = call.direction == 'Outgoing';
```

*  若要检查当前麦克风是否静音，请检查 `muted` 属性，返回 `Boolean` 。
```js

const muted = call.isMicrophoneMuted;

```

* 若要查看是否正在从给定的终结点发送屏幕共享流，请检查 `isScreenSharingOn` 属性，返回 `Boolean` 。
```js

const isScreenSharingOn = call.isScreenSharingOn;

```

* 若要检查活动视频流，请检查 `localVideoStreams` 集合，其中包含 `LocalVideoStream` 对象
```js

const localVideoStreams = call.localVideoStreams;

```

### <a name="call-ended-event"></a>Call 结束事件

`Call` `callEnded` 当调用结束时，实例将发出事件。 若要按以下方式侦听此事件：

```js
const callEndHander = async (args: { callEndReason: CallEndReason }) => {
    console.log(args.callEndReason)
};

call.on('callEnded', callEndHander);
```

### <a name="mute-and-unmute"></a>静音和取消静音

若要使本地终结点静音或取消静音，你可以使用 `mute` 和 `unmute` 异步 api：

```js

//mute local device 
await call.mute();

//unmute local device 
await call.unmute();

```

### <a name="start-and-stop-sending-local-video"></a>开始和停止发送本地视频


若要开始视频，必须使用对象上的方法来枚举摄像机 `getCameraList` `deviceManager` 。 然后创建一个新实例，将 `LocalVideoStream` 所需的照相机 `startVideo` 作为参数传递给方法：


```js
const localVideoStream = new LocalVideoStream(videoDeviceInfo);
await call.startVideo(localVideoStream);

```

成功开始发送视频后， `LocalVideoStream` 实例将添加到 `localVideoStreams` 调用实例上的集合中。

```js

call.localVideoStreams[0] === localVideoStream;

```

若要停止本地视频，请 `localVideoStream` 在集合中传递可用的实例 `localVideoStreams` ：

```js

await call.stopVideo(localVideoStream);

```

通过对实例调用来发送视频时，可以切换到不同的相机设备 `switchSource` `localVideoStream` ：

```js
const source callClient.getDeviceManager().getCameraList()[1];
localVideoStream.switchSource(source);

```

## <a name="remote-participants-management"></a>远程参与者管理

所有远程参与者均由 `RemoteParticipant` 类型表示，并可通过 `remoteParticipants` 集合在调用实例上使用。

### <a name="list-participants-in-a-call"></a>列出调用中的参与者
`remoteParticipants`集合返回给定调用中的远程参与者列表：

```js

call.remoteParticipants; // [remoteParticipant, remoteParticipant....]

```

### <a name="remote-participant-properties"></a>远程参与者属性
远程参与者具有与之关联的一组属性和集合
#### <a name="communicationidentifier"></a>CommunicationIdentifier
获取此远程参与者的标识符。
```js
const identifier = remoteParticipant.identifier;
```
它可以是 "CommunicationIdentifier" 类型之一：
  * {communicationUserId： "<ACS_USER_ID" >}-表示 ACS 用户的对象
  * {phoneNumber： "<> '}-以164格式表示电话号码的对象
  * {microsoftTeamsUserId： "<TEAMS_USER_ID>"，isAnonymous？： boolean; cloud？： "public" |"dod" |"gcch"-表示团队用户的对象

#### <a name="state"></a>状态
获取此远程参与者的状态。
```js

const state = remoteParticipant.state;
```
状态可以是
* "空闲"-初始状态
* 参与者连接到呼叫时的 "正在连接"-转换状态
* "已连接"-参与者已连接到呼叫
* "保持"-参与者处于暂停状态
* "EarlyMedia"-在参与者连接到呼叫之前播放公告
* "断开连接"-最终状态-参与者已与呼叫断开连接
  * 如果远程参与者丢失了其网络连接，则在大约2分钟后远程参与者状态将变为 "已断开连接"。

#### <a name="call-end-reason"></a>调用结束原因
若要了解参与者离开呼叫的原因，请检查 `callEndReason` 属性：
```js
const callEndReason = remoteParticipant.callEndReason;
// callEndReason.code (number) code associated with the reason
// callEndReason.subCode (number) subCode associated with the reason
```
#### <a name="is-muted"></a>静音
若要检查此远程参与者是否已静音，请检查 `isMuted` 属性，返回 `Boolean`
```js
const isMuted = remoteParticipant.isMuted;
```
#### <a name="is-speaking"></a>正在说话
若要检查此远程参与者是否正在通话，请检查 `isSpeaking` 它返回的属性 `Boolean`
```js
const isSpeaking = remoteParticipant.isSpeaking;
```

#### <a name="video-streams"></a>视频流
若要检查给定参与者在此调用中发送的所有视频流，请选中 `videoStreams` "集合"，其中包含 `RemoteVideoStream` 对象
```js

const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]

```


### <a name="add-a-participant-to-a-call"></a>向呼叫添加参与者

若要将参与者添加到呼叫 (可以调用的用户或电话号码) `addParticipant` 。
提供一个 "标识符" 类型。
这会以同步方式返回远程参与者实例。

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier, {alternateCallerId: '<Alternate Caller ID>'});
```

### <a name="remove-participant-from-a-call"></a>从呼叫中删除参与者

若要从调用中删除参与者 (可以调用的用户或电话号码) `removeParticipant` 。
必须传递 "Identifier" 类型之一，这会在从调用中删除参与者后异步解决。
还将从集合中删除该参与者 `remoteParticipants` 。

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
await call.removeParticipant(userIdentifier);
await call.removeParticipant(pstnIdentifier);
```

## <a name="render-remote-participant-video-streams"></a>呈现远程参与者视频流

若要列出视频流和远程参与者的屏幕共享流，请检查 `videoStreams` 集合：

```js
const remoteVideoStream: RemoteVideoStream = call.remoteParticipants[0].videoStreams[0];
const streamType: MediaStreamType = remoteVideoStream.type;
```
 
若要呈现 `RemoteVideoStream` ，必须订阅 `isAvailableChanged` 事件。
如果该 `isAvailable` 属性更改为 `true` ，则远程参与者将发送流。
一旦发生这种情况，请创建的新实例 `Renderer` ，然后 `RendererView` 使用异步方法创建新的实例 `createView` 。  然后，可以附加 `view.target` 到任何 UI 元素。
当远程流的可用性发生变化时，你可以选择销毁整个呈现器、特定 `RendererView` 或保留它们，但这将导致显示空白的视频帧。

```js
let renderer: Renderer = new Renderer(remoteParticipantStream);
const displayVideo = () => {
    const view = await renderer.createView();
    htmlElement.appendChild(view.target);
}
remoteParticipantStream.on('availabilityChanged', async () => {
    if (remoteParticipantStream.isAvailable) {
        displayVideo();
    } else {
        renderer.dispose();
    }
});
if (remoteParticipantStream.isAvailable) {
    displayVideo();
}
```

### <a name="remote-video-stream-properties"></a>远程视频流属性
远程视频流具有以下属性：

* `Id` -远程视频流的 ID
```js
const id: number = remoteVideoStream.id;
```

* `StreamSize` -size ( 远程视频流的宽度/高度 ) 
```js
const size: {width: number; height: number} = remoteVideoStream.size;
```

* `MediaStreamType` -可以是 "视频" 或 "ScreenSharing"
```js
const type: MediaStreamType = remoteVideoStream.type;
```
* `isAvailable` -指示远程参与者终结点是否正在主动发送流
```js
const type: boolean = remoteVideoStream.isAvailable;
```

### <a name="renderer-methods-and-properties"></a>呈现器方法和属性

* 创建一个 `RendererView` 可在以后附加到应用程序 UI 以呈现远程视频流的实例。
```js
renderer.createView()
```

* 释放呈现器和所有关联的 `RendererView` 实例。
```js
renderer.dispose()
```


### <a name="rendererview-methods-and-properties"></a>RendererView 方法和属性
创建时， `RendererView` 可以指定 `scalingMode` 和 `mirrored` 属性。
如果指定了，缩放模式可以是 "拉伸"、"裁剪" 或 "拟合" `Mirrored` 。呈现的流将垂直翻转。

```js
const rendererView: RendererView = renderer.createView({ scalingMode, mirrored });
```
任何给定 `RendererView` 实例都具有一个 `target` 表示呈现图面的属性。 此操作必须在应用程序 UI 中附加：
```js
document.body.appendChild(rendererView.target);
```

稍后可以通过调用方法来更新缩放模式 `updateScalingMode` 。
```js
view.updateScalingMode('Crop')
```

## <a name="device-management"></a>设备管理

`DeviceManager` 允许你枚举可在调用中用于传输音频/视频流的本地设备。 它还允许你请求用户使用本机浏览器 API 访问其麦克风和照相机的权限。

可以 `deviceManager` 通过调用 `callClient.getDeviceManager()` 方法访问。
> [!WARNING]
> 当前 `callAgent` 必须首先实例化对象，才能获得对 DeviceManager 的访问权限

```js

const deviceManager = await callClient.getDeviceManager();

```

### <a name="enumerate-local-devices"></a>枚举本地设备

若要访问本地设备，可以在 Device Manager 上使用枚举方法。 枚举是同步操作。

```js

//  Get a list of available video devices for use.
const localCameras = deviceManager.getCameraList(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = deviceManager.getMicrophoneList(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = deviceManager.getSpeakerList(); // [AudioDeviceInfo, AudioDeviceInfo...]

```

### <a name="set-default-microphonespeaker"></a>设置默认麦克风/发言人

使用设备管理器，可以设置一个默认设备，该设备将在启动呼叫时使用。
如果未设置客户端默认值，则通信服务将回退到 OS 默认值。

```js

// Get the microphone device that is being used.
const defaultMicrophone = deviceManager.getMicrophone();

// Set the microphone device to use.
await deviceManager.setMicrophone(AudioDeviceInfo);

// Get the speaker device that is being used.
const defaultSpeaker = deviceManager.getSpeaker();

// Set the speaker device to use.
await deviceManager.setSpeaker(AudioDeviceInfo);

```

### <a name="local-camera-preview"></a>本地相机预览

你可以使用 `DeviceManager` 和 `Renderer` 从本地相机开始呈现流。 此流不会发送给其他参与者;这是一个本地预览源。 这是一个异步操作。

```js
const localVideoDevice = deviceManager.getCameraList()[0];
const localCameraStream = new LocalVideoStream(localVideoDevice);
const renderer = new Renderer(localCameraStream);
const view = await renderer.createView();
document.body.appendChild(view.target);

```

### <a name="request-permission-to-cameramicrophone"></a>向照相机/麦克风请求权限

提示用户使用以下项授予相机/麦克风权限：

```js
const result = await deviceManager.askDevicePermission(audio: true, video: true);
```
这会以异步方式解析，该对象指示是否 `audio` `video` 授予了和权限：
```js
console.log(result.audio);
console.log(result.video);
```

可以通过调用来检查给定类型的当前权限状态 `getPermissionState` ：

```js

const result = deviceManager.getPermissionState('Microphone'); // for microphone permission state
const result = deviceManager.getPermissionState('Camera'); // for camera permission state

console.log(result); // 'Granted' | 'Denied' | 'Prompt' | 'Unknown';

```

## <a name="call-recording-management"></a>调用录制管理

呼叫记录是核心 API 的扩展功能 `Call` 。 首先需要获取录制功能 API 对象：

```js
const callRecordingApi = call.api(Features.Recording);
```

然后，若要检查是否正在记录调用，请检查的 `isRecordingActive` 属性 `callRecordingApi` ，返回 `Boolean` 。

```js
const isResordingActive = callRecordingApi.isRecordingActive;
```

你还可以订阅记录更改：

```js
const isRecordingActiveChangedHandler = () => {
  console.log(callRecordingApi.isRecordingActive);
};

callRecordingApi.on('isRecordingActiveChanged', isRecordingActiveChangedHandler);
               
```

## <a name="call-transfer-management"></a>调用传输管理

调用传输是核心 API 的扩展功能 `Call` 。 首先需要获取传输功能 API 对象：

```js
const callTransferApi = call.api(Features.Transfer);
```

调用传输涉及三方 *出让人*、 *transferee* 和 *传输目标*。 传输流的工作方式如下：

1. *出让人* 和 *transferee* 之间已有连接的调用
2. *出让人* 决定将呼叫转移 (*transferee*  ->  *转接目标*) 
3. *出让人* 调用 `transfer` API
4. *transferee* 决定是 `accept` `reject` 通过事件 *传输目标* 还是传输请求 `transferRequested` 。
5. 仅当 *transferee* 执行了传输请求时，*传输目标* 才会收到传入呼叫 `accept`

### <a name="transfer-terminology"></a>传输术语

- 出让人-启动传输请求的用户
- Transferee-由出让人传输到传输目标的用户
- 传输目标-要传输到的目标

若要传输当前调用，可以使用 `transfer` 同步 API。 `transfer` 采用可选 `TransferCallOptions` 的，它允许您设置 `disableForwardingAndUnanswered` 标志：

- `disableForwardingAndUnanswered` = false-如果 *传输目标* 未应答传输呼叫，则会跟随 *传输目标* 转发和未应答的设置
- `disableForwardingAndUnanswered` = true-如果 *传输目标* 未应答传输调用，则传输尝试将结束

```js
// transfer target can be ACS user
const id = { communicationUserId: <ACS_USER_ID> };
```

```js
// call transfer API
const transfer = callTransferApi.transfer({targetParticipant: id});
```

传输允许您订阅 `transferStateChanged` 和 `transferRequested` 事件。 `transferRequsted` 事件来自 `call` 实例、 `transferStateChanged` 事件和传输， `state` 来自 `error` `transfer` 实例

```js
// transfer state
const transferState = transfer.state; // None | Transferring | Transferred | Failed

// to check the transfer failure reason
const transferError = transfer.error; // transfer error code that describes the failure if transfer request failed
```

Transferee 可以 `transferRequested` 通过 `accept()` 或 `reject()` 在中接受或拒绝由出让人启动的传输请求 `transferRequestedEventArgs` 。 你可以访问 `targetParticipant` 中的信息、 `accept` 和 `reject` 方法 `transferRequestedEventArgs` 。

```js
// Transferee to accept the transfer request
callTransferApi.on('transferRequested', args => {
  args.accept();
});

// Transferee to reject the transfer request
callTransferApi.on('transferRequested', args => {
  args.reject();
});
```

## <a name="eventing-model"></a>事件模型

您可以订阅大多数属性和集合，以便在值发生更改时收到通知。

### <a name="properties"></a>属性
订阅 `property changed` 事件：

```js

const eventHandler = () => {
    // check current value of a property, value is not passed to callback
    console.log(object.property);
};
object.on('propertyNameChanged',eventHandler);

// To unsubscribe:

object.off('propertyNameChanged',eventHandler);

```

### <a name="collections"></a>集合
订阅 `collection updated` 事件：

```js

const eventHandler = (e) => {
    // check added elements
    console.log(e.added);
    // check removed elements
    console.log(e.removed);
};
object.on('collectionNameUpdated',eventHandler);

// To unsubscribe:

object.off('collectionNameUpdated',eventHandler);

```
