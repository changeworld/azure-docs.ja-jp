<properties linkid="dev-nodejs-how-to-service-bus-queues" urlDisplayName="Service Bus Queues" pageTitle="How to use Service Bus queues (Node.js) - Azure" metaKeywords="Azure Service Bus queues, Azure queues, Azure messaging, Azure queues Node.js" description="Learn how to use Service Bus queues in Azure. Code samples written in Node.js." metaCanonical="" services="service-bus" documentationCenter="nodejs" title="How to Use Service Bus Queues" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# サービス バス キューの使用方法

このガイドでは、サービス バス キューの使用方法について説明します。サンプルは JavaScript で記述され、Node.js Azure モジュールを利用しています。紹介するシナリオは、**キューの作成、メッセージの送受信**、および**キューの削除**です。キューの詳細については、「[次のステップ][次のステップ]」のセクションを参照してください。

## 目次

-   [サービス バス キューとは][サービス バス キューとは]
-   [サービス名前空間の作成][サービス名前空間の作成]
-   [名前空間の既定の管理資格情報の取得][名前空間の既定の管理資格情報の取得]
-   [Node.js アプリケーションの作成][Node.js アプリケーションの作成]
-   [サービス バスを使用するようにアプリケーションを構成する][サービス バスを使用するようにアプリケーションを構成する]
-   [方法: キューを作成する][方法: キューを作成する]
-   [方法: メッセージをキューに送信する][方法: メッセージをキューに送信する]
-   [方法: キューからメッセージを受信する][方法: キューからメッセージを受信する]
-   [方法: アプリケーションのクラッシュと読み取り不能のメッセージを処理する][方法: アプリケーションのクラッシュと読み取り不能のメッセージを処理する]
-   [次のステップ][次のステップ]

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

## <a name="create-app"> </a>Node.js アプリケーションの作成

空の Node.js アプリケーションを作成します。Node.js アプリケーションを作成する手順については、[Node.js アプリケーションの作成と Azure Web サイトへのデプロイ][Node.js アプリケーションの作成と Azure Web サイトへのデプロイ]、[Node.js クラウド サービスへのデプロイ][Node.js クラウド サービスへのデプロイ] (Windows PowerShell を使用)、または [WebMatrix による Web サイトの作成とデプロイ][WebMatrix による Web サイトの作成とデプロイ]に関するページを参照してください。

## <a name="configure-app"> </a> サービス バス を使用するようにアプリケーションを構成する

Azure Service Bus を使用するには、Node.js azure パッケージをダウンロードして使用する必要があります。このパッケージには、Service Bus REST サービスと通信するための便利なライブラリのセットが含まれています。

### ノード パッケージ マネージャー (NPM) を使用してパッケージを取得する

1.  **Windows PowerShell for Node.js** コマンド ウィンドウを使用して、サンプル アプリケーションを作成した **c:\\node\\sbqueues\\WebRole1** フォルダーに移動します。

2.  コマンド ウィンドウに「**npm install azure**」と入力すると、次のような出力が生成されます。

        azure@0.7.5 node_modules\azure
        ├── dateformat@1.0.2-1.2.3
        ├── xmlbuilder@0.4.2
        ├── node-uuid@1.2.0
        ├── mime@1.2.9
        ├── underscore@1.4.4
        ├── validator@1.1.1
        ├── tunnel@0.0.2
        ├── wns@0.5.3
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)

3.  手動で **ls** コマンドを実行して、**node\_modules** フォルダーが作成されたことを確認することもできます。このフォルダーで **azure** パッケージを検索します。このパッケージには、Service Bus キューにアクセスするために必要なライブラリが含まれています。

### モジュールのインポート

メモ帳などのテキスト エディターを使用して、アプリケーションの **server.js** ファイルの先頭に次の内容を追加します。

    var azure = require('azure');

### Azure のサービス バス接続の設定

azure モジュールは、Azure Service Bus に接続するために必要な情報として、環境変数 AZURE\_SERVICEBUS\_NAMESPACE および AZURE\_SERVICEBUS\_ACCESS\_KEY を読み取ります。これらの環境変数が設定されていない場合、**createServiceBusService** を呼び出すときにアカウント情報を指定する必要があります。

Azure クラウド サービスの構成ファイルで環境変数を設定する例については、[ストレージを使用する Node.js クラウド サービスに関するトピック][ストレージを使用する Node.js クラウド サービスに関するトピック]を参照してください。

Azure Web サイトの管理ポータルで環境変数を設定する例については、「[Azure テーブル サービスを使用する Node.js Web アプリケーション][Azure テーブル サービスを使用する Node.js Web アプリケーション]」を参照してください。

## <a name="create-queue"> </a>方法: キューを作成する

