---
title: Azure MySQL のインデックス データ (プレビュー)
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search でフルテキスト検索を行うために、検索インデクサーを設定し、Azure MySQL の格納データにインデックスを付けます。
author: gmndrg
ms.author: gimondra
manager: nitinme
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/17/2021
ms.openlocfilehash: aaf8597887eb7d5f7f416fc2c9aa8731b5b8ce1e
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892990"
---
# <a name="index-data-from-azure-mysql"></a>Azure MySQL のインデックス データ

> [!IMPORTANT] 
> MySQL のサポートは、現在、[追加の使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)の下でパブリック プレビュー段階にあります。 この機能への[アクセスの要求](https://aka.ms/azure-cognitive-search/indexer-preview)を行い、アクセスが有効になったら、[REST API のプレビュー (2020-06-30-preview 以降)](search-api-preview.md) を使用してコンテンツのインデックスを作成します。 現在は SDK もポータルもサポートされていません。

MySQL 用の Azure Cognitive Search インデクサーは Azure で MySQL データベースをクロールし、検索可能なデータを抽出して、それに Azure Cognitive Search でインデックスを付けます。 インデクサーは MySQL データベースのすべての変更、アップロード、および削除を取り込み、これらの変更を検索インデックスに反映します。

Azure MySQL のインデクサーは、次のうち任意のクライアントを使用して設定できます。

* [Azure Portal](https://ms.portal.azure.com)
* Azure Cognitive Search [REST API](/rest/api/searchservice/Indexer-operations)
* Azure Cognitive Search [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexer)

この記事では REST API を使用します。 

## <a name="create-an-azure-mysql-indexer"></a>Azure MySQL インデクサーの作成

Azure で MySQL にインデックスを付けるには、次の手順に従います。

### <a name="step-1-create-a-data-source"></a>手順 1:データ ソースを作成する

データ ソースを作成するには、次の要求を送信します。

```http

    POST https://[search service name].search.windows.net/datasources?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    
    {   
        "name" : "[Data source name]"
        "description" : "[Description of MySQL data source]",
        "type" : "mysql",
        "credentials" : { 
            "connectionString" : 
                "Server=[MySQLServerName].MySQL.database.azure.com; Port=3306; Database=[DatabaseName]; Uid=[UserName]; Pwd=[Password]; SslMode=Preferred;" 
        },
        "container" : { 
            "name" : "[TableName]" 
        },
        "dataChangeDetectionPolicy" : { 
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "[HighWaterMarkColumn]"
        }
    }

```

### <a name="step-2-create-an-index"></a>手順 2:インデックスを作成する

まだない場合は、ターゲットの Azure Cognitive Search インデックスを作成します。

```http

    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
       "name": "[Index name]",
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
         "facetable": false
       }]
    }

```

### <a name="step-3-create-the-indexer"></a>手順 3: インデクサーを作成する

インデックスとデータ ソースが作成されたら、インデクサーを作成できます。

```http

    POST https://[search service name].search.windows.net/indexers?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    
    {
        "name" : "[Indexer name]"
        "description" : "[Description of MySQL indexer]",
        "dataSourceName" : "[Data source name]",
        "targetIndexName" : "[Index name]"
    }

```

## <a name="run-indexers-on-a-schedule"></a>スケジュールに従ったインデクサーの実行
スケジュールに従って定期的に実行するようにインデクサーを調整することもできます。 そのためには、インデクサーを作成または更新するときに、**schedule** プロパティを追加します。 次の例では、インデクサーを更新する PUT 要求を示します。

```http
    PUT https://[search service name].search.windows.net/indexers/[Indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin-key]

    {
        "dataSourceName" : "[Data source name]",
        "targetIndexName" : "[Index name]",
        "schedule" : { 
            "interval" : "PT10M", 
            "startTime" : "2021-01-01T00:00:00Z"
        }
    }
```

**interval** パラメーターは必須です。 interval は、連続する 2 つのインデクサー実行の開始の時間間隔を示します。 許可される最短の間隔は 5 分です。最長は 1 日です。 XSD "dayTimeDuration" 値 ([ISO 8601 期間](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)値の制限されたサブセット) として書式設定する必要があります。 使用されるパターンは、`P(nD)(T(nH)(nM))` です。 たとえば、15 分ごとの場合は `PT15M`、2 時間ごとの場合は `PT2H` です。

インデクサーのスケジュールの定義の詳細については、[Azure Cognitive Search のインデクサーのスケジュールを設定する方法](search-howto-schedule-indexers.md)に関する記事を参照してください。

## <a name="capture-new-changed-and-deleted-rows"></a>新しい行、変更された行、削除された行の取得

Azure Cognitive Search では **増分インデックス作成** を使用することで、インデクサーを実行するたびにテーブルまたはビュー全体のインデックスを再作成する必要を回避できます。

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>高基準値変更検出ポリシー

この変更検出ポリシーは、行が最後に更新されたときのバージョンまたは時刻を取得する "高基準" 列に依存します。 ビューを使う場合は、高基準ポリシーを使用する必要があります。 高基準列では、次の要件を満たす必要があります。

#### <a name="requirements"></a>必要条件 

* すべての挿入は列の値を指定します。
* 項目を更新すると、列の値も変更されます。
* この列の値は挿入または更新のたびに増加します。
* 次の WHERE 句および ORDER BY 句のあるクエリを効率的に実行できます。`WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`

#### <a name="usage"></a>使用法

高基準値ポリシーを使用するには、データ ソースを次のように作成または更新します。

```http
    {
        "name" : "[Data source name]",
        "type" : "mysql",
        "credentials" : { "connectionString" : "[connection string]" },
        "container" : { "name" : "[table or view name]" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
           "highWaterMarkColumnName" : "[last_updated column name]"
      }
    }
```

> [!WARNING]
> ソース テーブルの高基準列にインデックスがない場合、MySQL インデクサーが使用するクエリはタイムアウトになる可能性があります。具体的には、多くの行が含まれるテーブルに対してクエリを効率的に実行するには、`ORDER BY [High Water Mark Column]` 句にはインデックスが必要です。

### <a name="soft-delete-column-deletion-detection-policy"></a>論理削除列削除検出ポリシー
ソース テーブルから行が削除されると、おあそらく検索インデックスからも同様に行を削除する必要があります。 行がテーブルから物理的に削除されている場合、Azure Cognitive Search では、存在しなくなったレコードの存在を推論することはできません。  ただし、"論理削除" テクニックを使用すると、行をテーブルから削除せずに論理的に削除できます。 このテクニックでは、列をテーブルまたはビューに追加し、その列を使用して行を削除済みとしてマークします。

論理削除手法を使用する場合は、データ ソースを作成または更新するときに、次のような論理削除ポリシーを指定できます。

```http
    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }
```

**softDeleteMarkerValue** は、文字列にする必要があります。実際の値の文字列表現を使用してください。 たとえば、削除された行が値 1 でマークされている整数列がある場合は、`"1"` を使用します。 削除された行が BIT 列でブール型の true 値でマークされている場合は、文字列リテラル `True` または `true` を使用します。大文字/小文字は区別されません。

<a name="TypeMapping"></a>

## <a name="mapping-between-mysql-and-azure-cognitive-search-data-types"></a>MySQL と Azure Cognitive Search のデータ型の間のマッピング

> [!NOTE]
> プレビューでは geometry 型と BLOB がサポートされていません。

| MySQL のデータ型 | ターゲット インデックス フィールドに許可される型 |
| --- | --- |
| bool、boolean | Edm.Boolean、Edm.String |
| tinyint、smallint、mediumint、int、integer、year | Edm.Int32、Edm.Int64、Edm.String |
| bigint | Edm.Int64、Edm.String |
| float、double、real | Edm.Double、Edm.String |
| date、datetime、timestamp | Edm.DateTimeOffset、Edm.String |
| char、varchar、tinytext、mediumtext、text、longtext、enum、set、time | Edm.String |
| unsigned numerical data、serial、decimal、dec、bit、blob、binary、geometry | 該当なし |


## <a name="next-steps"></a>次のステップ

お疲れさまでした。 インデクサーを使用して、MySQL を Azure Cognitive Search と統合する方法についての説明は以上で終了です。

+ インデクサーの詳細については、「[Azure Cognitive Search のインデクサーの作成](search-howto-create-indexers.md)」を参照してください。
