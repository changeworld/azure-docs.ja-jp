---
title: ナレッジ ストア (プレビュー) の概要
titleSuffix: Azure Cognitive Search
description: エンリッチメントされたドキュメントを Azure ストレージに送信し、そこで Azure Cognitive Search および他のアプリケーション内のエンリッチメントされたドキュメントを表示、整形、および使用することができます。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 82f8606f4b4201833667347d3ed16fdd73f70a36
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790366"
---
# <a name="introduction-to-knowledge-stores-in-azure-cognitive-search"></a>Azure Cognitive Search のナレッジ ストアの概要

> [!Note]
> ナレッジ ストアはプレビュー段階にあり、運用環境での使用は意図していません。 [REST API バージョン 2019-05-06-Preview](search-api-preview.md) でこの機能を提供します。 現時点で .NET SDK のサポートはありません。
>

ナレッジ ストアは、Azure Cognitive Search の機能の 1 つです。[AI エンリッチメント パイプライン](cognitive-search-concept-intro.md)からの出力を、後続の分析など、ダウンストリームの処理に使用できるよう永続化するものです。 "*エンリッチメントされたドキュメント*" とは、AI プロセスを使用して抽出、構造化、および分析されたコンテンツから作成される、パイプラインの出力のことです。 標準的な AI パイプラインでは、エンリッチメントされたドキュメントは一時的なものであり、インデックス作成時にのみ使用され、その後破棄されます。 エンリッチメントされたドキュメントは、ナレッジ ストアを使用して保存されます。 

過去に Azure Cognitive Search でコグニティブ スキルを使用したことがある方であれば、"*スキルセット*" によって、ドキュメントが一連のエンリッチメントを通じて移動されることを既にご存じと思われます。 結果は、検索インデックスまたは (このプレビューの新機能である) ナレッジ ストア内のプロジェクションです。 この 2 つの出力 (検索インデックスとナレッジ ストア) では同じコンテンツが共有されますが、保存方法と使用方法がそれぞれ異なります。

物理的には、ナレッジ ストアは [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-overview) です。つまり Azure Table Storage か Azure Blob Storage、またはその両方になります。 Azure Storage に接続できるすべてのツールまたはプロセスは、ナレッジ ストアのコンテンツを使用できます。

![パイプラインにおけるナレッジ ストアの図](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "パイプラインにおけるナレッジ ストアの図")

プロジェクションとは、ナレッジ ストア内にデータを構築するためのメカニズムのことです。 たとえば、プロジェクションを使用して、出力を 1 つの BLOB として保存するのか、関連するテーブルのコレクションとして保存するのかを選択できます。 

ナレッジ ストアを使用するには、インデックス作成パイプラインに段階的な操作を定義するスキルセットに `knowledgeStore` 要素を追加します。 実行中に、Azure Cognitive Search によって Azure Storage アカウント内にスペースが作成され、ご利用の構成に応じて BLOB として、またはテーブルに、エンリッチメントされたドキュメントがプロジェクションされます。

## <a name="benefits-of-knowledge-store"></a>ナレッジ ストアのメリット

ナレッジ ストアでは、BLOB などの非構造化および半構造化データ ファイル、分析が実行されたイメージ ファイル、または新しいフォームに整形された構造化データから収集された構造体、コンテキスト、および実際のコンテンツが提供されます。 このプレビュー用に記述された[ステップ バイ ステップ チュートリアル](knowledge-store-howto.md)では、高密度の JSON ドキュメントが下部構造にパーティション分割される方法、新しい構造体に再構築される方法、または機械学習やデータ サイエンスのワークロードなどのダウンストリーム プロセスで使用可能にされる方法を直接確認できます。

AI エンリッチメント パイプラインで何を生成できるかを確認するのに便利ですが、ナレッジ ストアの真価はデータを整形する機能にあります。 基本的なスキルセットから開始し、反復処理して構造のレベルを追加し、それを Azure Cognitive Search 以外のアプリ内でも使用できるように新しい構造に結合できます。

ナレッジ ストアのメリットの列挙には以下が含まれます。

