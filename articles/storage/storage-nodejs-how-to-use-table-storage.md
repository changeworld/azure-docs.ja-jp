<properties
	pageTitle="Node.js から Azure Table ストレージを使用する方法 | Microsoft Azure"
	description="NoSQL データ ストアの Azure Table Storage を使用して、構造化データをクラウドに格納します。"
	services="storage"
	documentationCenter="nodejs"
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="04/29/2016"
	ms.author="micurd"/>


# Node.js から Azure Table Storage を使用する方法

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]


## 概要

このトピックでは、Node.js アプリケーションで Azure Table サービスを使用して一般的なシナリオを実行する方法について説明します。

このトピックのコード例は、既に Node.js アプリケーションがあることを前提としています。Azure で Node.js アプリケーションを作成する方法については、次のいずれかのトピックを参照してください。

- [Azure App Service での Node.js Web アプリの作成](../app-service-web/web-sites-nodejs-develop-deploy-mac.md)
- [WebMatrix を使用した Node.js Web アプリの構築と Azure へのデプロイ](../app-service-web/web-sites-nodejs-use-webmatrix.md)
- [Node.js アプリケーションの構築と Azure クラウド サービスへのデプロイ](../cloud-services/cloud-services-nodejs-develop-deploy-app.md) (Windows PowerShell を使用)


[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]


## Azure Storage にアクセスするようにアプリケーションを構成する

Azure Storage を使用するには、Azure Storage SDK for Node.js が必要です。ここには、ストレージ REST サービスと通信するための便利なライブラリのセットが含まれています。

### ノード パッケージ マネージャー (NPM) を使用してパッケージをインストールする

1.  **PowerShell** (Windows)、**Terminal** (Mac)、**Bash** (Unix) などのコマンド ライン インターフェイスを使用して、アプリケーションを作成したフォルダーに移動します。

2.  コマンド ウィンドウに「**npm install azure-storage**」と入力します。このコマンドの出力は次の例のようになります。

		azure-storage@0.5.0 node_modules\azure-storage
		+-- extend@1.2.1
		+-- xmlbuilder@0.4.3
		+-- mime@1.2.11
		+-- node-uuid@1.4.3
		+-- validator@3.22.2
		+-- underscore@1.4.4
		+-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
		+-- xml2js@0.2.7 (sax@0.5.2)
		+-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)

3.  手動で **ls** コマンドを実行して、**node\_modules** フォルダーが作成されたことを確認することもできます。このフォルダーに **azure-storage** パッケージがあります。このパッケージには、ストレージにアクセスするために必要なライブラリが含まれています。

### パッケージをインポートする

アプリケーションの **server.js** ファイルの先頭に次のコードを追加します。

	var azure = require('azure-storage');

## Azure のストレージ接続文字列の設定

azure モジュールは、Azure のストレージ アカウントに接続するために必要な情報として、環境変数 AZURE\_STORAGE\_ACCOUNT、AZURE\_STORAGE\_ACCESS\_KEY、および AZURE\_STORAGE\_CONNECTION\_STRING を読み取ります。これらの環境変数が設定されていない場合、**TableService** を呼び出すときにアカウント情報を指定する必要があります。

