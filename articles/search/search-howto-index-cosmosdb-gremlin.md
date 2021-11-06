---
title: Gremlin API からのデータのインデックスを付ける (プレビュー)
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search でのフルテキスト検索用に Gremlin API コンテンツのインデックス付けを自動化するように Azure Cosmos DB インデクサーを設定します。
author: gmndrg
ms.author: gimondra
manager: nitinme
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/11/2021
ms.openlocfilehash: a0876ef39d74403ad6ae5d88fbac12aeed543e27
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893806"
---
# <a name="index-data-using-azure-cosmos-db-gremlin-api"></a>Azure Cosmos DB Gremlin API を使用したデータのインデックス作成

> [!IMPORTANT]
> Cosmos DB Gremlin API インデクサーは、現在、[追加の使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)の下でパブリック プレビュー段階にあります。 この機能への[アクセスの要求](https://aka.ms/azure-cognitive-search/indexer-preview)を行い、アクセスが有効になったら、[プレビュー REST API (2020-06-30-preview 以降)](search-api-preview.md) を使用してコンテンツのインデックスを作成します。 現時点で、ポータルによるサポートは制限されており、.NET SDK によるサポートはありません。

この記事では、コンテンツを抽出して Azure Cognitive Search で検索できるようにするために Azure Cosmos DB インデクサーを構成する方法を説明します。 このワークフローでは、Azure Cognitive Search で検索インデックスを作成し、Gremlin API を使用して Azure Cosmos DB から抽出された既存のコンテンツと共にそれを読み込みます。

用語が混乱を招く可能性があるため、[Azure Cosmos DB のインデックス作成](../cosmos-db/index-overview.md)と [Azure Cognitive Search のインデックス作成](search-what-is-an-index.md)はそれぞれのサービスに固有の異なる操作であることに注意してください。 Azure Cognitive Search のインデックス作成を開始するには、Azure Cosmos DB データベースが既に存在していて、データが格納されている必要があります。

## <a name="prerequisites"></a>前提条件

Azure Cognitive Search で Gremlin API を使用して Cosmos DB のデータにインデックスを付けるには、[Cosmos DB 独自のインデックス作成](../cosmos-db/index-overview.md)も有効にして、[[Consistent]\(一致\)](../cosmos-db/index-policy.md#indexing-mode) に設定する必要があります。 これは Cosmos DB の既定の構成です。 Azure Cognitive Search のインデックス作成は、Cosmos DB のインデックス作成が事前に有効になっていないと機能しません。

## <a name="get-started"></a>はじめに

[プレビューの REST API](/rest/api/searchservice/index-preview) を使用して、Azure Cognitive Search のすべてのインデクサーに共通する 3 部構成のワークフロー (データ ソースの作成、インデックスの作成、インデクサーの作成) に従うことで、Gremlin API を介して使用可能な Azure Cosmos DB データにインデックスを付けることができます。 下のプロセスでは、インデクサーの作成要求を送信すると、Cosmos DB からのデータ抽出が開始されます。

既定では、Azure Cognitive Search Cosmos DB Gremlin API インデクサーによって、グラフ内のすべての頂点が、インデックス内のドキュメントになります。 エッジは無視されます。 また、エッジのインデックスのみ作成するようにクエリを設定することもできます。

### <a name="step-1---assemble-inputs-for-the-request"></a>ステップ 1 - 要求に対する入力をアセンブルする

要求ごとに、Azure Cognitive Search のサービス名と管理者キーを (POST ヘッダーに) 指定する必要があります。 [Postman](./search-get-started-rest.md) または REST API クライアントを使用して、HTTPS 要求を Azure Cognitive Search に送信できます。

要求で使用する次の値をコピーして保存します。

+ Azure Cognitive Search サービス名
+ Azure Cognitive Search の管理者キー
+ Cosmos DB Gremlin API 接続文字列

これらの値は Azure portal で見つけることができます。

1. Azure Cognitive Search のポータル ページで、[概要] ページから Search サービスの URL をコピーします。

2. 左側のナビゲーション ウィンドウで **[キー]** をクリックし、プライマリまたはセカンダリのいずれかのキーをコピーします。

3. Cosmos DB アカウントのポータル ページに切り替えます。 左側のナビゲーション ウィンドウで、 **[設定]** の **[キー]** をクリックします。 このページには、1 つの URI、2 組の接続文字列、および 2 組のキーが表示されます。 接続文字列のいずれかをメモ帳にコピーします。

### <a name="step-2---create-a-data-source"></a>ステップ 2 - データ ソースを作成する

**データ ソース** は、インデックスを作成するデータ、資格情報、およびデータの変更を識別するためのポリシー (コレクション内の変更または削除されたドキュメントなど) を指定します。 データ ソースは、複数のインデクサーから使用できるように、独立したリソースとして定義します。

データ ソースを作成するには、POST 要求を作成します。

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [Search service admin key]
    
    {
        "name": "mycosmosdbgremlindatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;ApiKind=Gremlin;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myGraphId", "query": null }
    }
```

要求の本文には、次のフィールドを含むデータ ソースの定義が含まれている必要があります。

| フィールド   | 説明 |
|---------|-------------|
| **name** | 必須。 データ ソース オブジェクトを表す名前を選択します。 |
|**type**| 必須。 `cosmosdb`である必要があります。 |
|**credentials** | 必須。 **connectionString** には、AccountEndpoint、AccountKey、ApiKind、Database を含める必要があります。 ApiKind は **Gremlin** です。</br></br>次に例を示します。<br/>`AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=Gremlin`<br/><br/>AccountEndpoint では `*.documents.azure.com` エンドポイントを使用する必要があります。
| **container** | 次の要素が含まれます。 <br/>**name**:必須。 グラフの ID を指定します。<br/>**query**: 省略可能。 既定では、 `g.V()`です。 エッジのインデックスを作成するには、クエリを `g.E()` に設定します。 |
| **dataChangeDetectionPolicy** | 高基準列として `_ts` を使用すると、増分の進行状況が既定で有効になります。 |
|**dataDeletionDetectionPolicy** | 省略可能。 「[削除されたドキュメントのインデックス作成](#DataDeletionDetectionPolicy)」セクションを参照してください。|

### <a name="step-3---create-a-target-search-index"></a>ステップ 3 - ターゲット検索インデックスを作成する

まだない場合は、[ターゲットの Azure Cognitive Search インデックスを作成](/rest/api/searchservice/create-index)します。 次の例では、ID、ラベル、および説明の各フィールドがあるインデックスを作成します。

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [Search service admin key]

    {
       "name": "mysearchindex",
       "fields": [
        {
            "name": "rid",
            "type": "Edm.String",
            "facetable": false,
            "filterable": false,
            "key": true,
            "retrievable": true,
            "searchable": true,
            "sortable": false,
            "analyzer": "standard.lucene",
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "synonymMaps": [],
            "fields": []
        },{
            "name": "id",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "retrievable": true,
            "sortable": false,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": "standard.lucene",
            "synonymMaps": []
        }, {
            "name": "label",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "retrievable": true,
            "sortable": false,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": "standard.lucene",
            "synonymMaps": []
       }]
     }
```

ターゲット インデックスのスキーマがグラフと互換性があることを確認してください。

パーティション分割されたコレクションの場合、既定のドキュメント キーは Azure Cosmos DB の `_rid` プロパティですが、フィールド名の先頭にはアンダースコア文字を使用できないため、この名前は Azure Cognitive Search によって `rid` に自動的に変更されます。 また、Azure Cosmos DB の `_rid` 値には、Azure Cognitive Search キーでは無効な文字が含まれています。 このため、`_rid` 値をドキュメント キーにする場合は、Base64 でエンコードする必要があります。

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
      "name": "mycosmosdbgremlinindexer",
      "description": "My Cosmos DB Gremlin API indexer",
      "dataSourceName": "mycosmosdbgremlindatasource",
      "targetIndexName": "mysearchindex"
    }
