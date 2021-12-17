---
title: Azure Cosmos DB のデータにインデックスを作成する
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search でフルテキスト検索を行うために、検索インデクサーを設定し、Azure Cosmos DB に格納されているデータにインデックスを付けます。 この記事では、SQL と MongoDB API プロトコルを使用してデータにインデックスを作成する方法について説明します。
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/14/2021
ms.openlocfilehash: 389ecc550fd2b9e0fa41b7437b47aa5b40af3712
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123450919"
---
# <a name="index-data-from-azure-cosmos-db-using-sql-or-mongodb-apis"></a>SQL または MongoDB API を使用して Azure Cosmos DB のデータにインデックスを作成する

> [!IMPORTANT] 
> SQL API は一般提供されています。
> MongoDB API のサポートは、現在、[追加の使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)の下でパブリック プレビュー段階にあります。 [アクセスの要求](https://aka.ms/azure-cognitive-search/indexer-preview)を行い、アクセスが有効になった後で、[REST API のプレビュー (2020-06-30-preview 以降)](search-api-preview.md) を使用してデータにアクセスします。 現時点でポータルによるサポートは一部のみにとどまります。また、.NET SDK によるサポートはありません。

この記事では、コンテンツを抽出して Azure Cognitive Search で検索できるようにするために Azure Cosmos DB [インデクサー](search-indexer-overview.md)を構成する方法を説明します。 このワークフローでは、Azure Cognitive Search インデックスを作成して、Azure Cosmos DB から抽出された既存のテキストと共に読み込みます。

用語が混乱を招く可能性があるため、[Azure Cosmos DB のインデックス作成](../cosmos-db/index-overview.md)と [Azure Cognitive Search のインデックス作成](search-what-is-an-index.md)はそれぞれのサービスに固有の異なる操作であることに注意してください。 Azure Cognitive Search のインデックス作成を開始するには、Azure Cosmos DB データベースが既に存在していて、データが格納されている必要があります。

Azure Cognitive Search の Cosmos DB インデクサーでは、次のプロトコルでアクセスされる [Azure Cosmos DB 項目](../cosmos-db/account-databases-containers-items.md#azure-cosmos-items)をクロールできます。

+ [SQL API](../cosmos-db/sql-query-getting-started.md) (一般提供開始済み) では、データ ソースとインデクサーの作成に[ポータル](#cosmos-indexer-portal)、[REST API](/rest/api/searchservice/indexer-operations)、[.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexer)、または別の Azure SDK を利用できます。

+ [MongoDB API (プレビュー)](../cosmos-db/mongodb-introduction.md) では、データ ソースとインデクサーの作成に[ポータル](#cosmos-indexer-portal)または [REST API バージョン 2020-06-30-Preview](search-api-preview.md) を作成できます。

## <a name="prerequisites"></a>前提条件

Azure Cognitive Search でサポートされるのは、[インデックス作成ポリシー](../cosmos-db/index-policy.md)が [[常に]](../cosmos-db/index-policy.md#indexing-mode) に設定されている Cosmos DB コレクションだけです。 [遅延] インデックス作成ポリシーを使用したコレクションのインデックス作成はお勧めできず、データが失われることがあります。 インデックス作成が無効になっているコレクションはサポートされません。

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>ポータルの使用

> [!Note]
> ポータルでは現在、SQL API と MongoDB API (プレビュー) をサポートしています。

Azure Cosmos DB 項目のインデックスを作成する最も簡単な方法は、[Azure portal](https://portal.azure.com/) 内のウィザードを使用することです。 コンテナー内のデータをサンプリングしてメタデータを解析することにより、Azure Cognitive Search の [**データのインポート**](search-import-data-portal.md)ウィザードでは、既定のインデックスを作成し、ソース フィールドをターゲット インデックス フィールドにマップし、1 回の操作でインデックスを読み込むことができます。 ソース データのサイズと複雑さによっては、数分で運用可能なフルテキスト検索インデックスを作成できます。

待機時間を短くし、帯域幅の課金を回避するために、Azure Cognitive Search と Azure Cosmos DB の両方に同じリージョンまたは場所を使用することをお勧めします。

### <a name="step-1---prepare-source-data"></a>ステップ 1 - ソース データの準備

Cosmos DB アカウント、SQL API または MongoDB API (プレビュー) にマップされた Azure Cosmos DB データベース、およびデータベース内のコンテンツが必要です。

Cosmos DB データベースにデータが含まれることを確認してください。 [データのインポート ウィザード](search-import-data-portal.md)では、メタデータが読み取られ、インデックス スキーマを推論するためにデータのサンプリングが実行されますが、Cosmos DB からもデータが読み込まれます。 このデータが見つからない場合、ウィザードは "データ ソースからインデックス スキーマを削除するときにエラーが発生しました:データソース 'emptycollection' がデータを返さなかったため、プロトタイプ インデックスを構築できませんでした" というエラーで停止します。

### <a name="step-2---start-import-data-wizard"></a>ステップ 2 - データのインポート ウィザードを開始する

Azure Cognitive Search サービス ページのコマンド バーから [ウィザードを開始](search-import-data-portal.md)できます。このほか、Cosmos DB SQL API に接続する場合には、Cosmos DB アカウントの左側のナビゲーション ペインにある **[設定]** セクションで **[Add Azure Cognitive Search]\(Azure Cognitive Search の追加\)** をクリックする方法も利用できます。

   :::image type="content" source="media/search-import-data-portal/import-data-cmd.png" alt-text="[データのインポート] コマンドのスクリーンショット" border="true":::

### <a name="step-3---set-the-data-source"></a>ステップ 3 - データ ソースを設定する

**データソース** のページでは、ソースが次のように指定された **Cosmos DB** になっている必要があります。

+ **[Name]** は データ ソースの名前です。 作成されたら、その他のワークロード用に選択できます。

+ **Cosmos DB アカウント** は、次のいずれかの形式にする必要があります。
    1. Cosmos DB からの `AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;` の形式のプライマリまたはセカンダリ接続文字列。
        + バージョン 3.2 およびバージョン 3.6 の場合、**MongoDB コレクション** は、Azure portal の Cosmos DB アカウントに `AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;ApiKind=MongoDb` の形式を使用します
    1.  アカウント キーを含まない `ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.DocumentDB/databaseAccounts/<your cosmos db account name>/;(ApiKind=[api-kind];)` の形式のマネージド ID 接続文字列。 この接続文字列形式を使用するには、[マネージド ID を使用して Cosmos DB データベースへのインデクサー接続を設定する](search-howto-managed-identities-cosmos-db.md)ための手順に従います。

+ **[Database]** は、アカウントからの既存のデータベースです。 

+ **[Collection]** はドキュメントのコンテナーです。 インポートが成功するためには、ドキュメントが存在している必要があります。 

+ **[Query]** は、すべてのドキュメントが必要な場合は空にすることができます。それ以外の場合は、ドキュメントのサブセットを選択するクエリを入力できます。 **[Query]** は SQL API の場合にのみ利用できます。

   ![Cosmos DB データ ソースの定義](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "Cosmos DB データ ソースの定義")

### <a name="step-4---skip-the-enrich-content-page-in-the-wizard"></a>ステップ 4 - ウィザードの [コンテンツのエンリッチ] ページをスキップする

コグニティブ スキル (またはエンリッチメント) の追加は、インポート要件ではありません。 特別に [AI エンリッチメントを追加する](cognitive-search-concept-intro.md)必要がない場合は、この手順を省略てきます。

手順をスキップするには、ページの下部にある [次へ] と [スキップ] の青いボタンをクリックします。

### <a name="step-5---set-index-attributes"></a>ステップ 5 - インデックスの属性を設定する

**[インデックス]** ページには、フィールドとデータ型およびインデックスの属性を設定するための一連のチェック ボックスが一覧表示されます。 このウィザードで、メタデータに基づいてソース データをサンプリングすることで、フィールドの一覧を生成できます。 

属性列の上部にあるチェック ボックスをクリックすることで、属性を一括選択できます。 クライアント アプリに返してフル検索処理の対象にする必要があるすべてのフィールドで、 **[取得可能]** と **[検索可能]** を選択します。 整数はフルテキスト検索もあいまい検索もできないことに注意してください (数値は逐語的に評価され、多くの場合フィルターで役立ちます)。

詳細については、[インデックス属性](/rest/api/searchservice/create-index#bkmk_indexAttrib)と[言語アナライザー](/rest/api/searchservice/language-support)の説明を参照してください。 

時間をかけて選択内容を確認します。 ウィザードを実行すると、物理データ構造が作成されて、すべてのオブジェクトを削除して再作成しない限り、これらのフィールドを編集することはできません。

   ![Cosmos DB インデックスの定義](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "Cosmos DB インデックスの定義")

### <a name="step-6---create-indexer"></a>ステップ 6 - インデクサーを作成する

完全に指定すると、ウィザードによって検索サービスに 3 つの異なるオブジェクトが作成されます。 データ ソース オブジェクトとインデックス オブジェクトは、Azure Cognitive Search サービスに名前付きリソースとして保存されます。 最後のステップでは、インデクサー オブジェクトが作成されます。 インデクサーに名前を付けると、インデクサーはスタンドアロン リソースとして存在することができ、同じウィザードのシーケンスで作成されるインデックス オブジェクトやデータ ソース オブジェクトとは独立して、スケジュールを設定したり管理したりできます。

インデクサーに慣れていないユーザーのために説明すると、"*インデクサー*" とは Azure Cognitive Search のリソースであり、外部データ ソースで検索可能なコンテンツのクロールを行います。 **データのインポート** ウィザードで出力されるインデクサーでは、Cosmos DB データ ソースがクロールされ、検索可能なコンテンツが抽出されて、Azure Cognitive Search のインデックスにインポートされます。

次のスクリーンショットは、既定のインデクサー構成を示しています。 インデクサーを 1 回だけ実行する場合は、 **[1 度]** に切り替えることができます。 **[送信]** をクリックしてウィザードを実行し、すべてのオブジェクトを作成します。 インデックスの作成はすぐに開始されます。

   ![Cosmos DB インデクサーの定義](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "Cosmos DB インデクサーの定義")

ポータルのページでデータのインポートを監視することができます。 進行状況の通知では、インデックス作成の状態と、アップロードされたドキュメントの数が示されます。 

インデックスの作成が完了したら、[Search エクスプローラー](search-explorer.md)を使用してインデックスのクエリを実行できます。

> [!NOTE]
> 期待どおりのデータが表示されない場合は、その他のフィールドにその他の属性を設定する必要があります。 今作成したインデックスとインデクサーを削除し、もう一度ウィザードの手順に従って、手順 5 でインデックス属性の選択内容を変更してください。 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>REST API の使用

REST API を使用して、Azure Cognitive Search のすべてのインデクサーに共通する 3 部構成のワークフロー (データ ソースを作成し、インデックスを作成して、インデクサーを作成する) で、Azure Cosmos DB データのインデックスを作成できます。 次のプロセスでは、Create Indexer 要求を送信すると、Cosmos DB からのデータ抽出が開始されます。

この記事の前の方で、[Azure Cosmos DB のインデックス作成](../cosmos-db/index-overview.md)と [Azure Cognitive Search のインデックス作成](search-what-is-an-index.md)が異なる操作であると説明しました。 Cosmos DB のインデックス作成では、既定ですべてのドキュメントのインデックスが自動的に作成されます。 インデックスの自動作成を無効にすると、ドキュメント ID を使用したクエリまたは自己リンクでのみドキュメントにアクセスできるようになります。 Azure Cognitive Search のインデックス作成では、Azure Cognitive Search によってインデックスが作成されるコレクションで、Cosmos DB の自動インデックス作成が有効になっている必要があります。

> [!WARNING]
> Azure Cosmos DB とは、次世代の DocumentDB です。 以前の API バージョン **2017-11-11** では、`documentdb` 構文を使用できました。 つまり、データ ソースの種類を `cosmosdb` または `documentdb` として指定することができました。 API バージョン **2019-05-06** 以降では、この記事で説明するように、Azure Cognitive Search API とポータルの両方で `cosmosdb` 構文のみがサポートされるようになりました。 つまり、Cosmos DB エンドポイントに接続する場合場合、データ ソースの種類は `cosmosdb` でなければなりません。

### <a name="step-1---assemble-inputs-for-the-request"></a>ステップ 1 - 要求に対する入力をアセンブルする

要求ごとに、Azure Cognitive Search サービス名と管理者キーを (POST ヘッダーに) 指定し、BLOB ストレージのストレージ アカウント名とキーを指定する必要があります。 [Postman](search-get-started-rest.md) または [Visual Studio Code](search-get-started-vs-code.md) を使用して、Azure Cognitive Search に HTTP 要求を送信できます。

要求で使用する次の 3 つの値をコピーします。

+ Azure Cognitive Search サービス名
+ Azure Cognitive Search の管理者キー
+ Cosmos DB の接続文字列

これらの値は、ポータルで調べることができます。

1. Azure Cognitive Search のポータル ページで、[概要] ページから Search サービスの URL をコピーします。

2. 左側のナビゲーション ウィンドウで **[キー]** をクリックし、プライマリまたはセカンダリのいずれかのキーをコピーします。

3. Cosmos ストレージ アカウントのポータル ページに切り替えます。 左側のナビゲーション ウィンドウで、 **[設定]** の **[キー]** をクリックします。 このページには、1 つの URI、2 組の接続文字列、および 2 組のキーが表示されます。 接続文字列のいずれかをメモ帳にコピーします。

### <a name="step-2---create-a-data-source"></a>ステップ 2 - データ ソースを作成する

**データ ソース** は、インデックスを作成するデータ、資格情報、およびデータの変更を識別するためのポリシー (コレクション内の変更または削除されたドキュメントなど) を指定します。 データ ソースは、複数のインデクサーから使用できるように、独立したリソースとして定義します。

データ ソースを作成するには、POST 要求を作成します。

```http

    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCollection", "query": null },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        }
    }
```

要求の本文には、次のフィールドを含むデータ ソースの定義が含まれている必要があります。

| フィールド   | 説明 |
|---------|-------------|
| **name** | 必須。 データ ソース オブジェクトを表す名前を選択します。 |
|**type**| 必須。 `cosmosdb`である必要があります。 |
|**credentials** | 必須。 Cosmos DB の接続文字列でなければなりません。<br/><br/>**SQL コレクション** の接続文字列の形式は次のとおりです: `AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<br/><br/>バージョン 3.2 およびバージョン 3.6 の場合、**MongoDB コレクション** は、接続文字列に `AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb` の形式を使用します<br/><br/>エンドポイント URL では、ポート番号の使用を避けてください。 ポート番号を含めると、Azure Cognitive Search では、Azure Cosmos DB データベースのインデックスを作成できなくなります。|
| **container** | 次の要素が含まれます。 <br/>**name**:必須。 インデックスを作成するデータベース コレクションの ID を指定します。<br/>**query**: 省略可能。 任意の JSON ドキュメントを、Azure Cognitive Search がインデックスを作成できるフラット スキーマにフラット化するクエリを指定できます。<br/>MongoDB API では現在、クエリがサポートされていません。 |
| **dataChangeDetectionPolicy** | 推奨。 「[変更されたドキュメントのインデックス作成](#DataChangeDetectionPolicy)」セクションを参照してください。|
|**dataDeletionDetectionPolicy** | 省略可能。 「[削除されたドキュメントのインデックス作成](#DataDeletionDetectionPolicy)」セクションを参照してください。|

### <a name="using-queries-to-shape-indexed-data"></a>インデックス作成するデータの処理にクエリを活用する
SQL クエリを指定すると、ネストされたプロパティや配列のフラット化、JSON プロパティのプロジェクション、インデックスを作成するデータのフィルター処理を行うことができます。 

> [!WARNING]
> **MongoDB API** については、カスタム クエリがサポートされていません。`container.query` パラメーターは null を設定するか、省略する必要があります。 

ドキュメントのサンプル:

```http
    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "cosmosdb", "search"]
    }
```

フィルター処理クエリ:

```sql
SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts
```

平坦化クエリ:

```sql
SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
```

プロジェクション クエリ:

```sql
SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
```

配列を平坦化するクエリ:

```sql
SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts
```

<a name="SelectDistinctQuery"></a>

#### <a name="distinct-and-group-by"></a>DISTINCT と GROUP BY

[DISTINCT キーワード](../cosmos-db/sql-query-keywords.md#distinct)または [GROUP BY 句](../cosmos-db/sql-query-group-by.md)を使用するクエリはサポートされていません。 Azure Cognitive Search は、クエリの結果を完全に列挙するために、[SQL クエリの改ページ](../cosmos-db/sql-query-pagination.md)に依存します。 DISTINCT キーワードと GROUP BY 句はどちらも、結果の改ページに使用される[継続トークン](../cosmos-db/sql-query-pagination.md#continuation-tokens)と互換性がありません。

サポートされていないクエリの例を以下に示します。
```sql
SELECT DISTINCT c.id, c.userId, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts

SELECT DISTINCT VALUE c.name FROM c ORDER BY c.name

SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup FROM Food f GROUP BY f.foodGroup
```

Cosmos DB では、[ORDER BY 句を使用した DISTINCT キーワードによる SQL クエリの改ページ](../cosmos-db/sql-query-pagination.md#continuation-tokens)をサポートするための回避策がありますが、Azure Cognitive Search と互換性がありません。 クエリでは 1 つの JSON 値が返されるのに対し、Azure Cognitive Search は JSON オブジェクトを受け取る必要があります。

```sql
-- The following query returns a single JSON value and isn't supported by Azure Cognitive Search
SELECT DISTINCT VALUE c.name FROM c ORDER BY c.name
```

### <a name="step-3---create-a-target-search-index"></a>ステップ 3 - ターゲット検索インデックスを作成する 

まだない場合は、[ターゲットの Azure Cognitive Search インデックスを作成](/rest/api/searchservice/create-index)します。 次の例では、ID と説明のフィールドを持つインデックスを作成します。

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
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
         "searchable": true,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }
```

ターゲット インデックスのスキーマがソース JSON ドキュメントのスキーマまたはカスタムのクエリ プロジェクションの出力と互換性があることを確認します。

> [!NOTE]
> パーティション分割されたコレクションの場合、既定のドキュメント キーは Azure Cosmos DB の `_rid` プロパティですが、フィールド名の先頭にはアンダースコア文字を使用できないため、この名前は Azure Cognitive Search によって `rid` に自動的に変更されます。 また、Azure Cosmos DB の `_rid` 値には、Azure Cognitive Search キーでは無効な文字が含まれています。 そのため、`_rid` 値は Base64 でエンコードされます。
> 
> MongoDB コレクションの場合、`_id` プロパティ名は、Azure Cognitive Search によって `id` に自動的に変更されます。  

### <a name="mapping-between-json-data-types-and-azure-cognitive-search-data-types"></a>JSON データ型と Azure Cognitive Search データ型間のマッピング

| JSON データ型 | 互換性のあるターゲット インデックス フィールドの型 |
| --- | --- |
| Bool |Edm.Boolean、Edm.String |
| 整数などの数値 |Edm.Int32、Edm.Int64、Edm.String |
| 浮動小数点などの数値 |Edm.Double、Edm.String |
| String |Edm.String |
| プリミティブ型の配列。例: ["a"、"b"、"c"] |Collection(Edm.String) |
| 日付などの文字列 |Edm.DateTimeOffset、Edm.String |
| GeoJSON オブジェクト。例: { "type": "Point", "coordinates": [long, lat] } |Edm.GeographyPoint |
| その他の JSON オブジェクト |該当なし |

### <a name="step-4---configure-and-run-the-indexer"></a>ステップ 4 - インデクサーを構成して実行する

インデックスとデータ ソースを作成したら、インデクサーを作成できます。

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mycosmosdbindexer",
      "dataSourceName" : "mycosmosdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }
```

このインデクサーは 2 時間ごとに実行されます (スケジュールの間隔が "PT2H" に設定されています)。 インデクサーを 30 分ごとに実行するには、間隔を "PT30M" に設定します。 サポートされている最短の間隔は 5 分です。 スケジュールは省略可能です。省略した場合、インデクサーは作成時に一度だけ実行されます。 ただし、いつでもオンデマンドでインデクサーを実行できます。   

インデクサー作成 API の詳細については、「 [インデクサーの作成](/rest/api/searchservice/create-indexer)」をご覧ください。

インデクサーのスケジュールの定義の詳細については、[Azure Cognitive Search のインデクサーのスケジュールを設定する方法](search-howto-schedule-indexers.md)に関する記事を参照してください。

## <a name="use-net"></a>.NET の使用

一般提供されている .NET SDK では、一般提供されている REST API による完全なパリティを保持しています。 前の REST API セクションを確認し、概念、ワークフロー、要件を理解することをお勧めします。 その後は、次の .NET API リファレンス ドキュメントを参照して、マネージド コードで JSON インデクサーを実装できます。

+ [azure.search.documents.indexes.models.searchindexerdatasourceconnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection)
+ [azure.search.documents.indexes.models.searchindexerdatasourcetype](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype)
+ [azure.search.documents.indexes.models.searchindex](/dotnet/api/azure.search.documents.indexes.models.searchindex)
+ [azure.search.documents.indexes.models.searchindexer](/dotnet/api/azure.search.documents.indexes.models.searchindexer)

<a name="DataChangeDetectionPolicy"></a>

## <a name="indexing-changed-documents"></a>変更されたドキュメントのインデックス作成

データ変更の検出ポリシーの目的は、変更されたデータ項目を効率的に識別することです。 現在、唯一サポートされているポリシーは、次に示すように、Azure Cosmos DB によって指定される `_ts` (タイムスタンプ) プロパティを使用した [`HighWaterMarkChangeDetectionPolicy`](/dotnet/api/azure.search.documents.indexes.models.highwatermarkchangedetectionpolicy) です。

```http
    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }
```

インデクサーの優れたパフォーマンスを確保するのには、このポリシーを使用することが推奨されます。 

カスタム クエリを使用している場合、`_ts` プロパティはクエリによって投影されていることを確認します。

<a name="IncrementalProgress"></a>

### <a name="incremental-progress-and-custom-queries"></a>増分の進行状況とカスタム クエリ

インデックスの作成中に増分の進行状況を利用すると、一時的な障害や実行の制限時間によってインデクサーの実行が中断された場合、次の実行時に、最初からコレクション全体のインデックスを再作成するのではなく、中断したところからインデックスを作成することができます。 大規模なコレクションのインデックスを作成する場合、この機能は特に重要です。 

カスタム クエリを使用するときに増分の進行状況を有効にするには、クエリを使用して `_ts` 列で結果を並べ替えておきます。 こうすることで Azure Cognitive Search が使用する定期的なチェックポイントが作成され、エラーが発生した場合に増分の進行状況を利用できます。   

ただし状況によっては、クエリに `ORDER BY [collection alias]._ts` 句が含まれる場合でも、クエリ結果が `_ts` で並べ替えられることを Azure Cognitive Search で推論されない可能性があります。 `assumeOrderByHighWaterMarkColumn` 構成プロパティを使用して結果が並べ替えられるように Azure Cognitive Search に指示することもできます。 このヒントを指定するには、次のようにインデクサーを作成または更新します。 

```http
    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 
```

<a name="DataDeletionDetectionPolicy"></a>

## <a name="indexing-deleted-documents"></a>削除されたドキュメントのインデックス作成

コレクションから行が削除されると、通常、検索インデックスからも同様に行を削除する必要があります。 データ削除の検出ポリシーの目的は、削除されたデータ項目を効率的に識別することです。 現在、唯一サポートされているポリシーは、`Soft Delete` ポリシー (削除されると何らかのフラグでマークされる) のみです。このポリシーは、次のように指定します。

```http
    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }
```

カスタム クエリを使用している場合、`softDeleteColumnName` から参照されているプロパティはクエリによって投影されていることを確認します。

次の例では、ソフト削除ポリシーとともにデータ ソースを作成します。

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCosmosDbCollectionId" },
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
```

## <a name="next-steps"></a><a name="NextSteps"></a>次のステップ

お疲れさまでした。 インデクサーを使用して、Azure Cosmos DB を Azure Cognitive Search と統合する方法についての説明は以上で終了です。

* Azure Cosmos DB の詳細については、[Azure Cosmos DB サービスのページ](https://azure.microsoft.com/services/cosmos-db/)を参照してください。
* Azure Cognitive Search について詳しくは、[Search サービス ページ](https://azure.microsoft.com/services/search/)をご覧ください。