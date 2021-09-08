---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 06/30/2021
ms.author: rifox
ms.openlocfilehash: 1bba08e751afa5e3cd5c51401ca92e3b5bd9def1
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123454275"
---
## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- デプロイ済みの Communication Services リソース。 [Communication Services リソースを作成します](../../../create-communication-resource.md)。
- 通話クライアントを有効にするためのユーザー アクセス トークン。 詳細については、[アクセス トークンの作成と管理](../../../access-tokens.md)に関する記事を参照してください。
- 省略可能: クイックスタートを完了して、[アプリケーションに音声通話を追加します](../../getting-started-with-calling.md)。

## <a name="install-the-sdk"></a>SDK のインストール

`npm install` コマンドを使用して、JavaScript 用の Azure Communication Services 通話と共通の各 SDK をインストールします。

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```
Communication Services Web Calling SDK は、`https` 経由で使用する必要があります。 ローカル開発の場合は、`localhost` またはローカルの "file:" を使用します。

## <a name="documentation-support"></a>ドキュメントによるサポート
- [リリース ノート](https://github.com/Azure/Communication/blob/master/releasenotes/acs-javascript-calling-library-release-notes.md)
- [問題やバグを GitHub で報告する](https://github.com/Azure/Communication/issues)
- [サンプル アプリケーション](../../../../samples/overview.md)
- [API リファレンス](/javascript/api/azure-communication-services/@azure/communication-calling/?preserve-view=true&view=azure-communication-services-js)

## <a name="models"></a>モデル
### <a name="object-model"></a>オブジェクト モデル

Azure Communication Services Calling SDK の主な機能のいくつかは、次のクラスとインターフェイスによって処理されます。

| 名前                                | 説明                                                                                                                              |
| ----------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| `CallClient`                        | Calling SDK へのメイン エントリ ポイント。                                                                                                 |
| `AzureCommunicationTokenCredential` | `callAgent` のインスタンス化に使用される `CommunicationTokenCredential` インターフェイスを実装します。                                       |
| `CallAgent`                         | 通話を開始および管理するために使用されます。                                                                                                          |
| `DeviceManager`                     | メディア デバイスを管理するために使用されます。                                                                                                            |
| `Call`                              | 通話を表すために使用します                                                                                                              |
| `LocalVideoStream`                  | ローカル システム上のカメラ デバイスのローカル ビデオ ストリームを作成するために使用します。                                                          |
| `RemoteParticipant`                 | 通話のリモート参加者を表すために使用します                                                                                   |
| `RemoteVideoStream`                 | リモート参加者からのリモート ビデオ ストリームを表すために使用します。                                                                  |

> [!NOTE]
> Calling SDK オブジェクトのインスタンスは、プレーンな JavaScript オブジェクトではないと考えるようにしてください。 これらはさまざまなクラスの実際のインスタンスであるため、シリアル化できません。

### <a name="events-model"></a>イベント モデル
Calling SDK のオブジェクトにはそれぞれ、いくつかのプロパティとコレクションが備わっています。このプロパティとコレクションの値は、オブジェクトの有効期間内に随時変化します。
オブジェクトのイベントをサブスクライブするには、`on()` メソッドを使用します。逆に、サブスクライブを解除するには、`off()` メソッドを使用します。

#### <a name="properties"></a>Properties
- 初期値を調べたうえで、値の更新に対応するための `'<property>Changed'` イベントをサブスクライブする必要があります。

#### <a name="collections"></a>コレクション
- 初期値を調べたうえで、値の更新に対応するための `'<collection>Updated'` イベントをサブスクライブする必要があります。
- `'<collection>Updated'` イベントのペイロードには、コレクションに追加された値が格納される `added` 配列があります。
- また、`'<collection>Updated'` イベントのペイロードには、コレクションから削除された値が格納される `removed` 配列もあります。

```js
/*************************************
 * Example code - client.js          *
 * Convert this script into a        *
 * bundle.js that your html index    *
 * page can use.                     *
 *************************************/

// Make sure to install the necessary dependencies
const { CallClient, VideoStreamRenderer, LocalVideoStream } = require('@azure/communication-calling');
const { AzureCommunicationTokenCredential } = require('@azure/communication-common');
const { AzureLogger, setLogLevel } = require("@azure/logger");
// Set the log level and output
setLogLevel('verbose');
AzureLogger.log = (...args) => {
    console.log(...args);
};

// Calling web sdk objects
let callAgent;
let deviceManager;
let call;
let incomingCall;
let localVideoStream;
let localVideoStreamRenderer;

// UI widgets
let userAccessToken = document.getElementById('user-access-token');
let calleeAcsUserId = document.getElementById('callee-acs-user-id');
let initializeCallAgentButton = document.getElementById('initialize-call-agent');
let startCallButton = document.getElementById('start-call-button');
let acceptCallButton = document.getElementById('accept-call-button');
let startVideoButton = document.getElementById('start-video-button');
let stopVideoButton = document.getElementById('stop-video-button');
let connectedLabel = document.getElementById('connectedLabel');
let remoteVideoContainer = document.getElementById('remoteVideoContainer');
let localVideoContainer = document.getElementById('localVideoContainer');

