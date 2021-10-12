---
title: クイックスタート - Teams の会議に参加する
author: askaur
ms.author: askaur
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 616741c97b1e133dd9027b8622c181a9ca622b72
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129378475"
---
このクイックスタートでは、JavaScript 用 Azure Communication Services Chat SDK を使用して Teams 会議でチャットする方法について説明します。

## <a name="sample-code"></a>サンプル コード
このクイックスタートの最終的なコードは [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/join-chat-to-teams-meeting) にあります。

## <a name="prerequisites"></a>前提条件 

*  [Teams のデプロイ](/deployoffice/teams-install)。 
* 実際に動作する[チャット アプリ](../get-started.md)。 

## <a name="joining-the-meeting-chat"></a>会議チャットへの参加 

Communication Services ユーザーは、Calling SDK を使用して、匿名ユーザーとして Teams 会議に参加できます。 会議に参加すると、会議チャットにも参加者として追加されます。会議チャットでは、会議に参加している他のユーザーとメッセージを送受信できます。 ユーザーは、会議に参加する前に送信されたチャット メッセージにアクセスすることはできず、会議の終了後はメッセージを送受信することはできなくなります。 会議に参加してチャットを開始するには、次の手順に従います。

## <a name="create-a-new-nodejs-application"></a>新しい Node.js アプリケーションを作成する

ターミナルまたはコマンド ウィンドウを開き、アプリ用の新しいディレクトリを作成して、そこに移動します。

```console
mkdir chat-interop-quickstart && cd chat-interop-quickstart
```

既定の設定で `npm init -y` を実行して、**package.json** ファイルを作成します。

```console
npm init -y
```

## <a name="install-the-chat-packages"></a>チャット パッケージをインストールする

`npm install` コマンドを使用して、JavaScript 用の必要な Communication Services SDK をインストールします。

```console
npm install @azure/communication-common --save

npm install @azure/communication-identity --save

npm install @azure/communication-signaling --save

npm install @azure/communication-chat --save

npm install @azure/communication-calling --save
```

`--save` オプションを使用すると、**package.json** ファイル内の依存関係としてライブラリが表示されます。

## <a name="set-up-the-app-framework"></a>アプリのフレームワークを設定する

このクイックスタートでは、webpack を使用してアプリケーション資産をバンドルします。 次のコマンドを実行して、webpack、webpack-cli、および webpack-dev-server npm パッケージをインストールし、**package.json** 内の開発依存関係として表示します。

```console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```

自分のプロジェクトのルート ディレクトリに、**index.html** ファイルを作成します。 このファイルを使用して、ユーザーが会議に参加してチャットを開始できるようにする基本的なレイアウトを構成します。

## <a name="add-the-teams-ui-controls"></a>Teams の UI コントロールを追加する

index.html のコードを次のスニペットに置き換えます。
ページの上部にあるテキスト ボックスは、Teams 会議のコンテキストと会議スレッド ID を入力するために使用されます。 [Join Teams Meeting]\(Teams の会議に参加\) ボタンは、指定した会議に参加するために使用します。
ページの下部にチャットのポップアップが表示されます。 これは、会議スレッドでメッセージを送信するために使用でき、Communication Services ユーザーがメンバーである間にそのスレッドで送信されたメッセージがリアルタイムで表示されます。

