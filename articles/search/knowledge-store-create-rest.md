---
title: REST を使用してナレッジ ストアを作成する
titleSuffix: Azure Cognitive Search
description: REST API と Postman を使用して、スキルセットから AI エンリッチメントを永続化するための Azure Cognitive Search のナレッジ ストアを作成します。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/03/2021
ms.openlocfilehash: ad30dc4f59816f286f6ffe40909b76410e963c16
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131555781"
---
# <a name="create-a-knowledge-store-using-rest-and-postman"></a>REST と Postman を使用してナレッジ ストアを作成する

ナレッジ ストアは、Azure Cognitive Search の機能です。[AI エンリッチメント パイプライン](cognitive-search-concept-intro.md)からのスキルセット出力を、その後のナレッジ マイニング、データ分析、またはダウンストリーム処理のために Azure Storage に送信するものです。 ナレッジ ストアにデータが取り込まれた後は、[ストレージ ブラウザー](knowledge-store-view-storage-explorer.md)や [Power BI](knowledge-store-connect-power-bi.md) などのツールを使用してコンテンツを探索できます。

この記事では、REST API を使用して、Azure Storage でナレッジ ストアにホテル宿泊の一連の顧客レビューを取り込み、エンリッチし、探索します。 最終的には、ソースから取得された元のテキスト コンテンツと AI によって生成されたコンテンツ (センチメント スコア、キー フレーズ抽出、言語検出、および非英語圏の顧客によるコメントのテキスト翻訳が含まれている) を含むナレッジ ストアが作成されます。

初期データ セットを使用できるようにするため、最初にホテルのレビューが Azure Blob Storage にインポートされます。 後処理で、結果が Azure Table Storage にナレッジ ストアとして保存されます。

