<properties linkid="dev-java-how-to-service-bus-topics" urlDisplayName="サービス バス トピック" pageTitle="サービス バス トピックの使用方法 (Java) - Azure" metaKeywords="Azure サービス バス トピックの概要, サービス バス トピックの概要, Azure 発行/サブスクライブ メッセージング, Azure メッセージング トピックおよびサブスクリプション, サービス バス トピック Java" description="Azure でのサービス バスのトピックとサブスクリプションの使用方法について説明します。コード サンプルは Java アプリケーション向けに作成されています。" metaCanonical="" services="service-bus" documentationCenter="Java" title="サービス バス トピック/サブスクリプションの使用方法" authors="waltpo" solutions="" manager="bjsmith" editor="mollybos" />







# サービス バス トピック/サブスクリプションの使用方法

このガイドでは、サービス バスのトピックとサブスクリプションの
使用方法について説明します。サンプルは Java で記述され、[Azure SDK for Java][] を利用しています。ここでは、**トピックとサブスクリプションの作成**、
**サブスクリプション フィルターの作成**、**トピックへのメッセージの
送信**、**サブスクリプションからのメッセージの受信**、
**トピックとサブスクリプションの削除**などのシナリオについて説明します。

## 目次

-   [サービス バス トピックとサブスクリプションとは][]
-   [サービス名前空間の作成][]
-   [名前空間の既定の管理資格情報の取得][]
-   [サービス バスを使用するようにアプリケーションを構成する][]
-   [How to: トピックを作成する][]
-   [How to: サブスクリプションを作成する][]
-   [How to: メッセージをトピックに送信する][]
-   [How to: サブスクリプションからメッセージを受信する][]
-   [How to: アプリケーションのクラッシュと読み取り不能のメッセージを処理する][]
-   [How to: トピックとサブスクリプションを削除する][]
-   [次のステップ][]

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

## <a name="bkmk_ConfigYourApp"> </a>サービス バスを使用するようにアプリケーションを構成する

次の import ステートメントを Java ファイルの先頭に追加します。

    // Include the following imports to use service bus APIs
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import com.microsoft.windowsazure.services.core.*;
    import javax.xml.datatype.*;

Azure Libraries for Java をビルド パスに追加し、プロジェクトの展開アセンブリに含めます。

## <a name="bkmk_HowToCreateTopic"> </a>トピックの作成方法

サービス バス トピックの管理処理は**ServiceBusContract** クラスを
使用して実行できます。**ServiceBusContract** オブジェクトは、
管理用のトークン アクセス許可をカプセル化した適切な構成で作成します。
**ServiceBusContract** クラスでのみ Azure  サービス バス トピックとの
やり取りが可能です。

**ServiceBusService** クラスには、トピックの作成、列挙、および削除のための
メソッドが用意されています。次の例では、**ServiceBusService** オブジェクトを使用して、
"HowToSample" 名前空間の "TestTopic" という名前のトピックを作成する方法を示しています。

    Configuration config = 
    	ServiceBusConfiguration.configureWithWrapAuthentication(
          "HowToSample",
          "your_service_bus_owner",
          "your_service_bus_key",
          ".servicebus.windows.net",
          "-sb.accesscontrol.windows.net/WRAPv0.9");

	ServiceBusContract service = ServiceBusService.create(config);
    TopicInfo topicInfo = new TopicInfo("TestTopic");
	try  
	{
    	CreateTopicResult result = service.createTopic(topicInfo);
	}
	catch (ServiceException e) {
		System.out.print("ServiceException encountered: ");
	    System.out.println(e.getMessage());
		System.exit(-1);
	}

**TopicInfo** には、トピックのプロパティを調整できるメソッドが用意されて
います (たとえば、トピックに送信されるメッセージに対して既定の 
"有効期間" 値が適用されるように設定することができます)。次の例では、名前が "TestTopic"、
最大サイズが 5 GB であるトピックを作成する方法を示します。

    long maxSizeInMegabytes = 5120;  
	TopicInfo topicInfo = new TopicInfo("TestTopic");  
    topicInfo.setMaxSizeInMegabytes(maxSizeInMegabytes); 
    CreateTopicResult result = service.createTopic(topicInfo);

**ServiceBusContract** オブジェクトの **listTopics** 
メソッドを使用すると、指定した名前のトピックがサービス名前空間に
既に存在するかどうかを確認できます。

## <a name="bkmk_HowToCreateSubscrip"> </a>サブスクリプションの作成方法