```html
<!DOCTYPE html>
<html>
   <head>
      <title>Communication Client - Calling and Chat Sample</title>
      <style>
         body {box-sizing: border-box;}
         /* The popup chat - hidden by default */
         .chat-popup {
         display: none;
         position: fixed;
         bottom: 0;
         left: 15px;
         border: 3px solid #f1f1f1;
         z-index: 9;
         }
         .message-box {
         display: none;
         position: fixed;
         bottom: 0;
         left: 15px;
         border: 3px solid #FFFACD;
         z-index: 9;
         }
         .form-container {
         max-width: 300px;
         padding: 10px;
         background-color: white;
         }
         .form-container textarea {
         width: 90%;
         padding: 15px;
         margin: 5px 0 22px 0;
         border: none;
         background: #e1e1e1;
         resize: none;
         min-height: 50px;
         }
         .form-container .btn {
         background-color: #4CAF40;
         color: white;
         padding: 14px 18px;
         margin-bottom:10px;
         opacity: 0.6;
         border: none;
         cursor: pointer;
         width: 100%;
         }
         .container {
         border: 1px solid #dedede;
         background-color: #F1F1F1;
         border-radius: 3px;
         padding: 8px;
         margin: 8px 0;
         }
         .darker {
         border-color: #ccc;
         background-color: #ffdab9;
         margin-left: 25px;
         margin-right: 3px;
         }
         .lighter {
         margin-right: 20px;
         margin-left: 3px;
         }
         .container::after {
         content: "";
         clear: both;
         display: table;
         }
      </style>
   </head>
   <body>
      <h4>Azure Communication Services</h4>
      <h1>Calling and Chat Quickstart</h1>
          <input id="teams-link-input" type="text" placeholder="Teams meeting link"
        style="margin-bottom:1em; width: 300px;" />
          <input id="thread-id-input" type="text" placeholder="Chat thread id"
        style="margin-bottom:1em; width: 300px;" />
        <p>Call state <span style="font-weight: bold" id="call-state">-</span></p>
      <div>
        <button id="join-meeting-button" type="button">
            Join Teams Meeting
        </button>
        <button id="hang-up-button" type="button" disabled="true">
            Hang Up
        </button>
      </div>
      <div class="chat-popup" id="chat-box">
         <div id="messages-container"></div>
         <form class="form-container">
            <textarea placeholder="Type message.." name="msg" id="message-box" required></textarea>
            <button type="button" class="btn" id="send-message">Send</button>
         </form>
      </div>
      <script src="./bundle.js"></script>
   </body>
</html>
```

## <a name="enable-the-teams-ui-controls"></a>Teams の UI コントロールを有効にする

client.js ファイルの内容を次のスニペットに置き換えます。

スニペット内で、以下を置き換えます 
- `SECRET_CONNECTION_STRING` を Communication Services の接続文字列に 
- `ENDPOINT_URL` を Communication Services のエンドポイント URL に

```javascript
import { CallClient, CallAgent } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from "@azure/communication-common";
import { CommunicationIdentityClient } from "@azure/communication-identity";
import { ChatClient } from "@azure/communication-chat";

let call;
let callAgent;
let chatClient;
let chatThreadClient;

const meetingLinkInput = document.getElementById('teams-link-input');
const threadIdInput = document.getElementById('thread-id-input');
const callButton = document.getElementById("join-meeting-button");
const hangUpButton = document.getElementById("hang-up-button");
const callStateElement = document.getElementById('call-state');

const messagesContainer = document.getElementById("messages-container");
const chatBox = document.getElementById("chat-box");
const sendMessageButton = document.getElementById("send-message");
const messagebox = document.getElementById("message-box");

var userId = '';
var lastMessage = '';
var previousMessage = '';

async function init() {
  const connectionString = "<SECRET_CONNECTION_STRING>";
  const endpointUrl = "<ENDPOINT_URL>";

  const identityClient = new CommunicationIdentityClient(connectionString);

  let identityResponse = await identityClient.createUser();
  userId = identityResponse.communicationUserId;
  console.log(`\nCreated an identity with ID: ${identityResponse.communicationUserId}`);

  let tokenResponse = await identityClient.getToken(identityResponse, [
    "voip",
    "chat",
  ]);

  const { token, expiresOn } = tokenResponse;
  console.log(`\nIssued an access token that expires at: ${expiresOn}`);
  console.log(token);

  const callClient = new CallClient();
  const tokenCredential = new AzureCommunicationTokenCredential(token);
  callAgent = await callClient.createCallAgent(tokenCredential);
  callButton.disabled = false;

  chatClient = new ChatClient(
    endpointUrl,
    new AzureCommunicationTokenCredential(token)
  );

  console.log('Azure Communication Chat client created!');
}

init();

callButton.addEventListener("click", async () => {
  // join with meeting link
  call = callAgent.join({meetingLink: meetingLinkInput.value}, {});

  call.on('stateChanged', () => {
      callStateElement.innerText = call.state;
  })
  // toggle button and chat box states
  chatBox.style.display = "block";
  hangUpButton.disabled = false;
  callButton.disabled = true;

  messagesContainer.innerHTML = "";

  console.log(call);

  // open notifications channel
  await chatClient.startRealtimeNotifications();

  // subscribe to new message notifications
  chatClient.on("chatMessageReceived", (e) => {
    console.log("Notification chatMessageReceived!");

      // check whether the notification is intended for the current thread
    if (threadIdInput.value != e.threadId) {
      return;
    }

    if (e.sender.communicationUserId != userId) {
       renderReceivedMessage(e.message);
    }
    else {
       renderSentMessage(e.message);
    }
  });

  chatThreadClient = await chatClient.getChatThreadClient(threadIdInput.value);
});

async function renderReceivedMessage(message) {
  previousMessage = lastMessage;
  lastMessage = '<div class="container lighter">' + message + '</div>';
  messagesContainer.innerHTML = previousMessage + lastMessage;
}

async function renderSentMessage(message) {
  previousMessage = lastMessage;
  lastMessage = '<div class="container darker">' + message + '</div>';
  messagesContainer.innerHTML = previousMessage + lastMessage;
}

hangUpButton.addEventListener("click", async () => 
  {
    // end the current call
    await call.hangUp();

    // toggle button states
    hangUpButton.disabled = true;
    callButton.disabled = false;
    callStateElement.innerText = '-';

    // toggle chat states
    chatBox.style.display = "none";
    messages = "";
  });

sendMessageButton.addEventListener("click", async () =>
  {
    let message = messagebox.value;

    let sendMessageRequest = { content: message };
    let sendMessageOptions = { senderDisplayName : 'Jack' };
    let sendChatMessageResult = await chatThreadClient.sendMessage(sendMessageRequest, sendMessageOptions);
    let messageId = sendChatMessageResult.id;

    messagebox.value = '';
    console.log(`Message sent!, message id:${messageId}`);
  });
```

