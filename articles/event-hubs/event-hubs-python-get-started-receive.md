---
title: Python を使用して Azure Event Hubs からイベントを受信する | Microsoft Docs
description: Python を使用して Event Hubs からイベントの受信を開始する
services: event-hubs
author: sethmanheim
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 07/26/2018
ms.author: sethm
ms.openlocfilehash: f5388f2de599d94f68a1d24a7d701a2cb4795915
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/05/2018
ms.locfileid: "43703338"
---
# <a name="receive-events-from-event-hubs-using-python"></a>Python を使用して Event Hubs からイベントを受信する

Azure Event Hubs は、拡張性の高いイベント管理システムで、1 秒あたり何百万ものイベントを処理することができます。そのためアプリケーションは、接続されているデバイスや他のシステムによって生成された大量のデータを処理し、分析できます。 イベント ハブに収集されたイベントは、インプロセス ハンドラーを使用するか、他の分析システムに転送して、受信し処理することができます。

Event Hubs の詳細については、[Event Hubs の概要][Event Hubs overview]に関する記事を参照してください。

このチュートリアルでは、Python で記述されたアプリケーションでイベント ハブからイベントを受信する方法について説明します。 イベントの送信については、[対応する送信の記事](event-hubs-python-get-started-send.md)を参照してください。

このチュートリアルのコードは、[これらの GitHub サンプル](https://github.com/Azure/azure-event-hubs-python/tree/master/examples)から取得されたもので、import ステートメントや変数の宣言など、アプリケーションが完全に機能するかを確認することができます。 その他の例については、同じ GitHub フォルダーから入手できます。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の前提条件を用意しておく必要があります。

- Python 3.4 以降。
- 既存の Azure Event Hubs 名前空間とイベント ハブ。 これらのエンティティは、[こちらの記事](event-hubs-create.md)の手順に従って作成できます。 

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]


## <a name="install-python-package"></a>Python パッケージのインストール

Event Hubs 向けの Python パッケージをインストールするには、Python をパス設定した状態でコマンド プロンプトを開き、次のコマンドを実行します。 

```bash
pip install azure-eventhub
```

## <a name="create-a-python-script-to-receive-events"></a>イベントを受信する Python スクリプトの作成

次に、イベント ハブからイベントを受信する Python アプリケーションを作成します。

1. [Visual Studio Code][Visual Studio Code] など、お使いの Python エディターを開きます。
2. **recv.py** という名前のスクリプトを作成します。
3. 次のコードを recv.py に貼り付けます。ADDRESS、USER、および KEY の値を、前のセクションで Azure portal から取得した値に置き換えてください。 

```python
import os
import sys
import logging
import time
from azure.eventhub import EventHubClient, Receiver, Offset

logger = logging.getLogger("azure")

# Address can be in either of these formats:
# "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
# "amqps://<mynamespace>.servicebus.windows.net/myeventhub"
# For example:
ADDRESS = "amqps://mynamespace.servicebus.windows.net/myeventhub"

# SAS policy and key are not required if they are encoded in the URL
USER = "RootManageSharedAccessKey"
KEY = "namespaceSASKey"
CONSUMER_GROUP = "$default"
OFFSET = Offset("-1")
PARTITION = "0"

total = 0
last_sn = -1
last_offset = "-1"
client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
try:
    receiver = client.add_receiver(CONSUMER_GROUP, PARTITION, prefetch=5000, offset=OFFSET)
    client.run()
    start_time = time.time()
    for event_data in receiver.receive(timeout=100):
        last_offset = event_data.offset
        last_sn = event_data.sequence_number
        print("Received: {}, {}".format(last_offset, last_sn))
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

## <a name="receive-events"></a>受信イベント

スクリプトを実行するには、Python をパス設定した状態でコマンド プロンプトを開き、次のコマンドを実行します。

```bash
start python recv.py
```
 
## <a name="next-steps"></a>次の手順

イベントの送信については、[対応する送信の記事](event-hubs-python-get-started-send.md)を参照してください。

Event Hubs の詳細については、次のページを参照してください。

* [Event Hubs の概要][Event Hubs overview]
* [イベント ハブの作成](event-hubs-create.md)
* [Event Hubs の FAQ](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[Visual Studio Code]: https://code.visualstudio.com/
[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
