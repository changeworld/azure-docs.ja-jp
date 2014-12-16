<properties urlDisplayName="Service Bus Topics" pageTitle="Service Bus トピックの使用方法 (PHP) - Azure" metaKeywords="" description="Learn how to use Service Bus topics with PHP in Azure." metaCanonical="" services="service-bus" documentationCenter="PHP" title="How to Use Service Bus Topics/Subscriptions" authors="sethm" solutions="" manager="timlt" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="10/13/2014" ms.author="sethm" />


# サービス バス トピック/サブスクリプションの使用方法

このガイドでは、Service Bus のトピックとサブスクリプションの使用方法について説明します。サンプルは PHP で記述され、[Azure SDK for PHP][download-sdk] を利用しています。ここでは、**トピックとサブスクリプションの作成**、**サブスクリプション フィルターの作成**、**トピックへのメッセージの送信**、**サブスクリプションからのメッセージの受信**、**トピックとサブスクリプションの削除**などのシナリオについて説明します。

## 目次

-   [サービス バス トピックとサブスクリプションとは](#what-are-service-bus-topics)
-   [サービス名前空間の作成](#create-a-service-namespace)
-   [名前空間の既定の管理資格情報の取得](#obtain-default-credentials)
- 	[PHP アプリケーションの作成](#CreateApplication)
-	[Azure クライアント ライブラリの入手](#GetClientLibrary)
-   [サービス バスを使用するようにアプリケーションを構成する](#ConfigureApp)
-   [方法: トピックを作成する](#CreateTopic)
-   [方法: サブスクリプションを作成する](#CreateSubscription)
-   [方法: メッセージをトピックに送信する](#SendMessage)
-   [方法: サブスクリプションからメッセージを受信する](#ReceiveMessages)
-   [方法: アプリケーションのクラッシュと読み取り不能のメッセージを処理する](#HandleCrashes)
-   [方法: トピックとサブスクリプションを削除する](#DeleteTopicsAndSubscriptions)
-   [次のステップ](#NextSteps)

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

##<a id="CreateApplication"></a>PHP アプリケーションの作成

Azure BLOB サービスにアクセスする PHP アプリケーションを作成するための要件は、コード内から [Azure SDK for PHP][download-sdk] のクラスを参照することのみです。アプリケーションの作成には、メモ帳などの任意の開発ツールを使用できます。

> [WACOM.NOTE]
> PHP のインストールでは、<a href="http://php.net/openssl">OpenSSL 拡張機能</a>をインストールして有効にしておく必要もあります。

このガイドで使用するサービス機能は、PHP アプリケーション内でローカルで呼び出すことも、Azure の Web ロール、worker ロール、または Web サイト上で実行されるコード内で呼び出すこともできます。

##<a id="GetClientLibrary"></a>Azure クライアント ライブラリの入手

[WACOM.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

##<a id="ConfigureApp"></a>サービス バスを使用するようにアプリケーションを構成する

Azure の サービス バス トピック API を使用するには、次の要件があります。

1. [require_once][require-once] ステートメントを使用してオートローダー ファイルを参照する
2. 使用する可能性のあるクラスを参照する

次の例では、オートローダー ファイルをインクルードし、**ServiceBusService** クラスを参照する方法を示しています。

	> [WACOM.NOTE]
	> この例 (およびこの記事のその他の例) では、Composer を使用して Azure 向け PHP クライアント ライブラリがインストールされていることを前提としています。ライブラリを手動でまたは PEAR パッケージとしてインストールした場合は、<code>WindowsAzure.php</code> オートローダー ファイルを参照する必要があります。

	require_once 'vendor\autoload.php';
	use WindowsAzure\Common\ServicesBuilder;


この後のコード例では、"require_once" ステートメントが常に記述されていますが、コード例の実行に必要なクラスのみ参照されます。

##<a id="ConnectionString"></a>Azure Service Bus 接続の設定

Azure のサービス バス クライアントをインスタンス化するには、第一に、次の形式の有効な接続文字列が必要です。

	Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]

ここで Endpoint の一般的な形式は "https://[yourNamespace].servicebus.windows.net" です。

いずれの Azure サービス クライアントを作成するにも、**ServicesBuilder** クラスを使用する必要があります。そのための方法は次のとおりです。

* 接続文字列を直接渡す
* **CloudConfigurationManager (CCM)** を使用して複数の外部ソースに対して接続文字列を確認する
	* 既定では 1 つの外部ソース (環境変数) のみサポートされています。
	* **ConnectionStringSource** クラスを継承して新しいソースを追加できます。

ここで概説している例では、接続文字列を直接渡します。

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	
	$connectionString = "Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]";

	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

##<a id="CreateTopic"></a>方法: トピックを作成する

サービス バス トピックの管理操作は、
**ServiceBusRestProxy** クラス経由で実行できます。**ServiceBusRestProxy** オブジェクトを
作成するには、**ServicesBuilder::createServiceBusService** ファクトリ メソッドに、管理処理用のトークン アクセス許可をカプセル化した適切な接続文字列を渡します。

次の例では、**ServiceBusRestProxy** をインスタンス化し、**ServiceBusRestProxy->createTopic** を呼び出して、MySBNamespace サービス名前空間内で mytopic という名前のキューを作成する方法を示しています。

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\Models\TopicInfo;
	
	// Create Service Bus REST proxy.
	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
	
	try	{		
		// Create topic.
		$topicInfo = new TopicInfo("mytopic");
		$serviceBusRestProxy->createTopic($topicInfo);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/ja-jp/library/windowsazure/dd179357
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

	> [WACOM.NOTE]
	> You can use the <b>listTopics</b> method on <b>ServiceBusRestProxy</b> オブジェクトの listTopics メソッドを使用すると、指定した名前のトピックがサービス名前空間に既に存在するかどうかを確認できます。

##<a id="CreateSubscription"></a>方法: サブスクリプションを作成する

トピック サブスクリプションを **ServiceBusRestProxy->createSubscription** メソッドで作成することもできます。サブスクリプションを指定し、サブスクリプションの仮想キューに渡すメッセージを制限するフィルターを設定することができます。

### 既定の (MatchAll) フィルターを適用したサブスクリプションの作成

**MatchAll** フィルターは、新しいサブスクリプションが作成されたときにフィルターが指定されていない場合に使用される既定のフィルターです。**MatchAll** フィルターを使用すると、トピックに発行されたすべてのメッセージがサブスクリプションの仮想キューに置かれます。次の例では、'mysubscription' という名前のサブスクリプションを作成し、既定の **MatchAll** フィルターを使用します。

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\Models\SubscriptionInfo;

	// Create Service Bus REST proxy.
	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
	
	try	{
		// Create subscription.
		$subscriptionInfo = new SubscriptionInfo("mysubscription");
		$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/ja-jp/library/windowsazure/dd179357
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

### フィルターを適用したサブスクリプションの作成

トピックに送信されたメッセージのうち、特定のトピック サブスクリプション内に表示されるメッセージに絞り込めるフィルターを設定することもできます。サブスクリプションでサポートされるフィルターのうち、最も柔軟性の高いものが、SQL92 のサブセットを実装する **SqlFilter** です。SQL フィルターは、トピックに発行されるメッセージのプロパティに対して適用されます。SqlFilter の詳細については、「[SqlExpression プロパティ][sqlfilter]」を参照してください。

	> [WACOM.NOTE]
	> サブスクリプションのルールごとに受信メッセージが個別に処理され、処理後のメッセージがサブスクリプションに追加されます。さらに、新しいサブスクリプションにはそれぞれ、既定の<b>ルール</b>があり、トピックからのすべてのメッセージをサブスクリプションに追加するフィルターが設定されています。独自のフィルターに一致するメッセージのみ受信するには、この既定のルールを削除する必要があります。既定のルールを削除するには、<b>ServiceBusRestProxy->deleteRule</b> メソッドを使用します。

次の例では、"HighMessages" という名前のサブスクリプションを作成し、**SqlFilter** を適用します。このフィルターでは、カスタム プロパティ **MessageNumber** が 3 を超えるメッセージのみが選択されます (カスタム プロパティをメッセージに追加する方法については「[方法: トピックにメッセージを送信する](#SendMessage)」を参照)。

	$subscriptionInfo = new SubscriptionInfo("HighMessages");
   	$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

	$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

  	$ruleInfo = new RuleInfo("HighMessagesRule");
   	$ruleInfo->withSqlFilter("MessageNumber > 3");
   	$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);

Note that the code above requires the use of an additional namespace: `WindowsAzure\ServiceBus\Models\SubscriptionInfo`.

Similarly, the following example creates a subscription named "LowMessages" with a SqlFilter that only selects messages that have a MessageNumber property less than or equal to 3:

	$subscriptionInfo = new SubscriptionInfo("LowMessages");
   	$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

	$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

  	$ruleInfo = new RuleInfo("LowMessagesRule");
   	$ruleInfo->withSqlFilter("MessageNumber <= 3");
   	$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);

メッセージが mytopic トピックに送信されると、そのメッセージは mysubscription サブスクリプションにサブスクライブされた受信者に必ず配信され、さらにメッセージの内容に応じて、"HighMessages" および "LowMessages" サブスクリプションにサブスクライブされている受信者に対して選択的に配信されます。

##<a id="SendMessage"></a>方法: メッセージをトピックに送信する

メッセージを Service Bus トピックに送信するには、アプリケーションで **ServiceBusRestProxy->sendTopicMessage** メソッドを呼び出します。次のコードでは、前のコードで 
MySBNamespace サービス名前空間内で作成した mytopic トピックにメッセージを送信する方法を示しています。

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\Models\BrokeredMessage;

	// Create Service Bus REST proxy.
	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
		
	try	{
		// Create message.
		$message = new BrokeredMessage();
		$message->setBody("my message");
	
		// Send message.
		$serviceBusRestProxy->sendTopicMessage("mytopic", $message);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/ja-jp/library/windowsazure/hh780775
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Service Bus トピックに送信されたメッセージは、**BrokeredMessage** クラスのインスタンスです。**BrokeredMessage** オブジェクトには、一連の標準的なプロパティとメソッド (**getLabel**、**getTimeToLive**、**setLabel**、**setTimeToLive** など) だけでなく、アプリケーションに固有のカスタム プロパティの保持に使用できるプロパティも用意されています。次の例では、前に作成した mytopic トピックに 5 通のテスト メッセージを送信する方法を示しています。**setProperty** メソッドを使用して、カスタム プロパティ (MessageNumber) を各メッセージに追加します。MessageNumber プロパティの値がメッセージごとにどのように変化するのかに注目してください (この値はメッセージを受信するサブスクリプションを決めるために使用できます。前に示した「[方法: サブスクリプションを作成する](#CreateSubscription)」を参照してください)。

	for($i = 0; $i < 5; $i++){
		// Create message.
		$message = new BrokeredMessage();
		$message->setBody("my message ".$i);
			
		// Set custom property.
		$message->setProperty("MessageNumber", $i);
			
		// Send message.
		$serviceBusRestProxy->sendTopicMessage("mytopic", $message);
	}

Service Bus キューでは、最大 256 KB までのメッセージをサポートしています (標準とカスタムのアプリケーション プロパティが含まれるヘッダーは、64 KB が最大サイズです)。キューで保持されるメッセージ数には上限がありませんが、キュー 1 つあたりが保持できるメッセージの合計サイズには上限があります。このキュー サイズの上限は 5 GB です。

##<a id="ReceiveMessages"></a>方法: サブスクリプションからメッセージを受信する

サブスクリプションからメッセージを受信する主な方法は、**ServiceBusRestProxy->receiveSubscriptionMessage** メソッドを使用することです。メッセージは 2 つの異なるモードで受信できます。**ReceiveAndDelete** (既定) と **PeekLock** です。

**ReceiveAndDelete** モードを使用する場合、受信が 1 回ずつの動作になります。つまり、Service Bus はサブスクリプション内のメッセージに対する読み取り要求を受け取ると、メッセージを読み取り中としてマークし、アプリケーションに返します。**ReceiveAndDelete** モードは最もシンプルなモデルであり、障害発生時にアプリケーション側でメッセージを処理しないことを許容できるシナリオに最適です。このことを理解するために、コンシューマーが受信要求を発行した後で、メッセージを処理する前にクラッシュしたというシナリオを考えてみましょう。Service Bus はメッセージを読み取り済みとしてマークするため、アプリケーションが再起動してメッセージの読み取りを再開すると、クラッシュ前に読み取られていたメッセージは見落とされることになります。

**PeekLock** モードでは、メッセージの受信処理が 2 段階の動作になり、メッセージが失われることが許容できないアプリケーションに対応することができます。サービス バスは要求を受け取ると、次に読み取られるメッセージを検索して、他のコンシューマーが受信できないようロックしてから、アプリケーションにメッセージを返します。アプリケーションがメッセージの処理を終えた後 (または後で処理するために確実に保存した後)、受信したメッセージを **ServiceBusRestProxy->deleteMessage** に渡して受信処理の第 2 段階を完了します。Service Bus が **deleteMessage** の呼び出しを確認すると、メッセージが読み取り中としてマークされ、キューから削除されます。

次の例では、**PeekLock** モード (既定ではない) を使用したメッセージの受信および処理の方法を示しています。 

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

	// Create Service Bus REST proxy.
	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
		
	try	{
		// Set receive mode to PeekLock (default is ReceiveAndDelete)
		$options = new ReceiveMessageOptions();
		$options->setPeekLock();
	
		// Get message.
		$message = $serviceBusRestProxy->receiveSubscriptionMessage("mytopic", 
																	"mysubscription", 
																	$options);
		echo "Body: ".$message->getBody()."<br />";
		echo "MessageID: ".$message->getMessageId()."<br />";
		
		/*---------------------------
			Process message here.
		----------------------------*/
		
		// Delete message. Not necessary if peek lock is not set.
		echo "Deleting message...<br />";
		$serviceBusRestProxy->deleteMessage($message);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/ja-jp/library/windowsazure/hh780735
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

##<a id="HandleCrashes"></a>方法: アプリケーションのクラッシュと読み取り不能のメッセージを処理する

Service Bus には、アプリケーションにエラーが発生した場合や、メッセージの処理に問題がある場合に復旧を支援する機能が備わっています。受信側のアプリケーションが何らかの理由によってメッセージを処理できない場合には、受信したメッセージについて (**deleteMessage** メソッドの代わりに) **unlockMessage** メソッドを呼び出すことができます。このメソッドが呼び出されると、Service Bus によってキュー内のメッセージのロックが解除され、メッセージが再度受信できる状態に変わります。メッセージを受信するアプリケーションは、以前と同じものでも、別のものでもかまいません。

キュー内でロックされているメッセージにはタイムアウトも設定されています。アプリケーションがクラッシュした場合など、ロックがタイムアウトになる前にアプリケーションがメッセージの処理に失敗した場合には、メッセージのロックが自動的に解除され、再度受信できる状態に変わります。

メッセージが処理された後、**deleteMessage** 要求が発行される前にアプリケーションがクラッシュした場合は、アプリケーションが再起動する際にメッセージが再配信されます。一般的に、この動作は **"1 回以上の処理"** と呼ばれます。つまり、すべてのメッセージが 1 回以上処理されますが、特定の状況では、同じメッセージが再配信される可能性があります。重複処理が許されないシナリオの場合、重複メッセージの配信を扱うロジックをアプリケーションに追加する必要があります。通常、この問題はメッセージの **getMessageId** メソッドを使用して対処します。このプロパティは配信が試行された後も同じ値を保持します。

##<a id="DeleteTopicsAndSubscriptions"></a>方法: トピックとサブスクリプションを削除する

トピックまたはサブスクリプションを削除するには、**ServiceBusRestProxy->deleteTopic** メソッドまたは **ServiceBusRestProxy->deleteSubscripton** メソッドをそれぞれ使用します。トピックを削除すると、そのトピックに登録されたサブスクリプションもすべて削除されることに注意してください。

次のコードでは、トピック (mytopic) とその登録されたサブスクリプションを削除する方法を示しています。

    require_once 'vendor\autoload.php';

	use WindowsAzure\ServiceBus\ServiceBusService;
	use WindowsAzure\ServiceBus\ServiceBusSettings;
	use WindowsAzure\Common\ServiceException;

	// Create Service Bus REST proxy.
	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
	
	try	{		
		// Delete topic.
		$serviceBusRestProxy->deleteTopic("mytopic");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/ja-jp/library/windowsazure/dd179357
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

**deleteSubscription** メソッドを使用すると、サブスクリプションを個別に削除できます。

	$serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");

##<a id="NextSteps"></a>次のステップ

これで、Service Bus キューの基本を学習できました。詳細については、MSDN のトピック「[Service Bus キュー、トピックおよびサブスクリプション][]」を参照してください。

[download-sdk]: http://go.microsoft.com/fwlink/?LinkId=252473
[サービス バス トピックとサブスクリプションとは]: #bkmk_WhatAreSvcBusTopics
[サービス名前空間の作成]: #bkmk_CreateSvcNamespace
[名前空間の既定の管理資格情報の取得]: #bkmk_ObtainDefaultMngmntCredentials
[サービス バスを使用するようにアプリケーションを構成する]: #bkmk_ConfigYourApp
[方法: トピックを作成する]: #bkmk_HowToCreateTopic
[方法: サブスクリプションを作成する]: #bkmk_HowToCreateSubscrip
[方法: メッセージをトピックに送信する]: #bkmk_HowToSendMsgs
[方法: サブスクリプションからメッセージを受信する]: #bkmk_HowToReceiveMsgs
[方法: アプリケーションのクラッシュと読み取り不能のメッセージを処理する]: #bkmk_HowToHandleAppCrash
[方法: トピックとサブスクリプションを削除する]: #bkmk_HowToDeleteTopics
[次のステップ]: #bkmk_NextSteps
[サービス バス トピックの図]: ../../../DevCenter/Java/Media/SvcBusTopics_01_FlowDiagram.jpg
[Azure 管理ポータル]: http://manage.windowsazure.com/
[[サービス バス] ノードのスクリーンショット]: ../../../DevCenter/dotNet/Media/sb-queues-03.png
[[名前空間の新規作成] のスクリーンショット]: ../../../DevCenter/dotNet/Media/sb-queues-04.png
[名前空間の一覧のスクリーン ショット]: ../../../DevCenter/dotNet/Media/sb-queues-05.png
[[プロパティ] ウィンドウのスクリーン ショット]: ../../../DevCenter/dotNet/Media/sb-queues-06.png
[既定のキーのスクリーン ショット]: ../../../DevCenter/dotNet/Media/sb-queues-07.png
[サービス バス キュー、トピック、およびサブスクリプション]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh367516.aspx
[使用可能な名前空間のスクリーン ショット]: ../../../DevCenter/Java/Media/SvcBusQueues_04_SvcBusNode_AvailNamespaces.jpg
[sqlfilter]: http://msdn.microsoft.com/ja-jp/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx

[require-once]: http://php.net/require_once
