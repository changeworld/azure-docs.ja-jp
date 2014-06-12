<properties linkid="dev-nodejs-how-to-table-services" urlDisplayName="テーブル サービス" pageTitle="テーブル ストレージの使用方法 (Node.js) | Microsoft Azure" metaKeywords="Azure テーブル ストレージ サービス, Azure テーブル サービス Node.js, テーブル ストレージ Node.js" description="Azure でのテーブル ストレージ サービスの使用方法について説明します。コード サンプルは Node.js API を使用して記述されています。" metaCanonical="" services="storage" documentationCenter="Node.js" title="Node.js からテーブル サービスを使用する方法" authors="" solutions="" manager="" editor="" />





# Node.js からテーブル サービスを使用する方法

このガイドでは、Microsoft Azure テーブル サービスを使用して一般的なシナリオを
実行する方法について説明します。サンプルは Node.js API を使用して
記述されています。紹介するシナリオは、**テーブルの作成と削除、
テーブルのエンティティの挿入とクエリ実行**などです。テーブルの
詳細については、「[次のステップ][]」のセクションを参照してください。

## 目次

* [テーブル サービスとは][]
* [概念][]
* [Azure のストレージ アカウントの作成][]
* [Node.js アプリケーションの作成][]
* [アプリケーションからストレージへのアクセスの構成][]
* [Azure のストレージ接続文字列の設定][]
* [方法: テーブルを作成する][]
* [方法: エンティティをテーブルに追加する][]
* [方法: エンティティを更新する][]
* [方法: エンティティのグループを操作する][]
* [方法: エンティティを照会する][]
* [方法: エンティティのセットを照会する][]
* [方法: エンティティ プロパティのサブセットを照会する][]
* [方法: エンティティを削除する][]
* [方法: テーブルを削除する][]
* [次のステップ][]

## <a name="what-is"> </a>テーブル サービスとは

Azure テーブル サービスは、大量の構造化データを
格納します。このサービスは、Azure クラウドの内部および外部からの
認証された呼び出しを受け付けます。Azure テーブルは、構造化された
非リレーショナル データを格納するのに最適です。テーブル サービスの一般的な使用法を
次に示します。

-   スループットの需要に合わせて自動的に拡張される大量の (数 TB の)
    構造化データを格納する
-   複雑な結合、外部キー、またはストアド プロシージャを必要とせず、
    高速アクセスのために非正規化できるデータセットを格納する
-   クラスター化インデックスを使用してデータ (ユーザー プロファイルなど) をすばやく照会する

テーブル サービスを使用して、構造化された非リレーショナル データの
膨大なセットを格納してクエリを実行することができ、テーブルはデータ量が
増加すると拡張されます。

## <a name="concepts"> </a>概念

テーブル サービスには、次のコンポーネントが含まれます。

![Table1][Table1]

-   **URL 形式**: 次のアドレス形式を使用してアカウントのテーブルの
    アドレスを記述します。
   
	    http://storageaccount.table.core.windows.net/table  
      
    このアドレスを OData プロトコルで使用して、Azure テーブルを直接アドレス指定できます。詳細については、[OData.org の Web サイト][]を参照してください。

-   **ストレージ アカウント:** Azure のストレージにアクセス
    する場合には必ず、ストレージ アカウントを使用します。ストレージ アカウント内の BLOB、テーブル、
    およびキューの内容の合計サイズが 100 TB を超えることはできません。

-   **テーブル**: 無制限のエンティティのコレクションです。テーブルでは
    エンティティにスキーマを設定しないため、1 つのテーブルに異なるプロパティの
    セットを持つエンティティが含まれている場合があります。1 つのアカウントには、
    複数のテーブルを格納できます。

-   **エンティティ**: エンティティは、プロパティのセットで、データベース
    の行に似ています。エンティティの最大サイズは 1 MB です。

-   **プロパティ**: プロパティは、名前と値のペアです。それぞれのエンティティは、データを格納するために最大で 252 個のプロパティを含むことができます。さらに、それぞれのエンティティは、
    パーティション キー、行キー、およびタイムスタンプを指定する、
    3 つのシステム プロパティを持ちます。同じパーティション キーを持つエンティティは、
    アトミック操作でより迅速な照会と挿入/更新が可能です。エンティティの
    行キーは、パーティション内の一意の識別子です。

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
       フォルダーが作成されたことを確認することもできます。このフォルダーに
    **azure** パッケージがあります。このパッケージには、ストレージに
    アクセスするために必要なライブラリが含まれています。

