---
title: 'Azure Cosmos DB: SQL API の Java のサンプル | Microsoft Docs'
description: CRUD 操作など、Azure Cosmos DB SQL API を使う一般的なタスクについては、GitHub の Java のサンプルを参照してください。
keywords: NoSQL のサンプル
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: java
ms.topic: sample
ms.date: 02/08/2018
ms.author: sngun
ms.openlocfilehash: 539e863d05c2d343a643c54c39c1e5a28694c03a
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2018
ms.locfileid: "52163222"
---
# <a name="azure-cosmos-db-java-examples-for-the-sql-api"></a>Azure Cosmos DB: SQL API の Java のサンプル

> [!div class="op_single_selector"]
> * [.NET のサンプル](sql-api-dotnet-samples.md)
> * [Java のサンプル](sql-api-java-samples.md)
> * [非同期 Java のサンプル](sql-api-async-java-samples.md)
> * [Node.js のサンプル](sql-api-nodejs-samples.md)
> * [Python のサンプル](sql-api-python-samples.md)
> * [Azure のコード サンプル ギャラリー](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Azure Cosmos DB リソースに対する CRUD 操作などの一般的な操作を実行する最新のサンプル アプリケーションは、[azure-documentdb-java](https://github.com/Azure/azure-documentdb-java) GitHub レポジトリにあります。 この記事では、次の内容について説明します。

* 各サンプル Java プロジェクト ファイルのタスクへのリンク。 
* 関連する API リファレンス コンテンツへのリンク。

**前提条件**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Visual Studio サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)こともできます: Visual Studio サブスクリプションにより、有料の Azure サービスで使用できるクレジットが毎月提供されます。

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

このサンプル アプリケーションを実行するには、次のものが必要です。

* Java Development Kit 7
* Microsoft Azure DocumentDB Java SDK

プロジェクトで使用するための最新の Microsoft Azure DocumentDB Java SDK バイナリが必要な場合は、Maven を使用して取得できます。 必要な依存関係は Maven によって自動的に追加されます。 または、pom.xml ファイルに一覧表示されている依存関係を直接ダウンロードして、ビルドのパスに追加することもできます。

```bash
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-documentdb</artifactId>
    <version>LATEST</version>
</dependency>
```

**サンプル アプリケーションの実行**

サンプル リポジトリを複製します。
```bash
$ git clone https://github.com/Azure/azure-documentdb-java.git

$ cd azure-documentdb-java
```

サンプルは、Eclipse を使用して実行するか、Maven を使用してコマンドラインから実行できます。

Eclipse から実行するには:
* Eclipse にメインの親プロジェクト pom.xml ファイルを読み込みます。documentdb-examples が自動的に読み込まれます。
* サンプルを実行するには、有効な Azure Cosmos DB エンドポイントが必要です。 エンドポイントは次の場所から読み取られます。`src/test/java/com/microsoft/azure/documentdb/examples/AccountCredentials.java`
* エンドポイントの資格情報は、Eclipse JUnit Run Config で VM 引数として渡すか、AccountCredentials.java 内に設定できます。
    ```bash
    -DACCOUNT_HOST="https://REPLACE_THIS.documents.azure.com:443/" -DACCOUNT_KEY="REPLACE_THIS"
    ```
* これで、サンプルを Eclipse で JUnit テストとして実行できます。

コマンド ラインから実行するには:
* サンプルは、Maven を使用して実行することもできます。
* Maven を実行して、Azure Cosmos DB エンドポイントの資格情報を渡します。
    ```bash
    mvn test -DACCOUNT_HOST="https://REPLACE_THIS_WITH_YOURS.documents.azure.com:443/" -DACCOUNT_KEY="REPLACE_THIS_WITH_YOURS"
    ```

   > [!NOTE]
   > 各サンプルは自己完結型であり、自身をセットアップし、自身をクリーンアップします。 サンプルでは [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createcollection) の複数の呼び出しを発行します。 これが行われるたびに、作成中のコレクションのパフォーマンス階層ごとに 1 時間の使用量に対するサブスクリプションが課金されます。 
   > 
   > 

## <a name="database-examples"></a>データベースのサンプル
[DatabaseCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java) ファイルは、次のタスクを実行する方法を示しています。

| タスク | API リファレンス |
| --- | --- |
| [データベースの作成と読み取り](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L64-L79) | [DocumentClient.createDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createdatabase)<br>[DocumentClient.readDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.readdatabase)<br>[Resource.setId](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._resource.setid) |
| [データベースの作成と削除](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L82-L93) | [DocumentClient.deleteDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.deletedatabase) |
| [データベースの作成とクエリ](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L96-L111) | [DocumentClient.queryDatabases](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.querydatabases) |

## <a name="collection-examples"></a>コレクションのサンプル
[CollectionCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) ファイルは、次のタスクを実行する方法を示しています。

| タスク | API リファレンス |
| --- | --- |
| [シングル パーティション コレクションの作成](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L74-L84) | [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createcollection) |
| [カスタムのマルチパーティション コレクションの作成](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L103-L155) | [DocumentCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_collection)<br>[PartitionKeyDefinition](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._partition_key_definition)<br>[RequestOptions](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._request_options) |
| [コレクションの削除](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L97-L99) | [DocumentClient.deleteCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.deletecollection) |

## <a name="document-examples"></a>ドキュメントのサンプル
[DocumentCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java) ファイルは、次のタスクを実行する方法を示しています。

| タスク | API リファレンス |
| --- | --- |
| [ドキュメントの作成、読み取り、削除](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java#L84-L122) | [DocumentClient.createDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createdocument)<br>[DocumentClient.readDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.readdocument)<br>[DocumentClient.deleteDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.deletedocument) |
| [プログラミング可能なドキュメント定義を使用したドキュメントの作成](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java#L126-L147) | [ドキュメント](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document)<br>[Resource.setId](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._resource.setid) |

## <a name="indexing-examples"></a>インデックス作成のサンプル
[CollectionCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) ファイルは、次のタスクを実行する方法を示しています。

| タスク | API リファレンス |
| --- | --- |
| [インデックスの作成とインデックス作成ポリシーの設定](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L125-L141) | [Index](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._index)<br>[IndexingPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._indexing_policy) |

インデックス作成の詳細については、「[Azure Cosmos DB インデックス作成ポリシー](indexing-policies.md)」をご覧ください。

## <a name="query-examples"></a>クエリのサンプル
[DocumentQuerySamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java) ファイルは、次のタスクを実行する方法を示しています。

| タスク | API リファレンス |
| --- | --- |
| [シンプルなクロス パーティション ドキュメント クエリの実行](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java#L108-L129) | [DocumentClient.queryDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.querydocuments)<br>[FeedOptions.setEnableCrossPartitionQuery](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_options.setenablecrosspartitionquery) |
| [ORDER BY クエリ](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java#L132-L154) | [FeedResponse<T>.getQueryIterator](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_response.getqueryiterator) |

クエリの記述について詳しくは、[Azure Cosmos DB 内の SQL クエリ](how-to-sql-query.md)に関するページをご覧ください。

## <a name="offer-examples"></a>プランのサンプル
[OfferCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) ファイルは、次のタスクを実行する方法を示しています。

| タスク | API リファレンス |
| --- | --- |
| [コレクションの作成とスループットの設定](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java#L76-L102) | [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createcollection)<br>[RequestOptions.setOfferThroughput ](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._request_options.setofferthroughput) |
| [コレクションの読み取りと関連付けられているプランの検索](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java#L108-L132) | [Offer.getContent](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._offer.getcontent)<br>[DocumentClient.replaceOffer](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.replaceoffer)<br>[DocumentClient.readCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.readcollection)<br>[DocumentClient.queryOffers](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.queryoffers) |

## <a name="partition-key-examples"></a>パーティション キーのサンプル
[SinglePartitionCollectionDocumentCrudSample](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java) ファイルは、次のタスクを実行する方法を示しています。

| タスク | API リファレンス |
| --- | --- |
| [シングル パーティション コレクションの作成](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java#L164-L207) | [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createcollection) |
| [シングル パーティション コレクションのスループット プランの変更](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java#L209-L223) | [DocumentClient.replaceOffer](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.replaceoffer) |

## <a name="stored-procedure-examples"></a>ストアド プロシージャのサンプル
[StoredProcedureSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java) ファイルは、次のタスクを実行する方法を示しています。

| タスク | API リファレンス |
| --- | --- |
| [ストアド プロシージャの作成](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L85-L118) | [StoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._stored_procedure)<br>[DocumentClient.createStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createstoredprocedure) |
| [引数を用いたストアド プロシージャの実行](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L121-L144) | [DocumentClient.executeStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.executestoredprocedure) |
| [オブジェクト引数を用いたストアド プロシージャの実行](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L147-L177) | [DocumentClient.executeStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.executestoredprocedure) |
