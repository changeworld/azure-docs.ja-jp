<properties linkid="develop-python-service-bus-topics" urlDisplayName="サービス バス トピック" pageTitle="サービス バス トピックの使用方法 (Python) - Azure" metaKeywords="Azure サービス バス トピックの概要, 発行サブスクライブ メッセージング Python" description="Azure でのサービス バスのトピックとサブスクリプションの使用方法について説明します。コード サンプルは Python アプリケーション向けに作成されています。" metaCanonical="" services="service-bus" documentationCenter="Python" title="サービス バス トピック/サブスクリプションの使用方法" authors="" solutions="" manager="" editor="" />





# サービス バス トピック/サブスクリプションの使用方法
このガイドでは、Python アプリケーションからサービス バスのトピックと
サブスクリプションを使用する方法について説明します。ここでは、**トピックとサブスクリプションの作成、
サブスクリプション フィルターの作成、トピックへのメッセージの
送信**、**サブスクリプションからのメッセージの受信**、
**トピックとサブスクリプションの削除**などのシナリオについて説明します。トピックとサブスクリプションの詳細については、
「[次のステップ](#Next_Steps)」を参照してください。

## 目次

-   [サービス バス トピックとサブスクリプションとは](#what-are-service-bus-topics)
-   [サービス名前空間の作成](#create-a-service-namespace)
-   [名前空間の既定の管理資格情報の取得](#obtain-default-credentials)
-   [How to: トピックを作成する](#How_to_Create_a_Topic)
-   [How to: サブスクリプションを作成する](#How_to_Create_Subscriptions)
-   [How to: メッセージをトピックに送信する](#How_to_Send_Messages_to_a_Topic)
-   [How to: サブスクリプションからメッセージを受信する](#How_to_Receive_Messages_from_a_Subscription)
-   [How to: アプリケーションのクラッシュと読み取り不能のメッセージを処理する](#How_to_Handle_Application_Crashes_and_Unreadable_Messages)
-   [How to: トピックとサブスクリプションを削除する](#How_to_Delete_Topics_and_Subscriptions)
-   [次のステップ](#Next_Steps)


[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

**注:** Python またはクライアント ライブラリをインストールする場合は、「[Python Installation Guide (Python インストール ガイド)](../python-how-to-install/)」を参照してください。


##<a name="How_to_Create_a_Topic"></a>トピックの作成方法

**ServiceBusService** オブジェクトを使用して、トピックを操作できます。プログラムを使用して Azure のサービス バスにアクセスするすべての Python ファイルの先頭付近に、次のコードを追加します。

	from azure.servicebus import *

次のコードでは、**ServiceBusService** オブジェクトを作成します。"mynamespace"、"mykey"、"myissuer" の部分は、実際の名前空間、キー、発行者に置き換えます。

	bus_service = ServiceBusService(service_namespace='mynamespace', account_key='mykey', issuer='myissuer')

	bus_service.create_topic('mytopic')

**create\_topic** は追加のオプションもサポートしています。
これにより、メッセージの有効期間や最大トピック サイズなどの既定のトピックの
設定をオーバーライドできます。次の例では、トピックの最大サイズを 5 GB に、
メッセージの有効期間を 1 分に設定する方法を示しています。

	topic_options = Topic()
	topic_options.max_size_in_megabytes = '5120'
	topic_options.default_message_time_to_live = 'PT1M'

	bus_service.create_topic('mytopic', topic_options)

##<a name="How_to_Create_Subscriptions"></a>サブスクリプションの作成方法

トピック サブスクリプションも、**ServiceBusService** オブジェクトで
作成します。サブスクリプションを指定し、サブスクリプションの仮想キューに配信する
メッセージを制限するフィルターを設定することができます。

**注**: サブスクリプションは永続的であり、サブスクリプション、または
サブスクリプションが関連付けられているトピックが削除されるまで存在し続けます。

### 既定の (MatchAll) フィルターを適用したサブスクリプションの作成

**MatchAll** フィルターは、新しいサブスクリプションの作成時にフィルターが
指定されていない場合に使用される既定のフィルターです。**MatchAll** フィルターを
使用すると、トピックに発行されたすべてのメッセージがサブスクリプションの
仮想キューに置かれます。次の例では、"AllMessages" という
名前のサブスクリプションを作成し、既定の **MatchAll** フィルターを
使用します。

	bus_service.create_subscription('mytopic', 'AllMessages')

### フィルターを適用したサブスクリプションの作成

トピックに送信されたメッセージのうち、特定のトピック サブスクリプション内に表示
されるメッセージに絞り込めるフィルターを設定することもできます。

サブスクリプションでサポートされるフィルターのうち、最も柔軟性の高いものが、
SQL92 のサブセットを実装する **SqlFilter** です。SQL フィルターは、
トピックに発行されるメッセージのプロパティに対して適用されます。
SQL フィルターで使用できる式の詳細については、
[SqlFilter.SqlExpression][] 構文の説明を参照してください。

フィルターをサブスクリプションに追加するには、**ServiceBusService** 
オブジェクトの **create\_rule** メソッドを使用します。このメソッドによって、新しいフィルターを
既存のサブスクリプションに追加できます。

**注**: 既定のフィルターはすべての新しいサブスクリプションに
自動的に適用されるので、最初に既定のフィルターを削除する必要が
あります。削除しない場合、**MatchAll** は指定される他のすべてのフィルターをオーバーライドします。既定のルールを
削除するには、**ServiceBusService** オブジェクトの 
**delete\_rule** メソッドを使用します。

次の例では、"HighMessages" という名前のサブスクリプションを作成し、
**SqlFilter** を適用します。このフィルターでは、カスタム プロパティ 
**messagenumber** が 3 を超えるメッセージのみが選択されます。

	bus_service.create_subscription('mytopic', 'HighMessages')

	rule = Rule()
	rule.filter_type = 'SqlFilter'
	rule.filter_expression = 'messagenumber > 3'

	bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
	bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)

同様に、次の例では "LowMessages" という名前のサブスクリプションを
作成し、**SqlFilter** を適用します。このフィルターでは、
**messagenumber** プロパティが 3 以下のメッセージのみが選択されます。

	bus_service.create_subscription('mytopic', 'LowMessages')

	rule = Rule()
	rule.filter_type = 'SqlFilter'
	rule.filter_expression = 'messagenumber <= 3'

	bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
	bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)

メッセージが "mytopic" に送信されると、そのメッセージは "AllMessages" トピック 
サブスクリプションをサブスクライブした受信者に必ず配信され、さらにメッセージの
内容に応じて、"HighMessages" および "LowMessages" トピック サブスクリプションを
サブスクライブしている受信者に対して選択的に配信されます。

##<a name="How_to_Send_Messages_to_a_Topic"></a>メッセージをトピックに送信する方法

アプリケーションでサービス バス トピックにメッセージを送信するには、
**ServiceBusService** オブジェクトの **send\_topic\_message** メソッドを使用します。

次の例では、"mytopic" に 5 通のテスト メッセージを送信する方法を示して
います。各メッセージの **messagenumber** プロパティの値がループの反復
回数に応じてどのように変化するかに注目してください (これによって
メッセージを受信するサブスクリプションが決定されます)。

	for i in range(5):
		msg = Message('Msg ' + str(i), custom_properties={'messagenumber':i})
		bus_service.send_topic_message('mytopic', msg)

サービス バス トピックでは、最大 256 MB までのメッセージをサポートしています 
(標準とカスタムのアプリケーション プロパティが含まれるヘッダーの最大サイズは 
64 MB です)。トピックで保持されるメッセージ数には
上限がありませんが、1 つのトピックで保持できるメッセージの合計サイズには
上限があります。このトピックのサイズはトピックの作成時に定義します。
上限は 5 GB です。

##<a name="How_to_Receive_Messages_from_a_Subscription"></a>サブスクリプションからメッセージを受信する方法

サブスクリプションからメッセージを受信するには、
**ServiceBusService** オブジェクトの **receive\_subscription\_message** 
メソッドを使用します。

	msg = bus_service.receive_subscription_message('mytopic', 'LowMessages')
	print(msg.body)

メッセージは読み取られるときにサブスクリプションから削除されますが、
省略可能な **peek\_lock** パラメーターを **True** に設定することによって、
サブスクリプションからメッセージを削除せずに、
メッセージを読み取って (ピークして) ロックすることができます。

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
アプリケーションがメッセージの処理を終えた後 (または
後で処理するために確実に保存した後)、**Message** オブジェクトの 
**delete** メソッドを呼び出して受信処理の第 2 段階を完了します。**delete** メソッドによって、
メッセージが読み取り中としてマークされ、サブスクリプションから削除されます。

	msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
	print(msg.body)
	
	msg.delete()
	

##<a name="How_to_Handle_Application_Crashes_and_Unreadable_Messages"></a>アプリケーションのクラッシュと読み取り不能のメッセージを処理する方法

サービス バス には、アプリケーションにエラーが発生した場合や、メッセージの
処理に問題がある場合に復旧を支援する機能が備わっています。受信側の
アプリケーションが何らかの理由によってメッセージを処理できない場合には、
**Message** オブジェクトの **unlock** 
メソッドを呼び出すことができます。このメソッドが呼び出されると、サブスクリプション内の
メッセージのロックが解除され、メッセージが再度受信できる状態に変わります。
このメッセージは、同じコンシューマー側アプリケーションまたは他のコンシューマー側
アプリケーションで受信されます。

サブスクリプション内でロックされているメッセージにはタイムアウトも設定されています。
アプリケーションがクラッシュした場合など、ロックがタイムアウトになる前に
アプリケーションがメッセージの処理に失敗した場合には、メッセージのロックが自動的に
解除され、再度受信できる状態に変わります。

メッセージが処理された後、**delete** メソッドが呼び出される前に
アプリケーションがクラッシュした場合は、アプリケーションが
再起動する際にメッセージが再配信されます。一般的に、
この動作は **"1 回以上の処理"** と呼ばれます。つまり、すべての
メッセージが 1 回以上処理されますが、特定の状況では、同じメッセージが
再配信される可能性があります。重複処理が許されないシナリオの場合、重複メッセージの配信を
扱うロジックをアプリケーションに追加する必要があります。通常、この問題はメッセージの 
**MessageId** プロパティを使用して対処します。このプロパティは配信が
試行された後も同じ値を保持します。

##<a name="How_to_Delete_Topics_and_Subscriptions"></a>トピックとサブスクリプションを削除する方法

トピックおよびサブスクリプションは永続的であり、Azure 管理ポータルまたは
プログラムによって明示的に削除する必要があります。
次の例では、"mytopic" という名前のトピックを削除する方法を示しています。

	bus_service.delete_topic('mytopic')

トピックを削除すると、そのトピックに登録されたサブスクリプションも
すべて削除されます。サブスクリプションは、個別に削除することもできます。次の
コードでは、"HighMessages" という名前のサブスクリプションを 
"mytopic" トピックから削除する方法を示しています。

	bus_service.delete_subscription('mytopic', 'HighMessages')

##<a name="Next_Steps"></a>次のステップ

これで、サービス バス トピックの基本を学習できました。さらに詳細な情報が
必要な場合は、次のリンク先を参照してください。

-   MSDN リファレンス: [キュー、トピック、およびサブスクリプション][]。
-   [SqlFilter][] の API のリファレンス。

  [次のステップ]: #nextsteps
  [サービス バス トピックとサブスクリプションとは]: #what-are-service-bus-topics
  [サービス名前空間の作成]: #create-a-service-namespace
  [名前空間の既定の管理資格情報の取得]: #obtain-default-credentials
  [How to: トピックを作成する]: #How_to_Create_a_Topic
  [How to: サブスクリプションを作成する]: #How_to_Create_Subscriptions
  [How to: メッセージをトピックに送信する]: #How_to_Send_Messages_to_a_Topic
  [How to: サブスクリプションからメッセージを受信する]: #How_to_Receive_Messages_from_a_Subscription
  [How to: アプリケーションのクラッシュと読み取り不能のメッセージを処理する]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
  [方法: トピックとサブスクリプションを削除する]: #How_to_Delete_Topics_and_Subscriptions
  
  [トピックの概念]: ../../../DevCenter/dotNet/Media/sb-topics-01.png
  [Azure 管理ポータル]: http://manage.windowsazure.com
  
  
  
  
  
  
  [サービス バス キュー、トピック、およびサブスクリプション]: http://msdn.microsoft.com/ja-jp/library/hh367516.aspx
  [SqlFilter]: http://msdn.microsoft.com/ja-jp/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx

