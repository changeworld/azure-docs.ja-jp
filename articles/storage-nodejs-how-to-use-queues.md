<properties linkid="dev-nodejs-how-to-service-bus-queues" urlDisplayName="キュー サービス" pageTitle="キュー サービスを使用する方法 (Node.js) | Microsoft Azure" metaKeywords="Azure キュー サービス メッセージ取得 Node.js" description="Azure キュー サービスを使用して、キューを作成および削除する方法、メッセージを挿入、取得、削除する方法について説明します。サンプルは Node.js で記述されています。" metaCanonical="" services="storage" documentationCenter="Node.js" title="Node.js からキュー サービスを使用する方法" authors="" solutions="" manager="" editor="" />





# Node.js からキュー サービスを使用する方法

このガイドでは、Azure キュー サービスを使用して一般的なシナリオを
実行する方法について説明します。サンプルは Node.js API を使用して
記述されています。紹介するシナリオは、キュー メッセージの**挿入**、**ピーク**、
**取得**、および**削除**と、**キューの作成および削除**の各シナリオに
ついて説明します。キューの詳細については、「[次のステップ][]」を参照してください。

## 目次

* [キュー サービスとは][]
* [概念][]
* [Azure のストレージ アカウントの作成][]
* [Node.js アプリケーションの作成][]
* [アプリケーションのストレージへのアクセスの構成][]
* [Azure のストレージ接続文字列の設定][]
* [方法: キューを作成する][]
* [方法: メッセージをキューに挿入する][]
* [方法: 次のメッセージをピークする][]
* [方法: 次のメッセージをデキューする][]
* [方法: キューに配置されたメッセージの内容を変更する][]
* [方法: メッセージをデキューするための追加オプション][]
* [方法: キューの長さを取得する][]
* [方法: キューを削除する][]
* [次のステップ][]

## <a name="what-is"> </a>キュー サービスとは

Azure キュー サービスは、大量のメッセージを格納するためのサービス
です。これらのメッセージは、HTTP または HTTPS を使用して認証
された呼び出しを介して世界中のどこからでもアクセスできます。キューの 1 つのメッセージの最大サイズは
64 KB で、1 つのキューには、ストレージ アカウントの合計容量の上限である
100 TB に達するまで、数百万のメッセージを格納できます。キュー サービスの一般的な
使用法を次に示します。

-   <span>非同期に処理する作業のバックログを作成する</span>
-   Azure Web ロールから worker ロールにメッセージを渡す

## <a name="concepts"> </a>概念

キュー サービスには、次のコンポーネントが含まれます。

![Queue1][Queue1]

-   **URL 形式:** キューは、次の URL 形式を使用してアドレス指定
    できます。   
    
		http://storageaccount.queue.core.windows.net/queue  
      
    次の URL を使用すると、図のいずれかのキューをアドレス指定できます。  
    
		http://myaccount.queue.core.windows.net/imagesToDownload

-   **ストレージ アカウント:** Azure のストレージにアクセス
    する場合には必ず、ストレージ アカウントを使用します。ストレージ アカウントは、アクセスするキューの
    名前空間の中でも最高レベルに位置するものです。ストレージ アカウント内の BLOB、テーブル、
    およびキューの内容の合計サイズが 100 TB を超えることはできません。

-   **キュー**: キューは、メッセージのセットを格納します。すべてのメッセージはキューに
    格納されている必要があります。

-   **メッセージ:** 形式を問わず、メッセージのサイズは最大で 64 KB です。

## <a name="create-account"> </a>Azure のストレージ アカウントの作成

ストレージ操作を行うには、Azure のストレージ アカウントが必要です。ストレージ アカウントを
作成するには、次の手順に従います。([REST API を使用して][]
ストレージ アカウントを作成することもできます)。

1. [Azure 管理ポータル]にログインします。

2. ナビゲーション ウィンドウの下部にある **[新規]** をクリックします。

	![[+ 新規]][plus-new]

3. **[ストレージ アカウント]**、**[簡易作成]** の順にクリックします。

	![[簡易作成] ダイアログ][quick-create-storage]

