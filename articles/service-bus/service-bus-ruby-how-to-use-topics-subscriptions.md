<properties
	pageTitle="Service Bus トピックの使用方法 (Ruby) | Microsoft Azure"
	description="Azure での Service Bus のトピックとサブスクリプションの使用方法について学習します。コード サンプルは Ruby アプリケーション向けに作成されています。"
	services="service-bus"
	documentationCenter="ruby"
	authors="sethmanheim"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="ruby"
	ms.topic="article"
	ms.date="12/09/2015"
	ms.author="sethm"/>

# Service Bus トピック/サブスクリプションの使用方法

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

このガイドでは、Ruby アプリケーションから Service Bus のトピックとサブスクリプションを使用する方法について説明します。ここでは、**トピックとサブスクリプションの作成、サブスクリプション フィルターの作成、トピックへのメッセージの送信**、**サブスクリプションからのメッセージの受信**、**トピックとサブスクリプションの削除**などのシナリオについて説明します。トピックとサブスクリプションの詳細については、「[次の手順](#next-steps)」を参照してください。

## Service Bus トピックとサブスクリプション

Service Bus のトピックとサブスクリプションは、**メッセージ通信の発行とサブスクライブ** モデルをサポートします。トピックとサブスクリプションを使用すると、分散アプリケーションのコンポーネントが互いに直接通信することがなくなり、仲介者の役割を果たすトピックを介してメッセージをやり取りすることになります。

![TopicConcepts](./media/service-bus-ruby-how-to-use-topics-subscriptions/sb-topics-01.png)

すべてのメッセージが 1 つのコンシューマーによって処理される Service Bus キューとは異なり、トピックとサブスクリプションでは発行/サブスクライブ パターンを使用した **1 対多**形式の通信を行います。複数のサブスクリプションを 1 つのトピックに登録できます。トピックに送信されたメッセージはサブスクリプションに渡され、各サブスクリプションで独立して処理できます。

トピック サブスクリプションは、トピックに送信されたメッセージのコピーを受け取る仮想キューのようなものです。トピックに対するフィルター ルールをサブスクリプション単位で登録することもできます。これを使用すると、トピックに送信されるどのメッセージをどのトピック サブスクリプションで受信するかのフィルター処理や制限ができます。

Service Bus のトピックとサブスクリプションを使用すると、多数のユーザーとアプリケーションの間でやり取りされる膨大な数のメッセージを処理することもできます。

## サービス名前空間の作成

Azure の Service Bus キューを使用するには、最初にサービス名前空間を作成する必要があります。名前空間は、アプリケーション内で Service Bus リソースをアドレス指定するためのスコープ コンテナーを提供します。[Azure クラシック ポータル][]では、名前空間の作成に ACS 接続が使用されないため、コマンド ライン インターフェイスを使用して名前空間を作成する必要があります。

名前空間を作成するには:

1. Azure PowerShell コンソールを開きます。

2. コマンドを下に示すように入力して、名前空間を作成します。独自の名前空間値と、アプリケーションと同じリージョンを指定します。

      New-AzureSBNamespace -Name 'yourexamplenamespace' -Location 'West US' -NamespaceType 'Messaging' -CreateACSNamespace $true

      ![名前空間の作成](./media/service-bus-ruby-how-to-use-topics-subscriptions/showcmdcreate.png)

## 名前空間の既定の管理資格情報の取得

新規作成した名前空間に対してキューの作成などの管理操作を実行するには、名前空間の管理資格情報を取得する必要があります。

Service Bus 名前空間を作成するために実行した PowerShell コマンドレットでは、名前空間の管理に使用できるキーが表示されます。**DefaultKey** 値をコピーします。このチュートリアルでは、後でコードにこの値を使用します。

      ![Copy key](./media/service-bus-ruby-how-to-use-topics-subscriptions/defaultkey.png)

> [AZURE.NOTE]このキーは、[Azure クラシック ポータル][]にログオンして、名前空間の接続情報に移動する場合にも見つかります。

## Ruby アプリケーションの作成

手順については、[Windows Azure での Ruby アプリケーションの作成に関するページ](/develop/ruby/tutorials/web-app-with-linux-vm/)を参照してください。

## Service Bus を使用するようにアプリケーションを構成する

Azure Service Bus を使用するには、Ruby Azure パッケージをダウンロードして使用します。このパッケージには、ストレージ REST サービスと通信するための便利なライブラリのセットが含まれています。

### RubyGems を使用してパッケージを取得する

1. **PowerShell** (Windows)、**ターミナル** (Mac)、**Bash** (Unix) などのコマンド ライン インターフェイスを使用します。

2. コマンド ウィンドウに「gem install azure」と入力して、gem と依存関係をインストールします。

### パッケージをインポートする

任意のテキスト エディターを使用して、ストレージを使用するアプリケーションの Ruby ファイルの先頭に次のコードを追加します。

    require "azure"

## Service Bus 接続の設定

Azure モジュールは、名前空間に接続するために必要な情報として、環境変数 **AZURE\_SERVICEBUS\_NAMESPACE** と **AZURE\_SERVICEBUS\_ACCESS\_KEY** を読み取ります。これらの環境変数が設定されていない場合は、**Azure::ServiceBusService** を使用する前に、次のコードを使用して名前空間情報を指定する必要があります。

    Azure.config.sb_namespace = "<your azure service bus namespace>"
    Azure.config.sb_access_key = "<your azure service bus access key>"

作成した値には、URL 全体ではなく、名前空間値を設定してください。たとえば、"yourexamplenamespace.servicebus.windows.net" ではなく、**"yourexamplenamespace"** を使用します。

## トピックを作成する

**Azure::ServiceBusService** オブジェクトを使用すると、トピックを操作できます。次のコードでは、**Azure::ServiceBusService** オブジェクトを作成します。トピックを作成するには、**create\_topic()** メソッドを使用します。次の例では、トピックを作成し、既に存在する場合はエラーを出力します。

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

## サブスクリプションを作成する

トピック サブスクリプションも、**Azure::ServiceBusService** オブジェクトで作成します。サブスクリプションを指定し、サブスクリプションの仮想キューに配信するメッセージを制限するフィルターを設定することができます。

サブスクリプションは永続的であり、サブスクリプション、またはサブスクリプションが関連付けられているトピックが削除されるまで存在し続けます。アプリケーションにサブスクリプションを作成するロジックが含まれている場合は、最初に getSubscription メソッドを使用して、サブスクリプションが既に存在しているかどうかを確認する必要があります。

### 既定の (MatchAll) フィルターを適用したサブスクリプションの作成

**MatchAll** フィルターは、新しいサブスクリプションの作成時にフィルターが指定されていない場合に使用される既定のフィルターです。**MatchAll** フィルターを使用すると、トピックに発行されたすべてのメッセージがサブスクリプションの仮想キューに置かれます。次の例では、"all-messages" という名前のサブスクリプションを作成し、既定の **MatchAll** フィルターを使用します。

	subscription = azure_service_bus_service.create_subscription("test-topic",
	  "all-messages")

### フィルターを適用したサブスクリプションの作成

トピックに送信されたメッセージのうち、特定のサブスクリプション内に表示されるメッセージを指定するフィルターを定義することもできます。

サブスクリプションでサポートされるフィルターのうち、最も柔軟性の高いものが、SQL92 のサブセットを実装する **Azure::ServiceBus::SqlFilter** です。SQL フィルターは、トピックに発行されるメッセージのプロパティに対して適用されます。SQL フィルターで使用できる式の詳細については、[SqlFilter.SqlExpression](http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx) 構文の説明を参照してください。

**Azure::ServiceBusService** オブジェクトの **create\_rule()** メソッドを使用して、サブスクリプションにフィルターを追加できます。このメソッドでは、新しいフィルターを既存のサブスクリプションに追加できます。

既定のフィルターはすべての新しいサブスクリプションに自動的に適用されるので、最初に既定のフィルターを削除する必要があります。削除しないと **MatchAll** は指定される他のすべてのフィルターをオーバーライドします。既定のルールを削除するには、**Azure::ServiceBusService** オブジェクトの **delete\_rule()** メソッドを使用します。

次の例では、"high-messages" という名前のサブスクリプションを作成し、**Azure::ServiceBus::SqlFilter** を適用します。このフィルターでは、カスタム プロパティ **message\_number** が 3 を超えるメッセージのみが選択されます。

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

同様に、次の例では "low-messages" という名前のサブスクリプションを作成し、**Azure::ServiceBus::SqlFilter** を適用します。このフィルターでは、**message\_number** プロパティが 3 以下のメッセージのみが選択されます。

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

メッセージが "test-topic" に送信されると、そのメッセージは "all-messages" トピック サブスクリプションにサブスクライブしている受信者に必ず配信され、さらにメッセージのコンテンツに応じて、"high-messages" および "low-messages" トピック サブスクリプションにサブスクライブしている受信者に対して選択的に配信されます。

## メッセージをトピックに送信する

メッセージを Service Bus トピックに送信するには、アプリケーションで **Azure::ServiceBusService** オブジェクトの **send\_topic\_message()** メソッドを使用する必要があります。Service Bus トピックに送信されたメッセージは、**Azure::ServiceBus::BrokeredMessage** オブジェクトのインスタンスです。**Azure::ServiceBus::BrokeredMessage** オブジェクトには、一連の標準的なプロパティ (**label**、**time\_to\_live** など)、アプリケーションに特有のカスタム プロパティの保持に使用するディクショナリ、および文字列データの本体が備わっています。アプリケーションでは、文字列値を **send\_topic\_message()** メソッドに渡すことによってメッセージの本文を設定でき、必須の標準プロパティは既定値に設定されます。

次の例では、"test-topic" に 5 件のテスト メッセージを送信する方法を示します。各メッセージの **message\_number** カスタム プロパティの値が、ループの反復回数に応じて変化することに注意してください (これによってメッセージを受信するサブスクリプションが決定されます)。

	5.times do |i|
	  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
	    { :message_number => i })
	  azure_service_bus_service.send_topic_message("test-topic", message)
	end

