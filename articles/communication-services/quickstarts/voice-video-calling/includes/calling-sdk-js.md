---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 2ecbd207c4b1946a69b01f43ec2bc77d29b1a8c9
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106073283"
---
## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- デプロイ済みの Communication Services リソース。 [Communication Services リソースを作成します](../../create-communication-resource.md)。
- 通話クライアントを有効にするためのユーザー アクセス トークン。 詳細については、[アクセス トークンの作成と管理](../../access-tokens.md)に関する記事を参照してください。
- 省略可能: クイックスタートを完了して、[アプリケーションに音声通話を追加します](../getting-started-with-calling.md)。

## <a name="install-the-sdk"></a>SDK のインストール

> [!NOTE]
> このドキュメントでは、ACS Calling Web SDK を使用します。

`npm install` コマンドを使用して、JavaScript 用の Azure Communication Services 通話と共通の各 SDK をインストールします。

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save

```

## <a name="object-model"></a>オブジェクト モデル

Azure Communication Services Calling SDK の主な機能のいくつかは、次のクラスとインターフェイスによって処理されます。

| 名前                             | 説明                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| `CallClient`                      | Calling SDK へのメイン エントリ ポイント。                                                                       |
| `CallAgent`                        | 通話を開始および管理するために使用されます。                                                                                            |
| `DeviceManager`                    | メディア デバイスを管理するために使用されます。                                                                                           |
| `AzureCommunicationTokenCredential` | `callAgent` のインスタンス化に使用される `CommunicationTokenCredential` インターフェイスを実装します。 |

## <a name="initialize-a-callclient-instance-create-a-callagent-instance-and-access-devicemanager"></a>CallClient インスタンスの初期化、CallAgent インスタンスの作成、deviceManager へのアクセスを行う

新しい `CallClient` インスタンスを作成します。 Logger インスタンスなどのカスタム オプションを使用して構成できます。

`CallClient` インスタンスを用意したら、`CallClient` インスタンスで `createCallAgent` メソッドを呼び出すことで、`CallAgent` インスタンスを作成できます。 これにより、`CallAgent` インスタンス オブジェクトが非同期に返されます。

`createCallAgent` メソッドでは、`CommunicationTokenCredential` が引数として使用されます。 これは、[ユーザー アクセス トークン](../../access-tokens.md)を受け取ります。

`callAgent` インスタンスを作成した後、`CallClient` インスタンスで `getDeviceManager` メソッドを使用して `deviceManager` にアクセスできます。

```js
// Set the logger's log level
setLogLevel('verbose');
// Redirect logger output to wherever desired. By default it logs to console
AzureLogger.log = (...args) => { console.log(...args) };
const userToken = '<user token>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationTokenCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'optional ACS user name'});
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-a-call"></a>通話を行う

通話を作成して開始するには、`callAgent` で API のいずれかを使用し、Communication Services ID SDK を使用して作成したユーザーを指定する必要があります。

通話の作成と開始は同期的に行われます。 通話インスタンスを使用すると、通話イベントをサブスクライブできます。

### <a name="place-a-1n-call-to-a-user-or-pstn"></a>ユーザーまたは PSTN と 1:n の通話を行う

別の Communication Services ユーザーと通話を行うには、`callAgent` で `startCall` メソッドを呼び出し、[Communication Services 管理ライブラリで作成した](../../access-tokens.md)、受信者の `CommunicationUserIdentifier` を渡します。

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const oneToOneCall = callAgent.startCall([userCallee]);
```

公衆交換電話網 (PSTN) の通話を行うには、`callAgent` で `startCall` メソッドを使用し、受信者の `PhoneNumberIdentifier` を渡します。 PSTN 通話を許可するように Communication Services リソースを構成する必要があります。

PSTN 番号に電話をかけるときは、代替の発信者 ID を指定します。 代替の発信者 ID は、PSTN 通話の呼び出し元を識別する (E.164 標準に基づく) 電話番号です。 これは、着信通話の場合に通話受信者に表示される電話番号です。

> [!NOTE]
> PSTN 通話は、現在プライベート プレビュー段階にあります。 アクセスするには、[早期導入者プログラムに申し込みます](https://aka.ms/ACS-EarlyAdopter)。

1:1 の通話の場合は、次のコードを使用します。

```js
const pstnCalee = { phoneNumber: '<ACS_USER_ID>' }
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const oneToOneCall = callAgent.startCall([pstnCallee], {alternateCallerId});
```

1:n の通話の場合は、次のコードを使用します。

```js
const userCallee = { communicationUserId: <ACS_USER_ID> }
const pstnCallee = { phoneNumber: <PHONE_NUMBER>};
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const groupCall = callAgent.startCall([userCallee, pstnCallee], {alternateCallerId});

```

### <a name="place-a-11-call-with-video-camera"></a>ビデオ カメラを使用して 1:1 の通話を行う

> [!IMPORTANT]
> 現在、発信ローカル動画ストリームは 1 つしか使用できません。

ビデオ通話を行うには、`deviceManager` の `getCameras()` メソッドを使用してカメラを指定する必要があります。

カメラを選択したら、それを使用して `LocalVideoStream` インスタンスを作成します。 `videoOptions` 内のそれを `localVideoStream` 配列内の項目として `startCall` メソッドに渡します。

```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
const camera = cameras[0]
localVideoStream = new LocalVideoStream(camera);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const call = callAgent.startCall(['acsUserId'], placeCallOptions);

```

通話が接続されると、選択したカメラから他の参加者への動画ストリームの送信が自動的に開始されます。 これは、`Call.Accept()` ビデオ オプションと `CallAgent.join()` ビデオ オプションにも適用されます。

### <a name="join-a-group-call"></a>グループ通話に参加する

> [!NOTE]
> `groupId` パラメーターはシステム メタデータと見なされ、システムの実行に必要な操作のために Microsoft によって使用される場合があります。 `groupId` 値には個人データを含めないでください。 Microsoft ではこのパラメーターを個人データとして扱わず、そのコンテンツが Microsoft の従業員に表示されたり、長期間保管されたりすることがあります。
>
> `groupId` パラメーターのデータは GUID 形式でなければなりません。 ご利用のシステム内で個人データと見なされない、ランダムに生成された GUID を使用することをお勧めします。
>

新しいグループ通話を始めるか、進行中のグループ通話に参加するには、`join` メソッドを使用して、`groupId` プロパティを含むオブジェクトを渡します。 `groupId` 値には GUID を指定する必要があります。

```js

const context = { groupId: <GUID>}
const call = callAgent.join(context);

```

### <a name="join-a-teams-meeting"></a>Teams 会議に参加する
> [!NOTE]
> この API は開発者向けにプレビューとして提供されており、寄せられたフィードバックにもとづいて変更される場合があります。 この API は運用環境で使用しないでください。 この API を使用するには、ACS Calling Web SDK の "ベータ" リリースを使用してください

Teams 会議に参加するには、`join` メソッドを使用し、会議リンクまたは座標を渡します。

会議リンクを使用して参加する:

```js
const locator = { meetingLink: <meeting link>}
const call = callAgent.join(locator);
```

会議の座標を使用して参加する:

```js
const locator = {
    threadId: <thread id>,
    organizerId: <organizer id>,
    tenantId: <tenant id>,
    messageId: <message id>
}
const call = callAgent.join(locator);
```

## <a name="receive-an-incoming-call"></a>着信した通話に出る

ログインしている ID が着信通話を受信すると、`callAgent` インスタンスで `incomingCall` イベントが生成されます。 このイベントをリッスンするには、次のいずれかのオプションを使用してサブスクライブします。

```js
const incomingCallHander = async (args: { incomingCall: IncomingCall }) => {

    //Get incoming call ID
    var incomingCallId = incomingCall.id

    // Get information about caller
    var callerInfo = incomingCall.callerInfo

    // Accept the call
    var call = await incomingCall.accept();

    // Reject the call
    incomingCall.reject();

    // Subscribe to callEnded event and get the call end reason
     incomingCall.on('callEnded', args => {
        console.log(args.callEndReason);
    });

    // callEndReason is also a property of IncomingCall
    var callEndReason = incomingCall.callEndReason;
};
callAgentInstance.on('incomingCall', incomingCallHander);
```

`incomingCall` イベントには、受け入れまたは拒否できる `incomingCall` インスタンスが含まれています。

## <a name="manage-calls"></a>通話を管理する

通話中、通話のプロパティにアクセスしたり、ビデオとオーディオの設定を管理したりできます。

### <a name="check-call-properties"></a>通話のプロパティを確認する

通話の一意の ID (文字列) を取得します。

   ```js
    const callId: string = call.id;
   ```

通話の他の参加者について知るには、"call" インスタンスの `remoteParticipants` コレクションを調べます。

   ```js
   const remoteParticipants = call.remoteParticipants;
   ```

着信通話の発信者を特定します。

   ```js
   const callerIdentity = call.callerInfo.identifier;
   ```

   `identifier` は `CommunicationIdentifier` の種類の 1 つです。

通話の状態を取得します。

   ```js
   const callState = call.state;
   ```

   これにより、通話の現在の状態を表す文字列が返されます。

  - `None`: 通話の初期状態。
  - `Connecting`: 通話が発信または受信されたときの初期遷移状態。
  - `Ringing`: 発信通話の場合、リモート参加者に対して通話が発信されていることを示します。 参加者側では、これは `Incoming` です。
  - `EarlyMedia`: 通話が接続される前に、アナウンスが再生されている状態を示します。
  - `Connected`: 通話が接続されていることを示します。
  - `LocalHold`: 通話がローカル参加者によって保留にされていることを示します。 ローカル エンドポイントとリモート参加者の間でメディアは送信されていません。
  - `RemoteHold`: 通話がリモート参加者によって保留にされていることを示します。 ローカル エンドポイントとリモート参加者の間でメディアは送信されていません。
  - `Disconnecting`: 通話が `Disconnected` 状態になる前の遷移状態。
  - `Disconnected`: 通話の最終状態。 ネットワーク接続が失われると、2 分後に状態は `Disconnected` になります。

通話が終了した理由を確認するには、`callEndReason` プロパティを調べます。

   ```js
   const callEndReason = call.callEndReason;
   const callEndReasonCode = callEndReason.code // (number) code associated with the reason
   const callEndReasonSubCode = callEndReason.subCode // (number) subCode associated with the reason
   ```

現在の通話が着信か、発信かを知るには、`direction` プロパティを調べます。 `CallDirection` を返します。

  ```js
   const isIncoming = call.direction == 'Incoming';
   const isOutgoing = call.direction == 'Outgoing';
   ```

現在マイクがミュートされているかどうかを調べます。 `Boolean` を返します。

   ```js
   const muted = call.isMuted;
   ```

画面共有ストリームが特定のエンドポイントから送信されているかどうかを確認するには、`isScreenSharingOn` プロパティを調べます。 `Boolean` を返します。

   ```js
   const isScreenSharingOn = call.isScreenSharingOn;
   ```

アクティブな動画ストリームを調べるには、`localVideoStreams` コレクションを確認します。 これにより、`LocalVideoStream` オブジェクトが返されます。

   ```js
   const localVideoStreams = call.localVideoStreams;
   ```

### <a name="check-a-callended-event"></a>callEnded イベントを確認する

`call` インスタンスにより、通話が終了したときに `callEnded` イベントが生成されます。 このイベントをリッスンするには、次のコードを使用してサブスクライブします。

```js
const callEndHander = async (args: { callEndReason: CallEndReason }) => {
    console.log(args.callEndReason)
};

call.on('callEnded', callEndHander);
```

### <a name="mute-and-unmute"></a>ミュートとミュート解除

ローカル エンドポイントをミュートまたはミュート解除するには、非同期 API の `mute` と `unmute` を使用します。

```js

//mute local device
await call.mute();

//unmute local device
await call.unmute();

```

### <a name="start-and-stop-sending-local-video"></a>ローカル動画の送信を開始および停止する

動画を開始するには、`deviceManager` オブジェクトで `getCameras` メソッドを使用して、カメラを指定する必要があります。 次に、目的のカメラを引数として `startVideo` メソッドに渡して `LocalVideoStream` の新しいインスタンスを作成します。

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
```

`localVideoStream` インスタンスで `switchSource` を呼び出すことにより、動画の送信中に別のカメラ デバイスに切り替えることができます。

```js
const cameras = await callClient.getDeviceManager().getCameras();
const camera = cameras[1];
localVideoStream.switchSource(camera);
```

## <a name="manage-remote-participants"></a>リモート参加者を管理する

すべてのリモート参加者は `RemoteParticipant` 型で表され、通話インスタンスの `remoteParticipants` コレクションを通して使用できます。

### <a name="list-the-participants-in-a-call"></a>通話の参加者を一覧表示する

