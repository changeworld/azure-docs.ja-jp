---
title: Azure Cosmos DB データ ソースのインデックス作成 - Azure Search
description: Azure Cosmos DB のデータ ソースをクロールし、Azure Search のフルテキスト検索可能なインデックスにデータを取り込みます。 インデクサーにより、選択したデータ ソース (Azure Cosmos DB など) のデータ インジェストが自動化されます。
ms.date: 10/17/2018
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
robot: noindex
ms.custom: seodec2018
ms.openlocfilehash: 99f19f6595f2e3105c4b30a7c2e7ef1e296fad6e
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53724864"
---
# <a name="connecting-cosmos-db-with-azure-search-using-indexers"></a>インデクサーを使用した Cosmos DB と Azure Search の接続

この記事では、次のことについて説明します:

> [!div class="checklist"]
> * Azure Cosmos DB コレクションをデータ ソースとして使用する [Azure Search インデクサー](search-indexer-overview.md)を構成します。
> * JSON と互換性のあるデータ型を持つ検索インデックスを作成します。
> * インデクサーでオンデマンドおよび定期的なインデックス作成を構成します。
> * 基になるデータの変更に基づいてインデックスを増分更新します。

> [!NOTE]
> Azure Cosmos DB とは、次世代の DocumentDB です。 製品名が変更されていますが、下位互換性のために Azure Search インデクサーの `documentdb` 公文は Azure Search API とポータル ページの両方に依然として存在します。 インデクサーを構成するときには、この記事の説明に従って必ず `documentdb` 構文を指定します。

次のビデオでは、Azure Cosmos DB のプログラム マネージャーである Andrew Liu が、Azure Search インデックスを Azure Cosmos DB コンテナーに追加する方法を紹介しています。

