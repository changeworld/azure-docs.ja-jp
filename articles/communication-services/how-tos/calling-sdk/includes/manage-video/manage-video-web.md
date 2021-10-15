---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 97e28db07e950fde42033ef57c76b2734b73f7cc
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "128699162"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-web.md)]

## <a name="device-management"></a>デバイス管理
通話でのビデオの使用を開始するには、デバイスを管理する方法を知る必要があります。 デバイスを使用すると、どのデバイスで通話にオーディオとビデオを送信するかを制御できます。

`deviceManager` で、通話でオーディオとビデオのストリームを送信できるローカル デバイスを列挙できます。 また、ローカル デバイスのマイクやカメラにアクセスするためのアクセス許可を要求するためにも使用できます。

`callClient.getDeviceManager()` メソッドを呼び出すことによって `deviceManager` にアクセスできます。

```js
const deviceManager = await callClient.getDeviceManager();
```

### <a name="get-local-devices"></a>ローカル デバイスを取得する

ローカル デバイスにアクセスするには、`deviceManager` で列挙メソッドを使用します。 列挙は非同期アクションです。

```js
//  Get a list of available video devices for use.
const localCameras = await deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = await deviceManager.getMicrophones(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = await deviceManager.getSpeakers(); // [AudioDeviceInfo, AudioDeviceInfo...]
```

### <a name="set-the-default-microphone-and-speaker"></a>既定のマイクまたはスピーカーを設定する

`deviceManager` で、通話を開始するために使用する既定のデバイスを設定できます。 クライアントの既定値が設定されていない場合、Communication Services はオペレーティング システムの既定値を使用します。

```js
// Get the microphone device that is being used.
const defaultMicrophone = deviceManager.selectedMicrophone;

// Set the microphone device to use.
await deviceManager.selectMicrophone(localMicrophones[0]);

// Get the speaker device that is being used.
const defaultSpeaker = deviceManager.selectedSpeaker;

// Set the speaker device to use.
await deviceManager.selectSpeaker(localSpeakers[0]);
```

### <a name="local-camera-preview"></a>ローカル カメラのプレビュー

`deviceManager` と `VideoStreamRenderer` を使用して、ローカル カメラからのストリームのレンダリングを開始できます。 このストリームは、他の参加者には送信されません。ローカル プレビュー フィードです。

```js
const cameras = await deviceManager.getCameras();
const camera = cameras[0];
const localCameraStream = new LocalVideoStream(camera);
const videoStreamRenderer = new VideoStreamRenderer(localCameraStream);
const view = await videoStreamRenderer.createView();
htmlElement.appendChild(view.target);
```

### <a name="request-permission-to-camera-and-microphone"></a>カメラとマイクへのアクセス許可を要求する

カメラやマイクへのアクセス許可を付与するようにユーザーに求めます。

```js
const result = await deviceManager.askDevicePermission({audio: true, video: true});
```

これは、`audio` と `video` のアクセス許可が付与されたかどうかを示すオブジェクトを使用して解決されます。

```js
console.log(result.audio);
console.log(result.video);
```
#### <a name="notes"></a>メモ
- ビデオ デバイスが接続されたり、取り外されたりすると、"videoDevicesUpdated" イベントが発生します。
- オーディオ デバイスが接続されると、"audioDevicesUpdated" イベントが発生します
- DeviceManager が作成された当初の時点では、どのデバイスについてもアクセス許可が付与されているかどうかが認識されていないため、デバイス一覧が空になっています。 その後 DeviceManager.askPermission() API を呼び出すと、デバイスのアクセスに関するプロンプトが表示されます。そこでユーザーが [許可] をクリックしてアクセスを許可すると、デバイス マネージャーによりシステム上のそのデバイスが認識され、デバイス一覧が更新された後、"audioDevicesUpdated" と "videoDevicesUpdated" の 2 つのイベントが出力されます。 ここで、ページを更新してデバイス マネージャーを作成したとしましょう。この場合、ユーザーが既にアクセスを許可しているので、デバイス マネージャーがデバイスを認識できます。そのため、当初のデバイス一覧は空ではなく、"audioDevicesUpdated" と "videoDevicesUpdated" の 2 つのイベントが出力されることもありません。
- スピーカーの列挙や選択は、Android の Chrome、iOS の Safari、macOS の Safari のいずれでもサポートされていません。

