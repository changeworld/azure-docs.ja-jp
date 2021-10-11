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
ms.openlocfilehash: d61120d3f4e1256b9da50b8128b23fd73b36a5ef
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2021
ms.locfileid: "129406823"
---
## <a name="sample-code"></a>サンプル コード
このクイックスタートの最終的なコードは <bpt id="p1">[</bpt>GitHub<ept id="p1">](https://github.com/Azure-Samples/communication-services-ios-quickstarts/tree/main/add-chat)</ept> にあります。

## <a name="prerequisites"></a>前提条件
開始する前に、必ず次のことを行ってください。

- アクティブなサブスクリプションがある Azure アカウントを作成します。 詳細については、<bpt id="p1">[</bpt>アカウントの無料作成<ept id="p1">](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)</ept>に関するページを参照してください。 
- <bpt id="p1">[</bpt>Xcode<ept id="p1">](https://developer.apple.com/xcode/)</ept> と <bpt id="p2">[</bpt>CocoaPods<ept id="p2">](https://cocoapods.org/)</ept> をインストールします。 Xcode は、このクイックスタート用の iOS アプリケーションの作成に、CocoaPods は依存関係のインストールに使用します。
- Azure Communication Services リソースを作成します。 詳細については、「<bpt id="p1">[</bpt>クイックスタート: Communication Services のリソースを作成して管理する<ept id="p1">](../../create-communication-resource.md)</ept>」を参照してください。 このクイックスタート用に、自分のリソースのエンドポイントを記録する必要があります。
- Azure Communication Services で 2 人のユーザーを作成し、それらに対して<bpt id="p1">[</bpt>ユーザー アクセス トークン<ept id="p1">](../../access-tokens.md)</ept>を発行します。 スコープは必ず <ph id="ph1">`chat`</ph> に設定し、<ph id="ph2">`token`</ph> 文字列と <ph id="ph3">`userId`</ph> 文字列をメモしてください。 このクイックスタートでは、最初の参加者でスレッドを作成した後、そのスレッドに 2 人目の参加者を追加します。

## <a name="setting-up"></a>設定

### <a name="create-a-new-ios-application"></a>新しい iOS アプリケーションを作成する

Xcode を開き、 <bpt id="p1">**</bpt>[Create a new Xcode project (新しい Xcode プロジェクトを作成)]<ept id="p1">**</ept> を選択します。 次に、プラットフォームとして <bpt id="p1">**</bpt>[iOS]<ept id="p1">**</ept> を選択し、テンプレートとして <bpt id="p2">**</bpt>[App]\(アプリ\)<ept id="p2">**</ept> を選択します。

プロジェクト名として、「<bpt id="p1">**</bpt>ChatQuickstart<ept id="p1">**</ept>」と入力します。 次に、インターフェイスとして <bpt id="p1">**</bpt>[Storyboard]\(ストーリーボード\)<ept id="p1">**</ept> 、ライフ サイクルとして <bpt id="p2">**</bpt>[UIKit App Delegate]\(UIKit アプリ デリゲート\)<ept id="p2">**</ept> 、言語として <bpt id="p3">**</bpt>[Swift]<ept id="p3">**</ept> を選択します。

<bpt id="p1">**</bpt>[Next]\(次へ\)<ept id="p1">**</ept> を選択し、プロジェクトの作成先ディレクトリを選択します。

### <a name="install-the-libraries"></a>ライブラリをインストールする

CocoaPods を使用して、必要な Communication Services の依存関係をインストールします。

コマンド ラインから、<ph id="ph1">`ChatQuickstart`</ph> iOS プロジェクトのルート ディレクトリに移動します。 <ph id="ph1">`pod init`</ph> コマンドを使用してポッドファイルを作成します。

そのポッドファイルを開き、<ph id="ph1">`ChatQuickstart`</ph> ターゲットに次の依存関係を追加します。

```
pod 'AzureCommunicationCommon', '~> 1.0.2'
pod 'AzureCommunicationChat', '~> 1.1.0-beta.2'
```

<ph id="ph1">`pod install`</ph> コマンドを使用して、依存関係をインストールします。 これにより、Xcode ワークスペースも作成されることに注意してください。

<ph id="ph1">`pod install`</ph> を実行した後、新しく作成された <ph id="ph2">`.xcworkspace`</ph> を選択して、Xcode でプロジェクトを再度開きます。

### <a name="set-up-the-placeholders"></a>プレースホルダーを設定する

Xcode でワークスペース <ph id="ph1">`ChatQuickstart.xcworkspace`</ph> を開いてから、<ph id="ph2">`ViewController.swift`</ph> を開きます。

このクイックスタートでは、<ph id="ph1">`viewController`</ph> にコードを追加し、その出力を Xcode コンソールで表示します。 このクイックスタートでは、iOS でのユーザー インターフェイスの構築については説明しません。 

<ph id="ph1">`viewController.swift`</ph> の先頭で、<ph id="ph2">`AzureCommunication`</ph> および <ph id="ph3">`AzureCommunicatonChat`</ph> ライブラリをインポートします。

```
import AzureCommunicationCommon
import AzureCommunicationChat
```

<ph id="ph2">`ViewController`</ph> の <ph id="ph1">`viewDidLoad()`</ph> メソッドに次のコードをコピーします。

```
override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.

        let semaphore = DispatchSemaphore(value: 0)
        DispatchQueue.global(qos: .background).async {
            do {
                // <CREATE A CHAT CLIENT>
                
                // <CREATE A CHAT THREAD>

                // <LIST ALL CHAT THREADS>

                // <GET A CHAT THREAD CLIENT>

                // <SEND A MESSAGE>

                // <SEND A READ RECEIPT >

                // <RECEIVE MESSAGES>

                // <ADD A USER>
                
                // <LIST USERS>
            } catch {
                print("Quickstart failed: \(error.localizedDescription)")
            }
        }
    }
```

デモンストレーション用に、コードの同期にはセマフォを使用します。 以降の手順では、Azure Communication Services の Chat ライブラリを使用して、プレースホルダーをサンプル コードに置き換えていきます。


### <a name="create-a-chat-client"></a>チャット クライアントを作成する

チャット クライアントを作成するには、Communication Services エンドポイントと、前提条件の手順で生成されたアクセス トークンを使用します。

詳細については、<bpt id="p1">[</bpt>ユーザー アクセス トークン<ept id="p1">](../../access-tokens.md)</ept>に関するページを参照してください。

このクイックスタートでは、チャット アプリケーションのトークンを管理するためのサービス レベルの作成については説明しませんが、サービス レベルの使用をお勧めします。 詳細については、<bpt id="p1">[</bpt>チャットのアーキテクチャ<ept id="p1">](../../../concepts/chat/concepts.md)</ept>に関するドキュメントを参照してください

コメント `<CREATE A CHAT CLIENT>` を以下のコード スニペットに置き換えます。

```
let endpoint = "<ACS_RESOURCE_ENDPOINT>"
let credential =
try CommunicationTokenCredential(
    token: "<ACCESS_TOKEN>"
)
let options = AzureCommunicationChatClientOptions()

let chatClient = try ChatClient(
    endpoint: endpoint,
    credential: credential,
    withOptions: options
)
```

<ph id="ph1">`<ACS_RESOURCE_ENDPOINT>`</ph> を、実際の Azure Communication Services リソースのエンドポイントに置き換えます。 <ph id="ph1">`<ACCESS_TOKEN>`</ph> を 有効な Communication Services アクセス トークンに置き換えます。

## <a name="object-model"></a>オブジェクト モデル 

iOS 用 Azure Communication Services Chat SDK が備える主な機能のいくつかは、以下のクラスとインターフェイスにより処理されます。

| 名前                                   | 説明                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ChatClient` | このクラスはチャットの機能に必要です。 サブスクリプション情報を使用してインスタンス化し、それを使用してスレッドを作成、取得、削除し、チャット イベントにサブスクライブします。 |
| `ChatThreadClient` | このクラスはチャット スレッド機能に必要です。 <ph id="ph1">`ChatClient`</ph> を介してインスタンスを取得し、それを使用して、メッセージの送信、受信、更新、および削除を行います。 また、これを使用して、ユーザーの追加、削除、取得、および入力通知と開封確認メッセージの送信を行うこともできます。 |

## <a name="start-a-chat-thread"></a>チャット スレッドを開始する

<ph id="ph1">`CreateChatThreadResult`</ph> は、チャット スレッドの作成から返された応答です。
これには、`ChatThreadProperties` オブジェクトである `chatThread` プロパティが含まれています。 このオブジェクトには threadId が含まれており、作成されたスレッドに対する操作 (参加者の追加、メッセージの送信など) を実行するための `ChatThreadClient` を取得するときに使用できます。

コメント `<CREATE A CHAT THREAD>` を以下のコード スニペットに置き換えます。

```
let request = CreateChatThreadRequest(
    topic: "Quickstart",
    participants: [
        ChatParticipant(
            id: CommunicationUserIdentifier("<USER_ID>"),
            displayName: "Jack"
        )
    ]
)

var threadId: String?
chatClient.create(thread: request) { result, _ in
    switch result {
    case let .success(result):
        threadId = result.chatThread?.id

    case .failure:
        fatalError("Failed to create thread.")
    }
    semaphore.signal()
}
semaphore.wait()
```

<ph id="ph1">`<USER_ID>`</ph> は、有効な Communication Services のユーザー ID に置き換えます。

ここで、セマフォを使用して、処理を続行する前に完了ハンドラーを待機します。 後続の手順で、完了ハンドラーに返された応答の <ph id="ph1">`threadId`</ph> を使用します。

## <a name="list-all-chat-threads"></a>すべてのチャット スレッドを一覧表示する

チャット スレッドを作成した後、<ph id="ph2">`ChatClient`</ph> の <ph id="ph1">`listChatThreads`</ph> メソッドを呼び出すことで、すべてのチャット スレッドを一覧表示できます。 <ph id="ph1">`<LIST ALL CHAT THREADS>`</ph> コメントを、次のコードに置き換えます。

```
chatClient.listThreads { result, _ in
    switch result {
    case let .success(threads):
        guard let chatThreadItems = threads.pageItems else {
            print("No threads returned.")
            return
        }

        for chatThreadItem in chatThreadItems {
            print("Thread id: \(chatThreadItem.id)")
        }
    case .failure:
        print("Failed to list threads")
    }
    semaphore.signal()
}
semaphore.wait()
```

## <a name="get-a-chat-thread-client"></a>チャット スレッド クライアントを取得する

<ph id="ph1">`createClient`</ph> メソッドは、既に存在するスレッドの <ph id="ph2">`ChatThreadClient`</ph> を返します。 これは、作成したスレッドに対し、参加者の追加、メッセージの送信などの操作を実行する場合に使用できます。threadId は、既存のチャット スレッドの一意の ID です。

<ph id="ph1">`<GET A CHAT THREAD CLIENT>`</ph> コメントを、次のコードに置き換えます。

```
let chatThreadClient = try chatClient.createClient(forThread: threadId!)
```

## <a name="send-a-message-to-a-chat-thread"></a>チャット スレッドにメッセージを送信する

threadId で識別されるスレッドにメッセージを送信するには、`send` メソッドを使用します。

メッセージ要求は、<ph id="ph1">`SendChatMessageRequest`</ph> を使用して記述します。

- チャット メッセージの内容は、`content` を使用して設定します
- 送信者の表示名を指定するには、`senderDisplayName` を使用します
- 'text' や 'html' などのメッセージの種類を指定するには、`type` を使用します
- メッセージと共に送信する追加データを含めるには、必要に応じて <ph id="ph1">`metadata`</ph> を使用します。 このフィールドは、開発者がチャット メッセージ機能を拡張し、ユース ケースに応じたカスタム情報を追加するためのメカニズムを提供します。 たとえば、メッセージ内でファイル リンクを共有する場合、'hasAttachment:true' をメタデータに追加することで、受信者のアプリケーションがそれを解析して適切に表示できます。

<ph id="ph1">`SendChatMessageResult`</ph> は、メッセージの送信から返された応答です。ここには ID (メッセージの一意の ID) が含まれています。

コメント `<SEND A MESSAGE>` を以下のコード スニペットに置き換えます。

```
let message = SendChatMessageRequest(
                        content: "Hello!",
                        senderDisplayName: "Jack",
                        type: .text,
                        metadata: [
                            "hasAttachment": "true",
                            "attachmentUrl": "https://contoso.com/files/attachment.docx"
                        ]
                    )

var messageId: String?

chatThreadClient.send(message: message) { result, _ in
    switch result {
    case let .success(result):
        print("Message sent, message id: \(result.id)")
        messageId = result.id
    case .failure:
        print("Failed to send message")
    }
    semaphore.signal()
}
semaphore.wait()
```

## <a name="send-a-read-receipt"></a>開封確認メッセージを送信する

<ph id="ph1">`sendReadReceipt`</ph> メソッドを使用すると、ユーザーに代わって開封確認イベントをチャット スレッドに送信できます。
<ph id="ph1">`messageId`</ph> は、開封されたチャット メッセージの一意の ID です。

コメント `<SEND A READ RECEIPT>` を以下のコードに置き換えます。

```
if let id = messageId {
    chatThreadClient.sendReadReceipt(forMessage: id) { result, _ in
        switch result {
        case .success:
            print("Read receipt sent")
        case .failure:
            print("Failed to send read receipt")
        }
        semaphore.signal()
    }
    semaphore.wait()
} else {
    print("Cannot send read receipt without a message id")
}
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>チャット スレッドからチャット メッセージを受信する

リアルタイム シグナリングを使用すると、新しい受信メッセージをサブスクライブしてリッスンし、それに応じてメモリ内の現在のメッセージを更新することができます。 Azure Communication Services は、<bpt id="p1">[</bpt>サブスクライブ可能な一連のイベント<ept id="p1">](../../../concepts/chat/concepts.md#real-time-notifications)</ept>をサポートしています。

コメント `<RECEIVE MESSAGES>` を以下のコードに置き換えます。 通知を有効にした後、新しいメッセージを送信して ChatMessageReceivedEvents を確認してみてください。

```
chatClient.startRealTimeNotifications { result in
    switch result {
    case .success:
        print("Real-time notifications started.")
    case .failure:
        print("Failed to start real-time notifications.")
    }
    semaphore.signal()
}
semaphore.wait()

chatClient.register(event: .chatMessageReceived, handler: { response in
    switch response {
    case let .chatMessageReceivedEvent(event):
        print("Received a message: \(event.message)")
    default:
        return
    }
})
```

チャット メッセージは、特定の間隔で <ph id="ph1">`listMessages`</ph> メソッドをポーリングすることによって取得することもできます。 `listMessages` に対して次のコード スニペットを実行します

```
chatThreadClient.listMessages { result, _ in
    switch result {
    case let .success(messagesResult):
        guard let messages = messagesResult.pageItems else {
            print("No messages returned.")
            return
        }

        for message in messages {
            print("Received message with id: \(message.id)")
        }

    case .failure:
        print("Failed to receive messages")
    }
    semaphore.signal()
}
semaphore.wait()
```

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>チャット スレッドに参加者としてユーザーを追加する

スレッドの作成後、ユーザーを追加したり削除したりすることができます。 追加したユーザーには、スレッドにメッセージを送信したり、他の参加者を追加または削除したりできるアクセス権が与えられます。 <ph id="ph1">`add`</ph> を呼び出す前に必ず、そのユーザーの新しいアクセス トークンと ID を取得してください。 チャット クライアントを初期化するためには、ユーザーにアクセス トークンが必要となります。

チャット スレッドに 1 人以上の参加者を追加するには、`ChatThreadClient` の `add` メソッドを使用します。 各スレッド参加者でサポートされている属性は次のとおりです。
- <ph id="ph1">`id`</ph> (必須) は、スレッド参加者の ID です。
- <ph id="ph1">`displayName`</ph> (省略可) は、スレッド参加者の表示名です。
- <ph id="ph1">`shareHistoryTime`</ph> (省略可) は、参加者との間でチャット履歴が共有される際の起点となる時刻です。

<ph id="ph1">`<ADD A USER>`</ph> コメントを、次のコードに置き換えます。

```
let user = ChatParticipant(
    id: CommunicationUserIdentifier("<USER_ID>"),
    displayName: "Jane"
)

chatThreadClient.add(participants: [user]) { result, _ in
    switch result {
    case let .success(result):
        (result.invalidParticipants != nil) ? print("Added participant") : print("Error adding participant")
    case .failure:
        print("Failed to add the participant")
    }
    semaphore.signal()
}
semaphore.wait()
```

<ph id="ph1">`<USER_ID>`</ph> を、追加するユーザーの Communication Services ユーザー ID に置き換えます。

## <a name="list-users-in-a-thread"></a>スレッド内のユーザーを一覧表示する

特定のチャット スレッドのすべての参加者を取得するには、`listParticipants` メソッドを使用します。

コメント <ph id="ph1">`<LIST USERS>`</ph> を、次のコードに置き換えます。

```
chatThreadClient.listParticipants { result, _ in
    switch result {
    case let .success(participantsResult):
        guard let participants = participantsResult.pageItems else {
            print("No participants returned.")
            return
        }

        for participant in participants {
            let user = participant.id as! CommunicationUserIdentifier
            print("User with id: \(user.identifier)")
        }
    case .failure:
        print("Failed to list participants")
    }
    semaphore.signal()
}
semaphore.wait()
```

## <a name="run-the-code"></a>コードの実行

Xcode で [Run]\(実行\) ボタンをクリックし、プロジェクトをビルドして実行します。 コンソールには、そのコードからの出力と ChatClient からのロガー出力が表示されます。

<bpt id="p1">**</bpt>注:<ept id="p1">**</ept> <ph id="ph1">`Build Settings > Build Options > Enable Bitcode`</ph> を <ph id="ph2">`No`</ph> に設定します。 現時点では、AzureCommunicationChat SDK for iOS では bitcode の有効化がサポートされていません。次の [GitHub のイシュー](https://github.com/Azure/azure-sdk-for-ios/issues/787)によって追跡されています。
