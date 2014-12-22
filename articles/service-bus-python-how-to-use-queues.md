<properties urlDisplayName="Service Bus Queues" pageTitle="Service Bus キューの使用方法 (Python) - Azure" metaKeywords="Azure Service Bus キュー, Azure キュー, Azure メッセージング, Azure キュー Python" description="Learn how to use Service Bus queues in Azure. Code samples written in Python." metaCanonical="" services="service-bus" documentationCenter="Python" title="How to Use Service Bus Queues" authors="huvalo" solutions="" manager="wpickett" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="09/19/2014" ms.author="huvalo" />




# サービス バス キューの使用方法
このガイドでは、サービス バス キューの使用方法について説明します。サンプルは 
Python で記述され、Python Azure モジュールを利用しています。紹介するシナリオは、
**キューの作成、メッセージの送受信**、および
**キューの削除**です。キューの詳細については、「[次のステップ][]」のセクションを参照してください。

## 目次

-   [サービス バス キューとは][]
-   [サービス名前空間の作成][]
-   [名前空間の既定の管理資格情報の取得][]
-   [方法: キューを作成する][]
-   [方法: メッセージをキューに送信する][]
-   [方法: キューからメッセージを受信する][]
-   [方法: アプリケーションのクラッシュと読み取り不能のメッセージを処理する][]
-   [次のステップ][]

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

**注: **Python またはクライアント ライブラリをインストールする場合は、「[Python Installation Guide (Python インストール ガイド)](../python-how-to-install/)」を参照してください。


## <a name="create-queue"> </a>キューの作成方法

**ServiceBusService** オブジェクトを使用して、キューを操作できます。プログラムを使用して Azure Service Bus にアクセスするすべての Python ファイルの先頭付近に、次のコードを追加します。

	from azure.servicebus import ServiceBusService, Message, Queue

次のコードでは、**ServiceBusService** オブジェクトを作成します。"mynamespace"、"sharedaccesskeyname"、"sharedaccesskey" の部分は、実際の名前空間、共有アクセス署名 (SAS) キーの名前と値に置き換えます。

	bus_service = ServiceBusService(
		service_namespace='mynamespace',
		shared_access_key_name='sharedaccesskeyname',
		shared_access_key_value='sharedaccesskey')

SAS キーの名前と値は、Azure ポータル接続情報に含まれています。また、サービス エクスプローラーで Service Bus 名前空間を選択すると、Visual Studio プロパティ ウィンドウに表示されます (前のセクションに示されているとおり)。

	bus_service.create_queue('taskqueue')

**create_queue** は追加のオプションもサポートしています。
これにより、メッセージの有効期間や最大キュー サイズなどの既定のキューの設定をオーバーライドできます。
次の例では、
キューの最大サイズを 5 GB に、メッセージの既定の有効期間を 1 分に設定する方法を示しています。

	queue_options = Queue()
	queue_options.max_size_in_megabytes = '5120'
	queue_options.default_message_time_to_live = 'PT1M'

	bus_service.create_queue('taskqueue', queue_options)

## <a name="send-messages"> </a>メッセージをキューに送信する方法

サービス バス キューにメッセージを送信するには、アプリケーションで 
**send\_queue\_message** メソッドを **ServiceBusService** オブジェクトに対して呼び出します。

次の例では、
**send\_queue\_message** を使用して *taskqueue* という名前のキューにテスト メッセージを送信する方法を示しています。

	msg = Message(b'Test Message')
	bus_service.send_queue_message('taskqueue', msg)

Service Bus キューでは、最大 256 KB までのメッセージをサポートしています 
(標準とカスタムのアプリケーション プロパティが含まれるヘッダーは、
64 KB が最大サイズです)。キューで保持されるメッセージ数には上限がありませんが、
キュー 1 つあたりが保持できるメッセージの合計サイズには上限
があります。このキューのサイズはキューの作成時に定義します。
上限は 5 GB です。

## <a name="receive-messages"> </a>キューからメッセージを受信する方法

キューからメッセージを受信するには、ServiceBusService オブジェクトの **receive\_queue\_message** 
メソッドを使用します。****

	msg = bus_service.receive_queue_message('taskqueue', peek_lock=False)
	print(msg.body)

