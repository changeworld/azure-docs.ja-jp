---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 3ab9331dddb90803867f6cfb5118aa33bdd313d0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "128699164"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-web.md)]

## <a name="place-a-call"></a>通話を行う

通話を作成して開始するには、`callAgent` で API のいずれかを使用し、Communication Services ID SDK を使用して作成したユーザーを指定する必要があります。

通話の作成と開始は同期的に行われます。 `call` インスタンスを使用すると、通話イベントをサブスクライブできます。

### <a name="place-a-1n-call-to-a-user-or-pstn"></a>ユーザーまたは PSTN と 1:n の通話を行う

別の Communication Services ユーザーと通話を行うには、`callAgent` で `startCall` メソッドを呼び出し、[Communication Services 管理ライブラリで作成した](../../../../quickstarts/access-tokens.md)、受信者の `CommunicationUserIdentifier` を渡します。

相手がユーザー 1 名の場合の 1 対 1 の通話には、次のコードを使用します。

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const oneToOneCall = callAgent.startCall([userCallee]);
```

公衆交換電話網 (PSTN) の通話を行うには、`callAgent` で `startCall` メソッドを使用し、受信者の `PhoneNumberIdentifier` を渡します。 PSTN 通話を許可するように Communication Services リソースを構成する必要があります。

PSTN 番号に電話をかけるときは、代替の発信者 ID を指定します。 代替の発信者 ID は、PSTN 通話の呼び出し元を識別する (E.164 標準に基づく) 電話番号です。 これは、着信通話の場合に通話受信者に表示される電話番号です。

> [!NOTE]
> PSTN 通話は、現在プライベート プレビュー段階にあります。 アクセスするには、[早期導入者プログラムに申し込みます](https://aka.ms/ACS-EarlyAdopter)。

相手が PSTN 番号の場合の 1 対 1 の通話には、次のコードを使用します。
```js
const pstnCallee = { phoneNumber: '<ACS_USER_ID>' }
const alternateCallerId = {alternateCallerId: '<ALTERNATE_CALLER_ID>'};
const oneToOneCall = callAgent.startCall([pstnCallee], {alternateCallerId});
```

相手がユーザー 1 名と PSTN 番号の場合の 1 対 n 名の通話には、次のコードを使用します。

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const pstnCallee = { phoneNumber: '<PHONE_NUMBER>'};
const alternateCallerId = {alternateCallerId: '<ALTERNATE_CALLER_ID>'};
const groupCall = callAgent.startCall([userCallee, pstnCallee], {alternateCallerId});
```

### <a name="join-a-group-call"></a>グループ通話に参加する

> [!NOTE]
> `groupId` パラメーターはシステム メタデータと見なされ、システムの実行に必要な操作のために Microsoft によって使用される場合があります。 `groupId` 値には個人データを含めないでください。 Microsoft ではこのパラメーターを個人データとして扱わず、そのコンテンツが Microsoft の従業員に表示されたり、長期間保管されたりすることがあります。
>
> `groupId` パラメーターのデータは GUID 形式でなければなりません。 ご利用のシステム内で個人データと見なされない、ランダムに生成された GUID を使用することをお勧めします。
>

新しいグループ通話を始めるか、進行中のグループ通話に参加するには、`join` メソッドを使用して、`groupId` プロパティを含むオブジェクトを渡します。 `groupId` 値には GUID を指定する必要があります。

```js
const context = { groupId: '<GUID>'};
const call = callAgent.join(context);
```

## <a name="receive-an-incoming-call"></a>着信した通話に出る

ログインしている ID が着信通話を受信すると、`callAgent` インスタンスで `incomingCall` イベントが生成されます。 このイベントをリッスンするには、次のいずれかのオプションを使用してサブスクライブします。

```js
const incomingCallHandler = async (args: { incomingCall: IncomingCall }) => {
    const incomingCall = args.incomingCall; 

    // Get incoming call ID
    var incomingCallId = incomingCall.id

    // Get information about this Call. This API is provided as a preview for developers
    // and may change based on feedback that we receive. Do not use this API in a production environment.
    // To use this api please use 'beta' release of ACS Calling Web SDK
    var callInfo = incomingCall.info;

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
callAgentInstance.on('incomingCall', incomingCallHandler);
```

`incomingCall` イベントには、受け入れまたは拒否できる `incomingCall` インスタンスが含まれています。

ビデオをオンにして通話を開始したり、通話に参加したり、通話に出たりした際に、指定されたビデオ カメラ デバイスが別のプロセスで使用されていたり、システムで無効になっていたりした場合には、ビデオがオフの状態で通話が開始され、通話診断の cameraStartFailed が True になります。

## <a name="mute-and-unmute"></a>ミュートとミュート解除

ローカル エンドポイントをミュートまたはミュート解除するには、非同期 API の `mute` と `unmute` を使用します。

```js
//mute local device
await call.mute();

//unmute local device
await call.unmute();
```

## <a name="manage-remote-participants"></a>リモート参加者を管理する

すべてのリモート参加者は `RemoteParticipant` 型で表され、通話インスタンスの `remoteParticipants` コレクションを通して使用できます。

### <a name="list-the-participants-in-a-call"></a>通話の参加者を一覧表示する

`remoteParticipants` コレクションで、通話におけるリモート参加者の一覧が返されます。

```js
call.remoteParticipants; // [remoteParticipant, remoteParticipant....]
```

### <a name="add-a-participant-to-a-call"></a>通話に参加者を追加する

通話に参加者 (ユーザーまたは電話番号) を追加するには、`addParticipant` を使用します。 `Identifier` の種類のいずれかを指定します。 `remoteParticipant` インスタンスを同期的に返します。 参加者が通話に正常に追加されると、通話から `remoteParticipantsUpdated` イベントが発生します。

```js
const userIdentifier = { communicationUserId: '<ACS_USER_ID>' };
const pstnIdentifier = { phoneNumber: '<PHONE_NUMBER>' }
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier, {alternateCallerId: '<ALTERNATE_CALLER_ID>'});
```

### <a name="remove-a-participant-from-a-call"></a>通話から参加者を削除する

通話から参加者 (ユーザーまたは電話番号) を削除するには、`removeParticipant` を呼び出します。 `Identifier` の種類のいずれかを渡す必要があります。 このメソッドは、参加者が通話から削除されると、非同期的に終了します。 参加者は、`remoteParticipants` コレクションからも削除されます。

```js
const userIdentifier = { communicationUserId: '<ACS_USER_ID>' };
const pstnIdentifier = { phoneNumber: '<PHONE_NUMBER>' }
await call.removeParticipant(userIdentifier);
await call.removeParticipant(pstnIdentifier);
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
- `InLobby`: リモート参加要素がロビーにあることを示します。
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

## <a name="check-call-properties"></a>通話のプロパティを確認する

通話の一意の ID (文字列) を取得します。

```js
const callId: string = call.id;
```
通話に関する情報を取得する:
> [!NOTE]
> この API は開発者向けにプレビューとして提供されており、寄せられたフィードバックにもとづいて変更される場合があります。 この API は運用環境で使用しないでください。 この API を使用するには、ACS Calling Web SDK の "ベータ" リリースを使用してください
```js
const callInfo = call.info;
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
- `InLobby`: ユーザーがロビーにいることを示します。
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