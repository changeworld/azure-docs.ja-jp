---
title: Python を使用して Azure Event Hubs にイベントを送信する | Microsoft Docs
description: Python を使用して Event Hubs へのイベントの送信を開始する
services: event-hubs
author: sethmanheim
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 07/26/2018
ms.author: sethm
ms.openlocfilehash: 762e21cfc7d16b614eb637c569f8bfc5b6115db1
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/05/2018
ms.locfileid: "43703526"
---
# <a name="send-events-to-event-hubs-using-python"></a>Python を使用して Event Hubs にイベントを送信する

Azure Event Hubs は、拡張性の高いイベント管理システムで、1 秒あたり何百万ものイベントを処理することができます。そのためアプリケーションは、接続されているデバイスや他のシステムによって生成された大量のデータを処理し、分析できます。 イベント ハブに収集されたイベントは、インプロセス ハンドラーを使用するか、他の分析システムに転送して、受信し処理することができます。

Event Hubs の詳細については、[Event Hubs の概要][Event Hubs overview]に関する記事を参照してください。

このチュートリアルでは、Python で記述されたアプリケーションからイベント ハブにイベントを送信する方法について説明します。 イベントの受信については、[対応する受信の記事](event-hubs-python-get-started-receive.md)を参照してください。

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

## <a name="create-a-python-script-to-send-events"></a>イベントを送信する Python スクリプトの作成

次に、イベント ハブにイベントを送信する Python アプリケーションを作成します。

1. [Visual Studio Code][Visual Studio Code] など、お使いの Python エディターを開きます。
2. **send.py** という名前のスクリプトを作成します。 このスクリプトでは、100 のイベントをイベント ハブに送信します。
3. 次のコードを send.py に貼り付けます。ADDRESS、USER、および KEY の値を、前のセクションで Azure portal から取得した値に置き換えてください。 

```python
import sys
import logging
import datetime
import time
import os

from azure.eventhub import EventHubClient, Sender, EventData

logger = logging.getLogger("azure")

# Address can be in either of these formats:
# "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
# "amqps://<mynamespace>.servicebus.windows.net/myeventhub"
# For example:
ADDRESS = "amqps://mynamespace.servicebus.windows.net/myeventhub"

# SAS policy and key are not required if they are encoded in the URL
USER = "RootManageSharedAccessKey"
KEY = "namespaceSASKey"

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
            sender.send(EventData(str(i)))
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

## <a name="send-events"></a>送信イベント

スクリプトを実行するには、Python をパス設定した状態でコマンド プロンプトを開き、次のコマンドを実行します。

```bash
start python send.py
```
 
## <a name="next-steps"></a>次の手順

Python を使ってイベント ハブにイベントを送信したら、今度はイベントを受信してみましょう。[対応する受信の記事](event-hubs-python-get-started-receive.md)を参照してください。

Event Hubs の詳細については、次のページを参照してください。

* [Event Hubs の概要][Event Hubs overview]
* [イベント ハブの作成](event-hubs-create.md)
* [Event Hubs の FAQ](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[Visual Studio Code]: https://code.visualstudio.com/
[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
