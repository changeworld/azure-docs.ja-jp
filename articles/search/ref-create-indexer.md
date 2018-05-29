---
title: インデクサーを作成する (Azure Search サービス REST api-version=2017-11-11-Preview)
description: プレビュー API では、インデクサーが 拡張されて、Azure Search インデックス内のフィールドにエンリッチメント出力をマップするために使用される outputFieldMapping パラメーターが含められています。
services: search
manager: pablocas
author: luiscabrer
ms.author: luisca
ms.date: 05/01/2018
ms.prod: azure
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: language-reference
ms.openlocfilehash: d47b14342caf0312e052584d20f1a9c86ca29cad
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786921"
---
# <a name="create-indexer-azure-search-service-rest-api-version2017-11-11-preview"></a>インデクサーを作成する (Azure Search サービス REST api-version=2017-11-11-Preview)

この API リファレンスは、インデックス作成に対するコクニティブ検索の強化に対応したドキュメントのプレビュー固有のバージョンです。

[一般公開](https://docs.microsoft.com/rest/api/searchservice/create-indexer)バージョンと同様に、HTTP POST 要求を使用して、Azure Search サービス内に新しいインデクサーを作成できます。 

```http
POST https://[service name].search.windows.net/indexers?api-version=[api-version]  
    Content-Type: application/json  
    api-key: [admin key]  
```  
**api-key** は (クエリ キーではなく) 管理者キーにする必要があります。 キーの詳細については、[Azure Search のセキュリティ](search-security-overview.md)に関するページの、認証についてのセクションをご覧ください。 「[ポータルで Azure Search サービスを作成する](search-create-service-portal.md)」では、要求で使用されるサービスの URL とキーのプロパティを取得する方法を説明しています。

または、PUT を使用し、URI でデータ ソース名を指定できます。 データ ソースが存在しない場合、作成されます。  

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]  
```  
**api-version** は必須です。 現行バージョンは `2016-09-01`です。 詳細については、「[Azure Search の API バージョン](search-api-versions.md)」をご覧ください。

インデクサーの作成に関するデータ プラットフォーム固有のガイダンスについては、「[インデクサーの概要](search-indexer-overview.md)」から始めてください。そこに、[関連記事](search-indexer-overview.md#next-steps)の完全な一覧が記載されています。

> [!NOTE]  
>  許可されるインデクサーの最大数は価格レベルによって異なります。 無料サービスの場合、最大 3 つのインデクサーが許可されます。 Standard サービスの場合、50 のインデクサーが許可されます。 詳細については、「 [サービスの制限](search-limits-quotas-capacity.md) 」を参照してください。    

## <a name="request"></a>要求  
 要求の本文には、インデックス作成のためのデータ ソースとターゲット インデックスを指定するインデクサー定義と、任意でインデックス作成のスケジュールとパラメーターが含まれます。  

 要求ペイロードを構築するための構文は次のとおりです。 サンプル要求については、このトピックで後述します。  

```json
{   
    "name" : "Required for POST, optional for PUT. The name of the indexer",  
    "description" : "Optional. Anything you want, or null",  
    "dataSourceName" : "Required. The name of an existing data source",  
    "targetIndexName" : "Required. The name of an existing index",  
    "schedule" : { Optional. See Indexing Schedule below. },  
    "parameters" : { Optional. See Indexing Parameters below. },  
    "fieldMappings" : { Optional. See fieldMappings below. },
    "outputFieldMappings" : { Required for enrichment pipelines. See outputFieldMappings below. },
    "disabled" : Optional boolean value indicating whether the indexer is disabled. False by default.
}  
```

### <a name="indexer-schedule"></a>インデクサーのスケジュール  
インデクサーには、必要に応じてスケジュールを指定できます。 スケジュールが存在する場合、インデクサーはスケジュールに従って定期的に実行されます。 スケジューラは組み込まれており、外部のスケジューラを使用することはできません。 **Schedule** には次の属性があります。 

-   **interval**: 必須。 インデクサーが実行される間隔または期間を指定する時間の値。 許可される最短の間隔は 5 分です。最長は 1 日です。 XSD "dayTimeDuration" 値 ([ISO 8601 期間](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)値の制限されたサブセット) として書式設定する必要があります。 使用されるパターンは `"P[nD][T[nH][nM]]".` です。たとえば、15 分ごとの場合は `PT15M`、2 分ごとの場合は `PT2H` です。  

-   **startTime** - 省略可能。 インデクサーの実行を開始する UTC 日時。  

### <a name="indexer-parameters"></a>インデクサーのパラメーター  
 インデクサーには任意でパラメーターを指定し、インデクサーの動作を変えることができます。 以下に示すすべてのパラメーターは省略可能です。  

-   **maxFailedItems**: インデックス作成失敗がこの項目数に到達すると、そのインデクサー実行は失敗であるとみなされます。 既定値は 0 です。 失敗した項目に関する情報は [インデクサー状態の取得 &#40;Azure Search サービス REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) 操作で返されます。  

-   **maxFailedItemsPerBatch**: バッチごとのインデックス作成失敗がこの項目数に到達すると、そのインデクサー実行は失敗であるとみなされます。 既定値は 0 です。  

-   **batchSize:** パフォーマンスを改善する目的で、データ ソースから読み込まれ、1 つのバッチとしてインデックスが作成されるアイテムの数を指定します。 既定値はデータ ソースの種類によって異なります。Azure SQL と Azure Cosmos DB の場合は 1000 であり、Azure Blob Storage の場合は 10 です。

### <a name="field-mapping-parameters"></a>フィールド マッピングのパラメーター

インデクサーの定義には、Azure Search インデックス内のターゲット フィールドにソース フィールドをマッピングするための、フィールドの関連付けが含まれます。 コンテンツの転送が直接のパスとエンリッチメントされるパスのどちらに従うかに応じて、2 種類の関連付けがあります。

+ **fieldMappings** は省略可能で、ソースとターゲットのフィールド名が一致しない場合、または関数を指定する場合に適用されます。
+ **outputFieldMappings** は、[エンリッチメント パイプライン](cognitive-search-concept-intro.md)を構築する場合は必須です。 エンリッチメント パイプラインでは、出力フィールドは、エンリッチメント処理中に定義される構造です。 たとえば、出力フィールドは、エンリッチメント時にソース ドキュメント内の 2 つの別個のフィールドから構築される複合構造である場合があります。 

#### <a name="fieldmappings"></a>fieldMappings

次の例で、ソース テーブルに `_id` というフィールドがあるとします。 Azure Search では下線で始まるフィールド名は許可されません。そのため、このフィールドの名前を変更する必要があります。 次のようにインデクサーの `fieldMappings` プロパティを使用すれば名前を変更できます。

```json
"fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
```

複数のフィールド マッピングを指定できます。

```json
"fieldMappings" : [
    { "sourceFieldName" : "_id", "targetFieldName" : "id" },
    { "sourceFieldName" : "_timestamp", "targetFieldName" : "timestamp" }
]
```

ソースとターゲットの両方のフィールド名で大文字と小文字が区別されます。

フィールド マッピングが役立つシナリオについては、「[検索インデクサーのフィールド マッピング](https://docs.microsoft.com/azure/search/search-indexer-field-mappings)」をご覧ください。

#### <a name="outputfieldmappings"></a>outputFieldMappings

スキルセットがインデクサーにバインドされるコグニティブ検索のシナリオでは、`outputFieldMappings` を追加して、コンテンツを提供するエンリッチメント ステップの出力を、インデックス内の検索可能なフィールドに関連付ける必要があります。

```json
  "outputFieldMappings" : [
        {
          "sourceFieldName" : "/document/organizations", 
          "targetFieldName" : "organizations"
        },
        {
          "sourceFieldName" : "/document/pages/*/keyPhrases/*", 
          "targetFieldName" : "keyphrases"
        },
        {
            "sourceFieldName": "/document/languageCode",
            "targetFieldName": "language",
            "mappingFunction": null
        }      
   ],