チャット スレッドの参加者の表示名は、Teams クライアントによって設定されません。 これらの名前は、`participantsAdded` イベントと `participantsRemoved` イベントの、参加者を一覧表示する API では null として返されます。 チャット参加者の表示名は、`call` オブジェクトの `remoteParticipants` フィールドから取得できます。 名簿の変更に関する通知を受信するときに、このコードを使用して、追加または削除されたユーザーの名前を取得できます。

```
var displayName = call.remoteParticipants.find(p => p.identifier.communicationUserId == '<REMOTE_USER_ID>').displayName;
```

## <a name="get-a-teams-meeting-chat-thread-for-a-communication-services-user"></a>Communication Services ユーザーの Teams 会議チャット スレッドを取得する

Teams 会議の詳細情報は Graph API を使用して取得できます。詳細については、[Graph のドキュメント](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true)を参照してください。 Communication Services 通話 SDK は、Teams 会議の完全なリンクまたはミーティング ID を受け入れます。 これらは `onlineMeeting` リソースの一部として返され、[`joinWebUrl` プロパティ](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true)の下でアクセスできます。

[Graph API](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true) を使用して、`threadID` を取得することもできます。 応答には、`threadID` を含む `chatInfo` オブジェクトがあります。 

必要な会議情報とスレッド ID は、Teams 会議の招待状自体に含まれている **[Join Meeting]\(会議に参加\)** URL から取得することもできます。
Teams の会議リンクは、`https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here` のようになっています。 `threadId` は、このリンクの `meeting_chat_thread_id` 部分になります。 使用する前に、`meeting_chat_thread_id` がエスケープされていないことを確認してください。 これは、`19:meeting_ZWRhZDY4ZGUtYmRlNS00OWZaLTlkZTgtZWRiYjIxOWI2NTQ4@thread.v2` という形式になっている必要があります。


## <a name="run-the-code"></a>コードの実行

Webpack ユーザーは、`webpack-dev-server` を使用してアプリをビルドし、実行することができます。 ローカルの Web サーバーにアプリケーション ホストをバンドルするには、次のコマンドを実行します。

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

ブラウザーを開き、http://localhost:8080/ に移動します。 次のように表示されます。

:::image type="content" source="../join-teams-meeting-chat-quickstart.png" alt-text="完成した JavaScript アプリケーションのスクリーンショット。":::

Teams 会議のリンクとスレッド ID を各テキスト ボックスに挿入します。 Teams 会議に参加するには、 *[Join Teams Meeting]\(Teams の会議に参加\)* を押します。 Communication Services ユーザーが会議への参加を許可されたら、Communication Services アプリケーション内からチャットを行うことができます。 チャットを開始するには、ページの下部にあるボックスに移動します。 わかりやすくするために、このアプリケーションには、チャット内の最後の 2 つのメッセージのみが表示されます。

> [!NOTE] 
> 現在、Teams との相互運用性のシナリオでは、メッセージの送受信と編集、および入力通知の送信のみがサポートされています。 開封確認メッセージや、Communication Services ユーザーが Teams 会議に他のユーザーを追加または削除するなどの機能は、まだサポートされていません。
