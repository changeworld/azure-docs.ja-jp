<properties
	pageTitle="Azure Event Hubs Archive チュートリアル |Microsoft Azure"
	description="Azure Python SDK を使用して、Event Hubs Archive 機能を使用する方法を試すサンプル。"
	services="event-hubs"
	documentationCenter=""
	authors="djrosanova"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/13/2016"
	ms.author="darosa;sethm"/>

# Event Hubs Archive チュートリアル: Python

Event Hubs Archive は Event Hubs の新機能で、任意の Azure Blob Storage アカウントに、Event Hub 内のストリーム データを自動的に配布することができます。これにより、ストリーミング データをリアルタイムで容易にバッチ処理することができます。この記事では、Python で Event Hubs Archive を使用する方法について説明します。Event Hubs Archive の詳細については、「[概要の記事](event-hubs-archive-overview.md)」を参照してください。

このサンプルでは、Azure Python SDK を使用して、Archive の機能を使用する方法を試してみます。Sender.py は、シミュレートされた環境のテレメトリを JSON 形式で Event Hubs に送信します。Event Hub を構成して Archive 機能を使用することにより、このデータをバッチ内の BLOB ストレージに書き込みます。Archivereader.py はこれらの BLOB を読み込み、デバイスごとに追加ファイルを作成し、データを .csv ファイルに書き込みます。

作業内容

1.  Azure Portal を使用した、Azure Blob Storage アカウントと BLOB コンテナーの作成

2.  Azure Portal を使用した Event Hub 名前空間の作成

3.  Azure Portal を使用した、Archive 機能が有効な Event Hub の作成

4.  Python スクリプトを使用した Event Hub へのデータ送信

5.  アーカイブからのファイル読み取りと、別の Python スクリプトによるそれらの処理

前提条件

1.  Python 2.7.x

2.  Azure サブスクリプション

[AZURE.INCLUDE [アカウント作成メモ](../../includes/create-account-note.md)]

## Azure Storage アカウントの作成

1.  [Azure ポータル][]にログオンします。

2.  ポータルの左側のナビゲーション ウィンドウで [新規] をクリックし、[データ + ストレージ]、[ストレージ アカウント] の順にクリックします。

3.  ストレージ アカウントのブレードで、フィールドを入力し、**[作成]** をクリックします。

    ![][1]

4.  **[デプロイメントが成功しました]** メッセージが表示されたら、新しいストレージ アカウントをクリックし、 **[Essentials]** ブレードで **[BLOB]** をクリックします。**[BLOB サービス]** ブレードが開いたら、上部にある **[+ コンテナー]** をクリックします。コンテナーの **[アーカイブ]** に名前を付け、**[BLOB サービス]** ブレードを閉じます。

5.  左のブレードにある **[アクセス キー]** をクリックして、ストレージ アカウントの名前と **[key1]** の値をコピーします。これらの値をメモ帳などに一時的に保存します。

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

## イベントを Event Hub に送信する Python スクリプトを作成する

1.  [Visual Studio Code][] など、お使いの Python エディターを開きます。

2.  **sender.py** という名前のスクリプトを作成します。このスクリプトでは、200 のイベントを Event Hub に送信します。これらは、JSON で送信される単純な環境の測定値です。

3.  以下のコードを sender.py に貼り付けます:

	```
	import uuid
	import datetime
	import random
	import json
	from azure.servicebus import ServiceBusService
	
	sbs = ServiceBusService(service_namespace='INSERT YOUR NAMESPACE NAME', shared_access_key_name='RootManageSharedAccessKey', shared_access_key_value='INSERT YOUR KEY')
	devices = []
	for x in range(0, 10):
	    devices.append(str(uuid.uuid4()))
	
	for y in range(0,20):
	    for dev in devices:
	        reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
	        s = json.dumps(reading)
	        sbs.send\_event('myhub', s)
	    print y
	```
4.  名前空間の名前と Event Hubs の名前空間を作成したときに取得したキーの値を使用するように、上記のコードを更新します。

## アーカイブ ファイルを読み取る Python スクリプトを作成する

1.  ブレードに入力し、**[作成]** をクリックします。

2.  **archivereader.py** と言う名前のスクリプトを作成します。このスクリプトはアーカイブ ファイルを読み取り、それぞれのデバイスにのみデータを書き込むようにデバイスごとのファイルを作成します。

3.  以下のコードを archivereader.py に貼り付けます:

	```
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
	        parsed\_json = json.loads(reading["Body"])
	        if not 'id' in parsed\_json:
	            return
	        if not dict.has\_key(parsed\_json['id']):
	        list = []
	        dict[parsed\_json['id']] = list
	    else:
	        list = dict[parsed\_json['id']]
	        list.append(parsed\_json)
	    reader.close()
	    for device in dict.keys():
	        deviceFile = open(device + '.csv', "a")
	        for r in dict[device]:
	            deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\\n')

	def startProcessing(accountName, key, container):
	    print 'Processor started using path: ' + os.getcwd()
	    block\_blob\_service = BlockBlobService(account\_name=accountName, account\_key=key)
	    generator = block\_blob\_service.list\_blobs(container)
	    for blob in generator:
	        if blob.properties.content\_length != 0:
	            print('Downloaded a non empty blob: ' + blob.name)
	            cleanName = string.replace(blob.name, '/', '\_')
	            block\_blob\_service.get\_blob\_to\_path(container, blob.name, cleanName)
	            processBlob(cleanName)
	            os.remove(cleanName)
	        block\_blob\_service.delete\_blob(container, blob.name)
	startProcessing('YOUR STORAGE ACCOUNT NAME', 'YOUR KEY', 'archive')
    ```

4.  呼び出しでのストレージ アカウント名とキーには必ず適切な値を `startProcessing`に貼り付けます。

## スクリプトの実行

1.  Python をパス設定した状態でコマンド プロンプトを開き、これらのコマンドを実行してPython の前提条件となるパッケージをインストールします:

	```
    pip install azure-storage
	pip install azure-servicebus
	pip install avro
    ```
  
    azure ストレージまたは azure の以前のバージョンを使用する場合は、 **--アップグレード** オプションを使用する場合があります。

    また、次のことを実行する場合もあります (ほとんどのシステムでは必要ありません):

    ```
    pip install cryptography
    ```

2.  ディレクトリを、sender.py と archivereader.py を保存した場所に変更し、このコマンドを実行します:

    ```
    start python sender.py
    ```
    
    これにより、新しい Python プロセスが起動して sender を実行します。

3. アーカイブが実行されるまで、数分間待ちます。元のコマンド ウィンドウに、次のコマンドを入力します。

    ```
    python archivereader.py
    ```

このアーカイブ プロセッサでは、ローカルのディレクトリを使用して、ストレージ アカウントとコンテナーからすべての BLOB をダウンロードします。空ではないものすべてを処理し、結果を .csv ファイルとしてローカル ディレクトリに書き込みます。

## 次のステップ

Event Hubs の詳細については、次のリンク先を参照してください:

- [Event Hubs Archive の概要][]
- [Event Hub を使用する完全なサンプル アプリケーション][]
- [Event Hubs でイベント処理の拡張][]サンプル
- [Event Hubs の概要][]
 

[Azure ポータル]: https://portal.azure.com/
[Event Hubs Archive の概要]: event-hubs-archive-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]: https://azure.microsoft.com/documentation/articles/storage-create-storage-account/
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs の概要]: event-hubs-overview.md
[Event Hub を使用する完全なサンプル アプリケーション]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Event Hubs でイベント処理の拡張]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3

<!---HONumber=AcomDC_0914_2016-->