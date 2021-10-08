---
title: クイックスタート - Teams の会議に参加する
author: askaur
ms.author: askaur
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 1bee7ed16bae65b227bdb099565d9bbdc5ff6008
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129377987"
---
このクイックスタートでは、iOS 用 Azure Communication Services Chat SDK を使用して Teams 会議でチャットする方法について説明します。

## <a name="prerequisites"></a>前提条件 

-  [Teams のデプロイ](/deployoffice/teams-install)。 
- 実際に動作する[通話アプリ](../../voice-video-calling/get-started-teams-interop.md)。 

## <a name="joining-the-meeting-chat"></a>会議チャットへの参加 

Communication Services ユーザーは、Calling SDK を使用して、匿名ユーザーとして Teams 会議に参加できます。 会議に参加すると、会議チャットにも参加者として追加されます。会議チャットでは、会議に参加している他のユーザーとメッセージを送受信できます。 ユーザーは、会議に参加する前に送信されたチャット メッセージにアクセスすることはできず、会議の終了後はメッセージを送受信することはできなくなります。 会議に参加してチャットを開始するには、次の手順に従います。

## <a name="add-chat-to-the-teams-calling-app"></a>Teams 通話アプリにチャットを追加する

`Podfile.lock` を削除し、ポッドファイルの内容を次のものに置き換えます。

```
platform :ios, '13.0'
use_frameworks!

target 'AzureCommunicationCallingSample' do
  pod 'AzureCommunicationCalling', '~> 1.0.0-beta.12'
  pod 'AzureCommunicationChat', '~> 1.0.0-beta.11'
end
```

## <a name="install-the-chat-packages"></a>チャット パッケージをインストールする

`pod install` を実行して、AzureCommunicationChat パッケージをインストールします。
インストールした後、`.xcworkspace` ファイルを開きます。

## <a name="set-up-the-app-framework"></a>アプリのフレームワークを設定する

次のスニペットを追加して、`ContentView.swift` にAzureCommunicationChat パッケージをインポートします。

```
import AzureCommunicationChat
```

## <a name="add-the-teams-ui-controls"></a>Teams の UI コントロールを追加する

`ContentView.swift` で、既存の状態変数の下に次のスニペットを追加します。 

```
    // Chat
    @State var chatClient: ChatClient?
    @State var chatThreadClient: ChatThreadClient?
    @State var chatMessage: String = ""
    @State var meetingMessages: [MeetingMessage] = []

    let displayName: String = "<YOUR_DISPLAY_NAME_HERE>"
```

`<YOUR_DISPLAY_NAME_HERE>` をチャットで使用する表示名に置き換えます。

次に、チャット メッセージを表示するようにフォーム `Section` を変更し、チャット用の UI コントロールを追加します。

次のスニペットを既存のフォームに追加します。 状態を記録するため、Text ビュー `Text(recordingStatus)` の直後です。

```
VStack(alignment: .leading) {
    ForEach(meetingMessages, id: \.id) { message in
        let currentUser: Bool = (message.displayName == self.displayName)
        let foregroundColor = currentUser ? Color.white : Color.black
        let background = currentUser ? Color.blue : Color(.systemGray6)
        let alignment = currentUser ? HorizontalAlignment.trailing : HorizontalAlignment.leading
        VStack {
            Text(message.displayName).font(Font.system(size: 10))
            Text(message.content)
        }
        .alignmentGuide(.leading) { d in d[alignment] }
        .padding(10)
        .foregroundColor(foregroundColor)
        .background(background)
        .cornerRadius(10)
    }
}.frame(minWidth: 0, maxWidth: .infinity)
TextField("Enter your message...", text: $chatMessage)
Button(action: sendMessage) {
    Text("Send Message")
}.disabled(chatThreadClient == nil)
```

次に、タイトルを `Chat Teams Quickstart` に変更します。 次の行をこのタイトルで変更します。

```
.navigationBarTitle("Chat Teams Quickstart")
```

