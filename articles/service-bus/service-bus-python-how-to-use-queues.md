<properties 
	pageTitle="Service Bus キューの使用方法 (Python) | Microsoft Azure" 
	description="Python から Azure Service Bus キューを使用する方法を説明します。" 
	services="service-bus" 
	documentationCenter="python" 
	authors="sethmanheim" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="05/10/2016" 
	ms.author="sethm"/>


# Service Bus キューの使用方法

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

この記事では、Service Bus キューの使用方法について説明します。サンプルは Python で記述され、[Python Azure パッケージ][]を使用しています。紹介するシナリオは、**キューの作成、メッセージの送受信**、**キューの削除**です。

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

> [AZURE.NOTE] Python または [Python Azure パッケージ][]をインストールする方法については、「[Python インストール ガイド](../python-how-to-install.md)」を参照してください。

## キューを作成する

**ServiceBusService** オブジェクトを使用すると、キューを操作できます。プログラムを使用して Service Bus にアクセスするすべての Python ファイルの先頭付近に次のコードを追加します。

```
from azure.servicebus import ServiceBusService, Message, Queue
```

次のコードでは、**ServiceBusService** オブジェクトを作成します。`mynamespace`、`sharedaccesskeyname`、`sharedaccesskey` の部分は、実際の名前空間、Shared Access Signature (SAS) キー名、キー値に置き換えます。

```
bus_service = ServiceBusService(
	service_namespace='mynamespace',
	shared_access_key_name='sharedaccesskeyname',
	shared_access_key_value='sharedaccesskey')
```

SAS キーの名前と値は、[Azure クラシック ポータル][]接続情報に含まれています。また、サービス エクスプローラーで Service Bus 名前空間を選択すると、Visual Studio の**プロパティ** ウィンドウに表示されます (前のセクションに示されているとおり)。

```
bus_service.create_queue('taskqueue')
```

**create\_queue** は追加のオプションもサポートしています。これにより、メッセージの有効期間 (TTL) や最大キュー サイズなどの既定のキューの設定をオーバーライドできます。次の例では、最大キュー サイズを 5 GB に設定し、TTL 値を 1 分に設定しています。

```
queue_options = Queue()
queue_options.max_size_in_megabytes = '5120'
queue_options.default_message_time_to_live = 'PT1M'

bus_service.create_queue('taskqueue', queue_options)
```

## メッセージをキューに送信する

メッセージを Service Bus キューに送信するには、アプリケーションで **ServiceBusService** オブジェクトの **send\_queue\_message** メソッドを呼び出します。

次の例では、**send\_queue\_message** を使用して、*taskqueue* という名前のキューにテスト メッセージを送信する方法を示しています。

```
msg = Message(b'Test Message')
bus_service.send_queue_message('taskqueue', msg)
```

Service Bus キューでは、最大 256 KB までのメッセージをサポートしています (標準とカスタムのアプリケーション プロパティが含まれるヘッダーの最大サイズは 64 KB です)。キューで保持されるメッセージ数には上限がありませんが、キュー 1 つあたりが保持できるメッセージの合計サイズには上限があります。このキュー サイズは作成時に定義され、上限は 5 GB です。クォータの詳細については、「[Service Bus のクォータ][]」を参照してください。

## キューからメッセージを受信する

キューからメッセージを受信するには、**ServiceBusService** オブジェクトの **receive\_queue\_message** メソッドを使用します。

```
msg = bus_service.receive_queue_message('taskqueue', peek_lock=False)
print(msg.body)
```

**peek\_lock** パラメーターが **False** に設定されていると、メッセージが読み取られるときにキューから削除されます。**peek\_lock** パラメーターを **True** に設定することによって、キューからメッセージを削除せずに、メッセージを読み取って (ピークして) ロックすることができます。

受信操作の中で行われるメッセージの読み取りと削除の動作は、最もシンプルなモデルであり、障害発生時にアプリケーション側でメッセージを処理しないことを許容できるシナリオに最適です。このことを理解するために、コンシューマーが受信要求を発行した後で、メッセージを処理する前にクラッシュしたというシナリオを考えてみましょう。Service Bus はメッセージを読み取り済みとしてマークするため、アプリケーションが再起動してメッセージの読み取りを再開すると、クラッシュ前に読み取られていたメッセージは見落とされることになります。

**peek\_lock** パラメーターが **True** に設定されている場合、受信処理が 2 段階の動作になり、メッセージが失われることが許容できないアプリケーションに対応することができます。Service Bus は要求を受け取ると、次に読み取られるメッセージを検索して、他のコンシューマーが受信できないようロックしてから、アプリケーションにメッセージを返します。アプリケーションがメッセージの処理を終えた後 (または後で処理するために確実に保存した後)、**Message** オブジェクトの **delete** メソッドを呼び出して受信処理の第 2 段階を完了します。**delete** メソッドによって、メッセージが読み取り中としてマークされ、キューから削除されます。

```
msg = bus_service.receive_queue_message('taskqueue', peek_lock=True)
print(msg.body)

msg.delete()
```

## アプリケーションのクラッシュと読み取り不能のメッセージを処理する方法

Service Bus には、アプリケーションにエラーが発生した場合や、メッセージの処理に問題がある場合に復旧を支援する機能が備わっています。受信側のアプリケーションがなんらかの理由によってメッセージを処理できない場合には、**Message** オブジェクトの **unlock** メソッドを呼び出すことができます。このメソッドが呼び出されると、Service Bus によってキュー内のメッセージのロックが解除され、メッセージが再度受信できる状態に変わります。メッセージを受信するアプリケーションは、以前と同じものでも、別のものでもかまいません。

キュー内でロックされているメッセージにはタイムアウトも設定されています。アプリケーションがクラッシュした場合など、ロックがタイムアウトになる前にアプリケーションがメッセージの処理に失敗した場合には、Service Bus によりメッセージのロックが自動的に解除され、再度受信できる状態に変わります。

メッセージが処理された後、**delete** メソッドが呼び出される前にアプリケーションがクラッシュした場合は、アプリケーションが再起動する際にメッセージが再配信されます。一般的に、この動作は **"1 回以上の処理"** と呼ばれます。つまり、すべてのメッセージが 1 回以上処理されますが、特定の状況では、同じメッセージが再配信される可能性があります。重複処理が許されないシナリオの場合、重複メッセージの配信を扱うロジックをアプリケーションに追加する必要があります。通常、この問題はメッセージの **MessageId** プロパティを使用して対処します。このプロパティは配信が試行された後も同じ値を保持します。

## 次のステップ

これで、Service Bus キューの基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先をご覧ください。

-   [キュー、トピック、およびサブスクリプション][]に関するページをご覧ください。

[Azure クラシック ポータル]: https://manage.windowsazure.com
[Python Azure パッケージ]: https://pypi.python.org/pypi/azure
[キュー、トピック、およびサブスクリプション]: service-bus-queues-topics-subscriptions.md
[Service Bus のクォータ]: service-bus-quotas.md
 

<!---HONumber=AcomDC_0511_2016-->