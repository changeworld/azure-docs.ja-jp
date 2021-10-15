---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 7d77857152ad381bb12e5bacc9889c10ca633948
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129584998"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-android.md)]

## <a name="device-management"></a>デバイス管理
通話でのビデオの使用を開始するには、デバイスを管理する方法を知る必要があります。 デバイスを使用すると、どのデバイスで通話にオーディオとビデオを送信するかを制御できます。

`DeviceManager` を使用すると、オーディオと動画のストリームを送信する呼び出しで使用できるローカル デバイスを列挙できます。 また、ネイティブ ブラウザー API を使用してマイクやカメラにアクセスするために、ユーザーにアクセス許可を要求することもできます。

`callClient.getDeviceManager()` メソッドを呼び出すことによって `deviceManager` にアクセスできます。

```java
Context appContext = this.getApplicationContext();
DeviceManager deviceManager = callClient.getDeviceManager(appContext).get();
```

### <a name="enumerate-local-devices"></a>ローカル デバイスを列挙する

ローカル デバイスにアクセスするには、デバイス マネージャーで列挙メソッドを使用します。 列挙は同期アクションです。

```java
//  Get a list of available video devices for use.
List<VideoDeviceInfo> localCameras = deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]
```

### <a name="local-camera-preview"></a>ローカル カメラのプレビュー

`DeviceManager` と `Renderer` を使用して、ローカル カメラからのストリームのレンダリングを開始できます。 このストリームは、他の参加者には送信されません。ローカル プレビュー フィードです。 これは、非同期アクションです。

```java
VideoDeviceInfo videoDevice = <get-video-device>; // See the `Enumerate local devices` topic above
Context appContext = this.getApplicationContext();

LocalVideoStream currentVideoStream = new LocalVideoStream(videoDevice, appContext);

LocalVideoStream[] localVideoStreams = new LocalVideoStream[1];
localVideoStreams[0] = currentVideoStream;

VideoOptions videoOptions = new VideoOptions(localVideoStreams);

RenderingOptions renderingOptions = new RenderingOptions(ScalingMode.Fit);
VideoStreamRenderer previewRenderer = new VideoStreamRenderer(currentVideoStream, appContext);

VideoStreamRendererView uiView = previewRenderer.createView(renderingOptions);

// Attach the uiView to a viewable location on the app at this point
layout.addView(uiView);
```

## <a name="place-a-11-call-with-video-camera"></a>ビデオ カメラを使用して 1:1 の通話を行う
> [!WARNING]
> 現在、サポートされている発信ローカル動画ストリームは 1 つだけです。動画を使用して通話を行うには、`deviceManager` `getCameras` API を使用して、ローカル カメラを列挙する必要があります。
目的のカメラを選択したら、それを使用して `LocalVideoStream` インスタンスを構築し、それを `call` メソッドへの `localVideoStream` 配列内の項目として、`videoOptions` に渡します。
通話が接続されると、選択したカメラから他の参加者への動画ストリームの送信が自動的に開始されます。

