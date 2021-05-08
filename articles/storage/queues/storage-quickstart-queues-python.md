---
title: 'クイックスタート: Azure Queue Storage クライアント ライブラリ v12 - Python'
description: Python 用 Azure Queue Storage クライアント ライブラリ v12 を使用して、キューを作成し、そこにメッセージを追加する方法について説明します。 次に、キューからメッセージを読み取って削除する方法について説明します。 キューを削除する方法についても説明します。
author: twooley
ms.author: twooley
ms.date: 12/10/2019
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom:
- devx-track-python
- mode-api
ms.openlocfilehash: 68f68c32b160757e82d59b1dd5ee4847a06ec698
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534352"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-python"></a>クイックスタート: Python 用 Azure Queue Storage クライアント ライブラリ v12

Python 用 Azure Queue Storage クライアント ライブラリ v12 を使用してみましょう。 Azure Queue Storage は、後で取得して処理するために多数のメッセージを格納するためのサービスです。 以下の手順に従って、パッケージをインストールし、基本タスクのコード例を試してみましょう。

Python 用 Azure Queue Storage クライアント ライブラリ v12 を使用すると、以下のことができます。

- キューを作成する
- メッセージをキューに追加する
- キュー内のメッセージを表示する
- キュー内のメッセージを更新する
- キューからメッセージを受信する
- キューからメッセージを削除する
- キューを削除する

その他のリソース:

