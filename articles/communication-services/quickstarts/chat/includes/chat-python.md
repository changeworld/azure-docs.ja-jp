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
ms.openlocfilehash: b22cee560284a5a185e6b3ba560531d43d37ca67
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2021
ms.locfileid: "122967930"
---
## <a name="sample-code"></a>サンプル コード
このクイックスタートの最終的なコードは [GitHub](https://github.com/Azure-Samples/communication-services-python-quickstarts/tree/main/add-chat) にあります。

## <a name="prerequisites"></a>前提条件
開始する前に、必ず次のことを行ってください。

- アクティブなサブスクリプションがある Azure アカウントを作成します。 詳細については、[アカウントの無料作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)に関するページを参照してください。
- [Python](https://www.python.org/downloads/) をインストールします。
- Azure Communication Services リソースを作成します。 詳細については、「[クイックスタート: Communication Services のリソースを作成して管理する](../../create-communication-resource.md)」を参照してください。 このクイックスタート用に、自分のリソースのエンドポイントを記録する必要があります。
- [ユーザー アクセス トークン](../../access-tokens.md)。 スコープは必ず `chat` に設定し、`token` 文字列と `userId` 文字列をメモしてください。

## <a name="setting-up"></a>設定

### <a name="create-a-new-python-application"></a>新しい Python アプリケーションを作成する

ターミナルまたはコマンド ウィンドウを開き、自分のアプリ用に新しいディレクトリを作成し、そこに移動します。

```console
mkdir chat-quickstart && cd chat-quickstart
```

テキスト エディターを使用して、プロジェクトのルート ディレクトリに *start-chat.py* というファイルを作成します。 基本的な例外処理を含め、プログラムの構造を追加します。 以降のセクションで、このクイックスタートのすべてのソース コードをこのファイルに追加していきます。

```python
import os
# Add required SDK components from quickstart here

try:
    print('Azure Communication Services - Chat Quickstart')
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-sdk"></a>SDK をインストールする

次のコマンドを使用して SDK をインストールします。

```console

pip install azure-communication-chat

```

## <a name="object-model"></a>オブジェクト モデル

Python 用 Azure Communication Services Chat SDK が備える主な機能のいくつかは、以下のクラスとインターフェイスにより処理されます。

| 名前                                  | 説明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| `ChatClient` | このクラスはチャットの機能に必要です。 サブスクリプション情報を使用してインスタンス化し、それを使用してスレッドを作成、取得、削除します。 |
| `ChatThreadClient` | このクラスはチャット スレッド機能に必要です。 `ChatClient` を介してインスタンスを取得し、それを使用して、メッセージの送信、受信、更新、および削除を行います。 また、これを使用して、ユーザーの追加、削除、取得、および入力通知と開封確認メッセージの送信を行うこともできます。 |

## <a name="create-a-chat-client"></a>チャット クライアントを作成する

チャット クライアントを作成するには、Communication Services エンドポイントと、前提条件の手順の一部として生成したアクセス トークンを使用します。

```console
pip install azure-communication-identity
```

```python
from azure.communication.chat import ChatClient, CommunicationTokenCredential

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
chat_client = ChatClient(endpoint, CommunicationTokenCredential("<Access Token>"))
```
このクイックスタートでは、チャット アプリケーションのトークンを管理するためのサービス レベルの作成については説明しませんが、そうすることをお勧めします。 詳細については、「[チャットに関する概念](../../../concepts/chat/concepts.md)」の「チャットのアーキテクチャ」セクションを参照してください。

## <a name="start-a-chat-thread"></a>チャット スレッドを開始する

チャット スレッドは、`create_chat_thread` メソッドを使用して作成します。

- スレッドにトピックを提供するには、`topic` を使用します。 チャット スレッドが作成された後は、`update_thread` 関数を使用してトピックを更新できます。
- チャット スレッドに追加する `ChatParticipant` をリストアップするには、`thread_participants` を使用します。 `ChatParticipant` は、`CommunicationUserIdentifier` 型を `user` として受け取ります。

`CreateChatThreadResult` は、スレッドの作成から返される結果です。 これを使用して、作成されたチャット スレッドの `id` をフェッチできます。 その後、この `id` は、`get_chat_thread_client` メソッドを使用して `ChatThreadClient` オブジェクトをフェッチするために使用できます。 `ChatThreadClient` は、このチャット スレッドに対して他のチャット操作を実行するために使用できます。

```python
topic="test topic"

create_chat_thread_result = chat_client.create_chat_thread(topic)
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)
```

## <a name="get-a-chat-thread-client"></a>チャット スレッド クライアントを取得する

`get_chat_thread_client` メソッドは、既に存在するスレッドのスレッド クライアントを返します。 これを使用して、作成されたスレッドに対して操作を実行できます。 たとえば、参加者を追加したり、メッセージを送信したりできます。 `thread_id` は、既存のチャット スレッドの一意の ID です。

`ChatThreadClient` は、このチャット スレッドに対して他のチャット操作を実行するために使用できます。

```python
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(thread_id)
```

## <a name="list-all-chat-threads"></a>すべてのチャット スレッドを一覧表示する

`list_chat_threads` メソッドは、`ChatThreadItem` 型の反復子を返します。

- チャット スレッドを取得する最も早い時点を指定するには、`start_time` を使用します。
- ページごとに返されるチャット スレッドの最大数を指定するには、`results_per_page` を使用します。

`[ChatThreadItem]` の反復子は、スレッドの一覧表示から返される応答です。

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=2)

chat_threads = chat_client.list_chat_threads(results_per_page=5, start_time=start_time)
for chat_thread_item_page in chat_threads.by_page():
    for chat_thread_item in chat_thread_item_page:
        print(chat_thread_item)
        print('Chat Thread Id: ', chat_thread_item.id)
```


## <a name="send-a-message-to-a-chat-thread"></a>チャット スレッドにメッセージを送信する

作成したチャット スレッド (`thread_id` で識別されます) にメッセージを送信するには、`send_message` メソッドを使用します。

- チャット メッセージの内容は、`content` を使用して設定します。
- メッセージのコンテンツの種類を指定するには、`chat_message_type` を使用します。 設定可能な値は `text` および `html` です。 値を指定しない場合、既定値は `text` になります。
- 送信者の表示名を指定するには、`sender_display_name` を使用します。
- メッセージと共に送信する追加データを含めるには、必要に応じて `metadata` を使用します。 このフィールドは、開発者がチャット メッセージ機能を拡張し、ユース ケースに応じたカスタム情報を追加するためのメカニズムを提供します。 たとえば、メッセージ内でファイル リンクを共有する場合、'hasAttachment:true' をメタデータに追加することで、受信者のアプリケーションがそれを解析して適切に表示できます。

`SendChatMessageResult` は、メッセージの送信から返される応答です。 これには、ID (そのメッセージの一意の ID) が含まれています。

```python
from azure.communication.chat import ChatMessageType

topic = "test topic"
create_chat_thread_result = chat_client.create_chat_thread(topic)
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)


content='Please take a look at the attachment'
sender_display_name='sender name'
metadata={
    'hasAttachment': 'true',
    'attachmentUrl': 'https://contoso.com/files/attachment.docx'
}

# specify chat message type with pre-built enumerations
send_message_result_w_enum = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name, chat_message_type=ChatMessageType.TEXT, metadata=metadata)
print("Message sent: id: ", send_message_result_w_enum.id)
```


## <a name="receive-chat-messages-from-a-chat-thread"></a>チャット スレッドからチャット メッセージを受信する

チャット メッセージは、特定の間隔で `list_messages` メソッドをポーリングすることによって取得できます。

- ページごとに返されるメッセージの最大数を指定するには、`results_per_page` を使用します。
- メッセージを取得する最も早い時点を指定するには、`start_time` を使用します。

`[ChatMessage]` の反復子は、メッセージの一覧表示から返される応答です。

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=1)

