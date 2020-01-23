---
title: Python を使用してイベントを送受信する - Azure Event Hubs | Microsoft Docs
description: この記事では、Azure Event Hubs にイベントを送信する Python アプリケーションを作成するためのチュートリアルを提供します。
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 01/08/2020
ms.author: spelluru
ms.openlocfilehash: 11548ba180a2dd6541240431d670812448c502b8
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981578"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-using-python"></a>Python を使用して Event Hubs との間でイベントを送受信する

Azure Event Hubs はビッグ データ ストリーミング プラットフォームであり、毎秒数百万のイベントを受け取って処理できるイベント インジェスト サービスです。 Event Hubs では、分散されたソフトウェアやデバイスから生成されるイベント、データ、またはテレメトリを処理および格納できます。 イベント ハブに送信されたデータは、任意のリアルタイム分析プロバイダーやバッチ処理/ストレージ アダプターを使用して、変換および保存できます。 Event Hubs の詳しい概要については、[Event Hubs の概要](event-hubs-about.md)と [Event Hubs の機能](event-hubs-features.md)に関するページをご覧ください。

このチュートリアルでは、Python アプリケーションを作成し、イベント ハブとの間でイベントを送受信する方法について説明します。

> [!IMPORTANT]
> このクイックスタートでは、Azure Event Hubs Python SDK のバージョン 5 を使用します。 Python SDK の古いバージョン 1 を使用するクイック スタートについては、[この記事](event-hubs-python-get-started-send.md)を参照してください。 SDK のバージョン 1 を使用している場合は、コードを最新バージョンに移行することをお勧めします。 詳細については、[移行ガイド](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)を参照してください。


## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の前提条件を用意しておく必要があります。

- Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。
- 次の手順に従って作成された、アクティブな Event Hubs 名前空間とイベント ハブ: 「[クイック スタート: Azure portal を使用したイベント ハブの作成](event-hubs-create.md)」。 このチュートリアルで後ほど使用するため、名前空間とイベント ハブの名前をメモしておきます。
- Event Hubs 名前空間の共有アクセス キー名と主キー値。 「[ポータルから接続文字列を取得する](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)」の手順に従って、アクセス キーの名前と値を取得します。 既定のアクセス キー名は **RootManageSharedAccessKey** です。 このチュートリアルで後ほど使用するため、キー名と主キー値をコピーします。
- Python 2.7 および 3.5 以降 (`pip` がインストールおよび更新済み)。
- Event Hubs 用の Python パッケージ。 パッケージをインストールするには、コマンド プロンプトで Python をパス設定して、次のコマンドを実行します。

    ```cmd
    pip install azure-eventhub
    ```

    Azure BLOB ストレージをチェックポイント ストアとして使用してイベントを受信するには、このパッケージをインストールします。

    ```cmd
    pip install azure-eventhub-checkpointstoreblobaio
    ```

## <a name="send-events"></a>送信イベント
このセクションでは、前に作成したイベント ハブにイベントを送信する Python スクリプトを作成します。

1. [Visual Studio Code](https://code.visualstudio.com/) など、お使いの Python エディターを開きます。
2. **send.py** という名前のスクリプトを作成します。 このスクリプトは、前に作成したイベント ハブにイベントのバッチを送信します。
3. 次のコードを send.py に貼り付けます。 詳細については、コードのコメントを参照してください。

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubProducerClient
    from azure.eventhub import EventData

    async def run():
        # create a producer client to send messages to the event hub
        # specify connection string to your event hubs namespace and
            # the event hub name
        producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESPACE - CONNECTION STRING", eventhub_name="EVENT HUB NAME")
        async with producer:
            # create a batch
            event_data_batch = await producer.create_batch()

            # add events to the batch
            event_data_batch.add(EventData('First event '))
            event_data_batch.add(EventData('Second event'))
            event_data_batch.add(EventData('Third event'))

            # send the batch of events to the event hub
            await producer.send_batch(event_data_batch)

    loop = asyncio.get_event_loop()
    loop.run_until_complete(run())

    ```

    > [!NOTE]
    > 非常に役立つコメントを含む完全なソース コードについては、[GitHub 上のこのファイル](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py)を参照してください。

## <a name="receive-events"></a>受信イベント
このクイックスタートでは、Azure Blob Storage をチェックポイント ストアとして使用します。 チェックポイント ストアは、チェックポイント (最後の読み取り位置) を保持するために使用されます。  

### <a name="create-an-azure-storage-and-a-blob-container"></a>Azure Storage と BLOB コンテナーを作成する
次の手順に従って、Azure Storage アカウントと BLOB コンテナーを作成します。

1. [Azure ストレージ アカウントの作成](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [BLOB コンテナーを作成する](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [ストレージ アカウントへの接続文字列を取得します](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)。

    接続文字列とコンテナー名を書き留めておきます。 これらは、受信コードで使用します。


### <a name="create-python-script-to-receive-events"></a>イベントを受信する Python スクリプトを作成する

このセクションでは、イベント ハブからイベントを受信する Python スクリプトを作成します。

1. [Visual Studio Code](https://code.visualstudio.com/) など、お使いの Python エディターを開きます。
2. **recv.py** という名前のスクリプトを作成します。
3. 次のコードを recv.py に貼り付けます。 詳細については、コードのコメントを参照してください。

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubConsumerClient
    from azure.eventhub.extensions.checkpointstoreblobaio import BlobCheckpointStore


    async def on_event(partition_context, event):
        # print the event data
        print("Received the event: \"{}\" from the partition with ID: \"{}\"".format(event.body_as_str(encoding='UTF-8'), partition_context.partition_id))

        # update the checkpoint so that the program doesn't read the events
        # that it has already read when you run it next time
        await partition_context.update_checkpoint(event)

    async def main():
        # create an Azure blob checkpoint store to store the checkpoints
        checkpoint_store = BlobCheckpointStore.from_connection_string("AZURE STORAGE CONNECTION STRING", "BLOB CONTAINER NAME")

        # create a consumer client for the event hub
        client = EventHubConsumerClient.from_connection_string("EVENT HUBS NAMESPACE CONNECTION STRING", consumer_group="$Default", eventhub_name="EVENT HUB NAME", checkpoint_store=checkpoint_store)
        async with client:
            # call the receive method
            await client.receive(on_event=on_event)

    if __name__ == '__main__':
        loop = asyncio.get_event_loop()
        # run the main method
        loop.run_until_complete(main())    
    ```

    > [!NOTE]
    > 非常に役立つコメントを含む完全なソース コードについては、[GitHub 上のこのファイル](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/recv_with_checkpoint_store_async.py)を参照してください。


### <a name="run-the-receiver-app"></a>受信側アプリを実行する

スクリプトを実行するには、Python をパス設定した状態でコマンド プロンプトを開き、次のコマンドを実行します。

```bash
python recv.py
```

### <a name="run-the-sender-app"></a>送信側アプリを実行する

スクリプトを実行するには、Python をパス設定した状態でコマンド プロンプトを開き、次のコマンドを実行します。

```bash
python send.py
```

イベント ハブに送信されたメッセージが受信側ウィンドウに表示されます。


## <a name="next-steps"></a>次のステップ
このクイックスタートでは、イベントを非同期的に送受信しました。 イベントを同期的に送受信する方法については、[この場所](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples/sync_samples)にあるサンプルを参照してください。

すべてのサンプル (同期と非同期の両方) は、GitHub の[ここ](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples)で見つけることができます。
