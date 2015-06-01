<properties 
	pageTitle="Service Bus トピックの使用方法 (Python) - Azure" 
	description="Python から Azure Service Bus のトピックとサブスクリプションを使用する方法を説明します。" 
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
	ms.date="02/09/2015" 
	ms.author="huvalo"/>





#サービス バス トピック/サブスクリプションの使用方法
このガイドでは、Service Bus のトピックとサブスクリプションの使用方法について説明します。サンプルは Python で記述され、[Python Azure パッケージ][]を使用しています。ここでは、**トピックとサブスクリプションの作成、サブスクリプション フィルターの作成、トピックへのメッセージの送信**、**サブスクリプションからのメッセージの受信**、
**トピックとサブスクリプションの削除**などのシナリオについて説明します。トピックとサブスクリプションの詳細については、[次のステップ](#Next_Steps) セクションをご覧ください。

[AZURE.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

**注:** Python または [Python Azure パッケージ][]をインストールする場合は、「[Python インストール ガイド](python-how-to-install.md)」をご覧ください。


##トピックの作成方法

**ServiceBusService** オブジェクトを使用して、トピックを操作できます。プログラムを使用して Azure のサービス バスにアクセスするすべての Python ファイルの先頭付近に、次のコードを追加します。

	from azure.servicebus import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME

次のコードでは、**ServiceBusService** オブジェクトを作成します。 'mynamespace'、 'sharedaccesskeyname'、 'sharedaccesskey' の部分は、実際の名前空間、共有アクセス署名 (SAS) キーの名前と値に置き換えます。

	bus_service = ServiceBusService(
		service_namespace='mynamespace',
		shared_access_key_name='sharedaccesskeyname',
		shared_access_key_value='sharedaccesskey')

SAS キーの名前と値は、Azure ポータル接続情報に含まれています。また、サービス エクスプローラーで Service Bus 名前空間を選択すると、Visual Studio プロパティ ウィンドウに表示されます (前のセクションに示されているとおり)。

	bus_service.create_topic('mytopic')

**create_topic** は追加のオプションもサポートしています。これにより、メッセージの有効期間や最大トピック サイズなどの既定のトピックの設定をオーバーライドできます。次の例では、トピックの最大サイズを 5 GB に、メッセージの既定の有効期間を 1 分に設定する方法を示しています。

	topic_options = Topic()
	topic_options.max_size_in_megabytes = '5120'
	topic_options.default_message_time_to_live = 'PT1M'

	bus_service.create_topic('mytopic', topic_options)

##サブスクリプションの作成方法

トピック サブスクリプションも、**ServiceBusService** オブジェクトで作成します。サブスクリプションを指定し、サブスクリプションの仮想キューに配信するメッセージを制限するフィルターを設定できます。

**注**:サブスクリプションは永続的であり、サブスクリプション、またはサブスクリプションが関連付けられているトピックが削除されるまで存在し続けます。

###既定の (MatchAll) フィルターを適用したサブスクリプションの作成

**MatchAll** フィルターは、新しいサブスクリプションの作成時にフィルターが指定されていない場合に使用される既定のフィルターです。**MatchAll** フィルターを使用すると、トピックに発行されたすべてのメッセージがサブスクリプションの仮想キューに置かれます。次の例では、 'AllMessages' という名前のサブスクリプションを作成し、既定の **MatchAll** フィルターを使用します。

	bus_service.create_subscription('mytopic', 'AllMessages')

###フィルターを適用したサブスクリプションの作成

トピックに送信されたメッセージのうち、特定のトピック サブスクリプション内に表示されるメッセージに絞り込めるフィルターを設定することもできます。

サブスクリプションでサポートされるフィルターのうち、最も柔軟性の高いものが、SQL92 のサブセットを実装する **SqlFilter** です。SQL フィルターは、トピックに発行されるメッセージのプロパティに対して適用されます。SQL フィルターで使用できる式の詳細については、[SqlFilter.SqlExpression][] 構文の説明をご覧ください。

フィルターをサブスクリプションに追加するには、**ServiceBusService** オブジェクトの **create_rule** メソッドを使用します。このメソッドによって、新しいフィルターを既存のサブスクリプションに追加できます。

**注**:既定のフィルターはすべての新しいサブスクリプションに自動的に適用されるので、最初に既定のフィルターを削除する必要があります。削除しないと、**MatchAll** は指定される他のすべてのフィルターをオーバーライドします。既定のルールを削除するには、**deleteRule** メソッド (**ServiceBusService** オブジェクトの) を使用します。

次の例では、 'HighMessages' という名前のサブスクリプションを作成し、
**SqlFilter** を適用します。このフィルターでは、カスタム プロパティ
**messagenumber** が 3 を超えるメッセージのみが選択されます。

	bus_service.create_subscription('mytopic', 'HighMessages')

	rule = Rule()
	rule.filter_type = 'SqlFilter'
	rule.filter_expression = 'messagenumber > 3'

	bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
	bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)

同様に、次の例では、"LowMessages" という名前のサブスクリプションを作成し、**SqlFilter** を適用します。このフィルターでは、**messagenumber** プロパティが 3 以下のメッセージのみが選択されます。

	bus_service.create_subscription('mytopic', 'LowMessages')

	rule = Rule()
	rule.filter_type = 'SqlFilter'
	rule.filter_expression = 'messagenumber <= 3'

	bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
	bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)

メッセージが  'mytopic' に送信されると、そのメッセージは  'AllMessages' トピック サブスクリプションにサブスクライブしている受信者に必ず配信され、さらにメッセージのコンテンツに応じて、 'HighMessages' と 'LowMessages' トピック サブスクリプションにサブスクライブしている受信者に対して選択的に配信されます。