```

<a name="FieldMappingFunctions"></a>

#### <a name="field-mapping-functions"></a>フィールド マッピング関数

フィールド マッピングは、*フィールド マッピング関数*を使用してソース フィールドの値を変換するためにも利用できます。 たとえば、任意の文字列値を base64 でエンコードできるため、その値を、ドキュメントのキー フィールドに格納するために使用できます。

フィールド マッピング関数をいつ、どのように使用するかの詳細については、「[フィールド マッピング関数](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#field-mapping-functions)」をご覧ください。

### <a name="request-body-examples"></a>要求本文の例  
 次の例では、UTC 2015 年 1 月 1 日から 1 時間ごとに実行されるスケジュールで `ordersds` データ ソースによって参照されるテーブルのデータを `orders` インデックスにコピーするインデクサーを作成します。 インデックス作成に失敗した項目が各バッチで 5 つ以下で、合計で 10 以下あれば、各インデクサーの呼び出しは成功となります。  

```json
{
    "name" : "myindexer",  
    "description" : "a cool indexer",  
    "dataSourceName" : "ordersds",  
    "targetIndexName" : "orders",  
    "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },  
    "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5 }  
}
```

## <a name="response"></a>Response  
 要求成功の場合: 201 作成されました。  

## <a name="see-also"></a>関連項目

+ [コグニティブ検索の概要](cognitive-search-concept-intro.md)
+ [クイック スタート: コグニティブ検索を使ってみる](cognitive-search-quickstart-blob.md)
+ [フィールドをマップする方法](cognitive-search-output-field-mapping.md)
