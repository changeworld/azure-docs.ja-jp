<properties linkid="develop-python-service-bus-queues" UrlDisplayName="サービス バス キュー" pageTitle="サービス バス キューの使用方法 (Python) - Windows Azure" MetaKeywords="Azure サービス バス キュー, Azure キュー, Azure メッセージング, Azure キュー Python" description="Windows Azure でのサービス バス キューの使用方法について説明します。コード サンプルは Python で記述されています。" metaCanonical="" services="service-bus" documentationCenter="Python" title="サービス バス キューの使用方法" authors=""  solutions="" writer="" manager="" editor=""  />




# サービス バス キューの使用方法
このガイドでは、サービス バス キューの使用方法について説明します。サンプルは Python で
記述され、Python Azure モジュールを利用しています。紹介する
シナリオは、**キューの作成、メッセージの送受信**、および**キューの削除**
です。キューの詳細については、「[次のステップ][]」を参照してください。

## 目次

-   [サービス バス キューとは][]
-   [サービス名前空間の作成][]
-   [名前空間の既定の管理資格情報の取得][]
-   [キューの作成方法][]
-   [メッセージをキューに送信する方法][]
-   [キューからメッセージを受信する方法][]
-   [アプリケーションのクラッシュと読み取り不能のメッセージを処理する方法][]
-   [次のステップ][]

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

**注:** Python またはクライアント ライブラリをインストールする場合は、「[Python Installation Guide (Python インストール ガイド)](../python-how-to-install/)」を参照してください。


## <a name="create-queue"> </a>キューの作成方法

**ServiceBusService** オブジェクトを使用して、キューを操作できます。プログラムを使用して Windows Azure のサービス バス にアクセスするすべての Python ファイルの先頭付近に、次のコードを追加します。

	from azure.servicebus import *

次のコードでは、**ServiceBusService** オブジェクトを作成します。"mynamespace"、"mykey"、"myissuer" の部分は、実際の名前空間、キー、発行者に置き換えます。

	bus_service = ServiceBusService(service_namespace='mynamespace', account_key='mykey', issuer='myissuer')
	
	bus_service.create_queue('taskqueue')

**create_queue** は追加のオプションもサポートしています。
これにより、メッセージの有効期間や最大キュー サイズなどの既定のキューの
設定をオーバーライドできます。次の例では、キューの最大サイズを 5 GB に、
メッセージの既定の有効期間を 1 分に設定する方法を示しています。

	queue_options = Queue()
	queue_options.max_size_in_megabytes = '5120'
	queue_options.default_message_time_to_live = 'PT1M'

	bus_service.create_queue('taskqueue', queue_options)

## <a name="send-messages"> </a>メッセージをキューに送信する方法

メッセージをサービス バス キューに送信するには、アプリケーションで 
**ServiceBusService** オブジェクトの **send\_queue\_message** メソッドを呼び出します。

次の例では、**send\_queue\_message** *を使用して、"taskqueue"* と
いう名前のキューにテスト メッセージを送信する方法を示しています。

	msg = Message('Test Message')
	bus_service.send_queue_message('taskqueue', msg)

サービス バス キューでは、最大 256 KB までのメッセージをサポートしています (標準と
カスタムのアプリケーション プロパティが含まれるヘッダーは、64 KB が最大
サイズです)。キューで保持されるメッセージ数には上限が
ありませんが、キュー 1 つあたりが保持できるメッセージの合計サイズには
上限があります。このキューのサイズはキューの作成時に定義します。
上限は 5 GB です。

## <a name="receive-messages"> </a>キューからメッセージを受信する方法

キューからメッセージを受信するには、**ServiceBusService** オブジェクトの
 **receive\_queue\_message** メソッドを使用します。

	msg = bus_service.receive_queue_message('taskqueue')
	print(msg.body)

メッセージは
読み取られるときにキューから削除されますが、省略可能な **peek\_lock** パラメーター
を **True** に設定することによって、キューからメッセージを削除せずに、
メッセージを読み取って (ピークして) ロックすることができます。

