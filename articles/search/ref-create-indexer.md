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
ms.openlocfilehash: 595502ecf0a78491c73800e8077de65707c7a486
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365187"
---
# <a name="create-indexer-azure-search-service-rest-api-version2017-11-11-preview"></a>インデクサーを作成する (Azure Search サービス REST api-version=2017-11-11-Preview)

この API リファレンスは、プレビューに特有のバージョンのドキュメントです。また、インデクサーの作成 API に対する[コグニティブ検索](cognitive-search-concept-intro.md)の要素も含まれます。 [一般公開](https://docs.microsoft.com/rest/api/searchservice/create-indexer)バージョンと同様に、HTTP POST 要求を使用して、Azure Search サービス内に新しいインデクサーを作成できます。 

```http
POST https://[service name].search.windows.net/indexers?api-version=2017-11-11-Preview
    Content-Type: application/json  
    api-key: [admin key]  
```  
**api-key** は (クエリ キーではなく) 管理者キーにする必要があります。 キーの詳細については、[Azure Search のセキュリティ](search-security-overview.md)に関するページの、認証についてのセクションをご覧ください。 「[ポータルで Azure Search サービスを作成する](search-create-service-portal.md)」では、要求で使用されるサービスの URL とキーのプロパティを取得する方法を説明しています。

または、PUT を使用し、URI でデータ ソース名を指定できます。 データ ソースが存在しない場合、作成されます。  

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]  
```  
**api-version** は必須です。 現在一般的に使用可能なバージョンは `api-version=2017-11-11` ですが、コグニティブ検索のためにはプレビュー バージョン (`api-version=2017-11-11-Preview`) が必要です。  詳細については、「[Azure Search の API バージョン](search-api-versions.md)」をご覧ください。

インデクサーの作成に関するデータ プラットフォーム固有のガイダンスについては、「[インデクサーの概要](search-indexer-overview.md)」から始めてください。そこに、[関連記事](search-indexer-overview.md#next-steps)の完全な一覧が記載されています。

> [!NOTE]  
>  許可されるインデクサーの最大数は価格レベルによって異なります。 無料サービスの場合、最大 3 つのインデクサーが許可されます。 Standard サービスの場合、50 のインデクサーが許可されます。 Standard High Definition サービスではインデクサーはサポートされません。 詳細については、「 [サービスの制限](search-limits-quotas-capacity.md) 」を参照してください。    

## <a name="request"></a>要求  

[データ ソース](https://docs.microsoft.com/rest/api/searchservice/create-data-source)、[インデックス](https://docs.microsoft.com/rest/api/searchservice/create-index)、[スキルセット](ref-create-skillset.md)は[インデクサー](search-indexer-overview.md)の定義の一部ですが、それぞれが独立したコンポーネントであり、さまざまな組み合わせで使用できます。 たとえば、複数のインデクサーで同じデータ ソースを使用したり、複数のインデクサーで同じインデックスを使用したり、単一のインデックスに書き込む複数のインデクサーを使用したりできます。

 要求の本文には、インデクサーの定義と、次の部分が含まれます。

+ [dataSourceName](#dataSourceName)
+ [targetIndexName](#targetIndexName)
+ [skillsetName](#skillset)
+ [schedule](#indexer-schedule)
+ [parameters](#indexer-parameters)
+ [fieldMappings](#field-mappings)
+ [outputFieldMappings](#output-fieldmappings)

## <a name="request-syntax"></a>要求構文

要求ペイロードを構築するための構文は次のとおりです。 サンプル要求については、この記事で後述します。  

```json
{   
    "name" : "Required for POST, optional for PUT. The name of the indexer",  
    "description" : "Optional. Anything you want, or null",  
    "dataSourceName" : "Required. The name of an existing data source",  
    "targetIndexName" : "Required. The name of an existing index",  
    "skillsetName" : "Required for cognitive search enrichment",
    "schedule" : { Optional, but immediately runs once if unspecified. See Indexing Schedule below. },  
    "parameters" : { Optional. See Indexing Parameters below. },  
    "fieldMappings" : { Optional. See fieldMappings below. },
    "outputFieldMappings" : { Required for enrichment pipelines. See outputFieldMappings below. },
    "disabled" : Optional boolean value indicating whether the indexer is disabled. False by default.
}  
```
<a name="dataSourceName"></a>

### <a name="datasourcename"></a>"dataSourceName"

多くの場合、[データ ソースの定義](https://docs.microsoft.com/rest/api/searchservice/create-data-source)には、ソース プラットフォームの特性を活用するためにインデクサーが使用できるプロパティが含まれています。 そのため、インデクサーに渡すデータ ソースによって、特定のプロパティやパラメーターの可用性が決定されます。たとえば、Azure BLOB でのコンテンツの種類のフィルター処理や、Azure SQL Database に対するクエリのタイムアウトです。 

<a name="targetIndexName"></a>

### <a name="targetindexname"></a>"targetIndexName"

[インデックス スキーマ](https://docs.microsoft.com/rest/api/searchservice/create-index)によってフィールド コレクションが定義されます。これには、検索、フィルター処理、取得などを始めとする、フィールドの使用方法を決める属性が含まれます。 インデックス作成中、インデクサーはデータ ソースをクロールします。必要に応じて、ドキュメントを解読して情報を抽出したり、結果を JSON にシリアル化したり、インデックス用に定義したスキーマに基づいてペイロードにインデックスを付けたりします。

<a name="skillset"></a>

### <a name="skillsetname"></a>"skillsetName"

[コグニティブ検索 (プレビュー)](cognitive-search-concept-intro.md) は、Azure Search の自然言語および画像処理機能を参照します。これは、エンティティ、キー フレーズ、言語、画像からの情報などを抽出する、データ インジェストの実行中に適用されます。 コンテンツに適用される変換は "*スキル*" を通じて行われます。これは、インデクサーごとに 1 つ設定する単一の "[*スキルセット*](ref-create-skillset.md)" にまとめられます。 データ ソースやインデックスと同様に、スキルセットはインデクサーにアタッチする独立したコンポーネントです。 1 つのスキルセットを別のインデクサーと共に使用することもできます。ただし、各インデクサーは一度に 1 つのスキルセットしか使用できません。
 
<a name="indexer-schedule"></a>

### <a name="schedule"></a>"schedule"  
インデクサーには、必要に応じてスケジュールを指定できます。 スケジュールを指定しない場合、要求を送信するとすぐにインデクサーが実行され、データ ソースに接続してクロールやインデックスの作成を行います。 実行時間の長いインデックス作成ジョブを含む一部のシナリオでは、24 時間の最大値を超えて[処理ウィンドウを拡張する](search-howto-reindex.md#scale-out-indexing)ために、スケジュールが使用されます。 スケジュールが存在する場合、インデクサーはスケジュールに従って定期的に実行されます。 スケジューラは組み込まれており、外部のスケジューラを使用することはできません。 **Schedule** には次の属性があります。 

-   **interval**: 必須。 インデクサーが実行される間隔または期間を指定する時間の値。 許可される最短の間隔は 5 分です。最長は 1 日です。 XSD "dayTimeDuration" 値 ([ISO 8601 期間](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)値の制限されたサブセット) として書式設定する必要があります。 使用されるパターンは `"P[nD][T[nH][nM]]".` です。たとえば、15 分ごとの場合は `PT15M`、2 分ごとの場合は `PT2H` です。  

-   **startTime** - 省略可能。 インデクサーの実行を開始する UTC 日時。  

<a name="indexer-parameters"></a>

### <a name="parameters"></a>"parameters"

必要に応じて、インデクサーに、実行時の動作を変更する構成パラメーターを指定できます。 構成パラメーターは、インデクサーの要求のコンマ区切りです。 

```json
    {
      "name" : "my-blob-indexer-for-cognitive-search",
      ... other indexer properties
      "parameters" : { "maxFailedItems" : "15", "batchSize" : "100", "configuration" : { "parsingMode" : "json", "indexedFileNameExtensions" : ".json, .jpg, .png", "imageAction" : "generateNormalizedImages", "dataToExtract" : "contentAndMetadata" } }
    }