Azure Website の [Azure ポータル](https://portal.azure.com)で環境変数を設定する例については、「[Azure Table サービスを使用する Node.js Web アプリ]」を参照してください。

## テーブルの作成

次のコードは、**TableService** オブジェクトを作成し、これを使用して新しいテーブルを作成します。**server.js** ファイルの先頭付近に次の内容を追加します。

	var tableSvc = azure.createTableService();

**createTableIfNotExists** の呼び出しにより、指定した名前の新しいテーブルが (まだ存在していない場合は) 作成されます。次の例では、"mytable" という名前のテーブルが存在しない場合、このテーブルを作成します。

	tableSvc.createTableIfNotExists('mytable', function(error, result, response){
	  if(!error){
	    // Table exists or created
	  }
	});

新しいテーブルを作成する場合、`result.created`は `true` になります。テーブルが既に存在する場合は `false` になります。`response`には要求に関する情報が含まれます。

### フィルター

オプションのフィルター操作は、**TableService** を使用して行われる操作に適用できます。フィルター操作には、ログ、自動的な再試行などが含まれる場合があります。フィルターは、次のシグネチャを持つメソッドを実装するオブジェクトです。

	function handle (requestOptions, next)

要求オプションに対するプリプロセスを行った後で、このメソッドは "next" を呼び出して、次のシグネチャのコールバックを渡す必要があります。

	function (returnObject, finalCallback, next)

このコールバックで、returnObject (サーバーへの要求からの応答) の処理の後に、コールバックは next を呼び出すか (他のフィルターの処理を続けるために next が存在する場合)、単に finalCallback を呼び出す必要があります (サービス呼び出しを終了する場合)。

再試行のロジックを実装する 2 つのフィルター (**ExponentialRetryPolicyFilter** と **LinearRetryPolicyFilter**) が、Azure SDK for Node.js に含まれています。次のコードは、**ExponentialRetryPolicyFilter** を使用する **TableService** オブジェクトを作成します。

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var tableSvc = azure.createTableService().withFilter(retryOperations);

## エンティティをテーブルに追加する

エンティティを追加するには、エンティティのプロパティを定義するオブジェクトを最初に作成します。すべてのエンティティには、エンティティの一意の識別子である **PartitionKey** と **RowKey** が含まれます。

* **PartitionKey** - エンティティが格納されるパーティションを決定します。

* **RowKey** - パーティション内のエンティティを一意に識別します。

**PartitionKey** と **RowKey** は両方とも文字列値にする必要があります。詳細については、「[Table サービス データ モデルについて](http://msdn.microsoft.com/library/azure/dd179338.aspx)」を参照してください。

エンティティを定義する例を次に示します。**dueDate** が **Edm.DateTime** の型として定義されている点に注意してください。型の指定は省略可能です。型を指定しなかった場合、型は推測されます。

	var task = {
	  PartitionKey: {'_':'hometasks'},
	  RowKey: {'_': '1'},
	  description: {'_':'take out the trash'},
	  dueDate: {'_':new Date(2015, 6, 20), '$':'Edm.DateTime'}
	};

> [AZURE.NOTE] 各レコードには、エンティティが挿入または更新される場合に Azure により設定される **Timestamp** フィールドもあります。

**entityGenerator** を使用してエンティティを作成することもできます。次の例では、**entityGenerator** を使用して同じタスク エンティティを作成しています。

	var entGen = azure.TableUtilities.entityGenerator;
	var task = {
	  PartitionKey: entGen.String('hometasks'),
	  RowKey: entGen.String('1'),
	  description: entGen.String('take out the trash'),
	  dueDate: entGen.DateTime(new Date(Date.UTC(2015, 6, 20))),
	};

エンティティをテーブルに追加するには、エンティティ オブジェクトを **insertEntity** メソッドに渡します。

	tableSvc.insertEntity('mytable',task, function (error, result, response) {
	  if(!error){
	    // Entity inserted
	  }
	});

操作が成功した場合、`result` には挿入されたレコードの [ETag](http://en.wikipedia.org/wiki/HTTP_ETag) が含まれ、`response` には操作に関する情報が含まれます。

応答の例:

	{ '.metadata': { etag: 'W/"datetime\'2015-02-25T01%3A22%3A22.5Z\'"' } }

> [AZURE.NOTE] 既定では、**insertEntity** は、`response` 情報の一部として、挿入されたエンティティを返しません。このエンティティに対して他の操作を実行する予定がある場合、または情報をキャッシュする場合は、`result` の一部として返されるようにすると便利です。そのためには、次のように **echoContent** を有効にします。
>
> `tableSvc.insertEntity('mytable', task, {echoContent: true}, function (error, result, response) {...}`

## エンティティを更新する

既存のエンティティを更新するには、複数のメソッドがあります。

* **replaceEntity** - 既存のエンティティを置換することで更新します。

* **mergeEntity** - 新しいプロパティ値を既存のエンティティにマージすることで既存のエンティティを更新します。

* **insertOrReplaceEntity** - 既存のエンティティを、置換することで更新します。エンティティが存在しない場合は、新しいエンティティが挿入されます。

* **insertOrMergeEntity** - 既存のエンティティを、新しいプロパティ値を既存のエンティティにマージすることで更新します。エンティティが存在しない場合は、新しいエンティティが挿入されます。

次の例に、**replaceEntity** を使用してエンティティを更新する方法を示します。

	tableSvc.replaceEntity('mytable', updatedTask, function(error, result, response){
	  if(!error) {
	    // Entity updated
	  }
	});

> [AZURE.NOTE] 既定では、エンティティを更新するときに、更新対象のデータが以前に別のプロセスにより変更されているかどうかは確認されません。同時更新をサポートするには、次の手順を実行します。
>
> 1. 更新するオブジェクトの ETag を取得します。これは、任意のエンティティに関連する操作の `response` の一部として返され、`response['.metadata'].etag` を通じて取得できます。
>
> 2. エンティティで更新操作を実行する場合は、以前に取得した ETag 情報を新しいエンティティに追加します。次に例を示します。
>
>     `entity2['.metadata'].etag = currentEtag;`
>
> 3. 更新操作を実行します。アプリケーションの別のインスタンスなど、ETag 値を取得した後でエンティティが更新されている場合は、要求で指定された更新の条件が満たされていないことを示す `error` が返されます。

**replaceEntity** と **mergeEntity** では、更新されるエンティティが存在しない場合、更新操作は失敗します。したがって、既に存在しているかどうかに関係なくエンティティを格納するには、代わりに **insertOrReplaceEntity** または **insertOrMergeEntity** を使用します。

成功した更新操作の `result` には、更新されたエンティティの **Etag** が含まれます。

## エンティティのグループを操作する

状況によって、複数の操作をバッチとして送信し、サーバーによるアトミック処理を行うことが合理的である場合があります。これを実現するには、**TableBatch** クラスを使用してバッチを作成し、**TableService** の **executeBatch** メソッドを使用してバッチ操作を実行します。

 次の例に、2 つのエンティティをバッチで送信する方法を示します。

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

### バッチ操作の処理

バッチに追加された操作は、`operations` プロパティで確認できます。次のメソッドを使用して操作を処理できます。

* **clear** - バッチのすべての操作をクリアします。

* **getOperations** - バッチから操作を取得します。

* **hasOperations** - バッチに操作が含まれている場合は true を返します。

* **removeOperations** - 操作を削除します。

* **size** - バッチ内の操作の数を返します。

## キーを使用したエンティティを取得する

**PartitionKey** および **RowKey** に基づいて特定のエンティティを返すには、**retrieveEntity** メソッドを使用します。

	tableSvc.retrieveEntity('mytable', 'hometasks', '1', function(error, result, response){
	  if(!error){
	    // result contains the entity
	  }
	});

この操作を完了すると、`result` にはエンティティが含まれます。

## エンティティのセットを照会する

テーブルを照会するには、**TableQuery** オブジェクトを使用し、以下の句を使用してクエリ式を作成します。

* **select** - クエリから返されるフィールド

* **where** - where 句

	* **and** - `and` where 条件

	* **or** - `or` where 条件

* **top** - 取得する項目の数


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

成功した場合は、`result.entries` にはクエリに一致するエンティティの配列が含まれます。クエリですべてのエンティティを返すことができなかった場合、`result.continuationToken` は *null* 以外になり、さらに結果を取得するために、これを **queryEntities** の 3 番目のパラメーターとして使用できます。最初のクエリでは、3 番目のパラメーターに *null* を使用します。

### エンティティ プロパティのサブセットを照会する

テーブルに対するクエリでは、ごくわずかのフィールドだけをエンティティから取得できます。この方法では、帯域幅の使用が削減され、クエリのパフォーマンスが向上します。特に、大量のエンティティがある場合に役立ちます。**select** 句を使用して、返されるフィールドの名前を渡します。たとえば、次のクエリでは **description** フィールドと **dueDate** フィールドのみを返します。

	var query = new azure.TableQuery()
	  .select(['description', 'dueDate'])
	  .top(5)
	  .where('PartitionKey eq ?', 'hometasks');

## エンティティを削除する

パーティション キーと行キーを使用してエンティティを削除できます。次の例では、**task1** オブジェクトに、削除するエンティティの **RowKey** と **PartitionKey** の値が格納されます。次に、このオブジェクトが **deleteEntity** メソッドに渡されます。

	var task = {
	  PartitionKey: {'_':'hometasks'},
	  RowKey: {'_': '1'}
	};

	tableSvc.deleteEntity('mytable', task, function(error, response){
	  if(!error) {
	    // Entity deleted
	  }
	});

> [AZURE.NOTE] 項目を削除する場合は、項目が別のプロセスによって変更されていないことを確認するために ETag を使用することを検討してください。ETag の使用の詳細については、「[エンティティを更新する](#update-an-entity)」を参照してください。

## テーブルを削除する

次のコードは、ストレージ アカウントからテーブルを削除します。

	tableSvc.deleteTable('mytable', function(error, response){
		if(!error){
			// Table deleted
		}
	});

テーブルが存在するかどうかが不明な場合は、**deleteTableIfExists** を使用します。

## 継続トークンを使用する

テーブルに対するクエリによって大量の結果が返される場合は、継続トークンを探します。クエリの対象となるデータは大量になる可能性があります。継続トークンが存在するときにわかるようにしていない場合はこの事実に気付かない可能性があります。

エンティティを照会したときに返される結果オブジェクトは、このようなトークンが存在する場合に `continuationToken` プロパティを設定します。クエリを実行するときにこれを使用して、後続のパーティションおよびテーブル エンティティに移動できます。

クエリを実行するとき、クエリ オブジェクト インスタンスとコールバック関数の間に continuationToken パラメーターを指定することができます。

```
var nextContinuationToken = null;
dc.table.queryEntities(tableName,
    query,
    nextContinuationToken,
    function (error, results) {
        if (error) throw error;

        // iterate through results.entries with results

        if (results.continuationToken) {
            nextContinuationToken = results.continuationToken;
        }

    });
```

`continuationToken` オブジェクトには `nextPartitionKey`、`nextRowKey`、`targetLocation` などのプロパティがあり、これらを使用してすべての結果を反復処理できます。

GitHub の Azure Storage の Node.js のリポジトリには継続のサンプルも用意されています。`examples/samples/continuationsample.js` を参照してください。

## 共有アクセス署名を操作する

共有アクセス署名 (SAS) は、ストレージ アカウントの名前またはキーを指定せずにテーブルへの細密なアクセスを提供する安全な方法です。多くの場合、SAS は、モバイル アプリでのレコードの照会などデータへの限定的なアクセスのために使用されます。

クラウドベースのサービスなどの信頼されたアプリケーションは、**TableService** の **generateSharedAccessSignature** を使用して SAS を生成し、信頼されていないか、モバイル アプリなどの部分的に信頼されたアプリケーションにこれを提供します。SAS は、SAS が有効である期間の開始日と終了日のほか、SAS の保有者に付与されたアクセス レベルを示したポリシーを使用して生成されます。

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

### アクセス制御リスト

SAS のアクセス ポリシーを設定するために、アクセス制御リスト (ACL) も使用できます。複数のクライアントにテーブルへのアクセスを許可し、各クライアントに異なるアクセス ポリシーを提供する場合に便利です。

ACL は、アクセス ポリシーの配列と、各ポリシーに関連付けられた ID を使用して実装されます。次の例では、2 つのポリシーを定義しています。1 つは "user1" 用、もう 1 つは "user2" 用です。

	var sharedAccessPolicy = {
	  user1: {
	    Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
	    Start: startDate,
	    Expiry: expiryDate
	  },
	  user2: {
	    Permissions: azure.TableUtilities.SharedAccessPermissions.ADD,
	    Start: startDate,
	    Expiry: expiryDate
	  }
	};

次の例では、現在の **hometasks** テーブルの ACL を取得し、**setTableAcl** を使用して新しいポリシーを追加しています。この手法で以下を実行できます。

	var extend = require('extend');
	tableSvc.getTableAcl('hometasks', function(error, result, response) {
    if(!error){
	    var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
	    tableSvc.setTableAcl('hometasks', newSignedIdentifiers, function(error, result, response){
	      if(!error){
	        // ACL set
	      }
	    });
	  }
	});

ACL を設定した後で、ポリシーの ID に基づいて SAS を作成できます。次の例では、"user2" 用に新しい SAS を作成しています。

	tableSAS = tableSvc.generateSharedAccessSignature('hometasks', { Id: 'user2' });

## 次のステップ

詳細については、次のリソースを参照してください。

-   [Azure Storage チーム ブログ][]
-   GitHub の [Azure Storage SDK for Node][] リポジトリ
-   [Node.js デベロッパー センター](/develop/nodejs/)

  [Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node
  [OData.org]: http://www.odata.org/
  [Using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
  [Azure Portal]: portal.azure.com

  [Node.js Cloud Service]: ../cloud-services-nodejs-develop-deploy-app.md
  [Azure Storage チーム ブログ]: http://blogs.msdn.com/b/windowsazurestorage/
  [Website with WebMatrix]: ../web-sites-nodejs-use-webmatrix.md
  [Node.js Cloud Service with Storage]: ../storage-nodejs-use-table-storage-cloud-service-app.md
  [Azure Table サービスを使用する Node.js Web アプリ]: ../storage-nodejs-use-table-storage-web-site.md
  [Create and deploy a Node.js application to an Azure website]: ../web-sites-nodejs-develop-deploy-mac.md

<!---HONumber=AcomDC_0511_2016-->