### パッケージをインポートする

メモ帳などのテキスト エディターを使用して、ストレージを使用するアプリケーションの
**server.js** ファイルの先頭に次の内容を追加します。

    var azure = require('azure');

## <a name="setup-connection-string"> </a>Azure のストレージ接続文字列の設定

azure モジュールは、Azure のストレージ アカウントに接続するために必要な情報として、環境変数 AZURE\_STORAGE\_ACCOUNT および AZURE\_STORAGE\_ACCESS\_KEY を読み取ります。これらの環境変数が設定されていない場合、**TableService** を呼び出すときにアカウント情報を指定する必要があります。

Azure クラウド サービスの構成ファイルで環境変数を設定する例については、[ストレージを使用する Node.js クラウド サービスに関するトピック]を参照してください。

Azure Web サイトの管理ポータルで環境変数を設定する例については、[ストレージを使用する Node.js Web アプリケーションに関するトピック]を参照してください。

## <a name="create-table"> </a>方法: テーブルを作成する

次のコードは、**TableService** オブジェクトを作成し、これを使用して
新しいテーブルを作成します。**server.js** ファイルの先頭付近に次の内容を追加します。

    var tableService = azure.createTableService();

**createTableIfNotExists** の呼び出しは、指定されたテーブルが存在
する場合、そのテーブルを返します。指定されたキューが存在しない場合
は、指定された名前で新しいテーブルを作成します。次の例では、"mytable" という名前のテーブルが存在しない場合、このテーブルを作成します。

    tableService.createTableIfNotExists('mytable', function(error){
		if(!error){
			// Table exists or created
		}
	});

###フィルター

オプションのフィルター操作は、**TableService** を使用して行われる操作に適用できます。フィルター操作には、ログ、自動的な再試行などが含まれる場合があります。フィルターは、次のようなシグネチャを実装するオブジェクトです。

		function handle (requestOptions, next)

要求オプションに対するプリプロセスを行った後で、このメソッドは "next" を呼び出して、次のシグネチャのコールバックを渡す必要があります。

		function (returnObject, finalCallback, next)

このコールバックで、returnObject (サーバーへの要求からの応答) の処理の後に、コールバックは next を呼び出すか (他のフィルターの処理を続けるために next が存在する場合)、単に finalCallback を呼び出す必要があります (サービス呼び出しを終了する場合)。

再試行のロジックを実装する 2 つのフィルター (**ExponentialRetryPolicyFilter** と **LinearRetryPolicyFilter**) が、Azure SDK for Node.js に含まれています。次のコードは、**ExponentialRetryPolicyFilter** を使用する **TableService** オブジェクトを作成します。

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var tableService = azure.createTableService().withFilter(retryOperations);

## <a name="add-entity"> </a>方法: エンティティをテーブルに追加する

エンティティを追加するには、最初に、エンティティのプロパティと
そのデータ型を定義するオブジェクトを作成します。すべてのエンティティについて、**PartitionKey** と 
**RowKey** を指定する必要があることに注意してください。これらはエンティティの
一意の識別子であり、他のエンティティのプロパティよりはるかに高速に
照会できる値です。システムでは **PartitionKey** が使用
されて多くのストレージ ノードにテーブルのエンティティが自動的に配布されます。
**PartitionKey** が同じエンティティは同じノードに格納されます。
**RowKey** は、エンティティが属するパーティション内のエンティティ
の一意の ID です。エンティティをテーブルに追加するには、エンティティ 
オブジェクトを **insertEntity** メソッドに渡します。

    var task = {
		PartitionKey : 'hometasks'
		, RowKey : '1'
		, Description : 'Take out the trash'
		, DueDate: new Date(2012, 6, 20)
	};
	tableService.insertEntity('mytable', task, function(error){
		if(!error){
			// Entity inserted
		}
	});

## <a name="update-entity"> </a>方法: エンティティを更新する

既存のエンティティを更新するには、複数のメソッドがあります。

* **updateEntity** - 既存のエンティティを、置換することで更新します。

* **mergeEntity** - 既存のエンティティを、新しいプロパティ値を既存のエンティティにマージすることで更新します。

