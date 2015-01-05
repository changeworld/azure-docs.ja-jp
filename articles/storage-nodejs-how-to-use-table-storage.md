<properties urlDisplayName="Table Service" pageTitle="テーブル ストレージを使用する方法 (Node.js) | Microsoft Azure" metaKeywords="Azure table storage service, Azure table service Node.js, table storage Node.js" description="Learn how to use the table storage service in Azure. Code samples are written using the Node.js API." metaCanonical="" services="storage" documentationCenter="nodejs" title="How to Use the Table Service from Node.js" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="mwasson" />

# Node.js からテーブル サービスを使用する方法

このガイドでは、Microsoft Azure テーブル サービスを使用して一般的なシナリオを実行する方法について説明します。サンプルは Node.js API を使用して記述されています。紹介するシナリオは、**テーブルの作成と削除、テーブルのエンティティの挿入とクエリ実行**などです。テーブルの詳細については、「[次のステップ][]」のセクションを参照してください。

## 目次

* [テーブル サービスとは][]   
* [概念][]   
* [Azure のストレージ アカウントの作成](#create-account)
* [Node.js アプリケーションの作成](#create-app)
* [アプリケーションからストレージへのアクセスの構成](#configure-access)
* [Azure のストレージ接続文字列の設定](#setup-connection-string)  
* [方法: テーブルを作成する](#create-table)
* [方法: エンティティをテーブルに追加する](#add-entity)
* [方法: エンティティを更新する](#update-entity)
* [方法: エンティティのグループを操作する](#change-entities)
* [方法: エンティティを取得する](#query-for-entity)
* [方法: エンティティのセットを照会する](#query-set-entities)
* [方法: エンティティを削除する](#delete-entity)
* [方法: テーブルを削除する](#delete-table)   
* [方法: 共有アクセス署名を操作する](#sas)
* [次のステップ][]

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

<h2><a name="create-account"></a>Azure のストレージ アカウントの作成</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="create-app"> </a>Node.js アプリケーションの作成

空の Node.js アプリケーションを作成します。Node.js アプリケーションを作成する手順については、[Node.js アプリケーションの作成と Azure Web サイトへのデプロイ]、[Node.js クラウド サービスへのデプロイ][Node.js Cloud Service] (Windows PowerShell を使用)、または [WebMatrix による Web サイトの作成とデプロイ]に関するページを参照してください。

## <a name="configure-access"> </a>アプリケーションからストレージへのアクセスの構成

Azure Storage を使用するには、Azure Storage SDK for Node.js が必要です。ここには、ストレージ REST サービスと通信するための
便利なライブラリのセットが含まれています。

### ノード パッケージ マネージャー (NPM) を使用してパッケージを取得する

1.  **PowerShell** (Windows)、**Terminal** (Mac)、**Bash** (UNIX) などのコマンド ライン インターフェイスを使用して、サンプル アプリケーションを作成したフォルダーに移動します。

2.  コマンド ウィンドウに「**npm install azure-storage**」と入力すると、次のような出力が生成されます。

        azure-storage@0.1.0 node_modules\azure-storage
		├── extend@1.2.1
		├── xmlbuilder@0.4.3
		├── mime@1.2.11
		├── underscore@1.4.4
		├── validator@3.1.0
		├── node-uuid@1.4.1
		├── xml2js@0.2.7 (sax@0.5.2)
		└── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)

3.  手動で **ls** コマンドを実行して、**node\_modules** フォルダーが作成されたことを確認することもできます。このフォルダーに **azure-storage** パッケージがあります。このパッケージには、ストレージにアクセスするために必要なライブラリが含まれています。

### パッケージをインポートする

メモ帳などのテキスト エディターを使用して、ストレージを使用するアプリケーションの **server.js** ファイルの先頭に次の内容を追加します。

    var azure = require('azure-storage');

## <a name="setup-connection-string"> </a>Azure のストレージ接続文字列の設定

azure モジュールは、Azure のストレージ アカウントに接続するために必要な情報として、環境変数 AZURE\_STORAGE\_ACCOUNT および AZURE\_STORAGE\_ACCESS\_KEY、または AZURE\_STORAGE\_CONNECTION\_STRING を読み取ります。これらの環境変数が設定されていない場合、**TableService** を呼び出すときにアカウント情報を指定する必要があります。

Azure Web サイトの管理ポータルで環境変数を設定する例については、「[Azure テーブル サービスを使用する Node.js Web アプリケーション]」を参照してください。

## <a name="create-table"> </a>テーブルの作成方法

次のコードは、**TableService** オブジェクトを作成し、これを使用して新しいテーブルを作成します。**server.js** ファイルの先頭付近に次の内容を追加します。

    var tableSvc = azure.createTableService();

**createTableIfNotExists** の呼び出しにより、指定した名前の新しいテーブルが (まだ存在していない場合は) 作成されます。次の例では、"mytable" という名前のテーブルが存在しない場合、このテーブルを作成します。

    tableSvc.createTableIfNotExists('mytable', function(error, result, response){
		if(!error){
			// Table exists or created
		}
	});

新しいテーブルが作成される場合、`result` は `true` になり、テーブルが既に存在する場合には `false` になります。`response` には要求に関する情報が含まれます。

###フィルター

オプションのフィルター操作は、**TableService** を使用して行われる操作に適用できます。フィルター操作には、ログ、自動的な再試行などが含まれる場合があります。フィルターは、次のようなシグネチャを実装するオブジェクトです。

		function handle (requestOptions, next)

要求オプションに対するプリプロセスを行った後で、このメソッドは "next" を呼び出して、次のシグネチャのコールバックを渡す必要があります。

		function (returnObject, finalCallback, next)

このコールバックで、returnObject (サーバーへの要求からの応答) の処理の後に、コールバックは next を呼び出すか (他のフィルターの処理を続けるために next が存在する場合)、単に finalCallback を呼び出す必要があります (サービス呼び出しを終了する場合)。

再試行のロジックを実装する 2 つのフィルター (**ExponentialRetryPolicyFilter** と **LinearRetryPolicyFilter**) が、Azure SDK for Node.js に含まれています。次のコードは、**ExponentialRetryPolicyFilter** を使用する **TableService** オブジェクトを作成します。

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var tableSvc = azure.createTableService().withFilter(retryOperations);

## <a name="add-entity"> </a>エンティティをテーブルに追加する方法

エンティティを追加するには、エンティティのプロパティを定義するオブジェクトを最初に作成します。すべてのエンティティには、エンティティの一意の識別子である **PartitionKey** と **RowKey** が含まれます。

* **PartitionKey** - エンティティが格納されるパーティションを決定します。

* **RowKey** - パーティション内のエンティティを一意に識別します。

**PartitionKey** と **RowKey** は両方とも文字列値にする必要があります。詳細については、「[テーブル サービス データ モデルについて]」を参照してください(http://msdn.microsoft.com/library/azure/dd179338.aspx)。

エンティティを定義する例を次に示します。**dueDate** が **Edm.DateTime** の型として定義されている点に注意してください。型の指定は省略可能です。型を指定しなかった場合、型は推測されます。

	var task = { 
	  PartitionKey: {'_':'hometasks'},
	  RowKey: {'_': '1'},
	  description: {'_':'take out the trash'},
	  dueDate: {'_':new Date(2015, 6, 20), '$':'Edm.DateTime'}
	};

> [WACOM.NOTE] 各レコードには、エンティティが挿入または更新される場合に Azure により設定される **Timestamp** フィールドもあります。

**entityGenerator** を使用してエンティティを作成することもできます。次の例では、**entityGenerator** を使用して同じタスク エンティティを作成しています。

	var entGen = azure.TableUtilities.entityGenerator;
    var task = {
	  PartitionKey: entGen.String('hometasks'),
      RowKey: entGen.String('1'),
      description: entGen.String('take out the trash'),
      dueDate: entGen.DateTime(new Date(Date.UTC(2015, 6, 20))),
    };

エンティティをテーブルに追加するには、エンティティ オブジェクトを 
**insertEntity** メソッドに渡します。

	tableSvc.insertEntity('mytable',task, function (error, result, response) {
		if(!error){
			// Entity inserted
		}
	});

操作が成功した場合、`result` には挿入されたレコードの [ETag](http://en.wikipedia.org/wiki/HTTP_ETag)  が含まれ、`response` には操作に関する情報が含まれます。

> [WACOM.NOTE] 既定では、**insertEntity** は、`response` 情報の一部として、挿入されたエンティティを返しません。このエンティティに対して他の操作を実行する予定がある場合、または情報をキャッシュする場合は、`result` の一部として返されるようにすると便利です。そのためには、次のように **echoContent** を有効にします。
>
> `tableSvc.insertEntity('mytable', task, {echoContent: true}, function (error, result, response) {...}`

## <a name="update-entity"> </a>エンティティを更新する方法

既存のエンティティを更新するには、複数のメソッドがあります。

* **updateEntity** - 既存のエンティティを置換することで更新します。

* **mergeEntity** - 新しいプロパティ値を既存のエンティティにマージすることで既存のエンティティを更新します。

* **insertOrReplaceEntity** - 既存のエンティティを、置換することで更新します。エンティティが存在しない場合は、新しいエンティティが挿入されます。

* **insertOrMergeEntity** - 既存のエンティティを、新しいプロパティ値を既存のエンティティにマージすることで更新します。エンティティが存在しない場合は、新しいエンティティが挿入されます。

次の例に、**updateEntity** を使用してエンティティを更新する方法を示します。

	tableSvc.updateEntity('mytable', updatedTask, function(error, result, response){
      if(!error) {
        // Entity updated
      }
    });

> [WACOM.NOTE] 既定では、エンティティを更新するときに、更新対象のデータが以前に別のプロセスにより変更されているかどうかは確認されません。同時更新をサポートするには、次の手順を実行します。
> 
> 1.更新するオブジェクトの ETag を取得します。これは、任意のエンティティに関連する操作の `response` の一部として返され、`response['.metadata'].etag` を通じて取得できます。
> 
> 2.エンティティで更新操作を実行する場合は、以前に取得した ETag 情報を新しいエンティティに追加します。次に例を示します。
> 
>     `entity2['.metadata'].etag = currentEtag;`
>    
> 3.更新操作を実行します。アプリケーションの別のインスタンスなど、ETag 値を取得した後でエンティティが更新されている場合は、要求で指定された更新の条件が満たされていないことを示す `error` が返されます。
    
**updateEntity** と **mergeEntity** では、更新されるエンティティが存在しない場合、更新操作は失敗します。したがって、既に存在しているかどうかに関係なくエンティティを格納するには、代わりに **insertOrReplaceEntity** または **insertOrMergeEntity** を使用する必要があります。

成功した更新操作の `result` には、更新されたエンティティの **Etag** が含まれます。

## <a name="change-entities"> </a>エンティティのグループを操作する方法
状況によって、複数の操作をバッチとして送信し、サーバーによるアトミック処理を行うことが合理的である場合があります。これを実現するには、**TableBatch** クラスを使用してバッチを作成し、**TableService** の **executeBatch** メソッドを使用してバッチ操作を実行します。

 The following example demonstrates submitting two entities in a batch:

    var task1 = { 
	  PartitionKey: {'_':'hometasks'},
	  RowKey: {'_': '1'},
	  description: {'_':'Take out the trash'},
	  dueDate: {'_':new Date(2015, 6, 20)}
	};
	var task2 = { 
	  PartitionKey: {'_':'hometasks'},
	  RowKey: {'_': '2'},
	  description: {'_':'Wash the dishes'},
	  dueDate: {'_':new Date(2015, 6, 20)}
	};

	var batch = new azure.TableBatch();
	
	batch.insertEntity(task1, {echoContent: true});
	batch.insertEntity(task2, {echoContent: true});

	tableSvc.executeBatch('mytable', batch, function (error, result, response) {
	  if(!error) {
	    // Batch completed
	  }
	});

バッチ操作が成功した場合、`result` にはバッチ内の各操作の情報が含まれます。

###バッチ操作の処理

バッチに追加された操作は、`operations` プロパティで確認できます。次のメソッドを使用して操作を処理できます。

* **clear** - バッチのすべての操作をクリアします。

* **getOperations** - バッチから操作を取得します。

* **hasOperations** - バッチに操作が含まれている場合は true を返します。

* **removeOperations** - 操作を削除します。

* **size** - バッチ内の操作の数を返します。

## <a name="query-for-entity"> </a>エンティティを取得する方法

**PartitionKey** および **RowKey** に基づいて特定のエンティティを返すには、**retrieveEntity** メソッドを使用します。

    tableSvc.retrieveEntity('mytable', 'hometasks', '1', function(error, result, response){
	  if(!error){
	    // result contains the entity
	  }
    });

この操作が完了すると、`result` にエンティティが含まれます。

## <a name="query-set-entities"> </a>エンティティのセットを照会する方法

テーブルを照会するには、**TableQuery** オブジェクトを使用し、
以下の句を使用してクエリ式を作成します。

* **select** - クエリから返されるフィールド。

* **where** - where 句。

	* **and** - `and` where 条件。

	* **or** - `or` where 条件。

* **top** - 取得する項目の数。


次の例では、"hometasks" の PartitionKey で最初の 5 つの項目を返すクエリを作成します。

	var query = new azure.TableQuery()
	  .top(5)
	  .where('PartitionKey eq ?', 'hometasks');

**select** が使用されていないため、すべてのフィールドが返されます。テーブルでクエリを実行するには、**queryEntities** を使用します。次の例では、このクエリを使用して "mytable" からエンティティを返します。

	tableSvc.queryEntities('mytable',query, null, function(error, result, response) {
	  if(!error) {
	    // query was successful
	  }
	});

成功した場合は、`result.entries` にはクエリに一致するエンティティの配列が含まれます。クエリですべてのエンティティを返すことができなかった場合、`result.continuationToken` は *null* 以外になり、さらに結果を取得するために、`result.continuationToken` を **queryEntities** の 3 番目のパラメーターとして使用できます。最初のクエリでは、3 番目のパラメーターは *null* にする必要があります。

###エンティティ プロパティのサブセットを照会する方法

テーブルに対するクエリでは、ごくわずかのフィールドだけをエンティティから取得できます。
この方法では、帯域幅の使用が削減され、クエリのパフォーマンスが向上します。特に、大量のエンティティがある場合に役立ちます。**select** 句を使用して、返されるフィールドの名前を渡します。たとえば、次のクエリでは **description** フィールドと **dueDate** フィールドのみを返します。

	var query = new azure.TableQuery()
	  .select(['description', 'dueDate'])
	  .top(5)
	  .where('PartitionKey eq ?', 'hometasks');

## <a name="delete-entity"> </a>エンティティを削除する方法

パーティション キーと行キーを使用してエンティティを削除できます。次の
例では、**task1** オブジェクトに、削除するエンティティの **RowKey** と 
**PartitionKey** の値が格納されます。次に、このオブジェクトが 
**deleteEntity** メソッドに渡されます。

	var task = { 
	  PartitionKey: {'_':'hometasks'},
	  RowKey: {'_': '1'}
	};

    tableSvc.deleteEntity('mytable', task, function(error, response){
	  if(!error) {
		// Entity deleted
	  }
	});

> [WACOM.NOTE] 項目を削除する場合は、項目が別のプロセスによって変更されていないことを確認するために ETag を使用することを検討してください。ETag の使用の詳細については、「[方法: エンティティを更新する][]」を参照してください。

## <a name="delete-table"> </a>テーブルを削除する方法

次のコードは、ストレージ アカウントからテーブルを削除します。

    tableSvc.deleteTable('mytable', function(error, response){
		if(!error){
			// Table deleted
		}
	});

テーブルが存在するかどうかが不明な場合は、**deleteTableIfExists** を使用します。

## <a name="sas"></a>方法:共有アクセス署名を操作する

共有アクセス署名 (SAS) は、ストレージ アカウントの名前またはキーを指定せずにテーブルへの細密なアクセスを提供する安全な方法です。多くの場合、SAS は、モバイル アプリでのレコードの照会などデータへの限定的なアクセスのために使用されます。

クラウドベースのサービスなど信頼されたアプリケーションは、**TableService** の **generateSharedAccessSignature** を使用して SAS を生成し、信頼されていない、または信頼性の低いアプリケーションに提供します。たとえば、モバイル アプリなどです。SAS は、SAS が有効である期間の開始日と終了日のほか、SAS の保有者に付与されたアクセス レベルを示したポリシーを使用して生成されます。

次の例では、SAS の保有者に対してテーブルのクエリ ('r') を許可し、作成時から 100 分後に期限が切れる、新しい共有アクセス ポリシーを作成しています。

	var startDate = new Date();
	var expiryDate = new Date(startDate);
	expiryDate.setMinutes(startDate.getMinutes() + 100);
	startDate.setMinutes(startDate.getMinutes() - 100);
		
	var sharedAccessPolicy = {
	  AccessPolicy: {
	    Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
	    Start: startDate,
	    Expiry: expiryDate
	  },
	};

	var tableSAS = tableSvc.generateSharedAccessSignature('mytable', sharedAccessPolicy);
	var host = tableSvc.host;

SAS の保有者がテーブルにアクセスするときに必要なホスト情報も提供する必要があることに注意してください。

クライアント アプリケーションは、この SAS と **TableServiceWithSAS** を使用してテーブルに対する操作を実行します。次の例では、テーブルに接続してクエリを実行しています。

	var sharedTableService = azure.createTableServiceWithSas(host, tableSAS);
	var query = azure.TableQuery()
	  .where('PartitionKey eq ?', 'hometasks');
		
	sharedTableService.queryEntities(query, null, function(error, result, response) {
	  if(!error) {
		// result contains the entities
	  }
	});

SAS がクエリ アクセスのみで生成された場合は、エンティティの挿入、更新、または削除を試行するとエラーが返されます。

###アクセス制御リスト

SAS のアクセス ポリシーを設定するために、アクセス制御リスト (ACL) も使用できます。複数のクライアントにテーブルへのアクセスを許可し、各クライアントに異なるアクセス ポリシーを提供する場合に便利です。

ACL は、アクセス ポリシーの配列と、各ポリシーに関連付けられた ID を使用して実装されます。次の例では、2 つのポリシーを定義しています。1 つは "user1" 用、もう 1 つは "user2" 用です。

	var sharedAccessPolicy = [
	  {
	    AccessPolicy: {
	      Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user1'
	  },
	  {
	    AccessPolicy: {
	      Permissions: azure.TableUtilities.SharedAccessPermissions.ADD,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user2'
	  }
	];

次の例では、現在の **hometasks** テーブルの ACL を取得し、**setTableAcl** を使用して新しいポリシーを追加しています。この手法で以下を実行できます。

	tableSvc.getTableAcl('hometasks', function(error, result, response) {
      if(!error){
		//push the new policy into signedIdentifiers
		result.signedIdentifiers.push(sharedAccessPolicy);
		tableSvc.setTableAcl('hometasks', result, function(error, result, response){
	  	  if(!error){
	    	// ACL set
	  	  }
		});
	  }
	});

ACL を設定した後で、ポリシーの ID に基づいて SAS を作成できます。次の例では、"user2" 用に新しい SAS を作成しています。

	tableSAS = tableSvc.generateSharedAccessSignature('hometasks', { Id: 'user2' });

## <a name="next-steps"> </a>次のステップ

これで、テーブル ストレージの基本を学習できました。さらに複雑なストレージ タスクを実行する方法については、次のリンク先を参照してください。
さらに複雑なストレージ タスクを実行する方法については、次のリンク先を参照してください。

-   MSDN リファレンス:[Azure のデータの格納とアクセス][]
-   [Azure のストレージ チーム ブログ][]
-   GitHub の [Azure Storage SDK for Node][] リポジトリ

  [Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node
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

  [OData.org]: http://www.odata.org/
  [REST API を使用する]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh264518.aspx
  [Azure の管理ポータル]: http://manage.windowsazure.com

  [Node.js クラウド サービス]: /ja-jp/documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Azure のデータの格納とアクセス]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg433040.aspx
  [Azure のストレージ チーム ブログ]: http://blogs.msdn.com/b/windowsazurestorage/
  [WebMatrix を使用した Web サイト]: /ja-jp/documentation/articles/web-sites-nodejs-use-webmatrix/
  [ストレージを使用する Node.js クラウド サービス]: /ja-jp/documentation/articles/storage-nodejs-use-table-storage-cloud-service-app/
  [ストレージを使用する Node.js Web アプリケーション]: /ja-jp/documentation/articles/storage-nodejs-use-table-storage-web-site/
  [Node.js アプリケーションの作成と Azure の Web サイトへの展開]: /ja-jp/documentation/articles/web-sites-nodejs-develop-deploy-mac/

<!--HONumber=35.1-->
