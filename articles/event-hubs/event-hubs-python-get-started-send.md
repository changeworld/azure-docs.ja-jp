---
title: クイック スタート:Python を使用してイベントを送受信する - Azure Event Hubs
description: クイック スタート:このチュートリアルでは、Azure Event Hubs 間でイベントを送受信する Python スクリプトを作成して実行する方法について説明します。
services: event-hubs
author: ShubhaVijayasarathy
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/08/2020
ms.author: shvija
ms.openlocfilehash: c4fa9e6038f4007246552610f537825f9def92a8
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/14/2020
ms.locfileid: "75939951"
---
# <a name="quickstart-send-and-receive-events-with-event-hubs-using-python"></a>クイック スタート:Python を使用して Event Hubs 間でイベントを送受信する

Azure Event Hubs はビッグ データ ストリーミング プラットフォームであり、毎秒数百万のイベントを受け取って処理できるイベント インジェスト サービスです。 Event Hubs では、分散されたソフトウェアとデバイスからイベント、データ、またはテレメトリを処理および格納できます。 イベント ハブに送信されたデータは、任意のリアルタイム分析プロバイダーやバッチ処理/ストレージ アダプターを使用して、変換および保存できます。 Event Hubs の詳細については、[Azure Event Hubs](event-hubs-about.md) に関するページと「[Azure Event Hubs の機能と用語](event-hubs-features.md)」を参照してください。

このクイックスタートでは、Python アプリケーションを作成し、イベント ハブ間でイベントを送受信する方法について説明します。 

> [!IMPORTANT]
> このクイックスタートでは、Azure Event Hubs Python SDK のバージョン 1 を使用します。 Azure Event Hubs を初めて使用する場合は、Python SDK のバージョン 5 を使用してください。 Python SDK のバージョン 5 を使用するクイックスタートについては、[こちらの記事](get-started-python-send-v2.md)を参照してください。 既存のコードをバージョン 1 からバージョン 5 に移行する場合は、[移行ガイド](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)をご覧ください。


> [!NOTE]
> クイックスタートに取り組む代わりに、GitHub から[サンプル アプリ](https://github.com/Azure/azure-event-hubs-python/tree/master/examples)をダウンロードして実行することができます。 `EventHubConnectionString` と `EventHubName` の文字列を、使用するイベント ハブの値に置き換えます。 

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、次の前提条件を用意しておく必要があります。

- Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。
- 次の手順に従って作成された、アクティブな Event Hubs 名前空間とイベント ハブ: 「[クイック スタート: Azure portal を使用したイベント ハブの作成](event-hubs-create.md)」。 このチュートリアルで後ほど使用するため、名前空間とイベント ハブの名前をメモしておきます。 
- Event Hubs 名前空間の共有アクセス キー名と主キー値。 「[ポータルから接続文字列を取得する](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)」の手順に従って、アクセス キーの名前と値を取得します。 既定のアクセス キー名は **RootManageSharedAccessKey** です。 このチュートリアルで後ほど使用するため、キー名と主キー値をコピーします。 
- Python 3.4 以降 (`pip` がインストールおよび更新されている)。
- Event Hubs 用の Python パッケージ。 パッケージをインストールするには、コマンド プロンプトで Python をパス設定して、次のコマンドを実行します。 
  
  ```cmd
  pip install azure-eventhub
  ```
  
  > [!NOTE]
  > このクイックスタートのコードは、Event Hubs SDK の現在の安定バージョン 1.3.1 を使用しています。 プレビュー バージョンの SDK を使用するサンプル コードについては、[https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhubs/examples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhubs/examples) を参照してください。

## <a name="send-events"></a>送信イベント

イベント ハブにイベントを送信する Python アプリケーションを作成するには、次のようにします。

1. [Visual Studio Code](https://code.visualstudio.com/) など、お使いの Python エディターを開きます。
2. "*send.py*" という名前の新しいファイルを作成します。 このスクリプトでは、100 のイベントをイベント ハブに送信します。
3. 次のコードを "*send.py*" に貼り付けて、Event Hubs の \<namespace>、\<eventhub>、\<AccessKeyName>、および \<primary key value> を実際の値に置き換えます。 
   
   ```python
   import sys
   import logging
   import datetime
   import time
   import os
   
   from azure.eventhub import EventHubClient, Sender, EventData
   
   logger = logging.getLogger("azure")
   
   # Address can be in either of these formats:
   # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<namespace>.servicebus.windows.net/eventhub"
   # "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   # SAS policy and key are not required if they are encoded in the URL
   
   ADDRESS = "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   USER = "<AccessKeyName>"
   KEY = "<primary key value>"
   
   try:
       if not ADDRESS:
           raise ValueError("No EventHubs URL supplied.")
   
       # Create Event Hubs client
       client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
       sender = client.add_sender(partition="0")
       client.run()
       try:
           start_time = time.time()
           for i in range(100):
               print("Sending message: {}".format(i))
               message = "Message {}".format(i)
               sender.send(EventData(message))
       except:
           raise
       finally:
           end_time = time.time()
           client.stop()
           run_time = end_time - start_time
           logger.info("Runtime: {} seconds".format(run_time))
   
   except KeyboardInterrupt:
       pass
   ```
   
4. ファイルを保存します。 

スクリプトを実行するには、"*send.py*" を保存したディレクトリから、次のコマンドを実行します。

```cmd
start python send.py
```

お疲れさまでした。 メッセージをイベント ハブに送信しました。

## <a name="receive-events"></a>受信イベント

イベント ハブからイベントを受信する Python アプリケーションを作成するには、次のようにします。

1. お使いの Python エディターで、"*recv.py*" という名前のファイルを作成します。
2. 次のコードを "*recv.py*" に貼り付けて、Event Hubs の \<namespace>、\<eventhub>、\<AccessKeyName>、および \<primary key value> を実際の値に置き換えます。 
   
   ```python
   import os
   import sys
   import logging
   import time
   from azure.eventhub import EventHubClient, Receiver, Offset
   
   logger = logging.getLogger("azure")
   
   # Address can be in either of these formats:
   # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
   # "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   # SAS policy and key are not required if they are encoded in the URL
   
   ADDRESS = "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   USER = "<AccessKeyName>"
   KEY = "<primary key value>"
   
   
   CONSUMER_GROUP = "$default"
   OFFSET = Offset("-1")
   PARTITION = "0"
   
   total = 0
   last_sn = -1
   last_offset = "-1"
   client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
   try:
       receiver = client.add_receiver(
           CONSUMER_GROUP, PARTITION, prefetch=5000, offset=OFFSET)
       client.run()
       start_time = time.time()
       for event_data in receiver.receive(timeout=100):
           print("Received: {}".format(event_data.body_as_str(encoding='UTF-8')))
           total += 1
   
       end_time = time.time()
       client.stop()
       run_time = end_time - start_time
       print("Received {} messages in {} seconds".format(total, run_time))
   
   except KeyboardInterrupt:
       pass
   finally:
       client.stop()
   ```
   
4. ファイルを保存します。

スクリプトを実行するには、"*recv.py*" を保存したディレクトリから、次のコマンドを実行します。

```cmd
start python recv.py
```

## <a name="next-steps"></a>次のステップ
Event Hubs の詳細については、次の記事を参照してください。

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Azure Event Hubs の機能と用語](event-hubs-features.md)
- [Event Hubs の FAQ](event-hubs-faq.md)

