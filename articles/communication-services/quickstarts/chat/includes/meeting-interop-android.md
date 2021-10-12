---
title: クイックスタート - Teams の会議に参加する
author: agiurg
ms.author: agiurg
ms.date: 07/20/2021
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: bb5ccb022b4bfeb90f7c50f27ce2a76d43386571
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129378590"
---
このクイックスタートでは、Android 用 Azure Communication Services Chat SDK を使用して Teams 会議でチャットする方法について説明します。

## <a name="prerequisites"></a>前提条件 

-  [Teams のデプロイ](/deployoffice/teams-install)。 
- 実際に動作する[通話アプリ](../../voice-video-calling/get-started-teams-interop.md)。 

## <a name="enable-teams-interoperability"></a>Teams の相互運用性を有効にする 

ゲスト ユーザーとして Teams に参加している Communication Services ユーザーは、Teams の会議通話に参加したときにのみ会議のチャットにアクセスできます。 Communication Services ユーザーを Teams の会議通話に追加する方法については、[Teams の相互運用性](../../voice-video-calling/get-started-teams-interop.md)に関するドキュメントを参照してください。

この機能を使用するには、両方のエンティティの所有組織のメンバーである必要があります。

## <a name="joining-the-meeting-chat"></a>会議チャットへの参加 

Teams の相互運用性が有効になると、Communication Services ユーザーは、Calling SDK を使用して外部ユーザーとして Teams の通話に参加できます。 通話に参加すると、会議チャットにも参加者として追加されます。会議チャットでは、通話の他のユーザーとメッセージを送受信できます。 ユーザーは、通話に参加する前に送信されたチャット メッセージにはアクセスできません。 会議に参加してチャットを開始するには、次の手順に従います。

## <a name="add-chat-to-the-teams-calling-app"></a>Teams 通話アプリにチャットを追加する

モジュール レベルの build.gradle で、Chat SDK への依存関係を追加します。

> [!IMPORTANT]
> 既知の問題: 同じアプリケーションで Android チャットと Calling SDK を一緒に使用している場合、Chat SDK のリアルタイム通知機能は機能しません。 依存関係の解決の問題が発生します。 Microsoft が解決策に取り組んでいる間は、アプリの `build.gradle` ファイル内の Chat SDK の依存関係に次の除外を追加することで、リアルタイム通知機能をオフにすることができます。
> 
> ```groovy
> implementation ("com.azure.android:azure-communication-chat:1.0.0") {
>     exclude group: 'com.microsoft', module: 'trouter-client-android'
> }
> ```


## <a name="add-the-teams-ui-layout"></a>Teams UI レイアウトを追加する

activity_main.xml のコードを次のスニペットで置き換えます。 これにより、スレッド ID とメッセージ送信の入力、入力したメッセージを送信するボタン、基本的なチャット レイアウトが追加されます。

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <EditText
        android:id="@+id/teams_meeting_thread_id"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginHorizontal="20dp"
        android:layout_marginTop="128dp"
        android:ems="10"
        android:hint="Meeting Thread Id"
        android:inputType="textUri"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <EditText
        android:id="@+id/teams_meeting_link"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginHorizontal="20dp"
        android:layout_marginTop="64dp"
        android:ems="10"
        android:hint="Teams meeting link"
        android:inputType="textUri"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <LinearLayout
        android:id="@+id/button_layout"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="30dp"
        android:gravity="center"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.0"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/teams_meeting_thread_id">

        <Button
            android:id="@+id/join_meeting_button"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Join Meeting" />

        <Button
            android:id="@+id/hangup_button"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Hangup" />

    </LinearLayout>

    <TextView
        android:id="@+id/call_status_bar"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="40dp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent" />

    <TextView
        android:id="@+id/recording_status_bar"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="20dp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent" />

    <ScrollView
        android:id="@+id/chat_box"
        android:layout_width="374dp"
        android:layout_height="294dp"
        android:layout_marginTop="40dp"
        android:layout_marginBottom="20dp"
        app:layout_constraintBottom_toTopOf="@+id/send_message_button"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/button_layout"
        android:orientation="vertical"
        android:gravity="bottom"
        android:layout_gravity="bottom"
        android:fillViewport="true">

        <LinearLayout
            android:id="@+id/chat_box_layout"
            android:orientation="vertical"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent"
            android:gravity="bottom"
            android:layout_gravity="top"
            android:layout_alignParentBottom="true"/>
    </ScrollView>

    <EditText
        android:id="@+id/message_body"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginHorizontal="20dp"
        android:layout_marginTop="588dp"
        android:ems="10"
        android:inputType="textUri"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        tools:text="Type your message here..."
        tools:visibility="invisible" />

    <Button
        android:id="@+id/send_message_button"
        android:layout_width="138dp"
        android:layout_height="45dp"
        android:layout_marginStart="133dp"
        android:layout_marginTop="48dp"
        android:layout_marginEnd="133dp"
        android:text="Send Message"
        android:visibility="invisible"
        app:layout_constraintBottom_toTopOf="@+id/recording_status_bar"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.428"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/chat_box" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

