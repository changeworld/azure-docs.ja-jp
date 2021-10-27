---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 7d2a6415a2cc03513606183c290443c453a82577
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2021
ms.locfileid: "130143813"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-android.md)]

## <a name="place-a-call"></a>通話を行う

通話を作成して開始するには、`CallAgent.startCall()` メソッドを呼び出して、通話先の `Identifier` を指定する必要があります。
グループ通話に参加するには、`CallAgent.join()` メソッドを呼び出して、groupId を指定する必要があります。 グループ ID は、GUID または UUID 形式にする必要があります。

通話の作成と開始は同期的に行われます。 通話インスタンスを使用すると、通話のすべてのイベントをサブスクライブできます。

### <a name="place-a-11-call-to-a-user"></a>ユーザーと 1:1 の通話を行う
別の Communication Services ユーザーと通話するには、`callAgent` で `call` メソッドを呼び出し、`communicationUserId` キーを使用してオブジェクトを渡します。
```java
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
CommunicationUserIdentifier acsUserId = new CommunicationUserIdentifier(<USER_ID>);
CommunicationUserIdentifier participants[] = new CommunicationUserIdentifier[]{ acsUserId };
call oneToOneCall = callAgent.startCall(appContext, participants, startCallOptions);
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>ユーザーおよび PSTN と 1:n の通話を行う
> [!WARNING]
> 現在、PSTN 通話は使用できません

ユーザーおよび PSTN 番号との 1:n の通話を行うには、通話先の電話番号を指定する必要があります。
PSTN 通話を許可するように Communication Services リソースを構成する必要があります。
```java
CommunicationUserIdentifier acsUser1 = new CommunicationUserIdentifier(<USER_ID>);
PhoneNumberIdentifier acsUser2 = new PhoneNumberIdentifier("<PHONE_NUMBER>");
CommunicationIdentifier participants[] = new CommunicationIdentifier[]{ acsUser1, acsUser2 };
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
Call groupCall = callAgent.startCall(participants, startCallOptions);
```

## <a name="accept-a-call"></a>着信を受け入れる
着信を受け入れるには、呼び出しオブジェクトに対して "accept" メソッドを呼び出します。

```java
Context appContext = this.getApplicationContext();
IncomingCall incomingCall = retrieveIncomingCall();
Call call = incomingCall.accept(context).get();
```

ビデオ カメラを使用して着信を受け入れるには:

```java
Context appContext = this.getApplicationContext();
IncomingCall incomingCall = retrieveIncomingCall();
AcceptCallOptions acceptCallOptions = new AcceptCallOptions();
VideoDeviceInfo desiredCamera = callClient.getDeviceManager().get().getCameraList().get(0);
acceptCallOptions.setVideoOptions(new VideoOptions(new LocalVideoStream(desiredCamera, appContext)));
Call call = incomingCall.accept(context, acceptCallOptions).get();
```

着信は、`callAgent` オブジェクトの `onIncomingCall` イベントをサブスクライブすることによって取得できます。

```java
// Assuming "callAgent" is an instance property obtained by calling the 'createCallAgent' method on CallClient instance 
public Call retrieveIncomingCall() {
    IncomingCall incomingCall;
    callAgent.addOnIncomingCallListener(new IncomingCallListener() {
        void onIncomingCall(IncomingCall inboundCall) {
            // Look for incoming call
            incomingCall = inboundCall;
        }
    });
    return incomingCall;
}
```

## <a name="join-a-group-call"></a>グループ通話に参加する
新しいグループ通話を始めるか、進行中のグループ通話に参加するには、"join" メソッドを呼び出して、`groupId` プロパティを含むオブジェクトを渡す必要があります。 値には GUID を指定する必要があります
```java
Context appContext = this.getApplicationContext();
GroupCallLocator groupCallLocator = new GroupCallLocator("<GUID>");
JoinCallOptions joinCallOptions = new JoinCallOptions();

