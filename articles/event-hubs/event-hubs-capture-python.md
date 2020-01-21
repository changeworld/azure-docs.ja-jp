---
title: クイック スタート:Python アプリからキャプチャしたデータを読み取る - Azure Event Hubs
description: クイック スタート:Azure Python SDK を用いて、Event Hubs Capture 機能を示すスクリプト。
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: bdff820c-5b38-4054-a06a-d1de207f01f6
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: seodec18
ms.date: 01/08/2020
ms.author: shvija
ms.openlocfilehash: e81871e27c04f8a43f678110d7f44cc9c3be149c
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/14/2020
ms.locfileid: "75940757"
---
# <a name="quickstart-event-hubs-capture-walkthrough-python"></a>クイック スタート:Event Hubs Capture チュートリアル:Python

Capture は Azure Event Hubs の機能です。 Capture を使用すると、任意の Azure Blob Storage アカウントに、イベント ハブ内のストリーミング データを自動的に配布できます。 この機能により、リアルタイムのストリーミング データを容易にバッチ処理することができます。 この記事では、Event Hubs Capture を Python で使用する方法について説明します。 Event Hubs Capture の詳細については、「[Azure Event Hubs で Azure Blob Storage または Azure Data Lake Storage にイベントをキャプチャする][Overview of Event Hubs Capture]」を参照してください。