受信操作の中で行われるメッセージの読み取りと削除の既定の動作は、
最もシンプルなモデルであり、障害発生時にアプリケーション側でメッセージを
処理しないことを許容できるシナリオに最適です。このことを理解するために、コンシューマーが受信要求を
発行した後で、メッセージを処理する前にクラッシュしたというシナリオを考えて
みましょう。サービス バスはメッセージを読み取り済みとしてマークするため、アプリケーションが
再起動してメッセージの読み取りを再開すると、クラッシュ前に読み取られていた
メッセージは見落とされることになります。


**peek\_lock** パラメーターが **True** に設定されている場合、受信処理
が 2 段階の動作になり、メッセージが失われることが許容できないアプリケーションに
対応することができます。サービス バスは要求を受け取ると、
次に読み取られるメッセージを検索して、他のコンシューマーが受信できないよう
ロックしてから、アプリケーションにメッセージを返します。
アプリケーションがメッセージの処理を終えた後 (または後で処理するために
確実に保存した後)、**Message** オブジェクトの **delete** メソッドを
呼び出して受信処理の第 2 段階を完了します。**delete** メソッドによって、
メッセージが読み取り中としてマークされ、キューから削除されます。

	msg = bus_service.receive_queue_message('taskqueue', peek_lock=True)
	print(msg.body)

	msg.delete()

## <a name="handle-crashes"> </a>アプリケーションのクラッシュと読み取り不能のメッセージを処理する方法

サービス バスには、アプリケーションにエラーが発生した場合や、メッセージの
処理に問題がある場合に復旧を支援する機能が備わっています。受信
側のアプリケーションが何かの理由によってメッセージを処理できない場合には、
**Message** オブジェクト
の **unlock** メソッドを呼び出すことができます。このメソッドが呼び出されると、サービス バスに
よってキュー内のメッセージのロックが解除され、メッセージが再度受信できる
状態に変わります。メッセージを受信するアプリケーションは、以前と同じものでも、
別のものでもかまいません。

キュー内でロックされているメッセージにはタイムアウトも設定されています。
アプリケーションがクラッシュした場合など、ロックがタイムアウトになる前に
アプリケーションがメッセージの処理に失敗した場合には、メッセージの
ロックが自動的に解除され、再度受信できる状態に変わります。

メッセージが処理された後、**delete** メソッドが呼び出される前に
アプリケーションがクラッシュした場合は、アプリケーションが再起動する際に
メッセージが再配信されます。一般的に、
この動作は **"1 回以上の処理"** と呼ばれます。つまり、すべてのメッセージ
が 1 回以上処理されますが、特定の状況では、同じメッセージが
再配信される可能性があります。重複処理が許されないシナリオの場合、重複メッセージの
配信を扱うロジックをアプリケーションに追加する
必要があります。通常、この問題はメッセージ
の **MessageId** プロパティを使用して対処します。このプロパティは配信が試行
された後も同じ値を保持します。

## <a name="next-steps"> </a>次のステップ

これで、サービス バス キューの基本を学習できました。さらに詳細な情報が必要な
場合は、次のリンク先を参照してください。

-   MSDN リファレンス: [サービス バス キュー、トピックおよびサブスクリプション][]

  [次のステップ]: #next-steps
[サービス バス キューとは]: #what-are-service-bus-queues
[サービス名前空間の作成]: #create-a-service-namespace
[名前空間の既定の管理資格情報の取得]: #obtain-default-credentials
[キューの作成方法]: #create-queue
[メッセージをキューに送信する方法]: #send-messages
[キューからメッセージを受信する方法]: #receive-messages
[アプリケーションのクラッシュと読み取り不能のメッセージを処理する方法]: #handle-crashes
[キューの概念]: ../../../DevCenter/dotNet/Media/sb-queues-08.png
  [Windows Azure の管理ポータル]: http://manage.windowsazure.com
  
  
  
  
  
  [サービス バス キュー、トピックおよびサブスクリプション]: http://msdn.microsoft.com/en-us/library/windowsazure/hh367516.aspx

