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
ms.openlocfilehash: 0225c948fddf65b9312c689144ecc567a70aa27e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750288"
---
## <a name="prerequisites"></a>前提条件
開始する前に、必ず次のことを行ってください。

- アクティブなサブスクリプションがある Azure アカウントを作成します。 詳細については、[アカウントの無料作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)に関するページを参照してください。 
- [Python](https://www.python.org/downloads/) のインストール
- Azure Communication Services リソースを作成します。 詳細については、[Azure Communication リソースの作成](../../create-communication-resource.md)に関するページを参照してください。 このクイックスタート用に、自分のリソースの **エンドポイント** を記録する必要があります
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
    print('Azure Communication Services - Chat Quickstart')
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
| ChatClient | このクラスは、チャット機能に必要となります。 サブスクリプション情報を使用してインスタンス化し、それを使用してスレッドを作成、取得、削除します。 |
| ChatThreadClient | このクラスは、チャット スレッド機能に必要となります。 ChatClient を介してインスタンスを取得し、それを使用して、メッセージの送信、受信、更新、削除、ユーザーの追加、削除、取得、入力通知の送信、開封確認を行います。 |

## <a name="create-a-chat-client"></a>チャット クライアントを作成する

チャット クライアントを作成するには、Communications Service エンドポイントと、前提条件の手順で生成された `Access Token` を使用します。 詳細については、[ユーザー アクセス トークン](../../access-tokens.md)に関するページを参照してください。

このクイックスタートでは、チャット アプリケーションのトークンを管理するためのサービス レベルの作成については説明しませんが、サービス レベルの使用をお勧めします。 詳細については、[チャットのアーキテクチャ](../../../concepts/chat/concepts.md)に関するドキュメントを参照してください

```console
pip install azure-communication-administration
```

```python
from azure.communication.chat import ChatClient, CommunicationTokenCredential, CommunicationTokenRefreshOptions

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
refresh_options = CommunicationTokenRefreshOptions(<Access Token>)
chat_client = ChatClient(endpoint, CommunicationTokenCredential(refresh_options))
```

## <a name="start-a-chat-thread"></a>チャット スレッドを開始する

チャット スレッドは、`create_chat_thread` メソッドを使用して作成します。

- スレッドにトピックを指定するには、`topic` を使用します。チャット スレッドの作成後に、`update_thread` 関数を使用してトピックを更新することができます。
- チャット スレッドに追加する `ChatThreadParticipant` をリストアップするには、`thread_participants` を使用します。`ChatThreadParticipant` は、[ユーザーを作成](../../access-tokens.md#create-an-identity)することによって得られる `CommunicationUserIdentifier` 型を `user` として受け取ります。
- 要求が反復可能であることを指示するには、`repeatability_request_id` を使用します。 クライアントは、同じ Repeatability-Request-ID を使用して要求を複数回行い、サーバーで要求が複数回実行されなくても、適切な応答を取得できます。

新しく作成したチャット スレッドに対して操作 (チャット スレッドへの参加者の追加、メッセージの送信、メッセージの削除など) を実行するには、応答 `chat_thread_client` を使用します。これには、チャット スレッドの一意の ID である `thread_id` プロパティが含まれています。

#### <a name="without-repeatability-request-id"></a>Repeatability-Request-ID を使用しない
```python
from datetime import datetime
from azure.communication.chat import ChatThreadParticipant

topic="test topic"
participants = [ChatThreadParticipant(
    user=user,
    display_name='name',
    share_history_time=datetime.utcnow()
)]

chat_thread_client = chat_client.create_chat_thread(topic, participants)
```

#### <a name="with-repeatability-request-id"></a>Repeatability-Request-ID を使用する
```python
from datetime import datetime
from azure.communication.chat import ChatThreadParticipant

topic="test topic"
participants = [ChatThreadParticipant(
    user=user,
    display_name='name',
    share_history_time=datetime.utcnow()
)]

repeatability_request_id = 'b66d6031-fdcc-41df-8306-e524c9f226b8' # some unique identifier
chat_thread_client = chat_client.create_chat_thread(topic, participants, repeatability_request_id)
```

## <a name="get-a-chat-thread-client"></a>チャット スレッド クライアントを取得する
`get_chat_thread_client` メソッドは、既に存在するスレッドのスレッド クライアントを返します。 これは、作成したスレッドに対して操作 (参加者の追加、メッセージの送信など) を実行するために使用できます。thread_id は、既存のチャット スレッドの一意の ID です。

```python
thread_id = chat_thread_client.thread_id
chat_thread_client = chat_client.get_chat_thread_client(thread_id)
```

## <a name="list-all-chat-threads"></a>すべてのチャット スレッドを一覧表示する
`list_chat_threads` メソッドは、`ChatThreadInfo` タイプの反復子を返します。 これを使用すると、すべてのチャット スレッドを一覧表示できます。

- チャット スレッドを取得する最も早い時点を指定するには、`start_time` を使用します。
- ページごとに返されるチャット スレッドの最大数を指定するには、`results_per_page` を使用します。

```python
from datetime import datetime, timedelta
import pytz

start_time = datetime.utcnow() - timedelta(days=2)
start_time = start_time.replace(tzinfo=pytz.utc)
chat_thread_infos = chat_client.list_chat_threads(results_per_page=5, start_time=start_time)

for chat_thread_info_page in chat_thread_infos.by_page():
    for chat_thread_info in chat_thread_info_page:
        # Iterate over all chat threads
        print("thread id:", chat_thread_info.id)
```

## <a name="delete-a-chat-thread"></a>チャット スレッドを削除する
`delete_chat_thread` は、チャット スレッドを削除するために使用されます。

- 削除する必要がある既存のチャット スレッドの thread_id を指定するには、`thread_id` を使用します

```python
thread_id = chat_thread_client.thread_id
chat_client.delete_chat_thread(thread_id)
```

## <a name="send-a-message-to-a-chat-thread"></a>チャット スレッドにメッセージを送信する

先ほど作成したチャット スレッド (thread_id で識別されます) にメッセージを送信するには、`send_message` メソッドを使用します。

- チャット メッセージの内容は、`content` を使用して設定します。
- メッセージのコンテンツの種類を指定するには、`chat_message_type` を使用します。 指定できる値は、"text" と "html" です。指定しない場合は、既定値の "text" が割り当てられます。
- 送信者の表示名を指定するには、`sender_display_name` を使用します。

応答は、`str` 型の "id" (そのメッセージの一意の ID) です。

#### <a name="message-type-not-specified"></a>メッセージの種類が指定されていない
```python
chat_thread_client = chat_client.create_chat_thread(topic, participants)

content='hello world'
sender_display_name='sender name'

send_message_result_id = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name)
```

#### <a name="message-type-specified"></a>メッセージの種類が指定されている
```python
from azure.communication.chat import ChatMessageType

content='hello world'
sender_display_name='sender name'

# specify chat message type with pre-built enumerations
send_message_result_id_w_enum = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name, chat_message_type=ChatMessageType.TEXT)

# specify chat message type as string
send_message_result_id_w_str = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name, chat_message_type='text')
```

## <a name="get-a-specific-chat-message-from-a-chat-thread"></a>チャット スレッドから特定のチャット メッセージを取得する
`get_message` 関数を使用すると、message_id で識別される特定のメッセージを取得できます

- メッセージ ID を指定するには、`message_id` を使用します。

`ChatMessage` タイプの応答には、1 つのメッセージに関連するすべての情報が含まれています。

```python
message_id = send_message_result_id
chat_message = chat_thread_client.get_message(message_id)
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>チャット スレッドからチャット メッセージを受信する

チャット メッセージは、特定の間隔で `list_messages` メソッドをポーリングすることによって取得できます。

- ページごとに返されるメッセージの最大数を指定するには、`results_per_page` を使用します。
- メッセージを取得する最も早い時点を指定するには、`start_time` を使用します。

```python
chat_messages = chat_thread_client.list_messages(results_per_page=1, start_time=start_time)
for chat_message_page in chat_messages.by_page():
    for chat_message in chat_message_page:
        print('ChatMessage: ', chat_message)
        print('ChatMessage: ', chat_message.content.message)
```

`list_messages` は、メッセージに対して `update_message` や `delete_message` を使用して行われた編集や削除を含む、最新バージョンのメッセージを返します。 削除されたメッセージについては、そのメッセージがいつ削除されたかを示す datetime 値が `ChatMessage.deleted_on` から返されます。 編集されたメッセージについては、メッセージがいつ編集されたかを示す datetime が `ChatMessage.edited_on` から返されます。 メッセージの最初の作成日時には、`ChatMessage.created_on` を使用してアクセスできます。これをメッセージの並べ替えに使用することができます。

`list_messages` は、`ChatMessage.type` で識別できるさまざまな種類のメッセージを返します。 次の種類があります。

- `ChatMessageType.TEXT`:スレッド参加者によって送信された通常のチャット メッセージ。

- `ChatMessageType.HTML`: スレッド参加者によって送信された HTML チャット メッセージ。

- `ChatMessageType.TOPIC_UPDATED`:トピックが更新されたことを示すシステム メッセージ。

- `ChatMessageType.PARTICIPANT_ADDED`:1 人以上の参加者がチャット スレッドに追加されたことを示すシステム メッセージ。

- `ChatMessageType.PARTICIPANT_REMOVED`:参加者がチャット スレッドから削除されたことを示すシステム メッセージ。

詳細については、「[メッセージの種類](../../../concepts/chat/concepts.md#message-types)」を参照してください。

## <a name="update-topic-of-a-chat-thread"></a>チャット スレッドのトピックを更新する
`update_topic` メソッドを使用してチャット スレッドのトピックを更新できます

```python
topic = "updated thread topic"
chat_thread_client.update_topic(topic=topic)
updated_topic = chat_client.get_chat_thread(chat_thread_client.thread_id).topic
print('Updated topic: ', updated_topic)
```

## <a name="update-a-message"></a>メッセージを更新する
`update_message` メソッドを使用して、message_id で識別される既存のメッセージのコンテンツを更新できます

- message_id を指定するには、`message_id` を使用します
- メッセージの新しいコンテンツを設定するには、`content` を使用します

```python
content = 'Hello world!'
send_message_result_id = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name)

content = 'Hello! I am updated content'
chat_thread_client.update_message(message_id=send_message_result_id, content=content)

chat_message = chat_thread_client.get_message(send_message_result_id)
print('Updated message content: ', chat_message.content.message)
```

## <a name="send-read-receipt-for-a-message"></a>メッセージの開封確認メッセージを送信する
`send_read_receipt` メソッドを使用すると、ユーザーに代わって開封確認イベントをスレッドに送信できます。

- 現在のユーザーに読まれた最新のメッセージの ID を指定するには、`message_id` を使用します。

```python
message_id=send_message_result_id
chat_thread_client.send_read_receipt(message_id=message_id)
```

## <a name="list-read-receipts-for-a-chat-thread"></a>チャット スレッドの開封確認メッセージを一覧表示する
`list_read_receipts` メソッドを使用すると、スレッドの開封確認メッセージを取得できます。

- ページごとに返されるチャット メッセージの開封確認メッセージの最大数を指定するには、`results_per_page` を使用します。
- 応答内の指定された位置までチャット メッセージの開封確認メッセージをスキップするように指定するには、`skip` を使用します。

```python
read_receipts = chat_thread_client.list_read_receipts(results_per_page=2, skip=0)

for read_receipt_page in read_receipts.by_page():
    for read_receipt in read_receipt_page:
        print('ChatMessageReadReceipt: ', read_receipt)
```

## <a name="send-typing-notification"></a>入力通知を送信する
`send_typing_notification` メソッドを使用すると、ユーザーに代わって入力イベントをスレッドに送信できます。

```python
chat_thread_client.send_typing_notification()
```

## <a name="delete-message"></a>メッセージを削除する
`delete_message` メソッドを使用すると、message_id で識別されるメッセージを削除できます

- message_id を指定するには、`message_id` を使用します

```python
message_id=send_message_result_id
chat_thread_client.delete_message(message_id=message_id)
```

## <a name="add-a-user-as-participant-to-the-chat-thread"></a>チャット スレッドに参加者としてユーザーを追加する

チャット スレッドの作成後、ユーザーを追加したり削除したりすることができます。 追加したユーザーには、チャット スレッドにメッセージを送信したり、他の参加者を追加または削除したりできるアクセス権が与えられます。 `add_participant` メソッドを呼び出す前に必ず、そのユーザーの新しいアクセス トークンと ID を取得しておいてください。 チャット クライアントを初期化するためには、ユーザーにアクセス トークンが必要となります。

thread_id で識別されるスレッドにスレッド参加者を追加するには、`add_participant` メソッドを使用します。

- チャット スレッドに追加する参加者を指定するには、`thread_participant` を使用します。
- `user` (必須) は、[ユーザーの作成](../../access-tokens.md#create-an-identity)時に `CommunicationIdentityClient` で作成した `CommunicationUserIdentifier` です。
- `display_name` (省略可) は、スレッド参加者の表示名です。
- `share_history_time` (省略可) は、参加者との間でチャット履歴が共有される際の起点となる時刻です。 チャット スレッドの始めから履歴を共有する場合は、スレッドの作成日時と同じかそれ以前の任意の日付にこのプロパティを設定してください。 参加者が追加された時点よりも前の履歴は共有しない場合は、現在の日付に設定します。 履歴を部分的に共有するには、中間の日付に設定します。

```python
new_user = identity_client.create_user()

from azure.communication.chat import ChatThreadParticipant
from datetime import datetime

new_chat_thread_participant = ChatThreadParticipant(
    user=new_user,
    display_name='name',
    share_history_time=datetime.utcnow())

chat_thread_client.add_participant(new_chat_thread_participant)
```

`add_participants` メソッドを使用してチャット スレッドに複数のユーザーを追加することもでき ます (すべてのユーザーが新しいアクセス トークンと ID を使用できる場合)。

```python
from azure.communication.chat import ChatThreadParticipant
from datetime import datetime

new_chat_thread_participant = ChatThreadParticipant(
        user=self.new_user,
        display_name='name',
        share_history_time=datetime.utcnow())
thread_participants = [new_chat_thread_participant] # instead of passing a single participant, you can pass a list of participants
chat_thread_client.add_participants(thread_participants)
```


## <a name="remove-user-from-a-chat-thread"></a>チャット スレッドからユーザーを削除する

参加者の追加と同様、スレッドから参加者を削除することもできます。 削除するには、追加した参加者の ID を追跡する必要があります。

threadId で識別されるスレッドからスレッド参加者を削除するには、`remove_participant` メソッドを使用します。
- `user` は、スレッドから削除される `CommunicationUserIdentifier` です。

```python
chat_thread_client.remove_participant(new_user)
```

## <a name="run-the-code"></a>コードの実行

アプリケーション ディレクトリから `python` コマンドを使用してアプリケーションを実行します。

```console
python start-chat.py
```