このチュートリアルでは、[Azure Python SDK](https://azure.microsoft.com/develop/python/) を使用して、Capture の機能を示します。 "*sender.py*" プログラムでは、シミュレートされた環境のテレメトリが JSON 形式で Event Hubs に送信されます。 イベント ハブで Capture 機能が使用され、このデータが数回に分けて Blob Storage に書き込まれます。 "*capturereader.py*" アプリでは、これらの BLOB を読み取り、各デバイスに追加ファイルを作成して、各デバイスの " *.csv*" ファイルにデータを書き込みます。

> [!IMPORTANT]
> このクイックスタートでは、Azure Event Hubs Python SDK のバージョン 1 を使用します。 Azure Event Hubs を初めて使用する場合は、Python SDK のバージョン 5 を使用してください。 Python SDK のバージョン 5 を使用するクイックスタートについては、[こちらの記事](get-started-capture-python-v2.md)を参照してください。 既存のコードをバージョン 1 からバージョン 5 に移行する必要がある場合は、[移行ガイド](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)をご覧ください。

このチュートリアルでは次を行います。 

> [!div class="checklist"]
> * Azure portal で Azure Blob Storage アカウントとコンテナーを作成します。
> * Event Hubs Capture を有効にしてストレージ アカウントに送ります。
> * Python スクリプトを使用して、利用するイベント ハブへデータを送信します。
> * 別の Python スクリプトを使用して、Event Hubs Capture からファイルを読み取り、処理します。

## <a name="prerequisites"></a>前提条件

- Python 3.4 以降 (`pip` がインストールおよび更新されている)。
  
- Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。
  
- 次の手順に従って作成された、アクティブな Event Hubs 名前空間とイベント ハブ: 「[クイック スタート: Azure portal を使用したイベント ハブの作成](event-hubs-create.md)」。 このチュートリアルの後半で使用するため、名前空間とイベント ハブの名前をメモしておきます。 
  
  > [!NOTE]
  > 使用するストレージ コンテナーが既にある場合は、Event Hub の作成時に Capture を有効にし、ストレージ コンテナーを選択することができます。 
  > 
  
- Event Hubs 共有アクセス キー名と主キー値。 これらの値は、Event Hubs ページの **[共有アクセス ポリシー]** で検索または作成します。 既定のアクセス キー名は **RootManageSharedAccessKey** です。 このチュートリアルで後ほど使用するため、アクセス キー名と主キー値をコピーします。 

## <a name="create-an-azure-blob-storage-account-and-container"></a>Azure Blob Storage アカウントとコンテナーを作成する

キャプチャに使用するストレージ アカウントとコンテナーを作成します。 

1. [Azure portal][Azure portal] にサインインする
2. 左側のナビゲーションで、 **[ストレージ アカウント]** を選択し、 **[ストレージ アカウント]** 画面で **[追加]** を選択します。
3. ストレージ アカウントの作成画面で、サブスクリプションとリソース グループを選択し、ストレージ アカウントに名前を付けます。 他の選択は既定のままにしておくことができます。 **[確認および作成]** を選択し、設定を確認してから、 **[作成]** を選択します。 
   
   ![ストレージ アカウントの作成][1]
   
4. デプロイが完了したら、 **[リソースに移動]** を選択し、ストレージ アカウントの **[概要]** 画面で **[コンテナー]** を選択します。
5. **[コンテナー]** 画面で、 **[+ コンテナー]** を選択します。 
6. **[新しいコンテナー]** 画面で、コンテナーに名前を付け、 **[OK]** を選択します。 このチュートリアルで後ほど使用するため、コンテナー名をメモしておきます。 
7. **[コンテナー]** 画面の左側のナビゲーションで、 **[アクセス キー]** を選択します。 このチュートリアルで後ほど使用するため、**ストレージ アカウント名**、**key1** の下の **キー** 値をコピーします。
 
## <a name="enable-event-hubs-capture"></a>Event Hubs Capture を有効にする

1. Azure portal で、 **[すべてのリソース]** からその Event Hubs 名前空間を選択し、左側のナビゲーションで **[イベント ハブ]** を選択してから、ご自分のイベント ハブを選択して、イベント ハブに移動します。 
2. イベント ハブの **[概要]** 画面で、 **[Capture events]\(イベントのキャプチャ\)** を選択します。
3. **[キャプチャ]** 画面で、 **[オン]** を選択します。 次に、 **[Azure ストレージ コンテナー]** で、 **[コンテナーの選択]** を選択します。 
4. **[コンテナー]** 画面で、使用するストレージ コンテナーを選択し、 **[選択]** を選択します。 
5. **[キャプチャ]** 画面で、 **[変更の保存]** を選択します。 

## <a name="create-a-python-script-to-send-events-to-event-hub"></a>イベントをイベント ハブに送信する Python スクリプトを作成する
このスクリプトでは、200 のイベントをイベント ハブに送信します。 イベントは、JSON 形式で送信される単純な環境測定値です。

1. [Visual Studio Code][Visual Studio Code]など、お使いの Python エディターを開きます。
2. "*sender.py*" という名前の新しいファイルを作成します。 
3. 次のコードを "*sender.py*" に貼り付けます。 Event Hubs の \<namespace>、\<AccessKeyName>、\<primary key value>、および \<eventhub> を、実際の値に置き換えます。
   
   ```python
   import uuid
   import datetime
   import random
   import json
   from azure.servicebus.control_client import ServiceBusService
   
   sbs = ServiceBusService(service_namespace='<namespace>', shared_access_key_name='<AccessKeyName>', shared_access_key_value='<primary key value>')
   devices = []
   for x in range(0, 10):
       devices.append(str(uuid.uuid4()))
   
   for y in range(0,20):
       for dev in devices:
           reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
           s = json.dumps(reading)
           sbs.send_event('<eventhub>', s)
       print(y)
   ```
4. ファイルを保存します。

## <a name="create-a-python-script-to-read-capture-files"></a>Capture ファイルを読み取る Python スクリプトを作成する

このスクリプトでは、キャプチャされたファイルを読み取り、デバイスごとに、そのデバイスのデータのみを書き込めるようにファイルを作成します。

1. Python エディターで、"*capturereader.py*" という名前の新しいファイルを作成します。 
2. 次のコードを "*capturereader.py*" に貼り付けます。 \<storageaccount>、\<storage account access key>、および \<storagecontainer> を、保存した値に置き換えます。
   
   ```python
   import os
   import string
   import json
   import avro.schema
   from avro.datafile import DataFileReader, DataFileWriter
   from avro.io import DatumReader, DatumWriter
   from azure.storage.blob import BlockBlobService
   
   def processBlob(filename):
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
           deviceFile = open(device + '.csv', "a")
           for r in dict[device]:
               deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')
   
   def startProcessing(accountName, key, container):
       print('Processor started using path: ' + os.getcwd())
       block_blob_service = BlockBlobService(account_name=accountName, account_key=key)
       generator = block_blob_service.list_blobs(container)
       for blob in generator:
           #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)
           if blob.properties.content_length > 508:
               print('Downloaded a non empty blob: ' + blob.name)
               cleanName = str.replace(blob.name, '/', '_')
               block_blob_service.get_blob_to_path(container, blob.name, cleanName)
               processBlob(cleanName)
               os.remove(cleanName)
           block_blob_service.delete_blob(container, blob.name)
   startProcessing('<storageaccount>', '<storage account access key>', '<storagecontainer>')
   ```

## <a name="run-the-python-scripts"></a>Python スクリプトを実行する

1. Python をパス設定したコマンド プロンプトを開き、次のコマンドを実行して Python の前提条件となるパッケージをインストールします。
   
   ```cmd
   pip install azure-storage
   pip install azure-servicebus
   pip install avro-python3
   ```
   
   以前のバージョンの `azure-storage` または `azure` をご利用の場合は、`--upgrade` オプションの使用が必要になることがあります。
   
   また、次のコマンドの実行が必要になる場合もあります。 ほとんどのシステムでは、このコマンドを実行する必要はありません。 
   
   ```cmd
   pip install cryptography
   ```
   
2. "*sender.py*" と "*capturereader.py*" を保存したディレクトリから、次のコマンドを実行します。
   
   ```cmd
   start python sender.py
   ```
   
   このコマンドにより、新しい Python プロセスが起動して sender が実行されます。
   
3. キャプチャの実行が完了したら、次のコマンドを実行します。
   
   ```cmd
   python capturereader.py
   ```

   キャプチャ プロセッサでは、ストレージ アカウント コンテナーから空ではないすべての BLOB がダウンロードされ、その結果が " *.csv*" ファイルとしてローカル ディレクトリに書き込まれます。 

## <a name="next-steps"></a>次のステップ

Event Hubs の詳細については、以下を参照してください。 

* [Event Hubs Capture の概要][Overview of Event Hubs Capture]
* [Event Hubs を使用するサンプル アプリケーション](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Event Hubs の概要][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