## <a name="enable-the-teams-ui-controls"></a>Teams の UI コントロールを有効にする

### <a name="import-packages-and-define-state-variables"></a>パッケージのインポートと状態変数の定義

`MainActivity.java` の内容に、さらに次のインポートを追加します。

```
import android.graphics.Typeface;
import android.graphics.Color;
import android.text.Html;
import android.os.Handler;
import android.view.Gravity;
import android.view.View;
import android.widget.LinearLayout;
import java.util.Collections;
import java.util.concurrent.CountDownLatch;
import java.util.concurrent.TimeUnit;
import com.azure.android.communication.chat.ChatThreadAsyncClient;
import com.azure.android.communication.chat.ChatThreadClientBuilder;
import com.azure.android.communication.chat.models.ChatMessage;
import com.azure.android.communication.chat.models.ChatMessageType;
import com.azure.android.communication.chat.models.ChatParticipant;
import com.azure.android.communication.chat.models.ListChatMessagesOptions;
import com.azure.android.communication.chat.models.SendChatMessageOptions;
import com.azure.android.communication.common.CommunicationIdentifier;
import com.azure.android.communication.common.CommunicationUserIdentifier;
import com.azure.android.core.rest.util.paging.PagedAsyncStream;
import com.azure.android.core.util.AsyncStreamHandler;
```

`MainActivity` ファイルに次の変数を追加します。

```
    // InitiatorId is used to differentiate incoming messages from outgoing messages
    private static final String InitiatorId = "<USER_ID>";
    private static final String ResourceUrl = "<COMMUNICATION_SERVICES_RESOURCE_ENDPOINT>";
    private String threadId;
    private ChatThreadAsyncClient chatThreadAsyncClient;
    
    // The list of ids corresponsding to messages which have already been processed
    ArrayList<String> chatMessages = new ArrayList<>();
```

`<USER_ID>` を、チャットを開始したユーザーの ID に置き換えます。
`<COMMUNICATION_SERVICES_RESOURCE_ENDPOINT>` を、実際の Communication Services リソースのエンドポイントに置き換えます。 

### <a name="initialize-the-chatthreadclient"></a>ChatThreadClient を初期化する

会議への参加後、`ChatThreadClient` のインスタンスを作成し、チャット コンポーネントを表示します。

`MainActivity.joinTeamsMeeting()` メソッドの末尾を次のコードのように更新します。

```
    private void joinTeamsMeeting() {
        ...
        EditText threadIdView = findViewById(R.id.teams_meeting_thread_id);
        threadId = threadIdView.getText().toString();
        // Initialize Chat Thread Client
        chatThreadAsyncClient = new ChatThreadClientBuilder()
                .endpoint(ResourceUrl)
                .credential(new CommunicationTokenCredential(UserToken))
                .chatThreadId(threadId)
                .buildAsyncClient();
        Button sendMessageButton = findViewById(R.id.send_message_button);
        EditText messageBody = findViewById(R.id.message_body);
        // Register the method for sending messages and toggle the visibility of chat components
        sendMessageButton.setOnClickListener(l -> sendMessage());
        sendMessageButton.setVisibility(View.VISIBLE);
        messageBody.setVisibility(View.VISIBLE);
        
        // Start the polling for chat messages immediately
        handler.post(runnable);
    }
```