// Instantiate the Call Agent.
initializeCallAgentButton.onclick = async () => {
    try {
        const callClient = new CallClient(); 
        tokenCredential = new AzureCommunicationTokenCredential(userAccessToken.value.trim());
        callAgent = await callClient.createCallAgent(tokenCredential)
        // Set up a camera device to use.
        deviceManager = await callClient.getDeviceManager();
        await deviceManager.askDevicePermission({ video: true });
        await deviceManager.askDevicePermission({ audio: true });
        // Listen for an incoming call to accept.
        callAgent.on('incomingCall', async (args) => {
            try {
                incomingCall = args.incomingCall;
                acceptCallButton.disabled = false;
                startCallButton.disabled = true;
            } catch (error) {
                console.error(error);
            }
        });

        startCallButton.disabled = false;
        initializeCallAgentButton.disabled = true;
    } catch(error) {
        console.error(error);
    }
}

// Start an out-going call.
startCallButton.onclick = async () => {
    try {
        const localVideoStream = await createLocalVideoStream();
        const videoOptions = localVideoStream ? { localVideoStreams: [localVideoStream] } : undefined;
        call = callAgent.startCall([{ communicationUserId: calleeAcsUserId.value.trim() }], { videoOptions });
        // Subscribe to the call's properties and events.
        subscribeToCall(call);
    } catch (error) {
        console.error(error);
    }
}

// Accept the incoming call.
acceptCallButton.onclick = async () => {
    try {
        const localVideoStream = await createLocalVideoStream();
        const videoOptions = localVideoStream ? { localVideoStreams: [localVideoStream] } : undefined;
        call = await incomingCall.accept({ videoOptions });
        // Subscribe to the call's properties and events.
        subscribeToCall(call);
    } catch (error) {
        console.error(error);
    }
}

// Subscribe to a call obj.
// Listen for property changes and collection udpates.
subscribeToCall = (call) => {
    try {
        // Inspect the initial call.id value.
        console.log(`Call Id: ${call.id}`);
        //Subsribe to call's 'idChanged' event for value changes.
        call.on('idChanged', () => {
            console.log(`Call Id changed: ${call.id}`); 
        });

        // Inspect the initial call.state value.
        console.log(`Call state: ${call.state}`);
        // Subscribe to call's 'stateChanged' event for value changes.
        call.on('stateChanged', async () => {
            console.log(`Call state changed: ${call.state}`);
            if(call.state === 'Connected') {
                connectedLabel.hidden = false;
                acceptCallButton.disabled = true;
                startCallButton.disabled = true;
                startVideoButton.disabled = false;
                stopVideoButton.disabled = false
            } else if (call.state === 'Disconnected') {
                connectedLabel.hidden = true;
                startCallButton.disabled = false;
                console.log(`Call ended, call end reason={code=${call.callEndReason.code}, subCode=${call.callEndReason.subCode}}`);
            }   
        });

        call.localVideoStreams.forEach(async (lvs) => {
            localVideoStream = lvs;
            await displayLocalVideoStream();
        });
        call.on('localVideoStreamsUpdated', e => {
            e.added.forEach(async (lvs) => {
                localVideoStream = lvs;
                await displayLocalVideoStream();
            });
            e.removed.forEach(lvs => {
               removeLocalVideoStream();
            });
        });
        
        // Inspect the call's current remote participants and subscribe to them.
        call.remoteParticipants.forEach(remoteParticipant => {
            subscribeToRemoteParticipant(remoteParticipant);
        })
        // Subscribe to the call's 'remoteParticipantsUpdated' event to be
        // notified when new participants are added to the call or removed from the call.
        call.on('remoteParticipantsUpdated', e => {
            // Subscribe to new remote participants that are added to the call.
            e.added.forEach(remoteParticipant => {
                subscribeToRemoteParticipant(remoteParticipant)
            });
            // Unsubscribe from participants that are removed from the call
            e.removed.forEach(remoteParticipant => {
                console.log('Remote participant removed from the call.');
            })
        });
    } catch (error) {
        console.error(error);
    }
}

// Subscribe to a remote participant obj.
// Listen for property changes and collection udpates.
subscribeToRemoteParticipant = (remoteParticipant) => {
    try {
        // Inspect the initial remoteParticipant.state value.
        console.log(`Remote participant state: ${remoteParticipant.state}`);
        // Subscribe to remoteParticipant's 'stateChanged' event for value changes.
        remoteParticipant.on('stateChanged', () => {
            console.log(`Remote participant state changed: ${remoteParticipant.state}`);
        });

        // Inspect the remoteParticipants's current videoStreams and subscribe to them.
        remoteParticipant.videoStreams.forEach(remoteVideoStream => {
            subscribeToRemoteVideoStream(remoteVideoStream)
        });
        // Subscribe to the remoteParticipant's 'videoStreamsUpdated' event to be
        // notified when the remoteParticiapant adds new videoStreams and removes video streams.
        remoteParticipant.on('videoStreamsUpdated', e => {
            // Subscribe to new remote participant's video streams that were added.
            e.added.forEach(remoteVideoStream => {
                subscribeToRemoteVideoStream(remoteVideoStream)
            });
            // Unsubscribe from remote participant's video streams that were removed.
            e.removed.forEach(remoteVideoStream => {
                console.log('Remote participant video stream was removed.');
            })
        });
    } catch (error) {
        console.error(error);
    }
}

