---
title: Azure Cosmos DB の Node.js サンプル | Microsoft Docs
description: CRUD 操作など、Azure Cosmos DB の一般的なタスクについては、GitHub の Node.js のサンプルを参照してください。
keywords: Node.js のサンプル
services: cosmos-db
author: moderakh
manager: kfile
editor: monicar
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: na
ms.topic: sample
ms.date: 05/23/2017
ms.author: moderakh
ms.openlocfilehash: 8ee5add72845a0540c05bb8353f8d7d4a40cac71
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39600582"
---
# <a name="azure-cosmos-db-nodejs-examples"></a>Azure Cosmos DB Node.js のサンプル
> [!div class="op_single_selector"]
> * [.NET のサンプル](sql-api-dotnet-samples.md)
> * [Java のサンプル](sql-api-java-samples.md)
> * [非同期 Java のサンプル](sql-api-async-java-samples.md)
> * [Node.js のサンプル](sql-api-nodejs-samples.md)
> * [Node.js のサンプル - v2.0 プレビュー](sql-api-nodejs-samples-preview.md)
> * [Python のサンプル](sql-api-python-samples.md)
> * [Azure のコード サンプル ギャラリー](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Azure Cosmos DB のリソースで CRUD 操作などの一般的な操作を実行するサンプル ソリューションは、[azure-documentdb-nodejs](https://github.com/Azure/azure-documentdb-node/tree/master/samples) GitHub リポジトリに含まれています。 この記事では、次の内容について説明します。

* 各 Node.js サンプル プロジェクト ファイルのタスクへのリンク。
* 関連する API リファレンス コンテンツへのリンク。

**前提条件**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- [Visual Studio サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)こともできます: Visual Studio サブスクリプションにより、有料の Azure サービスで使用できるクレジットが毎月提供されます。

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

[Node.js SDK](sql-api-sdk-node.md)も必要です。
   
   > [!NOTE]
   > 各サンプルは自己完結型であり、自身をセットアップし、自身をクリーンアップします。 そのため、サンプルでは [DocumentClient.createCollection](https://docs.microsoft.com/en-us/javascript/api/documentdb/DocumentClient?view=azure-node-latest#createcollection-databaselink--body--options--callback-
) の複数の呼び出しを発行します。 これが行われるたびに、作成中のコレクションのパフォーマンス階層ごとに 1 時間の使用量に対するサブスクリプションが課金されます。
   > 
   > 

## <a name="database-examples"></a>データベースのサンプル
[DatabaseManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/DatabaseManagement) プロジェクトの [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/DatabaseManagement/app.js) ファイルは、次のタスクを実行する方法を示しています。

| タスク | API リファレンス |
| --- | --- |
| [データベースの作成](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L121-L131) |[DocumentClient.createDatabase](https://docs.microsoft.com/en-us/javascript/api/documentdb/DocumentClient?view=azure-node-latest#createdatabase-body--options--callback-) |
| [データベースのアカウントのクエリ](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L146-L171) |[DocumentClient.queryDatabases](https://docs.microsoft.com/en-us/javascript/api/documentdb/DocumentClient?view=azure-node-latest#querydatabases-query--options-) |
| [ID でのデータベースの読み取り](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L89-L99) |[DocumentClient.readDatabase](https://docs.microsoft.com/en-us/javascript/api/documentdb/DocumentClient?view=azure-node-latest#readdatabase-databaselink--options--callback-) |
| [アカウントのデータベースの一覧表示](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L111-L119) |[DocumentClient.readDatabases](https://docs.microsoft.com/en-us/javascript/api/documentdb/DocumentClient?view=azure-node-latest#readdatabases-options-) |
| [データベースの削除](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L133-L144) |[DocumentClient.deleteDatabase](https://docs.microsoft.com/en-us/javascript/api/documentdb/DocumentClient?view=azure-node-latest#deletedatabase-databaselink--options--callback-) |

## <a name="collection-examples"></a>コレクションのサンプル
[CollectionManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/CollectionManagement) プロジェクトの [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/CollectionManagement/app.js) ファイルは、次のタスクを実行する方法を示しています。

| タスク | API リファレンス |
| --- | --- |
| [コレクションの作成](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L97-L118) |[DocumentClient.createCollection](https://docs.microsoft.com/en-us/javascript/api/documentdb/DocumentClient?view=azure-node-latest#createcollection-databaselink--body--options--callback-) |
| [データベース内のすべてのコレクションの一覧の読み取り](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L120-L130) |[DocumentClient.readCollections](https://docs.microsoft.com/en-us/javascript/api/documentdb/DocumentClient?view=azure-node-latest#readcollections-databaselink--options-) |
| [_self でのコレクションの取得](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L132-L141) |[DocumentClient.readCollection](https://docs.microsoft.com/en-us/javascript/api/documentdb/DocumentClient?view=azure-node-latest#readcollection-collectionlink--options--callback-) |
| [ID でのコレクションの取得](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L143-L156) |[DocumentClient.readCollection](https://docs.microsoft.com/en-us/javascript/api/documentdb/DocumentClient?view=azure-node-latest#readcollection-collectionlink--options--callback-) |
| [コレクションのパフォーマンス階層の取得](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L158-L186) |[DocumentQueryable.queryOffers](https://docs.microsoft.com/en-us/javascript/api/documentdb/DocumentClient?view=azure-node-latest#queryoffers-query--options-) |
| [コレクションのパフォーマンス階層の変更](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L188-L202) |[DocumentClient.replaceOffer](https://docs.microsoft.com/en-us/javascript/api/documentdb/DocumentClient?view=azure-node-latest#replaceoffer-offerlink--offer--callback-) |
| [コレクションの削除](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L204-L215) |[DocumentClient.deleteCollection](https://docs.microsoft.com/en-us/javascript/api/documentdb/DocumentClient?view=azure-node-latest#deletecollection-collectionlink--options--callback-) |

## <a name="document-examples"></a>ドキュメントのサンプル
[DocumentManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/DocumentManagement) プロジェクトの [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/DocumentManagement/app.js) ファイルは、次のタスクを実行する方法を示しています。

| タスク | API リファレンス |
| --- | --- |
| [ドキュメントの作成](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L153-L177) |[DocumentClient.createDocument](https://docs.microsoft.com/en-us/javascript/api/documentdb/DocumentClient?view=azure-node-latest#createdocument-documentsfeedordatabaselink--body--options--callback-) |
| [コレクションのドキュメント フィードの読み取り](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L179-L189) |[DocumentClient.readDocument](https://docs.microsoft.com/en-us/javascript/api/documentdb/DocumentClient?view=azure-node-latest#readdocument-documentlink--options--callback-) |
| [ID でのドキュメントの読み取り](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L191-L201) |[DocumentClient.readDocument](https://docs.microsoft.com/en-us/javascript/api/documentdb/DocumentClient?view=azure-node-latest#readdocument-documentlink--options--callback-) |
| [ドキュメントが変更された場合にのみ、ドキュメントを読み取る](https://github.com/Azure/azure-documentdb-node/blob/0778eadea7abb2af41e8c22a239dc872c584f421/samples/DocumentManagement/app.js#L79-L107) |[DocumentClient.readDocument](https://docs.microsoft.com/en-us/javascript/api/documentdb/DocumentClient?view=azure-node-latest#readdocument-documentlink--options--callback-)<br/>[RequestOptions.accessCondition](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.documents.client.requestoptions.accesscondition?view=azure-dotnet#Microsoft_Azure_Documents_Client_RequestOptions_AccessCondition) |
| [ドキュメントのクエリ](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L82-L110) |[DocumentClient.queryDocuments](https://docs.microsoft.com/javascript/api/documentdb/DocumentClient?view=azure-node-latest#querydocuments-documentsfeedordatabaselink--query--options-) |
| [ドキュメントの置換](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L112-L119) |[DocumentClient.replaceDocument](https://docs.microsoft.com/javascript/api/documentdb/DocumentClient?view=azure-node-latest#replacedocument-documentlink--document--options--callback-) |
| [条件付き ETag チェックでドキュメントを置換する](https://github.com/Azure/azure-documentdb-node/blob/0778eadea7abb2af41e8c22a239dc872c584f421/samples/DocumentManagement/app.js#L147-L164) |[DocumentClient.replaceDocument](https://docs.microsoft.com/javascript/api/documentdb/DocumentClient?view=azure-node-latest#replacedocument-documentlink--document--options--callback-)<br/>[RequestOptions.accessCondition](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.documents.client.requestoptions.accesscondition?view=azure-dotnet#Microsoft_Azure_Documents_Client_RequestOptions_AccessCondition) |
| [ドキュメントの削除](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L122-L133) |[DocumentClient.deleteDocument](https://docs.microsoft.com/javascript/api/documentdb/DocumentClient?view=azure-node-latest#deletedocument-documentlink--options--callback-) |

## <a name="indexing-examples"></a>インデックス作成のサンプル
[IndexManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/IndexManagement) プロジェクトの [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/IndexManagement/app.js) ファイルは、次のタスクを実行する方法を示しています。

| タスク | API リファレンス |
| --- | --- |
| [既定のインデックス作成でのコレクションの作成](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L657-L701) |[DocumentClient.createCollection](https://docs.microsoft.com/en-us/javascript/api/documentdb/DocumentClient?view=azure-node-latest#createcollection-databaselink--body--options--callback-) |
| [特定のドキュメントの手動でのインデックス作成](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L185-L238) |[RequestOptions.indexingDirective: 'include'](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.documents.client.requestoptions.indexingdirective?view=azure-dotnet#Microsoft_Azure_Documents_Client_RequestOptions_IndexingDirective) |
| [インデックスからの特定のドキュメントの手動での除外](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L120-L183) |[RequestOptions.indexingDirective: 'exclude'](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.documents.client.requestoptions.indexingdirective?view=azure-dotnet#Microsoft_Azure_Documents_Client_RequestOptions_IndexingDirective) |
| [一括インポートでの非同期インデックス作成の使用または大量のコレクションの読み取り](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L240-L269) |[IndexingMode.Lazy](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.indexingmode?view=azure-dotnet) |
| [インデックス作成にドキュメントの特定のパスを含める](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L433-L444) |[IndexingPolicy.IncludedPaths](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.documents.indexingpolicy.includedpaths?view=azure-dotnet#Microsoft_Azure_Documents_IndexingPolicy_IncludedPaths) |
| [インデックス作成からの特定のパスの除外](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L427-L450) |[IndexingPolicy.ExcludedPath](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.documents.indexingpolicy.excludedpaths?view=azure-dotnet#Microsoft_Azure_Documents_IndexingPolicy_ExcludedPaths) |
| [範囲の操作中での文字列のパスのスキャンの許可](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L271-L347) |[FeedOptions.EnableScanInQuery](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.documents.client.feedoptions.enablescaninquery?view=azure-dotnet#Microsoft_Azure_Documents_Client_FeedOptions_EnableScanInQuery) |
| [文字列のパスでの範囲インデックスの作成](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L349-L425) |[IndexKind.Range](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.documents.indexkind?view=azure-dotnet#Microsoft_Azure_Documents_IndexKind_Range)、[IndexingPolicy](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.documents.indexingpolicy?view=azure-dotnet)、[DocumentClient.queryDocument](https://docs.microsoft.com/en-us/javascript/api/documentdb/documentclient?view=azure-node-latest) |
| [既定の indexPolicy のコレクションを作成し、オンラインで更新する](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L519-L614) |[DocumentClient.createCollection](https://docs.microsoft.com/javascript/api/documentdb/DocumentClient?view=azure-node-latest#createcollection-databaselink--body--options--callback-)<br> [DocumentClient.replaceCollection#replaceCollection](https://docs.microsoft.com/javascript/api/documentdb/DocumentClient?view=azure-node-latest#replacecollection-collectionlink--collection--options--callback-) |

インデックス作成の詳細については、「[Azure Cosmos DB インデックス作成ポリシー](indexing-policies.md)」をご覧ください。

## <a name="server-side-programming-examples"></a>サーバー側プログラミングのサンプル
[ServerSideScripts](https://github.com/Azure/azure-documentdb-node/tree/master/samples/ServerSideScripts) プロジェクトの [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/ServerSideScripts/app.js) ファイルは、次のタスクを実行する方法を示しています。

| タスク | API リファレンス |
| --- | --- |
| [ストアド プロシージャの作成](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.ServerSideScripts/app.js#L44-L71) |[DocumentClient.createStoredProcedure](https://docs.microsoft.com/javascript/api/documentdb/DocumentClient?view=azure-node-latest#createstoredprocedure-collectionlink--sproc--options--callback-) |
| [ストアド プロシージャの実行](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.ServerSideScripts/app.js#L73-L90) |[DocumentClient.executeStoredProcedure](https://docs.microsoft.com/javascript/api/documentdb/DocumentClient?view=azure-node-latest#executestoredprocedure-sproclink--params--options--callback-) |

サーバー側プログラミングの詳細については、「[Azure Cosmos DB server-side programming: Stored procedures, database triggers, and UDFs (Azure Cosmos DB のサーバー側プログラミング: ストアド プロシージャ、データベース トリガー、UDF)](programming.md)」をご覧ください。

## <a name="partitioning-examples"></a>パーティション分割のサンプル
[Partitioning](https://github.com/Azure/azure-documentdb-node/tree/master/samples/Partitioning) プロジェクトの [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/Partitioning/app.js) ファイルは、次のタスクを実行する方法を示しています。

| タスク | API リファレンス |
| --- | --- |
| [HashPartitionResolver の使用](https://github.com/Azure/azure-documentdb-node/blob/ce0fc3c4e70b0279091a1e03620a668d93a14fc2/samples/Partitioning/app.js#L53-L103) |[HashPartitionResolver](https://docs.microsoft.com/javascript/api/documentdb/HashPartitionResolver?view=azure-node-latest) |

Azure Cosmos DB でのデータのパーティション分割の詳細については、[Azure Cosmos DB でのデータのパーティション分割とスケーリング](partition-data.md)に関するページをご覧ください。

