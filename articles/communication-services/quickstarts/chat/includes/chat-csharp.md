---
title: インクルード ファイル
description: インクルード ファイル
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 4c8bd66dde54ff90ea2191fba58f10c87c45cf68
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105958235"
---
## <a name="prerequisites"></a>前提条件
開始する前に、必ず次のことを行ってください。
- アクティブなサブスクリプションがある Azure アカウントを作成します。 詳細については、[アカウントの無料作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)に関するページを参照してください。
- [Visual Studio](https://visualstudio.microsoft.com/downloads/)
- Azure Communication Services リソースを作成します。 詳細については、[Azure Communication リソースの作成](../../create-communication-resource.md)に関するページを参照してください。 このクイックスタート用に、自分のリソースの **エンドポイント** を記録する必要があります。
- [ユーザー アクセス トークン](../../access-tokens.md)。 スコープは必ず "chat" に設定し、トークン文字列と userId 文字列をメモしてください。

## <a name="setting-up"></a>設定

### <a name="create-a-new-c-application"></a>新しい C# アプリケーションを作成する

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、`dotnet new` コマンドを使用し、`ChatQuickstart` という名前で新しいコンソール アプリを作成します。 このコマンドにより、1 つのソース ファイルを使用する単純な "Hello World" C# プロジェクトが作成されます。**Program.cs**。

```console
dotnet new console -o ChatQuickstart
```

新しく作成したアプリ フォルダーにディレクトリを変更し、`dotnet build` コマンドを使用してアプリケーションをコンパイルします。

```console
cd ChatQuickstart
dotnet build
```

### <a name="install-the-package"></a>パッケージをインストールする

.NET 用 Azure Communication Chat SDK をインストールします

```PowerShell
dotnet add package Azure.Communication.Chat --version 1.0.0
```

## <a name="object-model"></a>オブジェクト モデル

C# 用 Azure Communication Services Chat SDK が備える主な機能のいくつかは、以下のクラスにより処理されます。

| 名前                                  | 説明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | このクラスは、チャット機能に必要となります。 サブスクリプション情報を使用してインスタンス化し、それを使用してスレッドを作成、取得、削除します。 |
| ChatThreadClient | このクラスは、チャット スレッド機能に必要となります。 ChatClient を介してインスタンスを取得し、それを使用して、メッセージの送信、受信、更新、削除、参加者の追加、削除、取得、入力通知の送信、開封確認を行います。 |

## <a name="create-a-chat-client"></a>チャット クライアントを作成する

チャット クライアントを作成するには、Communication Services エンドポイントと、前提条件の手順で生成されたアクセス トークンを使用します。 ユーザーを作成し、トークンを発行して自分のチャット クライアントに渡すには、Identity SDK の `CommunicationIdentityClient` クラスを使用する必要があります。

詳細については、[ユーザー アクセス トークン](../../access-tokens.md)に関するページを参照してください。

このクイックスタートでは、チャット アプリケーションのトークンを管理するためのサービス レベルの作成については説明しませんが、サービス レベルの使用をお勧めします。 詳細については、[チャットのアーキテクチャ](../../../concepts/chat/concepts.md)に関するドキュメントを参照してください

次のコード スニペットをコピーして、ソース ファイル **Program.cs** に貼り付けます
```csharp
using Azure;
using Azure.Communication;
using Azure.Communication.Chat;
using System;

namespace ChatQuickstart
{
    class Program
    {
        static async System.Threading.Tasks.Task Main(string[] args)
        {
            // Your unique Azure Communication service endpoint
            Uri endpoint = new Uri("https://<RESOURCE_NAME>.communication.azure.com");

            CommunicationTokenCredential communicationTokenCredential = new CommunicationTokenCredential(<Access_Token>);
            ChatClient chatClient = new ChatClient(endpoint, communicationTokenCredential);
        }
    }
}
```

## <a name="start-a-chat-thread"></a>チャット スレッドを開始する

チャット スレッドは、ChatClient で `createChatThread` メソッドを使用して作成します
- このチャットにトピックを指定するには、`topic` を使用します。チャット スレッドの作成後に、`UpdateTopic` 関数を使用してトピックを更新することができます。
- `participants` プロパティを使用して、チャット スレッドに追加する `ChatParticipant` オブジェクトのリストを渡します。 `ChatParticipant` オブジェクトは、`CommunicationIdentifier` オブジェクトを使用して初期化します。 `CommunicationIdentifier` は、`CommunicationUserIdentifier`、`MicrosoftTeamsUserIdentifier`、または `PhoneNumberIdentifier` タイプである場合もあります。 たとえば、`CommunicationIdentifier` オブジェクトを取得するには、[ユーザーの作成](../../access-tokens.md#create-an-identity)手順に従って作成したアクセス ID を渡す必要があります

`createChatThread` メソッドからの応答オブジェクトには、`chatThread` の詳細が含まれています。 チャット スレッドの操作 (参加者の追加、メッセージの送信、メッセージの削除など) とやり取りするには、`ChatClient` クライアントで `GetChatThreadClient` メソッドを使用して、`chatThreadClient` クライアント インスタンスをインスタンス化する必要があります。

```csharp
var chatParticipant = new ChatParticipant(identifier: new CommunicationUserIdentifier(id: "<Access_ID>"))
{
    DisplayName = "UserDisplayName"
};
CreateChatThreadResult createChatThreadResult = await chatClient.CreateChatThreadAsync(topic: "Hello world!", participants: new[] { chatParticipant });
ChatThreadClient chatThreadClient = chatClient.GetChatThreadClient(threadId: createChatThreadResult.ChatThread.Id);
string threadId = chatThreadClient.Id;
```

## <a name="get-a-chat-thread-client"></a>チャット スレッド クライアントを取得する
`GetChatThreadClient` メソッドは、既に存在するスレッドのスレッド クライアントを返します。 作成したスレッドに対し、メンバーの追加、メッセージの送信などの操作を実行するために使用できます。threadId は、既存のチャット スレッドの一意の ID です。

```csharp
string threadId = "<THREAD_ID>";
ChatThreadClient chatThreadClient = chatClient.GetChatThreadClient(threadId: threadId);
```

## <a name="list-all-chat-threads"></a>すべてのチャット スレッドを一覧表示する
ユーザーが属しているすべてのチャット スレッドを取得するには、`GetChatThreads` を使用します。

```csharp
AsyncPageable<ChatThreadItem> chatThreadItems = chatClient.GetChatThreadsAsync();
await foreach (ChatThreadItem chatThreadItem in chatThreadItems)
{
    Console.WriteLine($"{ chatThreadItem.Id}");
}
```

## <a name="send-a-message-to-a-chat-thread"></a>チャット スレッドにメッセージを送信する

スレッドにメッセージを送信するには、`SendMessage` を使用します。

- メッセージのコンテンツは、`content` を使用して設定します (必須)。
- "Text" や "Html" などのメッセージのコンテンツの種類には `type` を使用します。 指定しない場合は、"Text" が設定されます。
- 送信者の表示名を指定するには、`senderDisplayName` を使用します。 指定しない場合は、空の文字列が設定されます。

```csharp
SendChatMessageResult sendChatMessageResult = await chatThreadClient.SendMessageAsync(content:"hello world", type: ChatMessageType.Text);
string messageId = sendChatMessageResult.Id;
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>チャット スレッドからチャット メッセージを受信する

チャット メッセージは、チャット スレッド クライアントで、特定の間隔で `GetMessages` メソッドをポーリングすることによって取得できます。

```csharp
AsyncPageable<ChatMessage> allMessages = chatThreadClient.GetMessagesAsync();
await foreach (ChatMessage message in allMessages)
{
    Console.WriteLine($"{message.Id}:{message.Content.Message}");
}
```

`GetMessages` には、省略可能な `DateTimeOffset` パラメーターがあります。 このオフセットを指定した場合、その後に受信、更新、または削除されたメッセージを受け取ることになります。 オフセット時刻よりも前に受信したものの、その後、編集または削除されたメッセージも返されることに注意してください。

`GetMessages` は、メッセージに対して `UpdateMessage` や `DeleteMessage` を使用して行われた編集や削除を含む、最新バージョンのメッセージを返します。 削除されたメッセージについては、そのメッセージがいつ削除されたかを示す datetime 値が `chatMessage.DeletedOn` から返されます。 編集されたメッセージについては、メッセージがいつ編集されたかを示す datetime が `chatMessage.EditedOn` から返されます。 メッセージの最初の作成日時には、`chatMessage.CreatedOn` を使用してアクセスできます。これをメッセージの並べ替えに使用することができます。

`GetMessages` は、`chatMessage.Type` で識別できるさまざまな種類のメッセージを返します。 次の種類があります。

- `Text`:スレッド メンバーによって送信された通常のチャット メッセージ。

- `Html`: 書式設定されたテキスト メッセージ。 Communication Services ユーザーは現在、RichText メッセージを送信できないことに注意してください。 このメッセージ型は、Teams の相互運用シナリオで、Teams ユーザーから Communication Services ユーザーに送信されるメッセージでサポートされています。

- `TopicUpdated`:トピックが更新されたことを示すシステム メッセージ。 (読み取り専用)

- `ParticipantAdded`: 1 人以上の参加者がチャット スレッドに追加されたことを示すシステム メッセージ。(読み取り専用)

- `ParticipantRemoved`:参加者がチャット スレッドから削除されたことを示すシステム メッセージ。

詳細については、「[メッセージの種類](../../../concepts/chat/concepts.md#message-types)」を参照してください。

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>チャット スレッドに参加者としてユーザーを追加する

スレッドの作成後、ユーザーを追加したり削除したりすることができます。 追加したユーザーには、スレッドにメッセージを送信したり、他の参加者を追加または削除したりできるアクセス権が与えられます。 `AddParticipants` を呼び出す前に必ず、そのユーザーの新しいアクセス トークンと ID を取得してください。 チャット クライアントを初期化するためには、ユーザーにアクセス トークンが必要となります。

チャット スレッドに 1 人以上の参加者を追加するには、`AddParticipants` を使用します。 各スレッド参加者でサポートされている属性は次のとおりです。
- `communicationUser` (必須) は、スレッド参加者の ID です。
- `displayName` (省略可) は、スレッド参加者の表示名です。
- `shareHistoryTime` (省略可) は、参加者との間でチャット履歴が共有される際の起点となる時刻です。

```csharp
var josh = new CommunicationUserIdentifier(id: "<Access_ID_For_Josh>");
var gloria = new CommunicationUserIdentifier(id: "<Access_ID_For_Gloria>");
var amy = new CommunicationUserIdentifier(id: "<Access_ID_For_Amy>");

var participants = new[]
{
    new ChatParticipant(josh) { DisplayName = "Josh" },
    new ChatParticipant(gloria) { DisplayName = "Gloria" },
    new ChatParticipant(amy) { DisplayName = "Amy" }
};

await chatThreadClient.AddParticipantsAsync(participants: participants);
```

## <a name="get-thread-participants"></a>スレッド参加者を取得する

チャット スレッドの参加者を取得するには、`GetParticipants` を使用します。

```csharp
AsyncPageable<ChatParticipant> allParticipants = chatThreadClient.GetParticipantsAsync();
await foreach (ChatParticipant participant in allParticipants)
{
    Console.WriteLine($"{((CommunicationUserIdentifier)participant.User).Id}:{participant.DisplayName}:{participant.ShareHistoryTime}");
}
```

## <a name="send-read-receipt"></a>開封確認メッセージを送信する

メッセージがユーザーに読まれたことを他の参加者に通知するには、`SendReadReceipt` を使用します。

```csharp
await chatThreadClient.SendReadReceiptAsync(messageId: messageId);
```

## <a name="run-the-code"></a>コードの実行

アプリケーション ディレクトリから `dotnet run` コマンドを使用してアプリケーションを実行します。

```console
dotnet run
```
