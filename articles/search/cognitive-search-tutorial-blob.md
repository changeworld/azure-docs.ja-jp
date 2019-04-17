---
title: チュートリアル:インデックス パイプラインで Cognitive Services APIs を呼び出す - Azure Search
description: JSON BLOB を介したデータの抽出と変換のために Azure Search のインデックス作成で行われる、データ抽出、自然言語、画像の AI 処理の例を段階的に説明していきます。
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 5fbcef1d8bc19df251a4d33cafa2fa7b5a7d9431
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2019
ms.locfileid: "59261923"
---
# <a name="tutorial-call-cognitive-services-apis-in-an-azure-search-indexing-pipeline-preview"></a>チュートリアル:Azure Search のインデックス パイプラインで Cognitive Services APIs を呼び出す (プレビュー)

このチュートリアルでは、*コグニティブ スキル*を使用した Azure Search でのデータ エンリッチメントのプログラミングのしくみを学習します。 複数のスキルが、Cognitive Services の自然言語処理 (NLP) と画像分析機能によって支えられています。 スキルセットを複合および構成することで、画像やスキャンされたドキュメント ファイルのテキストとテキスト表現を抽出できます。 また、言語、エンティティ、キーフレーズなども検出できます。 最終的に、AI で強化されたインデックス パイプラインによって作成された豊富なコンテンツが Azure Search インデックスに追加されます。 

このチュートリアルでは、REST API を呼び出して、次のタスクを実行します。

> [!div class="checklist"]
> * インデックスへのルートでサンプル データをエンリッチする、インデックス作成パイプラインを作成する
> * 組み込みのスキル (エンティティの認識、言語の検出、テキスト操作、キー フレーズ抽出) を適用する
> * スキルセットの出力に入力をマップして、スキルをまとめて連結する方法を学習する
> * 要求を実行し、結果を確認する
> * 将来の開発のためにインデックスとインデクサーをリセットする

出力は、Azure Search のフルテキスト検索可能なインデックスです。 インデックスは、[シノニム](search-synonyms.md)、[スコアリング プロファイル](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)、[アナライザー](search-analyzers.md)、および[フィルター](search-filters.md)などの他の標準的な機能を使って強化できます。

このチュートリアルは無料のサービスで実行されますが、無料のトランザクションの数は 1 日あたり 20 のドキュメントまでに制限されます。 このチュートリアルを同じ日に複数回実行する場合は、より小さなファイル セットを使用して、より多くの実行が制限内に収まるようにします。

