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
ms.openlocfilehash: e5b5433be4a95a9df9d3b3527473c3004d24acac
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107327280"
---
## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Java Development Kit (JDK)](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install) バージョン 8 以降。
- [Apache Maven](https://maven.apache.org/download.cgi)。
- デプロイされた Communication Services リソースと接続文字列。 [Communication Services リソースを作成します](../../create-communication-resource.md)。
- [ユーザー アクセス トークン](../../access-tokens.md)。 スコープは必ず "chat" に設定し、トークン文字列と userId 文字列をメモしてください。

## <a name="setting-up"></a>設定

### <a name="create-a-new-java-application"></a>新しい Java アプリケーションを作成する

ターミナルまたはコマンド ウィンドウを開き、Java アプリケーションを作成するディレクトリに移動します。 次のコマンドを実行して、maven-archetype-quickstart テンプレートから Java プロジェクトを生成します。

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

"generate" ゴールにより、artifactId と同じ名前のディレクトリが作成されたことがわかります。 このディレクトリの下の `src/main/java directory` にはプロジェクトのソース コードが含まれており、`src/test/java` ディレクトリにはテスト ソースが含まれており、pom.xml ファイルはプロジェクトのプロジェクト オブジェクト モデル ([POM](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) です。

Java 8 以降を使用するように、アプリケーションの POM ファイルを更新します。

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
</properties>
```

### <a name="add-the-package-references-for-the-chat-sdk"></a>Chat SDK のパッケージ参照を追加する

POM ファイルで、チャット API を使用して `azure-communication-chat` パッケージを参照します。

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-chat</artifactId>
    <version>1.0.0</version>
</dependency>
```

認証の場合、クライアントは `azure-communication-common` パッケージを参照する必要があります。

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-common</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="object-model"></a>オブジェクト モデル

Java 用 Azure Communication Services Chat SDK が備える主な機能のいくつかは、以下のクラスとインターフェイスにより処理されます。

| 名前                                  | 説明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | このクラスは、チャット機能に必要となります。 サブスクリプション情報を使用してインスタンス化し、それを使用してスレッドを作成、取得、削除します。 |
| ChatAsyncClient | このクラスは、非同期チャット機能に必要となります。 サブスクリプション情報を使用してインスタンス化し、それを使用してスレッドを作成、取得、削除します。 |
| ChatThreadClient | このクラスは、チャット スレッド機能に必要となります。 ChatClient を介してインスタンスを取得し、それを使用して、メッセージの送信/受信/更新/削除、ユーザーの追加/削除/取得、入力通知の送信、開封確認を行います。 |
| ChatThreadAsyncClient | このクラスは、非同期チャット スレッド機能に必要となります。 ChatAsyncClient を介してインスタンスを取得し、それを使用して、メッセージの送信/受信/更新/削除、ユーザーの追加/削除/取得、入力通知の送信、開封確認を行います。 |

## <a name="create-a-chat-client"></a>チャット クライアントを作成する
チャット クライアントを作成するには、Communications Service エンドポイントと、前提条件の手順で生成されたアクセス トークンを使用します。 ユーザーのアクセス トークンを使用することで、Azure Communication Services に対して直接認証を行うクライアント アプリケーションを作成できます。 サーバーでこれらのトークンを生成した後、それらをクライアント デバイスに渡します。 チャット クライアントにトークンを渡すには、Common SDK の CommunicationTokenCredential クラスを使用する必要があります。

詳細については、[チャットのアーキテクチャ](../../../concepts/chat/concepts.md)に関するドキュメントを参照してください

import ステートメントを追加する場合は、com.azure.communication.chat.implementation 名前空間ではなく、com.azure.communication.chat および com.azure.communication.chat.models 名前空間からのインポートのみを追加してください。 Maven を使用して生成された App.java ファイルで、次のコードを使用して開始できます。

```Java
package com.communication.quickstart;

import com.azure.communication.chat.*;
import com.azure.communication.chat.models.*;
import com.azure.communication.common.*;
import com.azure.core.http.rest.PagedIterable;

import java.io.*;
import java.util.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Chat Quickstart");

        // Your unique Azure Communication service endpoint
        String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

        // User access token fetched from your trusted service
        String userAccessToken = "<USER_ACCESS_TOKEN>";

        // Create a CommunicationTokenCredential with the given access token, which is only valid until the token is valid
        CommunicationTokenCredential userCredential = new CommunicationTokenCredential(userAccessToken);

        // Initialize the chat client
        final ChatClientBuilder builder = new ChatClientBuilder();
        builder.endpoint(endpoint)
            .credential(userCredential);
        ChatClient chatClient = builder.buildClient();
    }
}
```

## <a name="start-a-chat-thread"></a>チャット スレッドを開始する

チャット スレッドは、`createChatThread` メソッドを使用して作成します。
スレッド要求は、`createChatThreadOptions` を使用して記述します。

- このチャットにトピックを指定するには、コンストラクターの `topic` パラメーターを使用します。チャット スレッドの作成後に、`UpdateThread` 関数を使用してトピックを更新できます。
- スレッドに追加するスレッド参加者をリストアップするには、`participants` を使用します。 `ChatParticipant` には、[ユーザー アクセス トークン](../../access-tokens.md)に関するクイックスタートで作成したユーザーが渡されます。

`CreateChatThreadResult` は、チャット スレッドの作成から返された応答です。
これには、スレッド クライアントを取得するために使用できる `ChatThread` オブジェクトを返す `getChatThread()` メソッドが含まれています。そのスレッド クライアントからは、作成されたスレッドに対して参加者の追加、メッセージの送信などを含む操作を実行するための `ChatThreadClient` を取得できます。`ChatThread` オブジェクトには、スレッドの一意の ID を取得する `getId()` メソッドも含まれています。

```Java
CommunicationUserIdentifier identity1 = new CommunicationUserIdentifier("<USER_1_ID>");
CommunicationUserIdentifier identity2 = new CommunicationUserIdentifier("<USER_2_ID>");

ChatParticipant firstThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity1)
    .setDisplayName("Participant Display Name 1");

ChatParticipant secondThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity2)
    .setDisplayName("Participant Display Name 2");

CreateChatThreadOptions createChatThreadOptions = new CreateChatThreadOptions("Topic")
    .addParticipant(firstThreadParticipant)
    .addParticipant(secondThreadParticipant);

CreateChatThreadResult result = chatClient.createChatThread(createChatThreadOptions);
String chatThreadId = result.getChatThread().getId();
```

## <a name="list-chat-threads"></a>チャット スレッドを一覧表示する

`listChatThreads` メソッドを使用して、既存のチャット スレッドの一覧を取得します。

```java
PagedIterable<ChatThreadItem> chatThreads = chatClient.listChatThreads();

chatThreads.forEach(chatThread -> {
    System.out.printf("ChatThread id is %s.\n", chatThread.getId());
});
```

## <a name="get-a-chat-thread-client"></a>チャット スレッド クライアントを取得する

`getChatThreadClient` メソッドは、既に存在するスレッドのスレッド クライアントを返します。 作成したスレッドに対し、参加者の追加、メッセージの送信などの操作を実行するときに使用できます。`chatThreadId` は、既存のチャット スレッドの一意の ID です。

```Java
ChatThreadClient chatThreadClient = chatClient.getChatThreadClient(chatThreadId);
```

## <a name="send-a-message-to-a-chat-thread"></a>チャット スレッドにメッセージを送信する

作成したスレッド (chatThreadId で識別) にメッセージを送信するには、`sendMessage` メソッドを使用します。
チャット メッセージ要求は、`sendChatMessageOptions` を使用して記述します。

- チャット メッセージの内容は、`content` を使用して設定します。
- チャット メッセージのコンテンツの種類、テキスト、または HTML は、`type` を使用して指定します。
- 送信者の表示名を指定するには、`senderDisplayName` を使用します。

応答である `sendChatMessageResult` には、`id` (そのメッセージの一意の ID) が含まれています。

```Java
SendChatMessageOptions sendChatMessageOptions = new SendChatMessageOptions()
    .setContent("Message content")
    .setType(ChatMessageType.TEXT)
    .setSenderDisplayName("Sender Display Name");

SendChatMessageResult sendChatMessageResult = chatThreadClient.sendMessage(sendChatMessageOptions);
String chatMessageId = sendChatMessageResult.getId();
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>チャット スレッドからチャット メッセージを受信する

チャット メッセージは、チャット スレッド クライアントで、特定の間隔で `listMessages` メソッドをポーリングすることによって取得できます。

```Java
chatThreadClient.listMessages().forEach(message -> {
    System.out.printf("Message id is %s.\n", message.getId());
});
```

`listMessages` は、メッセージに対して .editMessage() や .deleteMessage() を使用して行われた編集や削除を含む、最新バージョンのメッセージを返します。 削除されたメッセージについては、それがいつ削除されたかを示す datetime 値が `chatMessage.getDeletedOn()` から返されます。 編集されたメッセージについては、メッセージがいつ編集されたかを示す datetime が `chatMessage.getEditedOn()` から返されます。 メッセージの最初の作成日時には、`chatMessage.getCreatedOn()` を使用してアクセスできます。これをメッセージの並べ替えに使用することができます。

メッセージの種類の詳細については、「[メッセージの種類](../../../concepts/chat/concepts.md#message-types)」をご覧ください。

## <a name="send-read-receipt"></a>開封確認メッセージを送信する

`sendReadReceipt` メソッドを使用すると、ユーザーに代わって開封確認イベントをチャット スレッドに送信できます。
`chatMessageId` は、開封されたチャット メッセージの一意の ID です。

```Java
String chatMessageId = message.getId();
chatThreadClient.sendReadReceipt(chatMessageId);
```

## <a name="list-chat-participants"></a>チャット参加者を一覧表示する

chatThreadId によって識別されるチャット スレッドの参加者を含むページ コレクションを取得するには、`listParticipants` を使用します。

```Java
PagedIterable<ChatParticipant> chatParticipantsResponse = chatThreadClient.listParticipants();
chatParticipantsResponse.forEach(chatParticipant -> {
    System.out.printf("Participant id is %s.\n", ((CommunicationUserIdentifier) chatParticipant.getCommunicationIdentifier()).getId());
});
```

## <a name="add-a-user-as-participant-to-the-chat-thread"></a>チャット スレッドに参加者としてユーザーを追加する

チャット スレッドの作成後、ユーザーを追加したり削除したりすることができます。 追加したユーザーには、チャット スレッドにメッセージを送信したり、他の参加者を追加、削除したりできるアクセス権が与えられます。 まず、そのユーザーの新しいアクセス トークンと ID を取得する必要があります。 addParticipants メソッドを呼び出す前に必ず、そのユーザーの新しいアクセス トークンと ID を取得してください。 チャット クライアントを初期化するためには、ユーザーにアクセス トークンが必要となります。

スレッドに参加者を追加するには、`addParticipants` メソッドを使用します。

- `communicationIdentifier` (必須) は、[ユーザー アクセス トークン](../../access-tokens.md)に関するクイックスタートで CommunicationIdentityClient によって作成された CommunicationIdentifier です。
- `displayName` (省略可) は、スレッド参加者の表示名です。
- `shareHistoryTime` (省略可) は、参加者との間でチャット履歴が共有される際の起点となる時刻です。 チャット スレッドの始めから履歴を共有する場合は、スレッドの作成日時と同じかそれ以前の任意の日付にこのプロパティを設定してください。 参加者が追加された時点よりも前の履歴は共有しない場合は、現在の日付に設定します。 部分的に履歴を共有するには、必要な日付に設定します。

```Java
List<ChatParticipant> participants = new ArrayList<ChatParticipant>();

CommunicationUserIdentifier identity3 = new CommunicationUserIdentifier("<USER_3_ID>");
CommunicationUserIdentifier identity4 = new CommunicationUserIdentifier("<USER_4_ID>");

ChatParticipant thirdThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity3)
    .setDisplayName("Display Name 3");

ChatParticipant fourthThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity4)
    .setDisplayName("Display Name 4");

participants.add(thirdThreadParticipant);
participants.add(fourthThreadParticipant);

chatThreadClient.addParticipants(participants);
```

## <a name="run-the-code"></a>コードの実行

*pom.xml* ファイルが格納されているディレクトリに移動し、次の `mvn` コマンドを使用してプロジェクトをコンパイルします。

```console
mvn compile
```

次に、パッケージをビルドします。

```console
mvn package
```

次の `mvn` コマンドを実行して、アプリを実行します。

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```