call = callAgent.join(context, groupCallLocator, joinCallOptions);
```

## <a name="call-properties"></a>通話のプロパティ

この通話の一意の ID を取得します。

```java
String callId = call.getId();
```

通話の他の参加者について知るには、`call` インスタンスの `remoteParticipant` コレクションを調べます。

```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants();
```

通話が着信の場合の呼び出し元の ID です。

```java
CommunicationIdentifier callerId = call.getCallerInfo().getIdentifier();
```

通話の状態を取得します。 

```java
CallState callState = call.getState();
```

これにより、通話の現在の状態を表す文字列が返されます。
* "NONE" - 通話の初期状態です
* "EARLY_MEDIA" - 通話が接続される前の、アナウンスの再生状態を示します
* "CONNECTING" - 通話が発信または受諾された後の初期遷移状態です
* "RINGING" - 発信通話の場合 - リモート参加者に対して通話が発信されています
* "CONNECTED" - 通話は接続されています
* "LOCAL_HOLD" - 通話はローカル参加者によって保留にされており、ローカル エンドポイントとリモート参加者の間でメディアは送信されていません
* "REMOTE_HOLD" - 通話はリモート参加者によって保留にされており、ローカル エンドポイントとリモート参加者の間でメディアは送信されていません
* "DISCONNECTING" - 通話は、"Disconnected" 状態になる前の移行状態です
* "DISCONNECTED" - 通話の最終状態です
* "IN_LOBBY" - Teams の会議の相互運用性のためにロビーにいます

通話が終了した理由を確認するには、`callEndReason` プロパティを調べます。 これには、コードとサブコードが含まれます。 

```java
CallEndReason callEndReason = call.getCallEndReason();
int code = callEndReason.getCode();
int subCode = callEndReason.getSubCode();
```

現在の通話が着信通話と発信通話のどちらであるかを確認するには、`callDirection` プロパティを調べます。

```java
CallDirection callDirection = call.getCallDirection(); 
// callDirection == CallDirection.INCOMING for incoming call
// callDirection == CallDirection.OUTGOING for outgoing call
```

現在マイクがミュートされているかどうかを確認するには、`muted` プロパティを調べます。

```java
boolean muted = call.isMuted();
```

アクティブな動画ストリームを調べるには、`localVideoStreams` コレクションを確認します。

```java
List<LocalVideoStream> localVideoStreams = call.getLocalVideoStreams();
```

## <a name="mute-and-unmute"></a>ミュートとミュート解除

ローカル エンドポイントをミュートまたはミュート解除するには、非同期 API の `mute` と `unmute` を使用できます。

```java
Context appContext = this.getApplicationContext();
call.mute(appContext).get();
call.unmute(appContext).get();
```
## <a name="change-the-volume-of-the-call"></a>通話の音量を変更する

通話中は、ユーザーが電話のハードウェア音量キーを使用して通話ボリュームを変更できるようにする必要があります。
これを行うには、通話が行われるアクティビティで `setVolumeControlStream` メソッドをストリーム型 `AudioManager.STREAM_VOICE_CALL` と共に使用します。
この場合、ハードウェアの音量キー (音量スライダーに表示される電話アイコンまたは類似のもの) を使用して通話の音量を変更できますが、アラーム、メディア、システム全体の音量などの他のサウンド プロファイルの音量は変更されません。 詳細については、「[音声出力の変更の処理 | Android 開発者](https://developer.android.com/guide/topics/media-apps/volume-and-earphones)」を確認してください。

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    ...
    setVolumeControlStream(AudioManager.STREAM_VOICE_CALL);
}
```

## <a name="remote-participants-management"></a>リモート参加者の管理

すべてのリモート参加者は `RemoteParticipant` 型で表され、通話インスタンスの `remoteParticipants` コレクションを通して使用できます。

### <a name="list-participants-in-a-call"></a>通話の参加者の一覧を取得する
`remoteParticipants` コレクションで、特定の通話におけるリモート参加者の一覧が返されます。
```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants(); // [remoteParticipant, remoteParticipant....]
```

### <a name="add-a-participant-to-a-call"></a>通話に参加者を追加する

通話に参加者を追加するには (ユーザーまたは電話番号のいずれか)、`addParticipant` を呼び出します。 これにより、リモート参加者インスタンスが同期的に返されます。

```java
const acsUser = new CommunicationUserIdentifier("<acs user id>");
const acsPhone = new PhoneNumberIdentifier("<phone number>");
RemoteParticipant remoteParticipant1 = call.addParticipant(acsUser);
AddPhoneNumberOptions addPhoneNumberOptions = new AddPhoneNumberOptions(new PhoneNumberIdentifier("<alternate phone number>"));
RemoteParticipant remoteParticipant2 = call.addParticipant(acsPhone, addPhoneNumberOptions);
```

