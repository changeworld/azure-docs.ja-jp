<properties 
	pageTitle="Service Bus トピックの使用方法 (Ruby) - Azure" 
	description="Azure での Service Bus のトピックとサブスクリプションの使用方法について学習します。コード サンプルは Ruby アプリケーション向けに作成されています。" 
	services="service-bus" 
	documentationCenter="ruby" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="11/25/2014" 
	ms.author="tomfitz"/>





#サービス バス トピック/サブスクリプションの使用方法

このガイドでは、Ruby アプリケーションからサービス バス トピックとサブスクリプションを使用する方法について説明します。ここでは、**トピックとサブスクリプションの作成、サブスクリプション フィルターの作成、トピックへのメッセージの送信**、**サブスクリプションからのメッセージの受信**、**トピックとサブスクリプションの削除**などのシナリオについて説明します。トピックとサブスクリプションの詳細については、「[次のステップ](#NextSteps) セクションをご覧ください。

##サービス バス トピックとサブスクリプションとは

サービス バスのトピックとサブスクリプションは、**メッセージ通信の発行/サブスクライブ** モデルをサポートします。トピックとサブスクリプションを使用すると、分散アプリケーションのコンポーネントが互いに直接通信することがなくなり、仲介者の役割を果たすトピックを介してメッセージをやり取りすることになります。

![TopicConcepts](./media/service-bus-ruby-how-to-use-topics-subscriptions/sb-topics-01.png)

すべてのメッセージが 1 つのコンシューマーによって処理される Service Bus キューとは異なり、トピックとサブスクリプションでは発行/サブスクライブ パターンを使用した **1 対多**形式の通信を行います。複数のサブスクリプションを 1 つのトピックに登録できます。トピックに送信されたメッセージはサブスクリプションに渡され、各サブスクリプションで独立して処理できます。

トピック サブスクリプションは、トピックに送信されたメッセージのコピーを受け取る仮想キューのようなものです。トピックに対するフィルター ルールをサブスクリプション単位で登録することもできます。これを使用すると、トピックに送信されるどのメッセージをどのトピック サブスクリプションで受信するかのフィルター処理や制限ができます。

Service Bus のトピックとサブスクリプションを使用することで、多数のユーザーとアプリケーションの間でやり取りされる膨大な数のメッセージを処理することもできます。

##サービス名前空間の作成

Azure の Service Bus キューを使用するには、最初にサービス名前空間を作成する必要があります。サービス名前空間は、 
アプリケーション内で Service Bus リソースをアドレス指定するためのスコープ コンテナーを提供します。ポータルではサービス バスの作成に ACS 接続が使用されないため、コマンド ライン インターフェイスを使用して名前空間を作成する必要があります。

名前空間を作成するには:

1. Azure PowerShell コンソールを開きます。

2. コマンドを下に示すように入力して、Azure のサービス バス名前空間を作成します。独自の名前空間値と、アプリケーションと同じリージョンを指定します。 

      New-AzureSBNamespace -Name 'yourexamplenamespace' -Location 'West US' -CreateACSNamespace $true

      ![Create Namespace](./media/service-bus-ruby-how-to-use-topics-subscriptions/showcmdcreate.png)

##名前空間の既定の管理資格情報の取得

新規作成した名前空間に対してキューの作成などの管理操作を実行するには、名前空間の管理資格情報を取得する必要があります。

1. [Azure 管理ポータル](http://manage.windowsazure.com/)へのログオン

2. 作成した Service Bus の名前空間を選択します。

     ![Select namespace](./media/service-bus-ruby-how-to-use-topics-subscriptions/selectns.png)

3. 下部で、**[接続情報]** を選択します。

      ![Select connection](./media/service-bus-ruby-how-to-use-topics-subscriptions/selectconnection.png)

4. 既定のキーをコピーします。この値は後にコードで使用します。

       ![Copy key](./media/service-bus-ruby-how-to-use-topics-subscriptions/defaultkey.png)

##Ruby アプリケーションの作成

Ruby アプリケーションを作成します。手順については、[Azure での Ruby アプリケーションの作成に関するページ](/develop/ruby/tutorials/web-app-with-linux-vm/)をご覧ください。

##Service Bus を使用するようにアプリケーションを構成する

Azure サービス バスを使用するには、Ruby azure パッケージをダウンロードして使用する必要があります。このパッケージには、ストレージ REST サービスと通信するための便利なライブラリのセットが含まれています。

###RubyGems を使用してパッケージを取得する

1. **PowerShell** (Windows)、**ターミナル** (Mac)、**Bash** (Unix) などのコマンド ライン インターフェイスを使用します。

2. コマンド ウィンドウに「gem install azure」と入力して、gem と依存関係をインストールします。

###パッケージをインポートする

任意のテキスト エディターを使用して、ストレージを使用する Ruby ファイルの先頭に次のコードを追加します。

    require "azure"

##Azure Service Bus 接続の設定

azure モジュールは、環境変数 **AZURE_SERVICEBUS_NAMESPACE** と **AZURE_SERVICEBUS_ACCESS_KEY** 
で、Azure Service Bus の名前空間に接続するために必要な情報を読み込みます。これらの環境変数が設定されていない場合は、**Azure::ServiceBusService** を使用する前に、次のコードを使用して名前空間情報を指定する必要があります。

    Azure.config.sb_namespace = "<your azure service bus namespace>"
    Azure.config.sb_access_key = "<your azure service bus access key>"

作成した値には、URL 全体ではなく、Service Bus の名前空間値を設定してください。たとえば、"yourexamplenamespace.servicebus.windows.net" ではなく、**"yourexamplenamespace"** を使用します。 

##トピックの作成方法

**Azure::ServiceBusService** オブジェクトを使用して、トピックを操作できます。次のコードでは、**Azure::ServiceBusService** オブジェクトを作成します。トピックを作成するには、**create_topic()** メソッドを使用します。次の例では、トピックを作成し、既に存在する場合はエラーを出力します。

	azure_service_bus_service = Azure::ServiceBusService.new
	begin
      topic = azure_service_bus_service.create_queue("test-topic")
    rescue
      puts $!
    end

**Azure::ServiceBus::Topic** オブジェクトに追加のオプションを渡すこともできます。これにより、メッセージの有効期間やキューの最大サイズなどの既定のトピックの設定をオーバーライドできます。次の例は、キューの最大サイズを 5 GB に、有効期間を 1 分に設定する方法を示しています。

	topic = Azure::ServiceBus::Topic.new("test-topic")
    topic.max_size_in_megabytes = 5120
    topic.default_message_time_to_live = "PT1M"

    topic = azure_service_bus_service.create_topic(topic)

##サブスクリプションの作成方法

トピック サブスクリプションも、**Azure::ServiceBusService** オブジェクトで作成します。サブスクリプションを指定し、サブスクリプションの仮想キューに配信するメッセージを制限するフィルターを設定できます。

サブスクリプションは永続的であり、サブスクリプション、またはサブスクリプションが関連付けられているトピックが削除されるまで存在し続けます。アプリケーションにサブスクリプションを作成するロジックが含まれている場合は、最初に getSubscription メソッドを使用して、サブスクリプションが既に存在しているかどうかを確認する必要があります。

###既定の (MatchAll) フィルターを適用したサブスクリプションの作成

**MatchAll** フィルターは、新しいサブスクリプションの作成時にフィルターが指定されていない場合に使用される既定のフィルターです。**MatchAll** フィルターを使用すると、トピックに発行されたすべてのメッセージがサブスクリプションの仮想キューに置かれます。次の例では、"all-messages" という名前のサブスクリプションを作成し、既定の **MatchAll** フィルターを使用します。

	subscription = azure_service_bus_service.create_subscription("test-topic", 
	  "all-messages")

###<a id="how-to-create-subscriptions"></a>フィルターを適用したサブスクリプションの作成

トピックに送信されたメッセージのうち、特定のトピック サブスクリプション内に表示されるメッセージに絞り込めるフィルターを設定することもできます。

サブスクリプションでサポートされるフィルターのうち、最も柔軟性の高いものが、SQL92 のサブセットを実装する **Azure::ServiceBus::SqlFilter** です。SQL フィルターは、トピックに発行されるメッセージのプロパティに対して適用されます。SQL フィルターで使用できる式の詳細については、[SqlFilter.SqlExpression](http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx) 構文の説明をご覧ください。

フィルターをサブスクリプションに追加するには、**Azure::ServiceBusService** オブジェクトの **create_rule()** メソッドを使用します。このメソッドによって、新しいフィルターを既存のサブスクリプションに追加できます。

既定のフィルターはすべての新しいサブスクリプションに自動的に適用されるので、最初に既定のフィルターを削除する必要があります。削除しないと、**MatchAll** は指定される他のすべてのフィルターをオーバーライドします。既定のルールを削除するには、**Azure::ServiceBusService** オブジェクトの **delete_rule()** メソッドを使用します。

次の例では、"high-messages" という名前のサブスクリプションを作成し、**Azure::ServiceBus::SqlFilter** を適用します。このフィルターでは、カスタム プロパティ **message_number** が 3 を超えるメッセージのみが選択されます。

	subscription = azure_service_bus_service.create_subscription("test-topic", 
	  "high-messages")
	azure_service_bus_service.delete_rule("test-topic", "high-messages", 
	  "$Default")
	
	rule = Azure::ServiceBus::Rule.new("high-messages-rule")
	rule.topic = "test-topic"
	rule.subscription = "high-messages"
	rule.filter = Azure::ServiceBus::SqlFilter.new({ 
	  :sql_expression => "message_number > 3" })
	rule = azure_service_bus_service.create_rule(rule)

同様に、次の例では "low-messages" という名前のサブスクリプションを作成し、**Azure::ServiceBus::SqlFilter** を適用します。このフィルターでは、**message_number** プロパティが 3 以下のメッセージのみが選択されます。

	subscription = azure_service_bus_service.create_subscription("test-topic", 
	  "low-messages")
	azure_service_bus_service.delete_rule("test-topic", "low-messages", 
	  "$Default")
	
	rule = Azure::ServiceBus::Rule.new("low-messages-rule")
	rule.topic = "test-topic"
	rule.subscription = "low-messages"
	rule.filter = Azure::ServiceBus::SqlFilter.new({ 
	  :sql_expression => "message_number <= 3" })
	rule = azure_service_bus_service.create_rule(rule)

メッセージが "test-topic" に送信されると、そのメッセージは "all-messages" トピック サブスクリプションにサブスクライブしている受信者に必ず配信され、さらにメッセージのコンテンツに応じて、"high-messages" と "low-messages" トピック サブスクリプションにサブスクライブしている受信者に対して選択的に配信されます。

##メッセージをトピックに送信する方法

メッセージを Service Bus トピックに送信するには、アプリケーションで **Azure::ServiceBusService** オブジェクトの **send_topic_message()** メソッドを使用する必要があります。Service Bus トピックに送信されたメッセージは、**Azure::ServiceBus::BrokeredMessage** オブジェクトです。**Azure::ServiceBus::BrokeredMessage** オブジェクトには、一連の標準的なプロパティ (**label**、**time_to_live** など)、アプリケーションに特有のカスタム プロパティの保持に使用するディクショナリや文字列データの本体が備わっています。アプリケーションでは、文字列値を **send_topic_message()** メソッドに渡すことによってメッセージの本文を設定でき、必須の標準プロパティは既定値に設定されます。

次の例では、"test-topic" に 5 件のテスト メッセージを送信する方法を示します。各メッセージの **message_number** カスタム プロパティの値がループの反復回数に応じて変化することに注目してください (これによってメッセージを受信するサブスクリプションが決定されます)。

	5.times do |i|
	  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i, 
	    { :message_number => i })
	  azure_service_bus_service.send_topic_message("test-topic", message)
	end

サービス バス トピックでは、最大 256 MB までのメッセージをサポートしています (標準とカスタムのアプリケーション プロパティが含まれるヘッダーの最大サイズは 64 MB です)。トピックで保持されるメッセージ数には上限がありませんが、1 つのトピックで保持できるメッセージの合計サイズには上限があります。このトピックのサイズはトピックの作成時に定義します。上限は 5 GB です。

##サブスクリプションからメッセージを受信する方法

サブスクリプションからメッセージを受信するには、**Azure::ServiceBusService** オブジェクトの **receive_subscription_message()** メソッドを使用します。既定では、メッセージは読み取られて (ピークされて) ロックされますが、サブスクリプションからは削除されません。**peek_lock** オプションを **false** に設定すると、サブスクリプションからメッセージを読み取って削除できます。

既定の動作では、読み取りと削除が 2 段階の操作になるため、メッセージが失われることを許容できないアプリケーションにも対応できます。サービス バスは要求を受け取ると、次に読み取られるメッセージを検索して、他のコンシューマーが受信できないようロックしてから、アプリケーションにメッセージを返します。アプリケーションがメッセージの処理を終えた後 (または後で処理するために確実に保存した後)、**delete_subscription_message()** メソッドを呼び出し、削除するメッセージをパラメーターとして指定して、受信処理の第 2 段階を完了します。**delete_subscription_message()** メソッドによって、メッセージが読み取り中としてマークされ、サブスクリプションから削除されます。

**:peek_lock** パラメーターを **false** に設定すると、メッセージの読み取りと削除は最もシンプルなモデルになります。これは、障害発生時にアプリケーション側でメッセージを処理しないことを許容できるシナリオに最適です。このことを理解するために、コンシューマーが受信要求を発行した後で、メッセージを処理する前にクラッシュしたというシナリオを考えてみましょう。サービス バスはメッセージを読み取り済みとしてマークするため、アプリケーションが再起動してメッセージの読み取りを再開すると、クラッシュ前に読み取られていたメッセージは見落とされることになります。

次の例では、**receive_subscription_message()** を使用したメッセージの受信と処理の方法を示しています。この例では、まず **:peek_lock** を **false** に設定して使用することで、"low-messages" サブスクリプションからメッセージを受信して削除します。次に、"high-messages" から別のメッセージを受信し、**delete_subscription_message()** を使用してそのメッセージを削除します。

    message = azure_service_bus_service.receive_subscription_message(
	  "test-topic", "low-messages", { :peek_lock => false })
    message = azure_service_bus_service.receive_subscription_message(
	  "test-topic", "high-messages")
    azure_service_bus_service.delete_subscription_message(message)

##アプリケーションのクラッシュと読み取り不能のメッセージを処理する方法

サービス バスには、アプリケーションにエラーが発生した場合や、メッセージの処理に問題がある場合に復旧を支援する機能が備わっています。受信側のアプリケーションがなんらかの理由によってメッセージを処理できない場合には、**Azure::ServiceBusService** オブジェクトの **unlock_subscription_message()** メソッドを呼び出すことができます。このメソッドが呼び出されると、サブスクリプション内のメッセージのロックが解除され、メッセージが再度受信できる状態に変わります。このメッセージは、同じコンシューマー側アプリケーションまたは他のコンシューマー側アプリケーションで受信されます。

サブスクリプション内でロックされているメッセージには、タイムアウトも設定されています。アプリケーションがクラッシュした場合など、ロックがタイムアウトになる前にアプリケーションがメッセージの処理に失敗した場合は、サービス バスによってメッセージのロックが自動的に解除され、再度受信できる状態に変わります。

メッセージが処理された後、**delete_subscription_message()** メソッドが呼び出される前にアプリケーションがクラッシュした場合は、アプリケーションが再起動する際にメッセージが再配信されます。一般的に、この動作は **"1 回以上の処理"** と呼ばれます。つまり、すべてのメッセージが 1 回以上処理されますが、特定の状況では、同じメッセージが再配信される可能性があります。重複処理が許されないシナリオの場合、重複メッセージの配信を扱うロジックをアプリケーションに追加する必要があります。通常、この問題は、メッセージの **message_id** プロパティを使用して対処します。このプロパティは配信が試行された後も同じ値を保持します。

##トピックとサブスクリプションを削除する方法

トピックとサブスクリプションは永続的であり、[Azure 管理ポータル](https://manage.windowsazure.com)またはプログラムによって明示的に削除する必要があります。次の例では、"test-topic" という名前のトピックを削除する方法を示します。

	azure_service_bus_service.delete_topic("test-topic")

トピックを削除すると、そのトピックに登録されたサブスクリプションもすべて削除されます。サブスクリプションは、個別に削除することもできます。次のコードでは、"high-messages" という名前のサブスクリプションを "test-topic" トピックから削除する方法を示しています。

	azure_service_bus_service.delete_subscription("test-topic", "high-messages")

##次のステップ

これで、サービス バス トピックの基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先をご覧ください。

-   MSDN リファレンス:[キュー、トピック、サブスクリプション](http://msdn.microsoft.com/library/windowsazure/hh367516.aspx)
-   [SqlFilter](http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx) の API リファレンス
-	GitHub の [Azure SDK for Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) リポジトリを参照

<!--HONumber=47-->
 