##メッセージをトピックに送信する方法

メッセージを Service Bus トピックに送信するには、アプリケーションで **ServiceBusService** オブジェクトの **send_topic_message** メソッドを呼び出します。

次の例では、'mytopic' トピックに 5 件のテスト メッセージを送信する方法を示しています。各メッセージの **messagenumber** プロパティの値がループの反復回数に応じて変化することに注目してください (これによってメッセージを受信するサブスクリプションが決定されます)。

	for i in range(5):
		msg = Message('Msg {0}'.format(i).encode('utf-8'), custom_properties={'messagenumber':i})
		bus_service.send_topic_message('mytopic', msg)

サービス バス トピックでは、最大 256 MB までのメッセージをサポートしています (標準とカスタムのアプリケーション プロパティが含まれるヘッダーの最大サイズは 64 MB です)。トピックで保持されるメッセージ数には上限がありませんが、1 つのトピックで保持できるメッセージの合計サイズには上限があります。このトピックのサイズはトピックの作成時に定義します。上限は 5 GB です。

##サブスクリプションからメッセージを受信する方法

サブスクリプションからメッセージを受信するには、**ServiceBusService** オブジェクトの **receive_subscription_message** メソッドを使用します。

	msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
	print(msg.body)

パラメーター**peek_lock** が **False** に設定されていると、メッセージが読み取られるときにサブスクリプションから削除されます。キューからメッセージを削除せずに、メッセージを読み取って (ピークして) ロックするには、
**peek_lock** パラメーターを **True** に設定します。

受信操作の中で行われるメッセージの読み取りと削除の動作は、最もシンプルなモデルであり、障害発生時にアプリケーション側でメッセージを処理しないことを許容できるシナリオに最適です。このことを理解するために、コンシューマーが受信要求を発行した後で、メッセージを処理する前にクラッシュしたというシナリオを考えてみましょう。サービス バスはメッセージを読み取り済みとしてマークするため、アプリケーションが再起動してメッセージの読み取りを再開すると、クラッシュ前に読み取られていたメッセージは見落とされることになります。


**peek_lock** パラメーターが **True** に設定されている場合、受信処理が 2 段階の動作になり、メッセージが失われることが許容できないアプリケーションに対応できます。サービス バスは要求を受け取ると、次に読み取られるメッセージを検索して、他のコンシューマーが受信できないようロックしてから、アプリケーションにメッセージを返します。アプリケーションがメッセージの処理を終えた後 (または後で処理するために確実に保存した後)、**Message** オブジェクトの **delete** メソッドを呼び出して受信処理の第 2 段階を完了します。**delete** メソッドによって、メッセージが読み取り中としてマークされ、サブスクリプションから削除されます。

	msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
	print(msg.body)

	msg.delete()


##アプリケーションのクラッシュと読み取り不能のメッセージを処理する方法

サービス バスには、アプリケーションにエラーが発生した場合や、メッセージの処理に問題がある場合に復旧を支援する機能が備わっています。受信側のアプリケーションが何らかの理由によってメッセージを処理できない場合には、**unlock** メソッドを
**Message** オブジェクトに対して呼び出すことができます。このメソッドが呼び出されると、サブスクリプション内のメッセージのロックが解除され、メッセージが再度受信できる状態に変わります。このメッセージは、同じコンシューマー側アプリケーションまたは他のコンシューマー側アプリケーションで受信されます。

サブスクリプション内でロックされているメッセージにはタイムアウトも設定されています。アプリケーションがクラッシュした場合など、ロックがタイムアウトになる前にアプリケーションがメッセージの処理に失敗した場合は、Service Bus によってメッセージのロックが自動的に解除され、再度受信できる状態に変わります。

メッセージが処理された後、**delete** メソッドが呼び出される前にアプリケーションがクラッシュした場合は、アプリケーションが再起動する際にメッセージがアプリケーションに再配信されます。一般的に、この動作は **"1 回以上の処理"** と呼ばれます。つまり、すべてのメッセージが 1 回以上処理されますが、特定の状況では、同じメッセージが再配信される可能性があります。重複処理が許されないシナリオの場合、重複メッセージの配信を扱うロジックをアプリケーションに追加する必要があります。通常、この問題はメッセージの**MessageId** プロパティを使用して対処します。このプロパティは配信が試行された後も同じ値を保持します。

##トピックとサブスクリプションを削除する方法

トピックとサブスクリプションは永続的であり、Azure 管理ポータルまたはプログラムによって明示的に削除する必要があります。
次の例では、 'mytopic' という名前のトピックを削除する方法を示します。

	bus_service.delete_topic('mytopic')

トピックを削除すると、そのトピックに登録されたサブスクリプションもすべて削除されます。サブスクリプションは、個別に削除することもできます。次のコードでは、"HighMessages" という名前のサブスクリプションを  'mytopic' トピックから削除する方法を示しています。

	bus_service.delete_subscription('mytopic', 'HighMessages')

##次のステップ

これで、サービス バス トピックの基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先をご覧ください。

-   MSDN リファレンス:[キュー、トピック、サブスクリプション][]
-   [SqlFilter.SqlExpression][] のリファレンス

[Azure 管理ポータル]: http://manage.windowsazure.com
[Python Azure パッケージ]: https://pypi.python.org/pypi/azure  
[キュー、トピック、サブスクリプション]: http://msdn.microsoft.com/library/hh367516.aspx
[SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx

<!--HONumber=47-->