`remoteParticipants` コレクションで、通話におけるリモート参加者の一覧が返されます。

```js
call.remoteParticipants; // [remoteParticipant, remoteParticipant....]
```

### <a name="access-remote-participant-properties"></a>リモート参加者のプロパティにアクセスする

リモート参加者には、一連のプロパティとコレクションが関連付けられています。

- `CommunicationIdentifier`: リモート参加者の識別子を取得します。 ID は `CommunicationIdentifier` の種類の 1 つです。

  ```js
  const identifier = remoteParticipant.identifier;
  ```

  これは、次の `CommunicationIdentifier` の種類のいずれかになります。

  - `{ communicationUserId: '<ACS_USER_ID'> }`: ACS ユーザーを表すオブジェクト。
  - `{ phoneNumber: '<E.164>' }`: E.164 形式で電話番号を表すオブジェクト。
  - `{ microsoftTeamsUserId: '<TEAMS_USER_ID>', isAnonymous?: boolean; cloud?: "public" | "dod" | "gcch" }`: Tems ユーザーを表すオブジェクト。
  - `{ id: string }`: 他の識別子の種類に適合しない識別子を表すオブジェクト

- `state`: リモート参加者の状態を取得します。

  ```js
  const state = remoteParticipant.state;
  ```

  次の状態があります。

  - `Idle`: 初期状態。
  - `Connecting`: 参加者が通話に接続している間の遷移状態。
  - `Ringing`: 参加者が発信しています。
  - `Connected`: 参加者は通話に接続されています。
  - `Hold`: 参加者は保留中です。
  - `EarlyMedia`: 参加者が通話に接続する前に再生されるアナウンス。
  - `Disconnected`: 最終状態。 参加者は通話から切断されました。 リモート参加者がネットワーク接続を失うと、2 分後に状態は `Disconnected` に変わります。

- `callEndReason`: 参加者が通話を終了した理由を知るには、`callEndReason` プロパティを調べます。

  ```js
  const callEndReason = remoteParticipant.callEndReason;
  const callEndReasonCode = callEndReason.code // (number) code associated with the reason
  const callEndReasonSubCode = callEndReason.subCode // (number) subCode associated with the reason
  ```

- `isMuted` 状態: リモート参加者がミュートされているかどうかを確認するには、`isMuted` プロパティを調べます。 `Boolean` を返します。

  ```js
  const isMuted = remoteParticipant.isMuted;
  ```

- `isSpeaking` 状態: リモート参加者が話しているかどうかを確認するには、`isSpeaking` プロパティを調べます。 `Boolean` を返します。

  ```js
  const isSpeaking = remoteParticipant.isSpeaking;
  ```

- `videoStreams`: 特定の参加者がこの通話で送信しているすべての動画ストリームを調べるには、`videoStreams` コレクションを確認します。 これには、`RemoteVideoStream` オブジェクトが含まれています。

  ```js
  const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]
  ```
- `displayName`: このリモート参加者の表示名を取得するには、文字列を返す `displayName` プロパティを調べます。 

  ```js
  const displayName = remoteParticipant.displayName;
  ```

### <a name="add-a-participant-to-a-call"></a>通話に参加者を追加する

通話に参加者 (ユーザーまたは電話番号) を追加するには、`addParticipant` を使用します。 `Identifier` の種類のいずれかを指定します。 これにより、`remoteParticipant` インスタンスが返されます。

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier, {alternateCallerId: '<Alternate Caller ID>'});
```

### <a name="remove-a-participant-from-a-call"></a>通話から参加者を削除する

通話から参加者 (ユーザーまたは電話番号) を削除するには、`removeParticipant` を呼び出します。 `Identifier` の種類のいずれかを渡す必要があります。 参加者が通話から削除されると、非同期的に解決されます。 参加者は、`remoteParticipants` コレクションからも削除されます。

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
await call.removeParticipant(userIdentifier);
await call.removeParticipant(pstnIdentifier);
```

## <a name="render-remote-participant-video-streams"></a>リモート参加者の動画ストリームをレンダリングする

リモート参加者の動画ストリームと画面共有ストリームの一覧を取得するには、`videoStreams` コレクションを調べます。

```js
const remoteVideoStream: RemoteVideoStream = call.remoteParticipants[0].videoStreams[0];
const streamType: MediaStreamType = remoteVideoStream.mediaStreamType;
```

