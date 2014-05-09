<properties linkid="develop-python-service-bus-queues" urlDisplayName="サービス バス キュー" pageTitle="サービス バス キューの使用方法 (Python) - Azure" metaKeywords="Azure サービス バス キュー, Azure キュー, Azure メッセージング, Azure キュー Python" description="Azure でのサービス バス キューの使用方法を学習します。コード サンプルは Python で記述されています。" metaCanonical="" services="service-bus" documentationCenter="Python" title="サービス バス キューの使用方法" authors="" solutions="" manager="" editor="" />




# サービス バス キューの使用方法
このガイドでは、サービス バス キューの使用方法について説明します。サンプルは 
Python で記述され、Python Azure モジュールを利用しています。紹介するシナリオでは、
**キューの作成、メッセージの送受信**、および**キューの削除**を対象としています。キューの詳細については、「[次のステップ][]」のセクションを参照してください。

## 目次

-   [サービス バス キューとは][]
-   [サービス名前空間の作成][]
-   [名前空間の既定の管理資格情報の取得][]
-   [How to: キューを作成する][]
-   [How to: メッセージをキューに送信する][]
-   [How to: キューからメッセージを受信する][]
-   [How to: アプリケーションのクラッシュと読み取り不能のメッセージを処理する][]
-   [次のステップ][]

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

**注:** Python またはクライアント ライブラリをインストールする場合は、「[Python Installation Guide (Python インストール ガイド)](../python-how-to-install/).
」を参照してください。

## <a name="create-queue"> </a>キューの作成方法

**ServiceBusService** オブジェクトを使用して、キューを操作できます。プログラムを使用して Azure のサービス バスにアクセスするすべての Python ファイルの先頭付近に、次のコードを追加します。

	from azure.servicebus import *

次のコードでは、**ServiceBusService** オブジェクトを作成します。"mynamespace"、"mykey"、"myissuer" の部分は、実際の名前空間、キー、発行者に置き換えます。

	bus_service = ServiceBusService(service_namespace='mynamespace', account_key='mykey', issuer='myissuer')
	
	bus_service.create_queue('taskqueue')

**create_queue** は追加のオプションもサポートしています。
これにより、メッセージの有効期間や最大キュー サイズなどの既定の
キューの設定をオーバーライドできます。次の例では、キューの最大サイズを 5 GB に、
メッセージの既定の有効期間を 1 分に設定する方法を示しています。

	queue_options = Queue()
	queue_options.max_size_in_megabytes = '5120'
	queue_options.default_message_time_to_live = 'PT1M'

	bus_service.create_queue('taskqueue', queue_options)

## <a name="send-messages"> </a>メッセージをキューに送信する方法

サービス バス キューにメッセージを送信するには、アプリケーションで 
**ServiceBusService** オブジェクトの **send\_queue\_message** メソッドを呼び出します。

次の例では、**send\_queue\_message** を使用して、
*taskqueue* という名前のキューにテスト メッセージを送信する方法を示しています。

	msg = Message('Test Message')
	bus_service.send_queue_message('taskqueue', msg)

サービス バス キューでは、最大 256 KB までのメッセージをサポートして
います (標準とカスタムのアプリケーション プロパティが含まれるヘッダー
の最大サイズは 64 KB です)。キューで保持されるメッセージ数には上限が
ありませんが、キュー 1 つあたりが保持できるメッセージの合計サイズには
上限があります。このキューのサイズはキューの作成時に定義します。
上限は 5 GB です。

## <a name="receive-messages"> </a>キューからメッセージを受信する方法

キューからメッセージを受信するには、**ServiceBusService** オブジェクトの 
**receive\_queue\_message** メソッドを使用します。

	msg = bus_service.receive_queue_message('taskqueue')
	print(msg.body)

メッセージは
読み取られるときにキューから削除されますが、省略可能な **peek\_lock** 
パラメーターを **True** に設定することによって、キューからメッセージを
削除せずに、メッセージを読み取って (ピークして) ロックすることができます。