> [!NOTE]
> 処理の頻度を増やしたり、ドキュメントを追加したり、AI アルゴリズムを追加したりすることによってスコープを拡大する場合は、請求対象の Cognitive Services リソースをアタッチする必要があります。 Cognitive Services の API を呼び出すとき、および Azure Search のドキュメントクラッキング段階の一部として画像抽出するときに、料金が発生します。 ドキュメントからのテキストの抽出には、料金はかかりません。
>
> 組み込みスキルの実行は、既存の [Cognitive Services の従量課金制の価格](https://azure.microsoft.com/pricing/details/cognitive-services/)で課金されます。 画像抽出の価格は、[Azure Search の価格のページ](https://go.microsoft.com/fwlink/?linkid=2042400)で説明されているように、プレビュー価格で課金されます。 [詳細情報](cognitive-search-attach-cognitive-services.md)。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

[Azure Search サービスを作成](search-create-service-portal.md)するか、現在のサブスクリプションから[既存のサービスを見つけます](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 このチュートリアル用には、無料のサービスを使用できます。

[Postman デスクトップ アプリ](https://www.getpostman.com/)は、Azure Search への REST 呼び出しを行うために使用されます。

### <a name="get-an-azure-search-api-key-and-endpoint"></a>Azure Search api-key とエンドポイントを取得する

REST 呼び出しには、要求ごとにサービス URL とアクセス キーが必要です。 両方を使用して検索サービスが作成されるので、Azure Search をサブスクリプションに追加した場合は、次の手順に従って必要な情報を入手してください。

1. Azure portal の検索サービスの **[概要]** ページで、URL を取得します。 たとえば、エンドポイントは `https://my-service-name.search.windows.net` のようになります。

2. **[設定]** > **[キー]** で、サービスに対する完全な権限の管理者キーを取得します。 管理キーをロールオーバーする必要がある場合に備えて、2 つの交換可能な管理キーがビジネス継続性のために提供されています。 オブジェクトの追加、変更、および削除の要求には、主キーまたはセカンダリ キーのどちらかを使用できます。

![HTTP エンドポイントとアクセス キーを取得する](media/search-fiddler/get-url-key.png "HTTP エンドポイントとアクセス キーを取得する")

すべての要求では、サービスに送信されるすべての要求に API キーが必要です。 有効なキーがあれば、要求を送信するアプリケーションとそれを処理するサービスの間で、要求ごとに信頼を確立できます。

### <a name="set-up-azure-blob-service-and-load-sample-data"></a>Azure BLOB サービスを設定し、サンプル データを読み込む

エンリッチメント パイプラインは、Azure データ ソースから取得されます。 ソース データは、サポートされているデータ ソースの種類の [Azure Search インデクサー](search-indexer-overview.md)から取得する必要があります。 コグニティブ検索では Azure Table Storage はサポートされていないことに注意してください。 この演習では、BLOB ストレージを使用して複数のコンテンツ タイプを示します。

1. さまざまなタイプの小さいファイル セットで構成されている[サンプル データをダウンロード](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4)します。 

1. [Azure Blob Storage にサインアップ](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)してストレージ アカウントを作成します。BLOB サービス ページを開き、コンテナーを作成します。 ストレージ アカウントは、Azure Search と同じリージョンに作成します。

1. 作成したコンテナーで、**[アップロード]** をクリックし、前の手順でダウンロードしたサンプル ファイルをアップロードします。

   ![Azure Blob Storage 内のソース ファイル](./media/cognitive-search-quickstart-blob/sample-data.png)

1. サンプル ファイルが読み込まれたら、BLOB ストレージのコンテナー名と接続文字列を取得します。 これは、Azure portal でストレージ アカウントに移動して実行できます。 **[アクセス キー]** で、**[接続文字列]** フィールドをコピーします。

   接続文字列は次の例のような URL です。

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

共有アクセス署名の提供など、接続文字列を指定する方法は他にもあります。 データ ソースの資格情報の詳細については、「[Azure Blob Storage のインデックスを Azure Search で作成する](search-howto-indexing-azure-blob-storage.md#Credentials)」をご覧ください。

## <a name="create-a-data-source"></a>データ ソースを作成する

これでサービスとソース ファイルの準備ができましたので、インデックス作成パイプラインのコンポーネントのアセンブルを開始します。 Azure Search に外部ソース データの取得方法を指示する、[データ ソース オブジェクト](https://docs.microsoft.com/rest/api/searchservice/create-data-source)から始めます。

このチュートリアルでは、REST API と、PowerShell、Postman、Fiddler などの HTTP 要求を作成および送信できるツールを使用します。 要求ヘッダーには、Azure Search サービスの作成時に使用したサービス名と、Search サービス用に生成した api-key を入力します。 要求本文には、BLOB コンテナー名と接続文字列を指定します。

### <a name="sample-request"></a>要求のサンプル
```http
POST https://[service name].search.windows.net/datasources?api-version=2017-11-11-Preview
Content-Type: application/json
api-key: [admin key]
```
#### <a name="request-body-syntax"></a>要求本文の構文
```json
{
  "name" : "demodata",
  "description" : "Demo files to demonstrate cognitive search capabilities.",
  "type" : "azureblob",
  "credentials" :
  { "connectionString" :
    "DefaultEndpointsProtocol=https;AccountName=<your account name>;AccountKey=<your account key>;"
  },
  "container" : { "name" : "<your blob container name>" }
}
```
要求を送信します。 この Web テスト ツールは、成功を確認する状態コード 201 を返します。 

これは最初の要求のため、Azure Portal を調べて、データ ソースが Azure Search で作成されたことを確認します。 Search サービスのダッシュボード ページで、[データ ソース] タイルに新しい項目があることを確認します。 Portal のページが更新されるまで数分かかる場合があります。 

  ![Portal の [データ ソース] タイル](./media/cognitive-search-tutorial-blob/data-source-tile.png "Portal の [データ ソース] タイル")

403 または 404 エラーが発生した場合は、要求の構造を確認してください。`api-version=2017-11-11-Preview` はエンドポイント上にある必要があり、`api-key` は `Content-Type` の後のヘッダーにある必要があり、その値は Search サービスに対して有効である必要があります。 このチュートリアルでは、残りの手順でこのヘッダーを再利用できます。

## <a name="create-a-skillset"></a>スキルセットを作成する

この手順では、データに適用するエンリッチメント ステップのセットを定義します。 それぞれのエンリッチメント ステップを*スキル*と呼び、エンリッチメント ステップのセットを*スキルセット*と呼びます。 このチュートリアルでは、スキルセット用に次の[定義済みのコグニティブ スキル](cognitive-search-predefined-skills.md)を使用します。

+ [言語検出](cognitive-search-skill-language-detection.md)。コンテンツの言語を識別します。

+ [テキスト分割](cognitive-search-skill-textsplit.md)。キー フレーズの抽出スキルを呼び出す前に、大きいコンテンツを小さいチャンクに分割します。 キー フレーズ抽出は、50,000 文字以下の入力を受け入れます。 いくつかのサンプル ファイルは、分割してこの制限内に収める必要があります。

+ [名前付きエンティティの認識](cognitive-search-skill-named-entity-recognition.md)。BLOB コンテナーのコンテンツから組織の名前を抽出します。

+ [キー フレーズ抽出](cognitive-search-skill-keyphrases.md)。上位のキー フレーズを抜き出します。 

### <a name="sample-request"></a>要求のサンプル
この REST 呼び出しを行う前に、ツールが呼び出しの間で要求ヘッダーを保持しない場合は、下の要求のサービス名と管理者キーを置き換えるようにしてください。 

この要求では、スキルセットが作成されます。 このチュートリアルの残りの部分では、スキル名 ```demoskillset``` を参照します。

```http
PUT https://[servicename].search.windows.net/skillsets/demoskillset?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```
#### <a name="request-body-syntax"></a>要求本文の構文
```json
{
  "description":
  "Extract entities, detect language and extract key-phrases",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text", "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations", "targetName": "organizations"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
      "inputs": [
        {
          "name": "text", "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "languageCode",
          "targetName": "languageCode"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
      "textSplitMode" : "pages",
      "maximumPageLength": 4000,
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        },
        {
          "name": "languageCode",
          "source": "/document/languageCode"
        }
      ],
      "outputs": [
        {
          "name": "textItems",
          "targetName": "pages"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
      "context": "/document/pages/*",
      "inputs": [
        {
          "name": "text", "source": "/document/pages/*"
        },
        {
          "name":"languageCode", "source": "/document/languageCode"
        }
      ],
      "outputs": [
        {
          "name": "keyPhrases",
          "targetName": "keyPhrases"
        }
      ]
    }
  ]
}
```

要求を送信します。 この Web テスト ツールは、成功を確認する状態コード 201 を返します。 

#### <a name="about-the-request"></a>要求について

キー フレーズ抽出スキルが各ページでどのように適用されるかに注意してください。 コンテキストを ```"document/pages/*"``` に設定して、ドキュメント/ページの配列のメンバーごとに (ドキュメントのページごとに) このエンリッチャーを実行します。

各スキルは、ドキュメントのコンテンツで実行されます。 処理中に、Azure Search が各ドキュメントを解読して、さまざまなファイル形式からコンテンツを読み取ります。 ソース ファイルから配信されたテキストが見つかると、ドキュメントごとに 1 つずつ、生成された ```content``` フィールドに配置されます。 そのため、入力を ```"/document/content"``` として設定します。

スキルセットのグラフィカル表示を以下に示します。 

![スキルセットを理解する](media/cognitive-search-tutorial-blob/skillset.png "スキルセットを理解する")

出力はインデックスにマップすることができ、ダウンストリーム スキルへの入力として、または言語コードと同様に両方として使用されます。 インデックスでは、言語コードはフィルター処理に役立ちます。 入力としての言語コードは、テキスト分析スキルによって、単語区切りに基づく言語学的規則を通知するために使用されます。

スキルセットの基礎の詳細については、[スキルセットを定義する方法](cognitive-search-defining-skillset.md)に関するページをご覧ください。

## <a name="create-an-index"></a>インデックスを作成する

このセクションでは、検索可能なインデックス、および各フィールドの検索属性に含めるフィールドを指定することによって、インデックス スキーマを定義します。 フィールドには型があり、フィールドの使用方法 (検索可能、並べ替え可能など) を決定する属性を取ることができます。 インデックス内のフィールド名は、ソース内のフィールド名と完全に一致する必要はありません。 後のほうの手順では、インデクサーにフィールド マッピングを追加して、ソースとターゲットのフィールドを接続します。 この手順で、検索アプリケーションに関連するフィールドの名前付け規則を使用してインデックスを定義します。

この演習では、次のフィールドとフィールドの型を使用します。

| field-names: | `id`       | content   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| field-types: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |


### <a name="sample-request"></a>要求のサンプル
この REST 呼び出しを行う前に、ツールが呼び出しの間で要求ヘッダーを保持しない場合は、下の要求のサービス名と管理者キーを置き換えるようにしてください。 

この要求では、インデックスが作成されます。 このチュートリアルの残りの部分では、インデックス名 ```demoindex``` を使用します。

```http
PUT https://[servicename].search.windows.net/indexes/demoindex?api-version=2017-11-11-Preview
api-key: [api-key]
Content-Type: application/json
```
#### <a name="request-body-syntax"></a>要求本文の構文

```json
{
  "fields": [
    {
      "name": "id",
      "type": "Edm.String",
      "key": true,
      "searchable": true,
      "filterable": false,
      "facetable": false,
      "sortable": true
    },
    {
      "name": "content",
      "type": "Edm.String",
      "sortable": false,
      "searchable": true,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "languageCode",
      "type": "Edm.String",
      "searchable": true,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "keyPhrases",
      "type": "Collection(Edm.String)",
      "searchable": true,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "organizations",
      "type": "Collection(Edm.String)",
      "searchable": true,
      "sortable": false,
      "filterable": false,
      "facetable": false
    }
  ]
}
```
要求を送信します。 この Web テスト ツールは、成功を確認する状態コード 201 を返します。 

インデックスの定義の詳細については、[インデックスの作成 (Azure Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) に関するページをご覧ください。


## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>インデクサーを作成し、フィールドをマップし、変換を実行する

ここまでで、データ ソース、スキルセット、およびインデックスを作成しました。 これら 3 つのコンポーネントが、各要素を複数フェーズにわたる 1 つの操作にまとめて入れる[インデクサー](search-indexer-overview.md)の一部になります。 これらをまとめてインデクサーに結び付けるには、フィールド マッピングを定義する必要があります。 フィールド マッピングはインデクサーの定義の一部であり、要求の送信時に変換を実行します。

エンリッチされていないインデックス作成では、フィールド名やデータ型が正確に一致しない場合、または関数を使用する場合は、インデクサーの定義により省略可能な *fieldMappings* セクションが指定されます。

エンリッチメント パイプラインのあるコグニティブ検索ワークロードでは、インデクサーは *outputFieldMappings* を必要とします。 これらのマッピングは、内部プロセス (エンリッチメント パイプライン) がフィールド値のソースである場合に使用されます。 *outputFieldMappings* に固有の動作には、(シェーパー スキルを使用して) エンリッチメントの一環として作成される複合型を処理する機能が含まれます。 また、ドキュメントごとに多数の要素があることがあります (たとえば、ドキュメント内の複数の組織)。 *outputFieldMappings* コンストラクトでは、要素のコレクションを 1 つのレコードに「フラット化」するよう、システムに指示できます。

### <a name="sample-request"></a>要求のサンプル

この REST 呼び出しを行う前に、ツールが呼び出しの間で要求ヘッダーを保持しない場合は、下の要求のサービス名と管理者キーを置き換えるようにしてください。 

また、インデクサーの名前も入力してください。 このチュートリアルの残りの部分では、これを ```demoindexer``` として参照できます。

```http
PUT https://[servicename].search.windows.net/indexers/demoindexer?api-version=2017-11-11-Preview
api-key: [api-key]
Content-Type: application/json
```
#### <a name="request-body-syntax"></a>要求本文の構文

```json
{
  "name":"demoindexer", 
  "dataSourceName" : "demodata",
  "targetIndexName" : "demoindex",
  "skillsetName" : "demoskillset",
  "fieldMappings" : [
    {
      "sourceFieldName" : "metadata_storage_path",
      "targetFieldName" : "id",
      "mappingFunction" :
        { "name" : "base64Encode" }
    },
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
    {
      "sourceFieldName" : "/document/pages/*/keyPhrases/*",
      "targetFieldName" : "keyPhrases"
    },
    {
      "sourceFieldName": "/document/languageCode",
      "targetFieldName": "languageCode"
    }
  ],
  "parameters":
  {
    "maxFailedItems":-1,
    "maxFailedItemsPerBatch":-1,
    "configuration":
    {
      "dataToExtract": "contentAndMetadata",
      "imageAction": "generateNormalizedImages"
    }
  }
}
```

要求を送信します。 この Web テスト ツールは、処理の成功を確認する状態コード 201 を返します。 

この手順は完了までに数分かかる予定です。 データ セットが小さい場合でも、分析スキルは計算を集中的に行います。 画像分析などの一部のスキルは、実行時間の長いスキルです。

> [!TIP]
> インデクサーを作成すると、パイプラインが呼び出されます。 データ、入力と出力のマッピング、または操作の順序に及ぶ問題がある場合は、この段階で現れます。 コードまたはスクリプトに変更を加えてパイプラインを再実行するには、最初にオブジェクトを削除する必要がある場合があります。 詳細については、「[リセットして再実行する](#reset)」をご覧ください。

### <a name="explore-the-request-body"></a>要求本文を調べる

スクリプトで ```"maxFailedItems"``` を -1 に設定します。これにより、インデックス作成エンジンに、データのインポート中のエラーを無視するよう指示します。 デモのデータ ソースにはドキュメントがほとんどないため、これは便利です。 大きいデータ ソースの場合は、この値を 0 より大きい値に設定します。

また、構成パラメーター内の ```"dataToExtract":"contentAndMetadata"``` ステートメントにも注意してください。 このステートメントは、さまざまなファイル形式と、各ファイルに関連するメタデータからコンテンツを、自動的に抽出するようにインデクサーに指示します。 

コンテンツが抽出されるときに、```imageAction``` を設定して、データ ソース内にある画像からテキストを抽出することができます。 ```"imageAction":"generateNormalizedImages"``` 構成は、OCR スキルおよびテキスト マージ スキルと組み合わされて、イメージからテキスト (たとえば、一時停止の道路標識から "stop" という単語) を抽出し、それをコンテンツ フィールドの一部として埋め込むよう、インデクサーに指示します。 この動作は、ドキュメントに埋め込まれているイメージ (PDF 内のイメージを考えてください) と、データ ソース内にあるイメージ (たとえば、JPG ファイル) の両方に適用されます。

## <a name="check-indexer-status"></a>インデクサーの状態を確認する

インデクサーを定義すると、要求を送信するときに自動的に実行されます。 定義したコグニティブ スキルによっては、インデックス作成に予想よりも時間がかかる場合があります。 インデクサーがまだ実行中かどうかを調べるには、次の要求を送信して、インデクサーの状態を確認します。

```http
GET https://[servicename].search.windows.net/indexers/demoindexer/status?api-version=2017-11-11-Preview
api-key: [api-key]
Content-Type: application/json
```

応答は、インデクサーが実行中かどうかを示します。 インデックス作成が完了したら、STATUS エンドポイント (上記と同じ) に対して別の HTTP GET を使用して、エンリッチメント中に発生したエラーと警告のレポートを表示します。  

警告は、一部のソース ファイルとスキルの組み合わせではよく見られ、必ずしも問題を示すわけではありません。 このチュートリアルでは、警告は無害です (たとえば、JPEG ファイルからのテキスト入力がない)。 インデックスの作成中に生成される警告についての詳細情報については、状態応答を確認することができます。
 
## <a name="verify-content"></a>コンテンツを検証する

インデックス作成が完了したら、個々 のフィールドの内容を返すクエリを実行します。 既定では、Azure Search によって上位 50 件の結果が返されます。 サンプル データは小さいため、既定値で問題なく動作します。 ただし、より大きなデータ セットを使用する場合は、より多くの結果が返されるよう、クエリ文字列にパラメーターを含める必要がある場合があります。 手順については、「[Azure Search でのページ検索結果の表示方法](search-pagination-page-layout.md)」をご覧ください。

検証手順として、すべてのフィールドのインデックスのクエリを実行します。

```http
GET https://[servicename].search.windows.net/indexes/demoindex?api-version=2017-11-11-Preview
api-key: [api-key]
Content-Type: application/json
```

出力は、インデックス スキーマと、各フィールドの名前、型、および属性です。

`"*"` の 2 番目のクエリを送信して、`organizations` などの 1 つのフィールドのすべての内容を返します。

```http
GET https://[servicename].search.windows.net/indexes/demoindex/docs?search=*&$select=organizations&api-version=2017-11-11-Preview
api-key: [api-key]
Content-Type: application/json
```

その他のフィールド (この演習では、content、languageCode、keyPhrases、および organizations) でも同様に繰り返します。 コンマ区切りリストを使用して、`$select` を介して複数のフィールドを返すことができます。

クエリ文字列の複雑さと長さによっては、GET や POST を使用できます。 詳細については、[REST API を使用したクエリ](https://docs.microsoft.com/rest/api/searchservice/search-documents)に関するページをご覧ください。

<a name="access-enriched-document"></a>

## <a name="accessing-the-enriched-document"></a>エンリッチされたドキュメントへのアクセス

コグニティブ検索では、エンリッチされたドキュメントの構造を確認できます。 エンリッチされたドキュメントは、エンリッチメント中に作成されてプロセスが完了すると削除される、一時的な構造体です。

インデックス作成時に作成されるエンリッチされたドキュメントのスナップショットをキャプチャするには、インデックスに ```enriched``` という名前のフィールドを追加します。 インデクサーは、そのドキュメントのすべてのエンリッチメントの文字列表現を、このフィールド内に自動的にダンプします。

```enriched``` フィールドには、メモリ内のエンリッチされたドキュメントの JSON での論理表現である文字列が含まれます。  ただし、フィールド値は有効な JSON ドキュメントです。 引用符はエスケープされるため、書式設定された JSON としてドキュメントを表示するためには、`\"` を `"` に置き換える必要があります。  

```enriched``` フィールドはデバッグのために使用するフィールドであり、式を評価するコンテンツの論理構造を理解するうえでは役に立ちます。 スキルセットを理解してデバッグするには便利なツールです。

エンリッチされたドキュメントの内容をキャプチャする `enriched` フィールドを含み、前述の演習を繰り返します。

### <a name="request-body-syntax"></a>要求本文の構文
```json
{
  "fields": [
    {
      "name": "id",
      "type": "Edm.String",
      "key": true,
      "searchable": true,
      "filterable": false,
      "facetable": false,
      "sortable": true
    },
    {
      "name": "content",
      "type": "Edm.String",
      "sortable": false,
      "searchable": true,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "languageCode",
      "type": "Edm.String",
      "searchable": true,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "keyPhrases",
      "type": "Collection(Edm.String)",
      "searchable": true,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "organizations",
      "type": "Collection(Edm.String)",
      "searchable": true,
      "sortable": false,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "enriched",
      "type": "Edm.String",
      "searchable": false,
      "sortable": false,
      "filterable": false,
      "facetable": false
    }
  ]
}
```
<a name="reset"></a>

## <a name="reset-and-rerun"></a>リセットして再実行する

パイプライン開発の初期の実験的な段階では、設計反復のための最も実用的なアプローチは、Azure Search からオブジェクトを削除してリビルドできるようにすることです。 リソース名は一意です。 オブジェクトを削除すると、同じ名前を使用して再作成することができます。

新しい定義でドキュメントのインデックスを再作成するには、以下の操作を行います。

1. インデックスを削除して、保持されているデータを削除します。 インデクサーを削除して、サービスに再作成します。
2. スキルセットとインデックスの定義を変更します。
3. サービスにインデックスとインデクサーを再作成して、パイプラインを実行します。 

ポータルを使用して、インデックス、インデクサ、およびスキルセットを削除できます。

```http
DELETE https://[servicename].search.windows.net/skillsets/demoskillset?api-version=2017-11-11-Preview
api-key: [api-key]
Content-Type: application/json
```

正常に削除されると、状態コード 204 が返されます。

コードが成熟したら、リビルド戦略を改善することもできます。 詳細については、[インデックスをリビルドする方法](search-howto-reindex.md)に関するページをご覧ください。

## <a name="takeaways"></a>ここまでのポイント

このチュートリアルでは、構成要素 (データ ソース、スキルセット、インデックス、およびインデクサー) の作成によってエンリッチされたインデックス作成パイプラインを作成するための、基本的な手順を示します。

[定義済みのスキル](cognitive-search-predefined-skills.md)については、スキルセットの定義と、入力と出力を介したスキルの連結のしくみとともに説明しました。 また、Azure Search サービスでエンリッチされた値をパイプラインから検索可能なインデックス内にルーティングするには、インデクサーの定義に `outputFieldMappings` が必要であることも学習しました。

最後に、結果をテストし、今後の反復のためにシステムをリセットする方法について学習しました。 インデックスに対するクエリを発行すると、エンリッチされたインデックス作成パイプラインによって作成された出力が返されることを学習しました。 このリリースには、内部構造 (システムによって作成されるエンリッチされたドキュメント) を表示するためのメカニズムがあります。 また、インデクサーの状態を確認する方法と、パイプラインを再実行する前に削除すべきオブジェクトについても学習しました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

チュートリアルの後で最も速くクリーンアップする方法は、Azure Search サービスと Azure BLOB サービスが含まれているリソース グループを削除することです。 両方のサービスを同じグループに配置すると仮定した場合は、ここでリソース グループを削除すると、このチュートリアル用に作成したサービスと保存したコンテンツを含み、そのリソース グループ内のすべてのものが完全に削除されます。 Portal では、リソース グループ名は各サービスの [概要] ページに表示されます。

## <a name="next-steps"></a>次の手順

カスタム スキルを使ってパイプラインをカスタマイズまたは拡張します。 カスタム スキルを作成してスキルセットに追加すると、自分で作成したテキストまたは画像分析をオンボードできます。 

> [!div class="nextstepaction"]
> [例: カスタム スキルの作成](cognitive-search-create-custom-skill-example.md)
