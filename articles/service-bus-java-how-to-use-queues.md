<properties linkid="dev-java-how-to-service-bus-queues" urlDisplayName="Service Bus Queues" pageTitle="How to use Service Bus queues (Java) - Azure" metaKeywords="Azure Service Bus queues, Azure queues, Azure messaging, Azure queues Java" description="Learn how to use Service Bus queues in Azure. Code samples written in Java." metaCanonical="" services="service-bus" documentationCenter="Java" title="How to Use Service Bus Queues" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm" />

# サービス バス キューの使用方法

このガイドでは、サービス バス キューの使用方法について説明します。サンプルは Java で記述され、[Azure SDK for Java][Azure SDK for Java] を利用しています。
紹介するシナリオは、**キューの作成**、**メッセージの送受信**、および**キューの削除**です。

## 目次

-   [サービス バス キューとは][サービス バス キューとは]
-   [サービス名前空間の作成][サービス名前空間の作成]
-   [名前空間の既定の管理資格情報の取得][名前空間の既定の管理資格情報の取得]
-   [サービス バスを使用するようにアプリケーションを構成する][サービス バスを使用するようにアプリケーションを構成する]
-   [方法: セキュリティ トークン プロバイダーを作成する][方法: セキュリティ トークン プロバイダーを作成する]
-   [方法: キューを作成する][方法: セキュリティ トークン プロバイダーを作成する]
-   [方法: メッセージをキューに送信する][方法: メッセージをキューに送信する]
-   [方法: キューからメッセージを受信する][方法: キューからメッセージを受信する]
-   [方法: アプリケーションのクラッシュと読み取り不能のメッセージを処理する][方法: アプリケーションのクラッシュと読み取り不能のメッセージを処理する]
-   [次のステップ][次のステップ]

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

## <a name="bkmk_ConfigApp"> </a> サービス バス を使用するようにアプリケーションを構成する

次の import ステートメントを Java ファイルの先頭に追加します。

    // Include the following imports to use service bus APIs
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*; 
    import com.microsoft.windowsazure.services.core.*; 
    import javax.xml.datatype.*;

## <a name="bkmk_HowToCreateQueue"> </a>方法: キューを作成する

サービス バス キューの管理処理は、
**ServiceBusContract** クラスを使用して実行できます。**ServiceBusContract** オブジェクトは、管理用のトークン アクセス許可をカプセル化した適切な構成で作成します。
**ServiceBusContract** クラスでのみ Azure Service Bus トピックとのやり取りが可能です。

**ServiceBusService** クラスには、キューの作成、列挙、および削除のためのメソッドが用意されています。
次の例では、**ServiceBusService** オブジェクトを使用して、"HowToSample" 名前空間の "TestQueue" という名前のキューを作成する方法を示しています。

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

QueueInfo には、キューのプロパティを調整できるメソッドが用意されています (キューに送信されるメッセージに適用される既定の "有効期限" の値を設定するメソッドなど)。
次の例では、名前が "TestQueue"、最大サイズが 5 GB であるキューを作成する方法を示しています。

    long maxSizeInMegabytes = 5120;
    QueueInfo queueInfo = new QueueInfo("TestQueue");
    queueInfo.setMaxSizeInMegabytes(maxSizeInMegabytes); 
    CreateQueueResult result = service.createQueue(queueInfo);

**ServiceBusContract** オブジェクトの **listQueues** メソッドを使用すると、指定した名前のキューがサービス名前空間に既に存在するかどうかを確認できます。

## <a name="bkmk_HowToSendMsgs"> </a>メッセージをキューに送信する方法

メッセージをサービス バス キューに送信するには、アプリケーションで
**ServiceBusContract** オブジェクトを取得します。次のコードでは、前のコードで "HowToSample" サービス名前空間内で作成した "TestQueue" キューにメッセージを送信する方法を示しています。

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

Service Bus キューに送信されたメッセージ (および Service Bus キューから受信したメッセージ) は、**BrokeredMessage** クラスのインスタンスになります。
**BrokeredMessage** オブジェクトには、一連の標準的なメソッド (**getLabel**、**getTimeToLive**、**setLabel**、**setTimeToLive** など)、アプリケーションに固有のカスタム プロパティの保持に使用するディクショナリ、および任意のアプリケーション データの本体が備わっています。
アプリケーションでは、**BrokeredMessage** のコンストラクターにシリアル化可能なオブジェクトを渡すことによってメッセージの本文を設定できます。その後で、適切なシリアライザーを使用してオブジェクトをシリアル化します。
この方法に代わって、**java.IO.InputStream** を使用できます。

次の例では、前に示したコード スニペットで取得した "TestQueue" の **MessageSender** に 5 通のテスト メッセージを送信する方法を示しています。

    for (int i=0; i<5; i++)
    {
         // Create message, passing a string message for the body.
         BrokeredMessage message = new BrokeredMessage("Test message " + i);
         // Set an additional app-specific property.
         message.setProperty("MyProperty", i); 
         // Send message to the queue
         service.sendQueueMessage("TestQueue", message);
    }