Service Bus トピックでは、最大 256 MB までのメッセージをサポートしています (標準とカスタムのアプリケーション プロパティが含まれるヘッダーの最大サイズは 64 MB です)。トピックで保持されるメッセージ数には上限がありませんが、1 つのトピックで保持できるメッセージの合計サイズには上限があります。このトピックのサイズはトピックの作成時に定義します。上限は 5 GB です。

## サブスクリプションからメッセージを受信する

サブスクリプションからメッセージを受信するには、**Azure::ServiceBusService** オブジェクトの **receive\_subscription\_message()** メソッドを使用します。既定では、メッセージは読み取られて (ピークされて) ロックされますが、サブスクリプションからは削除されません。**peek\_lock** オプションを **false** に設定すると、サブスクリプションからメッセージを読み取って削除できます。

既定の動作では、読み取りと削除が 2 段階の操作になるため、メッセージが失われることを許容できないアプリケーションにも対応することができます。Service Bus は要求を受け取ると、次に読み取られるメッセージを検索して、他のコンシューマーが受信できないようロックしてから、アプリケーションにメッセージを返します。アプリケーションがメッセージの処理を終えた後 (または後で処理するために確実に保存した後)、**delete\_subscription\_message()** メソッドを呼び出し、削除するメッセージをパラメーターとして指定して、受信処理の第 2 段階を完了します。**delete\_subscription\_message()** メソッドによって、メッセージが読み取り中としてマークされ、サブスクリプションから削除されます。