### <a name="enable-sending-messages"></a>メッセージの送信を有効にする

`sendMessage()` メソッドを `MainActivity` に追加します。 `ChatThreadClient` が使用され、ユーザーに代わってメッセージが送信されます。

```
    private void sendMessage() {
        // Retrieve the typed message content
        EditText messageBody = findViewById(R.id.message_body);
        // Set request options and send message
        SendChatMessageOptions options = new SendChatMessageOptions();
        options.setContent(messageBody.getText().toString());
        options.setSenderDisplayName("ACS User");
        chatThreadAsyncClient.sendMessage(options);
        // Clear the text box
        messageBody.setText("");
    }
```

### <a name="enable-polling-for-messages-and-rendering-them-in-the-application"></a>メッセージのポーリングとアプリケーションでのレンダリングを有効にする

> [!IMPORTANT]
> 既知の問題: Chat SDK のリアルタイム通知機能は Calling SDK の機能と連携していないため、事前に定義された間隔で `GetMessages` API をポーリングする必要があります。 このサンプルでは、3 秒間隔を使用します。

`GetMessages` API から返されるメッセージ一覧から次のデータを取得できます。 
 - 参加後のスレッド上の `text` と `html` のメッセージ
 - スレッド参加者一覧の変更
 - スレッド トピックの更新


`MainActivity` クラスに、ハンドラーと、3 秒間隔で実行される実行可能なタスクを追加します。

```
    private Handler handler = new Handler();
    private Runnable runnable = new Runnable() {
        @Override
        public void run() {
            try {
                retrieveMessages();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            // Repeat every 3 seconds
            handler.postDelayed(runnable, 3000);
        }
    };
```

初期化手順で更新された `MainActivity.joinTeamsMeeting()` メソッドの最後で、タスクが既に開始されていることに注意してください。

最後に、スレッド上のアクセス可能なすべてのメッセージのクエリを実行し、メッセージの種類ごとに解析し、`html` と `text` のものを表示するメソッドを追加します。

```
    private void retrieveMessages() throws InterruptedException {
        // Initialize the list of messages not yet processed
        ArrayList<ChatMessage> newChatMessages = new ArrayList<>();
        
        // Retrieve all messages accessible to the user
        PagedAsyncStream<ChatMessage> messagePagedAsyncStream
                = this.chatThreadAsyncClient.listMessages(new ListChatMessagesOptions(), null);
        // Set up a lock to wait until all returned messages have been inspected
        CountDownLatch latch = new CountDownLatch(1);
        // Traverse the returned messages
        messagePagedAsyncStream.forEach(new AsyncStreamHandler<ChatMessage>() {
            @Override
            public void onNext(ChatMessage message) {
                // Messages that should be displayed in the chat
                if ((message.getType().equals(ChatMessageType.TEXT)
                    || message.getType().equals(ChatMessageType.HTML))
                    && !chatMessages.contains(message.getId())) {
                    newChatMessages.add(message);
                    chatMessages.add(message.getId());
                }
                if (message.getType().equals(ChatMessageType.PARTICIPANT_ADDED)) {
                    // Handle participants added to chat operation
                    List<ChatParticipant> participantsAdded = message.getContent().getParticipants();
                    CommunicationIdentifier participantsAddedBy = message.getContent().getInitiatorCommunicationIdentifier();
                }
                if (message.getType().equals(ChatMessageType.PARTICIPANT_REMOVED)) {
                    // Handle participants removed from chat operation
                    List<ChatParticipant> participantsRemoved = message.getContent().getParticipants();
                    CommunicationIdentifier participantsRemovedBy = message.getContent().getInitiatorCommunicationIdentifier();
                }
                if (message.getType().equals(ChatMessageType.TOPIC_UPDATED)) {
                    // Handle topic updated
                    String newTopic = message.getContent().getTopic();
                    CommunicationIdentifier topicUpdatedBy = message.getContent().getInitiatorCommunicationIdentifier();
                }
            }
            @Override
            public void onError(Throwable throwable) {
                latch.countDown();
            }
            @Override
            public void onComplete() {
                latch.countDown();
            }
        });
        // Wait until the operation completes
        latch.await(1, TimeUnit.MINUTES);
        // Returned messages should be ordered by the createdOn field to be guaranteed a proper chronological order
        // For the purpose of this demo we will just reverse the list of returned messages
        Collections.reverse(newChatMessages);
        for (ChatMessage chatMessage : newChatMessages)
        {
            LinearLayout chatBoxLayout = findViewById(R.id.chat_box_layout);
            // For the purpose of this demo UI, we don't need to use HTML formatting for displaying messages
            // The Teams client always sends html messages in meeting chats 
            String message = Html.fromHtml(chatMessage.getContent().getMessage(), Html.FROM_HTML_MODE_LEGACY).toString().trim();
            TextView messageView = new TextView(this);
            messageView.setText(message);
            // Compare with sender identifier and align LEFT/RIGHT accordingly
            // ACS users are of type CommunicationUserIdentifier
            CommunicationIdentifier senderId = chatMessage.getSenderCommunicationIdentifier();
            if (senderId instanceof CommunicationUserIdentifier
                && InitiatorId.equals(((CommunicationUserIdentifier) senderId).getId())) {
                messageView.setTextColor(Color.GREEN);
                messageView.setGravity(Gravity.RIGHT);
            } else {
                messageView.setTextColor(Color.BLUE);
                messageView.setGravity(Gravity.LEFT);
            }
            // Note: messages with the deletedOn property set to a timestamp, should be marked as deleted
            // Note: messages with the editedOn property set to a timestamp, should be marked as edited
            messageView.setTypeface(Typeface.SANS_SERIF, Typeface.BOLD);
            chatBoxLayout.addView(messageView);
        }
    }
```

