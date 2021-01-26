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
ms.openlocfilehash: edf48bc75817b3510264d852eb9cc717ed022f33
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/19/2020
ms.locfileid: "94915453"
---
## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Java Development Kit (JDK)](/java/azure/jdk/?preserve-view=true&view=azure-java-stable) バージョン 8 以降。
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

### <a name="add-the-package-references-for-the-chat-client-library"></a>チャット クライアント ライブラリのパッケージ参照を追加する

POM ファイルで、チャット API を使用して `azure-communication-chat` パッケージを参照します。

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-chat</artifactId>
    <version>1.0.0-beta.3</version> 
</dependency>
```

認証の場合、クライアントは `azure-communication-common` パッケージを参照する必要があります。

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-common</artifactId>
    <version>1.0.0-beta.3</version> 
</dependency>

```

## <a name="object-model"></a>オブジェクト モデル

Java 用 Azure Communication Services チャット クライアント ライブラリが備える主な機能のいくつかは、以下のクラスとインターフェイスにより処理されます。

| 名前                                  | 説明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | このクラスは、チャット機能に必要となります。 サブスクリプション情報を使用してインスタンス化し、それを使用してスレッドを作成、取得、削除します。 |
| ChatAsyncClient | このクラスは、非同期チャット機能に必要となります。 サブスクリプション情報を使用してインスタンス化し、それを使用してスレッドを作成、取得、削除します。 |
| ChatThreadClient | このクラスは、チャット スレッド機能に必要となります。 ChatClient を介してインスタンスを取得し、それを使用して、メッセージの送信/受信/更新/削除、ユーザーの追加/削除/取得、入力通知の送信、開封確認を行います。 |
| ChatThreadAsyncClient | このクラスは、非同期チャット スレッド機能に必要となります。 ChatAsyncClient を介してインスタンスを取得し、それを使用して、メッセージの送信/受信/更新/削除、ユーザーの追加/削除/取得、入力通知の送信、開封確認を行います。 |

## <a name="create-a-chat-client"></a>チャット クライアントを作成する
チャット クライアントを作成するには、Communications Service エンドポイントと、前提条件の手順で生成されたアクセス トークンを使用します。 ユーザーのアクセス トークンを使用することで、Azure Communication Services に対して直接認証を行うクライアント アプリケーションを作成できます。 サーバーでこれらのトークンを生成した後、それらをクライアント デバイスに渡します。 チャット クライアントにトークンを渡すには、Common クライアント ライブラリの CommunicationUserCredential クラスを使用する必要があります。 

import ステートメントを追加する場合は、com.azure.communication.chat.implementation 名前空間ではなく、com.azure.communication.chat および com.azure.communication.chat.models 名前空間からのインポートのみを追加してください。 Maven を使用して生成された App.java ファイルで、次のコードを使用して開始できます。

```Java
import com.azure.communication.chat.*;
import com.azure.communication.chat.models.*;
import com.azure.communication.common.*;
import com.azure.core.http.HttpClient;

import java.io.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Chat Quickstart");
        
        // Your unique Azure Communication service endpoint
        String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

        // Create an HttpClient builder of your choice and customize it
        // Use com.azure.core.http.netty.NettyAsyncHttpClientBuilder if that suits your needs
        NettyAsyncHttpClientBuilder yourHttpClientBuilder = new NettyAsyncHttpClientBuilder();
        HttpClient httpClient = yourHttpClientBuilder.build();

        // User access token fetched from your trusted service
        String userAccessToken = "<USER_ACCESS_TOKEN>";

        // Create a CommunicationUserCredential with the given access token, which is only valid until the token is valid
        CommunicationUserCredential userCredential = new CommunicationUserCredential(userAccessToken);

        // Initialize the chat client
        final ChatClientBuilder builder = new ChatClientBuilder();
        builder.endpoint(endpoint)
            .credential(userCredential)
            .httpClient(httpClient);
        ChatClient chatClient = builder.buildClient();
    }
}
```