トピック サブスクリプションも **ServiceBusService** クラスで
作成します。サブスクリプションを指定し、サブスクリプションの仮想キューに渡す
メッセージを制限するフィルターを設定することができます。

### 既定の (MatchAll) フィルターを適用したサブスクリプションの作成

**MatchAll** フィルターは、新しいサブスクリプションの作成時にフィルターが
指定されていない場合に使用される既定のフィルターです。**MatchAll** フィルターを
使用すると、トピックに発行されたすべてのメッセージがサブスクリプションの
仮想キューに置かれます。次の例では、"AllMessages" という
名前のサブスクリプションを作成し、既定の **MatchAll** フィルターを
使用します。

    SubscriptionInfo subInfo = new SubscriptionInfo("AllMessages");
    CreateSubscriptionResult result = 
        service.createSubscription("TestTopic", subInfo);

### フィルターを適用したサブスクリプションの作成

トピックに送信されたメッセージのうち、特定のトピック サブスクリプション内に表示
されるメッセージに絞り込めるフィルターを設定することもできます。

サブスクリプションでサポートされるフィルターのうち、最も柔軟性の高いものが、
SQL92 のサブセットを実装する **SqlFilter** です。SQL フィルターは、トピックに
発行されるメッセージのプロパティに対して適用されます。SQL フィルターで
使用できる式の詳細については、SqlFilter.SqlExpression 構文の
説明を参照してください。

次の例では、"HighMessages" という名前のサブスクリプションを作成し、
**SqlFilter** を適用します。このフィルターでは、
カスタム プロパティ **MessageNumber** が 3 を超えるメッセージのみが選択されます。

    // Create a "HighMessages" filtered subscription  
	SubscriptionInfo subInfo = new SubscriptionInfo("HighMessages");
    CreateSubscriptionResult result = 
		service.createSubscription("TestTopic", subInfo);
	RuleInfo ruleInfo = new RuleInfo("myRuleGT3");
	ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber > 3");
	CreateRuleResult ruleResult = 
		service.createRule("TestTopic", "HighMessages", ruleInfo);
    // Delete the default rule, otherwise the new rule won't be invoked.
    service.deleteRule("TestTopic", "HighMessages", "$Default");

同様に、次の例では "LowMessages" という名前のサブスクリプションを
作成し、SqlFilter を適用   
 します。このフィルターでは、MessageNumber が 3 以下のメッセージ
のみが   
 選択されます。

    // Create a "LowMessages" filtered subscription
	SubscriptionInfo subInfo = new SubscriptionInfo("LowMessages");
	CreateSubscriptionResult result = 
		service.createSubscription("TestTopic", subInfo);
    RuleInfo ruleInfo = new RuleInfo("myRuleLE3");
	ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber <= 3");
	CreateRuleResult ruleResult = 
		service.createRule("TestTopic", "LowMessages", ruleInfo);
    // Delete the default rule, otherwise the new rule won't be invoked.
    service.deleteRule("TestTopic", "LowMessages", "$Default");


メッセージが "TestTopic" に送信されると、そのメッセージは 
"AllMessages" トピック サブスクリプションにサブスクライブされた
受信者に必ず配信され、さらにメッセージの内容に応じて、"HighMessages" 
および "LowMessages" トピック サブスクリプションにサブスクライブされている
受信者に対して選択的に配信されます。

## <a name="bkmk_HowToSendMsgs"> </a>メッセージをトピックに送信する方法

サービス バス トピックにメッセージを送信するには、アプリケーションで 
**ServiceBusContract** オブジェクトを取得します。次のコードでは、前のコードで "HowToSample" 
サービス名前空間内で作成した "TestTopic" トピックにメッセージを
送信する方法を示しています。

    BrokeredMessage message = new BrokeredMessage("MyMessage");
    service.sendTopicMessage("TestTopic", message);

サービス バス トピックに送信されたメッセージは、
**BrokeredMessage** クラスのインスタンスです。**BrokeredMessage** オブジェクトには、
一連の標準的なメソッド (**setLabel**、**TimeToLive** など) と、
アプリケーションに固有のカスタム プロパティの保持に使用するディクショナリが
用意されており、任意のアプリケーション データの本体が格納されます。アプリケーションでは、**BrokeredMessage** の
コンストラクターにシリアル化可能なオブジェクトを渡すことによって
メッセージの本文を設定できます。その後で、適切な **DataContractSerializer** 
を使用してオブジェクトをシリアル化します。この方法に代わって、
**java.io.InputStream** を使用できます。