4. [URL] で、ストレージ アカウントの URI で使用する
    サブドメイン名を入力します。文字数は 3 ～ 24 文字で、アルファベット小文字と数字を使用できます。この名前は、対応するサブスクリプションの BLOB リソース、キュー リソース、またはテーブル リソースのアドレス指定に使用される URL のホスト名になります。

5. ストレージの配置先となるリージョンまたは
    アフィニティ グループを選択します。Azure アプリケーションから
    ストレージを使用する場合は、アプリケーションを展開する
    リージョンと同じリージョンを選択します。

6. **[ストレージ アカウントの作成]** をクリックします。

## <a name="create-app"> </a>Node.js アプリケーションの作成

空の Node.js アプリケーションを作成します。Node.js アプリケーションを作成する手順については、[Node.js アプリケーションの作成と Azure Web サイトへのデプロイ]、[Node.js クラウド サービスへのデプロイ] (Windows PowerShell を使用)、または [WebMatrix による Web サイトの作成とデプロイ]に関するページを参照してください。

## <a name="configure-access"> </a>アプリケーションのストレージへのアクセスの構成

Azure ストレージを使用するには、Node.js azure パッケージをダウンロード
して使用する必要があります。このパッケージには、ストレージ REST サービス
と通信するための便利なライブラリのセットが含まれています。

### ノード パッケージ マネージャー (NPM) を使用してパッケージを取得する

1.  **PowerShell** (Windows)、**Terminal** (Mac)、**Bash** (Unix) などのコマンド ライン インターフェイスを使用して、サンプル アプリケーションを作成したフォルダーに移動します。

2.  コマンド ウィンドウに「**npm install azure**」と入力すると、
    次のような出力が生成されます。

        azure@0.7.5 node_modules\azure
		|-- dateformat@1.0.2-1.2.3
		|-- xmlbuilder@0.4.2
		|-- node-uuid@1.2.0
		|-- mime@1.2.9
		|-- underscore@1.4.4
		|-- validator@1.1.1
		|-- tunnel@0.0.2
		|-- wns@0.5.3
		|-- xml2js@0.2.7 (sax@0.5.2)
		|-- request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)

3. 手動で **ls** コマンドを実行して、**node\_modules**
    フォルダーが作成されたことを確認することもできます。このフォルダーに
    **azure** パッケージがあります。このパッケージには、ストレージに
    アクセスするために必要なライブラリが含まれています。

### パッケージをインポートする

メモ帳などのテキスト エディターを使用して、ストレージを使用するアプリケーションの
**server.js** ファイルの先頭に次の内容を追加します。

    var azure = require('azure');

## <a name="setup-connection-string"> </a>Azure のストレージ接続文字列の設定

azure モジュールは、Azure のストレージ アカウントに接続するために必要な情報として、環境変数 AZURE\_STORAGE\_ACCOUNT および AZURE\_STORAGE\_ACCESS\_KEY を読み取ります。これらの環境変数が設定されていない場合、**createQueueService** を呼び出すときにアカウント情報を指定する必要があります。

Azure クラウド サービスの構成ファイルで環境変数を設定する例については、[ストレージを使用する Node.js クラウド サービスに関するトピック]を参照してください。

Azure Web サイトの管理ポータルで環境変数を設定する例については、[ストレージを使用する Node.js Web アプリケーションに関するトピック]を参照してください。

## <a name="create-queue"> </a>方法: キューを作成する

次のコードは、**QueueService** オブジェクトを作成し、これによってキュー
を操作できるようにします。

    var queueService = azure.createQueueService();

**createQueueIfNotExists** メソッドを使用します。このメソッドは、
指定されたキューが存在する場合は、そのキューを返します。指定されたキューが
存在しない場合は、指定された名前で新しいキューを作成します。

	queueService.createQueueIfNotExists(queueName, function(error){
    	if(!error){
        	// Queue exists
	    }
	});

###フィルター

オプションのフィルター操作は、**QueueService** を使用して行われる操作に適用できます。フィルター操作には、ログ、自動的な再試行などが含まれる場合があります。フィルターは、次のようなシグネチャを実装するオブジェクトです。

		関数ハンドル (requestOptions, next)

要求オプションに対するプリプロセスを行った後で、このメソッドは "next" を呼び出して、次のシグネチャのコールバックを渡す必要があります。

		関数 (returnObject, finalCallback, next)