chat_messages = chat_thread_client.list_messages(results_per_page=1, start_time=start_time)
for chat_message_page in chat_messages.by_page():
    for chat_message in chat_message_page:
        print("ChatMessage: Id=", chat_message.id, "; Content=", chat_message.content.message)
```

`list_messages` は、メッセージに対して `update_message` や `delete_message` を使用して行われた編集や削除を含む、メッセージの最新バージョンを返します。 削除されたメッセージについては、そのメッセージがいつ削除されたかを示す `datetime` 値が `ChatMessage.deleted_on` から返されます。 編集されたメッセージについては、そのメッセージがいつ編集されたかを示す `datetime` 値が `ChatMessage.edited_on` から返されます。 メッセージの最初の作成日時には、`ChatMessage.created_on` を使用してアクセスできます。これをメッセージの並べ替えに使用することができます。

`list_messages` は、`ChatMessage.type` で識別できるさまざまな種類のメッセージを返します。 

詳細については、「[メッセージの種類](../../../concepts/chat/concepts.md#message-types)」を参照してください。

## <a name="send-read-receipt"></a>開封確認メッセージを送信する

ユーザーに代わって開封確認イベントをスレッドに送信するには、`send_read_receipt` メソッドを使用します。

- 現在のユーザーに読まれた最新のメッセージの ID を指定するには、`message_id` を使用します。

```python
content='hello world'