## <a name="start-and-stop-sending-local-video"></a>ローカル動画の送信を開始および停止する

動画を開始するには、`deviceManager` オブジェクトで `getCameras` メソッドを使用して、カメラを列挙する必要があります。 次に、目的のカメラを使用して `LocalVideoStream` の新しいインスタンスを作成し、その `LocalVideoStream` オブジェクトを既存の呼び出しオブジェクトの `startVideo` メソッドに渡します。

```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
const camera = cameras[0]
const localVideoStream = new LocalVideoStream(camera);
await call.startVideo(localVideoStream);
```

動画の送信が正常に開始されると、通話インスタンスの `localVideoStreams` コレクションに `LocalVideoStream` インスタンスが追加されます。

```js
call.localVideoStreams[0] === localVideoStream;
```

ローカル動画を停止するには、`localVideoStreams` コレクションで使用可能な `localVideoStream` インスタンスを渡します。

```js
await call.stopVideo(localVideoStream);
// or
await call.stopVideo(call.localVideoStreams[0]);
```

通話でビデオを開始するために `localVideoStream` インスタンスを渡すことができるメソッドには、`callAgent.startCall()`、`callAgent.join()`、`call.accept()`、`call.startVideo()` の 4 つがあります。 `call.stopVideo()` を使用するには、ビデオを開始するために使用された元のメソッドに渡したのと同じ `localVideoStream` インスタンスを渡す必要があります。

`localVideoStream` インスタンスで `switchSource` を呼び出すことにより、動画の送信中に別のカメラ デバイスに切り替えることができます。

```js
const cameras = await callClient.getDeviceManager().getCameras();
const camera = cameras[1];
localVideoStream.switchSource(camera);
```

指定されたビデオ デバイスが別のプロセスで使用されているか、システムで無効になっている場合には、次のようになります。
- 通話中にビデオがオフになり、`call.startVideo()` を使用してビデオを開始した場合は、このメソッドによって `SourceUnavailableError` がスローされ、`cameraStartFiled` が true に設定されます。
- `localVideoStream.switchSource()` メソッドの呼び出しにより、`cameraStartFailed` が true に設定されます。
通話診断ガイドによって、通話関連の問題を診断する方法に関する追加情報が提供されます。

## <a name="place-a-11-call-with-video-camera"></a>ビデオ カメラを使用して 1:1 の通話を行う

> [!IMPORTANT]
> 現在、サポートされている発信ローカル動画ストリームは 1 つだけです。

ビデオ通話を行うには、`deviceManager` の `getCameras()` メソッドを使用してローカル カメラを列挙する必要があります。

カメラを選択したら、それを使用して `LocalVideoStream` インスタンスを作成します。 `videoOptions` 内のそれを `localVideoStream` 配列内の項目として `startCall` メソッドに渡します。

```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
const camera = cameras[0]
localVideoStream = new LocalVideoStream(camera);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const call = callAgent.startCall([userCallee], placeCallOptions);
```

- 通話が接続されると、選択したカメラから他の参加者への動画ストリームの送信が自動的に開始されます。 これは、`Call.Accept()` ビデオ オプションと `CallAgent.join()` ビデオ オプションにも適用されます。

## <a name="render-remote-participant-video-streams"></a>リモート参加者の動画ストリームをレンダリングする

リモート参加者の動画ストリームと画面共有ストリームの一覧を取得するには、`videoStreams` コレクションを調べます。

```js
const remoteVideoStream: RemoteVideoStream = call.remoteParticipants[0].videoStreams[0];
const streamType: MediaStreamType = remoteVideoStream.mediaStreamType;
```

