---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 479aa522462d14f295177e6b2d2fcc4707657760
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106498799"
---
[!INCLUDE [Public Preview Notice](../../../includes/public-preview-include-android-ios.md)]

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- デプロイ済みの Azure Communication Services リソース。 [Communication Services リソースを作成します](../../create-communication-resource.md)。
- 通話クライアントを有効にするためのユーザー アクセス トークン。 [ユーザー アクセス トークンを取得します](../../access-tokens.md)。
- 省略可能: 「[音声通話をアプリに追加する](../getting-started-with-calling.md)」クイック スタートを完了します。

## <a name="set-up-your-system"></a>システムを設定する

### <a name="create-the-xcode-project"></a>Xcode プロジェクトを作成する

Xcode で、新しい iOS プロジェクトを作成し、 **[単一ビュー アプリ]** テンプレートを選択します。 このクイックスタートでは [SwiftUI フレームワーク](https://developer.apple.com/xcode/swiftui/)を使用します。そのため、 **[言語]** を **[Swift]** に、 **[ユーザー インターフェイス]** を **[SwiftUI]** に設定する必要があります。 

このクイックスタートでは、単体テストと UI テストは作成しません。 **[Include Unit Tests]\(単体テストを含める\)** と **[Include UI Tests]\(UI テストを含める\)** のテキスト ボックスは自由にオフにしてかまいません。

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="Xcode 内にプロジェクトを作成するためのウィンドウを示すスクリーンショット。":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>CocoaPods でパッケージと依存関係をインストールする

1. 次のようにして、アプリケーションのポッドファイルを作成します。

   ```
   platform :ios, '13.0'
   use_frameworks!
   target 'AzureCommunicationCallingSample' do
     pod 'AzureCommunicationCalling', '~> 1.0.0-beta.8'
     pod 'AzureCommunication', '~> 1.0.0-beta.8'
     pod 'AzureCore', '~> 1.0.0-beta.8'
   end
   ```

2. `pod install` を実行します。
3. Xcode で `.xcworkspace` を開きます。

### <a name="request-access-to-the-microphone"></a>マイクへのアクセスを要求する

デバイスのマイクにアクセスするには、アプリの情報プロパティ リストを `NSMicrophoneUsageDescription` で更新する必要があります。 関連付けられた値を `string` に設定します。これは、ユーザーからのアクセスを求めるためにシステムによって使用されるダイアログに含められます。

プロジェクト ツリーの `Info.plist` のエントリを右クリックし、 **[Open As]\(形式を指定して開く\)**  >  **[Source Code]\(ソース コード\)** の順に選択します。 最上位の `<dict>` セクションに以下の行を追加してから、ファイルを保存します。

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>アプリのフレームワークを設定する

プロジェクトの *ContentView.swift* ファイルを開き、そのファイルの先頭に `import` 宣言を追加して `AzureCommunicationCalling` ライブラリをインポートします。 さらに、`AVFoundation` をインポートします。 これは、コード内のオーディオ アクセス許可要求に必要になります。

```swift
import AzureCommunicationCalling
import AVFoundation
```

## <a name="learn-the-object-model"></a>オブジェクト モデルについて学習する

Azure Communication Services Calling SDK for iOS の主な機能のいくつかは、以下のクラスとインターフェイスにより処理されます。

> [!NOTE]
> このクイック スタートでは、バージョン 1.0.0-beta.8 の Calling SDK を使用します。


| 名前                                  | 説明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| `CallClient` | `CallClient` は、Calling SDK へのメイン エントリ ポイントです。|
| `CallAgent` | `CallAgent` は、通話を開始および管理するために使用されます。 |
| `CommunicationTokenCredential` | `CommunicationTokenCredential` は、`CallAgent` をインスタンス化するために資格情報トークンとして使用されます。| 
| `CommunicationIdentifier` | `CommunicationIdentifier` は、ユーザーの ID を表すために使用されます。 その ID は、`CommunicationUserIdentifier`、`PhoneNumberIdentifier`、または `CallingApplication` にすることができます。 |

> [!NOTE]
> アプリケーションでは、イベント デリゲートを実装する際に、イベント サブスクリプションを必要とするオブジェクトへの強い参照を保持する必要があります。 たとえば、`call.addParticipant` メソッドの呼び出し時に `RemoteParticipant` オブジェクトが返され、アプリケーションによって `RemoteParticipantDelegate` でリッスンするようにデリゲートが設定された場合には、そのアプリケーションによって `RemoteParticipant` オブジェクトへの強い参照が保持される必要があります。 それ以外の場合に、このオブジェクトが収集されると、Calling SDK によってオブジェクトの呼び出しが試行されるときに、デリゲートによって致命的な例外がスローされます。

## <a name="initialize-callagent"></a>CallAgent を初期化する

`CallClient` から `CallAgent` インスタンスを作成するには、初期化された後に `CallAgent` オブジェクトを非同期に返す `callClient.createCallAgent` メソッドを使用する必要があります。

通話クライアントを作成するには、`CommunicationTokenCredential` オブジェクトを渡す必要があります。

```swift

import AzureCommunication

let tokenString = "token_string"
var userCredential: CommunicationTokenCredential?
var userCredential: CommunicationTokenCredential?
   do {
       userCredential = try CommunicationTokenCredential(with: CommunicationTokenRefreshOptions(initialToken: token, 
                                                                     refreshProactively: true,
                                                                     tokenRefresher: self.fetchTokenSync))
   } catch {
       return
}

// tokenProvider needs to be implemented by Contoso, which fetches a new token
public func fetchTokenSync(then onCompletion: TokenRefreshOnCompletion) {
    let newToken = self.tokenProvider!.fetchNewToken()
    onCompletion(newToken, nil)
}
```

`CallClient` に作成した `CommunicationTokenCredential` オブジェクトを渡し、表示名を設定します。

```swift

callClient = CallClient()
let callAgentOptions:CallAgentOptions = CallAgentOptions()!
options.displayName = " iOS User"

callClient?.createCallAgent(userCredential: userCredential!,
    options: callAgentOptions) { (callAgent, error) in
        if error == nil {
            print("Create agent succeeded")
            self.callAgent = callAgent
        } else {
            print("Create agent failed")
        }
})

```

## <a name="place-an-outgoing-call"></a>発信通話を行う

通話を作成して開始するには、`CallAgent` でいずれかの API を呼び出し、Communication Services 管理クライアント SDK を使用してプロビジョニングしたユーザーの Communication Services ID を指定する必要があります。

通話の作成と開始は同期的に行われます。 通話のすべてのイベントにサブスクライブできる通話インスタンスを受信します。

### <a name="place-a-11-call-to-a-user-or-a-1n-call-with-users-and-pstn"></a>ユーザーに対する 1:1 の通話、またはユーザーおよび PSTN と 1:n の通話を行う

```swift

let callees = [CommunicationUser(identifier: 'UserId')]
let oneToOneCall = self.callAgent.call(participants: callees, options: StartCallOptions())

```

### <a name="place-a-1n-call-with-users-and-pstn"></a>ユーザーと PSTN で 1:n の通話を行う
PSTN への通話を行うには、Communication Services で取得した電話番号を指定する必要があります。

```swift

let pstnCallee = PhoneNumberIdentifier(phoneNumber: '+1999999999')
let callee = CommunicationUserIdentifier(identifier: 'UserId')
let groupCall = self.callAgent.call(participants: [pstnCallee, callee], options: StartCallOptions())

```

### <a name="place-a-11-call-with-video"></a>動画を使用して 1:1 の通話を行う
デバイス マネージャーのインスタンスを取得する場合は、[デバイスの管理](#manage-devices)に関するセクションを参照してください。

```swift

let camera = self.deviceManager!.cameras!.first
let localVideoStream = LocalVideoStream(camera: camera)
let videoOptions = VideoOptions(localVideoStream: localVideoStream)

let startCallOptions = StartCallOptions()
startCallOptions?.videoOptions = videoOptions

let callee = CommunicationUserIdentifier(identifier: 'UserId')
let call = self.callAgent?.call(participants: [callee], options: startCallOptions)

```

### <a name="join-a-group-call"></a>グループ通話に参加する
通話に参加するには、`CallAgent` でいずれかの API を呼び出す必要があります。

```swift

let groupCallLocator = GroupCallLocator(groupId: UUID(uuidString: "uuid_string"))!
let call = self.callAgent?.join(with: groupCallLocator, joinCallOptions: JoinCallOptions())

```

### <a name="subscribe-to-an-incoming-call"></a>着信通話をサブスクライブする
着信通話イベントをサブスクライブします。

```
final class IncomingCallHandler: NSObject, CallAgentDelegate, IncomingCallDelegate
{
    // Event raised when there is an incoming call
    public func onIncomingCall(_ callAgent: CallAgent!, incomingcall: IncomingCall!) {
        self.incomingCall = incomingcall
        // Subscribe to get OnCallEnded event
        self.incomingCall?.delegate = self
    }

    // Event raised when incoming call was not answered
    public func onCallEnded(_ incomingCall: IncomingCall!, args: PropertyChangedEventArgs!) {
        self.incomingCall = nil
    }
}
```

### <a name="accept-an-incoming-call"></a>電話の着信を受け入れる
通話を受け入れるには、呼び出しオブジェクトに対して `accept` メソッドを呼び出します。 デリゲートを `CallAgent` に設定します。

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

let firstCamera: VideoDeviceInfo? = self.deviceManager!.cameras!.first
let localVideoStream = LocalVideoStream(camera: firstCamera)
let acceptCallOptions = AcceptCallOptions()
acceptCallOptions!.videoOptions = VideoOptions(localVideoStream:localVideoStream!)
if let incomingCall = CallHandler().incomingCall {
   incomingCall.accept(options: acceptCallOptions) { (call, error) in
               if error == nil {
                   print("Incoming call accepted")
               } else {
                   print("Failed to accept incoming call")
               }
           }
} else {
   print("No incoming call found to accept")
}
```

## <a name="set-up-push-notifications"></a>プッシュ通知の設定

モバイル プッシュ通知は、モバイル デバイスで受け取るポップアップ通知です。 通話に関しては、VoIP (ボイス オーバー IP) でのプッシュ通知に焦点を絞ります。 

以下のセクションでは、プッシュ通知の登録、処理、および登録解除を行う方法について説明します。 これらの作業を開始する前に、次の前提条件を満たす必要があります。

1. Xcode で、 **[Signing & Capabilities]\(署名と機能\)** に移動します。 **[+ Capability]\(+ 機能\)** を選択して機能を追加してから、 **[プッシュ通知]** を選択します。
2. **[+ Capability]\(+ 機能\)** を選択して別の機能を追加してから、 **[バックグラウンド モード]** を選びます。
3. **[バックグラウンド モード]** で、 **[ボイス オーバー IP]** と **[リモート通知]** のチェックボックスをオンにします。

:::image type="content" source="../media/ios/xcode-push-notification.png" alt-text="Xcode での機能の追加方法を示すスクリーンショット。" lightbox="../media/ios/xcode-push-notification.png":::

### <a name="register-for-push-notifications"></a>プッシュ通知に登録する

プッシュ通知に登録するには、デバイス登録トークンを使用して `CallAgent` インスタンスで `registerPushNotification()` を呼び出します。

プッシュ通知への登録は、初期化が正常に完了した後に行われる必要があります。 `callAgent` オブジェクトが破棄されると、`logout` が呼び出されて、プッシュ通知の登録が自動的に解除されます。


```swift

let deviceToken: Data = pushRegistry?.pushToken(for: PKPushType.voIP)
callAgent.registerPushNotifications(deviceToken: deviceToken) { (error) in
    if(error == nil) {
        print("Successfully registered to push notification.")
    } else {
        print("Failed to register push notification.")
    }
}

```

### <a name="handle-push-notifications"></a>プッシュ通知を処理する
着信通話のプッシュ通知を受信するには、ディクショナリ ペイロードを設定して `CallAgent` インスタンスで `handlePushNotification()` を呼び出します。

```swift

let callNotification = IncomingCallInformation.from(payload: pushPayload?.dictionaryPayload)

callAgent.handlePush(notification: callNotification) { (error) in
    if (error != nil) {
        print("Handling of push notification failed")
    } else {
        print("Handling of push notification was successful")
    }
}

```
### <a name="unregister-push-notifications"></a>プッシュ通知の登録を解除する

アプリケーションによって、プッシュ通知の登録はいつでも解除できます。 `CallAgent` で `unregisterPushNotification` メソッドを呼び出すだけです。

> [!NOTE]
> ログアウト時に、プッシュ通知からアプリケーションの登録が自動的に解除されることはありません。

```swift

callAgent.unregisterPushNotifications { (error) in
    if (error != nil) {
        print("Unregister of push notification failed, please try again")
    } else {
        print("Unregister of push notification was successful")
    }
}

```

## <a name="perform-mid-call-operations"></a>通話中の操作を実行する

通話の間にさまざまな操作を実行し、動画やオーディオに関連する設定を管理できます。

### <a name="mute-and-unmute"></a>ミュートとミュート解除

ローカル エンドポイントのミュートまたはその解除を行う場合は、非同期 API の `mute` と `unmute` を使用できます。

```swift
call!.mute { (error) in
    if error == nil {
        print("Successfully muted")
    } else {
        print("Failed to mute")
    }
}

```

ローカル エンドポイントのミュートを非同期に解除するには、次のコードを使用します。

```swift
call!.unmute { (error) in
    if error == nil {
        print("Successfully un-muted")
    } else {
        print("Failed to unmute")
    }
}
```

### <a name="start-and-stop-sending-local-video"></a>ローカル動画の送信を開始および停止する

通話で他の参加者へのローカル動画の送信を開始するには、`startVideo` API を使用し、`camera` で `localVideoStream` を渡します。

```swift

let firstCamera: VideoDeviceInfo? = self.deviceManager!.cameras!.first
let localVideoStream = LocalVideoStream(camera: firstCamera)

call!.startVideo(stream: localVideoStream) { (error) in
    if (error == nil) {
        print("Local video started successfully")
    } else {
        print("Local video failed to start")
    }
}

```

動画の送信を開始した後、`LocalVideoStream` インスタンスが通話インスタンスの `localVideoStreams` コレクションに追加されます。

```swift

call.localVideoStreams[0]

```

ローカル動画を停止するには、`call.startVideo` の呼び出しから返された `localVideoStream` インスタンスを渡します。 これは、非同期アクションです。

```swift

call!.stopVideo(stream: localVideoStream) { (error) in
    if (error == nil) {
        print("Local video stopped successfully")
    } else {
        print("Local video failed to stop")
    }
}

```

## <a name="manage-remote-participants"></a>リモート参加者を管理する

すべてのリモート参加者は `RemoteParticipant` 型で表され、通話インスタンスの `remoteParticipants` コレクションを通して使用できます。

### <a name="list-participants-in-a-call"></a>通話の参加者の一覧を取得する

```swift

call.remoteParticipants

```

### <a name="get-remote-participant-properties"></a>リモート参加者のプロパティを取得する

```swift

// [RemoteParticipantDelegate] delegate - an object you provide to receive events from this RemoteParticipant instance
var remoteParticipantDelegate = remoteParticipant.delegate

// [CommunicationIdentifier] identity - same as the one used to provision a token for another user
var identity = remoteParticipant.identity

// ParticipantStateIdle = 0, ParticipantStateEarlyMedia = 1, ParticipantStateConnecting = 2, ParticipantStateConnected = 3, ParticipantStateOnHold = 4, ParticipantStateInLobby = 5, ParticipantStateDisconnected = 6
var state = remoteParticipant.state

// [Error] callEndReason - reason why participant left the call, contains code/subcode/message
var callEndReason = remoteParticipant.callEndReason

// [Bool] isMuted - indicating if participant is muted
var isMuted = remoteParticipant.isMuted

// [Bool] isSpeaking - indicating if participant is currently speaking
var isSpeaking = remoteParticipant.isSpeaking

// RemoteVideoStream[] - collection of video streams this participants has
var videoStreams = remoteParticipant.videoStreams // [RemoteVideoStream, RemoteVideoStream, ...]

```

### <a name="add-a-participant-to-a-call"></a>通話に参加者を追加する

通話に参加者 (ユーザーまたは電話番号のいずれか) を追加する場合は、`addParticipant` を呼び出すことができます。 このコマンドにより、リモート参加者インスタンスが同期的に返されます。

```swift

let remoteParticipantAdded: RemoteParticipant = call.add(participant: CommunicationUserIdentifier(identifier: "userId"))

```

### <a name="remove-a-participant-from-a-call"></a>通話から参加者を削除する
通話から参加者 (ユーザーまたは電話番号のいずれか) を削除する場合は、`removeParticipant` API を呼び出すことができます。 これは、非同期に解決されます。

```swift

call!.remove(participant: remoteParticipantAdded) { (error) in
    if (error == nil) {
        print("Successfully removed participant")
    } else {
        print("Failed to remove participant")
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

let renderer: Renderer? = Renderer(remoteVideoStream: remoteParticipantVideoStream)
let targetRemoteParticipantView: RendererView? = renderer?.createView(with: RenderingOptions(scalingMode: ScalingMode.crop))
// To update the scaling mode later
targetRemoteParticipantView.update(scalingMode: ScalingMode.fit)

```

### <a name="get-remote-video-renderer-methods-and-properties"></a>リモート動画レンダラーのメソッドとプロパティを取得する

```swift
// [Synchronous] dispose() - dispose renderer and all `RendererView` associated with this renderer. To be called when you have removed all associated views from the UI.
remoteVideoRenderer.dispose()
```

## <a name="manage-devices"></a>デバイスの管理

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
var localCameras = deviceManager.cameras! // [VideoDeviceInfo, VideoDeviceInfo...]
// enumerate local cameras
var localMicrophones = deviceManager.microphones! // [AudioDeviceInfo, AudioDeviceInfo...]
// enumerate local cameras
var localSpeakers = deviceManager.speakers! // [AudioDeviceInfo, AudioDeviceInfo...]
``` 

### <a name="set-the-default-microphone-or-speaker"></a>既定のマイクまたはスピーカーを設定する

デバイス マネージャーを使って、通話の開始時に使用される既定のデバイスを設定できます。 スタックの既定値が設定されていない場合、Communication Services は OS の既定値にフォールバックします。

```swift
// get first microphone
var firstMicrophone = self.deviceManager!.cameras!.first
// [Synchronous] set microphone
deviceManager.setMicrophone(microphoneDevice: firstMicrophone)
// get first speaker
var firstSpeaker = self.deviceManager!.speakers!
// [Synchronous] set speaker
deviceManager.setSpeaker(speakerDevice: firstSpeaker)
```

### <a name="get-a-local-camera-preview"></a>ローカル カメラのプレビューを取得する

`Renderer` を使用して、ローカル カメラからのストリームのレンダリングを開始できます。 このストリームは、他の参加者には送信されません。ローカル プレビュー フィードです。 これは、非同期アクションです。

```swift

let camera: VideoDeviceInfo = self.deviceManager!.getCameraList()![0]
let localVideoStream: LocalVideoStream = LocalVideoStream(camera: camera)
let renderer: Renderer = Renderer(localVideoStream: localVideoStream)
self.view = try renderer!.createView()

```

### <a name="get-local-camera-preview-properties"></a>ローカル カメラのプレビューのプロパティを取得する

レンダラーには、レンダリングを制御できるプロパティとメソッドのセットが用意されています。

```swift

// Constructor can take in LocalVideoStream or RemoteVideoStream
let localRenderer = Renderer(localVideoStream:localVideoStream)
let remoteRenderer = Renderer(remoteVideoStream:remoteVideoStream)

// [StreamSize] size of the rendering view
localRenderer.size

// [RendererDelegate] an object you provide to receive events from this Renderer instance
localRenderer.delegate

// [Synchronous] create view
try! localRenderer.createView()

// [Synchronous] create view with rendering options
try! localRenderer.createView(with: RenderingOptions(scalingMode: ScalingMode.fit))

// [Synchronous] dispose rendering view
localRenderer.dispose()

```

## <a name="subscribe-to-notifications"></a>通知に登録する

値が変更されたときに通知を受け取るように、ほとんどのプロパティとコレクションをサブスクライブすることができます。

### <a name="properties"></a>Properties
`property changed` イベントをサブスクライブするには、次のコードを使用します。

```swift
call.delegate = self
// Get the property of the call state by getting on the call's state member
public func onCallStateChanged(_ call: Call!,
                               args: PropertyChangedEventArgs!)
{
    print("Callback from SDK when the call state changes, current state: " + call.state.rawValue)
}

 // to unsubscribe
 self.call.delegate = nil

```

### <a name="collections"></a>コレクション
`collection updated` イベントをサブスクライブするには、次のコードを使用します。

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
