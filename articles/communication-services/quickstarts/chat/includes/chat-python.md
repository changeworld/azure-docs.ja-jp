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
ms.openlocfilehash: 3c05596e16edc5243b8a97002a5cc5990c69ec43
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2020
ms.locfileid: "90945623"
---
## <a name="prerequisites"></a>前提条件
開始する前に、必ず次のことを行ってください。

- アクティブなサブスクリプションがある Azure アカウントを作成します。 詳細については、[アカウントの無料作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)に関するページを参照してください。 
- [Python](https://www.python.org/downloads/) のインストール
- Azure Communication Services リソースを作成します。 詳細については、[Azure Communication リソースの作成](../../create-communication-resource.md)に関するページを参照してください。 このクイックスタート用に、自分のリソースの**エンドポイント**を記録する必要があります
- [ユーザー アクセス トークン](../../access-tokens.md)。 スコープは必ず "chat" に設定し、トークン文字列と userId 文字列をメモしてください。

## <a name="setting-up"></a>設定

### <a name="create-a-new-python-application"></a>新しい Python アプリケーションを作成する

ターミナルまたはコマンド ウィンドウを開き、自分のアプリ用に新しいディレクトリを作成し、そこに移動します。

```console
mkdir chat-quickstart && cd chat-quickstart
```

テキスト エディターを使用して、**start-chat.py** という名前のファイルをプロジェクトのルート ディレクトリに作成し、基本的な例外処理など、プログラムの構造を追加します。 このクイックスタートのソース コードはすべて、以降のセクションでこのファイルに追加することになります。

```python
import os
# Add required client library components from quickstart here

try:
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-client-library"></a>クライアント ライブラリをインストールする

```console

pip install azure-communication-chat

```

## <a name="object-model"></a>オブジェクト モデル

Python 用 Azure Communication Services チャット クライアント ライブラリが備える主な機能のいくつかは、以下のクラスとインターフェイスにより処理されます。

| 名前                                  | 説明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | このクラスはチャットの機能に必要です。 サブスクリプション情報を使用してインスタンス化し、それを使用してスレッドを作成、取得、削除します。 |
| ChatThreadClient | このクラスはチャット スレッド機能に必要です。 ChatClient を介してインスタンスを取得し、それを使用して、メッセージの送信、受信、更新、削除、ユーザーの追加、削除、取得、入力通知の送信、開封確認を行います。 |

## <a name="create-a-chat-client"></a>チャット クライアントを作成する

チャット クライアントを作成するには、Communications Service エンドポイントと、前提条件の手順で生成された `Access Token` を使用します。 詳細については、[ユーザー アクセス トークン](../../access-tokens.md)に関するページを参照してください。

```console
pip install azure-communication-administration
```

```python
from azure.communication.chat import ChatClient, CommunicationUserCredential

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
chat_client = ChatClient(endpoint, CommunicationUserCredential(<Access Token>))
```

## <a name="start-a-chat-thread"></a>チャット スレッドを開始する

チャット スレッドは、`create_chat_thread` メソッドを使用して作成します。

- スレッドにトピックを指定するには、`topic` を使用します。チャット スレッドの作成後に、`update_thread` 関数を使用してトピックを更新することができます。
- チャット スレッドに追加する `ChatThreadMember` をリストアップするには、`members` を使用します。`ChatThreadMember` は、[ユーザーを作成](../../access-tokens.md#create-a-user)することによって得られる `CommunicationUser` 型を `user` として受け取ります。

新しく作成されたチャット スレッドに対して操作 (チャット スレッドへのメンバーの追加、メッセージの送信、メッセージの削除など) を実行するには、応答 `chat_thread_client` を使用します。これには、チャット スレッドの一意の ID である `thread_id` プロパティが含まれています。

```python
from datetime import datetime
from azure.communication.chat import ChatThreadMember

topic="test topic"
thread_members=[ChatThreadMember(
    user=user,
    display_name='name',
    share_history_time=datetime.utcnow()
)]
chat_thread_client = chat_client.create_chat_thread(topic, thread_members)
```

## <a name="get-a-chat-thread-client"></a>チャット スレッド クライアントを取得する
get_chat_thread_client メソッドは、既に存在するスレッドのスレッド クライアントを返します。 作成したスレッドに対し、メンバーの追加、メッセージの送信などの操作を実行するために使用できます。thread_id は、既存のチャット スレッドの一意の ID です。

```python
thread_id = 'id'
chat_thread_client = chat_client.get_chat_thread_client(thread_id)
```

## <a name="send-a-message-to-a-chat-thread"></a>チャット スレッドにメッセージを送信する

作成したチャット スレッド (threadId で識別されます) にメッセージを送信するには、`send_message` メソッドを使用します。

- チャット メッセージの内容は、`content` を使用して設定します。
- "Normal (標準)" や "High (高)" など、メッセージの優先度を指定するには `priority` を使用します。このプロパティを使用して、自分のアプリ内の受信ユーザーにメッセージへの注意を促す UI インジケーターを表示したり、カスタム ビジネス ロジックを実行したりすることができます。
- 送信者の表示名を指定するには、`senderDisplayName` を使用します。

応答である `SendChatMessageResult` には、"id" (そのメッセージの一意の ID) が含まれています。

```python
from azure.communication.chat import ChatMessagePriority

content='hello world'
priority=ChatMessagePriority.NORMAL
sender_display_name='sender name'

send_message_result = chat_thread_client.send_message(content, priority=priority, sender_display_name=sender_display_name)
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>チャット スレッドからチャット メッセージを受信する

チャット メッセージは、特定の間隔で `list_messages` メソッドをポーリングすることによって取得できます。

```python
chat_messages = chat_thread_client.list_messages()
```
`list_messages` は、メッセージに対して `update_message` や `delete_message` を使用して行われた編集や削除を含む、最新バージョンのメッセージを返します。 削除されたメッセージについては、そのメッセージがいつ削除されたかを示す datetime 値が `ChatMessage.deleted_on` から返されます。 編集されたメッセージについては、メッセージがいつ編集されたかを示す datetime が `ChatMessage.edited_on` から返されます。 メッセージの最初の作成日時には、`ChatMessage.created_on` を使用してアクセスできます。これをメッセージの並べ替えに使用することができます。

`list_messages` は、`ChatMessage.type` で識別できるさまざまな種類のメッセージを返します。 これらの種類があります。

- `Text`:スレッド メンバーによって送信された通常のチャット メッセージ。

- `ThreadActivity/TopicUpdate`:トピックが更新されたことを示すシステム メッセージ。

- `ThreadActivity/AddMember`:1 つまたは複数のメンバーがチャット スレッドに追加されたことを示すシステム メッセージ。

- `ThreadActivity/DeleteMember`:メンバーがチャット スレッドから削除されたことを示すシステム メッセージ。

詳細については、「[メッセージの種類](../../../concepts/chat/concepts.md#message-types)」を参照してください。

## <a name="add-a-user-as-member-to-the-chat-thread"></a>チャット スレッドのメンバーとしてユーザーを追加する

チャット スレッドの作成後、そこにユーザーを追加したり削除したりすることができます。 追加したユーザーには、チャット スレッドにメッセージを送信したり、他のメンバーを追加、削除したりできるアクセス権が与えられます。 `add_members` メソッドを呼び出す前に必ず、そのユーザーの新しいアクセス トークンと ID を取得しておいてください。 チャット クライアントを初期化するためには、ユーザーにアクセス トークンが必要となります。

threadId で識別されるスレッドにメンバーを追加するには、`add_members` メソッドを使用します。

- チャット スレッドに追加するメンバーをリストアップするには、`members` を使用します。
- `user` (必須) は、[ユーザーの作成](../../access-tokens.md#create-a-user)時に `CommunicationIdentityClient` で作成した `CommunicationUser` です。
- `display_name` (省略可) は、スレッド メンバーの表示名です。
- `share_history_time` (省略可) は、メンバーとの間でチャット履歴が共有される際の起点となる時刻です。 チャット スレッドの始めから履歴を共有する場合は、スレッドの作成日時と同じかそれ以前の任意の日付にこのプロパティを設定してください。 メンバーが追加された時点よりも前の履歴は共有しない場合は、現在の日付に設定します。 履歴を部分的に共有するには、中間の日付に設定します。

```python
new_user = identity_client.create_user()

from azure.communication.chat import ChatThreadMember
from datetime import datetime
member = ChatThreadMember(
    user=new_user,
    display_name='name',
    share_history_time=datetime.utcnow())
thread_members = [member]
chat_thread_client.add_members(thread_members)
```

## <a name="remove-user-from-a-chat-thread"></a>チャット スレッドからユーザーを削除する

メンバーの追加と同様、スレッドからメンバーを削除することもできます。 削除するには、追加したメンバーの ID を追跡する必要があります。

threadId で識別されるスレッドからメンバーを削除するには、`remove_member` メソッドを使用します。
- `user` は、スレッドから削除する CommunicationUser です。

```python
chat_thread_client.remove_member(user)
```

## <a name="run-the-code"></a>コードの実行

アプリケーション ディレクトリから `python` コマンドを使用してアプリケーションを実行します。

```console
python start-chat.py
```