チャット スレッド参加者の表示名は、Teams クライアントによって設定されません。 これらの名前は、`participantsAdded` イベントと `participantsRemoved` イベントの、参加者を一覧表示する API では null として返されます。 チャット参加者の表示名は、`call` オブジェクトの `remoteParticipants` フィールドから取得できます。

## <a name="get-a-teams-meeting-chat-thread-for-a-communication-services-user"></a>Communication Services ユーザーの Teams 会議チャット スレッドを取得する

Teams 会議の詳細情報は Graph API を使用して取得できます。詳細については、[Graph のドキュメント](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true)を参照してください。 Communication Services 通話 SDK は、Teams 会議の完全なリンクまたはミーティング ID を受け入れます。 これらは `onlineMeeting` リソースの一部として返され、[`joinWebUrl` プロパティ](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true)の下でアクセスできます。

[Graph API](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true) を使用して、`threadID` を取得することもできます。 応答には、`threadID` を含む `chatInfo` オブジェクトがあります。

必要な会議情報とスレッド ID は、Teams 会議の招待状自体に含まれている **[Join Meeting]\(会議に参加\)** URL から取得することもできます。
Teams の会議リンクは、`https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here` のようになっています。 `threadId` は、このリンクの `meeting_chat_thread_id` 部分になります。 使用する前に、`meeting_chat_thread_id` がエスケープされていないことを確認してください。 これは、`19:meeting_ZWRhZDY4ZGUtYmRlNS00OWZaLTlkZTgtZWRiYjIxOWI2NTQ4@thread.v2` という形式になっている必要があります。


## <a name="run-the-code"></a>コードの実行

ツール バーの [Run App]\(アプリの実行\) ボタン (Shift + F10) を使用してアプリを起動できるようになりました。

Teams の会議とチャットに参加するには、Teams の会議リンクとスレッド ID を UI に入力します。

チームの会議に参加したら、チームのクライアントでユーザーに会議への参加を許可する必要があります。 ユーザーが許可され、チャットに参加すると、メッセージを送受信できるようになります。

:::image type="content" source="../join-teams-meeting-chat-quickstart-android.png" alt-text="完成した Android アプリケーションのスクリーンショット。":::

> [!NOTE] 
> 現在、Teams との相互運用性のシナリオでは、メッセージの送受信と編集のみがサポートされています。 入力インジケーターや、Communication Services ユーザーが Teams 会議に他のユーザーを追加または削除するなどの機能は、まだサポートされていません。