// Subscribe to a remote participant's remote video stream obj.
// Listen for property changes and collection udpates.
// When their remote video streams become available, display them in the UI.
subscribeToRemoteVideoStream = async (remoteVideoStream) => {
    // Create a video stream renderer for the remote video stream.
    let videoStreamRenderer = new VideoStreamRenderer(remoteVideoStream);
    let view;
    const renderVideo = async () => {
        try {
            // Create a renderer view for the remote video stream.
            view = await videoStreamRenderer.createView();
            // Attach the renderer view to the UI.
            remoteVideoContainer.hidden = false;
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

// Start your local video stream.
// This will send your local video stream to remote participants so they can view it.
startVideoButton.onclick = async () => {
    try {
        const localVideoStream = await createLocalVideoStream();
        await call.startVideo(localVideoStream);
    } catch (error) {
        console.error(error);
    }
}

// Stop your local video stream.
// This will stop your local video stream from being sent to remote participants.
stopVideoButton.onclick = async () => {
    try {
        await call.stopVideo(localVideoStream);
    } catch (error) {
        console.error(error);
    }
}

// Create a local video stream for your camera device
createLocalVideoStream = async () => {
    const camera = (await deviceManager.getCameras())[0];
    if (camera) {
        return new LocalVideoStream(camera);
    } else {
        console.error(`No camera device found on the system`);
    }
}

// Display your local video stream preview in your UI
displayLocalVideoStream = async () => {
    try {
        localVideoStreamRenderer = new VideoStreamRenderer(localVideoStream);
        const view = await localVideoStreamRenderer.createView();
        localVideoContainer.hidden = false;
        localVideoContainer.appendChild(view.target);
    } catch (error) {
        console.error(error);
    } 
}

// Remove your local video stream preview from your UI
removeLocalVideoStream = async() => {
    try {
        localVideoStreamRenderer.dispose();
        localVideoContainer.hidden = true;
    } catch (error) {
        console.error(error);
    } 
}
```

上の JavaScript の例 (`client.js`) により生成されたバンドルを利用できる HTML のサンプル コード。
```html
<!-- index.html -->
<!DOCTYPE html>
<html>
    <head>
        <title>Azure Communication Services - Calling Web SDK</title>
    </head>
    <body>
        <h4>Azure Communication Services - Calling Web SDK</h4>
        <input id="user-access-token"
            type="text"
            placeholder="User access token"
            style="margin-bottom:1em; width: 500px;"/>
        <button id="initialize-call-agent" type="button">Initialize Call Agent</button>
        <br>
        <br>
        <input id="callee-acs-user-id"
            type="text"
            placeholder="Enter callee's ACS user identity in format: '8:acs:resourceId_userId'"
            style="margin-bottom:1em; width: 500px; display: block;"/>
        <button id="start-call-button" type="button" disabled="true">Start Call</button>
        <button id="accept-call-button" type="button" disabled="true">Accept Call</button>
        <button id="start-video-button" type="button" disabled="true">Start Video</button>
        <button id="stop-video-button" type="button" disabled="true">Stop Video</button>
        <br>
        <br>
        <div id="connectedLabel" style="color: #13bb13;" hidden>Call is connected!</div>
        <br>
        <div id="remoteVideoContainer" style="width: 40%;" hidden>Remote participants' video streams:</div>
        <br>
        <div id="localVideoContainer" style="width: 30%;" hidden>Local video stream:</div>
        <!-- points to the bundle generated from client.js -->
        <script src="./bundle.js"></script>
    </body>
</html>
```
通話を開始したり、通話に参加したり、通話に出たりした後は、callAgent の "callsUpdated" イベントでも、新しい Call オブジェクトに関する通知を受信し、そのサブスクライブを開始できます。
```js
callAgent.on('callsUpdated', e => {
    // New Call object is added to callAgent.calls collection
    e.added.forEach(call => {
        call.remoteParticipants.forEach(remoteParticipant => {
            subscribeToRemoteParticipant(remoteParticipant);
        })
        call.on('remoteParticipantsUpdated', e => {
            e.added.forEach(remoteParticipant => {
                subscribeToRemoteParticipant(remoteParticipant)
            });
            e.removed.forEach(remoteParticipant => {
                console.log('Remote participant removed from the call.');
            })
        });
    })
})
```

## <a name="initialize-a-callclient-instance-create-a-callagent-instance-and-access-devicemanager"></a>CallClient インスタンスの初期化、CallAgent インスタンスの作成、deviceManager へのアクセスを行う

新しい `CallClient` インスタンスを作成します。 Logger インスタンスなどのカスタム オプションを使用して構成できます。

`CallClient` インスタンスを用意したら、`CallClient` インスタンスで `createCallAgent` メソッドを呼び出すことで、`CallAgent` インスタンスを作成できます。 このメソッドでは、非同期的に `CallAgent` インスタンス オブジェクトが返されます。

`createCallAgent` メソッドでは、`CommunicationTokenCredential` が引数として使用されます。 これは、[ユーザー アクセス トークン](../../../access-tokens.md)を受け取ります。

`CallClient` インスタンスで `getDeviceManager` メソッドを使用して、`deviceManager` にアクセスできます。

```js
const { CallClient } = require('@azure/communication-calling');
const { AzureCommunicationTokenCredential} = require('@azure/communication-common');
const { AzureLogger, setLogLevel } = require("@azure/logger");
// Set the logger's log level
setLogLevel('verbose');
// Redirect log output to wherever desired. To console, file, buffer, REST API, etc...
AzureLogger.log = (...args) => {
    console.log(...args); // Redirect log output to console
};
const userToken = '<USER_TOKEN>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationTokenCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'optional ACS user name'});
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-a-call"></a>通話を行う

通話を作成して開始するには、`callAgent` で API のいずれかを使用し、Communication Services ID SDK を使用して作成したユーザーを指定する必要があります。

通話の作成と開始は同期的に行われます。 `call` インスタンスを使用すると、通話イベントをサブスクライブできます。

### <a name="place-a-1n-call-to-a-user-or-pstn"></a>ユーザーまたは PSTN と 1:n の通話を行う

別の Communication Services ユーザーと通話を行うには、`callAgent` で `startCall` メソッドを呼び出し、[Communication Services 管理ライブラリで作成した](../../../access-tokens.md)、受信者の `CommunicationUserIdentifier` を渡します。

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
const pstnCalee = { phoneNumber: '<ACS_USER_ID>' }
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

### <a name="place-a-11-call-with-video-camera"></a>ビデオ カメラを使用して 1:1 の通話を行う

> [!IMPORTANT]
> 現在、発信ローカル動画ストリームは 1 つしか使用できません。

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


### <a name="join-a-group-call"></a>グループ通話に参加する

> [!NOTE]
> `groupId` パラメーターはシステム メタデータと見なされ、システムの実行に必要な操作のために Microsoft によって使用される場合があります。 `groupId` 値には個人データを含めないでください。 Microsoft ではこのパラメーターを個人データとして扱わず、そのコンテンツが Microsoft の従業員に表示されたり、長期間保管されたりすることがあります。
>
> `groupId` パラメーターのデータは GUID 形式でなければなりません。 ご利用のシステム内で個人データと見なされない、ランダムに生成された GUID を使用することをお勧めします。
>

新しいグループ通話を始めるか、進行中のグループ通話に参加するには、`join` メソッドを使用して、`groupId` プロパティを含むオブジェクトを渡します。 `groupId` 値には GUID を指定する必要があります。

```js

const context = { groupId: '<GUID>'}
const call = callAgent.join(context);

```

### <a name="join-a-teams-meeting"></a>Teams 会議に参加する
> [!NOTE]
> この API は開発者向けにプレビューとして提供されており、寄せられたフィードバックにもとづいて変更される場合があります。 この API は運用環境で使用しないでください。 この API を使用するには、ACS Calling Web SDK の "ベータ" リリースを使用してください

Teams 会議に参加するには、`join` メソッドを使用し、会議リンクまたは会議の座標を渡します。

会議リンクを使用して参加する:

```js
const locator = { meetingLink: '<MEETING_LINK>'}
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
callAgentInstance.on('incomingCall', incomingCallHander);
```

`incomingCall` イベントには、受け入れまたは拒否できる `incomingCall` インスタンスが含まれています。

ビデオをオンにして通話を開始したり、通話に参加したり、通話に出たりした際に、指定されたビデオ カメラ デバイスが別のプロセスで使用されていたり、システムで無効になっていたりした場合には、ビデオがオフの状態で通話が開始され、通話診断の cameraStartFailed が True になります。

この通話診断の処理方法については、「通話の診断」セクションを参照してください。

## <a name="manage-calls"></a>通話を管理する

通話中、通話のプロパティにアクセスしたり、ビデオとオーディオの設定を管理したりできます。

### <a name="check-call-properties"></a>通話のプロパティを確認する

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




### <a name="mute-and-unmute"></a>ミュートとミュート解除

ローカル エンドポイントをミュートまたはミュート解除するには、非同期 API の `mute` と `unmute` を使用します。

```js

//mute local device
await call.mute();

//unmute local device
await call.unmute();

```

### <a name="start-and-stop-sending-local-video"></a>ローカル動画の送信を開始および停止する

動画を開始するには、`deviceManager` オブジェクトで `getCameras` メソッドを使用して、カメラを列挙する必要があります。 次に、目的のカメラを使用して `LocalVideoStream` の新しいインスタンスを作成し、`LocalVideoStream` オブジェクトを `startVideo` メソッドに渡します。

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
localVideoStream インスタンスを渡して通話中にビデオの使用を開始できる API は 4 種類あります。具体的には、callAgent.startCall()、callAgent.join()、call.accept()、call.startVideo() の 4 つです。 call.stopVideo() API には、この 4 つの API で渡したものと同じ localVideoStream インスタンスを渡す必要があります。

`localVideoStream` インスタンスで `switchSource` を呼び出すことにより、動画の送信中に別のカメラ デバイスに切り替えることができます。

```js
const cameras = await callClient.getDeviceManager().getCameras();
const camera = cameras[1];
localVideoStream.switchSource(camera);
```

指定されたビデオ デバイスが別のプロセスで使用されているか、システムで無効になっている場合には、次のようになります。
- 通話中にビデオをオフにし、call.startVideo() API を使ってビデオを開始した場合には、この API により SourceUnavailableError がスローされ、通話診断の cameraStartFiled が True になります。
- localVideoStream.switchSource() API を呼び出すと、通話診断の cameraStartFailed が True になります。
通話診断の処理方法については、「通話の診断」セクションを参照してください。

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

### <a name="videostreamrenderer-methods-and-properties"></a>VideoStreamRenderer のメソッドとプロパティ
リモート動画ストリームをレンダリングするためにアプリケーション UI に付加できる `VideoStreamRendererView` インスタンスを作成し、非同期 `createView()` メソッドを使用します。これにより、ストリームをレンダリングする準備ができたときに解決され、DOM ツリー内のどこにでも追加できる `video` 要素を表す `target` プロパティを含むオブジェクトが返されます

  ```js
  await videoStreamRenderer.createView()
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
- スピーカーの列挙および選択は、Android の Chrome、iOS の Safari、macOS の Safari ではサポートされていません。

## <a name="call-feature-extensions"></a>通話の機能拡張

### <a name="record-calls"></a>通話を記録する
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

### <a name="transfer-calls"></a>通話を転送する
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

### <a name="dominant-speakers-of-a-call"></a>通話の主要話者
> [!NOTE]
> この API は開発者向けにプレビューとして提供されており、寄せられたフィードバックにもとづいて変更される場合があります。 この API は運用環境で使用しないでください。 この API を使用するには、ACS Calling Web SDK の "ベータ" リリースを使用してください

通話の主要話者は、コアとなる `Call` API の拡張機能の 1 つで、通話のアクティブな話者の一覧を取得できるというものです。 

この一覧は、通話で最後にアクティブになっていた話者を先頭としたランキング形式になっています。

通話の主要な話者を取得するには、まず通話の主要話者機能の API オブジェクトを取得する必要があります。

```js
const callDominantSpeakersApi = call.api(Features.CallDominantSpeakers);
```

そのうえで `dominantSpeakers` を呼び出すと、主要話者の一覧を取得できます。 これには `DominantSpeakersInfo` の一種が含まれており、そのメンバーは次のとおりです。

- `speakersList` には、通話の主要話者がランキング形式で並んだ一覧が格納されています。 これらは、参加者 ID によって表されます。
- `timestamp` は、通話の主要話者の最後の更新時点です。

```js
let dominantSpeakers: DominantSpeakersInfo = callDominantSpeakersApi.dominantSpeakers;
```
このほか、`dominantSpeakersChanged` イベントをサブスクライブして、主要話者の一覧に変更が発生したことを把握することもできます

```js
const dominantSpeakersChangedHandler = () => {
    // Get the most up to date list of dominant speakers
    let dominantSpeakers = callDominantSpeakersApi.dominantSpeakers;
};
callDominantSpeakersApi.api(Features.CallDominantSpeakers).on('dominantSpeakersChanged', dominantSpeakersChangedHandler);
``` 
#### <a name="handle-the-dominant-speakers-video-streams"></a>主要話者のビデオ ストリームを処理する

アプリケーションで `DominantSpeakers` 機能を使うと、主要話者のビデオ ストリームのいくつかをレンダリングしつつ、主要話者の一覧が更新された時点で随時 UI を更新することができます。 これを実現するためのコード例は次のとおりです。

```js
// RemoteParticipant obj representation of the dominant speaker
let dominantRemoteParticipant: RemoteParticipant;
// It is recommended to use a map to keep track of a stream's associated renderer
let streamRenderersMap: new Map<RemoteVideoStream, VideoStreamRenderer>();

function getRemoteParticipantForDominantSpeaker(dominantSpeakerIdentifier) {
    let dominantRemoteParticipant: RemoteParticipant;
    switch(dominantSpeakerIdentifier.kind) {
        case 'communicationUser': {
            dominantRemoteParticipant = currentCall.remoteParticipants.find(rm => {
                return (rm.identifier as CommunicationUserIdentifier).communicationUserId === dominantSpeakerIdentifier.communicationUserId
            });
            break;
        }
        case 'microsoftTeamsUser': {
            dominantRemoteParticipant = currentCall.remoteParticipants.find(rm => {
                return (rm.identifier as MicrosoftTeamsUserIdentifier).microsoftTeamsUserId === dominantSpeakerIdentifier.microsoftTeamsUserId
            });
            break;
        }
        case 'unknown': {
            dominantRemoteParticipant = currentCall.remoteParticipants.find(rm => {
                return (rm.identifier as UnknownIdentifier).id === dominantSpeakerIdentifier.id
            });
            break;
        }
    }
    return dominantRemoteParticipant;
}
// Handler function for when the dominant speaker changes
const dominantSpeakersChangedHandler = async () => {
    // Get the new dominant speaker's identifier
    const newDominantSpeakerIdentifier = currentCall.api(Features.DominantSpeakers).dominantSpeakers.speakersList[0];

     if (newDominantSpeakerIdentifier) {
        // Get the remote participant object that matches newDominantSpeakerIdentifier
        const newDominantRemoteParticipant = getRemoteParticipantForDominantSpeaker(newDominantSpeakerIdentifier);

        // Create the new dominant speaker's stream renderers
        const streamViews = [];
        for (const stream of newDominantRemoteParticipant.videoStreams) {
            if (stream.isAvailable && !streamRenderersMap.get(stream)) {
                const renderer = new VideoStreamRenderer(stream);
                streamRenderersMap.set(stream, renderer);
                const view = await videoStreamRenderer.createView();
                streamViews.push(view);
            }
        }

        // Remove the old dominant speaker's video streams by disposing of their associated renderers
        if (dominantRemoteParticipant) {
            for (const stream of dominantRemoteParticipant.videoStreams) {
                const renderer = streamRenderersMap.get(stream);
                if (renderer) {
                    streamRenderersMap.delete(stream);
                    renderer.dispose();
                }
            }
        }

        // Set the new dominant remote participant obj
        dominantRemoteParticipant = newDominantRemoteParticipant

        // Render the new dominant remote participant's streams
        for (const view of streamViewsToRender) {
            htmlElement.appendChild(view.target);
        }
     }
};

// When call is disconnected, set the dominant speaker to undefined
currentCall.on('stateChanged', () => {
    if (currentCall === 'Disconnected') {
        dominantRemoteParticipant = undefined;
    }
});

const dominantSpeakerIdentifier = currentCall.api(Features.DominantSpeakers).dominantSpeakers.speakersList[0];
dominantRemoteParticipant = getRemoteParticipantForDominantSpeaker(dominantSpeakerIdentifier);
currentCall.api(Features.DominantSpeakers).on('dominantSpeakersChanged', dominantSpeakersChangedHandler);

subscribeToRemoteVideoStream = async (stream: RemoteVideoStream, participant: RemoteParticipant) {
    let renderer: VideoStreamRenderer;

    const displayVideo = async () => {
        renderer = new VideoStreamRenderer(stream);
        streamRenderersMap.set(stream, renderer);
        const view = await renderer.createView();
        htmlElement.appendChild(view.target);
    }

    stream.on('isAvailableChanged', async () => {
        if (dominantRemoteParticipant !== participant) {
            return;
        }

        renderer = streamRenderersMap.get(stream);
        if (stream.isAvailable && !renderer) {
            await displayVideo();
        } else {
            streamRenderersMap.delete(stream);
            renderer.dispose();
        }
    });

    if (dominantRemoteParticipant !== participant) {
        return;
    }

    renderer = streamRenderersMap.get(stream);
    if (stream.isAvailable && !renderer) {
        await displayVideo();
    }
}
```
### <a name="call-diagnostics"></a>通話の診断
通話診断は、コアとなる `Call` API の拡張機能の 1 つで、現在の通話を診断できるというものです。

```js
const callDiagnostics = call.api(Features.Diagnostics);
```

ユーザー向けの診断として利用可能なものは次のとおりです。

| Type    | 名前                           | 説明                                                     | 設定可能な値                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | ユース ケース                                                                       |
| ------- | ------------------------------ | --------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------- |
| ネットワーク | noNetwork                      | 利用できるネットワークがありません。                                  | - 利用できるネットワークがないことを理由に通話に失敗した場合には、`True` に設定されます。 <br/> - ICE 候補が存在する場合には、`False` に設定されます。                                                                                                                                                                                                                                                                                                                                                      | デバイスがネットワークに接続されていない。                                           |
| ネットワーク | networkRelaysNotReachable      | ネットワークに問題があります。                                        | - ACS リレーに到達できなくなるような制約がネットワークに存在する場合には、`True` に設定されます。 <br/> - 新しい通話を発信すると、`False` に設定されます。                                                                                                                                                                                                                                                                                                                                                    | 通話中に Wi-Fi 信号のオンとオフが切り替わったとき。                             |  |
| ネットワーク | networkReconnect               | 接続が失われ、ネットワークに再度接続しています。 | - メディア転送接続が失われると、`Poor` に設定されます                                                                                                                                 <br/> - ネットワークへの接続が切れると、`Bad` に設定されます <br/> - 新しいセッションが接続済みになると、`Good` に設定されます。                                                                                                                                                                                           | 帯域幅が狭い、インターネット接続がない                                                      |
| ネットワーク | networkReceiveQuality          | 受信ストリームの品質に関するインジケーターです。                 | - ストリームの受信に深刻な問題が発生している場合には、`Bad` に設定されます。 品質                                                                                                                           <br/> - ストリームの受信に中等度の問題が発生している場合には、`Poor` に設定されます。 品質                                                                                                                           <br/> - ストリームの受信に問題がない場合には、`Good` に設定されます。 | 低帯域幅                                                                   |
| メディア   | noSpeakerDevicesEnumerated     | ユーザーのシステムにオーディオ出力デバイス (スピーカー) がありません。 | - システムにスピーカー デバイスがなく、スピーカー選択がサポートされている場合には、`True` に設定されます。 <br/> - システムにスピーカー デバイスが少なくとも 1 つあり、スピーカー選択がサポートされている場合には、`False` に設定されます。                                                                                                                                                                                                                                                                                | スピーカーがすべて取り外されている                                                      |
| メディア   | speakingWhileMicrophoneIsMuted | 話をしているものの、ミュート中です。                                   | - ローカルのマイクがミュートになっている間にローカル ユーザーが話をしていると、`True` に設定されます。 <br/> - ローカル ユーザーが話をやめるか、マイクのミュートを解除すると `False` に設定されます。 <br/> * 注: オーディオ レベルのサンプルを WebRTC の統計情報から取得している関係上、Safari ではまだこれがサポートされていません。                                                                                                                                                                                                     | 通話中にマイクをミュートした状態で話をする。                          |
| メディア   | noMicrophoneDevicesEnumerated  | ユーザーのシステムにオーディオ キャプチャ デバイス (マイク) がありません      | - システムにマイク デバイスがない場合には、`True` に設定されます。 <br/> - システムにマイク デバイスが 1 つ以上ある場合には、`False` に設定されます。                                                                                                                                                                                                                                                                                                                                                 | 通話中にマイクがすべて取り外される。                                  |
| メディア   | cameraFreeze                   | カメラのフレーム生成が 5 秒以上停止しています。          | - ローカル ビデオ ストリームが停止していると、`True` に設定されます。 これは、自分のビデオがリモートの相手側では停止して見えているか、リモート参加者がビデオを画面にレンダリングできていないことを意味します。 <br/> - 停止状態が解消し、相手側でビデオが通常どおり表示されるようになると、`False` に設定されます。                                                                                                                                                                                         | 通話中にカメラが失われたか、ネットワークの状態が悪いためにカメラが停止した。 |
| メディア   | cameraStartFailed              | カメラの障害全般です。                                         | - カメラ デバイスがシステムで無効になっている可能性があるか、別のプロセスで使用されていることが原因でローカル ビデオの送信開始に失敗した場合には、`True` に設定されます。 <br/> - 選択したカメラ デバイスがローカル ビデオの送信を正常に 再開した場合には、 に設定されます。                                                                                                                                                                                                                                           | カメラの障害                                                                 |
| メディア   | cameraStartTimedOut            | カメラの状態が悪い場合に共通するシナリオです。                   | - カメラ デバイスがビデオ ストリームの送信を開始する際にタイムアウトになった場合には、`True` に設定されます。 <br/> - 選択したカメラ デバイスがローカル ビデオの送信を正常に再開した場合には、`False` に設定されます。                                                                                                                                                                                                                                                                                                                                 | カメラの障害                                                                 |
| メディア   | microphoneNotFunctioning       | マイクが機能していません。                                  | - マイク デバイスがシステムで無効になっている可能性があるか、別のプロセスで使用されていることが原因でローカル オーディオ ストリームの送信開始に失敗した場合には、`True` に設定されます。 この UFD の発生には、約 10 秒かかります。 <br/> - マイクがオーディオ ストリームの送信を正常に再開した場合には、`False` に設定されます。                                                                                                                                                                                    | 利用できるマイクがない、システムでマイクへのアクセスが無効になっている                |
| メディア   | microphoneMuteUnexpectedly     | マイクがミュートになっています                                             | - マイクが予期せずミュート状態になった場合には、`True` に設定されます。 <br/> - マイクがオーディオ ストリームの送信を正常に開始した場合には、`False` に設定されます                                                                                                                                                                                                                                                                                                                                                     | マイクがシステムからミュートに設定されています。                                            |
| メディア   | screenshareRecordingDisabled   | システムの画面共有が、設定によって拒否されました。     | - システム設定 (共有) により画面共有のためのアクセス許可が拒否されている場合には、`True` に設定されます。 <br/> - ストリームの取得に成功した場合には、`False` に設定されます。 <br/> 注: この診断は、macOS の Chrome でのみ機能します。                                                                                                                                                                                                                                                                                               | 設定で画面のレコーディングが無効になっている。                                       |
| メディア   | microphonePermissionDenied     | macOS でデバイスの音量が小さいか、ほぼ無音の状態になっています。 | - システム設定 (オーディオ) によりオーディオへのアクセス許可が拒否されている場合には、`True` に設定されます。 <br/> - ストリームの取得に成功した場合には、`False` に設定されます。 <br/> 注: この診断は、macOS でのみ機能します。                                                                                                                                                                                                                                                                                                                | 設定でマイクへのアクセス許可が無効になっている。                            |
| メディア   | cameraPermissionDenied         | カメラへのアクセス許可が設定で拒否されました。                     | - システム設定 (ビデオ) によりカメラへのアクセス許可が拒否されている場合には、`True` に設定されます。 <br/> - ストリームの取得に成功した場合には、`False` に設定されます。 <br> 注: この診断は、macOS の Chrome でのみ機能します                                                                                                                                                                                                                                                                                                          | 設定でカメラへのアクセス許可が無効になっている。                                |

- 通話の診断に変更が発生したタイミングを監視するには、`diagnosticChanged` イベントをサブスクライブします。
```js
/**
 *  Each diagnostic has the following data:
 * - diagnostic is the type of diagnostic, e.g. NetworkSendQuality, DeviceSpeakWhileMuted, etc...
 * - value is DiagnosticQuality or DiagnosticFlag:
 *     - DiagnosticQuality = enum { Good = 1, Poor = 2, Bad = 3 }.
 *     - DiagnosticFlag = true | false.
 * - valueType = 'DiagnosticQuality' | 'DiagnosticFlag'
 * - mediaType is the media type associated with the event, e.g. Audio, Video, ScreenShare. These are defined in `CallDiagnosticEventMediaType`.
 */
const diagnosticChangedListener = (diagnosticInfo: NetworkDiagnosticChangedEventArgs | MediaDiagnosticChangedEventArgs) => {
    console.log(`Diagnostic changed: ` +
        `Diagnostic: ${diagnosticInfo.diagnostic}` +
        `Value: ${diagnosticInfo.value}` + 
        `Value type: ${diagnosticInfo.valueType}` +
        `Media type: ${diagnosticInfo.mediaType}` +

    if (diagnosticInfo.valueType === 'DiagnosticQuality') {
        if (diagnosticInfo.value === DiagnosticQuality.Bad) {
            console.error(`${diagnosticInfo.diagnostic} is bad quality`);

        } else if (diagnosticInfo.value === DiagnosticQuality.Poor) {
            console.error(`${diagnosticInfo.diagnostic} is poor quality`);
        }

    } else if (diagnosticInfo.valueType === 'DiagnosticFlag') {
        if (diagnosticInfo.value === true) {
            console.error(`${diagnosticInfo.diagnostic}`);
        }
    }
};

callDiagnostics.network.on('diagnosticChanged', diagnosticChangedListener);
callDiagnostics.media.on('diagnosticChanged', diagnosticChangedListener);
```

- 発生した通話診断の最新の値を取得します。 診断が定義されていないときは、一度も発生していないことを意味します。
```js
const latestNetworkDiagnostics = callDiagnostics.network.getLatest();
    
console.log(`noNetwork: ${latestNetworkDiagnostics.noNetwork.value}, ` +
    `value type = ${latestNetworkDiagnostics.noNetwork.valueType}`);
            
console.log(`networkReconnect: ${latestNetworkDiagnostics.networkReconnect.value}, ` +
    `value type = ${latestNetworkDiagnostics.networkReconnect.valueType}`);
            
console.log(`networkReceiveQuality: ${latestNetworkDiagnostics.networkReceiveQuality.value}, ` +
    `value type = ${latestNetworkDiagnostics.networkReceiveQuality.valueType}`);


const latestMediaDiagnostics = callDiagnostics.media.getLatest();
    
console.log(`speakingWhileMicrophoneIsMuted: ${latestMediaDiagnostics.speakingWhileMicrophoneIsMuted.value}, ` +
    `value type = ${latestMediaDiagnostics.speakingWhileMicrophoneIsMuted.valueType}`);
            
console.log(`cameraStartFailed: ${latestMediaDiagnostics.cameraStartFailed.value}, ` +
    `value type = ${latestMediaDiagnostics.cameraStartFailed.valueType}`);
            
console.log(`microphoneNotFunctioning: ${latestMediaDiagnostics.microphoneNotFunctioning.value}, ` +
    `value type = ${latestMediaDiagnostics.microphoneNotFunctioning.valueType}`);
```
## <a name="releasing-resources"></a>リソースを解放する
1. 通話の終了時にリソースを正しく解放する方法は次のとおりです。
    - 通話が終わると、SDK によりシグナリングとメディアのセッションに終了の処理が実行されますが、このとき、その通話の最後の状態を保持した通話インスタンスが残されます。 callEndReason を確認することは依然として可能です。 アプリでこの Call インスタンスに対する参照が保持されなければ、JavaScript のガベージ コレクターによりすべてが消去されます。その結果、メモリ消費という点でアプリが通話前の初期状態に戻ります。
2. リソースの種類のうち、有効期間が長い (有効期間がアプリと同じ) ものと有効期間が短い (通話の間のみ有効) ものはそれぞれ次のとおりです。
    - 次のリソースは、"有効期間が長い" リソースであり、作成したうえで長期間参照しておくことが可能です。いずれも、パフォーマンスに影響が及ぶことのないよう、リソース (メモリ) の消費という点で非常に軽量になっています。
        - CallClient
        - CallAgent
        - DeviceManager
    - 次のリソースは、通話そのものに一定の役割を果たしたり、アプリケーションにイベントを出力したり、ローカルのメディア デバイスとやり取りしたりするもので、"有効期間が短い" リソースであると考えられています。 これらは CPU とメモリの消費量が比較的多いものの、通話が終了すると SDK により状態がすべて消去され、リソースが解放されます。
        - Call - これは、通話の (シグナリングとメディアの両方の) 実際の状態を保持するものです。
        - RemoteParticipants - 通話のリモート参加者を表します。
        - VideoStreamRenderer とその VideoStreamRendererViews - ビデオのレンダリングを処理します。
