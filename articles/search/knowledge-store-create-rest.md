---
title: REST を使用してナレッジ ストアを作成する - Azure Search
description: REST API と Postman を使用して Azure Search のナレッジ ストアを作成し、コグニティブ検索パイプラインからのエンリッチメントを永続化します。
author: lobrien
services: search
ms.service: search
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: laobri
ms.openlocfilehash: 26dc66474eecffd7f5a34bcfcaf93fd49f59606c
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936511"
---
# <a name="create-an-azure-search-knowledge-store-using-rest"></a>REST を使用して Azure Search のナレッジ ストアを作成する

ナレッジ ストアは、Azure Search の機能の 1 つです。AI エンリッチメント パイプラインからの出力を、後から下流で行われる分析などの処理に使用できるよう永続化するものです。 AI エンリッチ パイプラインは、画像ファイルや構造化されていないテキスト ファイルを受け取って、Azure Search を使用してそのインデックスを作成し、Cognitive Services の AI エンリッチメント (画像分析、自然言語処理など) を適用したうえで、Azure ストレージ内のナレッジ ストアにその結果を保存します。 その後、Power BI や Storage Explorer などのツールを使用してナレッジ ストアを探索することができます。

この記事では、REST API インターフェイスを使用して AI エンリッチメントを取り込み、インデックスを作成して、一連のホテルのレビューに適用します。 ホテルのレビューは Azure Blob Storage にインポートされ、その結果がナレッジ ストアとして Azure Table Storage に保存されます。

ナレッジ ストアを作成したら、[Storage Explorer](knowledge-store-view-storage-explorer.md) または [Power BI](knowledge-store-connect-power-bi.md) を使用してそのナレッジ ストアにアクセスする方法について学習できます。

## <a name="1---create-services"></a>1 - サービスを作成する

