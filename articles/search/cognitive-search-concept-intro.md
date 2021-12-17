---
title: AI エンリッチメントの概念
titleSuffix: Azure Cognitive Search
description: コンテンツ抽出、自然言語処理 (NLP)、および画像処理は、定義済みのコグニティブ スキルとカスタム AI アルゴリズムの両方を用いて、Azure Cognitive Search インデックスで検索可能なコンテンツを作成するために使用されます。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/10/2021
ms.custom: references_regions
ms.openlocfilehash: 8010de7d6aa0af0d096a0c7ed23740f23734cda2
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131554395"
---
# <a name="ai-enrichment-in-azure-cognitive-search"></a>Azure Cognitive Search における AI エンリッチメント

Azure Cognitive Search での AI エンリッチメントとは、インデックス作成の間に分析、変換、コンテンツの生成を追加する、組み込みのコグニティブ スキルとカスタム スキルのことです。 エンリッチメントによって、イメージからの情報抽出、テキストからのセンチメント、キー フレーズ、エンティティの検出など、これまで存在しなかった新しい情報が作成されます。 また、エンリッチメントによって、区別されていないテキストに構造体が追加されます。 これらのすべてのプロセスの結果として、以前は検索不可能なコンテンツが、全文検索のシナリオで使用できるようになります。 多くの場合、エンリッチされたドキュメントは、ナレッジ マイニングなど、検索以外のシナリオに役立ちます。

エンリッチメントは、[**インデクサー**](search-indexer-overview.md)にアタッチされた [**スキルセット**](cognitive-search-working-with-skillsets.md)によって定義されています。 インデクサーによってコンテンツが抽出されて設定されるのに対し、スキルセットでは、画像、BLOB、その他の非構造化データ ソースから新しい情報や構造が識別、分析、作成されます。 エンリッチメント パイプラインの出力は、[**検索インデックス**](search-what-is-an-index.md)または [**ナレッジ ストア**](knowledge-store-concept-intro.md)です。

![エンリッチメント パイプラインの図](./media/cognitive-search-intro/cogsearch-architecture.png "エンリッチメント パイプラインの概要")

スキルセットには、Cognitive Search の組み込みのスキルを含めることも、[*カスタム スキル*](cognitive-search-create-custom-skill-example.md)で提供する外部の処理を埋め込むこともできます。 カスタム スキルの例としては、金融、科学出版物、医療などの専門分野を対象としたカスタム エンティティ モジュールまたはドキュメント分類子が挙げられます。

組み込みのスキルは、次のカテゴリに分類されます。

+ **自然言語処理** スキルには、[エンティティ認識](cognitive-search-skill-entity-recognition-v3.md)、[言語検出](cognitive-search-skill-language-detection.md)、[キー フレーズ抽出](cognitive-search-skill-keyphrases.md)、テキスト操作、[センチメント検出 (オピニオン マイニングを含む)](cognitive-search-skill-sentiment-v3.md)、[PII 検出](cognitive-search-skill-pii-detection.md)が含まれます。 これらのスキルによって、構造化されていないテキストが、インデックスで検索可能およびフィルター可能なフィールドとしてマップされます。

+ **画像処理** スキルには、[光学式文字認識 (OCR)](cognitive-search-skill-ocr.md) および [ビジュアル フィーチャー](cognitive-search-skill-image-analysis.md)の特定 (例: 顔検出、画像の解釈、画像の認識 (有名な人やランドマーク)、画像の向きなどの属性) が含まれます。 これらのスキルによって、画像コンテンツのテキスト表現が作成され、Azure Cognitive Search のクエリ機能を使用して検索できるようになります。

Azure Cognitive Search の組み込みのスキルは、Cognitive Services APIs の事前トレーニング済み機械学習モデル ([Computer Vision](../cognitive-services/computer-vision/index.yml) と[テキスト分析](../cognitive-services/text-analytics/overview.md)) に基づいています。 コンテンツの処理中にこれらのリソースを活用する場合は、Cognitive Services リソースをアタッチできます。

自然言語および画像処理はデータ インジェスト フェーズで適用され、結果は Azure Cognitive Search における検索可能なインデックス内のドキュメントの構成の一部になります。 データは Azure データ セットとして調達され、必要な[組み込みのスキル](cognitive-search-predefined-skills.md)を使用してインデックス パイプライン経由でプッシュされます。  

## <a name="feature-availability"></a>使用可能な機能