`RemoteVideoStream` をレンダリングするには、`isAvailableChanged` イベントをサブスクライブする必要があります。 `isAvailable` プロパティが `true` に変更された場合、リモート参加者はストリームを送信しています。 それが発生したら、`VideoStreamRenderer` の新しいインスタンスを作成し、非同期 `createView` メソッドを使用して新しい `VideoStreamRendererView` インスタンスを作成します。  その後、任意の UI 要素に `view.target` を付加できます。

リモート ストリームの使用可否が変わるたびに、`VideoStreamRenderer` 全体を破棄するか、特定の `VideoStreamRendererView` を破棄するか、それらを保持するかを選択できますが、これによって空の動画フレームが表示されます。

```js
function subscribeToRemoteVideoStream(remoteVideoStream: RemoteVideoStream) {
    let videoStreamRenderer: VideoStreamRenderer = new VideoStreamRenderer(remoteVideoStream);
    const displayVideo = () => {
        const view = await videoStreamRenderer.createView();
        htmlElement.appendChild(view.target);
    }
    remoteVideoStream.on('isAvailableChanged', async () => {
        if (remoteVideoStream.isAvailable) {
            displayVideo();
        } else {
            videoStreamRenderer.dispose();
        }
    });
    if (remoteVideoStream.isAvailable) {
        displayVideo();
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

### <a name="videostreamrenderer-methods-and-properties"></a>VideoStreamRenderer のメソッドとプロパティ

リモート動画ストリームをレンダリングするためにアプリケーション UI に付加できる `VideoStreamRendererView` インスタンスを作成し、非同期 `createView()` メソッドを使用します。これにより、ストリームをレンダリングする準備ができたときに解決され、DOM ツリー内のどこにでも追加できる `video` 要素を表す `target` プロパティを含むオブジェクトが返されます

  ```js
  videoStreamRenderer.createView()
  ```

`videoStreamRenderer` と、それに関連付けられているすべての `VideoStreamRendererView` インスタンスを破棄します。

  ```js
  videoStreamRenderer.dispose()
  ```

### <a name="videostreamrendererview-methods-and-properties"></a>VideoStreamRendererView のメソッドとプロパティ

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
view.updateScalingMode('Crop')
```

## <a name="device-management"></a>デバイス管理

`deviceManager` で、通話でオーディオとビデオのストリームを送信できるローカル デバイスを指定できます。 これにより、ネイティブ ブラウザー API を使用して、別のユーザーのマイクやカメラにアクセスするためのアクセス許可を要求することもできます。

`callClient.getDeviceManager()` メソッドを呼び出すことによって `deviceManager` にアクセスできます。

```js
const deviceManager = await callClient.getDeviceManager();
```

### <a name="get-local-devices"></a>ローカル デバイスを取得する

ローカル デバイスにアクセスするには、`deviceManager` で列挙メソッドを使用します。

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

カメラとマイクのアクセス許可を付与するようにユーザーに求めます。

```js
const result = await deviceManager.askDevicePermission({audio: true, video: true});
```

これは、`audio` と `video` のアクセス許可が付与されたかどうかを示すオブジェクトを使用して解決されます。

```js
console.log(result.audio);
console.log(result.video);
```

## <a name="record-calls"></a>通話を記録する
> [!NOTE]
> この API は開発者向けにプレビューとして提供されており、寄せられたフィードバックにもとづいて変更される場合があります。 この API は運用環境で使用しないでください。 この API を使用するには、ACS Calling Web SDK の "ベータ" リリースを使用してください

通話記録は、コア `Call` API の拡張機能です。 まず、記録機能 API オブジェクトを取得する必要があります。

```js
const callRecordingApi = call.api(Features.Recording);
```

次に、通話が記録されているかどうかを確認するために、`callRecordingApi` の `isRecordingActive` プロパティを調べます。 `Boolean` を返します。

```js
const isResordingActive = callRecordingApi.isRecordingActive;
```

また、変更の記録をサブスクライブすることもできます。

```js
const isRecordingActiveChangedHandler = () => {
  console.log(callRecordingApi.isRecordingActive);
};

callRecordingApi.on('isRecordingActiveChanged', isRecordingActiveChangedHandler);

```

## <a name="transfer-calls"></a>通話を転送する
> [!NOTE]
> この API は開発者向けにプレビューとして提供されており、寄せられたフィードバックにもとづいて変更される場合があります。 この API は運用環境で使用しないでください。 この API を使用するには、ACS Calling Web SDK の "ベータ" リリースを使用してください

