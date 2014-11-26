<properties linkid="dev-java-how-to-service-bus-topics" urlDisplayName="Service Bus Topics" pageTitle="How to use Service Bus topics (Java) - Azure" metaKeywords="Get started Azure Service Bus topics, Get Started Service Bus topics, Azure publish subscribe messaging, Azure messaging topics and subscriptions, Service Bus topic Java" description="Learn how to use Service Bus topics and subscriptions in Azure. Code samples are written for Java applications." metaCanonical="" services="service-bus" documentationCenter="Java" title="How to Use Service Bus Topics/Subscriptions" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm" />

# サービス バス トピック/サブスクリプションの使用方法

このガイドでは、Service Bus のトピックと
サブスクリプションの使用方法について説明します。サンプルは Java で記述され、[Azure SDK for Java][Azure SDK for Java] を利用しています。ここでは、**トピック
とサブスクリプションの作成**、**サブスクリプション フィルターの作成**、**トピックへのメッセージ
の送信**、**サブスクリプションからのメッセージの受信**、
**トピックとサブスクリプションの削除**などのシナリオについて説明します。

## 目次

-   [サービス バス トピックとサブスクリプションとは][サービス バス トピックとサブスクリプションとは]
-   [サービス名前空間の作成][サービス名前空間の作成]
-   [名前空間の既定の管理資格情報の取得][名前空間の既定の管理資格情報の取得]
-   [サービス バスを使用するようにアプリケーションを構成する][サービス バスを使用するようにアプリケーションを構成する]
-   [方法: トピックを作成する][方法: トピックを作成する]
-   [方法: サブスクリプションを作成する][方法: サブスクリプションを作成する]
-   [方法: メッセージをトピックに送信する][方法: メッセージをトピックに送信する]
-   [方法: サブスクリプションからメッセージを受信する][方法: サブスクリプションからメッセージを受信する]
-   [方法: アプリケーションのクラッシュと読み取り不能のメッセージを処理する][方法: アプリケーションのクラッシュと読み取り不能のメッセージを処理する]
-   [方法: トピックとサブスクリプションを削除する][方法: トピックとサブスクリプションを削除する]
-   [次のステップ][次のステップ]

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

## <a name="bkmk_ConfigYourApp"> </a> サービス バス を使用するようにアプリケーションを構成する

次の import ステートメントを Java ファイルの先頭に追加します。

    // Include the following imports to use service bus APIs
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import com.microsoft.windowsazure.services.core.*;
    import javax.xml.datatype.*;

Azure Libraries for Java をビルド パスに追加し、プロジェクトの展開アセンブリに含めます。

## <a name="bkmk_HowToCreateTopic"> </a>トピックの作成方法

サービス バス トピックの管理処理は
**ServiceBusContract** クラスを使用して実行できます。**ServiceBusContract** オブジェクトは、
管理用の
トークン アクセス許可をカプセル化した適切な構成で作成します。**ServiceBusContract** クラスが
Azure との唯一の通信ポイントです。

**ServiceBusService** クラスには、トピックの作成、列挙、および
削除のためのメソッドが用意されています。次の例では、**ServiceBusService** オブジェクト
を使用して、"HowToSample" 名前空間の "TestTopic" という名前のトピックを作成する方法を示しています。

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

**TopicInfo** には、トピックのプロパティ
を調整できるメソッドが用意されています (トピックに送信されるメッセージ
に適用される既定の "有効期限" の値を設定するメソッドなどです)。次の例では、名前が "TestTopic"、最大サイズが 5 GB であるトピックを作成する
方法を示します。

    long maxSizeInMegabytes = 5120;  
    TopicInfo topicInfo = new TopicInfo("TestTopic");  
    topicInfo.setMaxSizeInMegabytes(maxSizeInMegabytes); 
    CreateTopicResult result = service.createTopic(topicInfo);

**ServiceBusContract** オブジェクトの
**listTopics** メソッドを使用すると、指定した名前
のトピックがサービス名前空間に既に存在するかどうか確認できます。

## <a name="bkmk_HowToCreateSubscrip"> </a>サブスクリプションの作成方法

トピック サブスクリプションも **ServiceBusService**
 クラスで作成します。サブスクリプションを指定し、
サブスクリプションの仮想キューに渡すメッセージを制限する
フィルターを設定することができます。

### 既定の (MatchAll) フィルターを適用したサブスクリプションの作成

**MatchAll** フィルターは、新しいサブスクリプションの作成時にフィルターが
指定されていない場合に使用される既定のフィルターです。**MatchAll**
 フィルターを使用すると、トピックに発行されたすべてのメッセージが
