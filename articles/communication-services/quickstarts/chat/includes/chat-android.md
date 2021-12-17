---
title: インクルード ファイル
description: インクルード ファイル
services: azure-communication-services
author: probableprime
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: rifox
ms.openlocfilehash: 9f9c85a7674dfee99a3db41fdcf8b14d1ac8b96b
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2021
ms.locfileid: "122967963"
---
## <a name="sample-code"></a>サンプル コード
このクイックスタートの最終的なコードは [GitHub](https://github.com/Azure-Samples/communication-services-android-quickstarts/tree/main/Add-chat) にあります。

## <a name="prerequisites"></a>前提条件

開始する前に、必ず次のことを行ってください。

- アクティブなサブスクリプションがある Azure アカウントを作成します。 詳細については、[アカウントの無料作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)に関するページを参照してください。
- [Android Studio](https://developer.android.com/studio) をインストールします。Android Studio は、依存関係をインストールするためのクイックスタート用の Android アプリケーションを作成するために使用します。
- Azure Communication Services リソースを作成します。 詳細については、[Azure Communication Services リソースの作成](../../create-communication-resource.md)に関するページを参照してください。 このクイックスタート用に、自分のリソースの **エンドポイントを記録する** 必要があります。
- **2 人** の Communication Services ユーザーを作成し、これらのユーザーに対して [ユーザー アクセス トークン](../../access-tokens.md)を発行します。 スコープは必ず **chat** に設定し、**トークン文字列と userId 文字列をメモしてください**。 このクイックスタートでは、最初の参加者でスレッドを作成した後、そのスレッドに 2 人目の参加者を追加します。

## <a name="setting-up"></a>設定

### <a name="create-a-new-android-application"></a>新しい Android アプリケーションを作成する

1. Android Studio を開き、`Create a new project` を選択します。 
2. 次のウィンドウで、プロジェクト テンプレートとして `Empty Activity` を選択します。
3. オプションを選択する際に、プロジェクト名として「`ChatQuickstart`」と入力します。
4. [次へ] をクリックし、プロジェクトの作成先ディレクトリを選択します。

### <a name="install-the-libraries"></a>ライブラリをインストールする

Gradle を使用して、必要な Communication Services の依存関係をインストールします。 コマンド ラインから、`ChatQuickstart` プロジェクトのルート ディレクトリ内に移動します。 アプリの build.gradle ファイルを開き、`ChatQuickstart` ターゲットに次の依存関係を追加します。

```groovy
implementation 'com.azure.android:azure-communication-common:' + $azureCommunicationCommonVersion
implementation 'com.azure.android:azure-communication-chat:' + $azureCommunicationChatVersion
implementation 'org.slf4j:slf4j-log4j12:1.7.29'
```

最新のバージョン番号について https://search.maven.org/artifact/com.azure.android/azure-communication-common と https://search.maven.org/artifact/com.azure.android/azure-communication-chat を参照してください。

#### <a name="exclude-meta-files-in-packaging-options-in-root-buildgradle"></a>ルートの build.gradle 内のパッケージ化オプションでメタ ファイルを除外する
```groovy
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
  <version><!-- Please refer to https://search.maven.org/artifact/com.azure.android/azure-communication-chat for the latest version --></version>
</dependency>
```


### <a name="setup-the-placeholders"></a>プレースホルダーを設定する

ファイル `MainActivity.java` を開いて編集します。 このクイック スタートでは、`MainActivity` にコードを追加し、コンソールにその出力を表示します。 UI の作成については、このクイックスタートでは取り上げません。 ファイルの先頭で、`Communication common`、`Communication chat`、およびその他のシステム ライブラリをインポートします。

```
import com.azure.android.communication.chat.*;
import com.azure.android.communication.chat.models.*;
import com.azure.android.communication.common.*;

import android.os.Bundle;
import android.util.Log;
import android.widget.Toast;

import com.jakewharton.threetenabp.AndroidThreeTen;

import java.util.ArrayList;
import java.util.List;
```

ファイル `MainActivity.java` のクラス `MainActivity` に次のコードをコピーします。

```java
    private String endpoint = "https://<resource>.communication.azure.com";
    private String firstUserId = "<first_user_id>";
    private String secondUserId = "<second_user_id>";
    private String firstUserAccessToken = "<first_user_access_token>";
    private String threadId = "<thread_id>";
    private String chatMessageId = "<chat_message_id>";
    private final String sdkVersion = "<chat_sdk_version>";
    private static final String APPLICATION_ID = "Chat Quickstart App";
    private static final String SDK_NAME = "azure-communication-com.azure.android.communication.chat";
    private static final String TAG = "Chat Quickstart App";

    private void log(String msg) {
        Log.i(TAG, msg);
        Toast.makeText(this, msg, Toast.LENGTH_LONG).show();
    }
    
   @Override
    protected void onStart() {
        super.onStart();
        try {
            AndroidThreeTen.init(this);

            // <CREATE A CHAT CLIENT>

            // <CREATE A CHAT THREAD>

            // <CREATE A CHAT THREAD CLIENT>

            // <SEND A MESSAGE>
            
            // <RECEIVE CHAT MESSAGES>

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

1. `<resource>` を Communication Services リソースに置き換えます。
2. `<first_user_id>` と `<second_user_id>` を、前提条件の手順の一部として生成された有効な Communication Services ユーザー ID に置き換えます。
3. `<first_user_access_token>` を、前提条件の手順の一部として `<first_user_id>` を対象に生成された Communication Services アクセス トークンに置き換えます。
4. `<chat_sdk_version>` を、Azure Communication Chat SDK のバージョンに置き換えます。

以降の手順では、Azure Communication Services の Chat ライブラリを使用して、プレースホルダーをサンプル コードに置き換えていきます。


### <a name="create-a-chat-client"></a>チャット クライアントを作成する

コメント `<CREATE A CHAT CLIENT>` を次のコードに置き換えます (import ステートメントはファイルの先頭に 配置します)。

```java
import com.azure.android.core.http.policy.UserAgentPolicy;

ChatAsyncClient chatAsyncClient = new ChatClientBuilder()
    .endpoint(endpoint)
    .credential(new CommunicationTokenCredential(firstUserAccessToken))
    .addPolicy(new UserAgentPolicy(APPLICATION_ID, SDK_NAME, sdkVersion))
    .buildAsyncClient();

```

## <a name="object-model"></a>オブジェクト モデル
JavaScript 用 Azure Communication Services Chat SDK が備える主な機能のいくつかは、以下のクラスとインターフェイスにより処理されます。

| 名前                                   | 説明                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient/ChatAsyncClient | このクラスは、チャット機能に必要となります。 自分のサブスクリプション情報を使用してインスタンス化し、それを使用してスレッドを作成、取得、削除し、チャット イベントにサブスクライブします。 |
| ChatThreadClient/ChatThreadAsyncClient | このクラスはチャット スレッド機能に必要です。 ChatClient を介してインスタンスを取得し、それを使用して、メッセージの送信/受信/更新/削除、ユーザーの追加/削除/取得、入力通知の送信、開封確認を行います。 |

## <a name="start-a-chat-thread"></a>チャット スレッドを開始する

`ChatAsyncClient` を使用して、最初のユーザーで新しいスレッドを作成します。

`<CREATE A CHAT THREAD>` コメントを、次のコードに置き換えます。

```java
// A list of ChatParticipant to start the thread with.
List<ChatParticipant> participants = new ArrayList<>();
// The display name for the thread participant.
String displayName = "initial participant";
participants.add(new ChatParticipant()
    .setCommunicationIdentifier(new CommunicationUserIdentifier(firstUserId))
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

## <a name="get-a-chat-thread-client"></a>チャット スレッド クライアントを取得する

チャット スレッドを作成したので、そのスレッド内で操作を実行するために `ChatThreadAsyncClient` を取得します。 `<CREATE A CHAT THREAD CLIENT>` コメントを、次のコードに置き換えます。

```
ChatThreadAsyncClient chatThreadAsyncClient = new ChatThreadClientBuilder()
    .endpoint(endpoint)
    .credential(new CommunicationTokenCredential(firstUserAccessToken))
    .addPolicy(new UserAgentPolicy(APPLICATION_ID, SDK_NAME, sdkVersion))
    .chatThreadId(threadId)
    .buildAsyncClient();

```

## <a name="send-a-message-to-a-chat-thread"></a>チャット スレッドにメッセージを送信する

ここで、メッセージをそのスレッドに送信します。

`<SEND A MESSAGE>` コメントを、次のコードに置き換えます。

```java
// The chat message content, required.
final String content = "Please take a look at the attachment";

// The display name of the sender, if null (i.e. not specified), an empty name will be set.
final String senderDisplayName = "An important person";

// Use metadata optionally to include any additional data you want to send along with the message.
// This field provides a mechanism for developers to extend chat message functionality and add
// custom information for your use case. For example, when sharing a file link in the message, you
// might want to add 'hasAttachment:true' in metadata so that recipient's application can parse
// that and display accordingly.
final Map<String, String> metadata = new HashMap<String, String>();
metadata.put("hasAttachment", "true");
metadata.put("attachmentUrl", "https://contoso.com/files/attachment.docx");

SendChatMessageOptions chatMessageOptions = new SendChatMessageOptions()
    .setType(ChatMessageType.TEXT)
    .setContent(content)
    .setSenderDisplayName(senderDisplayName)
    .setMetadata(metadata);

// A string is the response returned from sending a message, it is an id, which is the unique ID
// of the message.
chatMessageId = chatThreadAsyncClient.sendMessage(chatMessageOptions).get().getId();

```

## <a name="receive-chat-messages-from-a-chat-thread"></a>チャット スレッドからチャット メッセージを受信する
リアルタイム シグナリングを使用すると、新しい受信メッセージをサブスクライブし、それに応じてメモリ内の現在のメッセージを更新することができます。 Azure Communication Services は、[サブスクライブ可能な一連のイベント](../../../concepts/chat/concepts.md#real-time-notifications)をサポートしています。

コメント `<RECEIVE CHAT MESSAGES>` を次のコードに置き換えます (import ステートメントはファイルの先頭に 配置します)。

```java

// Start real time notification
chatAsyncClient.startRealtimeNotifications(firstUserAccessToken, getApplicationContext());

// Register a listener for chatMessageReceived event
chatAsyncClient.addEventHandler(ChatEventType.CHAT_MESSAGE_RECEIVED, (ChatEvent payload) -> {
    ChatMessageReceivedEvent chatMessageReceivedEvent = (ChatMessageReceivedEvent) payload;
    // You code to handle chatMessageReceived event
    
});

```

> [!IMPORTANT]
> 既知の問題: 同じアプリケーションで Android チャットと Calling SDK を一緒に使用している場合、Chat SDK のリアルタイム通知機能は機能しません。 依存関係の解決の問題が発生することがあります。
> Microsoft がこの問題の解決に向けて取り組んでいる間、アプリの build.gradle ファイルに次の依存関係情報を追加し、代わりに GetMessages API をポーリングして受信メッセージをユーザーに表示することで、リアルタイム通知機能を無効にできます。 
> 
> ```
> implementation ("com.azure.android:azure-communication-chat:1.0.0") {
>     exclude group: 'com.microsoft', module: 'trouter-client-android'
> }
> implementation 'com.azure.android:azure-communication-calling:1.0.0'
> ```
> 
> 上記の更新では、アプリケーションが `chatAsyncClient.startRealtimeNotifications()` や `chatAsyncClient.addEventHandler()` のような通知 API にアクセスしようとすると、ランタイム エラーが発生することに注意してください。


## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>チャット スレッドに参加者としてユーザーを追加する

`<ADD A USER>` コメントを、次のコードに置き換えます。

```java
// The display name for the thread participant.
String secondUserDisplayName = "a new participant";
ChatParticipant participant = new ChatParticipant()
    .setCommunicationIdentifier(new CommunicationUserIdentifier(secondUserId))
    .setDisplayName(secondUserDisplayName);
        
chatThreadAsyncClient.addParticipant(participant);

```


## <a name="list-users-in-a-thread"></a>スレッド内のユーザーを一覧表示する

コメント `<LIST USERS>` を次のコードに置き換えます (import ステートメントはファイルの先頭に配置します)。

```java
import com.azure.android.core.rest.util.paging.PagedAsyncStream;
import com.azure.android.core.util.RequestContext;

// The maximum number of participants to be returned per page, optional.
int maxPageSize = 10;

// Skips participants up to a specified position in response.
int skip = 0;

// Options to pass to the list method.
ListParticipantsOptions listParticipantsOptions = new ListParticipantsOptions()
    .setMaxPageSize(maxPageSize)
    .setSkip(skip);

PagedAsyncStream<ChatParticipant> participantsPagedAsyncStream =
      chatThreadAsyncClient.listParticipants(listParticipantsOptions, RequestContext.NONE);

participantsPagedAsyncStream.forEach(chatParticipant -> {
    // You code to handle participant
});

```


## <a name="remove-user-from-a-chat-thread"></a>チャット スレッドからユーザーを削除する

ここで、2 番目のユーザーをスレッドから削除します。

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

上記で送信されたメッセージの開封確認メッセージを送信します。

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

PagedAsyncStream<ChatMessageReadReceipt> readReceiptsPagedAsyncStream =
      chatThreadAsyncClient.listReadReceipts(listReadReceiptOptions, RequestContext.NONE);

readReceiptsPagedAsyncStream.forEach(readReceipt -> {
    // You code to handle readReceipt
});

```

## <a name="run-the-code"></a>コードの実行

Android Studio で [Run]\(実行\) ボタンを押して、プロジェクトをビルドして実行します。 コンソールには、そのコードからの出力と ChatClient からのロガー出力が表示されます。
