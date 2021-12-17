---
title: インクリメンタル エンリッチメントのキャッシュを有効にする (プレビュー)
titleSuffix: Azure Cognitive Search
description: AI エンリッチメント パイプラインでダウンストリームのスキルとプロジェクションを変更するときに再利用できるよう、エンリッチされたコンテンツのキャッシュを有効にします。
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: b905517a2558eb6bc01b4ba218f0ecde7e830ebf
ms.sourcegitcommit: 5361d9fe40d5c00f19409649e5e8fed660ba4800
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2021
ms.locfileid: "130138600"
---
# <a name="enable-caching-for-incremental-enrichment-in-azure-cognitive-search"></a>Azure Cognitive Search でインクリメンタル エンリッチメントのキャッシュを有効にする

> [!IMPORTANT] 
> この機能はパブリック プレビュー段階にあり、[追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)の下で提供されます。 [プレビューの REST API](/rest/api/searchservice/index-preview) では、この機能がサポートされています

この記事では、エンリッチメント パイプラインにキャッシュを追加して、毎回完全にリビルドしなくてもダウンストリーム エンリッチメントの手順を変更できるようにする方法について説明します。 既定では、スキルセットはステートレスであり、その構成の任意の部分を変更するには、インデクサーを完全に再実行する必要があります。 [**インクリメンタル エンリッチメント**](cognitive-search-incremental-indexing-conceptual.md)を使用すると、スキルセットまたはインデクサーの定義で検出された変更に基づいて、ドキュメント ツリーのどの部分を更新する必要があるかをインデクサーで判断できます。 既存の処理済みの出力は保持され、可能な限り再利用されます。 

キャッシュされたコンテンツは、指定したアカウント情報を使用して Azure Storage に配置されます。 `ms-az-search-indexercache-<alpha-numerc-string>` という名前のコンテナーは、インデクサーの実行時に作成されます。 これは検索サービスによって管理される内部コンポーネントと見なされ、変更することはできません。

