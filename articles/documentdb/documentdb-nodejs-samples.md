<properties 
	pageTitle="DocumentDB の NoSQL Node.js のサンプル | Microsoft Azure" 
	description="NoSQL データベースでの JSON ドキュメントに対する CRUD 操作など、DocumentDB の一般的なタスクの Node.js サンプルを github で検索します。" 
	keywords="node.js のサンプル"
	services="documentdb" 
	authors="aliuy" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter="nodejs"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/18/2016" 
	ms.author="andrl"/>


# DocumentDB Node.js のサンプル

> [AZURE.SELECTOR]
- [.NET のサンプル](documentdb-dotnet-samples.md)
- [Node.js のサンプル](documentdb-nodejs-samples.md)
- [Python のサンプル](documentdb-python-samples.md)
- [Azure のコード サンプル ギャラリー](https://azure.microsoft.com/documentation/samples/?service=documentdb)

DocumentDB のリソースで CRUD 操作などの一般的な操作を実行するサンプル ソリューションは、[azure-documentdb-nodejs](https://github.com/Azure/azure-documentdb-node/tree/master/samples) GitHub リポジトリに含まれています。この記事では、次の内容について説明します。

- 各 Node.js サンプル プロジェクト ファイルのタスクへのリンク。 
- 関連する API リファレンス コンテンツへのリンク。

**前提条件**

1. これらの Node.js サンプルを使用するには Azure アカウントが必要です。
    - [無料で Azure アカウントを開く](https://azure.microsoft.com/pricing/free-trial/)ことができます。Azure の有料サービスを試用できるクレジットが提供されます。このクレジットを使い切ってもアカウントは維持されるため、Websites など無料の Azure サービスをご利用になれます。明示的に設定を変更して課金を求めない限り、クレジット カードに課金されることはありません。
   - [Visual Studio サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)こともできます: Visual Studio サブスクリプションにより、有料の Azure サービスで使用できるクレジットが毎月提供されます。
2. [Node.js SDK](documentdb-sdk-node.md) も必要です。 

    > [AZURE.NOTE] 各サンプルは自己完結型であり、自身をセットアップし、自身をクリーンアップします。そのため、サンプルでは [DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html) の複数の呼び出しを発行します。これが行われるたびに、作成中のコレクションのパフォーマンス階層ごとに 1 時間の使用量に対するサブスクリプションが課金されます。

## データベースのサンプル

[DatabaseManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/DatabaseManagement) プロジェクトの [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/DatabaseManagement/app.js) ファイルは、次のタスクを実行する方法を示しています。

タスク | API リファレンス
--- | ---
[データベースの作成](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L121-L131) | [DocumentClient.createDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createDatabase)
[データベースのアカウントのクエリ](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L146-L171) | [DocumentClient.queryDatabases](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDatabase)
[ID でのデータベースの読み取り](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L89-L99) | [DocumentClient.readDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDatabase)
[アカウントのデータベースの一覧表示](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L111-L119) | [DocumentClient.readDatabases](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDatabase)
[データベースの削除](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L133-L144) | [DocumentClient.deleteDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteDatabase)

## コレクションのサンプル 

[CollectionManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/CollectionManagement) プロジェクトの [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/CollectionManagement/app.js) ファイルは、次のタスクを実行する方法を示しています。

タスク | API リファレンス
--- | ---
[コレクションの作成](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L97-L118) | [DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createCollection)
[データベース内のすべてのコレクションの一覧の読み取り](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L120-L130) | [DocumentClient.listCollections](http://azure.github.io/azure-documentdb-node/DocumentClient.html#listCollections)
[\_self でのコレクションの取得] (https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L132-L141) | [DocumentClient.readCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readCollection) [ID でのコレクションの取得](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L143-L156) | [DocumentClient.readCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readCollection) [コレクションのパフォーマンス階層の取得](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L158-L186) | [DocumentQueryable.queryOffers](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryOffers) [コレクションのパフォーマンス階層の変更](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L188-L202) | [DocumentClient.replaceOffer](http://azure.github.io/azure-documentdb-node/DocumentClient.html#replaceOffer) [コレクションの削除](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L204-L215) | [DocumentClient.deleteCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteCollection)

## ドキュメントのサンプル

[DocumentManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/DocumentManagement) プロジェクトの [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/DocumentManagement/app.js) ファイルは、次のタスクを実行する方法を示しています。

タスク | API リファレンス
--- | ---
[ドキュメントの作成](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L153-L177) | [DocumentClient.createDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createDocument)
[コレクションのドキュメント フィードの読み取り](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L179-L189) | [DocumentClient.readDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDocument)
[ID でのドキュメントの読み取り](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L191-L201) | [DocumentClient.readDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDocument)
[ドキュメントが変更された場合にのみ、ドキュメントを読み取る](https://github.com/Azure/azure-documentdb-node/blob/0778eadea7abb2af41e8c22a239dc872c584f421/samples/DocumentManagement/app.js#L79-L107) | [DocumentClient.readDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDocument)<br/>[RequestOptions.accessCondition](http://azure.github.io/azure-documentdb-node/global.html#RequestOptions)
[ドキュメントのクエリ](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L82-L110) | [DocumentClient.queryDocuments](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDocuments) 
[ドキュメントの置換](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L112-L119) | [DocumentClient.replaceDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#replaceDocument)
[条件付き ETag チェックでドキュメントを置換する](https://github.com/Azure/azure-documentdb-node/blob/0778eadea7abb2af41e8c22a239dc872c584f421/samples/DocumentManagement/app.js#L147-L164) | [DocumentClient.replaceDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#replaceDocument)<br/>[RequestOptions.accessCondition](http://azure.github.io/azure-documentdb-node/global.html#RequestOptions)
[ドキュメントの削除](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L122-L133) | [DocumentClient.deleteDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteDocument)

## インデックス作成のサンプル

[IndexManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/IndexManagement) プロジェクトの [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/IndexManagement/app.js) ファイルは、次のタスクを実行する方法を示しています。

タスク | API リファレンス
--- | ---
既定のインデックス作成でのコレクションの作成 | [DocumentClient.createDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html)
[特定のドキュメントの手動でのインデックス作成](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L185-L238) | [indexingDirective: 'include'](http://azure.github.io/azure-documentdb-node/global.html#indexingDirective)
[インデックスからの特定のドキュメントの手動での除外](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L120-L183) | [RequestOptions.indexingDirective](http://azure.github.io/azure-documentdb-node/global.html#RequestOptions)
[一括インポートでの非同期インデックス作成の使用または大量のコレクションの読み取り](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L240-L269) | [IndexingMode.Lazy](http://azure.github.io/azure-documentdb-node/global.html#indexingmode)
[インデックス作成にドキュメントの特定のパスを含める](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L433-L444) | [IndexingPolicy.IncludedPaths](http://azure.github.io/azure-documentdb-node/global.html#indexingpolicy) 
[インデックス作成からの特定のパスの除外](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L427-L450) | [ExcludedPath](http://azure.github.io/azure-documentdb-node/global.html#indexingpolicy)
[範囲の操作中での文字列のパスのスキャンの許可](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L271-L347)| [ExcludedPath.EnableScanInQuery](http://azure.github.io/azure-documentdb-node/global.html#FeedOptions)
[文字列のパスでの範囲インデックスの作成](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L349-L425) | [DocumentClient.queryDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDocument)
[既定の indexPolicy のコレクションを作成し、オンラインで更新する](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L519-L614) | [DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createCollection)<br> [DocumentClient.replaceCollection#replaceCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html)

インデックス作成の詳細については、「[DocumentDB インデックス作成ポリシー](documentdb-indexing-policies.md)」をご覧ください。

## サーバー側プログラミングのサンプル

[ServerSideScripts](https://github.com/Azure/azure-documentdb-node/tree/master/samples/ServerSideScripts) プロジェクトの [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/ServerSideScripts/app.js) ファイルは、次のタスクを実行する方法を示しています。

タスク | API リファレンス
--- | ---
[ストアド プロシージャの作成](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.ServerSideScripts/app.js#L44-L71) | [DocumentClient.createStoredProcedure](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createStoredProcedure)
[ストアド プロシージャの実行](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.ServerSideScripts/app.js#L73-L90) | [DocumentClient.executeStoredProcedure](http://azure.github.io/azure-documentdb-node/DocumentClient.html#executeStoredProcedure)

サーバー側プログラミングの詳細については、「[DocumentDB のサーバー側プログラミング: ストアド プロシージャ、データベース トリガー、UDF](documentdb-programming.md)」をご覧ください。

## パーティション分割のサンプル

[Partitioning](https://github.com/Azure/azure-documentdb-node/tree/master/samples/Partitioning) プロジェクトの [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/Partitioning/app.js) ファイルは、次のタスクを実行する方法を示しています。

タスク | API リファレンス
--- | ---
[HashPartitionResolver の使用](https://github.com/Azure/azure-documentdb-node/blob/ce0fc3c4e70b0279091a1e03620a668d93a14fc2/samples/Partitioning/app.js#L53-L103) | [HashPartitionResolver](http://documentdb.com)

DocumentDB でのデータのパーティション分割の詳細については、「[DocumentDB のデータのパーティション分割と拡大縮小](documentdb-partition-data.md)」をご覧ください。

<!---HONumber=AcomDC_0420_2016-->