サブスクリプションの仮想キューに置かれます。次の例では、
"AllMessages" という名前のサブスクリプションを作成し、既定の **MatchAll**
 フィルターを使用します。

    SubscriptionInfo subInfo = new SubscriptionInfo("AllMessages");
    CreateSubscriptionResult result = 
        service.createSubscription("TestTopic", subInfo);

### フィルターを適用したサブスクリプションの作成

トピックに送信されたメッセージのうち、
特定のトピック サブスクリプション内に表示されるメッセージに絞り込むフィルターを設定することもできます。

サブスクリプションでサポートされるフィルターのうち、最も柔軟性の高いものが、SQL92 のサブセットを実装する
**SqlFilter** です。SQL フィルターは、トピックに発行されるメッセージのプロパティ
に対して適用されます。
SQL フィルターで使用できる式の詳細については、
SqlFilter.SqlExpression 構文の説明を参照してください。

次の例では、"HighMessages" という名前のサブスクリプションを作成し、
**SqlFilter** を適用します。このフィルターでは、カスタム プロパティ
**MessageNumber** が 3 を超えるメッセージのみが選択されます。

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

同様に、次の例では
"LowMessages"
 という名前のサブスクリプションを作成し、SqlFilter を適用します。このフィルターでは、MessageNumber
プロパティが 3 以下
のメッセージのみが選択されます。

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
"AllMessages" トピック
 サブスクリプションにサブスクライブされた受信者に必ず配信され、さらにメッセージの内容に応じて、
"HighMessages" および "LowMessages" トピック サブスクリプション
にサブスクライブされている受信者に対して選択的に配信されます。

## <a name="bkmk_HowToSendMsgs"> </a>メッセージをトピックに送信する方法

メッセージを Service Bus トピックに送信するには、アプリケーションで
**ServiceBusContract** オブジェクトを取得します。次のコードでは、前のコードで
"HowToSample" サービス名前空間内で作成した "TestTopic" トピックにメッセージ
を送信する方法を示しています。

    BrokeredMessage message = new BrokeredMessage("MyMessage");
    service.sendTopicMessage("TestTopic", message);

Service Bus バス トピックに送信されたメッセージは、
**BrokeredMessage** クラスのインスタンスです。**BrokeredMessage** オブジェクトには、一連の
標準的なメソッド (**setLabel**、**TimeToLive** など) と、アプリケーションに固有のカスタム プロパティの保持に使用する
ディクショナリが用意されており、任意のアプリケーション データの
本体が格納されます。アプリケーションでは、
**BrokeredMessage** のコンストラクターにシリアル化可能なオブジェクトを渡すことによってメッセージの本文を設定できます。
その後で、適切な **DataContractSerializer**
を使用してオブジェクトをシリアル化します。この方法に代わって、
**java.io.InputStream** を使用できます。

次の例では、前に示したコード スニペットで取得した
"TestTopic" の **MessageSender** にテスト メッセージを 5 件送信する方法を示しています。
各メッセージの **MessageNumber** プロパティの値が
ループの反復回数に応じてどのように変化するかに注目してください (これによってメッセージを受信するサブスクリプション
が決定されます)。

    for (int i=0; i<5; i++)  {
        // Create message, passing a string message for the body
        BrokeredMessage message = new BrokeredMessage("Test message " + i);
        // Set some additional custom app-specific property
        message.setProperty("MessageNumber", i);
        // Send message to the topic
        service.sendTopicMessage("TestTopic", message);
    }

Service Bus トピックでは、最大 256 MB までのメッセージをサポートしています (
標準とカスタムのアプリケーション プロパティが含まれるヘッダーは
、64 MB が最大サイズです)。トピックで保持されるメッセージ数には上限がありませんが、1 つのトピックで
保持できるメッセージの合計サイズ
には上限があります。このトピックのサイズはトピックの作成時に定義します。
上限は 5 GB です。

## <a name="bkmk_HowToReceiveMsgs"> </a>サブスクリプションからメッセージを受信する方法

サブスクリプションからメッセージを受信する主な方法は、
**ServiceBusContract** オブジェクトを使用することです。メッセージは 2 つの
異なるモードで受信できます。**ReceiveAndDelete** と **PeekLock** です。

**ReceiveAndDelete** モードを使用する場合、受信が 1 回ずつの
動作になります。つまり、Service Bus はメッセージに対する読み取り要求を受け取ると、
メッセージを読み取り中としてマークし、
アプリケーションに返します。**ReceiveAndDelete** モードは最もシンプルなモデルで、障害発生時にアプリケーション側でメッセージを
処理しないことを許容できるシナリオに最適
です。このことを理解するために、コンシューマーが受信要求を発行した後で、
メッセージを処理する前にクラッシュしたというシナリオ
を考えてみましょう。Service Bus は
メッセージを読み取り済みとしてマークするため、アプリケーションが再起動してメッセージの読み取り
を再開すると、クラッシュ前に読み取られていたメッセージは
見落とされることになります。