インデクサーの設定に慣れていない場合は、[インデクサーの概要](search-indexer-overview.md)から開始し、[スキルセット](cognitive-search-working-with-skillsets.md)に進んで、エンリッチメント パイプラインについて学習してください。 主要な概念の背景の詳細については、[インクリメンタル エンリッチメント](cognitive-search-incremental-indexing-conceptual.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

Azure Storage は、キャッシュされたエンリッチメントの格納に使用されます。 ストレージ アカウントは、[汎用 v2](../storage/common/storage-account-overview.md#types-of-storage-accounts) にする必要があります。

インデクサーでキャッシュを有効にするには、プレビュー API またはベータ版 Azure SDK が必要です。 現在、ポータルでは、エンリッチメントをキャッシュするオプションを提供していません。

## <a name="enable-on-new-indexers"></a>新しいインデクサーで有効にする

新しいインデクサーで、[インデクサーの作成または更新 (2021-04-30-Preview)](/rest/api/searchservice/preview-api/create-or-update-indexer) を呼び出すときに、インデクサー定義のペイロードに "cache" プロパティを追加します。 以前のプレビュー API バージョン、2020-06-30-Preview を使用することもできます。

```https
POST https://[service name].search.windows.net/indexers?api-version=2021-04-30-Preview
    {
        "name": "<YOUR-INDEXER-NAME>",
        "targetIndexName": "<YOUR-INDEX-NAME>",
        "dataSourceName": "<YOUR-DATASOURCE-NAME>",
        "skillsetName": "<YOUR-SKILLSET-NAME>",
        "cache" : {
            "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
            "enableReprocessing": true
        },
        "fieldMappings" : [],
        "outputFieldMappings": [],
        "parameters": []
        }
    }
```

## <a name="enable-on-existing-indexers"></a>既存のインデクサーで有効にする

既にスキルセットのある既存のインデクサーの場合、次の手順を使用してキャッシュを追加します。 1 回限りの操作として、インデクサーを完全にリセットして再実行し、キャッシュを読み込みます。

### <a name="step-1-get-the-indexer-definition"></a>手順 1:インデクサーの定義を取得する

コンポーネント (データソース、スキルセット、インデックス) を含む有効な作業インデクサーを使用して開始します。 API クライアントを使用して、[GET インデクサー](/rest/api/searchservice/get-indexer)要求を送信し、インデクサーを取得します。 プレビュー API バージョンを使用してインデクサーを取得すると、null に設定された "cache" プロパティが自動的に定義に追加されます。

```http
GET https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2021-04-30-Preview
    Content-Type: application/json
    api-key: [YOUR-ADMIN-KEY]
```

### <a name="step-2-set-the-cache-property"></a>手順 2: cache プロパティを設定する

インデックス定義で、次の必須プロパティと省略可能なプロパティを含めるように "cache" を変更します。

+ (必須) `storageConnectionString` は Azure Storage 接続文字列を設定する必要があります。
+ (省略可能) `enableReprocessing` のブール型プロパティ (既定では `true`) は、インクリメンタル エンリッチメントが有効になっていることを示します。 新しいドキュメントのインデックス作成など、リソースを大量に消費する他の操作の実行中に増分処理を一時停止させ、後で `true` に切り替える場合は、`false` に設定します。

```http
POST https://[service name].search.windows.net/indexers?api-version=2021-04-30-Preview
    {
        "name": "<YOUR-INDEXER-NAME>",
        "targetIndexName": "<YOUR-INDEX-NAME>",
        "dataSourceName": "<YOUR-DATASOURCE-NAME>",
        "skillsetName": "<YOUR-SKILLSET-NAME>",
        "cache" : {
            "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
            "enableReprocessing": true
        },
        "fieldMappings" : [],
        "outputFieldMappings": [],
        "parameters": []
    }
```

### <a name="step-3-reset-the-indexer"></a>手順 3:インデクサーをリセットする

すべてのドキュメントが一貫した状態になるように、既存のインデクサーにインクリメンタル エンリッチメントを設定するときは、[インデクサーのリセット](/rest/api/searchservice/reset-indexer)が必要です。 このタスクには、ポータルまたは API クライアントを使用することができます。

```https
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/reset?api-version=2021-04-30-Preview
    Content-Type: application/json
    api-key: [YOUR-ADMIN-KEY]
```

### <a name="step-4-save-the-indexer"></a>手順 4: インデクサーを保存する

PUT 要求を使用して[インデクサーを更新 (2021-04-30-Preview)](/rest/api/searchservice/preview-api/create-or-update-indexer) します。要求の本文には "cache" が含まれます。

```http
PUT https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2021-04-30-Preview
    Content-Type: application/json
    api-key: [YOUR-ADMIN-KEY]
    {
        "name" : "<YOUR-INDEXER-NAME>",
        ...
        "cache": {
            "storageConnectionString": "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
            "enableReprocessing": true
        }
    }
```

ここでインデクサーに別の GET 要求を発行した場合、サービスからの応答には、キャッシュ オブジェクトの `ID` プロパティが含まれます。 このインデクサーによって処理される各ドキュメントのキャッシュされたすべての結果と中間状態が格納されているコンテナーの名前に、英数字文字列が追加されます。 ID は、BLOB ストレージでキャッシュの名前を一意に指定するために使用されます。

```http
    "cache": {
        "ID": "<ALPHA-NUMERIC STRING>",
        "enableReprocessing": true,
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-STORAGE-KEY>;EndpointSuffix=core.windows.net"
    }
```

### <a name="step-5-run-the-indexer"></a>手順 5:インデクサーを実行する

インデクサーを実行するには、ポータルまたは API を使用します。 ポータルでは、インデクサーの一覧からインデクサーを選択し、 **[実行]** をクリックします。 ポータルを使用する利点の 1 つは、インデクサーの状態を監視し、ジョブの実行時間と処理されるドキュメントの数を確認できることです。 ポータルのページは、数分ごとに更新されます。

別の方法として、REST を使用して[インデクサーを実行](/rest/api/searchservice/run-indexer)することもできます。

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/run?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

> [!NOTE]
> インデクサーをリセットして再実行すると、コンテンツをキャッシュできるように完全な再構築が実行されます。 すべてのコグニティブ エンリッチメントが、すべてのドキュメントで再実行されます。 エンリッチされたコンテンツのキャッシュからの再利用は、キャッシュの読み込み後に開始されます。
>

## <a name="check-for-cached-output"></a>キャッシュされた出力の確認

[BLOB コンテナー] の下の Azure Storage でキャッシュを検索します。 コンテナー名は `ms-az-search-indexercache-<some-alphanumeric-string>` です。

キャッシュはインデクサーによって作成および使用されます。 そのコンテンツは人間が判読できるものではありません。

キャッシュが動作するかどうかを確認するには、スキルセットを変更してインデクサーを実行し、実行時間とドキュメント数の前後のメトリックを比較します。 

スキャンされたドキュメントの画像分析と光学式文字認識 (OCR) を含むスキルセットは、テスト ケースとして最適です。 ダウンストリーム テキストのスキルまたは画像に関連しないスキルを変更すると、インデクサーは、以前に処理されたすべての画像と OCR のコンテンツをキャッシュから取得し、編集によって示されたテキスト関連の変更だけを更新および処理することができます。  インデクサー実行ドキュメント数に含まれるドキュメントの数が少なくなり、実行時間が短縮され、請求書の料金が少なくなることが期待できます。 

[cog-search-demo チュートリアル](cognitive-search-tutorial-blob.md)で使用される[ファイル セット](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/ai-enrichment-mixed-media)は、JPG、PNG、HTML、DOCX、PPTX などのさまざまな形式のファイルが 14 個含まれているため、便利なテスト ケースです。 インクリメンタル エンリッチメントの概念実証テストのために、テキスト翻訳スキルの `en` を `es`、または別の言語に変更します。

## <a name="common-errors"></a>一般的なエラー

要求でプレビュー API バージョンを指定し忘れた場合、次のエラーが発生します。

`"The request is invalid. Details: indexer : A resource without a type name was found, but no expected type was specified. To allow entries without type information, the expected type must also be specified when the model is specified."`

インデクサーの要件が不足している場合は、400 Bad Request エラーも発生します。 エラー メッセージには、不足している依存関係が示されます。

## <a name="next-steps"></a>次のステップ

インクリメンタル エンリッチメントは、スキルセットを含むインデクサーに適用され、インデックスとナレッジ ストアの両方に再利用可能なコンテンツを提供します。 キャッシュとスキルセットの詳細については、次のリンクを参照してください。

+ [インクリメンタル エンリッチメント (ライフサイクルと管理)](cognitive-search-incremental-indexing-conceptual.md)
+ [スキルセットの概念と構成](cognitive-search-working-with-skillsets.md)
+ [スキルセットを作成する](cognitive-search-defining-skillset.md)
+ [チュートリアル: REST と AI を使用して Azure Blob から検索可能なコンテンツを生成する](cognitive-search-tutorial-blob.md)