**次の例では、前に示したコード スニペットで取得した "TestTopic" の 
**MessageSender にテスト メッセージを 5 件送信する方法を示しています。
各メッセージの **MessageNumber** プロパティの値がループの反復回数に応じて
どのように変化するのかに注目してください (これによってメッセージを
受信するサブスクリプションが決定されます)。

    for (int i=0; i<5; i++)  {
       	// Create message, passing a string message for the body
		BrokeredMessage message = new BrokeredMessage("Test message " + i);
		// Set some additional custom app-specific property
		message.setProperty("MessageNumber", i);
		// Send message to the topic
		service.sendTopicMessage("TestTopic", message);
	}

サービス バス トピックでは、最大 256 MB までのメッセージをサポートしています 
(標準とカスタムのアプリケーション プロパティが含まれるヘッダーの最大サイズは 
64 MB です)。トピックで保持されるメッセージ数には
上限がありませんが、1 つのトピックで保持できるメッセージの合計サイズには
上限があります。このトピックのサイズはトピックの作成時に定義します。
上限は 5 GB です。

## <a name="bkmk_HowToReceiveMsgs"> </a>サブスクリプションからメッセージを受信する方法

サブスクリプションからメッセージを受信する主な方法は、
**ServiceBusContract** オブジェクトを使用することです。メッセージは 2 つの異なるモードで受信できます。
**ReceiveAndDelete** と **PeekLock** です。

**ReceiveAndDelete** モードを使用する場合、受信が 1 回ずつの動作に
なります。つまり、サービス バスはメッセージに対する読み取り要求を
受け取ると、メッセージを読み取り中としてマークし、アプリケーションに
返します。**ReceiveAndDelete** モードは最もシンプルな
モデルであり、問題が発生した際にアプリケーション側でメッセージを処理しないことを
許容できるシナリオに最適です。このことを理解するために、
コンシューマーが受信要求を発行した後で、メッセージを処理する前に
クラッシュしたというシナリオを考えてみましょう。サービス バスはメッセージを読み取り済みとして
マークするため、アプリケーションが再起動してメッセージの読み取りを再開すると、
クラッシュ前に読み取られていたメッセージは見落とされることに
なります。

**PeekLock** モードでは、メッセージの受信処理が 2 段階の動作になり、
メッセージが失われることが許容できないアプリケーションに対応することが
できます。サービス バス は要求を受け取ると、次に読み取られるメッセージを
検索して、他のコンシューマーが受信できないようロックしてから、
アプリケーションにメッセージを返します。アプリケーションがメッセージの
処理を終えた後 (または後で処理するために確実に保存した後)、
受信したメッセージに対して **Delete** を呼び出して受信処理の
第 2 段階を完了します。サービス バスが **Delete** の呼び出しを確認すると、
メッセージが読み取り中としてマークされ、トピックから削除されます。

次の例では、**PeekLock** モード (既定ではない) を使用した
メッセージの受信および処理の方法を示しています。次の例では、
ループを実行し、"HighMessages" サブスクリプション内のメッセージ処理します。メッセージがなくなるとループを終了します (または、新しいメッセージを待機するように設定される場合もあります)。

	try
	{
		ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
		opts.setReceiveMode(ReceiveMode.PEEK_LOCK);
	
		while(true)  { 
		    ReceiveSubscriptionMessageResult  resultSubMsg = 
		        service.receiveSubscriptionMessage("TestTopic", "HighMessages", opts);
		    BrokeredMessage message = resultSubMsg.getValue();
		    if (message != null && message.getMessageId() != null)
		    {
			    System.out.println("MessageID: " + message.getMessageId());    
			    // Display the topic message.
			    System.out.print("From topic: ");
			    byte[] b = new byte[200];
			    String s = null;
			    int numRead = message.getBody().read(b);
			    while (-1 != numRead)
	            {
	                s = new String(b);
	                s = s.trim();
	                System.out.print(s);
	                numRead = message.getBody().read(b);
			    }
	            System.out.println();
			    System.out.println("Custom Property: " + 
			        message.getProperty("MessageNumber"));
			    // Delete message.
			    System.out.println("Deleting this message.");
			    service.deleteMessage(message);
		    }  
		    else  
		    {        
		        System.out.println("Finishing up - no more messages.");        
		        break; 
		        // Added to handle no more messages.
		        // Could instead wait for more messages to be added.
		    }
	    }
	}
	catch (ServiceException e) {
	    System.out.print("ServiceException encountered: ");
	    System.out.println(e.getMessage());
	    System.exit(-1);
	}
	catch (Exception e) {
	    System.out.print("Generic exception encountered: ");
	    System.out.println(e.getMessage());
	    System.exit(-1);
	} 

