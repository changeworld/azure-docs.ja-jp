<properties linkid="dev-nodejs-how-to-service-bus-topics" urlDisplayName="サービス バス トピック" pageTitle="サービス バス トピックの使用方法 (Node.js) - Azure" metaKeywords="Azure サービス バス トピックの概要, サービス バス トピックの概要, Azure 発行/サブスクライブ メッセージング, Azure メッセージング トピックおよびサブスクリプション, サービス バス トピック Node.js" description="Azure での サービス バス のトピックとサブスクリプションの使用方法について学習します。コード サンプルは Node.js アプリケーション向けに作成されています。" metaCanonical="" services="service-bus" documentationCenter="Node.js" title="サービス バス トピック/サブスクリプションの使用方法" authors="" solutions="" manager="" editor="" />





# サービス バス トピック/サブスクリプションの使用方法

このガイドでは、Node.js アプリケーションからサービス バスのトピックと
サブスクリプションを使用する方法について説明します。ここでは、**トピックとサブスクリプションの作成、
サブスクリプション フィルターの作成、トピックへのメッセージの
送信**、**サブスクリプションからのメッセージの受信**、
**トピックとサブスクリプションの削除**などのシナリオについて説明します。トピックとサブスクリプションの詳細については、
「[次のステップ][]」を参照してください。

## 目次

