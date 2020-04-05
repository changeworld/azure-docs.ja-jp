---
title: Python を使用して Azure Event Hubs との間でイベントを送受信する (旧バージョン)
description: このチュートリアルでは、以前の azure-eventhub バージョン 1 パッケージを使用して、Azure Event Hubs との間でイベントを送受信する Python スクリプトを作成して実行する方法について説明します。
services: event-hubs
author: spelluru
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/15/2020
ms.author: spelluru
ms.openlocfilehash: 22f6b2aba36e560e9bd335baa92925fe9846c670
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2020
ms.locfileid: "77162601"
---
# <a name="quickstart-send-and-receive-events-with-event-hubs-using-python-azure-eventhub-version-1"></a>クイック スタート:Python を使用して Event Hubs 間でイベントを送受信する (azure-eventhub バージョン 1)
このクイックスタートでは、**azure-eventhub バージョン 1** Python パッケージを使用して、イベント ハブとの間でイベントを送受信する方法について説明します。 

> [!WARNING]
> このクイックスタートでは、以前の azure-eventhub バージョン 1 パッケージを使用します。 最新パッケージの**バージョン 5** を使用するクイックスタートについては、[azure-eventhub バージョン 5 を使用したイベントの送受信](get-started-python-send-v2.md)に関するページを参照してください。 古いパッケージではなく新しいパッケージを使用するようにアプリケーションを移行するには、[azure-eventhub バージョン 1 からバージョン 5 への移行ガイド](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)に関するページを参照してください。
 

## <a name="prerequisites"></a>前提条件
Azure Event Hubs を初めて使用する場合は、このクイックスタートを行う前に[イベント ハブの概要](event-hubs-about.md)を参照してください。 

このクイック スタートを完了するには、次の前提条件を用意しておく必要があります。

- **Microsoft Azure サブスクリプション**。 Azure Event Hubs を含む Azure サービスを使用するには、サブスクリプションが必要です。  既存の Microsoft Azure アカウントをお持ちでない場合は、[アカウントを作成する](https://azure.microsoft.com)際に、[無料試用版](https://azure.microsoft.com/free/)にサインアップするか、MSDN サブスクライバー特典を利用できます。
- Python 3.4 以降 (`pip` がインストールおよび更新されている)。
- Event Hubs 用の Python パッケージ。 パッケージをインストールするには、コマンド プロンプトで Python をパス設定して、次のコマンドを実行します。 
  
  ```cmd
  pip install azure-eventhub==1.3.*
  ```
- **Event Hubs 名前空間とイベント ハブを作成する**。 最初の手順では、[Azure Portal](https://portal.azure.com) を使用して Event Hubs 型の名前空間を作成し、アプリケーションがイベント ハブと通信するために必要な管理資格情報を取得します。 名前空間とイベント ハブを作成するには、[こちらの記事](event-hubs-create.md)の手順に従います。 その後、次の記事の手順に従って、イベント ハブ用のアクセス キーの値を取得します。[接続文字列を取得する](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。 このクイックスタートの後半で記述するコードで、このアクセス キーを使用します。 既定のキー名は次のとおりです:**RootManageSharedAccessKey**。 


## <a name="send-events"></a>送信イベント

イベント ハブにイベントを送信する Python アプリケーションを作成するには、次のようにします。

> [!NOTE]
> クイックスタートに取り組む代わりに、GitHub から[サンプル アプリ](https://github.com/Azure/azure-event-hubs-python/tree/master/examples)をダウンロードして実行することができます。 `EventHubConnectionString` と `EventHubName` の文字列を、使用するイベント ハブの値に置き換えます。

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

