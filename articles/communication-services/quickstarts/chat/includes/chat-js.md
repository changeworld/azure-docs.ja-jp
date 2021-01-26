---
title: インクルード ファイル
description: インクルード ファイル
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 9/1/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: d0754ea2d7e8f8f59ec475be8e27fcffd058c11f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376868"
---
## <a name="prerequisites"></a>前提条件
開始する前に、必ず次のことを行ってください。

- アクティブなサブスクリプションがある Azure アカウントを作成します。 詳細については、[アカウントの無料作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)に関するページを参照してください。 
- [Node.js](https://nodejs.org/en/download/) アクティブ LTS およびメンテナンス LTS バージョン (8.11.1 および 10.14.1 を推奨) をインストールします。
- Azure Communication Services リソースを作成します。 詳細については、[Azure Communication リソースの作成](../../create-communication-resource.md)に関するページを参照してください。 このクイックスタート用に、自分のリソースの**エンドポイント**を記録する必要があります。
- [ユーザー アクセス トークン](../../access-tokens.md)。 スコープは必ず "chat" に設定し、トークン文字列と userId 文字列をメモしてください。

## <a name="setting-up"></a>設定

### <a name="create-a-new-web-application"></a>新しい Web アプリケーションを作成する

まず、ターミナルまたはコマンド ウィンドウを開き、自分のアプリ用に新しいディレクトリを作成して、そこに移動します。

```console
mkdir chat-quickstart && cd chat-quickstart
```
   
既定の設定で `npm init -y` を実行して、**package.json** ファイルを作成します。

```console
npm init -y
```

テキスト エディターを使用して、プロジェクトのルート ディレクトリに **start-chat.js** というファイルを作成します。 このクイックスタートのソース コードはすべて、以降のセクションでこのファイルに追加することになります。

### <a name="install-the-packages"></a>パッケージのインストール

`npm install` コマンドを使用して、以下の JavaScript 用の Communication Services クライアント ライブラリをインストールします。

```console
npm install @azure/communication-common --save

npm install @azure/communication-administration --save

npm install @azure/communication-signaling --save

npm install @azure/communication-chat --save

```

`--save` オプションを使用すると、**package.json** ファイル内の依存関係としてライブラリが表示されます。

### <a name="set-up-the-app-framework"></a>アプリのフレームワークを設定する

このクイックスタートでは、webpack を使用してアプリケーション資産をバンドルします。 次のコマンドを実行して、webpack、webpack-cli、および webpack-dev-server npm パッケージをインストールし、**package.json** 内の開発依存関係として表示します。

```console
npm install webpack webpack-cli webpack-dev-server --save-dev
```

自分のプロジェクトのルート ディレクトリに、**index.html** ファイルを作成します。 このファイルをテンプレートとして使い、JavaScript 用 Azure Communication チャット クライアント ライブラリを使用してチャット機能を追加します。

次にコードを示します。

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Chat Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Chat Quickstart</h1>
    <script src="./bundle.js"></script>
  </body>
</html>
```
このクイックスタートのアプリケーション ロジックを格納するために、**client.js** という名前のファイルを自分のプロジェクトのルート ディレクトリに作成します。 

### <a name="create-a-chat-client"></a>チャット クライアントを作成する

Web アプリにチャット クライアントを作成するには、Communications Service エンドポイントと、前提条件の手順で生成されたアクセス トークンを使用します。 ユーザーのアクセス トークンを使用することで、Azure Communication Services に対して直接認証を行うクライアント アプリケーションを作成できます。 自分のサーバーでこれらのトークンを生成した後、それらをクライアント デバイスに渡します。 チャット クライアントにトークンを渡すには、`Common client library` の `AzureCommunicationUserCredential` クラスを使用する必要があります。

自分のプロジェクトのルート ディレクトリに、**client.js** ファイルを作成します。 このファイルで、JavaScript 用 Azure Communication チャット クライアント ライブラリを使用してチャット機能を追加します。

```JavaScript

import { ChatClient } from '@azure/communication-chat';
import { AzureCommunicationUserCredential } from '@azure/communication-common';

// Your unique Azure Communication service endpoint
let endpointUrl = 'https://<RESOURCE_NAME>.communication.azure.com';
let userAccessToken = '<USER_ACCESS_TOKEN>';

let chatClient = new ChatClient(endpointUrl, new AzureCommunicationUserCredential(userAccessToken));
console.log('Azure Communication Chat client created!');
```
**ENDPOINT** は、[Azure Communication リソースの作成](../../create-communication-resource.md)に関するドキュメントに基づいて前に作成したものに置き換えてください。
**USER_ACCESS_TOKEN** は、[ユーザー アクセス トークン](../../access-tokens.md)に関するドキュメントに基づいて発行されたトークンに置き換えてください。
このコードを **client.js** ファイルに追加します


### <a name="run-the-code"></a>コードの実行
アプリをビルドして実行するには、`webpack-dev-server` を使用します。 次のコマンドを実行して、ローカルの Web サーバーにアプリケーション ホストをバンドルします。
```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```
ブラウザーを開き、http://localhost:8080/ に移動します。
ブラウザー内の開発者ツール コンソールには、次のように表示されます。

```console
Azure Communication Chat client created!
```

## <a name="object-model"></a>オブジェクト モデル 
JavaScript 用 Azure Communication Services チャット クライアント ライブラリが備える主な機能のいくつかは、以下のクラスとインターフェイスにより処理されます。

| 名前                                   | 説明                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient | このクラスはチャットの機能に必要です。 サブスクリプション情報を使用してインスタンス化し、それを使用してスレッドを作成、取得、削除します。 |
| ChatThreadClient | このクラスはチャット スレッド機能に必要です。 ChatClient を介してインスタンスを取得し、それを使用して、メッセージの送信、受信、更新、削除、ユーザーの追加、削除、取得、入力通知の送信、開封確認、チャット イベントのサブスクライブを行います。 |


## <a name="start-a-chat-thread"></a>チャット スレッドを開始する

チャット スレッドは、`createThread` メソッドを使用して作成します。

スレッド要求は、`createThreadRequest` を使用して記述します。

- このチャットにトピックを指定するには、`topic` を使用します。チャット スレッドの作成後に、`UpdateThread` 関数を使用してトピックを更新することができます。 
- チャット スレッドに追加するメンバーをリストアップするには、`members` を使用します。

解決すると、`createChatThread` メソッドから `threadId` が返されます。これは、新しく作成されたチャット スレッドに対して操作 (チャット スレッドへのメンバーの追加、メッセージの送信、メッセージの削除など) を実行するために使用されます。

```Javascript
async function createChatThread() {
   let createThreadRequest = {
       topic: 'Preparation for London conference',
       members: [{
                   user: { communicationUserId: '<USER_ID_FOR_JACK>' },
                   displayName: 'Jack'
               }, {
                   user: { communicationUserId: '<USER_ID_FOR_GEETA>' },
                   displayName: 'Geeta'
               }]
   };
   let chatThreadClient= await chatClient.createChatThread(createThreadRequest);
   let threadId = chatThreadClient.threadId;
   return threadId;
}

createChatThread().then(async threadId => {
   console.log(`Thread created:${threadId}`);
   // PLACEHOLDERS
   // <CREATE CHAT THREAD CLIENT>
   // <RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>
   // <SEND MESSAGE TO A CHAT THREAD>
   // <LIST MESSAGES IN A CHAT THREAD>
   // <ADD NEW MEMBER TO THREAD>
   // <LIST MEMBERS IN A THREAD>
   // <REMOVE MEMBER FROM THREAD>
});
```

**USER_ID_FOR_JACK** と **USER_ID_FOR_GEETA** は、前の手順 (ユーザーの作成と[ユーザー アクセス トークン](../../access-tokens.md)の発行) で取得したユーザー ID に置き換えてください

ブラウザー タブを最新の情報に更新すると、コンソールに以下が表示されます
```console
Thread created: <threadId>
```

## <a name="get-a-chat-thread-client"></a>チャット スレッド クライアントを取得する

`getChatThreadClient` メソッドは、既に存在するスレッドの `chatThreadClient` を返します。 作成したスレッドに対し、メンバーの追加、メッセージの送信などの操作を実行するために使用できます。threadId は、既存のチャット スレッドの一意の ID です。

```JavaScript

let chatThreadClient = await chatClient.getChatThreadClient(threadId);
console.log(`Chat Thread client for threadId:${chatThreadClient.threadId}`);

```
**client.js** の `<CREATE CHAT THREAD CLIENT>` コメントをこのコードで置き換え、ブラウザー タブを最新の情報に更新してコンソールを確認すると、以下が表示されます。
```console
Chat Thread client for threadId: <threadId>
```

## <a name="send-a-message-to-a-chat-thread"></a>チャット スレッドにメッセージを送信する

作成したスレッド (threadId で識別されます) にチャット メッセージを送信するには、`sendMessage` メソッドを使用します。

`sendMessageRequest` は、チャット メッセージ要求の必須フィールドを表します。

- チャット メッセージの内容は、`content` を使用して設定します。

`sendMessageOptions` は、チャット メッセージ要求の省略可能なフィールドを表します。

- "Normal (標準)" や "High (高)" など、チャット メッセージの優先度を指定するには `priority` を使用します。このプロパティを使用して、自分のアプリ内の受信ユーザーにメッセージへの注意を促す UI インジケーターを表示したり、カスタム ビジネス ロジックを実行したりすることができます。   
- 送信者の表示名を指定するには、`senderDisplayName` を使用します。

応答である `sendChatMessageResult` には、"id" (そのメッセージの一意の ID) が含まれています。

```JavaScript

let sendMessageRequest =
{
    content: 'Hello Geeta! Can you share the deck for the conference?'
};
let sendMessageOptions =
{
    priority: 'Normal',
    senderDisplayName : 'Jack'
};
let sendChatMessageResult = await chatThreadClient.sendMessage(sendMessageRequest, sendMessageOptions);
let messageId = sendChatMessageResult.id;
console.log(`Message sent!, message id:${messageId}`);

```
**client.js** の `<SEND MESSAGE TO A CHAT THREAD>` コメントをこのコードで置き換え、ブラウザー タブを最新の情報に更新してコンソールを確認します。
```console
Message sent!, message id:<number>
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>チャット スレッドからチャット メッセージを受信する

リアルタイム シグナリングを使用すると、新しい受信メッセージをサブスクライブしてリッスンし、それに応じてメモリ内の現在のメッセージを更新することができます。 Azure Communication Services は、[サブスクライブ可能な一連のイベント](../../../concepts/chat/concepts.md#real-time-signaling)をサポートしています。

```JavaScript
// open notifications channel
await chatClient.startRealtimeNotifications();
// subscribe to new notification
chatClient.on("chatMessageReceived", (e) => {
    console.log("Notification chatMessageReceived!");
    // your code here
});

```
**client.js** の `<RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>` コメントをこのコードで置き換えます。
ブラウザー タブを最新の情報に更新すると、コンソールに "`Notification chatMessageReceived`" というメッセージが表示されます。

チャット メッセージは、特定の間隔で `listMessages` メソッドをポーリングすることによって取得することもできます。 

```JavaScript

let pagedAsyncIterableIterator = await chatThreadClient.listMessages();
let nextMessage = await pagedAsyncIterableIterator.next();
 while (!nextMessage.done) {
     let chatMessage = nextMessage.value;
     console.log(`Message :${chatMessage.content}`);
     // your code here
     nextMessage = await pagedAsyncIterableIterator.next();
 }

```
**client.js** の `<LIST MESSAGES IN A CHAT THREAD>` コメントをこのコードで置き換えます。
タブを最新の情報に更新すると、このチャット スレッドに送信したメッセージがコンソールに一覧表示されます。


`listMessages` は、メッセージに対して `updateMessage` や `deleteMessage` を使用して行われた編集や削除を含む、最新バージョンのメッセージを返します。
削除されたメッセージについては、そのメッセージがいつ削除されたかを示す datetime 値が `chatMessage.deletedOn` から返されます。 編集されたメッセージについては、メッセージがいつ編集されたかを示す datetime が `chatMessage.editedOn` から返されます。 メッセージの最初の作成日時には、`chatMessage.createdOn` を使用してアクセスできます。これをメッセージの並べ替えに使用することができます。

`listMessages` は、`chatMessage.type` で識別できるさまざまな種類のメッセージを返します。 これらの種類があります。

- `Text`:スレッド メンバーによって送信された通常のチャット メッセージ。

- `ThreadActivity/TopicUpdate`:トピックが更新されたことを示すシステム メッセージ。

- `ThreadActivity/AddMember`:1 つまたは複数のメンバーがチャット スレッドに追加されたことを示すシステム メッセージ。

- `ThreadActivity/RemoveMember`:メンバーがチャット スレッドから削除されたことを示すシステム メッセージ。

詳細については、「[メッセージの種類](../../../concepts/chat/concepts.md#message-types)」を参照してください。

## <a name="add-a-user-as-member-to-the-chat-thread"></a>チャット スレッドのメンバーとしてユーザーを追加する

チャット スレッドの作成後、そこにユーザーを追加したり削除したりすることができます。 追加したユーザーには、チャット スレッドにメッセージを送信したり、他のメンバーを追加、削除したりできるアクセス権が与えられます。 `addMembers` メソッドを呼び出す前に必ず、そのユーザーの新しいアクセス トークンと ID を取得しておいてください。 チャット クライアントを初期化するためには、ユーザーにアクセス トークンが必要となります。

`addMembersRequest` は要求オブジェクトを表します。この中では、チャット スレッドに追加するメンバーが `members` を使用してリストアップされます。
- `user` (必須) は、チャット スレッドに追加するコミュニケーション ユーザーです。
- `displayName` (省略可) は、スレッド メンバーの表示名です。
- `shareHistoryTime` (省略可) は、メンバーとの間でチャット履歴が共有される際の起点となる時刻です。 チャット スレッドの始めから履歴を共有する場合は、スレッドの作成日時と同じかそれ以前の任意の日付にこのプロパティを設定してください。 メンバーが追加された時点よりも前の履歴は共有しない場合は、現在の日付に設定します。 履歴を部分的に共有するには、目的の日付に設定します。

```JavaScript

let addMembersRequest =
{
    members: [
        {
            user: { communicationUserId: '<NEW_MEMBER_USER_ID>' },
            displayName: 'Jane'
        }
    ]
};

await chatThreadClient.addMembers(addMembersRequest);

```
**NEW_MEMBER_USER_ID** は、[新しいユーザー ID](../../access-tokens.md) に置き換えます。**client.js** の `<ADD NEW MEMBER TO THREAD>` コメントをこのコードで置き換えてください。

## <a name="list-users-in-a-chat-thread"></a>チャット スレッド内のユーザーをリストアップする
```JavaScript
async function listThreadMembers() {
   let pagedAsyncIterableIterator = await chatThreadClient.listMembers();
   let next = await pagedAsyncIterableIterator.next();
   while (!next.done) {
      let user = next.value;
      console.log(`User :${user.displayName}`);
      next = await pagedAsyncIterableIterator.next();
   }
}
await listThreadMembers();
```
**client.js** の `<LIST MEMBERS IN A THREAD>` コメントをこのコードで置き換え、ブラウザー タブを最新の情報に更新してコンソールを確認すると、スレッド内のユーザーについての情報が表示されます。

## <a name="remove-user-from-a-chat-thread"></a>チャット スレッドからユーザーを削除する

メンバーの追加と同様、チャット スレッドからメンバーを削除することもできます。 削除するには、追加したメンバーの ID を追跡する必要があります。

`removeMember` メソッドを使用します。`member` は、スレッドから削除するコミュニケーション ユーザーです。

```JavaScript

await chatThreadClient.removeMember({ communicationUserId: <MEMBER_ID> });
await listThreadMembers();
```
**MEMBER_ID** は、前の手順で使用したユーザー ID (<NEW_MEMBER_USER_ID>) に置き換えてください。
**client.js** の `<REMOVE MEMBER FROM THREAD>` コメントをこのコードで置き換えます。