**PeekLock** モードでは、メッセージの受信処理が 2 段階の動作になり、メッセージが
失われることが許容できないアプリケーションに対応する
ことができます。サービス バスが要求を受け取ると、次に
読み取られるメッセージを検索して、他のコンシューマーが受信できないようロックしてから、
アプリケーションにメッセージを返します。アプリケーションがメッセージの処理を終えた後
 (または後で処理するために確実に保存した後)、
受信したメッセージに対して **Delete**
 を呼び出して受信処理の第 2 段階を完了します。Service Bus が **Delete** の呼び出しを確認すると、
メッセージが読み取り中としてマークされ、トピックから削除されます。

次の例では、(既定モードではなく) **PeekLock** モードを使用したメッセージの
受信および処理の方法を示しています。次の例では、
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

Service Bus には、アプリケーションにエラーが発生した場合や、メッセージの処理に問題がある場合に
復旧を支援する機能が備わっています。
受信側のアプリケーションが何らかの理由によってメッセージを処理できない場合には、
受信したメッセージについて (**deleteMessage** メソッドの代わりに)
**unlockMessage** メソッドを呼び出すことができます。このメソッドが呼び出されると、Service Bus によって
トピック内のメッセージのロックが解除され、メッセージが再度受信できる状態に変わります。
メッセージを受信するアプリケーションは、以前と同じものでも、別のものでもかまいません。

トピック内でロックされているメッセージにはタイムアウトも設定されています。アプリケーションがクラッシュした場合など、
ロックがタイムアウトになる前にアプリケーションがメッセージの処理に失敗した場合には、
ServiceBus ではメッセージのロックが自動的に解除され、
再度受信できる状態に変わります。

メッセージが処理された後、**deleteMessage** 要求が発行される前に
アプリケーションがクラッシュした場合は、アプリケーションが再起動する際に
メッセージが再配信されます。一般的に、この動作は
 **"1 回以上の処理"** と呼ばれます。つまり、すべてのメッセージが
 1 回以上処理されますが、特定の状況では、同じメッセージが
再配信される可能性があります。重複処理が許されないシナリオの場合、
アプリケーション開発者は重複メッセージの配信を扱うロジックをアプリケーション
に追加する必要があります。通常、
この問題はメッセージの **getMessageId** メソッドを使用して対処します。
このプロパティは配信が試行された後も同じ値を保持します。

## <a name="bkmk_HowToDeleteTopics"> </a>トピックとサブスクリプションを削除する方法

トピックとサブスクリプションを削除する主な方法は、
**ServiceBusContract** オブジェクトを使用することです。
トピックを削除すると、そのトピックに登録されたサブスクリプションもすべて削除されます。サブスクリプションは、個別に削除することもできます。

    // Delete subscriptions
    service.deleteSubscription("TestTopic", "AllMessages");
    service.deleteSubscription("TestTopic", "HighMessages");
    service.deleteSubscription("TestTopic", "LowMessages");

    // Delete a topic
    service.deleteTopic("TestTopic");

# <a name="bkmk_NextSteps"> </a> 次のステップ

これで、サービス バス キューの基本を学習できました。詳細については、MSDN
のトピック「[サービス バス キュー、トピック、およびサブスクリプション][サービス バス キュー、トピック、およびサブスクリプション]」を参照してください。

  [Azure SDK for Java]: http://www.windowsazure.com/ja-jp/develop/java/
  [サービス バス トピックとサブスクリプションとは]: #what-are-service-bus-topics
  [サービス名前空間の作成]: #create-a-service-namespace
  [名前空間の既定の管理資格情報の取得]: #obtain-default-credentials
  [サービス バスを使用するようにアプリケーションを構成する]: #bkmk_ConfigYourApp
  [方法: トピックを作成する]: #bkmk_HowToCreateTopic
  [方法: サブスクリプションを作成する]: #bkmk_HowToCreateSubscrip
  [方法: メッセージをトピックに送信する]: #bkmk_HowToSendMsgs
  [方法: サブスクリプションからメッセージを受信する]: #bkmk_HowToReceiveMsgs
  [方法: アプリケーションのクラッシュと読み取り不能のメッセージを処理する]: #bkmk_HowToHandleAppCrash
  [方法: トピックとサブスクリプションを削除する]: #bkmk_HowToDeleteTopics
  [次のステップ]: #bkmk_NextSteps
  [サービス バス キュー、トピック、およびサブスクリプション]: http://msdn.microsoft.com/library/windowsazure/hh367516.aspx