## <a name="bkmk_HowToHandleAppCrash"> </a>アプリケーションのクラッシュと読み取り不能のメッセージを処理する方法

サービス バス には、アプリケーションにエラーが発生した場合や、メッセージの
処理に問題がある場合に復旧を支援する機能が備わっています。受信側の
アプリケーションが何らかの理由によってメッセージを処理できない場合には、
受信したメッセージについて (**deleteMessage** メソッドの代わりに) 
**unlockMessage** メソッドを呼び出すことができます。このメソッドが呼び出されると、
サービス バスによってトピック内のメッセージのロックが解除され、
メッセージが再度受信できる状態に変わります。メッセージを受信する
アプリケーションは、以前と同じものでも、別のものでもかまいません。

トピック内でロックされているメッセージにはタイムアウトも設定されています。
アプリケーションがクラッシュした場合など、ロックがタイムアウトに
なる前にアプリケーションがメッセージの処理に失敗した場合には、
メッセージのロックが自動的に解除され、再度受信できる状態に
変わります。

メッセージが処理された後、**deleteMessage** 要求が発行される前に
アプリケーションがクラッシュした場合は、アプリケーションが再起動する際に
メッセージが再配信されます。一般的に、この動作は 
**"1 回以上の処理"** と呼ばれます。つまり、すべてのメッセージが 1 回以上
処理されますが、特定の状況では、同じメッセージが再配信される
可能性があります。重複処理が許されないシナリオの場合、
重複メッセージの配信を扱うロジックをアプリケーションに
追加する必要があります。通常、この問題は
メッセージの **getMessageId** メソッドを使用して対処します。このプロパティは
配信が試行された後も同じ値を保持します。

## <a name="bkmk_HowToDeleteTopics"> </a>トピックとサブスクリプションを削除する方法

トピックとサブスクリプションを削除する主な方法は、
**ServiceBusContract** オブジェクトを使用することです。トピックを削除すると、そのトピックに登録されたサブスクリプションも
すべて削除されます。サブスクリプションは、個別に削除することもできます。

    // Delete subscriptions
    service.deleteSubscription("TestTopic", "AllMessages");
    service.deleteSubscription("TestTopic", "HighMessages");
    service.deleteSubscription("TestTopic", "LowMessages");

    // Delete a topic
	service.deleteTopic("TestTopic");

# <a name="bkmk_NextSteps"> </a>次のステップ

これで、サービス バス キューの基本を学習できました。詳細については、
MSDN のトピック「[Service Bus Queues, Topics, and Subscriptions][]」を参照してください。

  [Azure SDK for Java]: http://www.windowsazure.com/ja-jp/develop/java/
  [サービス バス トピックとサブスクリプションとは]: #what-are-service-bus-topics
  [サービス名前空間の作成]: #create-a-service-namespace
  [名前空間の既定の管理資格情報の取得]: #obtain-default-credentials
  [サービス バスを使用するようにアプリケーションを構成する]: #bkmk_ConfigYourApp
  [How to: トピックを作成する]: #bkmk_HowToCreateTopic
  [How to: サブスクリプションを作成する]: #bkmk_HowToCreateSubscrip
  [How to: メッセージをトピックに送信する]: #bkmk_HowToSendMsgs
  [How to: サブスクリプションからメッセージを受信する]: #bkmk_HowToReceiveMsgs
  [How to: アプリケーションのクラッシュと読み取り不能のメッセージを処理する]: #bkmk_HowToHandleAppCrash
  [How to: トピックとサブスクリプションを削除する]: #bkmk_HowToDeleteTopics
  [次のステップ]: #bkmk_NextSteps
  [サービス バス トピックの図]: ../../../DevCenter/Java/Media/SvcBusTopics_01_FlowDiagram.jpg
  [Azure 管理ポータル]: http://manage.windowsazure.com/
  [[サービス バス] ノードのスクリーンショット]: ../../../DevCenter/dotNet/Media/sb-queues-03.png
  [名前空間の新規作成]: ../../../DevCenter/dotNet/Media/sb-queues-04.png
  [名前空間の一覧のスクリーンショット]: ../../../DevCenter/dotNet/Media/sb-queues-05.png
  [[プロパティ] ウィンドウのスクリーンショット]: ../../../DevCenter/dotNet/Media/sb-queues-06.png
  [[既定のキー] のスクリーンショット]: ../../../DevCenter/dotNet/Media/sb-queues-07.png
  [サービス バス キュー、トピック、およびサブスクリプション]: http://msdn.microsoft.com/library/windowsazure/hh367516.aspx