send_message_result = chat_thread_client.send_message(content)
chat_thread_client.send_read_receipt(message_id=send_message_result.id)
```


## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>チャット スレッドに参加者としてユーザーを追加する

チャット スレッドを作成した後、そこにユーザーを追加したり削除したりすることができます。 追加したユーザーには、チャット スレッドにメッセージを送信したり、他の参加者を追加または削除したりできるアクセス権が与えられます。 `add_participants` メソッドを呼び出す前に必ず、そのユーザーの新しいアクセス トークンと ID を取得しておいてください。 このアクセス トークンは、ユーザーがチャット クライアントを初期化するために必要になります。

`add_participants` メソッドを使用して、チャット スレッドに 1 人以上のユーザーを追加することができます (すべてのユーザーが新しいアクセス トークンと ID を使用できる場合)。

`list(tuple(ChatParticipant, CommunicationError))` が返される。 参加者が正常に追加された場合は、空の一覧が予期されます。 参加者の追加中にエラーが発生した場合は、失敗した参加者と、発生したエラーが一覧に設定されます。

```python
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.chat import ChatParticipant
from datetime import datetime

# create 2 users
identity_client = CommunicationIdentityClient.from_connection_string('<connection_string>')
new_users = [identity_client.create_user() for i in range(2)]

# # conversely, you can also add an existing user to a chat thread; provided the user_id is known
# from azure.communication.identity import CommunicationUserIdentifier
#
# user_id = 'some user id'
# user_display_name = "Wilma Flinstone"
# new_user = CommunicationUserIdentifier(user_id)
# participant = ChatParticipant(
#     identifier=new_user,
#     display_name=user_display_name,
#     share_history_time=datetime.utcnow())

participants = []
for _user in new_users:
  chat_thread_participant = ChatParticipant(
    identifier=_user,
    display_name='Fred Flinstone',
    share_history_time=datetime.utcnow()
  ) 
  participants.append(chat_thread_participant) 

response = chat_thread_client.add_participants(participants)

def decide_to_retry(error, **kwargs):
    """
    Insert some custom logic to decide if retry is applicable based on error
    """
    return True

# verify if all users has been successfully added or not
# in case of partial failures, you can retry to add all the failed participants 
retry = [p for p, e in response if decide_to_retry(e)]
if retry:
    chat_thread_client.add_participants(retry)
```


## <a name="list-thread-participants-in-a-chat-thread"></a>チャット スレッドのスレッド参加者の一覧を取得する

参加者の追加と同様、スレッドから参加者の一覧を取得することもできます。

スレッドの参加者を取得するには、`list_participants` を使用します。 次のコマンドはいずれも省略可能です。

- ページごとに返される参加者の最大数を指定するには、`results_per_page` を使用します。
- 応答内の指定した位置まで参加者をスキップするには、`skip` を使用します。

`[ChatParticipant]` の反復子は、参加者の一覧表示から返される応答です。

```python
chat_thread_participants = chat_thread_client.list_participants()
for chat_thread_participant_page in chat_thread_participants.by_page():
    for chat_thread_participant in chat_thread_participant_page:
        print("ChatParticipant: ", chat_thread_participant)
```

## <a name="run-the-code"></a>コードの実行

アプリケーション ディレクトリから `python` コマンドを使用してアプリケーションを実行します。

```console
python start-chat.py
```