- [API リファレンス ドキュメント](/python/api/azure-storage-queue/index)
- [ライブラリ ソース コード](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue)
- [パッケージ (Python Package Index)](https://pypi.org/project/azure-storage-queue/)
- [サンプル](../common/storage-samples-python.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)
- Azure Storage アカウント - [ストレージ アカウントを作成する](../common/storage-account-create.md)
- [オペレーティング システムには Python](https://www.python.org/downloads/) - 2.7 または 3.6 以上

## <a name="setting-up"></a>設定

このセクションでは、Python 用 Azure Queue Storage クライアント ライブラリ v12 を操作するためのプロジェクトの準備について説明します。

### <a name="create-the-project"></a>プロジェクトを作成する

`queues-quickstart-v12` という名前の Python アプリケーションを作成します。

1. コンソール ウィンドウ (cmd、PowerShell、Bash など) で、プロジェクト用に新しいディレクトリを作成します。

    ```console
    mkdir queues-quickstart-v12
    ```

1. 新しく作成した `queues-quickstart-v12` ディレクトリに切り替えます。

    ```console
    cd queues-quickstart-v12
    ```

### <a name="install-the-package"></a>パッケージをインストールする

`pip install` コマンドを使用して、Python 用 Azure Blob Storage クライアント ライブラリ パッケージをインストールします。

```console
pip install azure-storage-queue
```

このコマンドにより、Python 用 Azure Queue Storage クライアント ライブラリ パッケージと、それが依存しているすべてのライブラリがインストールされます。 この場合は、Python 用の Azure コア ライブラリだけです。

### <a name="set-up-the-app-framework"></a>アプリのフレームワークを設定する

1. コード エディターで新しいテキスト ファイルを開きます
1. `import` ステートメントを追加します
1. 極めて基本的な例外処理を含め、プログラムの構造を作成します

    コードは次のとおりです。

    ```python
    import os, uuid
    from azure.storage.queue import QueueServiceClient, QueueClient, QueueMessage

    try:
        print("Azure Queue storage v12 - Python quickstart sample")
        # Quick start code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)

    ```

1. `queues-quickstart-v12` ディレクトリに新しいファイルを `queues-quickstart-v12.py` として保存します。

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>オブジェクト モデル

Azure Queue storage は、多数のメッセージを格納するためのサービスです。 キュー メッセージの許容される最大サイズは 64 KB です。 キューには、ストレージ アカウントの総容量の上限を超えない限り、数百万のメッセージを含めることができます。 キューは通常、非同期的な処理用に作業のバックログを作成するために使用されます。 Queue Storage には、次の 3 種類のリソースがあります。

- ストレージ アカウント
- ストレージ アカウント内のキュー
- キュー内のメッセージ

次の図に、これらのリソースの関係を示します。

![Queue storage のアーキテクチャ図](./media/storage-queues-introduction/queue1.png)

これらのリソースとやり取りするには、以下の Python クラスを使用します。

- [`QueueServiceClient`](/python/api/azure-storage-queue/azure.storage.queue.queueserviceclient): `QueueServiceClient` を使用すると、ストレージ アカウント内のすべてのキューを管理できます。
- [`QueueClient`](/python/api/azure-storage-queue/azure.storage.queue.queueclient): `QueueClient` クラスを使用すると、個々のキューとそのメッセージを管理および操作できます。
- [`QueueMessage`](/python/api/azure-storage-queue/azure.storage.queue.queuemessage): `QueueMessage` クラスは、キューに対して [`receive_messages`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) を呼び出したときに返される個々のオブジェクトを表します。

## <a name="code-examples"></a>コード例

以下のサンプル コード スニペットは、Python 用 Azure Queue Storage クライアント ライブラリを使用して以下の操作を実行する方法を示します。

- [接続文字列を取得する](#get-the-connection-string)
- [キューを作成する](#create-a-queue)
- [メッセージをキューに追加する](#add-messages-to-a-queue)
- [キュー内のメッセージを表示する](#peek-at-messages-in-a-queue)
- [キュー内のメッセージを更新する](#update-a-message-in-a-queue)
- [キューからメッセージを受信する](#receive-messages-from-a-queue)
- [キューからメッセージを削除する](#delete-messages-from-a-queue)
- [キューを削除する](#delete-a-queue)

### <a name="get-the-connection-string"></a>接続文字列を取得する

以下のコードは、ストレージ アカウントの接続文字列を取得します。 この接続文字列は、「[ストレージ接続文字列の構成](#configure-your-storage-connection-string)」セクションで作成した環境変数に格納されます。

このコードを `try` ブロック内に追加します。

```python
    # Retrieve the connection string for use with the application. The storage
    # connection string is stored in an environment variable on the machine
    # running the application called AZURE_STORAGE_CONNECTION_STRING. If the
    # environment variable is created after the application is launched in a
    # console or with Visual Studio, the shell or application needs to be
    # closed and reloaded to take the environment variable into account.
    connect_str = os.getenv('AZURE_STORAGE_CONNECTION_STRING')
```

### <a name="create-a-queue"></a>キューを作成する

新しいキューの名前を決定します。 次のコードは、キュー名に UUID 値を追加して、確実に一意になるようにします。

> [!IMPORTANT]
> キュー名に使用できるのは小文字、数字、ハイフンのみであり、名前の先頭は文字または数字にする必要があります。 各ハイフンの前後にはハイフン以外の文字を指定する必要があります。 また、名前は 3 から 63 文字で指定する必要があります。 詳細については、「[キューおよびメタデータの名前付け](/rest/api/storageservices/naming-queues-and-metadata)」を参照してください。

[`QueueClient`](/python/api/azure-storage-queue/azure.storage.queue.queueclient) クラスのインスタンスを作成します。 次に、[`create_queue`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#create-queue---kwargs-) メソッドを呼び出して、ストレージ アカウントにキューを作成します。

`try` ブロックの末尾に、次のコードを追加します。

```python
    # Create a unique name for the queue
    queue_name = "quickstartqueues-" + str(uuid.uuid4())

    print("Creating queue: " + queue_name)

    # Instantiate a QueueClient which will be
    # used to create and manipulate the queue
    queue_client = QueueClient.from_connection_string(connect_str, queue_name)

    # Create the queue
    queue_client.create_queue()
```

### <a name="add-messages-to-a-queue"></a>メッセージをキューに追加する

以下のコード スニペットは、[`send_message`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#send-message-content----kwargs-) メソッドを呼び出してキューにメッセージを追加します。 また、3 番目の `send_message` 呼び出しで返された [`QueueMessage`](/python/api/azure-storage-queue/azure.storage.queue.queuemessage) を保存します。 `saved_message` は、後でプログラムの中でメッセージの内容を更新する際に使用します。

`try` ブロックの末尾に、次のコードを追加します。

```python
    print("\nAdding messages to the queue...")

    # Send several messages to the queue
    queue_client.send_message(u"First message")
    queue_client.send_message(u"Second message")
    saved_message = queue_client.send_message(u"Third message")
```

### <a name="peek-at-messages-in-a-queue"></a>キュー内のメッセージを表示する

キュー内のメッセージをクイック表示するには、[`peek_messages`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#peek-messages-max-messages-none----kwargs-) メソッドを呼び出します。 このメソッドは、キューの先頭からメッセージを 1 つ以上取得しますが、メッセージの可視性は変更しません。

`try` ブロックの末尾に、次のコードを追加します。

```python
    print("\nPeek at the messages in the queue...")

    # Peek at messages in the queue
    peeked_messages = queue_client.peek_messages(max_messages=5)

    for peeked_message in peeked_messages:
        # Display the message
        print("Message: " + peeked_message.content)
```

### <a name="update-a-message-in-a-queue"></a>キュー内のメッセージを更新する

メッセージの内容を更新するには、[`update_message`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#update-message-message--pop-receipt-none--content-none----kwargs-) メソッドを呼び出します。 メッセージの表示タイムアウトと内容は、このメソッドで変更できます。 メッセージの内容には UTF-8 でエンコードされた文字列を指定してください。最大サイズは 64 KB です。 先ほどこのコードの中で保存したメッセージの値を、新しい内容と共に渡します。 `saved_message` の値によって、更新するメッセージが識別されます。

```python
    print("\nUpdating the third message in the queue...")

    # Update a message using the message saved when calling send_message earlier
    queue_client.update_message(saved_message, pop_receipt=saved_message.pop_receipt, \
        content="Third message has been updated")
```

### <a name="receive-messages-from-a-queue"></a>キューからメッセージを受信する

[`receive_messages`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) メソッドを呼び出して、先ほど追加したメッセージをダウンロードします。

`try` ブロックの末尾に、次のコードを追加します。

```python
    print("\nReceiving messages from the queue...")

    # Get messages from the queue
    messages = queue_client.receive_messages(messages_per_page=5)
```

### <a name="delete-messages-from-a-queue"></a>キューからメッセージを削除する

メッセージは、受信して処理した後にキューから削除します。 ここでの処理は、単にメッセージをコンソールに表示するだけです。

ユーザーからの入力を待ってメッセージを処理、削除するために、`input` を呼び出してアプリを一時停止させます。 リソースが正しく作成されたことを [Azure portal](https://portal.azure.com) で確認してから、それらを削除してください。 明示的に削除されなかったメッセージは、最終的にキューに再表示され、別の機会に処理されることになります。

`try` ブロックの末尾に、次のコードを追加します。

```python
    print("\nPress Enter key to 'process' messages and delete them from the queue...")
    input()

    for msg_batch in messages.by_page():
            for msg in msg_batch:
                # "Process" the message
                print(msg.content)
                # Let the service know we're finished with
                # the message and it can be safely deleted.
                queue_client.delete_message(msg)
```

### <a name="delete-a-queue"></a>キューを削除する

次のコードでは、[`delete_queue`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#delete-queue---kwargs-) メソッドを使用してキューを削除することにより、アプリによって作成されたリソースがクリーンアップされます。

`try` ブロックの末尾に次のコードを追加してファイルを保存します。

```python
    print("\nPress Enter key to delete the queue...")
    input()

    # Clean up
    print("Deleting queue...")
    queue_client.delete_queue()

    print("Done")
```

## <a name="run-the-code"></a>コードの実行

このアプリは、3 つのメッセージを作成して Azure のキューに追加します。 コードでは、キュー内のメッセージを一覧表示した後にそれらを取得して削除してから、最後にキューを削除します。

コンソール ウィンドウで、`queues-quickstart-v12.py` ファイルが格納されているディレクトリに移動し、次の `python` コマンドを使用してアプリを実行します。

```console
python queues-quickstart-v12.py
```

アプリの出力は、次の例のようになります。

```output
Azure Queue Storage client library v12 - Python quickstart sample
Creating queue: quickstartqueues-cac365be-7ce6-4065-bd65-3756ea052cb8

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Receiving messages from the queue...

Press Enter key to 'process' messages and delete them from the queue...

First message
Second message
Third message has been updated

Press Enter key to delete the queue...

Deleting queue...
Done
```

メッセージを受信する前にアプリが一時停止したら、[Azure portal](https://portal.azure.com) でストレージ アカウントを確認してください。 キューにメッセージが存在することを確認します。

`Enter` キーを押してメッセージを受信し、削除します。 確認を求められたら、もう一度 `Enter` キーを押してキューを削除し、デモを終了します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Python コードを使用して、キューを作成してそこにメッセージを追加する方法について説明しました。 その後、メッセージの表示、取得、削除について説明しました。 最後に、メッセージ キューを削除する方法を説明しました。

チュートリアル、サンプル、クイック スタートなどのドキュメントについては、次のページを参照してください。

> [!div class="nextstepaction"]
> [Python 開発者向けの Azure](/azure/python/)

- 詳細については、「[Python 用 Azure Storage ライブラリ](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage)」を参照してください。
- その他の Azure Queue Storage サンプル アプリについては、「[Azure Queue Storage client library v12 for Python - samples (Python 用 Azure Queue Storage クライアント ライブラリ v12 - サンプル)](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue/samples)」を参照してください。