```

#### <a name="general-parameters-for-all-indexers"></a>すべてのインデクサーの一般的なパラメーター

| パラメーター | 型と使用できる値   | 使用法  |
|-----------|------------|--------------------------|--------|
| `"batchSize"` | 整数<br/>規定値はソースによって異なる (Azure SQL Database や Azure Cosmos DB の場合は 1,000、Azure Blob Storage の場合は 10) | パフォーマンスを改善する目的で、データ ソースから読み込まれ、1 つのバッチとしてインデックスが作成されるアイテムの数を指定します。 |
| `"maxFailedItems"` | 整数<br/>既定値は 0 です | エラーの数がこの数字を超えると、インデクサーの実行が失敗したとみなされます。 いかなるエラーによってもインデックス作成のプロセスを停止させない場合は、-1 に設定します。 失敗したアイテムについての情報は、[インデクサー状態の取得](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)を使用して取得できます。  |
| `"maxFailedItemsPerBatch"` | 整数<br/>既定値は 0 です | 各バッチのエラーの数がこの数字を超えると、インデクサーの実行が失敗したとみなされます。 いかなるエラーによってもインデックス作成のプロセスを停止させない場合は、-1 に設定します。 |

#### <a name="blob-configuration-parameters"></a>BLOB の構成パラメーター

[Azure Blob のインデックス付け](search-howto-indexing-azure-blob-storage.md)など、一部のパラメーターは特定のインデクサーにしかありません。

| パラメーター | 型と使用できる値   | 使用法  |
|-----------|---------------------------|--------|
| `"parsingMode"` | String<br/>`"text"`<br/>`"delimitedText"`<br/>`"json"`<br/>`"jsonArray"`  | [Azure BLOB](search-howto-indexing-azure-blob-storage.md) の場合、`text` に設定すると BLOB ストレージ内のプレーン テキスト ファイルのインデックス作成のパフォーマンスが向上します。 <br/>[CSV BLOB](search-howto-index-csv-blobs.md) の場合、BLOB が単純な CSV ファイルのときは `delimitedText` に設定します。 <br/>[JSON BLOB](search-howto-index-json-blobs.md) の場合、構造化コンテンツを抽出するためには `json` に設定し、配列の個々の要素を Azure Search で別個のドキュメントとして抽出するためには `jsonArray` (プレビュー) に設定します。 |
| `"excludedFileNameExtensions"` | String<br/>コンマ区切りのリスト | [Azure BLOB](search-howto-indexing-azure-blob-storage.md) の場合、リスト内のファイルの種類をすべて無視します。 たとえば、".png, .png, .mp4" を除外することで、インデックス作成中にこれらのファイルをスキップできます。 | 
| `"indexedFileNameExtensions"` | String<br/>コンマ区切りのリスト | [Azure BLOB](search-howto-indexing-azure-blob-storage.md) の場合、ファイルの拡張子がリストに含まれる場合は BLOB を選択します。 たとえば、インデックス作成を特定のアプリケーション ファイル ".docx, .pptx, .msg" に集中させ、これらのファイルの種類を具体的に含めることができます。 | 
| `"failOnUnsupportedContentType"` | true <br/>false (既定値) | [Azure BLOB](search-howto-indexing-azure-blob-storage.md) の場合、サポートされていないコンテンツの種類が出現し、すべてのコンテンツの種類 (ファイル拡張子) が事前にわからないときに、インデックス作成を続行する場合は `false` に設定します。 |
| `"failOnUnprocessableDocument"` | true <br/>false (既定値)| [Azure BLOB](search-howto-indexing-azure-blob-storage.md) の場合、ドキュメントのインデックス作成が失敗した場合にインデックス作成を続行する場合は、`false` に設定します。 |
| `"indexStorageMetadataOnlyForOversizedDocuments"` | true <br/>false (既定値)| [Azure BLOB](search-howto-indexing-azure-blob-storage.md) の場合、大きすぎて処理できない BLOB コンテンツのストレージ メタデータに対してインデックス作成を続行するには、このプロパティを `true` に設定します。  サイズが大きい BLOB は、既定ではエラーとして扱われます。 BLOB サイズの制限については、[サービスの制限](search-limits-quotas-capacity.md)に関するページをご覧ください。 |
| `"delimitedTextHeaders"` | String<br/>コンマ区切りのリスト| [CSV BLOB (プレビュー)](search-howto-index-csv-blobs.md) の場合、コンマ区切りの列見出しのリストを指定します。これは、ソース フィールドをインデックス内の宛先フィールドにマッピングするのに役立ちます。 |
| `"delimitedTextDelimiter"` | String<br/>ユーザー定義 | [CSV BLOB (プレビュー)](search-howto-index-csv-blobs.md) の場合、CSV ファイルに対して改行の区切り記号を指定します。各行で新しいドキュメントを開始します (たとえば、'"|"`)。  |
| `"firstLineContainsHeaders"` | true (既定値) <br/>false | [CSV BLOB (preview)](search-howto-index-csv-blobs.md) の場合、各 BLOB の最初の (空白以外の) 行にヘッダーが含まれていることを示します。|
| `"documentRoot"`  | String<br/>ユーザー定義 | [JSON 配列 (プレビュー)](search-howto-index-json-blobs.md#nested-json-arrays) の場合、指定された構造化または半構造化ドキュメントに対して、このプロパティを使用して配列へのパスを指定することができます。 |
| `"dataToExtract"` | String<br/>`"storageMetadata"`<br/>`"allMetadata"`<br/>`"contentAndMetadata"` (規定値) | [Azure BLOB](search-howto-indexing-azure-blob-storage.md) の場合:<br/>`"storageMetadata"` に設定することで、[標準的な BLOB のプロパティおよびユーザー指定のメタデータ](../storage/blobs/storage-properties-metadata.md)にのみインデックスを作成します。 <br/>`"allMetadata"` に設定することで、Azure Blob Storage サブシステムによって提供されるメタデータを抽出し、[content-type 固有のメタデータ](search-howto-indexing-azure-blob-storage.md#ContentSpecificMetadata) (たとえば、.png ファイルにのみ固有のメタデータ) にインデックスを作成します。 <br/>`"contentAndMetadata"` に設定することで、各 BLOB からすべてのメタデータとテキスト コンテンツを抽出します。 <br/><br/>[コグニティブ検索の画像分析 (プレビュー)](cognitive-search-concept-image-scenarios.md) の場合、`"imageAction"` を `"generateNormalizedImages"` に設定すると、`"dataToExtract"` 設定によってインデクサーが画像コンテンツから抽出するデータが指定されます。 これは、Azure BLOB 内の、.PDF またはその他のアプリケーションの埋め込み画像コンテンツや、.jpg や .png などの画像ファイルに適用されます。  |
| `"imageAction"` |  String<br/>`"none"`<br/>`"generateNormalizedImages"` | [Azure BLOB](search-howto-indexing-azure-blob-storage.md) の場合、`"none"` に設定することで、埋め込み画像またはデータ セット内の画像ファイルを無視します。 既定のプランです。 <br/><br/>[コグニティブ検索の画像分析](cognitive-search-concept-image-scenarios.md) の場合、`"generateNormalizedImages"` に設定することで、画像からテキスト (たとえば、一時停止の道路標識から "stop" という単語) を抽出し、それをコンテンツ フィールドの一部として埋め込みます。 画像分析中、インデクサーは、ドキュメント解読の一部として正規化された画像の配列を作成し、生成された情報をコンテンツ フィールドに埋め込みます。 このアクションを実行するには、`"dataToExtract"` を `"contentAndMetadata"` に設定する必要があります。 正規化された画像は、一様な画像出力をもたらす追加処理を参照します。画像出力は、ビジュアル検索の結果に画像を含めるときに一貫性のあるレンダリングを促進するために、サイズと回転を調整されます (たとえば、[JFK のデモ](https://github.com/Microsoft/AzureSearch_JFK_Files)で見られるような、グラフ コントロール内の同一サイズの写真)。 この情報は、使用する際に、各画像に対して生成されます。 |


#### <a name="other-configuration-parameters"></a>その他の構成パラメーター

Azure SQL Database に固有のパラメーターを次に示します。

| パラメーター | 型と使用できる値   | 使用法  |
|-----------|---------------------------|--------|
| `"queryTimeout"` | String<br/>"hh:mm:ss"<br/>"00:05:00"| [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) の場合、このパラメーターを設定することで、既定の 5 分よりもタイムアウトを長くします。|

<a name="field-mappings"></a>

### <a name="fieldmappings"></a>"fieldMappings"

インデクサーの定義には、Azure Search インデックス内のターゲット フィールドにソース フィールドをマッピングするための、フィールドの関連付けが含まれます。 コンテンツの転送が直接のパスとエンリッチメントされるパスのどちらに従うかに応じて、2 種類の関連付けがあります。

+ **fieldMappings** は省略可能で、ソースとターゲットのフィールド名が一致しない場合、または関数を指定する場合に適用されます。
+ **outputFieldMappings** は、[エンリッチメント パイプライン](cognitive-search-concept-intro.md)を構築する場合は必須です。 エンリッチメント パイプラインでは、出力フィールドは、エンリッチメント処理中に定義される構造です。 たとえば、出力フィールドは、エンリッチメント時にソース ドキュメント内の 2 つの別個のフィールドから構築される複合構造である場合があります。 

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

<a name="output-fieldmappings"></a>

### <a name="outputfieldmappings"></a>"outputFieldMappings"

スキルセットがインデクサーにバインドされる[コグニティブ検索](cognitive-search-concept-intro.md)のシナリオでは、`outputFieldMappings` を追加して、コンテンツを提供するエンリッチメント ステップの出力を、インデックス内の検索可能なフィールドに関連付ける必要があります。

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

### <a name="field-mapping-functions"></a>フィールド マッピング関数

フィールド マッピングは、*フィールド マッピング関数*を使用してソース フィールドの値を変換するためにも利用できます。 たとえば、任意の文字列値を base64 でエンコードできるため、その値を、ドキュメントのキー フィールドに格納するために使用できます。

フィールド マッピング関数をいつ、どのように使用するかの詳細については、「[フィールド マッピング関数](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#field-mapping-functions)」をご覧ください。

## <a name="request-examples"></a>要求例  
 最初の例では、UTC 2015 年 1 月 1 日から 1 時間ごとに実行されるスケジュールで、`ordersds` データ ソースによって参照されるテーブルのデータを `orders` インデックスにコピーするインデクサーを作成します。 インデックス作成に失敗した項目が各バッチで 5 つ以下で、合計で 10 以下あれば、各インデクサーの呼び出しは成功となります。  

```json
{
    "name" : "myindexer",  
    "description" : "a cool indexer",  
    "dataSourceName" : "ordersds",  
    "targetIndexName" : "orders",  
    "schedule" : { "interval" : "PT1H", "startTime" : "2018-01-01T00:00:00Z" },  
    "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5 }  
}
```

2 番目の例では、スキルセットへの参照と [outputFieldMappings](#output-fieldmappings) によって示されている、コグニティブ検索の操作を行います。 [スキルセット](ref-create-skillset.md)は高水準リソースであり、個別に定義されます。 この例は、[コグニティブ検索のチュートリアル](cognitive-search-tutorial-blob.md)内にあるインデクサーの定義の省略形です。

```json
{
  "name":"demoindexer", 
  "dataSourceName" : "demodata",
  "targetIndexName" : "demoindex",
  "skillsetName" : "demoskillset",
  "fieldMappings" : [
    {
        "sourceFieldName" : "content",
        "targetFieldName" : "content"
    }
   ],
  "outputFieldMappings" : 
  [
    {
        "sourceFieldName" : "/document/organizations", 
        "targetFieldName" : "organizations"
    },
  ],
  "parameters":
  {
    "maxFailedItems":-1,
    "configuration": 
    {
    "dataToExtract": "contentAndMetadata",
    "imageAction": "generateNormalizedImages"
    }
  }
}
```

## <a name="response"></a>Response  
 要求成功の場合: 201 作成されました。  

## <a name="see-also"></a>関連項目

+ [インデクサーの概要](search-indexer-overview.md)
+ [コグニティブ検索の概要](cognitive-search-concept-intro.md)
+ [クイック スタート: コグニティブ検索を使ってみる](cognitive-search-quickstart-blob.md)
+ [フィールドをマップする方法](cognitive-search-output-field-mapping.md)