## <a name="start-a-chat-thread"></a>チャット スレッドを開始する

チャット スレッドは、`createChatThread` メソッドを使用して作成します。
スレッド要求は、`createChatThreadOptions` を使用して記述します。

- このチャットにトピックを指定するには、`topic` を使用します。チャット スレッドの作成後に、`UpdateThread` 関数を使用してトピックを更新できます。
- チャット スレッドに追加するメンバーをリストするには、`members` を使用します。 `ChatThreadMember` には、[ユーザー アクセス トークン](../../access-tokens.md)に関するクイックスタートで作成したユーザーが渡されます。

作成したチャット スレッドに対して操作 (チャット スレッドへのメンバーの追加、メッセージの送信、メッセージの削除など) を実行するには、応答の `chatThreadClient` を使用します。これには、チャット スレッドの一意の ID である `chatThreadId` プロパティが含まれています。 このプロパティには、パブリック メソッド .getChatThreadId() によってアクセスできます。

```Java
List<ChatThreadMember> members = new ArrayList<ChatThreadMember>();

ChatThreadMember firstThreadMember = new ChatThreadMember()
    .setUser(firstUser)
    .setDisplayName("Member Display Name 1");
    
ChatThreadMember secondThreadMember = new ChatThreadMember()
    .setUser(secondUser)
    .setDisplayName("Member Display Name 2");

members.add(firstThreadMember);
members.add(secondThreadMember);

CreateChatThreadOptions createChatThreadOptions = new CreateChatThreadOptions()
    .setTopic("Topic")
    .setMembers(members);
ChatThreadClient chatThreadClient = chatClient.createChatThread(createChatThreadOptions);
String chatThreadId = chatThreadClient.getChatThreadId();
```

## <a name="send-a-message-to-a-chat-thread"></a>チャット スレッドにメッセージを送信する

作成したスレッド (chatThreadId で識別) にメッセージを送信するには、`sendMessage` メソッドを使用します。
チャット メッセージ要求は、`sendChatMessageOptions` を使用して記述します。

- チャット メッセージの内容は、`content` を使用して設定します。
- "Normal (標準)" や "High (高)" など、チャット メッセージの優先度を指定するには `priority` を使用します。このプロパティを使用して、アプリ内の受信ユーザーにメッセージへの注意を促す UI インジケーターを表示したり、カスタム ビジネス ロジックを実行したりすることができます。
- 送信者の表示名を指定するには、`senderDisplayName` を使用します。

応答である `sendChatMessageResult` には、`id` (そのメッセージの一意の ID) が含まれています。

```Java
SendChatMessageOptions sendChatMessageOptions = new SendChatMessageOptions()
    .setContent("Message content")
    .setPriority(ChatMessagePriority.NORMAL)
    .setSenderDisplayName("Sender Display Name");

SendChatMessageResult sendChatMessageResult = chatThreadClient.sendMessage(sendChatMessageOptions);
String chatMessageId = sendChatMessageResult.getId();
```


## <a name="get-a-chat-thread-client"></a>チャット スレッド クライアントを取得する

`getChatThreadClient` メソッドは、既に存在するスレッドのスレッド クライアントを返します。 作成したスレッドに対し、メンバーの追加、メッセージの送信などの操作を実行するときに使用できます。`chatThreadId` は、既存のチャット スレッドの一意の ID です。