### <a name="remove-participant-from-a-call"></a>通話から参加者を削除する
通話から参加者を削除するには (ユーザーまたは電話番号のいずれか)、`removeParticipant` を呼び出します。
参加者が通話から削除されると、非同期的に解決されます。
参加者は、`remoteParticipants` コレクションからも削除されます。
```java
RemoteParticipant acsUserRemoteParticipant = call.getParticipants().get(0);
RemoteParticipant acsPhoneRemoteParticipant = call.getParticipants().get(1);
call.removeParticipant(acsUserRemoteParticipant).get();
call.removeParticipant(acsPhoneRemoteParticipant).get();
```

### <a name="remote-participant-properties"></a>リモート参加者のプロパティ
特定のリモート参加者には、プロパティのセットとコレクションが関連付けられています。

* このリモート参加者の識別子を取得します。
ID は "Identifier" 型の 1 つです。
    ```java
    CommunicationIdentifier participantIdentifier = remoteParticipant.getIdentifier();
    ```

* このリモート参加者の状態を取得します。
    ```java
    ParticipantState state = remoteParticipant.getState();
    ```
状態は次のいずれかです
* "IDLE" - 初期状態です
* "EARLY_MEDIA" - 参加者が通話に接続される前に、アナウンスが再生されています
* 'RINGING' - 参加者の通話が発信されています
* "CONNECTING" - 参加者が通話に接続している間の遷移状態です
* "CONNECTED" - 参加者は通話に接続されています
* "HOLD" - 参加者は保留中です
* 'IN_LOBBY' - 参加者がロビーで許可されるのを待機しています。 現在、Teams の相互運用シナリオでのみ使用されています
* "DISCONNECTED" - 最終状態 - 参加者は通話から切断されました

* 参加者が通話を終了した理由を確認するには、`callEndReason` プロパティを調べます。
    ```java
    CallEndReason callEndReason = remoteParticipant.getCallEndReason();
    ```

* このリモート参加者がミュートされているかどうかを確認するには、`isMuted` プロパティを調べます。
    ```java
    boolean isParticipantMuted = remoteParticipant.isMuted();
    ```

* このリモート参加者が話しているかどうかを確認するには、`isSpeaking` プロパティを調べます。
    ```java
    boolean isParticipantSpeaking = remoteParticipant.isSpeaking();
    ```

* 特定の参加者がこの通話で送信しているすべての動画ストリームを調べるには、`videoStreams` コレクションを確認します。
    ```java
    List<RemoteVideoStream> videoStreams = remoteParticipant.getVideoStreams(); // [RemoteVideoStream, RemoteVideoStream, ...]
    ```
## <a name="using-foreground-services"></a>フォアグラウンド サービスの使用

アプリケーションがバックグラウンドで動作しているときでも、ユーザーに表示されるタスクを実行する場合は、[フォアグラウンド サービス](https://developer.android.com/guide/components/foreground-services)を使用できます。

フォアグラウンド サービスを使用すると、たとえば、アプリケーションにアクティブな通話があるときに、ユーザーに通知を表示したままにすることができます。 これにより、ユーザーがホーム画面に移動した場合や、[最近の画面](https://developer.android.com/guide/components/activities/recents)からアプリケーションを削除した場合でも、通話は引き続きアクティブになります。

通話中にフォアグラウンド サービスを使用しない場合、ホーム画面に移動しても通話は継続されますが、アプリケーションを最近の画面から削除すると、Android OS によってアプリケーションのプロセスを強制終了された場合に通話が停止する可能性があります。

たとえば、通話の開始時または参加時にフォアグラウンド サービスを開始することをお勧めします。

```java
call = callAgent.startCall(context, participants, options);
startService(yourForegroundServiceIntent);
```

また、通話を切断したときや、通話の状態が切断済みの場合にフォアグラウンド サービスを停止します。

```java
call.hangUp(new HangUpOptions()).get();
stopService(yourForegroundServiceIntent);
```

### <a name="notes-on-using-foreground-services"></a>フォアグラウンド サービスの使用に関する注意事項

アプリが最近の一覧から削除されたときに、既に実行されているフォアグラウンド サービスを停止するようなシナリオの場合、ユーザーに表示される通知が削除されても、Android OS によってアプリケーションのプロセスが一定期間維持される可能性があることに注意してください。つまり、この期間中も通話はアクティブな可能性があります。

たとえば、service `onTaskRemoved` メソッドでアプリケーションによってフォアグラウンド サービスが停止されている場合、[アクティビティのライフサイクル](https://developer.android.com/guide/components/activities/activity-lifecycle)に従って音声とビデオが開始または停止される可能性があります (`onDestroy` メソッドのオーバーライドでアクティビティが破棄されたときに音声とビデオが停止されるなど)。