```

このインデクサーは、作成後に実行が開始され、一度だけ実行されます。 要求にオプションの schedule パラメーターを追加することで、スケジュールに基づいて実行されるようにインデクサーを設定できます。 インデクサーのスケジュールの定義の詳細については、[Azure Cognitive Search のインデクサーのスケジュールを設定する方法](search-howto-schedule-indexers.md)に関する記事を参照してください。

インデクサー作成 API の詳細については、「[インデクサーの作成](/rest/api/searchservice/create-indexer)」をご覧ください。

<a name="DataDeletionDetectionPolicy"></a>

## <a name="indexing-deleted-documents"></a>削除されたドキュメントのインデックス作成

グラフ データが削除された場合、対応するドキュメントも検索インデックスから削除することをお勧めします。 データ削除検出ポリシーの目的は、削除対象のデータ項目を効率的に識別し、ドキュメント全体をインデックスから削除することです。 データ削除検出ポリシーは、一部のドキュメント情報を削除することを目的としたものではありません。 現在、唯一サポートされているポリシーは、`Soft Delete` ポリシー (削除されると何らかのフラグでマークされる) のみです。このポリシーは、次のように指定します。

```http
    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }
```

次の例では、ソフト削除ポリシーとともにデータ ソースを作成します。

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [Search service admin key]
    
    {
        "name": "mycosmosdbgremlindatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;ApiKind=Gremlin;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCollection" },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "`_ts`"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }
```

