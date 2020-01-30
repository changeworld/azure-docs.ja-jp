---
title: Python アプリから Azure Event Hubs でキャプチャされたデータを読み取る | Microsoft Docs
description: この記事では、イベント ハブに送信されたデータをキャプチャし、キャプチャされたイベント データを Azure Storage から読み取る Python コードを記述する方法について説明します。
services: event-hubs
documentationcenter: ''
author: spelluru
editor: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2019
ms.author: spelluru
ms.openlocfilehash: 43223f7cb9ed254340c99d235d494d1e93583c7f
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293540"
---
# <a name="capture-event-hubs-data-in-azure-storage-and-read-it-using-python"></a>Event Hubs データを Azure Storage にキャプチャし、Python を使用してそれを読み取る 
イベント ハブに送信されたデータが Azure Storage または Azure Data Lake Storage にキャプチャされるようにイベント ハブを構成できます。 この記事は、イベントをイベント ハブに送信し、キャプチャされたデータを Azure BLOB ストレージから読み取る Python コードを記述する方法を示します。 この機能の詳細については、[Event Hubs Capture 機能の概要](event-hubs-capture-overview.md)に関するページを参照してください。

このサンプルでは、[Azure Python SDK](https://azure.microsoft.com/develop/python/) を使用して、Capture の機能を試してみます。 Sender.py プログラムは、シミュレートされた環境のテレメトリを JSON 形式で Event Hubs に送信します。 イベント ハブは、Capture 機能を使用して、このデータを数回に分けて Blob Storage に書き込むように構成されています。 capturereader.py アプリは、こうした BLOB を読み取り、デバイスごとに追加ファイルを作成します。 その後、アプリはデータを .csv ファイルに書き込みます。

> [!IMPORTANT]
> このクイックスタートでは、Azure Event Hubs Python SDK のバージョン 5 を使用します。 Python SDK の古いバージョン 1 を使用するクイック スタートについては、[この記事](event-hubs-capture-python.md)を参照してください。 SDK のバージョン 1 を使用している場合は、コードを最新バージョンに移行することをお勧めします。 詳細については、[移行ガイド](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)を参照してください。

このクイック スタートでは次の作業を行います。 

> [!div class="checklist"]
> * Azure portal で Azure Blob Storage アカウントとコンテナーを作成します。
> * Azure portal を使用して Event Hubs 名前空間を作成します。
> * Capture 機能が有効になっているイベント ハブを作成し、それをストレージ アカウントに接続します。
> * Python スクリプトを使用して、利用するイベント ハブへデータを送信します。
> * 別の Python スクリプトを使用して、Event Hubs Capture からファイルを読み取り、処理します。

## <a name="prerequisites"></a>前提条件

- Python 2.7 および 3.5 以降 (`pip` がインストールおよび更新済み)。
- Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。
- [Event Hubs 名前空間を作成し、その名前空間内にイベント ハブを作成します](event-hubs-create.md)。 Event Hubs 名前空間の名前、イベント ハブの名前、および名前空間のプライマリ アクセス キーを書き留めておきます。 次の記事の手順に従ってアクセス キーを取得します。[接続文字列を取得する](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。 既定のキー名は次のとおりです:**RootManageSharedAccessKey**。 このチュートリアルの接続文字列は必要ありません。 必要なのはプライマリ キーだけです。 
- 次の手順に従って、**Azure Storage アカウント**と **BLOB コンテナー**を作成します。
    1. [Azure Storage アカウントを作成します](../storage/common/storage-quickstart-create-account.md?tabs=azure-portal)。
    2. [ストレージ内に BLOB コンテナーを作成します](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)。 
    3. [ストレージ アカウントへの接続文字列を取得します](../storage/common/storage-configure-connection-string.md#view-and-copy-a-connection-string)。

        **接続文字列**と**コンテナー名**を書き留めておきます。 これらは、後でコードの中で使用します。 
- 次の手順に従って、イベント ハブの**キャプチャ**機能を有効にする:「[Azure portal を使用して Event Hubs Capture を有効にする](event-hubs-capture-enable-through-portal.md)」の手順に従います。 前の手順で作成したストレージ アカウントと BLOB コンテナーを選択します。 この機能はまた、イベント ハブの作成時に有効にすることもできます。 

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>イベントをイベント ハブに送信する Python スクリプトを作成する
このセクションでは、イベント ハブに 200 のイベント (10 デバイス * 20 イベント) を送信する Python スクリプトを作成します。 これらのイベントは、JSON 形式で送信されたサンプルの環境測定値です。 

1. [Visual Studio Code][Visual Studio Code]など、お使いの Python エディターを開きます。
2. **sender.py**という名前のスクリプトを作成します。 
3. 次のコードを sender.py に貼り付けます。 詳細については、コードのコメントを参照してください。 
   
    ```python
    import time
    import os
    import uuid
    import datetime
    import random
    import json
    
    from azure.eventhub import EventHubProducerClient, EventData
    
    # this scripts simulates production of events for 10 devices
    devices = []
    for x in range(0, 10):
        devices.append(str(uuid.uuid4()))
    
    # create a producer client to produce/publish events to the event hub
    producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESAPCE CONNECTION STRING", eventhub_name="EVENT HUB NAME")
    
    for y in range(0,20):    # for each device, produce 20 events 
        event_data_batch = producer.create_batch() # create a batch. you will add events to the batch later. 
        for dev in devices:
            # create a dummy reading
            reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
            s = json.dumps(reading) # convert reading into a JSON string
            event_data_batch.add(EventData(s)) # add event data to the batch
        producer.send_batch(event_data_batch) # send the batch of events to the event hub
    
    # close the producer    
    producer.close()
    ```
4. スクリプト内の次の値を置き換えます。
    1. `EVENT HUBS NAMESPACE CONNECTION STRING` を Event Hubs 名前空間への接続文字列に置き換えます。
    2. `EVENT HUB NAME` をイベント ハブの名前に置き換えます。 
5. イベントをイベント ハブに送信するスクリプトを実行します。 
6. Azure portal で、イベント ハブがメッセージを受信したことを確認できます。 **[メトリック]** セクションで **[メッセージ]** ビューに切り替えます。 ページを最新の情報に更新して、グラフを更新します。 メッセージが受信されたことが示されるまでに数秒かかることがあります。 

    [![イベント ハブがメッセージを受信したことを確認する](./media/get-started-capture-python-v2/messages-portal.png)](./media/get-started-capture-python-v2/messages-portal.png#lightbox)

## <a name="create-a-python-script-to-read-your-capture-files"></a>Capture ファイルを読み取る Python スクリプトを作成する
この例では、キャプチャされたデータは Azure Blob Storage に格納されます。 このセクションのスクリプトは、Azure Storage からキャプチャ データ ファイルを読み取り、ユーザーが簡単に開いて内容を表示できる CSV ファイルを生成します。 アプリケーションの現在の作業ディレクトリ内に 10 個のファイルが表示されます。 これらのファイルには、10 個のデバイスの環境測定値が含まれています。 

1. Python エディターで、**capturereader.py** という名前のスクリプトを作成します。 このスクリプトはキャプチャされたファイルを読み取り、それぞれのデバイスにのみデータを書き込むようにデバイスごとのファイルを作成します。
2. 次のコードを capturereader.py に貼り付けます。 詳細については、コードのコメントを参照してください。 
   
    ```python
    import os
    import string
    import json
    import uuid
    import avro.schema
    
    from azure.storage.blob import ContainerClient, BlobClient
    from avro.datafile import DataFileReader, DataFileWriter
    from avro.io import DatumReader, DatumWriter
    
    
    def processBlob2(filename):
        reader = DataFileReader(open(filename, 'rb'), DatumReader())
        dict = {}
        for reading in reader:
            parsed_json = json.loads(reading["Body"])
            if not 'id' in parsed_json:
                return
            if not parsed_json['id'] in dict:
                list = []
                dict[parsed_json['id']] = list
            else:
                list = dict[parsed_json['id']]
                list.append(parsed_json)
        reader.close()
        for device in dict.keys():
            filename = os.getcwd() + '\\' + str(device) + '.csv'
            deviceFile = open(filename, "a")
            for r in dict[device]:
                deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')
    
    def startProcessing():
        print('Processor started using path: ' + os.getcwd())
        # create a blob container client
        container = ContainerClient.from_connection_string("AZURE STORAGE CONNECTION STRING", container_name="BLOB CONTAINER NAME")
        blob_list = container.list_blobs() # list all the blobs in the container
        for blob in blob_list:
            #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)        
            if blob.size > 508:
                print('Downloaded a non empty blob: ' + blob.name)
                # create a blob client for the blob
                blob_client = ContainerClient.get_blob_client(container, blob=blob.name)
                # construct a file name based on the blob name
                cleanName = str.replace(blob.name, '/', '_')
                cleanName = os.getcwd() + '\\' + cleanName 
                with open(cleanName, "wb+") as my_file: # open the file to write. create if it doesn't exist. 
                    my_file.write(blob_client.download_blob().readall()) # write blob contents into the file
                processBlob2(cleanName) # convert the file into a CSV file
                os.remove(cleanName) # remove the original downloaded file
                # delete the blob from the container after it's read
                container.delete_blob(blob.name)
    
    startProcessing()    
    ```
4. `<AZURE STORAGE CONNECTION STRING>` を Azure Storage アカウントへの接続文字列に置き換えます。 このチュートリアルで作成したコンテナーの名前は **capture** です。 コンテナーに別の名前を使用した場合は、`capture` をストレージ アカウント内のコンテナーの名前に置き換えます。 

## <a name="run-the-scripts"></a>スクリプトの実行
1. Python をパス設定した状態でコマンド プロンプトを開き、これらのコマンドを実行してPython の前提条件となるパッケージをインストールします:
   
   ```
   pip install azure-storage-blob
   pip install azure-eventhub
   pip install avro-python3
   ```
2. ディレクトリを sender.py と capturereader.py を保存した場所に変更し、このコマンドを実行します:
   
   ```
   python sender.py
   ```
   
   このコマンドにより、新しい Python プロセスが起動して sender を実行します。
3. キャプチャが実行されるまで、数分間待ちます。 元のコマンド ウィンドウに、次のコマンドを入力します。
   
   ```
   python capturereader.py
   ```

   このキャプチャ プロセッサでは、ローカル ディレクトリが使用され、ストレージ アカウントとコンテナーからすべての BLOB がダウンロードされます。 空ではないものがすべて処理され、結果が .csv ファイルとしてローカル ディレクトリに書き込まれます。

## <a name="next-steps"></a>次のステップ
GitHub の[ここ](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples)にある Python サンプルを確認してください。 


[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