* **insertOrReplaceEntity** - 既存のエンティティを、置換することで更新します。エンティティが存在しない場合は、新しいエンティティが挿入されます。

* **insertOrMergeEntity** - 既存のエンティティを、新しいプロパティ値を既存のエンティティにマージすることで更新します。エンティティが存在しない場合は、新しいエンティティが挿入されます。

次の例に、**updateEntity** を使用してエンティティを更新する方法を示します。

	var task = {
		PartitionKey : 'hometasks'
		, RowKey : '1'
		, Description : 'Wash Dishes'
	}
	tableService.updateEntity('mytable', task, function(error){
		if(!error){
			// Entity has been updated
		}
	});
    
**updateEntity** と **mergeEntity** では、更新されるエンティティが存在しない場合、更新操作は失敗します。したがって、既に存在しているかどうかに関係なくエンティティを格納するには、代わりに **insertOrReplaceEntity** または **insertOrMergeEntity** を使用する必要があります。

## <a name="change-entities"> </a>エンティティのグループを操作する方法

状況によって、複数の操作をバッチとして送信し、サーバーによる
アトミック処理を行うことが合理的である場合があります。これを実現するには、
**TableService** の **beginBatch** メソッドを使用した後、一連の操作を
通常どおり呼び出します。異なるのは、これらの演算子のコールバック関数が、
操作がサーバーに送信されたのではなく、バッチに追加されたことを
示す点です。バッチを送信するときは、
**commitBatch** を呼び出します。このメソッドに対して提供されるコールバックは、
バッチ全体が正常に送信されたかどうかを示します。次の例に、2 つのエンティティをバッチで送信する方法を示します。

    var tasks=[
		{
			PartitionKey : 'hometasks'
			, RowKey : '1'
			, Description : 'Take out the trash.'
			, DueDate : new Date(2012, 6, 20)
		}
		, {
			PartitionKey : 'hometasks'
			, RowKey : '2'
			, Description : 'Wash the dishes.'
			, DueDate : new Date(2012, 6, 20)
		}
	]
	tableService.beginBatch();
	var async=require('async');

	async.forEach(tasks
		, function taskIterator(task, callback){
			tableService.insertEntity('mytable', task, function(error){
				if(!error){
					// Entity inserted
					callback(null);
				} else {
					callback(error);
				}
			});
		}
		, function(error){
			if(!error){
				// All inserts completed
				tableService.commitBatch(function(error){
					if(!error){
						// Batch successfully commited
					}
				});
			}
		});

<div class="dev-callout">
<strong>注</strong>
<p>前の例では、"async" モジュールを使用して、**commitBatch** を呼び出す前に、エンティティがすべて正常に送信されるようにしています。</p>
</div>

## <a name="query-for-entity"> </a>エンティティを照会する方法

テーブル内のエンティティを照会するには、**queryEntity** メソッドを
使用して、**PartitionKey** と **RowKey** を渡します。

    tableService.queryEntity('mytable'
		, 'hometasks'
		, '1'
		, function(error, entity){
			if(!error){
				// entity contains the returned entity
			}
		});

## <a name="query-set-entities"> </a>方法: エンティティのセットを照会する

テーブルを照会するには、**TableQuery** オブジェクトを使用し、
**select**、**from**、**where** (**wherePartitionKey**、
**whereRowKey**、**whereNextPartitionKey**、
**whereNextRowKey** などの便利な句を含む)、**and**、**or**、**top** などの
句を使用してクエリ式を作成します。次に、このクエリ式を 
**queryEntities** メソッドに渡します。この結果を、コールバック内の 
**for** ループで使用できます。

次の例では、**PartitionKey** に基づいて、Seattle 内のすべてのタスクを検索します。

    var query = azure.TableQuery
		.select()
		.from('mytable')
		.where('PartitionKey eq ?', 'hometasks');
	tableService.queryEntities(query, function(error, entities){
		if(!error){
			//entities contains an array of entities
		}
	});

## <a name="query-entity-properties"> </a>方法: エンティティ プロパティのサブセットを照会する

テーブルに対するクエリでは、ごくわずかのプロパティだけをエンティティから取得できます。
プロジェクション**と呼ばれるこの方法では、帯域幅の使用が削減され、クエリの
パフォーマンスが向上します。特に、大量のエンティティがある場合に役立ちます。**select** 句を使用して、
クライアントに渡すプロパティの名前を
指定します。