```Java
String chatThreadId = "Id";
ChatThread chatThread = chatClient.getChatThread(chatThreadId);
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>チャット スレッドからチャット メッセージを受信する

チャット メッセージは、チャット スレッド クライアントで、特定の間隔で `listMessages` メソッドをポーリングすることによって取得できます。

```Java
chatThreadClient.listMessages().iterableByPage().forEach(resp -> {
    System.out.printf("Response headers are %s. Url %s  and status code %d %n", resp.getHeaders(),
        resp.getRequest().getUrl(), resp.getStatusCode());
    resp.getItems().forEach(message -> {
        System.out.printf("Message id is %s.", message.getId());
    });
});
```

`listMessages` は、メッセージに対して .editMessage() や .deleteMessage() を使用して行われた編集や削除を含む、最新バージョンのメッセージを返します。 削除されたメッセージについては、それがいつ削除されたかを示す datetime 値が `chatMessage.getDeletedOn()` から返されます。 編集されたメッセージについては、それがいつ編集されたかを示す datetime が `chatMessage.getEditedOn()` から返されます。 メッセージの最初の作成日時には、`chatMessage.getCreatedOn()` を使用してアクセスできます。これをメッセージの並べ替えに使用することができます。

`listMessages` は、`chatMessage.getType()` で識別できるさまざまな種類のメッセージを返します。 次の種類があります。

- `Text`:スレッド メンバーによって送信された通常のチャット メッセージ。

- `ThreadActivity/TopicUpdate`:トピックが更新されたことを示すシステム メッセージ。

- `ThreadActivity/AddMember`:1 人以上のメンバーがチャット スレッドに追加されたことを示すシステム メッセージ。

- `ThreadActivity/DeleteMember`:メンバーがチャット スレッドから削除されたことを示すシステム メッセージ。

詳細については、「[メッセージの種類](../../../concepts/chat/concepts.md#message-types)」を参照してください。

## <a name="add-a-user-as-member-to-the-chat-thread"></a>チャット スレッドにメンバーとしてユーザーを追加する

チャット スレッドの作成後、ユーザーを追加したり削除したりすることができます。 ユーザーを追加すると、それらのユーザーには、チャット スレッドにメッセージを送信したり、他のメンバーを追加、削除したりするためのアクセス権が与えられます。 まず、そのユーザーの新しいアクセス トークンと ID を取得する必要があります。 addMembers メソッドを呼び出す前に必ず、そのユーザーの新しいアクセス トークンと ID を取得してください。 チャット クライアントを初期化するためには、ユーザーにアクセス トークンが必要となります。

threadId で識別されるスレッドにメンバーを追加するには、`addMembers` メソッドを使用します。

- チャット スレッドに追加するメンバーをリストするには、`members` を使用します。
- `user` (必須) は、[ユーザー アクセス トークン](../../access-tokens.md)に関するクイックスタートで CommunicationIdentityClient によって作成された CommunicationUser です。
- `display_name` (省略可) は、スレッド メンバーの表示名です。
- `share_history_time` (省略可) は、メンバーとの間でチャット履歴が共有される際の起点となる時刻です。 チャット スレッドの始めから履歴を共有する場合は、スレッドの作成日時と同じかそれ以前の任意の日付にこのプロパティを設定してください。 メンバーが追加された時点よりも前の履歴は共有しない場合は、現在の日付に設定します。 部分的に履歴を共有するには、必要な日付に設定します。

```Java
List<ChatThreadMember> members = new ArrayList<ChatThreadMember>();

ChatThreadMember firstThreadMember = new ChatThreadMember()
    .setUser(user1)
    .setDisplayName("Display Name 1");

ChatThreadMember secondThreadMember = new ChatThreadMember()
    .setUser(user2)
    .setDisplayName("Display Name 2");

members.add(firstThreadMember);
members.add(secondThreadMember);

AddChatThreadMembersOptions addChatThreadMembersOptions = new AddChatThreadMembersOptions()
    .setMembers(members);
chatThreadClient.addMembers(addChatThreadMembersOptions);
```

## <a name="remove-user-from-a-chat-thread"></a>チャット スレッドからユーザーを削除する

スレッドへのユーザーの追加と同様、チャット スレッドからユーザーを削除することもできます。 そのためには、追加したメンバーのユーザー ID を追跡する必要があります。

`removeMember` を使用します。ここで `user` は作成した CommunicationUser です。

```Java
chatThreadClient.removeMember(user);
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