## <a name="enable-the-teams-ui-controls"></a>Teams の UI コントロールを有効にする

### <a name="initialize-the-chatclient"></a>ChatClient を初期化する

`ChatClient` をインスタンス化し、リアルタイム通知を有効にします。 リアルタイム通知は、チャット メッセージを受信するために使用します。

`NavigationView` `.onAppear` 内の、`CallAgent` を初期化する既存のコードの後に、以下のスニペットを追加します。

```
// Initialize the ChatClient
do {
    let endpoint = "COMMUNICATION_SERVICES_RESOURCE_ENDPOINT_HERE>"
    let credential = try CommunicationTokenCredential(token: "<USER_ACCESS_TOKEN_HERE>")

    self.chatClient = try ChatClient(
        endpoint: endpoint,
        credential: credential,
        withOptions: AzureCommunicationChatClientOptions()
    )

    self.message = "ChatClient successfully created"

    // Start real-time notifications
    self.chatClient?.startRealTimeNotifications() { result in
        switch result {
        case .success:
            print("Real-time notifications started")
            // Receive chat messages
            self.chatClient?.register(event: ChatEventId.chatMessageReceived, handler: receiveMessage)
        case .failure:
            print("Failed to start real-time notifications")
            self.message = "Failed to enable chat notifications"
        }
    }
} catch {
    print("Unable to create ChatClient")
    self.message = "Please enter a valid endpoint and Chat token in source code"
    return
}
```

`<COMMUNICATION_SERVICES_RESOURCE_ENDPOINT_HERE>` を、実際の Communication Services リソースのエンドポイントに置き換えます。
`<USER_ACCESS_TOKEN_HERE>` を、チャット スコープが設定されているアクセス トークンに置き換えます。 

ユーザー アクセス トークンの詳細については、[ユーザー アクセス トークン](../../access-tokens.md)に関する記事をお読みください。

### <a name="initialize-the-chatthreadclient"></a>ChatThreadClient を初期化する

既存の `joinTeamsMeeting()` 関数内で、ユーザーが会議に参加した後に `ChatThreadClient` を初期化します。

`self.callAgent?.join()` の呼び出しの完了ハンドラー内で、`// Initialize the ChatThreadClient` コメントの下にコードを追加します。 完全なコードを以下に示します。

```
self.callAgent?.join(with: teamsMeetingLinkLocator, joinCallOptions: joinCallOptions) { (call, error) in
    if (error == nil) {
        self.call = call
        self.callObserver = CallObserver(self)
        self.call!.delegate = self.callObserver
        self.message = "Teams meeting joined successfully"

        // Initialize the ChatThreadClient
        do {
            guard let threadId = getThreadId(from: self.meetingLink) else {
                self.message = "Failed to join meeting chat"
                return
            }
            self.chatThreadClient = try chatClient?.createClient(forThread: threadId)
            self.message = "Joined meeting chat successfully"
        } catch {
            print("Failed to create ChatThreadClient")
            self.message = "Failed to join meeting chat"
            return
        }
    } else {
        print("Failed to join Teams meeting")
    }
}
```

`ContentView` に次のヘルパー関数を追加します。これは、チームの会議リンクからチャット スレッド ID を取得するために使用されます。

```
func getThreadId(from meetingLink: String) -> String? {
    if let range = self.meetingLink.range(of: "meetup-join/") {
        let thread = self.meetingLink[range.upperBound...]
        if let endRange = thread.range(of: "/")?.lowerBound {
            return String(thread.prefix(upTo: endRange))
        }
    }
    return nil
}
```

### <a name="enable-sending-messages"></a>メッセージの送信を有効にする

`sendMessage()` 関数を `ContentView` に追加します。 この関数では、`ChatThreadClient` を使用してユーザーからのメッセージを送信します。

```
func sendMessage() {
    let message = SendChatMessageRequest(
        content: self.chatMessage,
        senderDisplayName: self.displayName
    )
    
    self.chatThreadClient?.send(message: message) { result, _ in
        switch result {
        case .success:
            print("Chat message sent")
        case .failure:
            print("Failed to send chat message")
        }

        self.chatMessage = ""
    }
}
```

