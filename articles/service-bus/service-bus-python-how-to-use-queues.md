<properties 
	pageTitle="Service Bus キューの使用方法 (Python) - Azure" 
	description="Python から Azure Service Bus キューを使用する方法を説明します。" 
	services="service-bus" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="02/10/2015" 
	ms.author="huvalo"/>




#Service Bus キューの使用方法

このガイドでは、Service Bus キューの使用方法について説明します。サンプルは Python で記述され、[Python Azure パッケージ][]を使用しています。紹介するシナリオは、**キューの作成、メッセージの送受信**、**キューの削除**です。キューの詳細については、「[次のステップ][]」のセクションをご覧ください。

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

**注:** Python または [Python Azure パッケージ][]をインストールする場合は、「[Python Installation Guide (Python インストール ガイド)](../python-how-to-install.md)」をご覧ください。


##キューの作成方法

**ServiceBusService** オブジェクトを使用して、キューを操作できます。プログラムを使用して Azure のサービス バスにアクセスするすべての Python ファイルの先頭付近に、次のコードを追加します。

	from azure.servicebus import ServiceBusService, Message, Queue

次のコードでは、**ServiceBusService** オブジェクトを作成します。 'mynamespace'、 'sharedaccesskeyname'、 'sharedaccesskey' の部分は、実際の名前空間、共有アクセス署名 (SAS) キーの名前と値に置き換えます。

	bus_service = ServiceBusService(
		service_namespace='mynamespace',
		shared_access_key_name='sharedaccesskeyname',
		shared_access_key_value='sharedaccesskey')

SAS キーの名前と値は、Azure ポータル接続情報に含まれています。また、サービス エクスプローラーで Service Bus 名前空間を選択すると、Visual Studio プロパティ ウィンドウに表示されます (前のセクションに示されているとおり)。

	bus_service.create_queue('taskqueue')

**create_queue** は追加のオプションもサポートしています。これにより、メッセージの有効期間や最大キュー サイズなどの既定のキューの設定をオーバーライドできます。次の例では、キューの最大サイズを 5 GB に、メッセージの既定の有効期間を 1 分に設定する方法を示しています。

	queue_options = Queue()
	queue_options.max_size_in_megabytes = '5120'
	queue_options.default_message_time_to_live = 'PT1M'

	bus_service.create_queue('taskqueue', queue_options)

##メッセージをキューに送信する方法

メッセージを Service Bus キューに送信するには、アプリケーションで **ServiceBusService** オブジェクトの **send_queue_message** メソッドを呼び出します。

次の例では、 **send_queue_message** を使用して、*taskqueue* という名前のキューにテスト メッセージを送信する方法を示しています。

	msg = Message(b'Test Message')
	bus_service.send_queue_message('taskqueue', msg)

Service Bus キューでは、最大 256 KB までのメッセージをサポートしています (標準とカスタムのアプリケーション プロパティが含まれるヘッダーの最大サイズは 64 KB です)。キューで保持されるメッセージ数には上限がありませんが、キュー 1 つあたりが保持できるメッセージの合計サイズには上限があります。このキュー サイズは作成時に定義され、上限は 5 GB です。

##キューからメッセージを受信する方法

キューからメッセージを受信するには、**ServiceBusService** オブジェクトの **receive_queue_message** メソッドを使用します。

	msg = bus_service.receive_queue_message('taskqueue', peek_lock=False)
	print(msg.body)

**peek_lock** パラメーターが **False** に設定されていると、メッセージが読み取られるときにキューから削除されます。**peek_lock** パラメーターを **True** に設定することによって、キューからメッセージを削除せずに、メッセージを読み取って (ピークして) ロックできます。

受信操作の中で行われるメッセージの読み取りと削除の動作は、最もシンプルなモデルであり、障害発生時にアプリケーション側でメッセージを処理しないことを許容できるシナリオに最適です。このことを理解するために、コンシューマーが受信要求を発行した後で、メッセージを処理する前にクラッシュしたというシナリオを考えてみましょう。サービス バスはメッセージを読み取り済みとしてマークするため、アプリケーションが再起動してメッセージの読み取りを再開すると、クラッシュ前に読み取られていたメッセージは見落とされることになります。


**peek_lock** パラメーターが **True** に設定されている場合、受信処理が 2 段階の動作になり、メッセージが失われることが許容できないアプリケーションに対応できます。サービス バスは要求を受け取ると、次に読み取られるメッセージを検索して、他のコンシューマーが受信できないようロックしてから、アプリケーションにメッセージを返します。
アプリケーションがメッセージの処理を終えた後 (または後で処理するために確実に保存した後)、**Message** オブジェクトの **delete** メソッドを呼び出して受信処理の第 2 段階を完了します。**delete** メソッドによって、メッセージが読み取り中としてマークされ、キューから削除されます。

	msg = bus_service.receive_queue_message('taskqueue', peek_lock=True)
	print(msg.body)

	msg.delete()

##アプリケーションのクラッシュと読み取り不能のメッセージを処理する方法

サービス バスには、アプリケーションにエラーが発生した場合や、メッセージの処理に問題がある場合に復旧を支援する機能が備わっています。受信側のアプリケーションがなんらかの理由によってメッセージを処理できない場合には、**Message** オブジェクトの **unlock** メソッドを呼び出すことができます。このメソッドが呼び出されると、サービス バスによってキュー内のメッセージのロックが解除され、メッセージが再度受信できる状態に変わります。メッセージを受信するアプリケーションは、以前と同じものでも、別のものでもかまいません。

キュー内でロックされているメッセージにはタイムアウトも設定されています。アプリケーションがクラッシュした場合など、ロックがタイムアウトになる前にアプリケーションがメッセージの処理に失敗した場合には、メッセージのロックが自動的に解除され、再度受信できる状態に変わります。

メッセージが処理された後、**delete** メソッドが呼び出される前にアプリケーションがクラッシュした場合は、アプリケーションが再起動する際にメッセージがアプリケーションに再配信されます。一般的に、この動作は **"1 回以上の処理"** と呼ばれます。つまり、すべてのメッセージが 1 回以上処理されますが、特定の状況では、同じメッセージが再配信される可能性があります。重複処理が許されないシナリオの場合、重複メッセージの配信を扱うロジックをアプリケーションに追加する必要があります。通常、この問題はメッセージの **MessageId** プロパティを使用して対処します。このプロパティは配信が試行された後も同じ値を保持します。

##次のステップ

これで、Service Bus キューの基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先をご覧ください。

-   MSDN リファレンス:[キュー、トピック、サブスクリプション][]

[Azure 管理ポータル]: http://manage.windowsazure.com
[Python Azure パッケージ]: https://pypi.python.org/pypi/azure  
[キュー、トピック、サブスクリプション]: http://msdn.microsoft.com/library/windowsazure/hh367516.aspx

<!--HONumber=47-->
 