パラメーターの 
**peek\_lock** が **False** に設定されていると、メッセージが読み取られるときにキューから削除されます。
パラメーター 
**peek\_lock** を **True** に設定することによって、キューからメッセージを削除せずに、メッセージを読み取って (ピークして) ロックすることができます。

受信操作の中で行われるメッセージの読み取りと削除の動作は、
最もシンプルなモデルであり、
障害発生時にアプリケーション側でメッセージを処理しないことを許容できるシナリオに最適です。
このことを理解するために、
コンシューマーが受信要求を発行した後で、メッセージを処理する前にクラッシュしたというシナリオを考えてみましょう。
Service Bus はメッセージを読み取り済みとしてマークするため、
アプリケーションが再起動してメッセージの読み取りを再開すると、
クラッシュ前に読み取られていたメッセージは見落とされることになります。


**peek\_lock** パラメーターが **True** に設定されている場合、
受信処理が 2 段階の動作になり、
メッセージが失われることが許容できないアプリケーションに対応することができます。Service Bus は要求を受け取ると、
次に読み取られるメッセージを検索して、
他のコンシューマーが受信できないようロックしてから、アプリケーションにメッセージを返します。
アプリケーションがメッセージの処理を終えた後 (または
後で処理するために確実に保存した後)、
**delete** メソッドを **Message** オブジェクトに対して呼び出して受信処理の第 2 段階を完了します。
**delete** メソッドによって、メッセージが読み取り中としてマークされ、
キューから削除されます。

	msg = bus_service.receive_queue_message('taskqueue', peek_lock=True)
	print(msg.body)

	msg.delete()

## <a name="handle-crashes"> </a>アプリケーションのクラッシュと読み取り不能のメッセージを処理する方法

Service Bus には、
アプリケーションにエラーが発生した場合や、メッセージの処理に問題がある場合に復旧を支援する機能が備わっています。
受信側のアプリケーションがなんらかの理由によってメッセージを処理できない場合には、
Message オブジェクトの **unlock** メソッドを呼び出すことができます
。****このメソッドが呼び出されると、Service Bus によってキュー内のメッセージのロックが解除され、
メッセージが再度受信できる状態に変わります。
メッセージを受信するアプリケーションは、以前と同じものでも、別のものでもかまいません
。

キュー内でロックされているメッセージにはタイムアウトも設定されています。
アプリケーションがクラッシュした場合など、ロックがタイムアウトになる前にアプリケーションがメッセージの処理に失敗した場合には、
メッセージのロックが自動的に解除され、
再度受信できる状態に変わります
。

メッセージが処理された後、
**delete** メソッドが呼び出される前にアプリケーションがクラッシュした場合は、
アプリケーションが再起動する際にメッセージが再配信されます。一般的に、
この動作は、**1 回以上の処理**と呼ばれます。つまり、すべてのメッセージが 1 回以上処理されますが、
特定の状況では、同じメッセージが再配信される可能性があります。
重複処理が許されないシナリオの場合、
重複メッセージの配信を扱うロジックを
アプリケーションに追加する必要があります。通常、この問題はメッセージの 
**MessageId** プロパティを使用して対処します。このプロパティは配信が試行された後も同じ値を保持します
。

## <a name="next-steps"> </a>次のステップ

これで、Service Bus キューの基本を学習できました。さらに詳細な情報が必要な場合は、
次のリンク先を参照してください。

-   MSDN リファレンス: [キュー、トピック、およびサブスクリプション][]

  [次のステップ]: #next-steps
  [サービス バス キューとは]: #what-are-service-bus-queues
  [サービス名前空間の作成]: #create-a-service-namespace
  [名前空間の既定の管理資格情報の取得]: #obtain-default-credentials
  [方法: キューを作成する]: #create-queue
  [方法: メッセージをキューに送信する]: #send-messages
  [方法: キューからメッセージを受信する]: #receive-messages
  [方法: アプリケーションのクラッシュと読み取り不能のメッセージを処理する]: #handle-crashes
  [キューの概念]: ../../../DevCenter/dotNet/Media/sb-queues-08.png
  [Azure 管理ポータル]: http://manage.windowsazure.com
  
  
  
  
  
  [キュー、トピック、およびサブスクリプション]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh367516.aspx