Service Bus キューでは、最大 256 KB までのメッセージをサポートしています(標準とカスタムのアプリケーション プロパティが含まれるヘッダーは、64 KB が最大サイズです)。キューで保持されるメッセージ数には上限がありませんが、キュー 1 つあたりが保持できるメッセージの合計サイズには上限があります。このキューのサイズはキューの作成時に定義します。上限は 5 GB です。

## <a name="bkmk_HowToReceiveMsgs"> </a>キューからメッセージを受信する方法

キューからメッセージを受信する主な方法は、**ServiceBusContract** オブジェクトを使用することです。メッセージは、**ReceiveAndDelete** および **PeekLock** の 2 つの異なるモードで受信できます。

**ReceiveAndDelete** モードを使用する場合、受信が 1 回ずつの動作になります。つまり、Service Bus はキュー内のメッセージに対する読み取り要求を受け取ると、メッセージを読み取り中としてマークし、アプリケーションに返します。**ReceiveAndDelete** モード (既定) は最もシンプルなモデルであり、障害発生時にアプリケーション側でメッセージを処理しないことを許容できるシナリオに最適です。このことを理解するために、コンシューマーが受信要求を発行した後で、メッセージを処理する前にクラッシュしたというシナリオを考えてみましょう。Service Bus はメッセージを読み取り済みとしてマークするため、アプリケーションが再起動してメッセージの読み取りを再開すると、クラッシュ前に読み取られていたメッセージは見落とされることになります。

**PeekLock** モードでは、メッセージの受信処理が 2 段階の動作になり、メッセージが失われることが許容できないアプリケーションに対応することができます。Service Bus は要求を受け取ると、次に読み取られるメッセージを検索して、他のコンシューマーが受信できないようロックしてから、アプリケーションにメッセージを返します。アプリケーションがメッセージの処理を終えた後 (または後で処理するために確実に保存した後)、受信したメッセージに対して **Delete** を呼び出して受信処理の第 2 段階を完了します。Service Bus が **Delete** の呼び出しを確認すると、メッセージが読み取り中としてマークされ、キューから削除されます。

次の例では、**PeekLock** モード (既定ではない) を使用したメッセージの受信および処理の方法を示しています。次の例では、無限ループを使用して、"TestQueue" にメッセージが到着するごとに処理しています。

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

Service Bus には、アプリケーションにエラーが発生した場合や、メッセージの処理に問題がある場合に復旧を支援する機能が備わっています。受信側のアプリケーションが何らかの理由によってメッセージを処理できない場合には、受信したメッセージについて (**deleteMessage** メソッドの代わりに) **unlockMessage** メソッドを呼び出すことができます。このメソッドが呼び出されると、Service Bus によってキュー内のメッセージのロックが解除され、メッセージが再度受信できる状態に変わります。メッセージを受信するアプリケーションは、以前と同じものでも、別のものでもかまいません。

キュー内でロックされているメッセージにはタイムアウトも設定されています。アプリケーションがクラッシュした場合など、ロックがタイムアウトになる前にアプリケーションがメッセージの処理に失敗した場合には、メッセージのロックが自動的に解除され、再度受信できる状態に変わります。

メッセージが処理された後、**deleteMessage** 要求が発行される前にアプリケーションがクラッシュした場合は、アプリケーションが再起動する際にメッセージが再配信されます。一般的に、この動作は **"1 回以上の処理"** と呼ばれます。つまり、すべてのメッセージが 1 回以上処理されますが、特定の状況では、同じメッセージが再配信される可能性があります。重複処理が許されないシナリオの場合、重複メッセージの配信を扱うロジックをアプリケーションに追加する必要があります。通常、この問題はメッセージの **getMessageId** メソッドを使用して対処します。このプロパティは配信が試行された後も同じ値を保持します。

## <a name="bkmk_NextSteps"> </a> 次のステップ

これで、Service Bus キューの基本を学習できました。詳細については、MSDN のトピック「[Service Bus キュー、トピックおよびサブスクリプション][Service Bus キュー、トピックおよびサブスクリプション]」を参照してください。

  [Azure SDK for Java]: http://azure.microsoft.com/ja-jp/develop/java/
  [サービス バス キューとは]: #what-are-service-bus-queues
  [サービス名前空間の作成]: #create-a-service-namespace
  [名前空間の既定の管理資格情報の取得]: #obtain-default-credentials
  [サービス バスを使用するようにアプリケーションを構成する]: #bkmk_ConfigApp
  [方法: セキュリティ トークン プロバイダーを作成する]: #bkmk_HowToCreateQueue
  [方法: メッセージをキューに送信する]: #bkmk_HowToSendMsgs
  [方法: キューからメッセージを受信する]: #bkmk_HowToReceiveMsgs
  [方法: アプリケーションのクラッシュと読み取り不能のメッセージを処理する]: #bkmk_HowToHandleAppCrashes
  [次のステップ]: #bkmk_NextSteps
  [howto-service-bus-queues]: ../includes/howto-service-bus-queues.md
  [Service Bus キュー、トピックおよびサブスクリプション]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh367516.aspx