-   [サービス バス トピックとサブスクリプションとは][]
-   [サービス名前空間の作成][]
-   [名前空間の既定の管理資格情報の取得][]
-   [Node.js アプリケーションの作成](#create-app)
-   [サービス バス を使用するようにアプリケーションを構成する](#configure-app)
-   [How to: トピックを作成する](#create-topic)
-   [How to: サブスクリプションを作成する](#create-subscription)
-   [How to: メッセージをトピックに送信する](#send-messages)
-   [How to: サブスクリプションからメッセージを受信する](#receive-messages)
-   [How to: アプリケーションのクラッシュと読み取り不能のメッセージを処理する](#handle-crashes)
-   [How to: トピックとサブスクリプションを削除する](#delete)
-   [次のステップ](#next-steps)

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

##<a name="create-app"></a> Node.js アプリケーションの作成

空の Node.js アプリケーションを作成します。Node.js アプリケーションを作成する手順については、[Node.js アプリケーションの作成と Azure Web サイトへのデプロイ]、[Node.js クラウド サービスへのデプロイ] (Windows PowerShell を使用)、または [WebMatrix による Web サイトの作成とデプロイ]に関するページを参照してください。

##<a name="configure-app"></a> サービス バス を使用するようにアプリケーションを構成する

Azure のサービス バスを使用するには、Node.js azure パッケージを
ダウンロードして使用する必要があります。このパッケージには、サービス バス REST サービスと通信
するための便利なライブラリのセットが含まれています。

### ノード パッケージ マネージャー (NPM) を使用してパッケージを取得する

1.  **PowerShell** (Windows)、**Terminal** (Mac)、**Bash** (Unix) などのコマンド ライン インターフェイスを使用して、サンプル アプリケーションを作成したフォルダーに移動します。

2.  コマンド ウィンドウに「**npm install azure**」と入力すると、
    次のような出力が生成されます。

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

3.  手動で **ls** コマンドを実行して、**node\_modules** 
       フォルダーが作成されたことを確認することもできます。このフォルダーで **azure** 
    パッケージを検索します。このパッケージには、サービス バス トピックにアクセス
    するために必要なライブラリが含まれています。

### モジュールのインポート

メモ帳などのテキスト エディターを使用して、アプリケーションの 
**server.js** ファイルの先頭に次の内容を追加します。

    var azure = require('azure');

### Azure のサービス バス接続の設定

azure モジュールは、Azure サービス バス に接続するために必要な情報として、環境変数 AZURE\_SERVICEBUS\_NAMESPACE および AZURE\_SERVICEBUS\_ACCESS\_KEY を読み取ります。これらの環境変数が設定されていない場合、**createServiceBusService** を呼び出すときにアカウント
情報を指定する必要があります。

Azure クラウド サービスの構成ファイルで環境変数を設定する例については、[ストレージを使用する Node.js クラウド サービスに関するトピック]を参照してください。

Azure Web サイトの管理ポータルで環境変数を設定する例については、[ストレージを使用する Node.js Web アプリケーションに関するトピック]を参照してください。

##<a name="create-topic"></a> トピックを作成する方法

**ServiceBusService** オブジェクトを使用して、トピックを操作できます。次の
コードでは、**ServiceBusService** オブジェクトを作成します。**server.js** ファイルの
先頭付近の、azure モジュールをインポートするステートメントの後に、このコードを追加します。

    var serviceBusService = azure.createServiceBusService();

**ServiceBusService** オブジェクトで **createTopicIfNotExists** を
呼び出すことによって、指定されたトピックが返されるか (存在する場合)、
指定された名前で新しいトピックが作成されます。次のコードでは、
**createTopicIfNotExists** を使用して、"MyTopic" という名前のトピックを作成、
またはトピックに接続します。

    serviceBusService.createTopicIfNotExists('MyTopic',function(error){
        if(!error){
            // Topic was created or exists
            console.log('topic created or exists.');
        }
    });

**createServiceBusService** は追加のオプションもサポートしています。
これにより、メッセージの有効期間や最大トピック サイズなどの既定のトピックの
設定をオーバーライドできます。次の例では、トピックの最大サイズを 5 GB に、
メッセージの有効期間を 1 分に設定する方法を示しています。

    var topicOptions = {
            MaxSizeInMegabytes: '5120',
            DefaultMessageTimeToLive: 'PT1M'
        };

    serviceBusService.createTopicIfNotExists('MyTopic', topicOptions, function(error){
        if(!error){
            // topic was created or exists
        }
    });

###フィルター

オプションのフィルター操作は、**ServiceBusService** を使用して行われる操作に適用できます。フィルター操作には、ログ、自動的な再試行などが含まれる場合があります。フィルターは、次のようなシグネチャを実装するオブジェクトです。

		function handle (requestOptions, next)

要求オプションに対するプリプロセスを行った後で、このメソッドは "next" を呼び出して、次のシグネチャのコールバックを渡す必要があります。

		function (returnObject, finalCallback, next)

このコールバックで、returnObject (サーバーへの要求からの応答) の処理の後に、コールバックは next を呼び出すか (他のフィルターの処理を続けるために next が存在する場合)、単に finalCallback を呼び出す必要があります (サービス呼び出しを終了する場合)。

再試行のロジックを実装する 2 つのフィルター (**ExponentialRetryPolicyFilter** と **LinearRetryPolicyFilter**) が、Azure SDK for Node.js に含まれています。 次のコードは、**ExponentialRetryPolicyFilter** を使う **ServiceBusService** オブジェクトを作成します。

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);

##<a name="create-subscription"></a> サブスクリプションの作成方法

トピック サブスクリプションも、**ServiceBusService** オブジェクトで
作成します。サブスクリプションを指定し、サブスクリプションの仮想キューに配信する
メッセージを制限するフィルターを設定することができます。

<div class="dev-callout">
<strong>注</strong>
<p>サブスクリプションは永続的であり、サブスクリプション、または
サブスクリプションが関連付けられているトピックが削除されるまで存在し続けます。アプリケーションに
サブスクリプションを作成するロジックが含まれている場合は、最初に 
<strong>getSubscription</strong> メソッドを使用して、
サブスクリプションが既に存在しているかどうかを確認する必要があります。</p>
</div>

### 既定の (MatchAll) フィルターを適用したサブスクリプションの作成

**MatchAll** フィルターは、新しいサブスクリプションの作成時にフィルターが
指定されていない場合に使用される既定のフィルターです。**MatchAll** フィルターを
使用すると、トピックに発行されたすべてのメッセージがサブスクリプションの
仮想キューに置かれます。次の例では、"AllMessages" という
名前のサブスクリプションを作成し、既定の **MatchAll** フィルターを
使用します。

    serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
        if(!error){
            // subscription created
        }
    });

### フィルターを適用したサブスクリプションの作成

トピックに送信されたメッセージのうち、特定のトピック サブスクリプション内に表示
されるメッセージに絞り込めるフィルターを設定することもできます。

サブスクリプションでサポートされるフィルターのうち、最も柔軟性の高いものが、
SQL92 のサブセットを実装する **SqlFilter** です。SQL フィルターは、トピックに
発行されるメッセージのプロパティに対して適用されます。
SQL フィルターで使用できる式の詳細については、
[SqlFilter.SqlExpression][SqlFilter.SqlExpression] 構文の説明を参照してください。

フィルターをサブスクリプションに追加するには、**ServiceBusService** 
オブジェクトの **createRule** メソッドを使用します。このメソッドによって、新しいフィルターを
既存のサブスクリプションに追加できます。

> [WACOM.NOTE]

> 既定のフィルターはすべての新しいサブスクリプションに自動的に適用
されるので、最初に既定のフィルターを削除する必要があります。削除しないと、
<strong>MatchAll</strong> は指定される他のすべてのフィルターをオーバーライドします。既定のルールを
削除するには、<strong>ServiceBusService</strong> オブジェクトの 
<strong>deleteRule</strong> メソッドを使用します。

次の例では、"HighMessages" という名前のサブスクリプションを作成し、
**SqlFilter** を適用します。このフィルターでは、カスタム プロパティ 
**messagenumber** が 3 を超えるメッセージのみが選択されます。

    serviceBusService.createSubscription('MyTopic', 'HighMessages', function (error){
        if(!error){
            // subscription created
            rule.create();
        }
    });
    var rule={
        deleteDefault: function(){
            serviceBusService.deleteRule('MyTopic',
                'HighMessages', 
                azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME, 
                rule.handleError);
        },
        create: function(){
            var ruleOptions = {
                sqlExpressionFilter: 'messagenumber > 3'
            };
            rule.deleteDefault();
            serviceBusService.createRule('MyTopic', 
                'HighMessages', 
                'HighMessageFilter', 
                ruleOptions, 
                rule.handleError);
        },
        handleError: function(error){
            if(error){
                console.log(error)
            }
        }
    }

同様に、次の例では "LowMessages" という名前のサブスクリプションを
作成し、**SqlFilter** を適用します。このフィルターでは、
**messagenumber** プロパティが 3 以下のメッセージのみが選択されます。

    serviceBusService.createSubscription('MyTopic', 'LowMessages', function (error){
        if(!error){
            // subscription created
            rule.create();
        }
    });
    var rule={
        deleteDefault: function(){
            serviceBusService.deleteRule('MyTopic',
                'LowMessages', 
                azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME, 
                rule.handleError);
        },
        create: function(){
            var ruleOptions = {
                sqlExpressionFilter: 'messagenumber <= 3'
            };
            rule.deleteDefault();
            serviceBusService.createRule('MyTopic', 
                'LowMessages', 
                'LowMessageFilter', 
                ruleOptions, 
                rule.handleError);
        },
        handleError: function(error){
            if(error){
                console.log(error)
            }
        }
    }

メッセージが "MyTopic" に送信されると、そのメッセージは "AllMessages" トピック 
サブスクリプションをサブスクライブした受信者に必ず配信され、さらにメッセージの
内容に応じて、"HighMessages" および "LowMessages" トピック サブスクリプションを
サブスクライブしている受信者に対して選択的に配信されます。

##<a name="send-messages"></a> メッセージをトピックに送信する方法

メッセージをサービス バス トピックに送信するには、アプリケーションで 
**ServiceBusService** オブジェクトの **sendTopicMessage** メソッドを使用する必要があります。
サービス バス トピックに送信されたメッセージは、**BrokeredMessage** オブジェクトです。
**BrokeredMessage** オブジェクトには、(**Label**、**TimeToLive** などの) 
標準的なプロパティ、アプリケーション固有のカスタム プロパティの保持に使用する
ディクショナリ、および文字列データの本体が備わっています。
アプリケーションでは、文字列値を **sendTopicMessage**  に渡すことに
よってメッセージの本文を設定でき、必須の標準プロパティは既定値に
設定されます。

次の例では、"MyTopic" に 5 通のテスト メッセージを送信する方法を示して
います。各メッセージの **messagenumber** プロパティの値がループの反復
回数に応じてどのように変化するかに注目してください (これによって
メッセージを受信するサブスクリプションが決定されます)。

    var message = {
        body: '',
        customProperties: {
            messagenumber: 0
        }
    }

    for (i = 0;i < 5;i++) {
        message.customProperties.messagenumber=i;
        message.body='This is Message #'+i;
        serviceBusService.sendTopicMessage(topic, message, function(error) {
          if (error) {
            console.log(error);
          }
        });
    }

サービス バス トピックでは、最大 256 MB までのメッセージをサポートしています 
(標準とカスタムのアプリケーション プロパティが含まれるヘッダーの最大サイズは 
64 MB です)。トピックで保持されるメッセージ数には
上限がありませんが、1 つのトピックで保持できるメッセージの合計サイズには
上限があります。このトピックのサイズはトピックの作成時に定義します。
上限は 5 GB です。

##<a name="receive-messages"></a> サブスクリプションからメッセージを受信する方法

サブスクリプションからメッセージを受信するには、
**ServiceBusService** オブジェクトの **receiveSubscriptionMessage** 
メソッドを使用します。既定では、メッセージは読み取られるときにサブスクリプションから削除されますが、
サブスクリプションからメッセージを削除せずに、メッセージを読み取って 
(ピークして) ロックすることができます。そのためには、省略可能なパラメーター 
**isPeekLock** を **true** に設定します。

受信操作の中で行われるメッセージの読み取りと削除の既定の動作は、
最もシンプルなモデルであり、障害発生時にアプリケーション側でメッセージを
処理しないことを許容できるシナリオに最適です。
このことを理解するために、コンシューマーが受信要求を
発行した後で、メッセージを処理する前にクラッシュしたというシナリオを
考えてみましょう。サービス バスはメッセージを読み取り済みとしてマークするため、アプリケーションが
再起動してメッセージの読み取りを再開すると、クラッシュ前に読み取られていた
メッセージは見落とされることになります。

**isPeekLock** パラメーターが **true** に設定されている場合、受信処理が 
2 段階の動作になり、メッセージが失われることが許容できないアプリケーションに
対応することができます。サービス バス は要求を受け取ると、
次に読み取られるメッセージを検索して、他のコンシューマーが受信できないよう
ロックしてから、アプリケーションにメッセージを返します。
アプリケーションはメッセージの処理を終えた後 (または後で処理するために
確実に保存した後)、**deleteMessage** メソッドを呼び出し、削除する
メッセージをパラメーターとして指定して、受信処理の第 2 段階を完了
します。**deleteMessage** メソッドによって、
メッセージが読み取り中としてマークされ、サブスクリプションから削除されます。

次の例では、**receiveSubscriptionMessage** を使用したメッセージの
受信および処理の方法を示しています。この例では、最初に 
"LowMessages" サブスクリプションからメッセージを受信して削除し、次に true に
設定された **isPeekLock** を使用して "HighMessages" 
サブスクリプションからメッセージを受信します。次に、
**deleteMessage** を使用してメッセージを削除します。

    serviceBusService.receiveSubscriptionMessage('MyTopic', 'LowMessages', function(error, receivedMessage){
        if(!error){
            // Message received and deleted
            console.log(receivedMessage);
        }
    });
    serviceBusService.receiveSubscriptionMessage('MyTopic', 'HighMessages', { isPeekLock: true }, function(error, lockedMessage){
        if(!error){
            // Message received and locked
            console.log(lockedMessage);
            serviceBusService.deleteMessage(lockedMessage, function (deleteError){
                if(!deleteError){
                    // Message deleted
                    console.log('message has been deleted.');
                }
            }
        }
    });

##<a name="handle-crashes"></a> アプリケーションのクラッシュと読み取り不能のメッセージを処理する方法

サービス バス には、アプリケーションにエラーが発生した場合や、メッセージの
処理に問題がある場合に復旧を支援する機能が備わっています。受信側の
アプリケーションが何らかの理由によってメッセージを処理できない場合には、
**ServiceBusService** オブジェクトの **unlockMessage** メソッドを
呼び出すことができます。このメソッドが呼び出されると、サブスクリプション内の
メッセージのロックが解除され、メッセージが再度受信できる状態に変わります。
このメッセージは、同じコンシューマー側アプリケーションまたは他のコンシューマー側
アプリケーションで受信されます。

サブスクリプション内でロックされているメッセージにはタイムアウトも設定されています。
アプリケーションがクラッシュした場合など、ロックがタイムアウトになる前に
アプリケーションがメッセージの処理に失敗した場合には、メッセージのロックが自動的に
解除され、再度受信できる状態に変わります。

メッセージが処理された後、**deleteMessage** メソッドが呼び出される前に
アプリケーションがクラッシュした場合は、アプリケーションが再起動する際に
メッセージが再配信されます。一般的に、この動作は 
**"1 回以上の処理"** と呼ばれます。つまり、すべてのメッセージが 1 回以上
処理されますが、特定の状況では、同じメッセージが再配信される
可能性があります。重複処理が許されないシナリオの場合、重複メッセージの配信を
扱うロジックをアプリケーションに追加する必要があります。通常、この問題はメッセージの 
**MessageId** プロパティを使用して対処します。このプロパティは配信が試行された
後も同じ値を保持します。

##<a name="delete"></a> トピックとサブスクリプションを削除する方法

トピックおよびサブスクリプションは永続的であり、Azure 管理ポータルまたは
プログラムによって明示的に削除する必要があります。
次の例では、"MyTopic" という名前のトピックを削除する方法を示しています。

    serviceBusService.deleteTopic('MyTopic', function (error) {
        if (error) {
            console.log(error);
        }
    });

トピックを削除すると、そのトピックに登録されたサブスクリプションも
すべて削除されます。サブスクリプションは、個別に削除することもできます。次の
コードでは、"HighMessages" という名前のサブスクリプションを 
"MyTopic" トピックから削除する方法を示しています。

    serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
        if(error) {
            console.log(error);
        }
    });

##<a name="next-steps"></a> 次のステップ

これで、サービス バス トピックの基本を学習できました。さらに詳細な情報が
必要な場合は、次のリンク先を参照してください。

-   MSDN リファレンス: [キュー、トピック、およびサブスクリプション][]。
-   [SqlFilter][] の API のリファレンス。
-   GitHub の [Azure SDK for Node] リポジトリ。

  [Azure SDK for Node]: https://github.com/WindowsAzure/azure-sdk-for-node
  [次のステップ]: #nextsteps
  [サービス バス トピックとサブスクリプションとは]: #what-are-service-bus-topics
  [サービス名前空間の作成]: #create-a-service-namespace
  [名前空間の既定の管理資格情報の取得]: #obtain-default-credentials
  [Node.js アプリケーションの作成]: #Create_a_Nodejs_Application
  [サービス バスを使用するようにアプリケーションを構成する]: #Configure_Your_Application_to_Use_Service_Bus
  [How to: トピックを作成する]: #How_to_Create_a_Topic
  [How to: サブスクリプションを作成する]: #How_to_Create_Subscriptions
  [How to: メッセージをトピックに送信する]: #How_to_Send_Messages_to_a_Topic
  [How to: サブスクリプションからメッセージを受信する]: #How_to_Receive_Messages_from_a_Subscription
  [How to: アプリケーションのクラッシュと読み取り不能のメッセージを処理する]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
  [How to: トピックとサブスクリプションを削除する]: #How_to_Delete_Topics_and_Subscriptions
  [1]: #Next_Steps
  [Azure 管理ポータル]: http://manage.windowsazure.com
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/ja-jp/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [キュー、トピック、およびサブスクリプション]: http://msdn.microsoft.com/ja-jp/library/hh367516.aspx
  [SqlFilter]: http://msdn.microsoft.com/ja-jp/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
  [WebMatrix による Web サイトの作成とデプロイ]: /ja-jp/develop/nodejs/tutorials/web-site-with-webmatrix/
  [Node.js クラウド サービスへのデプロイ]: /ja-jp/documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Node.js アプリケーションの作成と Azure Web サイトへのデプロイ]: /ja-jp/develop/nodejs/tutorials/create-a-website-(mac)/
  [ストレージを使用する Node.js クラウド サービスに関するトピック]: /ja-jp/develop/nodejs/tutorials/web-app-with-storage/
  [ストレージを使用する Node.js Web アプリケーションに関するトピック]: /ja-jp/develop/nodejs/tutorials/web-site-with-storage/