> [!NOTE]
> プライバシーに対する懸念から、動画がローカルでプレビューされていない場合に、通話に共有されることはありません。
詳細については、「[ローカル カメラのプレビュー](#local-camera-preview)」を参照してください。

```java
VideoDeviceInfo desiredCamera = <get-video-device>; // See the `Enumerate local devices` topic above
Context appContext = this.getApplicationContext();

LocalVideoStream currentVideoStream = new LocalVideoStream(desiredCamera, appContext);

LocalVideoStream[] localVideoStreams = new LocalVideoStream[1];
localVideoStreams[0] = currentVideoStream;

VideoOptions videoOptions = new VideoOptions(localVideoStreams);

// Render a local preview of video so the user knows that their video is being shared
Renderer previewRenderer = new VideoStreamRenderer(currentVideoStream, appContext);
View uiView = previewRenderer.createView(new CreateViewOptions(ScalingMode.FIT));

// Attach the uiView to a viewable location on the app at this point
layout.addView(uiView);

CommunicationUserIdentifier[] participants = new CommunicationUserIdentifier[]{ new CommunicationUserIdentifier("<acs user id>") };

StartCallOptions startCallOptions = new StartCallOptions();
startCallOptions.setVideoOptions(videoOptions);

Call call = callAgent.startCall(context, participants, startCallOptions);
```

## <a name="start-and-stop-sending-local-video"></a>ローカル動画の送信を開始および停止する

動画を開始するには、`deviceManager` オブジェクトの `getCameraList` API を使用して、カメラを列挙する必要があります。 次に、目的のカメラを渡して `LocalVideoStream` の新しいインスタンスを作成し、それを引数として `startVideo` API に渡します。

```java
VideoDeviceInfo desiredCamera = <get-video-device>; // See the `Enumerate local devices` topic above
Context appContext = this.getApplicationContext();

LocalVideoStream currentLocalVideoStream = new LocalVideoStream(desiredCamera, appContext);

VideoOptions videoOptions = new VideoOptions(currentLocalVideoStream);

Future startVideoFuture = call.startVideo(appContext, currentLocalVideoStream);
startVideoFuture.get();
```

動画の送信が正常に開始されると、通話インスタンスの `localVideoStreams` コレクションに `LocalVideoStream` インスタンスが追加されます。

```java
List<LocalVideoStream> videoStreams = call.getLocalVideoStreams();
LocalVideoStream currentLocalVideoStream = videoStreams.get(0); // Please make sure there are VideoStreams in the list before calling get(0).
```

ローカル動画を停止するには、`localVideoStreams` コレクションで使用可能な `LocalVideoStream` インスタンスを渡します。

```java
call.stopVideo(appContext, currentLocalVideoStream).get();
```

`LocalVideoStream` インスタンスで `switchSource` を呼び出すことにより、動画の送信中に別のカメラ デバイスに切り替えることができます。
```java
currentLocalVideoStream.switchSource(source).get();
```

## <a name="render-remote-participant-video-streams"></a>リモート参加者の動画ストリームをレンダリングする

リモート参加者の動画ストリームと画面共有ストリームの一覧を取得するには、`videoStreams` コレクションを調べます。

```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants();
RemoteParticipant remoteParticipant = remoteParticipants.get(0); // Please make sure there are remote participants in the list before calling get(0).

List<RemoteVideoStream> remoteStreams = remoteParticipant.getVideoStreams();
RemoteVideoStream remoteParticipantStream = remoteStreams.get(0); // Please make sure there are video streams in the list before calling get(0).

MediaStreamType streamType = remoteParticipantStream.getType(); // of type MediaStreamType.Video or MediaStreamType.ScreenSharing
```
 
リモート参加者からの `RemoteVideoStream` をレンダリングするには、`OnVideoStreamsUpdated` イベントをサブスクライブする必要があります。

イベント内で `isAvailable` プロパティが true に変更された場合、リモート参加者が現在ストリームを送信していることを示します。 それが発生したら、`Renderer` の新しいインスタンスを作成し、非同期 `createView` API を使用して新しい `RendererView` を作成し、お使いのアプリケーションの UI の任意の場所に `view.target` をアタッチします。

リモート ストリームの使用可能性が変わるたびに、レンダラー全体を破棄するか、特定の `RendererView` を破棄するか、それらを保持するかを選択できますが、これによって空の動画フレームが表示されます。

```java
VideoStreamRenderer remoteVideoRenderer = new VideoStreamRenderer(remoteParticipantStream, appContext);
VideoStreamRendererView uiView = remoteVideoRenderer.createView(new RenderingOptions(ScalingMode.FIT));
layout.addView(uiView);

remoteParticipant.addOnVideoStreamsUpdatedListener(e -> onRemoteParticipantVideoStreamsUpdated(p, e));

void onRemoteParticipantVideoStreamsUpdated(RemoteParticipant participant, RemoteVideoStreamsEvent args) {
    for(RemoteVideoStream stream : args.getAddedRemoteVideoStreams()) {
        if(stream.getIsAvailable()) {
            startRenderingVideo();
        } else {
            renderer.dispose();
        }
    }
}
```

### <a name="remote-video-stream-properties"></a>リモート動画ストリームのプロパティ
リモート動画ストリームにはいくつかのプロパティがあります

* `Id` - リモート動画ストリームの ID です
```java
int id = remoteVideoStream.getId();
```

* `MediaStreamType` - "Video" または "ScreenSharing" になります
```java
MediaStreamType type = remoteVideoStream.getMediaStreamType();
```

* `isAvailable` - リモート参加者のエンドポイントでストリームをアクティブに送信されているかどうかを示します
```java
boolean availability = remoteVideoStream.isAvailable();
```

### <a name="renderer-methods-and-properties"></a>Renderer のメソッドとプロパティ
Renderer オブジェクトには次の API があります

* 後でアプリケーションの UI にアタッチしてリモート動画ストリームをレンダリングできる `VideoStreamRendererView` インスタンスを作成します。
```java
// Create a view for a video stream
VideoStreamRendererView.createView()
```
* レンダラーと、このレンダラーに関連付けられているすべての `VideoStreamRendererView` を破棄します。 UI から関連付けられているすべてのビューを削除したときに呼び出します。
```java
VideoStreamRenderer.dispose()
```

* `StreamSize` - リモート動画ストリームのサイズ (幅と高さ)
```java
StreamSize renderStreamSize = VideoStreamRenderer.getSize();
int width = renderStreamSize.getWidth();
int height = renderStreamSize.getHeight();
```

### <a name="rendererview-methods-and-properties"></a>RendererView のメソッドとプロパティ
`VideoStreamRendererView` を作成するときに、このビューに適用される `ScalingMode` と `mirrored` のプロパティを指定できます。スケーリング モードは、'CROP' または 'FIT' のいずれかになります。

```java
VideoStreamRenderer remoteVideoRenderer = new VideoStreamRenderer(remoteVideoStream, appContext);
VideoStreamRendererView rendererView = remoteVideoRenderer.createView(new CreateViewOptions(ScalingMode.Fit));
```

作成した RendererView は、次のスニペットを使用してアプリケーションの UI にアタッチできます。
```java
layout.addView(rendererView);
```

後で、RendererView オブジェクトの `updateScalingMode` API を呼び出し、ScalingMode.CROP または ScalingMode.FIT のいずれかを引数として指定することで、スケーリング モードを更新できます。
```java
// Update the scale mode for this view.
rendererView.updateScalingMode(ScalingMode.CROP)
```
