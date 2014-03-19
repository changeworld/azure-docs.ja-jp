<properties linkid="develop-python-service-bus-topics" UrlDisplayName="サービス バス トピック" pageTitle="サービス バス トピックの使用方法 (Python) - Windows Azure" MetaKeywords="Azure サービス バス トピックの概要, 発行サブスクライブ メッセージング Python" description="Windows Azure でのサービス バス のトピックとサブスクリプションの使用方法について説明します。コード サンプルは Python アプリケーション向けに作成されています。" metaCanonical="" services="service-bus" documentationCenter="Python" title="サービス バス トピック/サブスクリプションの使用方法" authors=""  solutions="" writer="" manager="" editor=""  />





# サービス バス トピック/サブスクリプションの使用方法
このガイドでは、Python アプリケーションからサービス バス のトピックとサブスクリプション
を使用する方法について説明します。紹介するシナリオは、**トピックとサブスクリ
プションの作成、サブスクリプション フィルターの作成、**トピック
への**メッセージの送信、サブスクリプションからのメッセージの受信**、および
**トピックとサブスクリプションの削除**です。トピックとサブスクリプションの詳細に
ついては、「[次のステップ](#Next_Steps)」を参照してください。

## 目次

-   [サービス バス トピックとサブスクリプションとは](#what-are-service-bus-topics)
-   [サービス名前空間の作成](#create-a-service-namespace)
-   [名前空間の既定の管理資格情報の取得](#obtain-default-credentials)
-   [トピックを作成する方法](#How_to_Create_a_Topic)
-   [サブスクリプションの作成方法](#How_to_Create_Subscriptions)
-   [メッセージをトピックに送信する方法](#How_to_Send_Messages_to_a_Topic)
-   [サブスクリプションからメッセージを受信する方法](#How_to_Receive_Messages_from_a_Subscription)
-   [アプリケーションのクラッシュと読み取り不能のメッセージを処理する方法](#How_to_Handle_Application_Crashes_and_Unreadable_Messages)
-   [方法: トピックとサブスクリプションを削除する](#How_to_Delete_Topics_and_Subscriptions)
-   [次のステップ](#Next_Steps)


[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

**注:** Python またはクライアント ライブラリをインストールする場合は、「[Python Installation Guide (Python インストール ガイド)](../python-how-to-install/)」を参照してください。


##<a name="How_to_Create_a_Topic"></a>トピックを作成する方法

**ServiceBusService** オブジェクトを使用して、トピックを操作できます。プログラムを使用して Windows Azure のサービス バス にアクセスするすべての Python ファイルの先頭付近に、次のコードを追加します。

	from azure.servicebus import *

次のコードでは、**ServiceBusService** オブジェクトを作成します。"mynamespace"、"mykey"、"myissuer" の部分は、実際の名前空間、キー、発行者に置き換えます。

	bus_service = ServiceBusService(service_namespace='mynamespace', account_key='mykey', issuer='myissuer')

	bus_service.create_topic('mytopic')

**create\_topic** は追加のオプションもサポートしています。
これにより、メッセージの有効期間や最大トピック サイズなどの既定のトピックの
設定をオーバーライドできます。次の例では、トピックの最大サイズを 5 GB に、
メッセージの既定の有効期間を 1 分に設定する方法を示しています。

	topic_options = Topic()
	topic_options.max_size_in_megabytes = '5120'
	topic_options.default_message_time_to_live = 'PT1M'

	bus_service.create_topic('mytopic', topic_options)

##<a name="How_to_Create_Subscriptions"></a>サブスクリプションの作成方法

トピック サブスクリプションも **ServiceBusService** オブジェクトで作成
します。サブスクリプションを指定し、サブスクリプションの仮想キューに
配信するメッセージを制限するフィルターを設定することが
できます。

**注**: サブスクリプションは永続的であり、サブスクリプション、または
サブスクリプションが関連付けられているトピックが削除されるまで存在し続けます。

### 既定の (MatchAll) フィルターを適用したサブスクリプションの作成

**MatchAll** フィルターは、新しいサブスクリプションが作成されたときに
フィルターが指定されていない場合に使用される既定のフィルターです。**MatchAll** フィルターを
使用すると、トピックに発行されたすべてのメッセージがサブスクリプションの
仮想キューに置かれます。次の例では、"AllMessages" という
名前のサブスクリプションを作成し、既定の **MatchAll** フィルターを使用
します。

	bus_service.create_subscription('mytopic', 'AllMessages')

### フィルターを適用したサブスクリプションの作成

トピックに送信されたメッセージのうち、特定のトピック サブスクリプション内に表示
されるメッセージに絞り込めるフィルターを設定することもできます。

サブスクリプションでサポートされるフィルターのうち、最も柔軟性の高い
ものが、SQL92 のサブセットを実装する **SqlFilter** です。SQL フィルターは、
トピックに発行されるメッセージのプロパティに対して適用されます。SQL フィ
ルターで使用できる式の詳細については、[SqlFilter.SqlExpression][] 構文
の説明を参照してください。

フィルターをサブスクリプションに追加するには、**ServiceBusService** オブ
ジェクトの **create\_rule** メソッドを使用します。このメソッドによって、新しい
フィルターを既存のサブスクリプションに追加できます。

**注**: 既定のフィルターはすべての新しいサブスクリプションに自動的に適用
されるので、最初に既定のフィルターを削除する必要があります。削除しないと、
他のフィルターを指定しても、すべて **MatchAll** によってオーバーライドされます。既定の
ルールを削除するには、**ServiceBusService** オブジェクト
の **delete\_rule** メソッドを使用します。

次の例では、"HighMessages" という名前のサブスクリプションを作成し、**SqlFilter** を
適用します。このフィルターでは、カスタム プロパティ **messagenumber** が 3 を
超えるメッセージのみが選択されます。

	bus_service.create_subscription('mytopic', 'HighMessages')

	rule = Rule()
	rule.filter_type = 'SqlFilter'
	rule.filter_expression = 'messagenumber > 3'

	bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
	bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)

同様に、次の例では "LowMessages" という名前のサブスクリプションを作成し、
**SqlFilter** を適用します。このフィルターでは、**messagenumber** が 3 未満
のメッセージのみが選択されます。

	bus_service.create_subscription('mytopic', 'LowMessages')

	rule = Rule()
	rule.filter_type = 'SqlFilter'
	rule.filter_expression = 'messagenumber <= 3'

	bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
	bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)

メッセージが "mytopic" に送信されると、そのメッセージは "AllMessages" トピック サブ
スクリプションをサブスクライブした受信者に必ず配信され、さらに
メッセージの内容に応じて、"HighMessages" および "LowMessages" トピック サブ
スクリプションをサブスクライブしている受信者に対して選択的に配信されます。

##<a name="How_to_Send_Messages_to_a_Topic"></a>メッセージをトピックに送信する方法

メッセージをサービス バス トピックに送信するには、アプリケーションで
**ServiceBusService** オブジェクトの **send\_topic\_message** メソッドを使用する必要があります。

次の例では、"mytopic" にテスト メッセージを 5 通送信する方法を示して
います。各メッセージの **messagenumber** プロパティの値がループの
反復回数に応じて変化することに注目してください (これによってメッセージを
受信するサブスクリプションが決定されます)。

	for i in range(5):
		msg = Message('Msg ' + str(i), custom_properties={'messagenumber':i})
		bus_service.send_topic_message('mytopic', msg)

サービス バス トピックでは、最大 256 MB までのメッセージをサポートして
います (標準とカスタムのアプリケーション プロパティが含まれるヘッダーは、
64 MB が最大サイズです)。トピックで保持されるメッセージ数には上限が
ありませんが、1 つのトピックで保持できるメッセージの合計サイズには
上限があります。このトピックのサイズはトピックの作成時に定義します。
上限は 5 GB です。

##<a name="How_to_Receive_Messages_from_a_Subscription"></a>サブスクリプションからメッセージを受信する方法

サブスクリプションからメッセージを受信するには、
**ServiceBusService** オブジェクトの **receive\_subscription\_message** メソッド
を使用します。

	msg = bus_service.receive_subscription_message('mytopic', 'LowMessages')
	print(msg.body)

メッセージは読み取られるときにサブスクリプションから削除
されますが、省略可能な **peek\_lock** パラメーターを **True** に設定
することによって、サブスクリプションからメッセージを削除せずに、
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
メッセージが読み取り中としてマークされ、サブスクリプションから削除されます。

	msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
	print(msg.body)
	
	msg.delete()
	

##<a name="How_to_Handle_Application_Crashes_and_Unreadable_Messages"></a>アプリケーションのクラッシュと読み取り不能のメッセージを処理する方法

サービス バスには、アプリケーションにエラーが発生した場合や、メッセージの
処理に問題がある場合に復旧を支援する機能が備わっています。受信
側のアプリケーションが何かの理由によってメッセージを処理できない場合には、
**Message** オブジェクトの **unlock** メソッド
を呼び出すことができます。このメソッドが呼び出されると、サブスクリプション
内のメッセージのロックが解除され、メッセージが再度受信できる状態に変わります。
このメッセージは、同じコンシューマー側アプリケーションまたは他のコンシューマー側
アプリケーションで受信されます。

サブスクリプション内でロックされているメッセージにはタイムアウトも設定
されています。アプリケーションがクラッシュした場合など、ロックがタイムアウト
になる前にアプリケーションがメッセージの処理に失敗した場合には、
メッセージのロックが自動的に解除され、再度受信できる状態に
変わります。

メッセージが処理された後、**delete** メソッドが呼び出される前に
アプリケーションがクラッシュした場合は、アプリケーションが再起動する際に
メッセージが再配信されます。一般的に、
この動作は **"1 回以上の処理"** と呼ばれます。つまり、すべてのメッセージが 1 回
以上処理されますが、特定の状況では、同じメッセージが再配信
される可能性があります。重複処理が許されないシナリオの場合、重複メッセージの
配信を扱うロジックをアプリケーションに追加する
必要があります。通常、この問題はメッセージ
の **MessageId** プロパティを使用して対処します。このプロパティは配信が試行
された後も同じ値を保持します。

##<a name="How_to_Delete_Topics_and_Subscriptions"></a>方法: トピックとサブスクリプションを削除する

トピックおよびサブスクリプションは永続的であり、Windows Azure の管理ポータル
またはプログラムによって明示的に削除する必要があります。
次の例では、"mytopic" という名前のトピックを削除する方法を示しています。

	bus_service.delete_topic('mytopic')

トピックを削除すると、そのトピックに登録されたサブスクリプションもすべて
削除されます。サブスクリプションは、個別に削除することもできます。次の
コードでは、"HighMessages" という名前のサブスクリプションを "mytopic" トピック
から削除する方法を示しています。

	bus_service.delete_subscription('mytopic', 'HighMessages')

##<a name="Next_Steps"></a>次のステップ

これで、サービス バス トピックの基本を学習できました。さらに詳細な情報が
必要な場合は、次のリンク先を参照してください。

-   MSDN リファレンス: [サービス バス キュー、トピックおよびサブスクリプション][]
-   [SqlFilter][] の API のリファレンス

  [次のステップ]: #nextsteps
[サービス バス トピックとサブスクリプションとは]: #what-are-service-bus-topics
[サービス名前空間の作成]: #create-a-service-namespace
[名前空間の既定の管理資格情報の取得]: #obtain-default-credentials
[トピックを作成する方法]: #How_to_Create_a_Topic
[サブスクリプションの作成方法]: #How_to_Create_Subscriptions
[メッセージをトピックに送信する方法]: #How_to_Send_Messages_to_a_Topic
[サブスクリプションからメッセージを受信する方法]: #How_to_Receive_Messages_from_a_Subscription
[アプリケーションのクラッシュと読み取り不能のメッセージを処理する方法]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
[方法: トピックとサブスクリプションを削除する]: #How_to_Delete_Topics_and_Subscriptions
  
  [トピックの概念]: ../../../DevCenter/dotNet/Media/sb-topics-01.png
  [Windows Azure の管理ポータル]: http://manage.windowsazure.com
  
  
  
  
  
  
  [サービス バス キュー、トピックおよびサブスクリプション]: http://msdn.microsoft.com/en-us/library/hh367516.aspx
[SqlFilter]: http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx

