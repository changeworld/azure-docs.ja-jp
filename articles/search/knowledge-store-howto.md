---
title: ナレッジ ストアの使用を開始する方法 (プレビュー) - Azure Search
description: Azure Search 内で AI インデックス作成パイプラインによって作成されるエンリッチメントされたドキュメントを Azure ストレージ アカウント内のナレッジ ストアに送信する手順について説明します。 そこから、Azure Search および他のアプリケーション内のエンリッチメントされたドキュメントを表示、整形、および使用することができます。
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: heidist
ms.openlocfilehash: 2a904cfb049af413887798c8aab449561bc2b73f
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/02/2019
ms.locfileid: "65030050"
---
# <a name="how-to-get-started-with-knowledge-store"></a>ナレッジ ストアの使用を開始する方法

[ナレッジ ストア](knowledge-store-concept-intro.md)は、Azure Search 内の新しいプレビュー機能であり、他のアプリ内のナレッジ マイニング用にインデックス作成パイプラインに作成された AI エンリッチメントを保存します。 保存されているエンリッチメントを使用して、Azure Search インデックス作成パイプラインを理解し、絞り込むこともできます。

ナレッジ ストアは、スキルセットによって定義されます。 Azure Search の通常のフルテキスト検索シナリオでは、スキルセットの目的は、コンテンツをより検索しやすくする AI エンリッチメントを提供することです。 ナレッジ ストアのシナリオでは、スキルセットの役割は、ナレッジ マイニングのために複数のデータ構造を作成し、設定することです。

この演習では、サンプル データ、サービス、およびツールから開始して、スキルセット定義に重点を置いて、最初のナレッジ ストアの作成と使用の基本的なワークフローを確認します。

## <a name="prerequisites"></a>前提条件

このクイック スタートでは、次のサービス、ツール、およびデータを使用します。 