+ [Azure Search サービスを作成](search-create-service-portal.md)するか、現在のサブスクリプションから[既存のサービスを見つけます](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 このチュートリアル用には、無料のサービスを使用できます。

+ サンプル データとナレッジ ストアを格納するための [Azure ストレージ アカウントを作成](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)します。 ストレージ アカウントには、自分の Azure Search サービスと同じ場所 (米国西部など) を使用する必要があります。 "*アカウントの種類*" は、"*StorageV2 (汎用 V2)* " (既定) または "*Storage (汎用 V1)* " にする必要があります。

+ 推奨:Azure Search に要求を送信するための [Postman デスクトップ アプリ](https://www.getpostman.com/)。 REST API は、HTTP 要求および応答を操作できる任意のツールで使用できます。 この記事では、REST API の探索に適している Postman を使用します。 さらに、この記事の[ソース コード](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json)には、Postman の要求のコレクションが含まれています。 

## <a name="2---store-the-data"></a>2 - データを格納する

Azure Search のインデクサーからホテルのレビューにアクセスして AI エンリッチメント パイプラインを通じて取り込むことができるよう、その CSV ファイルを Azure Blob Storage に読み込みます。

### <a name="create-an-azure-blob-container-with-the-data"></a>データを含んだ Azure BLOB コンテナーを作成する

1. [CSV ファイルに保存されたホテルのレビュー データ (HotelReviews_Free.csv) をダウンロード](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)します。 このデータはホテルに関する顧客のフィードバックを含んでおり、出典は Kaggle.com です。
1. [Azure portal にサインイン](https://portal.azure.com)し、お使いの Azure ストレージ アカウントに移動します。
1. [BLOB コンテナーを作成します](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)。 コンテナーを作成するには、お使いのストレージ アカウントの左側のナビゲーション バーで **[BLOB]** をクリックし、コマンド バーの **[+ コンテナー]** をクリックします。
1. 新しいコンテナーの **[名前]** に「`hotel-reviews`」と入力します。
1. 任意の **[パブリック アクセス レベル]** を選択します。 今回使用したのは既定値です。
1. **[OK]** をクリックして Azure BLOB コンテナーを作成します。
1. 新しい `hotels-review` コンテナーを開いて **[アップロード]** をクリックし、最初の手順でダウンロードした **HotelReviews-Free.csv** ファイルを選択します。

    ![データをアップロードする](media/knowledge-store-create-portal/upload-command-bar.png "ホテルのレビューをアップロードする")

1. **[アップロード]** をクリックして CSV ファイルを Azure Blob Storage にインポートします。 新しいコンテナーが表示されます。

    ![Azure BLOB コンテナーを作成する](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Azure BLOB コンテナーを作成する")

## <a name="3---configure-postman"></a>3 - Postman を構成する

[Postman コレクションのソース コード](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json)をダウンロードし、 **[File, Import...]\(ファイル、インポート\)** を使用して Postman にインポートします。 **[Collections]\(コレクション\)** タブに切り替えて、 **[...]** ボタンをクリックし、 **[Edit]\(編集\)** を選択します。 

![ナビゲーションを示す Postman アプリ](media/knowledge-store-create-rest/postman-edit-menu.png "Postman の [Edit]\(編集\) メニューに移動")

結果として表示される編集ダイアログで、 **[Variables]\(変数\)** タブに移動します。 

**[Variables]\(変数\)** では、二重ブレースで囲まれた値が検出されるたびに Postman によってスワップされる値を追加できます。 たとえば、Postman は記号 `{{admin-key}}` を `admin-key` の "現在の値" に置き換えます。 Postman では、URL、ヘッダー、要求本文などでこの置換を行います。 

`admin-key` の値は、Search Service の **[キー]** タブで見つかります。`search-service-name` と `storage-account-name` は、[手順 1](#1---create-services) で選択した値に変更する必要があります。 `storage-connection-string` は、ストレージ アカウントの **[アクセス キー]** タブの値に設定します。その他の値は変更しなくてもかまいません。

![Postman アプリの変数タブ](media/knowledge-store-create-rest/postman-variables-window.png "Postman の変数ウインドウ")


| 変数    | 情報の入手元 |
|-------------|-----------------|
| `admin-key` | Search Service の **[キー]** タブ              |
| `api-version` | "2019-05-06-Preview" のまま |
| `datasource-name` | "hotel-reviews-ds" のまま | 
| `indexer-name` | "hotel-reviews-ixr" のまま | 
| `index-name` | "hotel-reviews-ix" のまま | 
| `search-service-name` | Search Service の実際の名前。 URL は `https://{{search-service-name}}.search.windows.net` です | 
| `skillset-name` | "hotel-reviews-ss" のまま | 
| `storage-account-name` | ストレージ アカウントの実際の名前 | 
| `storage-connection-string` | ストレージ アカウントの **[アクセス キー]** タブの **key1** **接続文字列** | 
| `storage-container-name` | "hotel-reviews" のまま | 

### <a name="review-the-request-collection-in-postman"></a>Postman の要求コレクションの確認

ナレッジ ストアを作成するには、4 つの HTTP 要求を発行する必要があります。 

1. インデックスを作成するための PUT 要求。 このインデックスは、Azure Search によって使用され、返されるデータを保持します。
1. データソースを作成するための POST 要求 このデータソースによって、Azure Search の動作がデータとナレッジ ストアのストレージ アカウントに接続されます。 
1. スキルセットを作成するための PUT 要求。 スキルセットでは、自分のデータとナレッジ ストアの構造に適用されるエンリッチメントを指定します。
1. インデクサーを作成するための PUT 要求。 インデクサーを実行すると、データの読み取り、スキルセットの適用、結果の格納が行われます。 この要求は最後に実行する必要があります。

[ソース コード](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json)には、これら 4 つの要求を備えた Postman コレクションが含まれています。 要求を発行するには、Postman で要求のタブに切り替えて、`api-key` および `Content-Type` 要求ヘッダーを追加します。 `api-key` の値を `{{admin-key}}` に設定します。 `Content-type` の値を `application/json` に設定します。 

> [!div class="mx-imgBorder"]
> ![Postman のヘッダーのインターフェイスを示すスクリーンショット](media/knowledge-store-create-rest/postman-headers-ui.png)

> [!Note]
> すべての要求で `api-key` および `Content-type` ヘッダーを設定する必要があります。 Postman によって認識された変数は、スクリーンショット内の `{{admin-key}}` のようにオレンジ色で表示されます。 変数にスペルミスがある場合は赤字で表示されます。
>

## <a name="4---create-an-azure-search-index"></a>4 - Azure Search インデックスを作成する

検索、フィルター処理、強化の対象となるデータを表す Azure Search インデックスを作成する必要があります。 PUT 要求を `https://{{search-service-name}}.search.windows.net/indexes/{{index-name}}?api-version={{api-version}}` に発行して、インデックスを作成します。 Postman によって、二重ブレースで囲まれた記号 (`{{search-service-name}}`、`{{index-name}}`、`{{api-version}}` など) が、[手順 3](#3---configure-postman) で指定した値に置き換えられます。 別のツールを使用して REST コマンドを発行する場合は、これらの値をご自分で置き換える必要があります。

要求の本文で自分の Azure Search インデックスの構造を指定します。 Postman で、`api-key` および `Content-type` ヘッダーを設定した後、要求の **[Body]\(本文\)** ウィンドウに切り替えます。 次の JSON が表示されます。表示されない場合は、 **[Raw]** および **[JSON (application/json)]** を選択し、本文として次のコードを貼り付けます。

```JSON
{
    "name": "{{index-name}}",
    "fields": [
        { "name": "address", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "categories", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "city", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "country", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "latitude", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "longitude", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "name", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "postalCode", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "province", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_date", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_dateAdded", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_rating", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_text", "type": "Edm.String", "filterable": false,  "sortable": false, "facetable": false },
        { "name": "reviews_title", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_username", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "AzureSearch_DocumentKey", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false, "key": true },
        { "name": "metadata_storage_content_type", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_size", "type": "Edm.Int64", "searchable": false, "filterable": false, "sortable": false, "facetable": false},
        { "name": "metadata_storage_last_modified", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_name", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_path", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "Sentiment", "type": "Collection(Edm.Double)", "searchable": false, "filterable": true, "retrievable": true, "sortable": false, "facetable": true },
        { "name": "Language", "type": "Edm.String", "filterable": true, "sortable": false, "facetable": true },
        { "name": "Keyphrases", "type": "Collection(Edm.String)", "filterable": true, "sortable": false, "facetable": true }
    ]
}

```

このインデックス定義は、ユーザーに提示したいデータ (ホテルの名前、レビューの内容、日付など)、検索メタデータ、AI 強化データ (センチメント、キーフレーズ、言語) の組み合わせであることがわかります。

**[Send]\(送信\)** ボタンを押して、PUT 要求を発行します。 `201 - Created` という状態メッセージが表示されます。 異なる状態を受け取る場合、 **[Body]\(本文\)** ウィンドウに、エラー メッセージと共に JSON 応答が表示されます。 

## <a name="5---create-the-datasource"></a>5 - データソースを作成する

次に、[手順 2](#2---store-the-data) で格納したホテルのデータに Azure Search を接続する必要があります。 データソースの作成は、`https://{{search-service-name}}.search.windows.net/datasources?api-version={{api-version}}` への POST によって行われます。 ここでも、前に指定したように `api-key` および `Content-Type` ヘッダーを設定する必要があります。 

Postman で、[Create Datasource]\(データソースの作成\) 要求を開きます。 **[Body]\(本文\)** ウィンドウに切り替えると、次のコードが表示されます。

```json
{
  "name" : "{{datasource-name}}",
  "description" : "Demo files to demonstrate knowledge store capabilities.",
  "type" : "azureblob",
  "credentials" : { "connectionString" : "{{storage-connection-string}}" },
  "container" : { "name" : "{{storage-container-name}}" }
}
```

**[Send]\(送信\)** ボタンを押して、POST 要求を発行します。 

## <a name="6---create-the-skillset"></a>6 - スキルセットを作成する 

次の手順ではスキルセットを指定します。これにより、適用される強化と、結果が格納されるナレッジ ストアの両方が指定されます。 Postman で、[Create the Skillset]\(スキルセットの作成\) タブを開きます。この要求では、PUT が `https://{{search-service-name}}.search.windows.net/skillsets/{{skillset-name}}?api-version={{api-version}}` に送信されます。
前に行ったように `api-key` および `Content-type` ヘッダーを設定します。 

最上位レベルに大きなオブジェクトが 2 つあります (`"skills"` と `"knowledgeStore"`)。 `"skills"` オブジェクト内の各オブジェクトは、エンリッチメント サービスです。 各エンリッチメント サービスには、`"inputs"` と `"outputs"` があります。 `LanguageDetectionSkill` に、`targetName` が `"Language"` の出力があることに注目してください。 このノードの値は、他のほとんどのスキルで入力として使用されます (source ＝ `document/Language`)。 あるノードの出力を別のノードの入力として使用するこの機能は、`ShaperSkill` でより顕著です。ここでは、ナレッジ ストアのテーブルに向かうデータの流れを指定します。

`"knowledge_store"` オブジェクトは、`{{storage-connection-string}}` Postman 変数を介してストレージ アカウントに接続します。 次に、強化されたドキュメントと、ナレッジ ストア自体で使用できるようになるテーブルおよび列との間の一連のマッピングを含みます。 

スキルセットを生成するには、Postman で **[Send]\(送信\)** ボタンを押して、要求を PUT します。

```json
{
    "name": "{{skillset-name}}",
    "description": "Skillset to detect language, extract key phrases, and detect sentiment",
    "skills": [ 
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill", 
            "context": "/document/reviews_text", "textSplitMode": "pages", "maximumPageLength": 5000,
            "inputs": [ 
                { "name": "text", "source": "/document/reviews_text" },
                { "name": "languageCode", "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "textItems", "targetName": "pages" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode", "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "score", "targetName": "Sentiment" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "context": "/document",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text" }
            ],
            "outputs": [
                { "name": "languageCode", "targetName": "Language" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text",  "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode",  "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "keyPhrases" , "targetName": "Keyphrases" }
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
                { "name": "AzureSearch_DocumentKey",  "source": "/document/AzureSearch_DocumentKey" },
                { 
                    "name": "pages",
                    "sourceContext": "/document/reviews_text/pages/*",
                    "inputs": [
                        { "name": "SentimentScore", "source": "/document/reviews_text/pages/*/Sentiment" },
                        { "name": "LanguageCode", "source": "/document/Language" },
                        { "name": "Page", "source": "/document/reviews_text/pages/*" },
                        { 
                            "name": "keyphrase", "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                            "inputs": [
                                { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/Keyphrases/*" }
                            ]
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
                    { "tableName": "hotelReviewsDocument", "generatedKeyName": "Documentid", "source": "/document/tableprojection" },
                    { "tableName": "hotelReviewsPages", "generatedKeyName": "Pagesid", "source": "/document/tableprojection/pages/*" },
                    { "tableName": "hotelReviewsKeyPhrases", "generatedKeyName": "KeyPhrasesid", "source": "/document/tableprojection/pages/*/keyphrase/*" },
                    { "tableName": "hotelReviewsSentiment", "generatedKeyName": "Sentimentid", "source": "/document/tableprojection/pages/*/sentiment/*" }
                ],
                "objects": []
            },
            {
                "tables": [
                    { 
                        "tableName": "hotelReviewsInlineDocument", "generatedKeyName": "Documentid", "sourceContext": "/document",
                        "inputs": [
                            { "name": "name", "source": "/document/name"},
                            { "name": "reviews_date", "source": "/document/reviews_date"},
                            { "name": "reviews_rating", "source": "/document/reviews_rating"},
                            { "name": "reviews_text", "source": "/document/reviews_text"},
                            { "name": "reviews_title", "source": "/document/reviews_title"},
                            { "name": "AzureSearch_DocumentKey", "source": "/document/AzureSearch_DocumentKey" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviewsInlinePages", "generatedKeyName": "Pagesid", "sourceContext": "/document/reviews_text/pages/*",
                        "inputs": [
                            { "name": "SentimentScore", "source": "/document/reviews_text/pages/*/Sentiment"},
                            { "name": "LanguageCode", "source": "/document/Language"},
                            { "name": "Page", "source": "/document/reviews_text/pages/*" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviewsInlineKeyPhrases", "generatedKeyName": "kpidv2", "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                        "inputs": [
                            { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/Keyphrases/*" }
                        ]
                    }
                ],
                "objects": []
            }
        ]
    }
}
```

## <a name="7---create-the-indexer"></a>7 - インデクサーを作成する

最後の手順ではインデクサーを作成します。これにより、データが実際に読み取られ、スキルセットがアクティブ化されます。 Postman で、[Create Indexer]\(インデクサーの作成\) 要求に切り替えて、本文を確認します。 ご覧のとおり、これまでに作成したいくつかの他のリソース (データソース、インデックス、スキルセット) がインデクサーの定義によって参照されます。 

`"parameters/configuration"` オブジェクトでは、インデクサーがデータを取り込む方法を制御します。 ここでは、入力データは、ヘッダー行とコンマ区切り値がある単一のドキュメントに含まれています。 ドキュメント キーはドキュメントの一意識別子です。エンコード前、これはソース ドキュメントの URL です。 最後に、スキルセットの出力値 (言語コード、センチメント、キー フレーズなど) が、ドキュメントの適切な場所にマッピングされます。 `Language` に単一の値があるのに対して、`Sentiment` は `pages` の配列の各要素に適用されることに注意してください。 `Keyphrases` はそれ自体が配列です。これも `pages` 配列の各要素に適用されます。

`api-key` および `Content-type` ヘッダーを設定して、要求の本文が後ろのソース コードと類似していることを確認したら、Postman で **[Send]\(送信\)** を押します。 Postman によって、要求が `https://{{search-service-name}}.search.windows.net/indexers/{{indexer-name}}?api-version={{api-version}}` に PUT されます。 Azure Search によってインデクサーが作成され、実行されます。 

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

## <a name="8---run-the-indexer"></a>8 - インデクサーを実行する 

Azure portal で Search Service の **[概要]** に移動して、 **[インデクサー]** タブを選択します。前の手順で作成した **hotels-reviews-ixr** をクリックします。 インデクサーがまだ実行されていない場合は、 **[実行]** ボタンを押してください。 インデックス作成タスクでは、言語認識に関する警告が表示される場合があります。これは、コグニティブ スキルによってまだサポートされていない言語で書かれたレビューが一部含まれているためです。 

## <a name="next-steps"></a>次の手順

Cognitive Services を使用してデータをエンリッチし、その結果をナレッジ ストアに投影したら、エンリッチ済みのデータ セットを Storage Explorer または Power BI を使用して探索することができます。

Storage Explorer を使用してこのナレッジ ストアを探索する方法については、次のチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [Storage Explorer を使用した表示](knowledge-store-view-storage-explorer.md)

このナレッジ ストアを Power BI に接続する方法については、次のチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [Power BI を使用した接続](knowledge-store-connect-power-bi.md)

ここに示した手順をもう一度やってみたい場合や、別の AI エンリッチメントに関するチュートリアルに挑戦してみたい場合には、*hotel-reviews-idxr* インデクサーを削除してください。 インデクサーを削除すると、1 日あたりの無料トランザクションのカウンターがリセットされ、ゼロに戻ります。