**:peek\_lock** パラメーターを **false** に設定すると、メッセージの読み取りと削除は最もシンプルなモデルになります。これは、障害発生時にアプリケーション側でメッセージを処理しないことを許容できるシナリオに最適です。このことを理解するために、コンシューマーが受信要求を発行した後で、メッセージを処理する前にクラッシュしたというシナリオを考えてみましょう。Service Bus はメッセージを読み取り済みとしてマークするため、アプリケーションが再起動してメッセージの読み取りを再開すると、クラッシュ前に読み取られていたメッセージは見落とされることになります。

次の例では、**receive\_subscription\_message()** を使用したメッセージの受信および処理の方法を示しています。この例では、まず **:peek\_lock** を **false** に設定して使用することで、"low-messages" サブスクリプションからメッセージを受信して削除します。次に、"high-messages" から別のメッセージを受信し、**delete\_subscription\_message()** を使用してそのメッセージを削除します。

    message = azure_service_bus_service.receive_subscription_message(
	  "test-topic", "low-messages", { :peek_lock => false })
    message = azure_service_bus_service.receive_subscription_message(
	  "test-topic", "high-messages")
    azure_service_bus_service.delete_subscription_message(message)

## アプリケーションのクラッシュと読み取り不能のメッセージを処理する

Service Bus には、アプリケーションにエラーが発生した場合や、メッセージの処理に問題がある場合に復旧を支援する機能が備わっています。受信側のアプリケーションが何らかの理由によってメッセージを処理できない場合には、**Azure::ServiceBusService** オブジェクトの **unlock\_subscription\_message()** メソッドを呼び出すことができます。このメソッドが呼び出されると、Service Bus によりサブスクリプション内のメッセージのロックが解除され、メッセージが再度受信できる状態に変わります。このメッセージは、同じコンシューマー側アプリケーションまたは他のコンシューマー側アプリケーションで受信されます。

