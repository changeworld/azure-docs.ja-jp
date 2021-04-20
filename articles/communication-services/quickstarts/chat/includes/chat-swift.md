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
ms.openlocfilehash: 24a5c92164e0eace41224edfd2153c6142f7ea49
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2021
ms.locfileid: "107251450"
---
[!INCLUDE [Public Preview Notice](../../../includes/public-preview-include-chat.md)]

## <a name="prerequisites"></a>前提条件
開始する前に、必ず次のことを行ってください。

- アクティブなサブスクリプションがある Azure アカウントを作成します。 詳細については、[アカウントの無料作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)に関するページを参照してください。 
- [Xcode](https://developer.apple.com/xcode/) と [CocoaPods](https://cocoapods.org/) をインストールします。 Xcode は、このクイックスタート用の iOS アプリケーションの作成に、CocoaPods は依存関係のインストールに使用します。
- Azure Communication Services リソースを作成します。 詳細については、「[クイックスタート: Communication Services のリソースを作成して管理する](../../create-communication-resource.md)」を参照してください。 このクイックスタート用に、自分のリソースのエンドポイントを記録する必要があります。
- Azure Communication Services で 2 人のユーザーを作成し、それらに対して[ユーザー アクセス トークン](../../access-tokens.md)を発行します。 スコープは必ず `chat` に設定し、`token` 文字列と `userId` 文字列をメモしてください。 このクイックスタートでは、最初の参加者でスレッドを作成した後、そのスレッドに 2 人目の参加者を追加します。

## <a name="setting-up"></a>設定

### <a name="create-a-new-ios-application"></a>新しい iOS アプリケーションを作成する

Xcode を開き、 **[Create a new Xcode project (新しい Xcode プロジェクトを作成)]** を選択します。 次に、プラットフォームとして **[iOS]** を選択し、テンプレートとして **[App]\(アプリ\)** を選択します。

プロジェクト名として、「**ChatQuickstart**」と入力します。 次に、インターフェイスとして **[Storyboard]\(ストーリーボード\)** 、ライフ サイクルとして **[UIKit App Delegate]\(UIKit アプリ デリゲート\)** 、言語として **[Swift]** を選択します。

**[Next]\(次へ\)** を選択し、プロジェクトの作成先ディレクトリを選択します。

### <a name="install-the-libraries"></a>ライブラリをインストールする

CocoaPods を使用して、必要な Communication Services の依存関係をインストールします。

コマンド ラインから、`ChatQuickstart` iOS プロジェクトのルート ディレクトリに移動します。 `pod init` コマンドを使用してポッドファイルを作成します。

そのポッドファイルを開き、`ChatQuickstart` ターゲットに次の依存関係を追加します。

```
pod 'AzureCommunication', '~> 1.0.0-beta.11'
pod 'AzureCommunicationChat', '~> 1.0.0-beta.11'
```

`pod install` コマンドを使用して、依存関係をインストールします。 これにより、Xcode ワークスペースも作成されることに注意してください。

`pod install` を実行した後、新しく作成された `.xcworkspace` を選択して、Xcode でプロジェクトを再度開きます。

### <a name="set-up-the-placeholders"></a>プレースホルダーを設定する

Xcode でワークスペース `ChatQuickstart.xcworkspace` を開いてから、`ViewController.swift` を開きます。

このクイックスタートでは、`viewController` にコードを追加し、その出力を Xcode コンソールで表示します。 このクイックスタートでは、iOS でのユーザー インターフェイスの構築については説明しません。 

`viewController.swift` の先頭で、`AzureCommunication` および `AzureCommunicatonChat` ライブラリをインポートします。

```
import AzureCommunication
import AzureCommunicationChat
```

`ViewController` の `viewDidLoad()` メソッドに次のコードをコピーします。

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

`<CREATE A CHAT CLIENT>` コメントを、次のコードに置き換えます。

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

`<ACS_RESOURCE_ENDPOINT>` を、実際の Azure Communication Services リソースのエンドポイントに置き換えます。 `<ACCESS_TOKEN>` を 有効な Communication Services アクセス トークンに置き換えます。

このクイックスタートでは、チャット アプリケーションのトークンを管理するためのサービス レベルの作成については説明しませんが、そうすることをお勧めします。 詳細については、「[チャットに関する概念](../../../concepts/chat/concepts.md)」の「チャットのアーキテクチャ」セクションを参照してください。

ユーザー アクセス トークンの詳細については、「[クイックスタート: アクセス トークンを作成して管理する](../../access-tokens.md)」を参照してください。

## <a name="object-model"></a>オブジェクト モデル 

JavaScript 用 Azure Communication Services Chat SDK が備える主な機能のいくつかは、以下のクラスとインターフェイスにより処理されます。

| 名前                                   | 説明                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ChatClient` | このクラスはチャットの機能に必要です。 サブスクリプション情報を使用してインスタンス化し、それを使用してスレッドを作成、取得、削除します。 |
| `ChatThreadClient` | このクラスはチャット スレッド機能に必要です。 `ChatClient` を介してインスタンスを取得し、それを使用して、メッセージの送信、受信、更新、および削除を行います。 また、これを使用して、ユーザーの追加、削除、取得、入力通知と開封確認メッセージの送信、およびチャット イベントのサブスクライブを行うこともできます。 |

## <a name="start-a-chat-thread"></a>チャット スレッドを開始する

`ChatClient` を使用して、最初のユーザーで新しいスレッドを作成します。

`<CREATE A CHAT THREAD>` コメントを、次のコードに置き換えます。

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

`<USER_ID>` は、有効な Communication Services のユーザー ID に置き換えます。

ここで、セマフォを使用して、処理を続行する前に完了ハンドラーを待機します。 後続の手順で、完了ハンドラーに返された応答の `threadId` を使用します。

## <a name="list-all-chat-threads"></a>すべてのチャット スレッドを一覧表示する

チャット スレッドを作成した後、`ChatClient` の `listChatThreads` メソッドを呼び出すことで、すべてのチャット スレッドを一覧表示できます。 `<LIST ALL CHAT THREADS>` コメントを、次のコードに置き換えます。

```
chatClient.listThreads { result, _ in
    switch result {
    case let .success(chatThreadItems):
        var iterator = chatThreadItems.syncIterator
            while let chatThreadItem = iterator.next() {
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

チャット スレッドを作成したので、そのスレッド内で操作を実行するために `ChatThreadClient` を取得できます。

`<GET A CHAT THREAD CLIENT>` コメントを、次のコードに置き換えます。

```
let chatThreadClient = try chatClient.createClient(forThread: threadId!)
```

## <a name="send-a-message-to-a-chat-thread"></a>チャット スレッドにメッセージを送信する

`<SEND A MESSAGE>` コメントを、次のコードに置き換えます。

```
let message = SendChatMessageRequest(
    content: "Hello!",
    senderDisplayName: "Jack"
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

まず、コンテンツと送信者の表示名を含む `SendChatMessageRequest` を作成します。 必要に応じて、この要求に共有履歴時刻を含めることもできます。 完了ハンドラーに返された応答には、送信されたメッセージの ID が含まれています。


## <a name="send-a-read-receipt"></a>開封確認メッセージを送信する

`ChatThreadClients` で `sendReadReceipt` メソッドを呼び出すことにより、特定のメッセージに対する開封確認メッセージを送信できます。 `<SEND A READ RECEIPT>` コメントを、次のコードに置き換えます。

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

`ChatThreadClient` から `listMessages()` メソッドを呼び出すことにより、チャット スレッドからメッセージを受信できます。 一覧表示されるメッセージには、システム メッセージとユーザーが送信したメッセージが含まれます。 受信できるメッセージの種類の詳細については、「[メッセージの種類](https://docs.microsoft.com/azure/communication-services/concepts/chat/concepts#message-types)」を参照してください

`<RECEIVE MESSAGES>` コメントを、次のコードに置き換えます。

```
chatThreadClient.listMessages { result, _ in
    switch result {
    case let .success(messages):
        var iterator = messages.syncIterator
        while let message = iterator.next() {
            print("Received message of type \(message.type)")
        }

    case .failure:
        print("Failed to receive messages")
    }
    semaphore.signal()
}
semaphore.wait()
```

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>チャット スレッドに参加者としてユーザーを追加する

`<ADD A USER>` コメントを、次のコードに置き換えます。

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

`<USER_ID>` を、追加するユーザーの Communication Services ユーザー ID に置き換えます。

スレッドに参加者を追加する際に、返された応答にエラーが含まれる場合があります。 これらのエラーは、特定の参加者を追加できなかったことを表します。

## <a name="list-users-in-a-thread"></a>スレッド内のユーザーを一覧表示する

コメント `<LIST USERS>` を、次のコードに置き換えます。

```
chatThreadClient.listParticipants { result, _ in
    switch result {
    case let .success(participants):
        var iterator = participants.syncIterator
        while let participant = iterator.next() {
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