次のコードのクエリでは、テーブル内のエンティティの **Descriptions** 
のみを返します。プログラムの出力では、**DueDate** はサーバーによって
送信されなかったため、**undefined** と表示されています。

<div class="dev-callout">
<strong>注</strong>
<p>次のスニペットはクラウド ストレージ サービスに対してのみ機能します。
<b>select</b> キーワードはストレージ エミュレーターではサポートされて
いません。</p>
</div>

    var query = azure.TableQuery
		.select('Description')
		.from('mytable')
		.where('PartitionKey eq ?', 'hometasks');
	tableService.queryEntities(query, function(error, entities){
		if(!error){
			//entities contains an array of entities
		}
	});

## <a name="delete-entity"> </a>方法: エンティティを削除する

パーティション キーと行キーを使用してエンティティを削除できます。次の例
では、**task1** オブジェクトに、削除するエンティティの 
**RowKey** と **PartitionKey** の値が格納されます。次に、
このオブジェクトが **deleteEntity** メソッドに渡されます。

    tableService.deleteEntity('mytable'
		, {
			PartitionKey : 'hometasks'
			, RowKey : '1'
		}
		, function(error){
			if(!error){
				// Entity deleted
			}
		});

## <a name="delete-table"> </a>方法: テーブルを削除する

次のコードは、ストレージ アカウントからテーブルを削除します。

    tableService.deleteTable('mytable', function(error){
		if(!error){
			// Table deleted
		}
	});

## <a name="next-steps"> </a>次のステップ

これで、テーブル ストレージの基本を学習できました。さらに複雑なストレージ タスクを実行する方法については、次のリンク先を参照してください。

-   MSDN リファレンス: [Azure のデータの格納とアクセス][]
-   [Azure のストレージ チーム ブログ][]
-   GitHub の [Azure SDK for Node] リポジトリ

  [Azure SDK for Node]: https://github.com/WindowsAzure/azure-sdk-for-node
  [次のステップ]: #next-steps
  [テーブル サービスとは]: #what-is
  [概念]: #concepts
  [Azure のストレージ アカウントの作成]: #create-account
  [Node.js アプリケーションの作成]: #create-app
  [アプリケーションからストレージへのアクセスの構成]: #configure-access
  [Azure のストレージ接続文字列の設定]: #setup-connection-string
  [方法: テーブルを作成する]: #create-table
  [方法: エンティティをテーブルに追加する]: #add-entity
  [方法: エンティティを更新する]: #update-entity
  [方法: エンティティのグループを操作する]: #change-entities
  [方法: エンティティを照会する]: #query-for-entity
  [方法: エンティティのセットを照会する]: #query-set-entities
  [方法: エンティティ プロパティのサブセットを照会する]: #query-entity-properties
  [方法: エンティティを削除する]: #delete-entity
  [方法: テーブルを削除する]: #delete-table
  [Table1]: ./media/storage-nodejs-how-to-use-table-storage/table1.png
  [OData.org の Web サイト]: http://www.odata.org/
  [REST API を使用して]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh264518.aspx
  [Azure 管理ポータル]: http://manage.windowsazure.com

  [plus-new]: ./media/storage-nodejs-how-to-use-table-storage/plus-new.png
  [quick-create-storage]: ./media/storage-nodejs-how-to-use-table-storage/quick-storage.png
  
  
  
  [Node.js クラウド サービスへのデプロイ]: {localLink:2221} "Express を使用した Web アプリケーション"
  [Azure のデータの格納とアクセス]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg433040.aspx
  [Azure のストレージ チーム ブログ]: http://blogs.msdn.com/b/windowsazurestorage/
  [WebMatrix による Web サイトの作成とデプロイ]: /ja-jp/develop/nodejs/tutorials/web-site-with-webmatrix/
[ストレージを使用する Node.js クラウド サービスに関するトピック]: /ja-jp/develop/nodejs/tutorials/web-app-with-storage/
  [ストレージを使用する Node.js Web アプリケーションに関するトピック]: /ja-jp/develop/nodejs/tutorials/web-site-with-storage/
 [Node.js アプリケーションの作成と Azure Web サイトへのデプロイ]: /ja-jp/develop/nodejs/tutorials/create-a-website-(mac)/