**ServiceBusService** オブジェクトを使用して、キューを操作できます。次のコードでは、**ServiceBusService** オブジェクトを作成します。**server.js** ファイルの先頭付近の、azure モジュールをインポートするステートメントの後に、このコードを追加します。

    var serviceBusService = azure.createServiceBusService();

**ServiceBusService** オブジェクトで **createQueueIfNotExists** を呼び出すことによって、指定されたキューが返されるか (存在する場合)、指定された名前で新しいキューが作成されます。次のコードでは、**createQueueIfNotExists** を使用して、"myqueue" という名前のキューを作成するか、キューに接続します。

    serviceBusService.createQueueIfNotExists('myqueue', function(error){
        if(!error){
            // Queue exists
        }
    });

**createServiceBusService** は追加のオプションもサポートしています。これにより、メッセージの有効期間や最大キュー サイズなどの既定のキューの設定をオーバーライドできます。次の例では、キューの最大サイズを 5 GB に、メッセージの既定の有効期間を 1 分に設定する方法を示しています。

    var queueOptions = {
          MaxSizeInMegabytes: '5120',
          DefaultMessageTimeToLive: 'PT1M'
        };

    serviceBusService.createQueueIfNotExists('myqueue', queueOptions, function(error){
        if(!error){
            // Queue exists
        }
    });

### フィルター

オプションのフィルター操作は、**ServiceBusService** を使用して行われる操作に適用できます。フィルター操作には、ログ、自動的な再試行などが含まれる場合があります。フィルターは、次のようなシグネチャを実装するオブジェクトです。

        function handle (requestOptions, next)

要求オプションに対するプリプロセスを行った後で、このメソッドは "next" を呼び出して、次のシグネチャのコールバックを渡す必要があります。

        function (returnObject, finalCallback, next)

このコールバックで、returnObject (サーバーへの要求からの応答) の処理の後に、コールバックは next を呼び出すか (他のフィルターの処理を続けるために next が存在する場合)、単に finalCallback を呼び出す必要があります (サービス呼び出しを終了する場合)。

再試行のロジックを実装する 2 つのフィルター (**ExponentialRetryPolicyFilter** と **LinearRetryPolicyFilter**) が、Azure SDK for Node.js に含まれています。次のコードは、**ExponentialRetryPolicyFilter** を使う **ServiceBusService** オブジェクトを作成します。

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);

## <a name="send-messages"> </a>メッセージをキューに送信する方法

メッセージを Service Bus キューに送信するために、アプリケーションによって **ServiceBusService** オブジェクトで **sendQueueMessage** メソッドが呼び出されます。Service Bus キューに送信された (およびキューから受信された) メッセージは **BrokeredMessage** オブジェクトであり、このオブジェクトには、一連の標準的なプロパティ (**Label**、**TimeToLive** など)、アプリケーションに特有のカスタム プロパティの保持に使用するディクショナリ、および任意のアプリケーション データの本体が備わっています。アプリケーションでは、メッセージとして文字列値を渡すことによってメッセージの本文を設定でき、必須の標準プロパティは既定値に設定されます。

次の例では、**sendQueueMessage** を使用して、myqueue という名前のキューにテスト メッセージを送信する方法を示しています。

    var message = {
        body: 'Test message',
        customProperties: {
            testproperty: 'TestValue'
        }};
    serviceBusService.sendQueueMessage('myqueue', message, function(error){
        if(!error){
            // message sent
        }
    });

Service Bus キューでは、最大 256 KB までのメッセージをサポートしています(標準とカスタムのアプリケーション プロパティが含まれるヘッダーは、64 KB が最大サイズです)。キューで保持されるメッセージ数には上限がありませんが、キュー 1 つあたりが保持できるメッセージの合計サイズには上限があります。このキューのサイズはキューの作成時に定義します。上限は 5 GB です。

## <a name="receive-messages"> </a>キューからメッセージを受信する方法

キューからメッセージを受信するには、**ServiceBusService** オブジェクトの **receiveQueueMessage** メソッドを使用します。既定では、メッセージは読み取られるときにキューから削除されますが、省略可能な **isPeekLock** パラメーターを **true** に設定することによって、キューからメッセージを削除せずに、メッセージを読み取って (ピークして) ロックすることができます。

受信操作の中で行われるメッセージの読み取りと削除の既定の動作は、最もシンプルなモデルであり、障害発生時にアプリケーション側でメッセージを処理しないことを許容できるシナリオに最適です。このことを理解するために、コンシューマーが受信要求を発行した後で、メッセージを処理する前にクラッシュしたというシナリオを考えてみましょう。Service Bus はメッセージを読み取り済みとしてマークするため、アプリケーションが再起動してメッセージの読み取りを再開すると、クラッシュ前に読み取られていたメッセージは見落とされることになります。

