---
title: Azure Blob Storage での JSON の検索に関するチュートリアル - Azure Search
description: このチュートリアルでは、半構造化された Azure BLOB データを Azure Search を使用して検索する方法について説明します。
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 1c8ce14dd3961eff33a54a14c2bd0b27650d8a50
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58201349"
---
# <a name="tutorial-search-semi-structured-data-in-azure-cloud-storage"></a>チュートリアル:Azure クラウド ストレージで半構造化データを検索する

Azure Search は、半構造化データの読み取り方法を解している[インデクサー](search-indexer-overview.md)を使用して、Azure Blob Storage に格納されている JSON のドキュメントや配列のインデックスを作成することができます。 半構造化データには、データ内のコンテンツを区別するタグやマーキングが含まれます。 このデータは、非構造化データ (全体にインデックスを付ける必要がある) と正式に構造化されたデータ (フィールドごとにインデックス付け可能な、リレーショナル データベース スキーマなどのデータ モデルに準拠) を折衷するものです。

このチュートリアルでは、[Azure Search の REST API](https://docs.microsoft.com/rest/api/searchservice/) と REST クライアントを使用して次のタスクを実行します。

> [!div class="checklist"]
> * Azure BLOB コンテナー用に Azure Search データ ソースを構成する
> * 検索可能なコンテンツを格納する Azure Search インデックスを作成する
> * コンテナーを読み取って検索可能なコンテンツを Azure Blob Storage から抽出するようにインデクサーを構成して実行する
> * 作成したインデックスを検索する

> [!NOTE]
> このチュートリアルは、現在 Azure Search のプレビュー機能である JSON 配列サポートに依存します。 ポータルでは使用できません。 そのため、この機能を提供するプレビュー版の REST API、および REST クライアント ツールを使用して、API を呼び出します。

## <a name="prerequisites"></a>前提条件

[Azure Search サービスを作成](search-create-service-portal.md)するか、現在のサブスクリプションから[既存のサービスを見つけます](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 このチュートリアル用には、無料のサービスを使用できます。

サンプル データを格納する [Azure ストレージ アカウントを作成](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)します。

[Postman を使用](https://www.getpostman.com/)するか、または別の REST クライアントを使用して要求を送信します。 Postman で HTTP 要求を設定する手順は、次のセクションで取り上げます。

## <a name="set-up-postman"></a>Postman の設定

Postman を開始し、HTTP 要求を設定します。 このツールに慣れていない場合は、[Postman を使用して Azure Search REST API を調べる方法](search-fiddler.md)に関するページを参照してください。

このチュートリアルでの各呼び出しの要求メソッドは "POST" です。 ヘッダー キーは "Content-type" と "api-key" です。 ヘッダー キーの値は、それぞれ "application/json" と "管理者キー" (管理者キーは、検索の主キーを表すプレースホルダー) です。 本文は、呼び出しの実際のコンテンツを配置する場所です。 使用するクライアントによっては、クエリの作成方法にいくつかのバリエーションがありますが、それらは基本的な機能です。

  ![半構造化検索](media/search-semi-structured-data/postmanoverview.png)

このチュートリアルで説明する REST 呼び出しには検索の api-key が必要です。 api-key は検索サービスの **[キー]** にあります。 この api-key は、このチュートリアルで行う各 API 呼び出しのヘッダーに配置する必要があります (前のスクリーンショットの "管理者キー" をこのキーに置き換えます)。 各呼び出しで必要になるため、このキーを保持しておいてください。

  ![半構造化検索](media/search-semi-structured-data/keys.png)

## <a name="prepare-sample-data"></a>サンプル データの準備

1. **[clinical-trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip) をダウンロード**し、独自のフォルダーに解凍します。 データの出典は [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results) で、このチュートリアルで使用するために JSON に変換しています。

2. [Azure portal](https://portal.azure.com) にサインインし、Azure ストレージ アカウントに移動して **data** コンテナーを開き、**[アップロード]** をクリックします。

3. **[詳細]** をクリックして、「clinical-trials-json」と入力し、ダウンロードしたすべての JSON ファイルをアップロードします。

  ![半構造化検索](media/search-semi-structured-data/clinicalupload.png)

アップロードが完了したら、データ コンテナー内の独自のサブフォルダーにファイルが表示されます。

## <a name="connect-your-search-service-to-your-container"></a>コンテナーへの検索サービスの接続

Postman を使用して、検索サービスに対して 3 つの API 呼び出しを行い、データ ソース、インデックス、およびインデクサーを作成します。 データ ソースには、ストレージ アカウントと JSON データへのポインターが含まれています。 データを読み込むときに、検索サービスは接続を行います。

クエリ文字列にはプレビュー API (**api-version=2017-11-11-Preview** など) を含める必要があり、各呼び出しは **201 Created** を返します。 一般公開されている api-version には json を jsonArray として処理する機能がまだ実装されておらず、現時点ではプレビュー版の api-version でのみ可能です。

REST クライアントから次の 3 つの API 呼び出しを実行します。

## <a name="create-a-data-source"></a>データ ソースを作成する

データ ソースは、インデックスの対象データを指定する Azure Search オブジェクトです。

この呼び出しのエンドポイントは `https://[service name].search.windows.net/datasources?api-version=2016-09-01-Preview` です。 `[service name]` を検索サービスの名前に置き換えます。 この呼び出しでは、ストレージ アカウントの名前とストレージ アカウント キーが必要です。 ストレージ アカウント キーは、Azure Portal のストレージ アカウントの **[アクセス キー]** にあります。 その場所を次の図に示します。

  ![半構造化検索](media/search-semi-structured-data/storagekeys.png)

呼び出しの本文の `[storage account name]` と `[storage account key]` を置き換えてから、呼び出しを実行してください。

```json
{
    "name" : "clinical-trials-json",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
    "container" : { "name" : "data", "query" : "clinical-trials-json" }
}
```

応答は次のようになります。

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#datasources/$entity",
    "@odata.etag": "\"0x8D505FBC3856C9E\"",
    "name": "clinical-trials-json",
    "description": null,
    "type": "azureblob",
    "subtype": null,
    "credentials": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=[mystorageaccounthere];AccountKey=[[myaccountkeyhere]]];"
    },
    "container": {
        "name": "data",
        "query": "clinical-trials-json"
    },
    "dataChangeDetectionPolicy": null,
    "dataDeletionDetectionPolicy": null
}
```

## <a name="create-an-index"></a>インデックスを作成する
    
2 番目の API 呼び出しでは、Azure Search インデックスを作成します。 インデックスでは、すべてのパラメーターとその属性を指定します。

この呼び出しの URL は `https://[service name].search.windows.net/indexes?api-version=2016-09-01-Preview` です。 `[service name]` を検索サービスの名前に置き換えます。

最初に、URL を置き換えます。 次のコードをコピーして本文に貼り付け、クエリを実行します。

```json
{
  "name": "clinical-trials-json-index",  
  "fields": [
  {"name": "FileName", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
  {"name": "Description", "type": "Edm.String", "searchable": true, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
  {"name": "MinimumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
  {"name": "Title", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
  {"name": "URL", "type": "Edm.String", "searchable": false, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
  {"name": "MyURL", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
  {"name": "Gender", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "MaximumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
  {"name": "Summary", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
  {"name": "NCTID", "type": "Edm.String", "key": true, "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
  {"name": "Phase", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "Date", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
  {"name": "OverallStatus", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "OrgStudyId", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": false},
  {"name": "HealthyVolunteers", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "Keywords", "type": "Collection(Edm.String)", "searchable": true, "retrievable": true, "facetable": true, "filterable": false, "sortable": false},
  {"name": "metadata_storage_last_modified", "type":"Edm.DateTimeOffset", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
  {"name": "metadata_storage_size", "type":"Edm.String", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
  {"name": "metadata_content_type", "type":"Edm.String", "searchable": true, "retrievable": true, "filterable": true, "sortable": false}
  ],
  "suggesters": [
  {
    "name": "sg",
    "searchMode": "analyzingInfixMatching",
    "sourceFields": ["Title"]
  }
  ]
}
```

応答は次のようになります。

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexes/$entity",
    "@odata.etag": "\"0x8D505FC00EDD5FA\"",
    "name": "clinical-trials-json-index",
    "fields": [
        {
            "name": "FileName",
            "type": "Edm.String",
            "searchable": false,
            "filterable": false,
            "retrievable": true,
            "sortable": true,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        {
            "name": "Description",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "retrievable": false,
            "sortable": false,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        ...
          "scoringProfiles": [],
    "defaultScoringProfile": null,
    "corsOptions": null,
    "suggesters": [],
    "analyzers": [],
    "tokenizers": [],
    "tokenFilters": [],
    "charFilters": []
}
```

## <a name="create-and-run-an-indexer"></a>インデクサーの作成と実行

インデクサーはデータ ソースに接続してターゲットの検索インデックスにデータをインポートし、データ更新を自動化するスケジュールを必要に応じて提供します。

この呼び出しの URL は `https://[service name].search.windows.net/indexers?api-version=2016-09-01-Preview` です。 `[service name]` を検索サービスの名前に置き換えます。

最初に、URL を置き換えます。 次のコードをコピーして本文に貼り付け、要求を送信します。 要求はすぐに処理されます。 応答が返された時点で、フルテキスト検索可能なインデックスが得られます。

```json
{
  "name" : "clinical-trials-json-indexer",
  "dataSourceName" : "clinical-trials-json",
  "targetIndexName" : "clinical-trials-json-index",
  "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
}
```

応答は次のようになります。

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexers/$entity",
    "@odata.etag": "\"0x8D505FDE143D164\"",
    "name": "clinical-trials-json-indexer",
    "description": null,
    "dataSourceName": "clinical-trials-json",
    "targetIndexName": "clinical-trials-json-index",
    "schedule": null,
    "parameters": {
        "batchSize": null,
        "maxFailedItems": null,
        "maxFailedItemsPerBatch": null,
        "base64EncodeKeys": null,
        "configuration": {
            "parsingMode": "jsonArray"
        }
    },
    "fieldMappings": [],
    "enrichers": [],
    "disabled": null
}
```

## <a name="search-your-json-files"></a>JSON ファイルの検索

これでインデックスに対してクエリを発行することができます。 この作業には、ポータルの[**検索エクスプローラー**](search-explorer.md)を使用します。

  ![非構造化検索](media/search-semi-structured-data/indexespane.png)

### <a name="user-defined-metadata-search"></a>ユーザー定義のメタデータの検索

前述のとおり、さまざまな方法 (フルテキスト検索、システム プロパティ、ユーザー定義のメタデータ) でデータを照会できます。 システム プロパティとユーザー定義のメタデータは、ターゲット インデックスの作成時に**取得可能**としてマークされている場合、`$select` パラメーターでのみ検索できます。 作成後にインデックス内のパラメーターを変更することはできません。 ただし、パラメーターの追加は可能です。

基本的なクエリの例として `$select=Gender,metadata_storage_size` があります。このクエリは、これらの 2 つのパラメーターに戻り値を制限します。

  ![半構造化検索](media/search-semi-structured-data/lastquery.png)

より複雑なクエリの例として `$filter=MinimumAge ge 30 and MaximumAge lt 75` があります。このクエリは、MinimumAge パラメーターが 30 以上および MaximumAge パラメーターが 75 未満の結果のみ返します。

  ![半構造化検索](media/search-semi-structured-data/metadatashort.png)

その他のクエリを試してみてもかまいません。 論理演算子 (and、or、not) と比較演算子 (eq、ne、gt、lt、ge、le) を使用できます。 文字列比較では大文字と小文字が区別されます。

`$filter` パラメーターは、インデックスの作成時にフィルター可能としてマークされたメタデータでのみ使用できます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

チュートリアルの後で最も速くクリーンアップする方法は、Azure Search サービスが含まれているリソース グループを削除することです。 リソース グループを削除することで、そのすべての内容を完全に削除することができます。 ポータルでは、リソース グループ名は Azure Search サービスの [概要] ページに表示されます。

## <a name="next-steps"></a>次の手順

インデクサー パイプラインには、AI を活用したアルゴリズムをアタッチすることができます。 引き続き次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Azure Blob Storage 内ドキュメントのインデックスを Azure Search で作成する](search-howto-indexing-azure-blob-storage.md)