`RemoteVideoStream` をレンダリングするには、その `isAvailableChanged` イベントをサブスクライブする必要があります。 `isAvailable` プロパティが `true` に変更された場合、リモート参加者はストリームを送信しています。 それが発生したら、`VideoStreamRenderer` の新しいインスタンスを作成し、非同期 `createView` メソッドを使用して新しい `VideoStreamRendererView` インスタンスを作成します。  その後、任意の UI 要素に `view.target` を付加できます。

リモート ストリームの使用可否が変わるたびに、`VideoStreamRenderer` 全体を破棄するか、特定の `VideoStreamRendererView` を破棄するか、それらを保持するかを選択できますが、これによって空の動画フレームが表示されます。

```js
subscribeToRemoteVideoStream = async (remoteVideoStream) => {
    // Create a video stream renderer for the remote video stream.
    let videoStreamRenderer = new VideoStreamRenderer(remoteVideoStream);
    let view;
    const remoteVideoContainer = document.getElementById('remoteVideoContainer');
    const renderVideo = async () => {
        try {
            // Create a renderer view for the remote video stream.
            view = await videoStreamRenderer.createView();
            // Attach the renderer view to the UI.
            remoteVideoContainer.appendChild(view.target);
        } catch (e) {
            console.warn(`Failed to createView, reason=${e.message}, code=${e.code}`);
        }
    }
    remoteVideoStream.on('isAvailableChanged', async () => {
        // Participant has switched video on.
        if (remoteVideoStream.isAvailable) {
            await renderVideo();

        // Participant has switched video off.
        } else {
            if (view) {
                view.dispose();
                view = undefined;
            }
        }
    });

    // Participant has video on initially.
    if (remoteVideoStream.isAvailable) {
        await renderVideo();
    }
}
```

### <a name="remote-video-stream-properties"></a>リモート動画ストリームのプロパティ

リモート動画ストリームには次のプロパティがあります。

- `id`: リモート動画ストリームの ID。

```js
const id: number = remoteVideoStream.id;
```

- `mediaStreamType`: `Video` または `ScreenSharing` を指定できます。

```js
const type: MediaStreamType = remoteVideoStream.mediaStreamType;
```

- `isAvailable`: リモート参加者のエンドポイントによりストリームがアクティブに送信されているかどうか。

```js
const type: boolean = remoteVideoStream.isAvailable;
```

## <a name="videostreamrenderer-methods-and-properties"></a>VideoStreamRenderer のメソッドとプロパティ
リモート動画ストリームをレンダリングするためにアプリケーション UI に付加できる `VideoStreamRendererView` インスタンスを作成し、非同期 `createView()` メソッドを使用します。これにより、ストリームをレンダリングする準備ができたときに解決され、DOM ツリー内のどこにでも追加できる `video` 要素を表す `target` プロパティを含むオブジェクトが返されます

```js
await videoStreamRenderer.createView();
```

`videoStreamRenderer` と、それに関連付けられているすべての `VideoStreamRendererView` インスタンスを破棄します。
```js
videoStreamRenderer.dispose();
```

## <a name="videostreamrendererview-methods-and-properties"></a>VideoStreamRendererView のメソッドとプロパティ

`VideoStreamRendererView` を作成するときに、`scalingMode` と `isMirrored` プロパティを指定できます。 `scalingMode` は、`Stretch`、`Crop`、または `Fit` のいずれかです。 `isMirrored` を指定すると、レンダリングされたストリームは垂直方向に反転されます。

```js
const videoStreamRendererView: VideoStreamRendererView = await videoStreamRenderer.createView({ scalingMode, isMirrored });
```
すべての `VideoStreamRendererView` インスタンスには、レンダリング サーフェイスを表す `target` プロパティがあります。 アプリケーション UI にこのプロパティを付加します。

```js
htmlElement.appendChild(view.target);
```

`scalingMode` は、`updateScalingMode` メソッドを呼び出すことで更新できます。

```js
view.updateScalingMode('Crop');
```