このコールバックで、returnObject (サーバーへの要求からの応答) の処理の後に、コールバックは next を呼び出すか (他のフィルターの処理を続けるために next が存在する場合)、単に finalCallback を呼び出す必要があります (サービス呼び出しを終了する場合)。

再試行のロジックを実装する 2 つのフィルター (**ExponentialRetryPolicyFilter** と **LinearRetryPolicyFilter**) が、Azure SDK for Node.js に含まれています。次のコードは、**ExponentialRetryPolicyFilter** を使う **QueueService** オブジェクトを作成します。

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var queueService = azure.createQueueService().withFilter(retryOperations);

## <a name="insert-message"> </a>方法: メッセージをキューに挿入する

キューにメッセージを挿入するには、**createMessage** メソッドを使用し、
新しいメッセージを作成してキューに追加します。

	queueService.createMessage(queueName, "Hello world!", function(error){
	    if(!error){
	        // Message inserted
	    }
	});


## <a name="peek-message"> </a>方法: 次のメッセージをピークする

**PeekMessage** メソッドを呼び出すと、キューの先頭にあるメッセージを
キューから削除せずにピークできます。既定では、
**peekMessages** は 1 つのメッセージを対象としてピークします。

	queueService.peekMessages(queueName, function(error, messages){
		if(!error){
			// Messages peeked
			// Text is available in messages[0].messagetext
		}
	});


> [WACOM.NOTE] 
キューにメッセージがないときに <strong>peekMessage</strong> を使用した場合、エラーは返されませんが、メッセージも返されません。

## <a name="get-message"> </a>方法: 次のメッセージをデキューする

コードでは、2 つの手順でキューからメッセージを削除します。既定では、
**getMessages** を呼び出すと、キュー内の次のメッセージを取得します。**GetMessage** から
返されたメッセージは、このキューからメッセージを読み取る他のコードから
参照できなくなります。既定では、このメッセージを参照
できない状態は 30 秒間続きます。また、キューからのメッセージの削除を完了するには、
**DeleteMessage** を呼び出す必要があります。2 段階の手順でメッセージを削除する
この方法では、ハードウェアまたはソフトウェアの問題が原因でコードによる
メッセージの処理が失敗した場合に、コードの別のインスタンスで同じ
メッセージを取得し、もう一度処理することができます。コードでは、メッセージが処理された直後に
**DeleteMessage** を呼び出します。

	queueService.getMessages(queueName, function(error, messages){
	    if(!error){
	        // Process the message in less than 30 seconds, the message
	        // text is available in messages[0].messagetext 
			var message = messages[0]
	        queueService.deleteMessage(queueName
				, message.messageid
				, message.popreceipt
				, function(error){
	            	if(!error){
	                	// Message deleted
	            	}
	        	});
	    }
	});

> [WACOM.NOTE]
> キューにメッセージがないときに <b>peekMessage</b> を使用した場合、エラーは返されませんが、メッセージも返されません。

## <a name="change-contents"> </a>方法: キューに配置されたメッセージの内容を変更する

キュー内のメッセージの内容をインプレースで変更できます。メッセージが
作業タスクを表している場合は、この機能を使用して、作業タスクの状態を
更新できます。次のコードでは、**updateMessage** メソッド
を使用してメッセージを更新します。

    queueService.getMessages(queueName, function(error, messages){
		if(!error){
			// Got the message
			var message = messages[0];
			queueService.updateMessage(queueName
				, message.messageid
				, message.popreceipt
				, 10
				, { messagetext: 'in your message, doing stuff.' }
				, function(error){
					if(!error){
						// Message updated successfully
					}
				});
		}
	});

## <a name="advanced-get"> </a>方法: メッセージをデキューするための追加オプション