### <a name="enable-receiving-messages"></a>メッセージの受信を有効にする

メッセージを受信するために、`ChatMessageReceived` イベントのハンドラーを実装します。 新しいメッセージがスレッドに送信されると、それらを UI に表示できるように、このハンドラーによって `meetingMessages` 変数にメッセージが追加されます。

まず、次の構造体を `ContentView.swift` に追加します。 UI では、構造体内のデータを使用してチャット メッセージを表示します。

```
struct MeetingMessage {
    let id: String
    let content: String
    let displayName: String
}
```

次に、`receiveMessage()` 関数を `ContentView` に追加します。 これは、`ChatMessageReceived` イベントが発生するたびに呼び出されるハンドラーです。

```
func receiveMessage(response: Any, eventId: ChatEventId) {
    let chatEvent: ChatMessageReceivedEvent = response as! ChatMessageReceivedEvent

    let displayName: String = chatEvent.senderDisplayName ?? "Unknown User"
    let content: String = chatEvent.message.replacingOccurrences(of: "<[^>]+>", with: "", options: String.CompareOptions.regularExpression)

    self.meetingMessages.append(
        MeetingMessage(
            id: chatEvent.id,
            content: content,
            displayName: displayName
        )
    )
}
```

### <a name="leave-the-chat"></a>チャットから退出する

ユーザーがチームの会議から退出すると、UI からそのチャットがクリアされます。 完全なコードを以下に示します。

```
func leaveMeeting() {
    if let call = call {
        call.hangUp(options: nil, completionHandler: { (error) in
            if error == nil {
                self.message = "Leaving Teams meeting was successful"
                // Clear the chat
                self.meetingMessages.removeAll()
            } else {
                self.message = "Leaving Teams meeting failed"
            }
        })
    } else {
        self.message = "No active call to hanup"
    }
}
```

## <a name="get-a-teams-meeting-chat-thread-for-a-communication-services-user"></a>Communication Services ユーザーの Teams 会議チャット スレッドを取得する

Teams 会議の詳細情報は Graph API を使用して取得できます。詳細については、[Graph のドキュメント](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true)を参照してください。 Communication Services 通話 SDK は、Teams 会議の完全なリンクまたはミーティング ID を受け入れます。 これらは `onlineMeeting` リソースの一部として返され、[`joinWebUrl` プロパティ](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true)の下でアクセスできます。

[Graph API](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true) を使用して、`threadID` を取得することもできます。 応答には、`threadID` を含む `chatInfo` オブジェクトがあります。

必要な会議情報とスレッド ID は、Teams 会議の招待状自体に含まれている **[Join Meeting]\(会議に参加\)** URL から取得することもできます。
Teams の会議リンクは、`https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here` のようになっています。 `threadID` は、このリンクの `meeting_chat_thread_id` 部分になります。 使用する前に、`meeting_chat_thread_id` がエスケープされていないことを確認してください。 これは、`19:meeting_ZWRhZDY4ZGUtYmRlNS00OWZaLTlkZTgtZWRiYjIxOWI2NTQ4@thread.v2` という形式になっている必要があります。


## <a name="run-the-code"></a>コードの実行

アプリケーションを実行します。 

Teams 会議に参加するには、UI にチームの会議リンクを入力します。

チームの会議に参加したら、チームのクライアントでユーザーに会議への参加を許可する必要があります。 ユーザーが許可され、チャットに参加すると、メッセージを送受信できるようになります。

:::image type="content" source="../join-teams-meeting-chat-quickstart-ios.png" alt-text="完了した iOS アプリケーションのスクリーンショット。":::

> [!NOTE] 
> 現在、Teams との相互運用性のシナリオでは、メッセージの送受信と編集のみがサポートされています。 入力インジケーターや、Communication Services ユーザーが Teams 会議に他のユーザーを追加または削除するなどの機能は、まだサポートされていません。