+ 検索以外の[分析およびレポート作成ツール](#tools-and-apps)内でエンリッチメントされたドキュメントを使用する。 Power Query を使用した Power BI は魅力的な選択肢ですが、Azure ストレージに接続できる任意のツールまたはアプリで、作成するナレッジ ストアからプルできます。

+ 手順とスキルセットの定義をデバッグ中に、AI インデックス作成パイプラインを調整する。 ナレッジ ストアによって、AI インデックス作成パイプライン内のスキルセット定義の製品が示されます。 これらの結果を使用すると、エンリッチメントがどのようになるかを正確に確認できるので、より優れたスキルセットを設計できます。 Azure ストレージ内で [Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) を使用して、ナレッジ ストアのコンテンツを表示できます。

+ データを新しいフォームに整形する。 整形はスキルセット内で体系化されていますが、ポイントは、スキルセットでこの機能を提供できるようになったことです。 Azure Cognitive Search 内の [Shaper スキル](cognitive-search-skill-shaper.md)は、このタスクに対応するために拡張されました。 整形により、関係を維持しながら、データの使用目的に合致したプロジェクションを定義することができます。

> [!Note]
> Cognitive Services を使用した AI エンリッチメントにまだ慣れていませんか? Azure Cognitive Search は Cognitive Services の Vision および Language 機能と統合され、イメージ ファイルの光学式文字認識 (OCR)、エンティティの認識、テキスト ファイルからのキー フレーズの抽出などを使用してソース データが抽出およびエンリッチメントされます。 詳細については、[Azure Cognitive Search の AI エンリッチメント](cognitive-search-concept-intro.md)に関するページを参照してください。

## <a name="creating-a-knowledge-store"></a>ナレッジ ストアの作成

ナレッジ ストアは[スキルセット](cognitive-search-working-with-skillsets.md)の構成要素であり、さらにそのスキルセットは[インデクサー](search-indexer-overview.md)の構成要素になっています。 

このプレビューでは、REST API と `api-version=2019-05-06-Preview` を使用するか、ポータルにある**データ インポート** ウィザードを使用してナレッジ ストアを作成できます。

### <a name="json-representation-of-a-knowledge-store"></a>ナレッジ ストアの JSON 表現

次の JSON では、`knowledgeStore` を指定します。これは、インデクサー (示されていません) によって呼び出されるスキルセットの一部です。 既に AI エンリッチメントに慣れている場合、エンリッチメントされた各ドキュメントの作成、編成、内容がスキルセットによって決まります。 スキルセットには、少なくとも 1 つのスキルが含まれている必要があります。データ構造を調整する場合、その筆頭に挙げられるのは Shaper スキルです。

`knowledgeStore` は、接続とプロジェクションから成ります。 

+ 接続は、Azure Search と同じリージョン内のストレージ アカウントに対するものです。 

+ プロジェクションは、テーブルとオブジェクトのペアです。 `Tables` は、Azure Table Storage におけるエンリッチメントされたドキュメントの物理的な表現を定義します。 `Objects` は、Azure Blob Storage における物理的なオブジェクトを定義します。

```json
{
  "name": "my-new-skillset",
  "description": "Example showing knowledgeStore placement in a skillset.",
  "skills":
  [
    {
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document/content/phrases/*",
    "inputs": [
        {
        "name": "text",
        "source": "/document/content/phrases/*"
        },
        {
        "name": "sentiment",
        "source": "/document/content/phrases/*/sentiment"
        }
    ],
    "outputs": [
        {
        "name": "output",
        "targetName": "analyzedText"
        }
    ]
    },
  ],
  "cognitiveServices": 
    {
    "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
    "description": "mycogsvcs resource in West US 2",
    "key": "<your key goes here>"
    },
  "knowledgeStore": { 
    "storageConnectionString": "<your connection string goes here>", 
    "projections": [ 
        { 
            "tables": [  
            { "tableName": "Reviews", "generatedKeyName": "ReviewId", "source": "/document/Review" , "sourceContext": null, "inputs": []}, 
            { "tableName": "Sentences", "generatedKeyName": "SentenceId", "source": "/document/Review/Sentences/*", "sourceContext": null, "inputs": []}, 
            { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/Review/Sentences/*/KeyPhrases", "sourceContext": null, "inputs": []}, 
            { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/Review/Sentences/*/Entities/*" ,"sourceContext": null, "inputs": []} 

            ], 
            "objects": [ 
               
            ]      
        },
        { 
            "tables": [ 
            ], 
            "objects": [ 
                { 
                "storageContainer": "Reviews", 
                "format": "json", 
                "source": "/document/Review", 
                "key": "/document/Review/Id" 
                } 
            ]      
        }        
    ]     
    } 
}
```

### <a name="sources-of-data-for-a-knowledge-store"></a>ナレッジ ストアのデータのソース

ナレッジ ストアが AI エンリッチメント パイプラインの出力だとしたら、入力は何でしょうか。 抽出してエンリッチメントし、最終的にはナレッジ ストアに保存することになる元のデータの取得元は、検索インデクサーによってサポートされる Azure のデータ ソースであれば何でもかまいません。 

* [Azure Cosmos DB](search-howto-index-cosmosdb.md)

* [Azure BLOB Storage](search-howto-indexing-azure-blob-storage.md)

* [Azure Table Storage](search-howto-indexing-azure-tables.md)

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

そのコンテンツは、皆さんが作成するインデクサーとスキルセットによって、インデックス作成ワークロードの構成要素として抽出され、エンリッチメントまたは変換された後、その結果がナレッジ ストアに保存されます。

### <a name="rest-apis-used-in-creation-of-a-knowledge-store"></a>ナレッジ ストアの作成に使用される REST API

ナレッジ ストアを作成するために必要な拡張機能がある API は 2 つだけです (Create Skillset と Create Indexer)。 それ以外の API はそのままで使用されます。

| Object | REST API | 説明 |
|--------|----------|-------------|
| データ ソース | [データ ソースの作成](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | エンリッチメントされたドキュメントを作成するために使用されるソース データを提供する外部 Azure データ ソースを識別するリソースです。  |
| スキルセット | スキルセットを作成する [(api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | エンリッチメント パイプラインでインデックス作成時に使用される[組み込みのスキル](cognitive-search-predefined-skills.md)と[カスタム コグニティブ スキル](cognitive-search-custom-skill-interface.md)の使用を調整するリソース。 スキルセットには、子要素として `knowledgeStore` 定義が存在します。 |
| index | [インデックスの作成](https://docs.microsoft.com/rest/api/searchservice/create-index)  | 検索インデックスを表すスキーマです。 ソース データ内のフィールドやエンリッチメント フェーズで作成されたフィールドにマッピングされるインデックス内のフィールド (たとえば、エンティティ認識によって作成された組織名のためのフィールド)。 |
| インデクサー | [インデクサーの作成 (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | データ ソース、スキルセット、ソースからのフィールドの関連付け、ターゲット インデックスまでの中間データ構造、およびインデックス自体などの、インデックス作成時に使用されるコンポーネントを定義するリソースです。 インデクサーを実行すると、データの取り込みやエンリッチメントがトリガーされます。 出力は、インデックス スキーマを基に作成され、ソース データが入力され、スキルセットでエンリッチメントされた検索インデックスです。  |

### <a name="physical-composition-of-a-knowledge-store"></a>ナレッジ ストアの物理的構成

 "*プロジェクション*" (`knowledgeStore` 定義の要素) は、出力が目的の用途と一致するよう、そのスキーマと構造を明確に表現するものです。 形式と形状が異なるデータを使用するアプリケーションがある場合は、複数のプロジェクションを定義できます。 

プロジェクションは、オブジェクトまたはテーブルとして表すことができます。

+ オブジェクトとして、プロジェクションは Blob Storage にマップされ、プロジェクションはコンテナーに保存されます。コンテナー内には、データ サイエンス パイプラインなどのシナリオ用に JSON でのオブジェクトまたは階層表現があります。

+ テーブルとして、プロジェクションは Table Storage にマップされます。 表形式では、機械学習用のデータ フレームとしてのデータの分析やエクスポートなどのシナリオの関係が維持されます。 エンリッチメントされたプロジェクションは、他のデータ ストアに簡単にインポートできます。 

組織内のさまざまな立場に対応するために、ナレッジ ストアには複数のプロジェクションを作成できます。 開発者は、エンリッチメントされたドキュメントの完全な JSON 表現にアクセスする必要がありますが、データ サイエンティストやアナリストは、スキルセットによって整形されたきめ細かいまたはモジュール形式のデータ構造を必要とする可能性があります。

たとえば、エンリッチメント プロセスの目標の 1 つがモデルのトレーニングに使用されるデータセットも作成することである場合、オブジェクト ストアへのデータの投影は、データ サイエンス パイプライン内でデータを使用する 1 つの方法です。 または、エンリッチメントされたドキュメントに基づく簡単な Power BI ダッシュボードを作成する場合は、表形式のプロジェクションがうまく機能します。

<a name="tools-and-apps"></a>

## <a name="connecting-with-tools-and-apps"></a>ツールやアプリを使用した接続

エンリッチメントがストレージ内に存在した後、Azure Blob または Table Storage に接続する任意のツールまたはテクノロジを使用して、コンテンツを探索、分析、または使用できます。 次の一覧が開始点です。

+ エンリッチメントされたドキュメントの構造とコンテンツを表示する [Storage Explorer](knowledge-store-view-storage-explorer.md)。 これは、ナレッジ ストアのコンテンツを表示するためのベースライン ツールと考えてください。

+ 数値データがある場合は [Power BI](knowledge-store-connect-power-bi.md) のレポート作成ツールと分析ツール。

+ さらに操作するための [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/)。


<!---
## Data lifecycle and billing

Each time you run the indexer, the cache in Azure storage is updated if the skillset definition or underlying source data has changed. As input documents are edited or deleted, changes are propagated through the annotation cache to the projections, ensuring that your projected data is a current representation of your inputs at the end of the indexer run. 

Generally speaking, pipeline processing can be an all-or-nothing operation, but Azure Search can process incremental changes, which saves you time and money.

If a document is new or updated, all skills are run. If only the skillset changes, reprocessing is scoped to just those skills and documents affected by your edit.

### Changes to a skillset
Suppose that you have a pipeline composed of multiple skills, operating over a large body of static data (for example, scanned documents), that takes 8 hours and costs $200 to create the knowledge store. Now suppose you need to tweak one of the skills in the skillset. Rather than starting over, Azure Search can determine which skill is affected, and reprocess only that skill. Cached data and projections that are unaffected by the change remain intact in the knowledge store.

### Changes in the data
Scenarios can vary considerably, but let's suppose instead of static data, you have volatile data that changes between indexer invocations. Given no changes to the skillset, you are charged for processing the delta of new and modified document. The timestamp information varies by data source, but for illustration, in a Blob container, Azure Search looks at the `lastmodified` date to determine which blobs need to be ingested.

> [!Note]
> While you can edit the data in the projections, any edits will be overwritten on the next pipeline invocation, assuming the document in source data is updated. 

### Deletions

Although Azure Search creates and updates structures and content in Azure storage, it does not delete them. Projections and cached documents continue to exist even when the skillset is deleted. As the owner of the storage account, you should delete a projection if it is no longer needed. 

### Tips for development

+ Start small with a representative sample of your data as you make significant changes to skillset composition. As your design finalizes, you can slowly add more data during later-stage development, and then roll in the entire data set when you are comfortable with the pipeline composition.

+ Retain control over indexer invocation. Indexers can run on a schedule, which is helpful for solutions that are rolled into production, but less helpful if you are actively developing your pipeline. During development, avoid schedules so that you don’t lose track of cache or projection state. Once your solution is in production and skillset composition is static, you can put the indexer on a schedule to pick up routine changes in the external source data. 

-->

<!-- ## Where do I start?

We recommend the Free service for learning purposes, but be aware that the number of free transactions is limited to 20 documents per day, per subscription.

When using multiple services, create all of your services in the same region for best performance and to minimize costs. You are not charged for bandwidth for inbound data or outbound data that goes to another service in the same region.

**Step 1: [Create an Azure Cognitive Search resource](search-create-service-portal.md)** 

**Step 2: [Create an Azure storage account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)** 

**Step 3: [Create a Cognitive Services resource](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)** 

**Step 4: [Get started with the portal](cognitive-search-quickstart-blob.md) - or - [Get started with sample data using REST and Postman](knowledge-store-howto.md)** 

You can use REST `api-version=2019-05-06-Preview` to construct an AI-based pipeline that includes knowledge store. In the newest preview API, the Skillset object provides the `knowledgeStore` definition. -->

## <a name="next-steps"></a>次の手順

ナレッジ ストアは、エンリッチメントされたドキュメントを永続化する手段として、スキルセットを設計する際に役立つほか、Azure Storage アカウントにアクセスする機能を備えた、あらゆるクライアント アプリケーションから利用する新しい構造やコンテンツを作成する際にも役立てることができます。

エンリッチメントされたドキュメントを作成する最も簡単なアプローチは、**データ インポート** ウィザードを使用することですが、Postman と REST API を使用する方法もあります。オブジェクトがどのように作成され、参照されるのかについて深く理解する必要がある場合には、後者の方が便利です。

> [!div class="nextstepaction"]
> [ポータルを使用してナレッジ ストアを作成する](knowledge-store-create-portal.md)
> [Postman と REST API を使用してナレッジ ストアを作成する](knowledge-store-create-rest.md)