キューからのメッセージの取得をカスタマイズする方法は 2 つあります。
1 つ目の方法では、(最大 32 個の) メッセージのバッチを取得できます。2 つ目の方法では、
コードで各メッセージを完全に処理できるように、非表示タイムアウトの設定を
長くまたは短くすることができます。次のコード例では、
**GetMessages** メソッドを使用して、1 回の呼び出しで 15 個のメッセージを取得します。その後、for ループを
使用して、各メッセージを処理します。また、各メッセージの非表示
タイムアウトを 5 分に設定します。

    queueService.getMessages(queueName
		, {numofmessages: 15, visibilitytimeout: 5 * 60}
		, function(error, messages){
		if(!error){
			// Messages retreived
			for(var index in messages){
				// text is available in messages[index].messagetext
				var message = messages[index];
				queueService.deleteMessage(queueName
					, message.messageid
					, message.popreceipt
					, function(error){
						if(!error){
							// Message deleted
						}
					});
			}
		}
	});

## <a name="get-queue-length"> </a>方法: キューの長さを取得する

キュー内のメッセージの概数を取得できます。メソッド 
**getQueueMetadata** では、キューのメタデータを返すように
キュー サービスに要求します。また、応答の **approximatemessagecount**
プロパティには、キュー内のメッセージの数が格納されます。キュー サービスが
要求に応答した後にメッセージが追加または削除される可能性があるため、
これらの値は概数にすぎません。

    queueService.getQueueMetadata(queueName, function(error, queueInfo){
		if(!error){
			// Queue length is available in queueInfo.approximatemessagecount
		}
	});

## <a name="delete-queue"> </a>方法: キューを削除する

キューおよびキューに含まれているすべてのメッセージを削除するには、
**deleteQueue** メソッドを呼び出します。

    queueService.deleteQueue(queueName, function(error){
		if(!error){
			// Queue has been deleted
		}
	});

## <a name="next-steps"> </a>次のステップ

これで、キュー ストレージの基本を学習できました。さらに複雑な
ストレージ タスクを実行する方法については、次のリンク先を参照してください。

-   MSDN リファレンス: [Azure のデータの格納とアクセス][]
-   [Azure のストレージ チーム ブログ][]
-   GitHub の [Azure SDK for Node] リポジトリ

  [Azure ノードの追加]: https://github.com/WindowsAzure/azure-sdk-for-node
  [次のステップ]: #next-steps
  [キュー サービスとは]: #what-is
  [概念]: #concepts
  [Azure のストレージ アカウントの作成]: #create-account
  [Node.js アプリケーションの作成]: #create-app
  [アプリケーションのストレージへのアクセスの構成]: #configure-access
  [Azure のストレージ接続文字列の設定]: #setup-connection-string
  [方法: キューを作成する]: #create-queue
  [方法: メッセージをキューに挿入する]: #insert-message
  [方法: 次のメッセージをピークする]: #peek-message
  [方法: 次のメッセージをデキューする]: #get-message
  [方法: キューに配置されたメッセージの内容を変更する]: #change-contents
  [方法: メッセージをデキューするための追加オプション]: #advanced-get
  [方法: キューの長さを取得する]: #get-queue-length
  [方法: キューを削除する]: #delete-queue
  [REST API を使用して]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh264518.aspx
  [Azure 管理ポータル]: http://manage.windowsazure.com
  [Node.js アプリケーションの作成と Azure Web サイトへのデプロイ]: /ja-jp/develop/nodejs/tutorials/create-a-website-(mac)/
  [ストレージを使用する Node.js クラウド サービスに関するトピック]: /ja-jp/develop/nodejs/tutorials/web-app-with-storage/
  [ストレージを使用する Node.js Web アプリケーションに関するトピック]: /ja-jp/develop/nodejs/tutorials/web-site-with-storage/
  
  [Queue1]: ./media/storage-nodejs-how-to-use-queues/queue1.png
  [plus-new]: ./media/storage-nodejs-how-to-use-queues/plus-new.png
  [quick-create-storage]: ./media/storage-nodejs-how-to-use-queues/quick-storage.png
  
  
  
  [Node.js クラウド サービスへのデプロイ]: {localLink:2221} "Express を使用した Web アプリケーション"
  [Azure のデータの格納とアクセス]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg433040.aspx
  [Azure のストレージ チーム ブログ]: http://blogs.msdn.com/b/windowsazurestorage/
  [WebMatrix による Web サイトの作成とデプロイ]: /ja-jp/develop/nodejs/tutorials/web-site-with-webmatrix/

