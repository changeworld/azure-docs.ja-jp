<properties linkid="dev-java-how-to-service-bus-queues" urlDisplayName="サービス バス キュー" pageTitle="サービス バス キューの使用方法 (Java) - Azure" metaKeywords="Azure サービス バス キュー, Azure キュー, Azure メッセージング, Azure キュー Java" description="Azure でのサービス バス キューの使用方法を学習します。コード サンプルは Java で記述されています。" metaCanonical="" services="service-bus" documentationCenter="Java" title="サービス バス キューの使用方法" authors="waltpo" solutions="" manager="" editor="mollybos" />








# サービス バス キューの使用方法

このガイドでは、サービス バス キューの使用方法について説明します。サンプルは Java で記述され、
[Azure SDK for Java][] を利用しています。紹介するシナリオは、
**キューの作成**、**メッセージの送受信**、および
**キューの削除**です。

## 目次

-   [サービス バス キューとは][]
-   [サービス名前空間の作成][]
-   [名前空間の既定の管理資格情報の取得][]
-   [サービス バスを使用するようにアプリケーションを構成する][]
-   [方法: セキュリティ トークン プロバイダーを作成する][]
-   [How to: キューを作成する][How to: Create a Security Token Provider]
-   [How to: メッセージをキューに送信する][]
-   [How to: キューからメッセージを受信する][]
-   [How to: アプリケーションのクラッシュと読み取り不能のメッセージを処理する][]
-   [次のステップ][]

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

## <a name="bkmk_ConfigApp"> </a>サービス バスを使用するようにアプリケーションを構成する

次の import ステートメントを Java ファイルの先頭に追加します。

	// Include the following imports to use service bus APIs
	import com.microsoft.windowsazure.services.serviceBus.*;
	import com.microsoft.windowsazure.services.serviceBus.models.*; 
	import com.microsoft.windowsazure.services.core.*; 
	import javax.xml.datatype.*;
	
## <a name="bkmk_HowToCreateQueue"> </a>キューの作成方法

サービス バス キューの管理処理は、**ServiceBusContract** クラスを
使用して実行できます。**ServiceBusContract** オブジェクトは、
管理用のトークン アクセス許可をカプセル化した適切な構成で作成します。
**ServiceBusContract** クラスでのみ Azure サービス バス トピックとの
やり取りが可能です。