> [!NOTE]
> この記事では、この記事の [Postman デスクトップ アプリ](https://www.getpostman.com/)が想定されています。 この記事の[ソース コード](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store)には、すべての要求が収められた Postman コレクションが含まれています。 

## <a name="create-services-and-load-data"></a>サービスを作成してデータを読み込む

この演習では、Azure Cognitive Search、Azure Blob Storage、[Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) を AI に使用します。 

ワークロードは非常に小さいので、1 日に最大 20 トランザクションの処理を無料で使うことができる Cognitive Services を内部で利用しています。 ワークロードが小さいということは、Cognitive Services リソースの作成またはアタッチをスキップできることを意味します。

1. [HotelReviews_Free.csv をダウンロードします](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D)。 このデータは CSV ファイルに保存されたホテル レビュー データ (ソースは Kaggle.com) であり、1 つのホテルに関する 19 個の顧客フィードバックが含まれています。 

1. [Azure Storage アカウントを作成](../storage/common/storage-account-create.md?tabs=azure-portal)するか、[既存のアカウントを検索](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/)してください。 インポートされる生コンテンツと最終的な結果であるナレッジ ストアの両方に Azure Storage を使用します。

   **[StorageV2 (general purpose V2)]\(StorageV2 (汎用 V2)\)** のアカウントの種類を選択します。

1. Azure Storage リソースで、**ストレージ ブラウザー** を使用して **hotel-reviews** という名前の BLOB コンテナーを作成します。

1. ページの上部にある **[アップロード]** を選択して、前の手順でダウンロードした **HotelReviews-Free.csv** ファイルを読み込みます。

   :::image type="content" source="media/knowledge-store-create-portal/blob-container-storage-explorer.png" alt-text="アップロードしたファイルと左側のナビゲーション ペインが示されたストレージ ブラウザーのスクリーンショット" border="true":::

1. このリソースでの作業はほぼ完了ですが、これらのページを離れる前に、インデクサーを使用してこのデータを取得できるように、左側のナビゲーション ペインで **[アクセス キー]** を選択して接続文字列を取得します。

1. **[アクセス キー]** で、ページの上部にある **[Show Keys]\(キーの表示\)** を選択して接続文字列を再表示し、key1 または key2 のいずれかの接続文字列をコピーします。

   接続文字列は次のような形式です: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

## <a name="configure-requests"></a>要求を構成する

1. GitHub から [azure-search-postman-samples](https://github.com/Azure-Samples/azure-search-postman-samples) をダウンロードし、ファイルを解凍します。 リポジトリには複数のコレクションが複数あります。 knowledge-store フォルダー内のコレクションを使用します。

1. Postman で **[ファイル]**  >  **[インポート]** を選択して、KnowledgeStore.postman_collection.json ファイルをインポートします。

1. **[Collections]\(コレクション\)** タブを選択し、 **[...]** (省略記号) ボタンを選択します。

1. **[編集]** を選択します。

   ![ナビゲーションを示す Postman アプリ](media/knowledge-store-create-rest/postman-edit-menu.png "Postman の [Edit]\(編集\) メニューに移動する")

1. **[Edit]\(編集\)** ダイアログ ボックスで、 **[Variables]\(変数\)** タブを選択します。 

**[Variables]\(変数\)** タブには、二重中かっこ内で特定の変数が出現するたびに、Postman でスワップされる値を追加できます。 たとえば、Postman によって、記号 `{{admin-key}}` は `admin-key` に設定した現在の値に置き換えられます。 Postman では、URL、ヘッダー、要求本文などの置き換えが行われます。 

変数は、Azure サービス、サービス接続、およびオブジェクト名に対して定義されています。 サービスと接続のプレースホルダーの値を、検索サービスとストレージ アカウントの実際の値に置き換えます。 これらの値は Azure portal で見つけることができます。

+ `search-service-name` と `search-service-admin-key` の値を取得するには、ポータルで Azure Cognitive Search サービスに移動し、 **[概要]** と **[キー]** ページの値をコピーします。

+ `storage-account-name` と `storage-account-connection-string` の値を取得するには、 **[アクセス キー]** ページを確認します。

![Postman アプリの [variables]\(変数\) タブ](media/knowledge-store-create-rest/postman-variables-window.png "Postman の [variables]\(変数\) ウィンドウ")

| 変数    | 情報の入手元 |
|-------------|-----------------|
| `admin-key` | Azure Cognitive Search サービスの **[キー]** ページ上。  |
| `api-version` | **2020-06-30** のままにします。 |
| `datasource-name` | **hotel-reviews-ds** のままにします。 | 
| `indexer-name` | **hotel-reviews-ixr** のままにします。 | 
| `index-name` | **hotel-reviews-ix** のままにします。 | 
| `search-service-name` | Azure Cognitive Search サービスの名前。 URL が `https://mySearchService.search.windows.net` の場合、入力する値は `mySearchService` です。 | 
| `skillset-name` | **hotel-reviews-ss** のままにします。 | 
| `storage-account-name` | Azure ストレージ アカウント名。 | 
| `storage-connection-string` | ストレージ アカウントの **[アクセス キー]** タブで、ページ上部の **[キーの表示]** を選択し、次に **[key1]**  >  **[接続文字列]** をコピーします。 | 
| `storage-container-name` | **hotel-reviews** のままにします。 | 

### <a name="review-the-request-collection-in-postman"></a>Postman の要求コレクションの確認

ナレッジ ストアはスキルセットで定義され、その後インデクサーにアタッチされます。 ナレッジストアを作成するには、インデックス、データ ソース、スキルセット、インデクサーなど、すべてのアップストリーム オブジェクトを作成する必要があります。 インデックスはナレッジ ストアに関連付けられていませんが、インデクサーでは実行にインデックスを必要とするため、インデクサーの前提条件として作成します。

ナレッジ ストアを作成する場合は、次の 4 つの HTTP 要求を発行します。 

+ **インデックスを作成するための PUT 要求**: このインデックスには、検索サービスに対して発行されたクエリ要求で返された検索可能なデータが含まれます。

+ **データ ソースを作成するための POST 要求**: このデータ ソースは、インデクサーに接続情報を提供します。 インデクサーは、Azure Storageアカウントに接続してサンプル データを取得します。

+ **スキルセットを作成するための PUT 要求**: スキルセットでは、データに適用されるエンリッチメントが指定されます。 また、ナレッジ ストアの構造も指定されます。

+ **インデクサーを作成するための PUT 要求**: インデクサーを実行すると、データが読み取られ、スキルセットが適用され、Azure Storage にナレッジ ストアが作成され、結果が保存されます。 この要求は最後に実行する必要があります。

前に設定した変数は、ヘッダーと URL で使用されます。 次に示すインデックスの作成要求のスクリーンショットは、これらの変数が要求内で現れる場所を示しています。

![Postman のヘッダーのインターフェイスを示すスクリーンショット](media/knowledge-store-create-rest/postman-headers-ui.png)

> [!Note]
> コレクション セット `api-key` と `Content-type` ヘッダー内のすべての要求。これは必須です。 Postman で変数が認識されている場合、前のスクリーンショットの `{{admin-key}}` のように、変数はオレンジ色のテキストで表示されます。 変数のスペルが間違っている場合は、赤いテキストで表示されます。
>

## <a name="create-an-index"></a>インデックスを作成する

[インデックスの作成 (REST API)](/rest/api/searchservice/create-index) を使用して、検索サービスの検索インデックスを作成します。 検索インデックスはナレッジ ストアとは無関係ですが、インデクサーにより、それを作成することが要求されます。 検索インデックスには、ナレッジ ストアと同じコンテンツが含まれます。 コンテンツを探索するための別の方法が必要な場合は、検索サービスにクエリ要求を送信することで、このインデックスに対してクエリを実行できます。 

PUT 要求を `https://{{search-service-name}}.search.windows.net/indexes/{{index-name}}?api-version={{api-version}}` に発行して、インデックスを作成します。 インデックス スキーマは、要求の本文で提供されます。

```JSON
{
    "name": "{{index-name}}",
    "fields": [
        { "name": "name", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_date", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_rating", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_text", "type": "Edm.String", "filterable": false,  "sortable": false, "facetable": false },
        { "name": "reviews_title", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_username", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "AzureSearch_DocumentKey", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false, "key": true },
        { "name": "language", "type": "Edm.String", "filterable": true, "sortable": false, "facetable": true },
        { "name": "translated_text", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "sentiment", "type": "Collection(Edm.String)", "searchable": false, "filterable": true, "retrievable": true, "sortable": false, "facetable": true },
        { "name": "keyphrases", "type": "Collection(Edm.String)", "filterable": true, "sortable": false, "facetable": true }
    ]
}
```

このインデックス定義は、ユーザーに表示するデータの組み合わせです。 これには、CSV に直接マップされるフィールド (ホテルの名前、レビューの日付など) と、スキルセットによって作成されるフィールド (センチメント、キー フレーズ、言語、翻訳されたテキスト) が含まれます。

**[送信]** を選択して PUT 要求を発行します。

状態 `201 - Created` が表示されます。 別の状態が表示される場合は、 **[Body]\(本文\)** ウィンドウでエラー メッセージを含む JSON 応答を探します。 無料の検索サービスを使用している場合は、新しいオブジェクト (無料サービスでは最大 3 つ) 用のスペースがあることを確認してください。

この時点では、インデックスは作成されますが、読み込まれません。 ドキュメントのインポートは、後でインデクサーを実行するときに行われます。 

## <a name="create-a-data-source"></a>データ ソースを作成する

次に、Blob Storage に格納したホテル データに Azure Cognitive Search を接続します。 データ ソースを作成するには、[データ ソースの作成](/rest/api/searchservice/create-data-source) POST 要求を `https://{{search-service-name}}.search.windows.net/datasources?api-version={{api-version}}` に送信します。 

Postman で、 **[Create Datasource]\(データソースの作成\)** 要求、 **[Body]\(本文\)** ウィンドウの順に移動します。 次のコードが表示されます。

```json
{
  "name" : "{{datasource-name}}",
  "description" : "Demo files to demonstrate knowledge store capabilities.",
  "type" : "azureblob",
  "credentials" : { "connectionString" : "{{storage-connection-string}}" },
  "container" : { "name" : "{{storage-container-name}}" }
}
```

**[Send]\(送信\)** を選択して POST 要求を発行します。 

## <a name="create-a-skillset"></a>スキルセットを作成する 

次の手順では、適用される機能強化と、結果が格納されるナレッジ ストアの両方を指定するスキルセットを作成します。 この要求では、[スキルセットの作成](/rest/api/searchservice/create-skillset) PUT 要求が `https://{{search-service-name}}.search.windows.net/skillsets/{{skillset-name}}?api-version={{api-version}}` に送信されます。

2 つの大きな最上位オブジェクト `skills` と `knowledgeStore` があります。

+ "skills" はスキル セットです。 `skills` オブジェクト内の各オブジェクトはエンリッチメント サービスです。 各エンリッチメント サービスには、`inputs` と `outputs` があります。 `LanguageDetectionSkill` には `Language` の出力 `targetName` があります。 このノードの値は、他のほとんどのスキルで入力として使用されます。 ソースは `document/Language` です。 あるノードの出力を別のノードの入力として使用するこの機能は、`ShaperSkill` でより顕著です。ここでは、ナレッジ ストアのテーブルに向かうデータの流れを指定します。

+ "knowledgeStore" には、ストレージ アカウントへの接続文字列と一連のプロジェクションが含まれています。 プロジェクション配列内の各プロジェクション項目は Azure Storage のテーブルになります。 テーブル内の列には、クロスリンク用に生成された列とコンテンツ フィールドが含まれます。 また、エンリッチメント中に作成されたコンテンツ フィールドも含まれます。

  プロジェクションでは、データ シェイプを入力として受け入れます。 スキルの出力をプロジェクションにマップすることで Shaper スキルを使用してシェイプを指定したり、各プロジェクション項目にインライン シェイプを定義したりできます。 

スキルセットを生成するには、Postman の **[Send]\(送信\)** ボタンを選択して要求を PUT します。

```json
{
    "name": "{{skillset-name}}",
    "description": "Skillset to detect language, translate text, extract key phrases, and score sentiment",
    "skills": [ 
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill", 
            "context": "/document/reviews_text", "textSplitMode": "pages", "maximumPageLength": 5000,
            "inputs": [ 
                { "name": "text", "source": "/document/reviews_text" }
            ],
            "outputs": [
                { "name": "textItems", "targetName": "pages" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.V3.SentimentSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode", "source": "/document/language" }
            ],
            "outputs": [
                { "name": "sentiment", "targetName": "sentiment" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "context": "/document",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text" }
            ],
            "outputs": [
                { "name": "languageCode", "targetName": "language" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.TranslationSkill",
            "context": "/document/reviews_text/pages/*",
            "defaultFromLanguageCode": null,
            "defaultToLanguageCode": "en",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text/pages/*" }
            ],
            "outputs": [
                { "name": "translatedText", "targetName": "translated_text" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text",  "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode",  "source": "/document/language" }
            ],
            "outputs": [
                { "name": "keyPhrases" , "targetName": "keyphrases" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "context": "/document",
            "inputs": [
                { "name": "name",  "source": "/document/name" },
                { "name": "reviews_date",  "source": "/document/reviews_date" },
                { "name": "reviews_rating",  "source": "/document/reviews_rating" },
                { "name": "reviews_text",  "source": "/document/reviews_text" },
                { "name": "reviews_title",  "source": "/document/reviews_title" },
                { "name": "reviews_username",  "source": "/document/reviews_username" },
                { "name": "AzureSearch_DocumentKey",  "source": "/document/AzureSearch_DocumentKey" },
                {
                "name": "pages",
                "sourceContext": "/document/reviews_text/pages/*",
                "inputs": [
                    {
                    "name": "languageCode",
                    "source": "/document/language"
                    },
                    {
                    "name": "translatedText",
                    "source": "/document/reviews_text/pages/*/translated_text"
                    },
                    { 
                    "name": "sentiment",
                    "source": "/document/reviews_text/pages/*/sentiment"
                    },
                    {
                    "name": "keyPhrases",
                    "source": "/document/reviews_text/pages/*/keyphrases/*"
                    },
                    {
                    "name": "Page",
                    "source": "/document/reviews_text/pages/*"
                    }
                ]
                }
            ],
            "outputs": [
                { "name": "output" , "targetName": "tableprojection" }
            ]
        }
    ],
    "knowledgeStore": {
        "storageConnectionString": "{{storage-connection-string}}",
        "projections": [
            {
                "tables": [
                    { "tableName": "hotelReviews1Document", "generatedKeyName": "Documentid", "source": "/document/tableprojection" },
                    { "tableName": "hotelReviews2Pages", "generatedKeyName": "Pagesid", "source": "/document/tableprojection/pages/*" },
                    { "tableName": "hotelReviews3KeyPhrases", "generatedKeyName": "KeyPhrasesid", "source": "/document/tableprojection/pages/*/keyPhrases/*" }
                ],
                "objects": []
            },
            {
                "tables": [
                    { 
                        "tableName": "hotelReviews4InlineProjectionDocument", "generatedKeyName": "Documentid", "sourceContext": "/document",
                        "inputs": [
                            { "name": "name", "source": "/document/name"},
                            { "name": "reviews_date", "source": "/document/reviews_date"},
                            { "name": "reviews_rating", "source": "/document/reviews_rating"},
                            { "name": "reviews_username", "source": "/document/reviews_username"},
                            { "name": "reviews_title", "source": "/document/reviews_title"},
                            { "name": "reviews_text", "source": "/document/reviews_text"},
                            { "name": "AzureSearch_DocumentKey", "source": "/document/AzureSearch_DocumentKey" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviews5InlineProjectionPages", "generatedKeyName": "Pagesid", "sourceContext": "/document/reviews_text/pages/*",
                        "inputs": [
                            { "name": "Sentiment", "source": "/document/reviews_text/pages/*/sentiment"},
                            { "name": "LanguageCode", "source": "/document/language"},
                            { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/keyphrases"},
                            { "name": "TranslatedText", "source": "/document/reviews_text/pages/*/translated_text"},
                            { "name": "Page", "source": "/document/reviews_text/pages/*" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviews6InlineProjectionKeyPhrases", "generatedKeyName": "kpidv2", "sourceContext": "/document/reviews_text/pages/*/keyphrases/*",
                        "inputs": [
                            { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/keyphrases/*" }
                        ]
                    }
                ],
                "objects": []
            }
        ]
    }
}
```

## <a name="create-an-indexer"></a>インデクサーの作成

最後の手順は、[インデクサーの作成](/rest/api/searchservice/create-indexer)要求です。 インデクサーによってデータが読み取られ、スキルセットがアクティブ化されます。 これまでに作成したいくつかの他のリソース (データソース、インデックス、スキルセット) がインデクサーの定義によって参照されます。 

+ `parameters/configuration` オブジェクトでは、インデクサーがデータを取り込む方法を制御します。 ここでは、入力データは、ヘッダー行とコンマ区切り値がある単一の CSV ファイルに含まれています。 

+ "AzureSearch_DocumentKey" は、(メタデータ ストレージ パスに基づいて) BLOB インデクサーによって生成される各ドキュメントの一意の識別子です。 

+ 出力フィールド マッピングでは、エンリッチされたフィールドが、検索インデックス内のフィールドにマップされる方法を指定します。 出力フィールド マッピングは、ナレッジ ストアでは使用されません (ナレッジ ストアでは、物理的なデータ構造を表すためにシェイプとプロジェクションが使用されます)。

インデクサーを作成して実行するには、Postman で **[送信]** を選択します。 この手順で、データのインポート、スキルセットの実行、ナレッジ ストアの作成が行われます。

```json
{
    "name": "{{indexer-name}}",
    "dataSourceName": "{{datasource-name}}",
    "skillsetName": "{{skillset-name}}",
    "targetIndexName": "{{index-name}}",
    "parameters": {
        "configuration": {
            "dataToExtract": "contentAndMetadata",
            "parsingMode": "delimitedText",
            "firstLineContainsHeaders": true,
            "delimitedTextDelimiter": ","
        }
    },
    "fieldMappings": [
        {
            "sourceFieldName": "AzureSearch_DocumentKey",
            "targetFieldName": "AzureSearch_DocumentKey",
            "mappingFunction": { "name": "base64Encode" }
        }
    ],
    "outputFieldMappings": [
        { "sourceFieldName": "/document/reviews_text/pages/*/Keyphrases/*", "targetFieldName": "Keyphrases" },
        { "sourceFieldName": "/document/Language", "targetFieldName": "Language" },
        { "sourceFieldName": "/document/reviews_text/pages/*/Sentiment", "targetFieldName": "Sentiment" }
    ]
}
```

## <a name="check-status"></a>状態の確認

各要求を送信すると、検索サービスは 201 成功メッセージで応答します。 エラーを受け取った場合は、変数を再確認し、検索サービスに新しいインデックス、インデクサー、データ ソース、スキルセット用のスペースがあることを確認してください (Free レベルでは、それぞれ 3 つずつに制限されています)。

Azure portal で、Azure Cognitive Search サービスの **[Overview]\(概要\)** ページに移動します。 **[Indexers]\(インデクサー\)** タブを選択し、 **[hotels-reviews-ixr]** を選択します。 1、2 分以内に、状態が "進行中" から "成功" になり、エラーと警告数がゼロになります。

## <a name="check-tables-in-storage-browser"></a>ストレージ ブラウザーでテーブルを確認する

Azure portal で Azure Storage アカウントに切り替え、**ストレージ ブラウザー** を使用して新しいテーブルを表示します。 スキルセットに定義されているプロジェクションごとに 1 つずつ、6 つのテーブルが表示されます。

各テーブルは、クエリ内のテーブルをクロスリンクするために必要な ID を使用して生成されます。 テーブルを開いたときに、これらのフィールドの下までスクロールして、パイプラインによって追加されたコンテンツ フィールドを表示します。

   :::image type="content" source="media/knowledge-store-create-portal/azure-table-hotel-reviews.png" alt-text="ストレージ ブラウザーのナレッジ ストア テーブルのスクリーンショット" border="true":::

このチュートリアルでは、ナレッジ ストアは、テーブルのさまざまな整形と構造化方法を示すさまざまなテーブルで構成されています。 テーブル 1 から 3 は、Shaper スキルの出力を使用して列と行を決定します。 テーブル 4 から 6 は、プロジェクション自体に埋め込まれたインライン整形命令から作成されます。 どちらの方法を使用しても、同じ結果を得られます。

| テーブル | 説明 |
|-------|-------------|
| hotelReviews1Document | CSV から引き継がれたフィールド (reviews_date や reviews_text など) が含まれます。 |
| hotelReviews2Pages | スキルセットによって作成された、エンリッチされたフィールド (センチメント スコアや翻訳されたテキストなど) が含まれます。 |
| hotelReviews3KeyPhrases | キー フレーズのみの長い一覧が含まれます。 |
| hotelReviews4InlineProjectionDocument | 最初のテーブルの代わりに、Shaper スキルではなくインライン整形を使用して、プロジェクションのデータを整形します。 |
| hotelReviews5InlineProjectionPages | 2 番目のテーブルの代わりに、インライン整形を使用します。 |
| hotelreviews6InlineProjectionKeyPhrases | 3 番目のテーブルの代わりに、インライン整形を使用します。 |

## <a name="clean-up"></a>クリーンアップ

独自のサブスクリプションを使用している場合は、プロジェクトの最後に、作成したリソースがまだ必要かどうかを確認してください。 リソースを実行したままにすると、お金がかかる場合があります。 リソースは個別に削除することも、リソース グループを削除してリソースのセット全体を削除することもできます。

ポータルの左側のナビゲーション ウィンドウにある **[All resources]\(すべてのリソース\)** または **[Resource groups]\(リソース グループ\)** リンクを使って、リソースを検索および管理できます。

無料サービスを使っている場合は、3 つのインデックス、インデクサー、およびデータソースに制限されることに注意してください。 ポータルで個別の項目を削除して、制限を超えないようにすることができます。

> [!TIP]
> この演習を繰り返したい場合や、別の AI エンリッチメントのチュートリアルを試したい場合は、**hotel-reviews-idxr** インデクサーと関連オブジェクトを削除し、再作成してください。 インデクサーを削除すると、1 日あたりの無料トランザクションのカウンターがリセットされ、ゼロに戻ります。

## <a name="next-steps"></a>次のステップ

Cognitive Services を使用してデータをエンリッチし、その結果をナレッジ ストアに投影したので、ストレージ ブラウザーや他のアプリを使用して、エンリッチしたデータ セットを探索できます。

ストレージ ブラウザーを使用してこのナレッジ ストアを調べる方法については、次のチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [ストレージ ブラウザーを使用して表示する](knowledge-store-view-storage-explorer.md)