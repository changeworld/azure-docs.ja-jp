---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: c2deff9146d0f6ec28e3e37981bcb82d3e056947
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "128699165"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-ios.md)]

> [!NOTE]
> アプリケーションでは、イベント デリゲートを実装する際に、イベント サブスクリプションを必要とするオブジェクトへの強い参照を保持する必要があります。 たとえば、`call.addParticipant` メソッドの呼び出し時に `RemoteParticipant` オブジェクトが返され、アプリケーションによって `RemoteParticipantDelegate` でリッスンするようにデリゲートが設定された場合には、そのアプリケーションによって `RemoteParticipant` オブジェクトへの強い参照が保持される必要があります。 それ以外の場合に、このオブジェクトが収集されると、Calling SDK によってオブジェクトの呼び出しが試行されるときに、デリゲートによって致命的な例外がスローされます。

## <a name="place-an-outgoing-call"></a>発信通話を行う

通話を作成して開始するには、`CallAgent` でいずれかの API を呼び出し、Communication Services 管理クライアント SDK を使用してプロビジョニングしたユーザーの Communication Services ID を指定する必要があります。

通話の作成と開始は同期的に行われます。 通話のすべてのイベントにサブスクライブできる通話インスタンスを受信します。

### <a name="place-a-11-call-to-a-user-or-a-1n-call-with-users-and-pstn"></a>ユーザーに対する 1:1 の通話、またはユーザーおよび PSTN と 1:n の通話を行う

```swift
let callees = [CommunicationUser(identifier: 'UserId')]
self.callAgent?.startCall(participants: callees, options: StartCallOptions()) { (call, error) in
     if error == nil {
         print("Successfully started outgoing call")
         self.call = call
     } else {
         print("Failed to start outgoing call")
     }
}
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>ユーザーと PSTN で 1:n の通話を行う
PSTN への通話を行うには、Communication Services で取得した電話番号を指定する必要があります。

```swift
let pstnCallee = PhoneNumberIdentifier(phoneNumber: '+1999999999')
let callee = CommunicationUserIdentifier('UserId')
self.callAgent?.startCall(participants: [pstnCallee, callee], options: StartCallOptions()) { (groupCall, error) in
     if error == nil {
         print("Successfully started outgoing call to multiple participants")
         self.call = groupCall
     } else {
         print("Failed to start outgoing call to multiple participants")
     }
}
```

## <a name="join-a-group-call"></a>グループ通話に参加する
通話に参加するには、`CallAgent` でいずれかの API を呼び出す必要があります。

```swift
let groupCallLocator = GroupCallLocator(groupId: UUID(uuidString: "uuid_string")!)
self.callAgent?.join(with: groupCallLocator, joinCallOptions: JoinCallOptions()) { (call, error) in
    if error == nil {
        print("Successfully joined group call")
        self.call = call
    } else {
        print("Failed to join group call")
    }
}
```

## <a name="subscribe-to-an-incoming-call"></a>着信通話をサブスクライブする
着信通話イベントをサブスクライブします。

```swift
final class IncomingCallHandler: NSObject, CallAgentDelegate, IncomingCallDelegate
{
    // Event raised when there is an incoming call
    public func callAgent(_ callAgent: CallAgent, didReceiveIncomingCall incomingcall: IncomingCall) {
        self.incomingCall = incomingcall
        // Subscribe to get OnCallEnded event
        self.incomingCall?.delegate = self
    }

    // Event raised when incoming call was not answered
    public func incomingCall(_ incomingCall: IncomingCall, didEnd args: PropertyChangedEventArgs) {
        print("Incoming call was not answered")
        self.incomingCall = nil
    }
}
```

### <a name="accept-an-incoming-call"></a>電話の着信を受け入れる
通話を受け入れるには、`IncomingCall` オブジェクトに対して `accept` メソッドを呼び出します。

```swift
self.incomingCall!.accept(options: AcceptCallOptions()) { (call, error) in
   if (error == nil) {
       print("Successfully accepted incoming call")
       self.call = call
   } else {
       print("Failed to accept incoming call")
   }
}

let firstCamera: VideoDeviceInfo? = self.deviceManager!.cameras.first
localVideoStreams = [LocalVideoStream]()
localVideoStreams!.append(LocalVideoStream(camera: firstCamera!))
let acceptCallOptions = AcceptCallOptions()
acceptCallOptions.videoOptions = VideoOptions(localVideoStreams: localVideoStreams!)
if let incomingCall = self.incomingCall {
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

## <a name="manage-remote-participants"></a>リモート参加者を管理する

すべてのリモート参加者は `RemoteParticipant` 型で表され、通話インスタンスの `remoteParticipants` コレクションを通して使用できます。

### <a name="list-participants-in-a-call"></a>通話の参加者の一覧を取得する

```swift
call.remoteParticipants
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

### <a name="get-remote-participant-properties"></a>リモート参加者のプロパティを取得する

```swift
// [RemoteParticipantDelegate] delegate - an object you provide to receive events from this RemoteParticipant instance
var remoteParticipantDelegate = remoteParticipant.delegate

// [CommunicationIdentifier] identity - same as the one used to provision a token for another user
var identity = remoteParticipant.identifier

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