サブスクリプション内でロックされているメッセージには、タイムアウトも設定されています。アプリケーションがクラッシュした場合など、ロックがタイムアウトになる前にアプリケーションがメッセージの処理に失敗した場合は、Service Bus によってメッセージのロックが自動的に解除され、再度受信できる状態に変わります。

メッセージが処理された後、**delete\_subscription\_message()** メソッドが呼び出される前にアプリケーションがクラッシュした場合は、アプリケーションが再起動するときにメッセージが再配信されます。一般的に、この動作は **"1 回以上の処理"** と呼ばれます。つまり、すべてのメッセージが 1 回以上処理されますが、特定の状況では、同じメッセージが再配信される可能性があります。重複処理が許されないシナリオの場合、重複メッセージの配信を扱うロジックをアプリケーションに追加する必要があります。通常、このロジックはメッセージの **message\_id** プロパティを使用して実行されます。このプロパティは配信が試行された後も同じ値を保持します。

## トピックとサブスクリプションを削除する

トピックおよびサブスクリプションは永続的であり、[Azure クラシック ポータル](https://manage.windowsazure.com)またはプログラムによって明示的に削除する必要があります。次の例では、"test-topic" という名前のトピックを削除する方法を示します。

	azure_service_bus_service.delete_topic("test-topic")

トピックを削除すると、そのトピックに登録されたサブスクリプションもすべて削除されます。サブスクリプションは、個別に削除することもできます。次のコードでは、"high-messages" という名前のサブスクリプションを "test-topic" トピックから削除する方法を示しています。

	azure_service_bus_service.delete_subscription("test-topic", "high-messages")

## 次のステップ

これで、Service Bus トピックの基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先を参照してください。

-   [キュー、トピック、およびサブスクリプション](service-bus-queues-topics-subscriptions.md)に関するページを参照してください。
-   [SqlFilter](http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx) の API のリファレンス
-	GitHub の [Azure SDK for Ruby](https://github.com/Azure/azure-sdk-for-ruby) リポジトリ
 
[Azure クラシック ポータル]: http://manage.windowsazure.com

<!---HONumber=AcomDC_1217_2015-->