**isPeekLock** パラメーターが **true** に設定されている場合、受信処理が 2 段階の動作になり、メッセージが失われることが許容できないアプリケーションに対応することができます。Service Bus は要求を受け取ると、次に読み取られるメッセージを検索して、他のコンシューマーが受信できないようロックしてから、アプリケーションにメッセージを返します。アプリケーションがメッセージの処理を終えた後(または後で処理するために確実に保存した後)、**deleteMessage** メソッドを呼び出し、削除するメッセージをパラメーターとして指定して、受信処理の第 2 段階を完了します。**deleteMessage** メソッドによって、メッセージが読み取り中としてマークされ、キューから削除されます。

次の例では、**receiveQueueMessage** を使用したメッセージの受信および処理の方法を示しています。この例では、最初にメッセージを受信して削除し、次に true に設定した **isPeekLock** を使用してメッセージを受信した後、**deleteMessage** を使用してメッセージを削除します。

    serviceBusService.receiveQueueMessage('taskqueue', function(error, receivedMessage){
        if(!error){
            // Message received and deleted
        }
    });
    serviceBusService.receiveQueueMessage(queueName, { isPeekLock: true }, function(error, lockedMessage){
        if(!error){
            // Message received and locked
            serviceBusService.deleteMessage(lockedMessage, function (deleteError){
                if(!deleteError){
                    // Message deleted
                }
            }
        }
    });

## <a name="handle-crashes"> </a>アプリケーションのクラッシュと読み取り不能のメッセージを処理する方法

Service Bus には、アプリケーションにエラーが発生した場合や、メッセージの処理に問題がある場合に復旧を支援する機能が備わっています。受信側のアプリケーションが何らかの理由によってメッセージを処理できない場合には、**ServiceBusService** オブジェクトの**unlockMessage** メソッドを呼び出すことができます。このメソッドが呼び出されると、Service Bus によってキュー内のメッセージのロックが解除され、メッセージが再度受信できる状態に変わります。メッセージを受信するアプリケーションは、以前と同じものでも、別のものでもかまいません。

キュー内でロックされているメッセージにはタイムアウトも設定されています。アプリケーションがクラッシュした場合など、ロックがタイムアウトになる前にアプリケーションがメッセージの処理に失敗した場合には、メッセージのロックが自動的に解除され、再度受信できる状態に変わります。

メッセージが処理された後、**deleteMessage** メソッドが呼び出される前にアプリケーションがクラッシュした場合は、アプリケーションが再起動する際にメッセージが再配信されます。一般的に、この動作は**"1 回以上の処理"** と呼ばれます。つまり、すべてのメッセージが 1 回以上処理されますが、特定の状況では、同じメッセージが再配信される可能性があります。重複処理が許されないシナリオの場合、重複メッセージの配信を扱うロジックをアプリケーションに追加する必要があります。通常、この問題はメッセージの**MessageId** プロパティを使用して対処します。このプロパティは配信が試行された後も同じ値を保持します。

## <a name="next-steps"> </a> 次のステップ

これで、Service Bus キューの基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先を参照してください。

-   MSDN リファレンス: [キュー、トピック、およびサブスクリプション][キュー、トピック、およびサブスクリプション]
-   GitHub の [Azure SDK for Node][Azure SDK for Node] リポジトリ

  [次のステップ]: #next-steps
  [サービス バス キューとは]: #what-are-service-bus-queues
  [サービス名前空間の作成]: #create-a-service-namespace
  [名前空間の既定の管理資格情報の取得]: #obtain-default-credentials
  [Node.js アプリケーションの作成]: #create-app
  [サービス バスを使用するようにアプリケーションを構成する]: #configure-app
  [方法: キューを作成する]: #create-queue
  [方法: メッセージをキューに送信する]: #send-messages
  [方法: キューからメッセージを受信する]: #receive-messages
  [方法: アプリケーションのクラッシュと読み取り不能のメッセージを処理する]: #handle-crashes
  [howto-service-bus-queues]: ../includes/howto-service-bus-queues.md
  [Node.js アプリケーションの作成と Azure Web サイトへのデプロイ]: /ja-jp/develop/nodejs/tutorials/create-a-website-(mac)/
  [Node.js クラウド サービスへのデプロイ]: /ja-jp/documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [WebMatrix による Web サイトの作成とデプロイ]: /ja-jp/develop/nodejs/tutorials/web-site-with-webmatrix/
  [ストレージを使用する Node.js クラウド サービスに関するトピック]: /ja-jp/develop/nodejs/tutorials/web-app-with-storage/
  [Azure テーブル サービスを使用する Node.js Web アプリケーション]: /ja-jp/develop/nodejs/tutorials/web-site-with-storage/
  [キュー、トピック、およびサブスクリプション]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh367516.aspx
  [Azure SDK for Node]: https://github.com/WindowsAzure/azure-sdk-for-node
