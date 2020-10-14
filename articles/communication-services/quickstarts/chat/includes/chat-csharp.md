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
ms.openlocfilehash: 994f2829d260223567dcb5aed859812aa1fef3d1
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2020
ms.locfileid: "91829270"
---
## <a name="prerequisites"></a>前提条件
開始する前に、必ず次のことを行ってください。
- アクティブなサブスクリプションがある Azure アカウントを作成します。 詳細については、[アカウントの無料作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)に関するページを参照してください。 
- [Visual Studio](https://visualstudio.microsoft.com/downloads/) 
- Azure Communication Services リソースを作成します。 詳細については、[Azure Communication リソースの作成](../../create-communication-resource.md)に関するページを参照してください。 このクイックスタート用に、自分のリソースの**エンドポイント**を記録する必要があります。
- [ユーザー アクセス トークン](../../access-tokens.md)。 スコープは必ず "chat" に設定し、トークン文字列と userId 文字列をメモしてください。

## <a name="setting-up"></a>設定

### <a name="create-a-new-c-application"></a>新しい C# アプリケーションを作成する

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、`dotnet new` コマンドを使用し、`ChatQuickstart` という名前で新しいコンソール アプリを作成します。 このコマンドにより、1 つのソース ファイルを使用する単純な "Hello World" C# プロジェクトが作成されます。**Program.cs**。

```console
dotnet new console -o ChatQuickstart
```

新しく作成したアプリ フォルダーにディレクトリを変更し、`dotnet build` コマンドを使用して自分のアプリケーションをコンパイルします。

```console
cd ChatQuickstart
dotnet build
```

### <a name="install-the-package"></a>パッケージをインストールする

.NET 用 Azure Communication チャット クライアント ライブラリをインストールする

```PowerShell
dotnet add package Azure.Communication.Chat --version 1.0.0-beta.2
``` 

## <a name="object-model"></a>オブジェクト モデル

C# 用 Azure Communication Services チャット クライアント ライブラリが備える主な機能のいくつかは、以下のクラスにより処理されます。

| 名前                                  | 説明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | このクラスはチャットの機能に必要です。 サブスクリプション情報を使用してインスタンス化し、それを使用してスレッドを作成、取得、削除します。 |
| ChatThreadClient | このクラスはチャット スレッド機能に必要です。 ChatClient を介してインスタンスを取得し、それを使用して、メッセージの送信、受信、更新、削除、ユーザーの追加、削除、取得、入力通知の送信、開封確認を行います。 |

## <a name="create-a-chat-client"></a>チャット クライアントを作成する

チャット クライアントを作成するには、Communication Services エンドポイントと、前提条件の手順で生成されたアクセス トークンを使用します。 ユーザーを作成し、トークンを発行して自分のチャット クライアントに渡すには、`Administration` クライアント ライブラリの `CommunicationIdentityClient` クラスを使用する必要があります。 詳細については、[ユーザー アクセス トークン](../../access-tokens.md)に関するページを参照してください。

```csharp
using Azure.Communication.Identity;
using Azure.Communication.Chat;

// Your unique Azure Communication service endpoint
Uri endpoint = new Uri("https://<RESOURCE_NAME>.communication.azure.com");

CommunicationUserCredential communicationUserCredential = new CommunicationUserCredential(<Access_Token>);
ChatClient chatClient = new ChatClient(endpoint, communicationUserCredential);
```

## <a name="start-a-chat-thread"></a>チャット スレッドを開始する

チャット スレッドは、`createChatThread` メソッドを使用して作成します。
- このチャットにトピックを指定するには、`topic` を使用します。チャット スレッドの作成後に、`UpdateThread` 関数を使用してトピックを更新することができます。
- `members` プロパティを使用して、チャット スレッドに追加する `ChatThreadMember` オブジェクトのリストを渡します。 `ChatThreadMember` オブジェクトは、`CommunicationUser` オブジェクトを使用して初期化します。 `CommunicationUser` オブジェクトを取得するには、[ユーザーの作成](../../access-tokens.md#create-a-user)手順に従って作成したアクセス ID を渡す必要があります

作成されたチャット スレッドに対して操作 (チャット スレッドへのメンバーの追加、メッセージの送信、メッセージの削除など) を実行するには、応答 `chatThreadClient` を使用します。これには、チャット スレッドの一意の ID である `Id` 属性が含まれています。 

```csharp
var chatThreadMember = new ChatThreadMember(new CommunicationUser("<Access_ID>"))
{
    DisplayName = "UserDisplayName"
};
ChatThreadClient chatThreadClient = await chatClient.CreateChatThreadAsync(topic: "Chat Thread topic C# sdk", members: new[] { chatThreadMember });
string threadId = chatThreadClient.Id;
```

## <a name="get-a-chat-thread-client"></a>チャット スレッド クライアントを取得する
`GetChatThreadClient` メソッドは、既に存在するスレッドのスレッド クライアントを返します。 作成したスレッドに対し、メンバーの追加、メッセージの送信などの操作を実行するために使用できます。threadId は、既存のチャット スレッドの一意の ID です。

```csharp
string threadId = "<THREAD_ID>";
ChatThreadClient chatThreadClient = chatClient.GetChatThreadClient(threadId);
```

## <a name="send-a-message-to-a-chat-thread"></a>チャット スレッドにメッセージを送信する

threadId で識別されるスレッドにメッセージを送信するには、`SendMessage` メソッドを使用します。

- チャット メッセージの内容は、`content` を使用して設定します (必須)。
- "Normal (標準)" や "High (高)" など、メッセージの優先度を指定するには `priority` を使用します。指定しなかった場合は、"Normal (標準)" が使用されます。
- 送信者の表示名を指定するには、`senderDisplayName` を使用します。指定しなかった場合は、空の名前が使用されます。

`SendChatMessageResult` は、メッセージの送信から返された応答です。ここには id (メッセージの一意の ID) が含まれています。

```csharp
var content = "hello world";
var priority = ChatMessagePriority.Normal;
var senderDisplayName = "sender name";

SendChatMessageResult sendChatMessageResult = await chatThreadClient.SendMessageAsync(content, priority, senderDisplayName);
string messageId = sendChatMessageResult.Id;
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>チャット スレッドからチャット メッセージを受信する

チャット メッセージは、チャット スレッド クライアントで、特定の間隔で `GetMessages` メソッドをポーリングすることによって取得できます。

```csharp
AsyncPageable<ChatMessage> allMessages = chatThreadClient.GetMessagesAsync();
await foreach (ChatMessage message in allMessages)
{
    Console.WriteLine($"{message.Id}:{message.Sender.Id}:{message.Content}");
}
```

`GetMessages` には、省略可能な `DateTimeOffset` パラメーターがあります。 このオフセットを指定した場合、その後に受信、更新、または削除されたメッセージを受け取ることになります。 オフセット時刻よりも前に受信したものの、その後、編集または削除されたメッセージも返されることに注意してください。

`GetMessages` は、メッセージに対して `UpdateMessage` や `DeleteMessage` を使用して行われた編集や削除を含む、最新バージョンのメッセージを返します。 削除されたメッセージについては、そのメッセージがいつ削除されたかを示す datetime 値が `chatMessage.DeletedOn` から返されます。 編集されたメッセージについては、メッセージがいつ編集されたかを示す datetime が `chatMessage.EditedOn` から返されます。 メッセージの最初の作成日時には、`chatMessage.CreatedOn` を使用してアクセスできます。これをメッセージの並べ替えに使用することができます。

`GetMessages` は、`chatMessage.Type` で識別できるさまざまな種類のメッセージを返します。 これらの種類があります。

- `Text`:スレッド メンバーによって送信された通常のチャット メッセージ。

- `ThreadActivity/TopicUpdate`:トピックが更新されたことを示すシステム メッセージ。

- `ThreadActivity/AddMember`:1 つまたは複数のメンバーがチャット スレッドに追加されたことを示すシステム メッセージ。

- `ThreadActivity/DeleteMember`:メンバーがチャット スレッドから削除されたことを示すシステム メッセージ。

詳細については、「[メッセージの種類](../../../concepts/chat/concepts.md#message-types)」を参照してください。

## <a name="update-a-message"></a>メッセージを更新する

既に送信されたメッセージは、`ChatThreadClient` の `UpdateMessage` を呼び出すことで更新できます。

```csharp
string id = "id-of-message-to-edit";
string content = "updated content";
await chatThreadClient.UpdateMessageAsync(id, content);
```

## <a name="deleting-a-message"></a>メッセージを削除する

メッセージは、`ChatThreadClient` の `DeleteMessage` を呼び出すことで削除できます。

```csharp
string id = "id-of-message-to-delete";
await chatThreadClient.DeleteMessageAsync(id);
```

## <a name="add-a-user-as-member-to-the-chat-thread"></a>チャット スレッドのメンバーとしてユーザーを追加する

スレッドの作成後、ユーザーを追加したり削除したりすることができます。 追加したユーザーには、スレッドにメッセージを送信したり、他のメンバーを追加、削除したりできるアクセス権が与えられます。 `AddMembers` を呼び出す前に必ず、そのユーザーの新しいアクセス トークンと ID を取得してください。 チャット クライアントを初期化するためには、ユーザーにアクセス トークンが必要となります。

threadId で識別されるスレッドにメンバーを追加するには、`AddMembers` メソッドを使用します。

 - チャット スレッドに追加するメンバーをリストアップするには、`members` を使用します。
 - `User` (必須) は、この新しいユーザーに関して得られる ID です。
 - `DisplayName` (省略可) は、スレッド メンバーの表示名です。
 - `ShareHistoryTime` (省略可) は、メンバーとの間でチャット履歴が共有される際の起点となる時刻です。 チャット スレッドの始めから履歴を共有する場合は、DateTime.MinValue に設定します。 メンバーが追加された時点よりも前の履歴を共有しない場合は、現在の時刻に設定します。 履歴を部分的に共有するには、スレッドの作成時刻から現在の時刻までのいずれかの時点に設定します。

```csharp
ChatThreadMember member = new ChatThreadMember(communicationUser);
member.DisplayName = "display name member 1";
member.ShareHistoryTime = DateTime.MinValue; // share all history
await chatThreadClient.AddMembersAsync(members: new[] {member});
```
## <a name="remove-user-from-a-chat-thread"></a>チャット スレッドからユーザーを削除する

スレッドへのユーザーの追加と同様、チャット スレッドからユーザーを削除することもできます。 そのためには、追加したメンバーの ID (CommunicationUser) を追跡する必要があります。

```csharp
await chatThreadClient.RemoveMemberAsync(communicationUser);
```

## <a name="run-the-code"></a>コードの実行

アプリケーション ディレクトリから `dotnet run` コマンドを使用してアプリケーションを実行します。

```console
dotnet run
```