>[!VIDEO https://www.youtube.com/embed/OyoYu1Wzk4w]

<a name="supportedAPIs"></a>
## <a name="supported-api-types"></a>サポートされる API の種類

Azure Cosmos DB ではさまざまなデータ モデルと API がサポートされていますが、Azure Search インデクサーの運用サポートは SQL API のみに拡張されます。 現在、MongoDB 用 Azure Cosmos DB API のサポートはパブリック プレビュー段階にあります。  

他の API のサポートは近日公開予定です。 どれを最初にサポートするかは、ユーザーの声 Web サイトでの投票によって決まります。是非ご協力ください。

* [Table API データ ソースのサポート](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab)
* [Graph API データ ソースのサポート](https://feedback.azure.com/forums/263029-azure-search/suggestions/13285011-add-graph-databases-to-your-data-sources-eg-neo4)
* [Apache Cassandra API データ ソースのサポート](https://feedback.azure.com/forums/263029-azure-search/suggestions/32857525-indexer-crawler-for-apache-cassandra-api-in-azu)

## <a name="prerequisites"></a>前提条件

Cosmos DB アカウントのほかに、[Azure Search サービス](search-create-service-portal.md)が必要です。 

ご自分の Cosmos DB アカウントで、コレクションですべてのドキュメントのインデックスを自動的に作成するかどうかを選択できます。 既定では、すべてのドキュメントのインデックスが自動的に作成されますが、自動インデックス作成を無効にすることができます。 自動インデックス作成が無効になっている場合、自己リンクまたはドキュメント ID を使用したクエリでのみドキュメントにアクセスできます。 Azure Search では、Azure Search によってインデックスが作成されるコレクションで、Cosmos DB の自動インデックス作成が有効化される必要があります。 

<a name="Concepts"></a>
## <a name="azure-search-indexer-concepts"></a>Azure Search インデクサーの概念

**データ ソース**は、インデックスを作成するデータ、資格情報、およびデータの変更を識別するためのポリシー (コレクション内の変更または削除されたドキュメントなど) を指定します。 データ ソースは、複数のインデクサーから使用できるように、独立したリソースとして定義します。

**インデクサー** は、データ ソースからターゲットの検索インデックスにデータが流れるしくみを説明します。 インデクサーは次の目的で使用します。

* 1 回限りのデータのコピーを実行して、インデックスを作成する。
* スケジュールに従ってデータ ソースの変更とインデックスを同期する。
* 必要に応じてインデックスのオンデマンド更新を呼び出す。

Azure Cosmos DB のインデクサーをセットアップするには、インデックス、データソースを作成したうえで、最後にインデクサーを作成する必要があります。 これらのオブジェクトは、[ポータル](search-import-data-portal.md)、[.NET SDK](/dotnet/api/microsoft.azure.search)、または [REST API](/rest/api/searchservice/) を使用して作成できます。 

この記事では、REST API を使用する方法について説明します。 ポータルを選択した場合は、[データのインポート ウィザード](search-import-data-portal.md)に従って、インデックスを含むこれらすべてのリソースを作成します。

> [!TIP]
> **データのインポート** ウィザードを Azure Cosmos DB ダッシュボードから起動して、そのデータ ソースのインデックス作成を簡略化できます。 左側のナビゲーションで、**[コレクション]** > **[Add Azure Search (Azure Search の追加)]** に移動します。

> [!NOTE] 
> 現時点では、Azure Portal または .NET SDK を使用して、**MongoDB** データ ソースを作成または編集することはできません。 ただし、ポータルでは MongoDB のインデクサーの実行履歴を監視**できます**。  

<a name="CreateDataSource"></a>
## <a name="step-1-create-a-data-source"></a>手順 1: データ ソースを作成する
データ ソースを作成するには、POST 要求を発行します。

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCollection", "query": null },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        }
    }

要求の本文には、次のフィールドを含むデータ ソースの定義が含まれている必要があります。

* **name**: データベースを表す名前を選択します。
* **type**: `documentdb`である必要があります。
* **credentials**:
  
  * **connectionString**: 必須。 次の形式で Azure Cosmos DB データベースへの接続情報を指定します。`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>` MongoDB コレクションの場合は、**ApiKind=MongoDb** を接続文字列に追加します。 `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`
  エンドポイント URL では、ポート番号の使用を避けてください。 ポート番号を含めると、Azure Search では、Azure Cosmos DB データベースのインデックスを作成できなくなります。
* **container**:
  
  * **name**: 必須。 インデックスを作成するデータベース コレクションの ID を指定します。
  * **query**: 省略可能。 任意の JSON ドキュメントを、Azure Search がインデックスを作成できるフラット スキーマにフラット化するクエリを指定できます。 MongoDB コレクションの場合、クエリはサポートされません。 
* **dataChangeDetectionPolicy**: 推奨。 「[変更されたドキュメントのインデックス作成](#DataChangeDetectionPolicy)」セクションを参照してください。
* **dataDeletionDetectionPolicy**: 省略可能。 「[削除されたドキュメントのインデックス作成](#DataDeletionDetectionPolicy)」セクションを参照してください。

### <a name="using-queries-to-shape-indexed-data"></a>インデックス作成するデータの処理にクエリを活用する
SQL クエリを指定すると、ネストされたプロパティや配列のフラット化、JSON プロパティのプロジェクション、インデックスを作成するデータのフィルター処理を行うことができます。 

> [!WARNING]
> **MongoDB** コレクションではカスタム クエリはサポートされていません。`container.query` パラメーターは null に設定するか、省略する必要があります。 カスタム クエリを使用する必要がある場合は、[ユーザーの声](https://feedback.azure.com/forums/263029-azure-search) Web サイトでお知らせください。

ドキュメントのサンプル:

    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "documentdb", "search"]
    }

フィルター処理クエリ:

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts

平坦化クエリ:

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
    
    
プロジェクション クエリ:

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts


配列を平坦化するクエリ:

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts

<a name="CreateIndex"></a>
## <a name="step-2-create-an-index"></a>手順 2: インデックスを作成する
ターゲットの Azure Search インデックスがまだない場合は、インデックスを作成します。 インデックスを作成するには、[Azure Portal UI](search-create-index-portal.md)、[Create Index REST API](/rest/api/searchservice/create-index) または [Index クラス](/dotnet/api/microsoft.azure.search.models.index)を使用します。

次の例では、ID と説明のフィールドを持つインデックスを作成します。

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
    Content-Type: application/json
    api-key: [Search service admin key]

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }

ターゲット インデックスのスキーマがソース JSON ドキュメントのスキーマまたはカスタムのクエリ プロジェクションの出力と互換性があることを確認します。

> [!NOTE]
> パーティション分割されたコレクションの場合、既定のドキュメント キーは Azure Cosmos DB の `_rid` プロパティですが、フィールド名の先頭にはアンダースコア文字を使用できないため、この名前は Azure Search によって `rid` に自動的に変更されます。 また、Azure Cosmos DB の `_rid` 値には、Azure Search キーでは無効な文字が含まれています。 そのため、`_rid` 値は Base64 でエンコードされます。
> 
> MongoDB コレクションの場合、`_id` プロパティ名は、Azure Search によって `doc_id` に自動的に変更されます。  

### <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>JSON データ型と Azure Search データ型間のマッピング
| JSON データ型 | 互換性のあるターゲット インデックス フィールドの型 |
| --- | --- |
| ブール値 |Edm.Boolean、Edm.String |
| 整数などの数値 |Edm.Int32、Edm.Int64、Edm.String |
| 浮動小数点などの数値 |Edm.Double、Edm.String |
| String |Edm.String |
| プリミティブ型の配列。例: ["a"、"b"、"c"] |Collection(Edm.String) |
| 日付などの文字列 |Edm.DateTimeOffset、Edm.String |
| GeoJSON オブジェクト。例: { "type": "Point", "coordinates": [long, lat] } |Edm.GeographyPoint |
| その他の JSON オブジェクト |該当なし |

<a name="CreateIndexer"></a>

## <a name="step-3-create-an-indexer"></a>手順 3: インデクサーの作成

インデックスとデータ ソースを作成したら、インデクサーを作成できます。

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mydocdbindexer",
      "dataSourceName" : "mydocdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

このインデクサーは 2 時間ごとに実行されます (スケジュールの間隔が "PT2H" に設定されています)。 インデクサーを 30 分ごとに実行するには、間隔を "PT30M" に設定します。 サポートされている最短の間隔は 5 分です。 スケジュールは省略可能です。省略した場合、インデクサーは作成時に一度だけ実行されます。 ただし、いつでもオンデマンドでインデクサーを実行できます。   

インデクサー作成 API の詳細については、「 [インデクサーの作成](https://docs.microsoft.com/rest/api/searchservice/create-indexer)」をご覧ください。

<a id="RunIndexer"></a>
### <a name="running-indexer-on-demand"></a>オンデマンドでインデクサーを実行する
スケジュールに従って定期的に実行されるだけでなく、オンデマンドでインデクサーを呼び出すこともできます。

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2017-11-11
    api-key: [Search service admin key]

> [!NOTE]
> API の実行で正しい応答があった場合、インデクサーの呼び出しはスケジュール済みですが、実際の処理は非同期的に実行されます。 

ポータル、または次に説明する Get Indexer Status API を使用すると、インデクサーの状態を監視できます。 

<a name="GetIndexerStatus"></a>
### <a name="getting-indexer-status"></a>インデクサーの状態を取得する
インデクサーの現在の状態と実行履歴を取得できます。

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2017-11-11
    api-key: [Search service admin key]

応答には、全体的なインデクサーの状態、最後の (または実行中の) インデクサー呼び出し、およびインデクサー呼び出しの最近の履歴が含まれています。

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

実行履歴には、最近完了した 50 件の実行内容が含まれ、時系列の逆の順に並べられます (そのため、最近の実行内容が応答の最初に表示されます)。

<a name="DataChangeDetectionPolicy"></a>
## <a name="indexing-changed-documents"></a>変更されたドキュメントのインデックス作成
データ変更の検出ポリシーの目的は、変更されたデータ項目を効率的に識別することです。 現在、唯一サポートされているポリシーは、次に示すように、Azure Cosmos DB によって指定される `_ts` (タイムスタンプ) プロパティを使用した `High Water Mark` ポリシーです。

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

インデクサーの優れたパフォーマンスを確保するのには、このポリシーを使用することが推奨されます。 

カスタム クエリを使用している場合、`_ts` プロパティはクエリによって投影されていることを確認します。

<a name="IncrementalProgress"></a>
### <a name="incremental-progress-and-custom-queries"></a>増分の進行状況とカスタム クエリ
インデックスの作成中に増分の進行状況を利用すると、一時的な障害や実行の制限時間によってインデクサーの実行が中断された場合、次の実行時に、最初からコレクション全体のインデックスを再作成するのではなく、中断したところからインデックスを作成することができます。 大規模なコレクションのインデックスを作成する場合、この機能は特に重要です。 

カスタム クエリを使用するときに増分の進行状況を有効にするには、クエリを使用して `_ts` 列で結果を並べ替えておきます。 こうすることで Azure Search が使用する定期的なチェックポイントが作成され、エラーが発生した場合に増分の進行状況を利用できます。   

ただし状況によっては、クエリに `ORDER BY [collection alias]._ts` 句が含まれる場合でも、クエリ結果が `_ts` で並べ替えられることを Azure Search で推論されない可能性があります。 `assumeOrderByHighWaterMarkColumn` 構成プロパティを使用して結果が並べ替えられるように Azure Search に指示することもできます。 このヒントを指定するには、次のようにインデクサーを作成または更新します。 

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 

<a name="DataDeletionDetectionPolicy"></a>
## <a name="indexing-deleted-documents"></a>削除されたドキュメントのインデックス作成
コレクションから行が削除されると、通常、検索インデックスからも同様に行を削除する必要があります。 データ削除の検出ポリシーの目的は、削除されたデータ項目を効率的に識別することです。 現在、唯一サポートされているポリシーは、`Soft Delete` ポリシー (削除されると何らかのフラグでマークされる) のみです。このポリシーは、次のように指定します。

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

カスタム クエリを使用している場合、`softDeleteColumnName` から参照されているプロパティはクエリによって投影されていることを確認します。

次の例では、ソフト削除ポリシーとともにデータ ソースを作成します。

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": { "name": "myDocDbCollectionId" },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }

## <a name="NextSteps"></a>次のステップ
お疲れさまでした。 インデクサーを使用して、Azure Cosmos DB を Azure Search と統合する方法についての説明は以上で終了です。

* Azure Cosmos DB の詳細については、[Azure Cosmos DB サービスのページ](https://azure.microsoft.com/services/cosmos-db/)を参照してください。
* Azure Search について詳しくは、[Search サービス ページ](https://azure.microsoft.com/services/search/)をご覧ください。