AI エンリッチメントは、Azure Cognitive Services が利用できるリージョンで利用できます。 AI エンリッチメントを現在ご利用いただけるかどうかは、[リージョン別の利用可能な Azure 製品](https://azure.microsoft.com/global-infrastructure/services/?products=search)ページでご確認いただけます。 AI エンリッチメントは、以下のリージョンを除き、サポートされているすべてのリージョンでご利用いただけます。

+ オーストラリア南東部
+ 中国北部 2
+ ノルウェー東部
+ ドイツ中西部

お使いの検索サービスがこれらのリージョンに置かれている場合、スキルセットを作成したり、使用したりできませんが、その他の検索サービス機能はすべてご利用いただけます。また、完全サポートされています。

## <a name="when-to-use-ai-enrichment"></a>どのような場合に AI エンリッチメントを使用するか

生コンテンツが、非構造化テキスト、画像コンテンツ、または言語検出と翻訳を必要とするコンテンツの場合は、エンリッチメントを検討する必要があります。 組み込みのコグニティブ スキルを通じて AI を適用することで、このコンテンツのロックを解除して、検索およびデータ サイエンス アプリの価値と有用性を向上させることができます。 

さらに、パイプラインに統合したいオープンソース コード、サードパーティ製コード、またはファースト パーティ製コードの追加を検討することもできます。 さまざまなドキュメントの種類の顕著な特徴を識別する分類モデルはこのカテゴリに分類されますが、コンテンツの価値を高める任意のパッケージを使用できます。

### <a name="use-cases-for-built-in-skills"></a>組み込みスキルのユース ケース

組み込みのスキルを使用して作られた[スキルセット](cognitive-search-defining-skillset.md)は、次のアプリケーション シナリオに適しています。

+ スキャンされたドキュメント (JPEG) の書体と手書きのテキストを認識する[光学式文字認識 (OCR)](cognitive-search-skill-ocr.md) は、おそらく最もよく使用されるスキルです。 OCR スキルをアタッチすると、JPEG ファイルのテキストが識別され、抽出されて、取り込まれます。

+ 多言語コンテンツの[テキスト翻訳](cognitive-search-skill-text-translation.md)は、よく使用されるもう 1 つのスキルです。 言語検出はテキスト翻訳に組み込まれていますが、コーパス内のコンテンツの言語コードだけが必要な場合は、[言語検出](cognitive-search-skill-language-detection.md)を独立して実行することもできます。

+ 画像とテキストが組み合わされた PDF。 PDF のテキストは、エンリッチメント ステップを使用せずにインデックス作成中に抽出できますが、画像と自然言語の処理を追加すると、標準のインデックス作成よりも優れた結果が得られやすくなります。

+ 大きなドキュメントでは表示されない固有の意味またはコンテキストを持つコンテンツを含む、非構造化または半構造化ドキュメント。 

  多くの場合、BLOB には、単一の "フィールド" にパックされた大規模なコンテンツが含まれています。 画像と自然言語処理のスキルをインデクサーにアタッチすることによって、生のコンテンツに現存するが、それ以外に個別のフィールドとして表示されない新しい情報を作成することができます。 [キー フレーズ抽出](cognitive-search-skill-keyphrases.md)や[エンティティ認識](cognitive-search-skill-entity-recognition-v3.md) (人、組織、場所など) は、すぐに使用できて役に立つ組み込みのコグニティブ スキルです。

  また、組み込みのスキルのテキスト分割、マージ、シェイプ操作を通じて、コンテンツを再構築することもできます。

### <a name="use-cases-for-custom-skills"></a>カスタム スキルのユース ケース

カスタム スキルは、フォームの認識や、[カスタム スキル Web インターフェイス](cognitive-search-custom-skill-interface.md)で指定してラップするモデルを使用するカスタム エンティティ検出などの、より複雑なシナリオに対応できます。 カスタム スキルの例としては、[Forms Recognizer](../applied-ai-services/form-recognizer/overview.md)、[Bing Entity Search API](./cognitive-search-create-custom-skill-example.md) の統合、[カスタム エンティティ認識](https://github.com/Microsoft/SkillsExtractorCognitiveSearch)などがあります。

## <a name="enrichment-steps"></a>エンリッチメントの手順 <a name="enrichment-steps"></a>

エンリッチメント パイプラインは、"[*スキルセット*](cognitive-search-working-with-skillsets.md)" を含む "[*インデクサー*](search-indexer-overview.md)" で構成されます。 スキルセットではエンリッチメントの手順が定義されており、インデクサーによってスキルセットが駆動されます。 インデクサーの設定を行うとき、エンリッチしたコンテンツを[検索インデックス](search-what-is-an-index.md)に送信する出力フィールド マッピングや、[ナレッジ ストア](knowledge-store-concept-intro.md)のデータ構造を決めるプロジェクションのようなプロパティを設定できます。

インデックスを作成した後は、[Azure Cognitive Search でサポートされているすべての種類のクエリ](search-query-overview.md)を使用して検索を要求し、コンテンツにアクセスできます。

### <a name="step-1-connection-and-document-cracking-phase"></a>手順 1:接続とドキュメント解読フェーズ

インデクサーは、インデクサー データ ソースで提供される情報を使用して外部ソースに接続します。 リソースに接続したインデクサーは、"[ドキュメントを解読](search-indexer-overview.md#document-cracking)" して、テキストとイメージを抽出します。 画像コンテンツは画像処理を実行するスキルにルーティングでき、テキスト コンテンツはテキスト処理のためキューに登録されます。 

![ドキュメント解析フェーズ](./media/cognitive-search-intro/document-cracking-phase-blowup.png "ドキュメント解析")

このステップでは、AI エンリッチメントの対象になるすべての初期コンテンツまたは生コンテンツを集めます。 ドキュメントごとに、エンリッチメント ツリーが作成されます。 最初、ツリーはルート ノードの表現だけですが、スキルセットが実行されている間に、拡張されて構造を取得します。

<<<<<<< HEAD
### <a name="step-2-cognitive-skills-and-enrichment-phase"></a>手順 2:コグニティブ スキルとエンリッチメント フェーズ
=======
### <a name="step-2-skillset-enrichment-phase"></a>手順 2: スキルセット エンリッチメント フェーズ
>>>>>>> repo_sync_working_branch

スキルセットでは、各ドキュメントに対して実行されるアトミック操作が定義されています。 たとえば、PDF から抽出されたテキストと画像では、スキルセットによってエンティティ認識、言語検出、またはキー フレーズ抽出が適用されて、ソースでネイティブに使用できない新しいフィールドが生成される場合があります。 

![エンリッチメント フェーズ](./media/cognitive-search-intro/enrichment-phase-blowup.png "エンリッチメント フェーズ")

 スキルセットは、単純なものから複雑なものまで設定でき、処理の種類だけでなく、演算の順序も決定します。 ほとんどのスキルセットには、約 3 から 5 個のスキルが含まれています。

スキルセットに加え、インデクサーの一部として定義された[出力フィールド マッピング](cognitive-search-output-field-mapping.md)によって、エンリッチメント パイプラインが完全に指定されます。 これらのコンポーネントの統合方法については、[スキルセットの定義](cognitive-search-defining-skillset.md)に関する記事を参照してください。

内部的には、パイプラインは、エンリッチメントされたドキュメントのコレクションを生成します。 エンリッチメントされたドキュメントのどの部分を、検索インデックス内のインデックスを作成可能なフィールドにマップするかをユーザーが決定できます。 たとえば、キー フレーズ抽出およびエンティティ認識スキルを適用した場合、これらの新しいフィールドはエンリッチメントされたドキュメントの一部になり、インデックス上のフィールドにマッピングできます。 入出力の構造の詳細については、[注釈](cognitive-search-concept-annotations-syntax.md)を参照してください。

### <a name="step-3-indexing"></a>手順 3: インデックス作成

インデックス作成のプロセスでは、生のエンリッチ済みコンテンツが、検索インデックスのフィールドとして、またナレッジ ストアも作成している場合は[プロジェクション](knowledge-store-projection-overview.md)として、取り込まれます。 暗黙的または明示的なフィールド マッピングを使用して、正しいフィールドにコンテンツを送信することで、同じエンリッチ済みコンテンツを両方に含めることができます。

エンリッチ済みコンテンツは、スキルセットの実行の間に生成され、ユーザーが保存しない限り一時的なものです。 エンリッチ済みコンテンツを検索インデックスに含めるには、エンリッチ済みコンテンツを検索インデックスのフィールドに送信できるように、インデクサーがマッピング情報を保持している必要があります。 [出力フィールド マッピング](cognitive-search-output-field-mapping.md)によって、これらの関連付けが設定されます。

## <a name="saving-enriched-output"></a>エンリッチ済みの出力の保存

Azure Cognitive Search では、インデクサーによって作成された出力が保存されます。

[**検索可能なインデックス**](search-what-is-an-index.md)は、インデクサーによって常に作成される出力の 1 つです。 インデックスの指定はインデクサーの要件であり、ユーザーがスキルセットをアタッチすると、スキルセットの出力に加えて、ソースから直接マップされたフィールドを使用して、インデックスが作成されます。 通常、キー フレーズやセンチメント スコアなどの特定のスキルの出力は、その目的のために作成されたフィールドのインデックスに取り込まれます。

[**ナレッジ ストア**](knowledge-store-concept-intro.md)はオプションの出力であり、ナレッジ マイニングのような下流のアプリに使用されます。 ナレッジ ストアは、スキルセット内で定義されています。 その定義により、エンリッチされたドキュメントが表またはオブジェクト (ファイルまたは BLOB) のどちらとして投影されるかが決まります。 表形式のプロジェクションは、Power BI などのツールでの対話型分析に適しています。一方、ファイルと BLOB は通常、データ サイエンスや同様のプロセスで使用されます。

最後に、後続のスキルセットの実行で再利用される場合に備えて、インデクサーで Azure Blob Storage に [**エンリッチされたドキュメントをキャッシュする**](cognitive-search-incremental-indexing-conceptual.md)ことができます。 キャッシュは、内部でのみ使用されます。 キャッシュされたエンリッチメントは、後で再実行する同じスキルセットによって使用できます。 キャッシュは、スキルセットに画像分析や OCR が含まれていて、画像ファイルの再処理にかかる時間と費用を避けたい場合に便利です。

インデックスとナレッジ ストアは相互に完全に独立しています。 インデクサーの要件を満たすにはインデックスをアタッチする必要がありますが、ナレッジ ストアだけが目的の場合は、作成された後でインデックスを無視してかまいません。 ただし、削除しないようにしてください。 インデクサーとスキルセットを再実行する場合、インデクサーを実行するにはインデックスが必要になります。

## <a name="using-enriched-content"></a>エンリッチ済みコンテンツの使用

処理が済むと、エンリッチメントされたドキュメントで構成された、Azure Cognitive Search で完全にテキスト検索が可能な、[検索インデックス](search-what-is-an-index.md)が手に入ります。 [**インデックスをクエリ**](search-query-overview.md)することで、開発者やユーザーは、パイプラインによって生成されるエンリッチメントされたコンテンツにアクセスできます。 インデックスは、Azure Cognitive Search 用に作成する他のものと同様に、カスタム アナライザーでテキスト分析を補完したり、ファジー検索クエリを呼び出したり、フィルターを追加したり、スコアリング プロファイルを使用して実験して検索の関連性を調整したりすることができます。

また、[ナレッジ ストア](knowledge-store-concept-intro.md)を使用する場合もあります。 ナレッジ ストアには、分析や機械学習などのナレッジ マイニング シナリオで使用できるデータが格納されています。 [ストレージ ブラウザー](knowledge-store-view-storage-explorer.md)、[Power BI](knowledge-store-connect-power-bi.md)、または Azure Storage に接続する任意のアプリを使用できます。

## <a name="checklist-a-typical-workflow"></a>チェックリスト:一般的なワークフロー

1. プロジェクトを開始するときは、データのサブセットを使用すると便利です。 インデクサーとスキルセットの設計は反復的なプロセスであり、小さい代表的なデータ セットを使用して作業すると、反復がいっそう迅速になります。

1. データへの接続が指定されている[データ ソース](/rest/api/searchservice/create-data-source)を作成します。

1. エンリッチメントを追加するための[スキルセット](/rest/api/searchservice/create-skillset)を作成します。

1. 検索インデックスが定義されている[インデックス スキーマ](/rest/api/searchservice/create-index)を作成します。

1. [インデクサー](/rest/api/searchservice/create-indexer)を作成して、上記のすべてのコンポーネントをまとめます。 インデクサーを作成または実行すると、データが取得され、スキルセットが実行されて、インデックスが読み込まれます。

1. クエリを実行して結果を評価し、スキルセット、スキーマ、またはインデクサー構成を更新するためにコードを編集します。

上記の手順を反復するには、パイプラインを再構築する前に[インデクサーをリセットする](search-howto-reindex.md)か、実行のたびにオブジェクトを削除して作成し直します (Free レベルを使用している場合に推奨)。 また、可能な限り、[エンリッチメントのキャッシュを有効](cognitive-search-incremental-indexing-conceptual.md)にして、既存のエンリッチメントを再利用する必要があります。

## <a name="next-steps"></a>次のステップ

+ [クイックスタート: テキスト翻訳とエンティティ スキルセットを作成する](cognitive-search-quickstart-blob.md)
+ [クイックスタート: OCR イメージ スキルセットを作成する](cognitive-search-quickstart-ocr.md)
+ [チュートリアル: AI エンリッチメント REST API について学習する](cognitive-search-tutorial-blob.md)
+ [スキルセットの概念](cognitive-search-working-with-skillsets.md)
+ [ナレッジ ストアの概念](knowledge-store-concept-intro.md)
+ [スキルセットを作成する](cognitive-search-defining-skillset.md)
+ [ナレッジ ストアの作成](knowledge-store-create-rest.md)