受信操作の中で行われるメッセージの読み取りと削除の既定の動作は、
最もシンプルなモデルであり、問題が発生した際にアプリケーション側で
メッセージを処理しないことを許容できるシナリオに
最適です。このことを理解するために、コンシューマーが受信要求を
発行した後で、メッセージを処理する前にクラッシュしたというシナリオを
考えてみましょう。サービス バス はメッセージを読み取り済みとしてマークするため、アプリケーションが
再起動してメッセージの読み取りを再開すると、クラッシュ前に読み取られていた
メッセージは見落とされることになります。


**peek\_lock** パラメーターが **True** に設定されている場合、
受信処理が 2 段階の動作になり、メッセージが失われることが許容できない
アプリケーションに対応することができます。サービス バス は要求を受け取ると、
次に読み取られるメッセージを検索して、他のコンシューマーが受信できないよう
ロックしてから、アプリケーションにメッセージを返します。
アプリケーションがメッセージの処理を終えた後 (または後で処理するために
確実に保存した後)、**Message** オブジェクトの **delete** メソッドを
呼び出して受信処理の第 2 段階を実行します。**delete** メソッドによって、
メッセージが読み取り中としてマークされ、キューから削除されます。

	msg = bus_service.receive_queue_message('taskqueue', peek_lock=True)
	print(msg.body)

	msg.delete()

## <a name="handle-crashes"> </a>アプリケーションのクラッシュと読み取り不能のメッセージを処理する方法

サービス バス には、アプリケーションにエラーが発生した場合や、メッセージの
処理に問題がある場合に復旧を支援する機能が備わっています。受信側の
アプリケーションが何らかの理由によってメッセージを処理できない場合には、
**Message** オブジェクトの **unlock** メソッドを
呼び出すことができます。このメソッドが呼び出されると、 サービス バス によって
キュー内のメッセージのロックが解除され、メッセージが再度受信できる状態に
変わります。メッセージを受信するアプリケーションは、以前と同じものでも、
別のものでもかまいません。

キュー内でロックされているメッセージにはタイムアウトも設定されています。
アプリケーションがクラッシュした場合など、ロックがタイムアウトに
なる前にアプリケーションがメッセージの処理に失敗した場合には、
メッセージのロックが自動的に解除され、再度受信できる状態に
変わります。

メッセージが処理された後、**delete** メソッドが呼び出される前に
アプリケーションがクラッシュした場合は、アプリケーションが
再起動する際にメッセージが再配信されます。一般的に、
この動作は **"1 回以上の処理"** と呼ばれます。つまり、すべての
メッセージが 1 回以上処理されますが、特定の状況では、同じメッセージが
再配信される可能性があります。重複処理が許されないシナリオの場合、重複メッセージの配信を
扱うロジックをアプリケーションに追加する必要があります。通常、この問題はメッセージの 
**MessageId** プロパティを使用して対処します。このプロパティは配信が
試行された後も同じ値を保持します。

## <a name="next-steps"> </a>次のステップ

これで、サービス バス キューの基本を学習できました。さらに詳細な情報が必要な場合は、
次のリンク先を参照してください。

-   MSDN リファレンス: [キュー、トピック、およびサブスクリプション][]

  [次のステップ]: #next-steps
  [サービス バス キューとは]: #what-are-service-bus-queues
  [サービス名前空間の作成]: #create-a-service-namespace
  [名前空間の既定の管理資格情報の取得]: #obtain-default-credentials
  [How to: キューを作成する]: #create-queue
  [How to: メッセージをキューに送信する]: #send-messages
  [How to: キューからメッセージを受信する]: #receive-messages
  [How to: アプリケーションのクラッシュと読み取り不能のメッセージを処理する]: #handle-crashes
  [キューの概念]: ../../../DevCenter/dotNet/Media/sb-queues-08.png
  [Azure 管理ポータル]: http://manage.windowsazure.com
  
  
  
  
  
  [サービス バス キュー、トピック、およびサブスクリプション]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh367516.aspx