+ [Azure Search サービスを作成](search-create-service-portal.md)するか、現在のサブスクリプションから[既存のサービスを見つけます](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 このチュートリアル用には、無料のサービスを使用できます。 

+ サンプル データの格納のための [Azure ストレージ アカウントを作成](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)します。 ナレッジ ストアは、Azure ストレージ内に存在します。

+ [Cognitive Services リソースを](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) S0 従量課金制レベルで作成し、AI エンリッチメント内で使用されるさまざまなスキルに幅広くアクセスできるようにします。

+ Azure Search に要求を送信するための [Postman デスクトップ アプリ](https://www.getpostman.com/)。

+ データ ソース、インデックス、スキルセット、およびインデクサーを作成するための要求が準備された [Postman コレクション](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/caselaw)。 いくつかのオブジェクト定義は、この記事に含めるには長すぎます。 インデックスとスキルセットの定義の全体を表示するには、このコレクションを取得する必要があります。

+ [Caselaw アクセス プロジェクト](https://case.law/bulk/download/)の [Public Bulk Data download]\(パブリック一括データのダウンロード\) ページから取得した [Caselaw サンプル データ](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw)。 具体的には、演習では最初のダウンロード (アーカンソー) の最初の 10 個のドキュメントを使用します。 この演習用に 10 ドキュメントのサンプルを GitHub にアップロードしました。

## <a name="get-a-key-and-url"></a>キーと URL を入手する

REST 呼び出しには、要求ごとにサービス URL とアクセス キーが必要です。 両方を使用して検索サービスが作成されるので、Azure Search をサブスクリプションに追加した場合は、次の手順に従って必要な情報を入手してください。

1. [Azure portal にサインイン](https://portal.azure.com/)し、ご使用の検索サービスの **[概要]** ページで、URL を入手します。 たとえば、エンドポイントは `https://mydemo.search.windows.net` のようになります。

1. **[設定]** > **[キー]** で、サービスに対する完全な権限の管理者キーを取得します。 管理キーをロールオーバーする必要がある場合に備えて、2 つの交換可能な管理キーがビジネス継続性のために提供されています。 オブジェクトの追加、変更、および削除の要求には、主キーまたはセカンダリ キーのどちらかを使用できます。

    ![HTTP エンドポイントとアクセス キーを取得する](media/search-fiddler/get-url-key.png "HTTP エンドポイントとアクセス キーを取得する")

すべての要求では、サービスに送信されるすべての要求に API キーが必要です。

## <a name="prepare-sample-data"></a>サンプル データの準備

1. [Azure portal にサインインし](https://portal.azure.com)、Azure ストレージ アカウントに移動して **[BLOB]** をクリックし、**[+ コンテナー]** をクリックします。

1. [BLOB コンテナーを作成](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)してサンプル データを含めます。 パブリック アクセス レベルは、有効な任意の値に設定できます。

1. コンテナーが作成されたら、コンテナーを開いてコマンド バーの **[アップロード]** を選択します。

   ![コマンド バーの [アップロード]](media/search-semi-structured-data/upload-command-bar.png "コマンド バーの [アップロード]")

1. **caselaw-sample.json** サンプル ファイルを含むフォルダーに移動します。 ファイルを選択し、**[アップロード]** をクリックします。


## <a name="set-up-postman"></a>Postman の設定

Postman を開始し、HTTP 要求を設定します。 このツールに慣れていない場合は、[Postman を使用して Azure Search REST API を調べる方法](search-fiddler.md)に関するページを参照してください。

+ このチュートリアルでの各呼び出しの要求メソッドは **POST** です。
+ 要求ヘッダー (2) には以下が含まれます:"application/json" に設定された "Content-type"、お使いの "admin key" に設定された "api-key" (admin key は、検索の主キーを表すプレースホルダーです)。 
+ 要求本文は、呼び出しの実際のコンテンツを配置する場所です。 

  ![半構造化検索](media/search-semi-structured-data/postmanoverview.png)

Postman を使用して、検索サービスに対して 4 つの API 呼び出しを行い、データ ソース、インデックス、スキルセット、インデクサーを作成します。 データ ソースには、ストレージ アカウントと JSON データへのポインターが含まれています。 データのインポート時に、検索サービスは接続を行います。

[スキルセットの作成](#create-skillset)は、このチュートリアルの焦点です。これにより、エンリッチメント手順と、ナレッジ ストアにデータを永続化する方法を指定します。

URL エンドポイントでは API バージョンを指定する必要があり、各呼び出しで **201 Created** が返される必要があります。 ナレッジ ストアがサポートされるスキルセットを作成するためのプレビュー API バージョンは `2019-05-06-Preview` です。

お使いの REST クライアントから次の API 呼び出しを実行します。

## <a name="create-a-data-source"></a>データ ソースを作成する

[データ ソースの作成 API](https://docs.microsoft.com/rest/api/searchservice/create-data-source) では、インデックス作成の対象データを指定する Azure Search オブジェクトが作成されます。

この呼び出しのエンドポイントは `https://[service name].search.windows.net/datasources?api-version=2019-05-06-Preview` です 

1. `[service name]` を検索サービスの名前に置き換えます。 

2. この呼び出しでは、要求本文にストレージ アカウントの接続文字列と BLOB コンテナー名を含める必要があります。 接続は、Azure portal のストレージ アカウントの **[アクセス キー]** にあります。 

   呼び出しを実行する前に、要求の本文内の接続文字列と BLOB コンテナー名を置換してください。

    ```json
    {
        "name": "caselaw-ds",
        "description": null,
        "type": "azureblob",
        "subtype": null,
        "credentials": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your storage key>;EndpointSuffix=core.windows.net"
        },
        "container": {
            "name": "<your blob container name>",
            "query": null
        },
        "dataChangeDetectionPolicy": null,
        "dataDeletionDetectionPolicy": null
    }
    ```

3. 要求を送信します。 応答は **201** である必要があり、応答本文は指定した要求ペイロードとほぼ同じである必要があります。

    ```json
    {
        "name": "caselaw-ds",
        "description": null,
        "type": "azureblob",
        "subtype": null,
        "credentials": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your storage key>;EndpointSuffix=core.windows.net"
        },
        "container": {
            "name": "<your blob container name>",
            "query": null
        },
        "dataChangeDetectionPolicy": null,
        "dataDeletionDetectionPolicy": null
    }
    ```

## <a name="create-an-index"></a>インデックスを作成する
    
2 つ目の呼び出しは[インデックスの作成 API](https://docs.microsoft.com/rest/api/searchservice/create-data-source) であり、検索可能なすべてのデータを格納する Azure Search インデックスを作成します。 インデックスでは、すべてのフィールド、パラメーター、属性を指定します。

ナレッジ マイニングにインデックスは必ずしも必要ではありませんが、インデックスを指定しないとインデクサーは実行されません。 

この呼び出しの URL は `https://[service name].search.windows.net/indexes?api-version=2019-05-06-Preview` です

1. `[service name]` を検索サービスの名前に置き換えます。

2. Postman コレクション内のインデックスの作成要求から要求本文にインデックス定義をコピーします。 インデックス定義は数百行あり、ここに出力するには長すぎます。 

   インデックスの外側のシェルは、次の要素で構成されます。 

   ```json
   {
      "name": "caselaw",
      "defaultScoringProfile": null,
      "fields": [],
      "scoringProfiles": [],
      "corsOptions": null,
      "suggesters": [],
      "analyzers": [],
      "tokenizers": [],
      "tokenFilters": [],
      "charFilters": [],
      "encryptionKey": null
   }
   ```

3. `fields` コレクションには、インデックス定義の大部分が含まれています。 これには、単純なフィールド、入れ子になったサブ構造体を持つ[複合型フィールド](search-howto-complex-data-types.md)、およびコレクションが含まれます。

   302 から 384 行目で、`casebody` のフィールド定義を確認します。 複合型フィールドでは、階層表現が必要な場合に他の複合型フィールドを含めることができることに注意してください。

   ```json
   {
    "name": "casebody",
    "type": "Edm.ComplexType",
    "fields": [
        {
            "name": "status",
            "type": "Edm.String",
            "searchable": true,
            "filterable": true,
            "retrievable": true,
            "sortable": true,
            "facetable": true,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        {
            "name": "data",
            "type": "Edm.ComplexType",
            "fields": [
                {
                    "name": "head_matter",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": false,
                    "retrievable": true,
                    "sortable": false,
                    "facetable": false,
                    "key": false,
                    "indexAnalyzer": null,
                    "searchAnalyzer": null,
                    "analyzer": null,
                    "synonymMaps": []
                },
                {
                    "name": "opinions",
                    "type": "Collection(Edm.ComplexType)",
                    "fields": [
                        {
                            "name": "author",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": true,
                            "retrievable": true,
                            "sortable": false,
                            "facetable": true,
                            "key": false,
                            "indexAnalyzer": null,
                            "searchAnalyzer": null,
                            "analyzer": null,
                            "synonymMaps": []
                        },
                        {
                            "name": "text",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": false,
                            "retrievable": true,
                            "sortable": false,
                            "facetable": false,
                            "key": false,
                            "indexAnalyzer": null,
                            "searchAnalyzer": null,
                            "analyzer": null,
                            "synonymMaps": []
                        },
                        {
                            "name": "type",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": true,
                            "retrievable": true,
                            "sortable": false,
                            "facetable": true,
                            "key": false,
                            "indexAnalyzer": null,
                            "searchAnalyzer": null,
                            "analyzer": null,
                            "synonymMaps": []
                        }
                    ]
                },
    . . .
   ```

4. 要求を送信します。 

   応答は **201** である必要があり、最初のいくつかのフィールドを示している次の例のようになります。

    ```json
    {
        "name": "caselaw",
        "defaultScoringProfile": null,
        "fields": [
            {
                "name": "id",
                "type": "Edm.String",
                "searchable": true,
                "filterable": true,
                "retrievable": true,
                "sortable": true,
                "facetable": true,
                "key": true,
                "indexAnalyzer": null,
                "searchAnalyzer": null,
                "analyzer": null,
                "synonymMaps": []
            },
            {
                "name": "name",
                "type": "Edm.String",
                "searchable": true,
                "filterable": true,
                "retrievable": true,
                "sortable": true,
                "facetable": true,
                "key": false,
                "indexAnalyzer": null,
                "searchAnalyzer": null,
                "analyzer": null,
                "synonymMaps": []
            },
      . . .
    ```

<a name="create-skillset"></a>

## <a name="create-a-skillset-and-knowledge-store"></a>スキルセットとナレッジ ストアの作成

[スキルセットの作成 API](https://docs.microsoft.com/rest/api/searchservice/create-skillset) では、Azure Search オブジェクトを作成します。Azure Search オブジェクトでは、呼び出すコグニティブ スキル、スキルをチェーンする方法、およびこのチュートリアルで最も重要な、ナレッジ ストアを指定する方法を指定します。

この呼び出しのエンドポイントは `https://[service name].search.windows.net/skillsets?api-version=2019-05-06-Preview` です

1. `[service name]` を検索サービスの名前に置き換えます。

2. Postman コレクション内のスキルセットの作成要求から要求本文にスキルセット定義をコピーします。 スキルセット定義は数百行あり、ここに出力するには長すぎますが、これはこのチュートリアルの焦点です。

   スキルセットの外側のシェルは、次の要素で構成されます。 `skills` コレクションではメモリ内エンリッチメントを定義しますが、`knowledgeStore` 定義では出力を格納する方法を指定します。 `cognitiveServices` 定義は、AI エンリッチメント エンジンへの接続です。

   ```json
   {
    "name": "caselaw-ss",
    "description": null,
    "skills": [],
    "cognitiveServices": [],
    "knowledgeStore": []
   }
   ```

3. 最初に、`cognitiveServices` および `knowledgeStore` キーと接続文字列を設定します。 この例では、これらの文字列は、スキルセットの定義の後の、要求本文の最後の方に配置されています。

    ```json
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "<your cognitive services resource name>",
        "key": "<your cognitive services key>"
    },
    "knowledgeStore": {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your storage account name>;AccountKey=<your storage account key>;EndpointSuffix=core.windows.net",
    ```

3. スキル コレクションを確認します。特に、85 行目と 170 行目のシェーパー スキルをそれぞれ確認します。 シェーパー スキルは、ナレッジ マイニングに必要なデータ構造をアセンブルするので重要です。 スキルセットの実行中には、これらの構造はメモリ内のみにありますが、次の手順に進むと、この出力をナレッジ ストアに保存してさらに探索する方法がわかります。

   次のスニペットは 207 行目からのものです。 

    ```json
    {
    "name": "Opinions",
    "source": null,
    "sourceContext": "/document/casebody/data/opinions/*",
    "inputs": [
        {
            "name": "Text",
            "source": "/document/casebody/data/opinions/*/text"
        },
        {
            "name": "Author",
            "source": "/document/casebody/data/opinions/*/author"
        },
        {
            "name": "Entities",
            "source": null,
            "sourceContext": "/document/casebody/data/opinions/*/text/pages/*/entities/*",
            "inputs": [
                {
                    "name": "Entity",
                    "source": "/document/casebody/data/opinions/*/text/pages/*/entities/*/value"
                },
                {
                    "name": "EntityType",
                    "source": "/document/casebody/data/opinions/*/text/pages/*/entities/*/category"
                }
             ]
          }
     ]
   }
   . . .
   ```

3. 253 行目から始まる `knowledgeStore` 内の `projections` 要素を確認します。 プロジェクションでは、ナレッジ ストアの構成を指定します。 プロジェクションは、テーブルとオブジェクトのペアで指定されますが、現在は一度に 1 つのみ指定されます。 最初のプロジェクションでわかるように、`tables` が指定されていますが `objects` は指定されていません。 2 番目では、その逆になっています。

   Azure ストレージでは、作成する各テーブルの Table Storage 内にテーブルが作成され、各オブジェクトによって Blob Storage 内のコンテナーが取得されます。

   通常、オブジェクトにはエンリッチメントの完全な式が含まれます。 通常、テーブルには、特定の目的に合わせて準備する組み合わせで、部分的なエンリッチメントが含まれます。 この例は、Cases テーブルを示していますが、Entities、Judges、Opinions などの他のテーブルは示されていません。

    ```json
    "projections": [
    {
        "tables": [
            {
              "tableName": "Opinions",
              "generatedKeyName": "OpinionId",
              "source": "/document/Case/OpinionsSnippets/*"
            },
          . . . 
        ],
        "objects": []
    },
    {
        "tables": [],
        "objects": [
            {
                "storageContainer": "enrichedcases",
                "key": "/document/CaseFull/Id",
                "source": "/document/CaseFull"
            }
          ]
        }
      ]
    }
    ```

5. 要求を送信します。 応答は **201** である必要があり、応答の最初の部分を示している次の例のようになります。

    ```json
    {
    "name": "caselaw-ss",
    "description": null,
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "name": "SplitSkill#1",
            "description": null,
            "context": "/document/casebody/data/opinions/*/text",
            "defaultLanguageCode": "en",
            "textSplitMode": "pages",
            "maximumPageLength": 5000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/casebody/data/opinions/*/text
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        . . .
    ```

## <a name="create-and-run-an-indexer"></a>インデクサーの作成と実行

[インデクサーの作成 API](https://docs.microsoft.com/rest/api/searchservice/create-indexer) では、インデクサーを作成し、すぐに実行します。 これまでに作成したすべての定義は、この手順で実行されます。 インデクサーは、サービス内に存在しないのですぐに実行されます。 存在した後の既存のインデクサーへの POST 呼び出しは更新操作です。

この呼び出しのエンドポイントは `https://[service name].search.windows.net/indexers?api-version=2019-05-06-Preview` です

1. `[service name]` を検索サービスの名前に置き換えます。 

2. この呼び出しでは、要求本文でインデクサー名を指定します。 インデクサーにはデータ ソースとインデックスが必要です。 スキルセットは、インデクサーでは省略可能ですが、AI エンリッチメントには必須です。

    ```json
    {
        "name": "caselaw-idxr",
        "description": null,
        "dataSourceName": "caselaw-ds",
        "skillsetName": "caselaw-ss",
        "targetIndexName": "caselaw",
        "disabled": null,
        "schedule": null,
        "parameters": {
            "batchSize": 1,
            "maxFailedItems": null,
            "maxFailedItemsPerBatch": null,
            "base64EncodeKeys": null,
            "configuration": {
                "parsingMode": "jsonLines"
            }
        },
        "fieldMappings": [],
        "outputFieldMappings": [
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/people/*",
                "targetFieldName": "people",
                "mappingFunction": null
            },
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/organizations/*",
                "targetFieldName": "orginizations",
                "mappingFunction": null
            },
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/locations/*",
                "targetFieldName": "locations",
                "mappingFunction": null
            },
            {
                "sourceFieldName": "/document/Case/OpinionsSnippets/*/Entities/*",
                "targetFieldName": "entities",
                "mappingFunction": null
            },
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/keyPhrases/*",
                "targetFieldName": "keyPhrases",
                "mappingFunction": null
            }
        ]
    }
    ```

3. 要求を送信します。 応答は **201** である必要があり、応答本文は指定した要求ペイロードとほぼ同じである必要があります (簡潔にするために切り取っています)。

    ```json
    {
        "name": "caselaw-idxr",
        "description": null,
        "dataSourceName": "caselaw-ds",
        "skillsetName": "caselaw-ss",
        "targetIndexName": "caselaw",
        "disabled": null,
        "schedule": null,
        "parameters": {
            "batchSize": 1,
            "maxFailedItems": null,
            "maxFailedItemsPerBatch": null,
            "base64EncodeKeys": null,
            "configuration": {
                "parsingMode": "jsonLines"
            }
        },
        "fieldMappings": [],
        "outputFieldMappings": [
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/people/*",
                "targetFieldName": "people",
                "mappingFunction": null
            }
        ]
    }
    ```

## <a name="explore-knowledge-store"></a>ナレッジ ストアの探索

最初のドキュメントがインポートされたらすぐに、検索を始めることができます。 この作業には、ポータル内で [**Storage Explorer**](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer) を使用します。

ナレッジ ストアが Azure Search から完全にデタッチされていることを認識することが重要です。 Azure Search インデックスとナレッジ ストアの両方にデータ表現とコンテンツが含まれますが、そこから分かれています。 フルテキスト検索、フィルター検索、および Azure Search でサポートされるすべてのシナリオにインデックスを使用します。 または、ナレッジ ストアだけを使用して先に進み、他のツールをアタッチしてコンテンツを分析します。

## <a name="takeaways"></a>重要なポイント

Azure ストレージ内に最初のナレッジ ストアを作成し、Storage Explorer を使用してエンリッチメントを表示しました。 これは、ストアド エンリッチメントを操作するための基本的なエクスペリエンスです。 

## <a name="next-steps"></a>次の手順

シェーパー スキルでは、新しいシェイプに結合できるきめ細かなデータ フォームの作成における手間のかかる作業が処理されます。 次の手順として、このスキルのリファレンス ページで使用方法の詳細を確認します。

> [!div class="nextstepaction"]
> [シェーパー スキルのリファレンス](cognitive-search-skill-shaper.md)


<!---
## Keep This

How to convert unformatted JSON into an indented JSON document structure that allows you to quickly identify nested structures. Useful for creating an index that includes complex types.

1. Use Visual Studio Code.
2. Open data.jsonl
--->