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
ms.openlocfilehash: b2c5237f3f7e949edbfb5486a3a17cc6e0a008a4
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2021
ms.locfileid: "106178583"
---
[!INCLUDE [Public Preview Notice](../../../includes/public-preview-include-chat.md)]

## <a name="prerequisites"></a>前提条件

開始する前に、必ず次のことを行ってください。

- アクティブなサブスクリプションがある Azure アカウントを作成します。 詳細については、[アカウントの無料作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)に関するページを参照してください。
- [Android Studio](https://developer.android.com/studio) をインストールします。Android Studio は、依存関係をインストールするためのクイックスタート用の Android アプリケーションを作成するために使用します。
- Azure Communication Services リソースを作成します。 詳細については、[Azure Communication リソースの作成](../../create-communication-resource.md)に関するページを参照してください。 このクイックスタート用に、自分のリソースの **エンドポイントを記録する** 必要があります。
- **2 人** の Communication Services ユーザーを作成し、これらのユーザーに対して [ユーザー アクセス トークン](../../access-tokens.md)を発行します。 スコープは必ず **chat** に設定し、**トークン文字列と userId 文字列をメモしてください**。 このクイックスタートでは、最初の参加者でスレッドを作成した後、そのスレッドに 2 人目の参加者を追加します。

## <a name="setting-up"></a>設定

### <a name="create-a-new-android-application"></a>新しい Android アプリケーションを作成する

1. Android Studio を開き、`Create a new project` を選択します。 
2. 次のウィンドウで、プロジェクト テンプレートとして `Empty Activity` を選択します。
3. オプションを選択する際に、プロジェクト名として「`ChatQuickstart`」と入力します。
4. [次へ] をクリックし、プロジェクトの作成先ディレクトリを選択します。

### <a name="install-the-libraries"></a>ライブラリをインストールする

Gradle を使用して、必要な Communication Services の依存関係をインストールします。 コマンド ラインから、`ChatQuickstart` プロジェクトのルート ディレクトリ内に移動します。 アプリの build.gradle ファイルを開き、`ChatQuickstart` ターゲットに次の依存関係を追加します。

```
implementation 'com.azure.android:azure-communication-common:1.0.0-beta.8'
implementation 'com.azure.android:azure-communication-chat:1.0.0-beta.8'
```

#### <a name="exclude-meta-files-in-packaging-options-in-root-buildgradle"></a>ルートの build.gradle 内のパッケージ化オプションでメタ ファイルを除外する
```
android {
   ...
    packagingOptions {
        exclude 'META-INF/DEPENDENCIES'
        exclude 'META-INF/LICENSE'
        exclude 'META-INF/license'
        exclude 'META-INF/NOTICE'
        exclude 'META-INF/notice'
        exclude 'META-INF/ASL2.0'
        exclude("META-INF/*.md")
        exclude("META-INF/*.txt")
        exclude("META-INF/*.kotlin_module")
    }
}
```

#### <a name="alternative-to-install-libraries-through-maven"></a>(代替方法) Maven を使用してライブラリをインストールするには
[Maven](https://maven.apache.org/) ビルド システムを使用してプロジェクトにライブラリをインポートするには、それをアプリの `pom.xml` ファイルの `dependencies` セクションに追加し、その成果物 ID と使用するバージョンを指定します。

```xml
<dependency>
  <groupId>com.azure.android</groupId>
  <artifactId>azure-communication-chat</artifactId>
  <version>1.0.0-beta.8</version>
</dependency>
```


### <a name="setup-the-placeholders"></a>プレースホルダーを設定する

ファイル `MainActivity.java` を開いて編集します。 このクイック スタートでは、`MainActivity` にコードを追加し、コンソールにその出力を表示します。 UI の作成については、このクイックスタートでは取り上げません。 ファイルの先頭で、`Communication common` および `Communication chat` ライブラリをインポートします。

```
import com.azure.android.communication.chat.*;
import com.azure.android.communication.common.*;
```

次のコードをファイル `MainActivity` にコピーします。

```java
    private String secondUserId = "<second_user_id>";
    private String threadId = "<thread_id>";
    private String chatMessageId = "<chat_message_id>";
    private final String sdkVersion = "1.0.0-beta.8";
    private static final String APPLICATION_ID = "Chat Quickstart App";
    private static final String SDK_NAME = "azure-communication-com.azure.android.communication.chat";
    private static final String TAG = "--------------Chat Quickstart App-------------";

    private void log(String msg) {
        Log.i(TAG, msg);
        Toast.makeText(this, msg, Toast.LENGTH_LONG).show();
    }
    
   @Override
    protected void onStart() {
        super.onStart();
        try {
            // <CREATE A CHAT CLIENT>

            // <CREATE A CHAT THREAD>

            // <CREATE A CHAT THREAD CLIENT>

            // <SEND A MESSAGE>

            // <ADD A USER>

            // <LIST USERS>

            // <REMOVE A USER>
            
            // <<SEND A TYPING NOTIFICATION>>
            
            // <<SEND A READ RECEIPT>>
               
            // <<LIST READ RECEIPTS>>
        } catch (Exception e){
            System.out.println("Quickstart failed: " + e.getMessage());
        }
    }
```

以降の手順では、Azure Communication Services の Chat ライブラリを使用して、プレースホルダーをサンプル コードに置き換えていきます。


### <a name="create-a-chat-client"></a>チャット クライアントを作成する

コメント `<CREATE A CHAT CLIENT>` を次のコードに置き換えます (import ステートメントはファイルの先頭に 配置します)。

```java
import com.azure.android.communication.chat.ChatAsyncClient;
import com.azure.android.communication.chat.ChatClientBuilder;
import com.azure.android.core.credential.AccessToken;
import com.azure.android.core.http.HttpHeader;
import com.azure.android.core.http.okhttp.OkHttpAsyncClientProvider;
import com.azure.android.core.http.policy.BearerTokenAuthenticationPolicy;
import com.azure.android.core.http.policy.UserAgentPolicy;

final String endpoint = "https://<resource>.communication.azure.com";
final String userAccessToken = "<user_access_token>";

ChatAsyncClient chatAsyncClient = new ChatClientBuilder()
    .endpoint(endpoint)
    .credentialPolicy(new BearerTokenAuthenticationPolicy((request, callback) ->
        callback.onSuccess(new AccessToken(userAccessToken, OffsetDateTime.now().plusDays(1)))))
    .addPolicy(new UserAgentPolicy(APPLICATION_ID, SDK_NAME, sdkVersion))
    .httpClient(new OkHttpAsyncClientProvider().createInstance())
    .buildAsyncClient();

```

1. `ChatClientBuilder` を使用して、`ChatAsyncClient` のインスタンスを構成して作成します。
2. `<resource>` を Communication Services リソースに置き換えます。
3. `<user_access_token>` を 有効な Communication Services アクセス トークンに置き換えます。

## <a name="object-model"></a>オブジェクト モデル
JavaScript 用 Azure Communication Services Chat SDK が備える主な機能のいくつかは、以下のクラスとインターフェイスにより処理されます。

| 名前                                   | 説明                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient/ChatAsyncClient | このクラスは、チャット機能に必要となります。 サブスクリプション情報を使用してインスタンス化し、それを使用してスレッドを作成、取得、削除します。 |
| ChatThreadClient/ChatThreadAsyncClient | このクラスはチャット スレッド機能に必要です。 ChatClient を介してインスタンスを取得し、それを使用して、メッセージの送信、受信、更新、削除、ユーザーの追加、削除、取得、入力通知の送信、開封確認、チャット イベントのサブスクライブを行います。 |

## <a name="start-a-chat-thread"></a>チャット スレッドを開始する

`ChatAsyncClient` を使用して、最初のユーザーで新しいスレッドを作成します。

`<CREATE A CHAT THREAD>` コメントを、次のコードに置き換えます。

```java
// A list of ChatParticipant to start the thread with.
List<ChatParticipant> participants = new ArrayList<>();
// The communication user ID you created before, required.
String id = "<user_id>";
// The display name for the thread participant.
String displayName = "initial participant";
participants.add(new ChatParticipant()
    .setCommunicationIdentifier(new CommunicationUserIdentifier(id))
    .setDisplayName(displayName));

// The topic for the thread.
final String topic = "General";
// Optional, set a repeat request ID.
final String repeatabilityRequestID = "";
// Options to pass to the create method.
CreateChatThreadOptions createChatThreadOptions = new CreateChatThreadOptions()
    .setTopic(topic)
    .setParticipants(participants)
    .setIdempotencyToken(repeatabilityRequestID);

CreateChatThreadResult createChatThreadResult =
    chatAsyncClient.createChatThread(createChatThreadOptions).get();
ChatThreadProperties chatThreadProperties = createChatThreadResult.getChatThreadProperties();
threadId = chatThreadProperties.getId();

```

`<user_id>` は、有効な Communication Services のユーザー ID に置き換えます。 後の手順で完了ハンドラーに返される応答の `threadId` を使用します。そのため、クラス内の `<thread_id>` をこの要求から取得した `threadId` に置き換えて、アプリを再実行します。

## <a name="get-a-chat-thread-client"></a>チャット スレッド クライアントを取得する

チャット スレッドを作成したので、そのスレッド内で操作を実行するために `ChatThreadAsyncClient` を取得します。 `<CREATE A CHAT THREAD CLIENT>` コメントを、次のコードに置き換えます。

```
ChatThreadAsyncClient chatThreadAsyncClient = new ChatThreadClientBuilder()
    .endpoint(endpoint)
    .credentialPolicy(new BearerTokenAuthenticationPolicy((request, callback) ->
        callback.onSuccess(new AccessToken(userAccessToken, OffsetDateTime.now().plusDays(1)))))
    .addPolicy(new UserAgentPolicy(APPLICATION_ID, SDK_NAME, sdkVersion))
    .httpClient(new OkHttpAsyncClientProvider().createInstance())
    .chatThreadId(threadId)
    .buildAsyncClient();

```

## <a name="send-a-message-to-a-chat-thread"></a>チャット スレッドにメッセージを送信する

`<thread_id>` を有効なスレッド ID に置き換えたことを確認します。これから、そのスレッドにメッセージが送信されます。

`<SEND A MESSAGE>` コメントを、次のコードに置き換えます。

```java
// The chat message content, required.
final String content = "Test message 1";
// The display name of the sender, if null (i.e. not specified), an empty name will be set.
final String senderDisplayName = "An important person";
SendChatMessageOptions chatMessageOptions = new SendChatMessageOptions()
    .setType(ChatMessageType.TEXT)
    .setContent(content)
    .setSenderDisplayName(senderDisplayName);

// A string is the response returned from sending a message, it is an id, which is the unique ID of the message.
chatMessageId = chatThreadAsyncClient.sendMessage(chatMessageOptions).get().getId();

```

`chatMessageId` を取得したら、クイックスタートで後ほどメソッドを使用するために `<chat_message_id>` を `chatMessageId` に置き換えて、アプリを再実行できます。

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>チャット スレッドに参加者としてユーザーを追加する

`<ADD A USER>` コメントを、次のコードに置き換えます。

```java
// The display name for the thread participant.
displayName = "a new participant";
ChatParticipant participant = new ChatParticipant()
    .setCommunicationIdentifier(new CommunicationUserIdentifier(secondUserId))
    .setDisplayName(secondUserDisplayName);
        
chatThreadAsyncClient.addParticipant(participant);

```

クラス内の `<second_user_id>` を、追加するユーザーの Communication Services ユーザー ID に置き換えます。 

## <a name="list-users-in-a-thread"></a>スレッド内のユーザーを一覧表示する

コメント `<LIST USERS>` を、次のコードに置き換えます。

```java
// The maximum number of participants to be returned per page, optional.
final int maxPageSize = 10;

// Skips participants up to a specified position in response.
final int skip = 0;

// Options to pass to the list method.
ListParticipantsOptions listParticipantsOptions = new ListParticipantsOptions()
    .setMaxPageSize(maxPageSize)
    .setSkip(skip);

PagedResponse<ChatParticipant> firstPageWithResponse =
    chatThreadAsyncClient.getParticipantsFirstPageWithResponse(listParticipantsOptions, Context.NONE).get();

for (ChatParticipant participant : firstPageWithResponse.getValue()) {
    // You code to handle participant
}

listParticipantsNextPage(firstPageWithResponse.getContinuationToken(), 2);

```

以下のヘルパー メソッドをクラスに配置します。

```java
void listParticipantsNextPage(String continuationToken, int pageNumber) {
if (continuationToken != null) {
    PagedResponse<ChatParticipant> nextPageWithResponse =
        chatThreadAsyncClient.getParticipantsNextPageWithResponse(continuationToken, Context.NONE).get();
        for (ChatParticipant participant : nextPageWithResponse.getValue()) {
            // You code to handle participant
        }
            
        listParticipantsNextPage(nextPageWithResponse.getContinuationToken(), ++pageNumber);
    }
}

```


## <a name="remove-user-from-a-chat-thread"></a>チャット スレッドからユーザーを削除する

必ず `<second_user_id>` を有効なユーザー ID に置き換えてください。これから、2 番目のユーザーがスレッドから削除されます。

コメント `<REMOVE A USER>` を、次のコードに置き換えます。

```java
// Using the unique ID of the participant.
chatThreadAsyncClient.removeParticipant(new CommunicationUserIdentifier(secondUserId)).get();

```

## <a name="send-a-typing-notification"></a>入力通知を送信する

コメント `<SEND A TYPING NOTIFICATION>` を、次のコードに置き換えます。

```java
chatThreadAsyncClient.sendTypingNotification().get();
```

## <a name="send-a-read-receipt"></a>開封確認メッセージを送信する

必ず `<chat_message_id>` を有効なチャット メッセージ ID に置き換えてください。これから、このメッセージの開封確認メッセージが送信されます。

コメント `<SEND A READ RECEIPT>` を、次のコードに置き換えます。

```java
chatThreadAsyncClient.sendReadReceipt(chatMessageId).get();
```

## <a name="list-read-receipts"></a>開封確認メッセージを一覧表示する

コメント `<READ RECEIPTS>` を、次のコードに置き換えます。

```java
// The maximum number of participants to be returned per page, optional.
maxPageSize = 10;
// Skips participants up to a specified position in response.
skip = 0;
// Options to pass to the list method.
ListReadReceiptOptions listReadReceiptOptions = new ListReadReceiptOptions()
    .setMaxPageSize(maxPageSize)
    .setSkip(skip);

PagedResponse<ChatMessageReadReceipt> firstPageWithResponse =
    chatThreadAsyncClient.getReadReceiptsFirstPageWithResponse(listReadReceiptOptions, Context.NONE).get();

for (ChatMessageReadReceipt readReceipt : firstPageWithResponse.getValue()) {
    // You code to handle readReceipt
}

listReadReceiptsNextPage(firstPageWithResponse.getContinuationToken(), 2);

```

以下のヘルパー メソッドをクラスに配置します。
```java
void listReadReceiptsNextPage(String continuationToken, int pageNumber) {
    if (continuationToken != null) {
        PagedResponse<ChatMessageReadReceipt> nextPageWithResponse =
            chatThreadAsyncClient.getReadReceiptsNextPageWithResponse(continuationToken, Context.NONE).get();

        for (ChatMessageReadReceipt readReceipt : nextPageWithResponse.getValue()) {
            // You code to handle readReceipt
        }

        listParticipantsNextPage(nextPageWithResponse.getContinuationToken(), ++pageNumber);
    }
}

```


## <a name="run-the-code"></a>コードの実行

Android Studio で [Run]\(実行\) ボタンを押して、プロジェクトをビルドして実行します。 コンソールには、そのコードからの出力と ChatClient からのロガー出力が表示されます。
