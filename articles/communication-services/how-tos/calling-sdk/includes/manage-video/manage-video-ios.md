---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 4c95736394cd112daa48f3bfa6a47ae4ba0f147d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "128699158"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-ios.md)]

## <a name="manage-devices"></a>デバイスの管理
通話でのビデオの使用を開始するには、デバイスを管理する方法を知る必要があります。 デバイスを使用すると、どのデバイスで通話にオーディオとビデオを送信するかを制御できます。

`DeviceManager` を使用すると、オーディオまたは動画のストリームを送信するために通話内で使用できるローカル デバイスを列挙できます。 また、マイクやカメラにアクセスするための許可をユーザーに求めることもできます。 `callClient` オブジェクトの `deviceManager` にアクセスできます。

```swift
self.callClient!.getDeviceManager { (deviceManager, error) in
        if (error == nil) {
            print("Got device manager instance")
            self.deviceManager = deviceManager
        } else {
            print("Failed to get device manager instance")
        }
    }
```

### <a name="enumerate-local-devices"></a>ローカル デバイスを列挙する

ローカル デバイスにアクセスする場合は、デバイス マネージャーで列挙メソッドを使用できます。 列挙は同期アクションです。

```swift
// enumerate local cameras
var localCameras = deviceManager.cameras // [VideoDeviceInfo, VideoDeviceInfo...]
```

### <a name="get-a-local-camera-preview"></a>ローカル カメラのプレビューを取得する

`Renderer` を使用して、ローカル カメラからのストリームのレンダリングを開始できます。 このストリームは、他の参加者には送信されません。ローカル プレビュー フィードです。 これは、非同期アクションです。

```swift
let camera: VideoDeviceInfo = self.deviceManager!.cameras.first!
let localVideoStream = LocalVideoStream(camera: camera)
let localRenderer = try! VideoStreamRenderer(localVideoStream: localVideoStream)
self.view = try! localRenderer.createView()
```

### <a name="get-local-camera-preview-properties"></a>ローカル カメラのプレビューのプロパティを取得する

レンダラーには、レンダリングを制御できるプロパティとメソッドのセットが用意されています。

```swift
// Constructor can take in LocalVideoStream or RemoteVideoStream
let localRenderer = VideoStreamRenderer(localVideoStream:localVideoStream)
let remoteRenderer = VideoStreamRenderer(remoteVideoStream:remoteVideoStream)

// [StreamSize] size of the rendering view
localRenderer.size

// [VideoStreamRendererDelegate] an object you provide to receive events from this Renderer instance
localRenderer.delegate

// [Synchronous] create view
try! localRenderer.createView()

// [Synchronous] create view with rendering options
try! localRenderer!.createView(withOptions: CreateViewOptions(scalingMode: ScalingMode.fit))

// [Synchronous] dispose rendering view
localRenderer.dispose()
```

## <a name="place-a-11-call-with-video"></a>動画を使用して 1:1 の通話を行う
デバイス マネージャーのインスタンスを取得する場合は、[デバイスの管理](#manage-devices)に関するセクションを参照してください。

```swift
let firstCamera = self.deviceManager!.cameras.first
self.localVideoStreams = [LocalVideoStream]()
self.localVideoStreams!.append(LocalVideoStream(camera: firstCamera!))
let videoOptions = VideoOptions(localVideoStreams: self.localVideoStreams!)

let startCallOptions = StartCallOptions()
startCallOptions.videoOptions = videoOptions

let callee = CommunicationUserIdentifier('UserId')
self.callAgent?.startCall(participants: [callee], options: startCallOptions) { (call, error) in
    if error == nil {
        print("Successfully started outgoing video call")
        self.call = call
    } else {
        print("Failed to start outgoing video call")
    }
}
```

## <a name="render-remote-participant-video-streams"></a>リモート参加者の動画ストリームをレンダリングする

リモート参加者は、通話中に動画または画面共有を開始できます。

### <a name="handle-video-sharing-or-screen-sharing-streams-of-remote-participants"></a>リモート参加者の動画共有または画面共有ストリームを処理する

リモート参加者のストリームを一覧表示するには、`videoStreams` コレクションを調べます。

```swift
var remoteParticipantVideoStream = call.remoteParticipants[0].videoStreams[0]
```

### <a name="get-remote-video-stream-properties"></a>リモート動画ストリームのプロパティを取得する

```swift
var type: MediaStreamType = remoteParticipantVideoStream.type // 'MediaStreamTypeVideo'
var isAvailable: Bool = remoteParticipantVideoStream.isAvailable // indicates if remote stream is available
var id: Int = remoteParticipantVideoStream.id // id of remoteParticipantStream
```

### <a name="render-remote-participant-streams"></a>リモート参加者ストリームをレンダリングする

リモート参加者ストリームのレンダリングを開始するには、次のコードを使用します。

```swift
let renderer = VideoStreamRenderer(remoteVideoStream: remoteParticipantVideoStream)
let targetRemoteParticipantView = renderer?.createView(withOptions: CreateViewOptions(scalingMode: ScalingMode.crop))
// To update the scaling mode later
targetRemoteParticipantView.update(scalingMode: ScalingMode.fit)
```

### <a name="get-remote-video-renderer-methods-and-properties"></a>リモート動画レンダラーのメソッドとプロパティを取得する

```swift
// [Synchronous] dispose() - dispose renderer and all `RendererView` associated with this renderer. To be called when you have removed all associated views from the UI.
remoteVideoRenderer.dispose()
```