通話転送は、コア `Call` API の拡張機能です。 まず、転送機能 API オブジェクトを取得する必要があります。

```js
const callTransferApi = call.api(Features.Transfer);
```

通話転送には、次の 3 者が関与します。

- "*転送者*": 転送要求を開始するユーザー。
- "*転送元*": 転送されるユーザー。
- "*転送先*": 転送先のユーザー。

転送は、こちらの手順に従います。

1. "*転送者*" と "*転送元*" の間に接続済みの通話が既に存在します。 "*転送者*" が、"*転送元*" から "*転送先*" への通話の転送を決定します。
1. "*転送者*" は `transfer` API を呼び出します。
1. "*転送元*" は、`transferRequested` イベントを使用して "*転送先*" への転送要求を `accept` するか、`reject` するかを決定します。
1. "*転送先*" は "*転送元*" が転送要求を受け入れる場合にのみ着信通話を受信します。

現在の通話を転送するには、`transfer` API を使用します。 `transfer` は、`disableForwardingAndUnanswered` フラグを設定できる省略可能な `transferCallOptions` を受け取ります。

- `disableForwardingAndUnanswered = false`: "*転送先*" が転送通話に応答しない場合、その転送は "*転送先*" の転送および未回答の設定に従います。
- `disableForwardingAndUnanswered = true`: "*転送先*" が転送通話に応答しない場合、転送の試行は終了します。

```js
// transfer target can be an ACS user
const id = { communicationUserId: <ACS_USER_ID> };
```

```js
// call transfer API
const transfer = callTransferApi.transfer({targetParticipant: id});
```

`transfer` API を使用すると、`transferStateChanged` と `transferRequested` のイベントにサブスクライブできます。 `transferRequested` イベントが `call` インスタンスから生成され、`transferStateChanged` イベントおよび転送の `state` と `error` が `transfer` インスタンスから生成されます。

```js
// transfer state
const transferState = transfer.state; // None | Transferring | Transferred | Failed

// to check the transfer failure reason
const transferError = transfer.error; // transfer error code that describes the failure if a transfer request failed
```

"*転送元*" は、`transferRequested` イベントで "*転送者*" によって開始された転送要求を、`transferRequestedEventArgs` の `accept()` または `reject()` を使用して、受け入れるか拒否することができます。 `transferRequestedEventArgs` で、`targetParticipant` 情報と `accept` または `reject` メソッドにアクセスできます。

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

## <a name="learn-about-eventing-models"></a>イベント処理のモデルを理解する

現在の値を調べて、将来の値の更新イベントをサブスクライブします。

### <a name="properties"></a>プロパティ

```js
// Inspect the current value
console.log(object.property);

// Subscribe to value updates
object.on('propertyChanged', () => {
    // Inspect new value
    console.log(object.property)
});

// Unsubscribe from updates:
object.off('propertyChanged', () => {});



// Example for inspecting a call state
console.log(call.state);
call.on('stateChanged', () => {
    console.log(call.state);
});
call.off('stateChanged', () => {});
```

### <a name="collections"></a>コレクション

```js
// Inspect the current collection
object.collection.forEach(v => {
    console.log(v);
});

// Subscribe to collection updates
object.on('collectionUpdated', e => {
    // Inspect new values added to the collection
    e.added.forEach(v => {
        console.log(v);
    });
    // Inspect values removed from the collection
    e.removed.forEach(v => {
        console.log(v);
    });
});

// Unsubscribe from updates:
object.off('collectionUpdated', () => {});

// Example for subscribing to remote participants and their video streams
call.remoteParticipants.forEach(p => {
    subscribeToRemoteParticipant(p);
})

call.on('remoteParticipantsUpdated', e => {
    e.added.forEach(p => { subscribeToRemoteParticipant(p) })
    e.removed.forEach(p => { unsubscribeFromRemoteParticipant(p) })
});

function subscribeToRemoteParticipant(p) {
    console.log(p.state);
    p.on('stateChanged', () => { console.log(p.state); });
    p.videoStreams.forEach(v => { subscribeToRemoteVideoStream(v) });
    p.on('videoStreamsUpdated', e => { e.added.forEach(v => { subscribeToRemoteVideoStream(v) }) })
}
```