<a name="MappingGraphData"></a>

## <a name="mapping-graph-data-to-a-search-index"></a>検索インデックスへのグラフ データのマッピング

Cosmos DB Gremlin API インデクサーによって、一部のグラフ データが自動的にマップされます。

1. `_rid` が存在する場合、インデクサーによってインデックス内の `rid` フィールドにマップされます。 `_rid` には Azure Cognitive Search ドキュメント キーにおいて無効な文字が含まれていることがあるため、インデックスのキーとして `rid` 値を使用する場合は、キーを Base64 でエンコードする必要があります。

1. `_id` が存在する場合、インデクサーによってインデックス内の `id` フィールドにマップされます。

1. Gremlin API を使用して Cosmos DB データベースに対してクエリを実行すると、各プロパティの JSON 出力には `id` と `value` が含まれている場合があります。 プロパティの `value` が存在する場合、Azure Cognitive Search Cosmos DB インデクサーによって、プロパティと同じ名前を持つ検索インデックスのフィールドに自動的にマップされます。 次の例では、450 は検索インデックスの `pages` フィールドにマップされます。

```http
    {
        "id": "Cookbook",
        "label": "book",
        "type": "vertex",
        "properties": {
          "pages": [
            {
              "id": "48cf6285-a145-42c8-a0aa-d39079277b71",
              "value": "450"
            }
          ]
        }
    }
```

クエリ出力をインデックス内のフィールドにマップするために、[出力フィールドのマッピング](cognitive-search-output-field-mapping.md)を使用することが必要な場合があります。 カスタム クエリでは複雑なデータを使用する場合が多いため、[フィールド マッピング](search-indexer-field-mappings.md)ではなく、出力フィールド マッピングを使用することが望ましい場合があります。

たとえば、クエリによって次の出力が生成されるとします。

```json
    [
      {
        "vertex": {
          "id": "Cookbook",
          "label": "book",
          "type": "vertex",
          "properties": {
            "pages": [
              {
                "id": "48cf6085-a211-42d8-a8ea-d38642987a71",
                "value": "450"
              }
            ],
          }
        },
        "written_by": [
          {
            "yearStarted": "2017"
          }
        ]
      }
    ]
```

上記の JSON の `pages` の値を、インデックス内の `totalpages` フィールドにマップする場合、次の[出力フィールドのマッピング](cognitive-search-output-field-mapping.md)をインデクサーの定義に追加できます。

```json
    ... // rest of indexer definition 
    "outputFieldMappings": [
        {
          "sourceFieldName": "/document/vertex/pages",
          "targetFieldName": "totalpages"
        }
    ]
```

出力フィールドのマッピングが `/document` で始まること、および JSON のプロパティ キーへの参照が含まれないことに注意してください。 これは、インデクサーによって、グラフ データが取り込まれるとき、各ドキュメントが `/document` ノードの下に配置されるためです。またインデクサーによって、単に `pages` を参照するだけで `pages` の値を自動的に参照できます。`pages` の配列内の最初のオブジェクトを参照する必要はありません。

## <a name="next-steps"></a>次の手順

+ Azure Cosmos DB Gremlin API について詳しくは、[Azure Cosmos DB の概要: Gremlin API]() /cosmos-db/graph-introduction.md) に関するページをご覧ください。

+ Azure Cognitive Search のシナリオと価格の詳細については、[azure.microsoft.com の Search Service に関するページ](https://azure.microsoft.com/services/search/)を参照してください。