**ServiceBusService** クラスには、キューの作成、列挙、および削除のための
メソッドが用意されています。次の例では、**ServiceBusService** オブジェクトを使用して、
"HowToSample" 名前空間の "TestQueue" という名前のキューを作成する方法を示しています。

    Configuration config = 
    	ServiceBusConfiguration.configureWithWrapAuthentication(
          "HowToSample",
          "your_service_bus_owner",
          "your_service_bus_key",
          ".servicebus.windows.net",
          "-sb.accesscontrol.windows.net/WRAPv0.9");

    ServiceBusContract service = ServiceBusService.create(config);
    QueueInfo queueInfo = new QueueInfo("TestQueue");
    try
    {     
		CreateQueueResult result = service.createQueue(queueInfo);
    }
	catch (ServiceException e)
	{
	    System.out.print("ServiceException encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

QueueInfo には、キューのプロパティを調整できるメソッドが用意されて
います (たとえば、キューに送信されるメッセージに対して既定の "有効期間" 値が
適用されるように設定することができます)。次の例では、名前が "TestQueue"、
最大サイズが 5 GB であるキューを作成する方法を示しています。

    long maxSizeInMegabytes = 5120;
    QueueInfo queueInfo = new QueueInfo("TestQueue");
    queueInfo.setMaxSizeInMegabytes(maxSizeInMegabytes); 
    CreateQueueResult result = service.createQueue(queueInfo);

**ServiceBusContract** オブジェクトの **listQueues** メソッドを使用すると、
指定した名前のキューがサービス名前空間に既に存在するかどうかを
確認できます。

## <a name="bkmk_HowToSendMsgs"> </a>メッセージをキューに送信する方法

メッセージをサービス バス キューに送信するには、アプリケーションで 
**ServiceBusContract** オブジェクトを取得します。次のコードでは、前のコードで "HowToSample" サービス名前空間内で
作成した "TestQueue" キューにメッセージを送信する方法を
示しています。

    try
    {
        BrokeredMessage message = new BrokeredMessage("MyMessage");
        service.sendQueueMessage("TestQueue", message);
    }
    catch (ServiceException e) 
    {
        System.out.print("ServiceException encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

サービス バス キューに送信されたメッセージ (およびサービス バス キューから受信したメッセージ) は、
**BrokeredMessage** クラスのインスタンスになります。**BrokeredMessage** オブジェクトには、
一連の標準的なメソッド (**getLabel**、**getTimeToLive**、
**setLabel**、**setTimeToLive** など) と、アプリケーションに
固有のカスタム プロパティの保持に使用するディクショナリが用意されており、
任意のアプリケーション データの本体が格納されます。アプリケーションでは、**BrokeredMessage** の
コンストラクターにシリアル化可能なオブジェクトを渡すことによって
メッセージの本文を設定できます。その後で、適切なシリアライザーを使用して
オブジェクトをシリアル化します。この方法に代わって、**java.IO.InputStream** を
使用できます。

次の例では、前に示したコード スニペットで取得した "TestQueue" の 
**MessageSender** にテスト メッセージを 5 件送信する方法を示しています。

    for (int i=0; i<5; i++)
    {
         // Create message, passing a string message for the body.
         BrokeredMessage message = new BrokeredMessage("Test message " + i);
         // Set an additional app-specific property.
         message.setProperty("MyProperty", i); 
         // Send message to the queue
         service.sendQueueMessage("TestQueue", message);
    }

サービス バス キューでは、最大 256 KB までのメッセージをサポートして
います (標準とカスタムのアプリケーション プロパティが含まれるヘッダー
の最大サイズは 64 KB です)。キューで保持されるメッセージ数には上限が
ありませんが、キュー 1 つあたりが保持できるメッセージの合計サイズには
上限があります。このキューのサイズはキューの作成時に定義します。
上限は 5 GB です。

## <a name="bkmk_HowToReceiveMsgs"> </a>キューからメッセージを受信する方法

キューからメッセージを受信する主な方法は、
**ServiceBusContract** オブジェクトを使用することです。メッセージは 2 つの異なるモードで受信できます。
**ReceiveAndDelete** と **PeekLock** です。

**ReceiveAndDelete** モードを使用する場合、受信が 1 回ずつの動作に
なります。つまり、サービス バス はキュー内のメッセージに対する読み取り要求を
受け取ると、メッセージを読み取り中としてマークし、アプリケーションに
返します。**ReceiveAndDelete** モード (既定) は最も
シンプルなモデルであり、障害発生時にアプリケーション側で
メッセージを処理しないことを許容できるシナリオに
最適です。このことを理解するために、コンシューマーが受信要求を発行した後で、
メッセージを処理する前にクラッシュしたというシナリオを考えてみましょう。
サービス バスはメッセージを読み取り済みとしてマークするため、アプリケーションが
再起動してメッセージの読み取りを再開すると、クラッシュ前に読み取られていた
メッセージは見落とされることになります。

**PeekLock** モードでは、メッセージの受信処理が 2 段階の動作になり、
メッセージが失われることが許容できないアプリケーションに対応することが
できます。サービス バスは要求を受け取ると、次に読み取られるメッセージを
検索して、他のコンシューマーが受信できないようロックしてから、
アプリケーションにメッセージを返します。アプリケーションがメッセージの
処理を終えた後 (または後で処理するために確実に保存した後)、
受信したメッセージに対して **Delete** を呼び出して受信処理の
第 2 段階を完了します。サービス バスが **Delete** の呼び出しを確認すると、
メッセージが読み取り中としてマークされ、キューから削除されます。

次の例では、**PeekLock** モード (既定ではない) を使用した
メッセージの受信および処理の方法を示しています。次の例では、
無限ループを使用して、"TestQueue" にメッセージが到着するごとに
処理しています。

    	try
	{
		ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
		opts.setReceiveMode(ReceiveMode.PEEK_LOCK);
	
		while(true)  { 
	         ReceiveQueueMessageResult resultQM = 
	     			service.receiveQueueMessage("TestQueue", opts);
		    BrokeredMessage message = resultQM.getValue();
		    if (message != null && message.getMessageId() != null)
		    {
			    System.out.println("MessageID: " + message.getMessageId());    
			    // Display the queue message.
			    System.out.print("From queue: ");
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
			        message.getProperty("MyProperty"));
			    // Remove message from queue.
			    System.out.println("Deleting this message.");
			    //service.deleteMessage(message);
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

## <a name="bkmk_HowToHandleAppCrashes"> </a>アプリケーションのクラッシュと読み取り不能のメッセージを処理する方法

サービス バスには、アプリケーションにエラーが発生した場合や、メッセージの
処理に問題がある場合に復旧を支援する機能が備わっています。受信側の
アプリケーションが何らかの理由によってメッセージを処理できない場合には、
受信したメッセージについて (**deleteMessage** メソッドの代わりに) 
**unlockMessage** メソッドを呼び出すことができます。このメソッドが呼び出されると、サービス バスによって
キュー内のメッセージのロックが解除され、メッセージが再度受信できる状態に
変わります。メッセージを受信するアプリケーションは、以前と同じものでも、
別のものでもかまいません。

キュー内でロックされているメッセージにはタイムアウトも設定されています。
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

## <a name="bkmk_NextSteps"> </a>次のステップ

これで、サービス バス キューの基本を学習できました。詳細については、MSDN のトピック「[サービス バス キュー、トピックおよびサブスクリプション][]」を参照してください。

  [Azure SDK for Java]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh690953(v=vs.103).aspx
  [サービス バス キューとは]: #what-are-service-bus-queues
  [サービス名前空間の作成]: #create-a-service-namespace
  [名前空間の既定の管理資格情報の取得]: #obtain-default-credentials
  [サービス バスを使用するようにアプリケーションを構成する]: #bkmk_ConfigApp
  [方法: セキュリティ トークン プロバイダーを作成する]: #bkmk_HowToCreateQueue
  [How to: メッセージをキューに送信する]: #bkmk_HowToSendMsgs
  [How to: キューからメッセージを受信する]: #bkmk_HowToReceiveMsgs
  [How to: アプリケーションのクラッシュと読み取り不能のメッセージを処理する]: #bkmk_HowToHandleAppCrashes
  [次のステップ]: #bkmk_NextSteps
  [サービス バス キューの図]: ../../../DevCenter/Java/Media/SvcBusQueues_01_FlowDiagram.jpg
  [Azure の管理ポータル]: http://manage.windowsazure.com/
  [サービス バス ノードのスクリーンショット]: ../../../DevCenter/Java/Media/SvcBusQueues_02_SvcBusNode.jpg
  [名前空間の新規作成のスクリーンショット]: ../../../DevCenter/Java/Media/SvcBusQueues_03_CreateNewSvcNamespace.jpg
  [利用可能な名前空間のスクリーンショット]: ../../../DevCenter/Java/Media/SvcBusQueues_04_SvcBusNode_AvailNamespaces.jpg
  [名前空間の一覧のスクリーンショット]: ../../../DevCenter/Java/Media/SvcBusQueues_05_NamespaceList.jpg
  [プロパティ ウィンドウのスクリーンショット]: ../../../DevCenter/Java/Media/SvcBusQueues_06_PropertiesPane.jpg
  [既定のキー のスクリーンショット]: ../../../DevCenter/Java/Media/SvcBusQueues_07_DefaultKey.jpg
  [サービス バス キュー、トピックおよびサブスクリプション]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh367516.aspx

