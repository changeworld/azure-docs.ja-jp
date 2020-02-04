---
title: AI エンリッチメントの概要
titleSuffix: Azure Cognitive Search
description: 認知スキルと AI アルゴリズムを使用したコンテンツ抽出、自然言語処理 (NLP) および画像処理を実行することにより、Azure Cognitive Search インデックスで検索可能なコンテンツを作成します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 11/04/2019
ms.openlocfilehash: e6ee75f4a7e00e8c21079e1336756db20221750f
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2020
ms.locfileid: "76838005"
---
# <a name="introduction-to-ai-in-azure-cognitive-search"></a>Azure Cognitive Search における AI の概要

AI エンリッチメントは、Azure Cognitive Search のインデックス作成の機能で、画像や BLOB などの構造化されていないデータ ソースからテキストを抽出し、コンテンツをエンリッチすることによって、インデックスまたはナレッジ ストアから検索しやすくします。 抽出とエンリッチメントは、インデックス作成パイプラインにアタッチされた*コグニティブ スキル*によって実装されています。 サービスに組み込まれたコグニティブ スキルは、次のカテゴリに分類されます。 

+ **自然言語処理**スキルには、[エンティティ認識](cognitive-search-skill-entity-recognition.md)、[言語検出](cognitive-search-skill-language-detection.md)、[キー フレーズ抽出](cognitive-search-skill-keyphrases.md)、テキスト操作、[センチメント検出](cognitive-search-skill-sentiment.md)、[PII 検出](cognitive-search-skill-pii-detection.md)が含まれます。 これらのスキルによって、構造化されていないテキストが、インデックス内の検索とフィルターが可能なフィールドにマップされた新しい形式を想定できます。

+ **画像処理**スキルには、[光学式文字認識 (OCR)](cognitive-search-skill-ocr.md) および[ビジュアル フィーチャー](cognitive-search-skill-image-analysis.md)の特定 (例: 顔検出、画像の解釈、画像の認識 (有名な人やランドマーク)、色や画像の向きなどの属性) が含まれます。 Azure Cognitive Search のすべてのクエリ機能を使用して、検索可能な画像コンテンツのテキスト表現を作成できます。

![エンリッチメント パイプラインの図](./media/cognitive-search-intro/cogsearch-architecture.png "エンリッチメント パイプラインの概要")

Azure Cognitive Search のコグニティブ スキルは、Cognitive Services APIs の事前トレーニング済み機械学習モデル ([Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/) と[テキスト分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)) に基づいています。 

自然言語および画像処理はデータ インジェスト フェーズで適用され、結果は Azure Cognitive Search における検索可能なインデックス内のドキュメントの構成の一部になります。 データは Azure データ セットとして調達され、必要な[組み込みのスキル](cognitive-search-predefined-skills.md)を使用してインデックス パイプライン経由でプッシュされます。 アーキテクチャは拡張可能なため、組み込みのスキルでは不十分な場合は、[カスタム スキル](cognitive-search-create-custom-skill-example.md)を作成して追加し、カスタム処理を統合できます。 例としては、金融、科学出版物、医療などの専門分野を対象としたカスタム エンティティ モジュールまたはドキュメント分類子が挙げられます。

> [!NOTE]
> 処理の頻度を増やす、ドキュメントを追加する、または AI アルゴリズムを追加することによってスコープを拡大する場合は、[課金対象の Cognitive Services リソースをアタッチする](cognitive-search-attach-cognitive-services.md)必要があります。 Cognitive Services の API を呼び出すとき、および Azure コグニティブ検索のドキュメント解析段階の一部として画像抽出するときに、料金が発生します。 ドキュメントからのテキストの抽出には、料金はかかりません。
>
> 組み込みスキルの実行は、既存の [Cognitive Services の従量課金制の価格](https://azure.microsoft.com/pricing/details/cognitive-services/)で課金されます。 画像抽出の価格は、[Azure コグニティブ検索の価格](https://go.microsoft.com/fwlink/?linkid=2042400)に関するページで説明されています。

## <a name="when-to-use-cognitive-skills"></a>いつコグニティブ スキルを使用するか

生コンテンツが、テキスト、画像コンテンツ、または言語検出と翻訳を必要とするコンテンツである場合、組み込みのコグニティブ スキルの使用を検討する必要があります。 組み込みのコグニティブ スキルを通じて AI を適用することで、このコンテンツのロックを解除して、検索およびデータ サイエンス アプリの価値と有用性を向上させることができます。 

さらに、パイプラインに統合したいオープンソース コード、サードパーティ製コード、またはファースト パーティ製コードの追加を検討することもできます。 さまざまなドキュメントの種類の顕著な特徴を識別する分類モデルはこのカテゴリに分類されますが、コンテンツの価値を高めるパッケージを使用することもできます。

### <a name="more-about-built-in-skills"></a>組み込みのスキルの詳細

組み込みのスキルを使用して作られたスキルセットは、次のアプリケーション シナリオに適しています。

+ フルテキスト検索を必要とするスキャンされたドキュメント (JPEG)。 光学式文字認識 (OCR) スキルをアタッチして、JPEG ファイルからのテキストの識別、抽出、取り込みを行うことができます。

+ 画像とテキストが組み合わされた PDF。 PDF のテキストは、エンリッチメント ステップを使用せずにインデックス作成中に抽出できますが、画像と自然言語の処理を追加すると、標準のインデックス作成よりも優れた結果が得られやすくなります。

+ 言語検出と状況に応じたテキスト翻訳を適用する多言語コンテンツ。

+ 大きなドキュメントでは表示されない固有の意味またはコンテキストを持つコンテンツを含む、非構造化または半構造化ドキュメント。 

  多くの場合、BLOB には、単一の "フィールド" にパックされた大規模なコンテンツが含まれています。 画像と自然言語処理のスキルをインデクサーにアタッチすることによって、生のコンテンツに現存するが、それ以外に個別のフィールドとして表示されない新しい情報を作成することができます。 キー フレーズ抽出、感情分析、エンティティ認識 (人、組織、場所) などの、すぐに使用できる組み込みのコグニティブなスキルが役立ちます。

  また、組み込みのスキルのテキスト分割、マージ、シェイプ操作を通じて、コンテンツを再構築することもできます。

### <a name="more-about-custom-skills"></a>カスタム スキルの詳細

カスタム スキルは、フォームの認識や、[カスタム スキル Web インターフェイス](cognitive-search-custom-skill-interface.md)で指定してラップするモデルを使用するカスタム エンティティ検出などの、より複雑なシナリオに対応できます。 カスタム スキルの例としては、[Forms Recognizer](/azure/cognitive-services/form-recognizer/overview)、[Bing Entity Search API](https://docs.microsoft.com/azure/search/cognitive-search-create-custom-skill-example) の統合、[カスタム エンティティ認識](https://github.com/Microsoft/SkillsExtractorCognitiveSearch)などがあります。


## <a name="components-of-an-enrichment-pipeline"></a>エンリッチメント パイプラインのコンポーネント

エンリッチメント パイプラインは、データ ソースをクロールし、エンドツーエンドのインデックス処理を提供する、"[*インデクサー*](search-indexer-overview.md)" を使用します。 インデクサーに接続されたスキルは、ユーザーが定義したスキルセットに従ってドキュメントをインターセプトし、エンリッチメントします。 インデックスが作成されると、[Azure Cognitive Search でサポートされているすべての種類のクエリ](search-query-overview.md)から検索を要求してコンテンツにアクセスできます。  インデクサーを使い慣れていない場合は、このセクションで手順を示します。

### <a name="step-1-connection-and-document-cracking-phase"></a>手順 1:接続とドキュメント解読フェーズ

パイプラインの先頭には、非構造化テキストまたはテキスト以外のコンテンツ (画像、スキャンされたドキュメントの JPEG ファイルなど) があります。 データは、インデクサーによってアクセスできる Azure のデータ ストレージ サービスに存在する必要があります。 インデクサーは、ソース ドキュメントを "解読" してソース データからテキストを抽出できます。

![ドキュメント解析フェーズ](./media/cognitive-search-intro/document-cracking-phase-blowup.png "ドキュメント解析")

 サポートされているソースには、Azure Table Storage、Azure Table Storage、Microsoft Azure SQL Database、および Microsoft Azure Cosmos DB などがあります。 テキスト ベースのコンテンツは、次のファイル タイプから抽出できます:PDF、Word、PowerPoint、CSV ファイル。 詳細な一覧については、[サポートされている形式](search-howto-indexing-azure-blob-storage.md#supported-document-formats)に関するページをご覧ください。

### <a name="step-2-cognitive-skills-and-enrichment-phase"></a>手順 2:認知スキルとエンリッチメント フェーズ

エンリッチメントは、アトミック操作を実行する*認知スキル*を使用して実行されます。 たとえば、PDF からテキスト コンテンツを作成した場合、エンティティの認識言語検出またはキー フレーズ抽出を適用して、ソースではネイティブで使用できない新しいフィールドを生成できます。 パイプラインで使用される技術を総称して*スキルセット*と呼びます。  

![エンリッチメント フェーズ](./media/cognitive-search-intro/enrichment-phase-blowup.png "エンリッチメント フェーズ")

スキルセットには、[組み込みのコグニティブ スキル](cognitive-search-predefined-skills.md)に基づくものと、ユーザーが提供してスキルセットに接続する[カスタム スキル](cognitive-search-create-custom-skill-example.md)に基づくものとがあります。 スキルセットは、単純なものから複雑なものまで設定でき、処理の種類だけでなく、演算の順序も決定します。 スキルセットに加え、インデクサーの一部として定義されたフィールド マッピングによってエンリッチメント パイプラインが指定されます。 これらのコンポーネントの統合方法については、[スキルセットの定義](cognitive-search-defining-skillset.md)に関する記事を参照してください。

内部的には、パイプラインは、エンリッチメントされたドキュメントのコレクションを生成します。 エンリッチメントされたドキュメントのどの部分を、検索インデックス内のインデックスを作成可能なフィールドにマップするかをユーザーが決定できます。 たとえば、キー フレーズ抽出およびエンティティ認識スキルを適用した場合、これらの新しいフィールドはエンリッチメントされたドキュメントの一部になり、インデックス上のフィールドにマッピングできます。 入出力の構造の詳細については、[注釈](cognitive-search-concept-annotations-syntax.md)を参照してください。

#### <a name="add-a-knowledgestore-element-to-save-enrichments"></a>knowledgeStore 要素を追加してエンリッチメントを保存する

[Search REST api-version=2019-05-06-Preview](search-api-preview.md) では、Azure ストレージ接続を提供する `knowledgeStore` 定義とエンリッチメントの格納方法について説明するプロジェクションによってスキルセットが拡張されます。 

スキルセットにナレッジ ストアを追加すると、フルテキスト検索以外のシナリオにエンリッチメントの表現を投影できるようになります。 詳細については、[ナレッジ ストア (プレビュー)](knowledge-store-concept-intro.md) に関するページを参照してください。

### <a name="step-3-search-index-and-query-based-access"></a>手順 3:検索インデックスとクエリ ベースのアクセス

処理が完了したら、エンリッチメントされたドキュメントで構成された、Azure Cognitive Search で完全にテキスト検索が可能な検索インデックスを取得できます。 [インデックスをクエリ](search-query-overview.md)することで、開発者やユーザーは、パイプラインによって生成されるエンリッチメントされたコンテンツにアクセスできます。 

![インデックスと検索のアイコン](./media/cognitive-search-intro/search-phase-blowup.png "インデックスと検索のアイコン")

インデックスは、Azure Cognitive Search のために作成する他のコンポーネント同様、カスタム アナライザーで補完したり、ファジー検索クエリを呼び出したり、フィルターを設定して検索したり、スコアリング プロファイルを使って検索結果の形式を調整することができます。

インデックスは、フィールド、属性、スコアリング プロファイルやシノニム マップなどの特定のインデックスに関連づけられているその他のコンストラクトを定義するインデックス スキーマから生成されます。 インデックスが定義され、作成されたら、新規あるいは更新されたソース ドキュメントに対応してインデックスを増分できます。 特定の変更では、完全な再構築が必要です。 スキーマ デザインが安定するまで、小規模のデータ セットを使用することをお勧めします。 詳細については、「[インデックスの再構成と再構築](search-howto-reindex.md)」をご覧ください。

<a name="feature-concepts"></a>

## <a name="key-features-and-concepts"></a>主要機能および概念

| 概念 | [説明]| リンク |
|---------|------------|-------|
| スキルセット | スキルのコレクションを含む最上位の名前付きリソースです。 スキルセットはエンリッチメント パイプラインです。 スキルセットは、インデクサーによるインデックス作成時に呼び出されます。 | 「[スキルセットを定義する](cognitive-search-defining-skillset.md)」を参照してください。 |
| 認知スキル | 認知スキルは、エンリッチメント パイプラインでのアトミック変換です。 多くの場合、構造を抽出または推論し、ユーザーによる入力データの理解を補佐するコンポーネントです。 ほとんどの場合、テキスト形式で出力され、画像入力からテキストを抽出または生成する自然言語処理または画像処理で処理されます。 スキルからの出力は、インデックス内のフィールドにマッピングするか、または下流のエンリッチメントの入力として使用できます。 スキルは、定義済みか、Microsoft が提供、またはカスタム スキルとしてお客様が作成および展開できます。 | [組み込みのコグニティブ スキル](cognitive-search-predefined-skills.md) |
| データの抽出 | 一般的に、そのままでは情報を提供しないソースからデータ (エンティティ) を抽出するために使用されるエンティティ認知スキルである AI エンリッチメントに関連する、さまざまな処理について説明します。 | [エンティティ認識スキル](cognitive-search-skill-entity-recognition.md)と[ドキュメント抽出スキル (プレビュー)](cognitive-search-skill-document-extraction.md) に関するページを参照してください。| 
| 画像処理 | ランドマークを認識する、あるいは画像からテキストを抽出する能力など、画像からテキストを推測します。 一般的な例としては、スキャンしたドキュメント (JPEG) ファイルから文字を認識する OCR や道路標識を含む写真の番地を認識する能力が含まれます。 | [画像の分析スキル](cognitive-search-skill-image-analysis.md)または [OCR スキル](cognitive-search-skill-ocr.md)に関するページを参照してください。
| 自然言語処理 | テキスト入力に関する洞察や情報を取得するテキスト処理。 言語の検出、センチメント分析、およびキー フレーズの抽出は自然言語処理に分類されるスキルです。  | [キー フレーズ抽出スキル](cognitive-search-skill-keyphrases.md)、[言語検出スキル](cognitive-search-skill-language-detection.md)、[テキスト翻訳スキル](cognitive-search-skill-text-translation.md)、[感情分析スキル](cognitive-search-skill-sentiment.md)、[PII 検出スキル (プレビュー)](cognitive-search-skill-pii-detection.md) に関するページを参照してください。 |
| ドキュメントの解読 | インデックス作成時にテキスト以外のソースからテキスト コンテンツを抽出または作成する処理。 光学式文字認識 (OCR) はこの例ですが、インデクサーがアプリケーション ファイルからコンテンツを抽出するため、一般的に、コア インデクサー機能を指します。 ソース ファイルの場所を規定するデータソースと、フィールドをマッピングを規定するインデクサー定義は、いずれも、ドキュメント解読処理で重要な要因です。 | [インデクサーの概要](search-indexer-overview.md)に関するページを参照してください。 |
| シェーパー | テキスト フラグメントをより大きな構造に統合したり、逆に大きなテキスト フラグメントを下流プロセスで処理できるよう管理可能なサイズに分割します。 | [シェーパー スキル](cognitive-search-skill-shaper.md)、[テキスト マージャー スキル](cognitive-search-skill-textmerger.md)、[テキスト分割スキル](cognitive-search-skill-textsplit.md)に関するページを参照してください。 |
| エンリッチメントされたドキュメント | 処理中に生成される一時的な内部構造。最終出力は検索インデックスに反映されます。 どのエンリッチメントが実行されるかは、スキルセットによって決まります。 フィールド マッピングにより、どのデータ要素をインデックスに追加するかが決定されます。 オプションで、Storage Explorer、Power BI などのツールや、Azure Blob Storage に接続するその他のツールを使用して、エンリッチメントされたドキュメントを永続化して探索するためのナレッジ ストアを作成できます。 | [ナレッジ ストア (プレビュー)](knowledge-store-concept-intro.md) に関するページを参照してください。 |
| Indexer |  検索可能なデータとメタデータを外部データ ソースから抽出し、ドキュメント解読のために、インデックスとデータ ソース間のフィールド対フィールドのマッピングに基づいてインデックスを作成するクローラーです。 AI エンリッチメントのために、インデクサーはスキルセットを呼び出し、インデックス内のターゲット フィールドとエンリッチメント出力を関連付けるフィールド マッピングを含みます。 インデクサーの定義には、パイプライン操作のためのすべての指示と参照が含まれ、ユーザーがインデクサーを実行すると、パイプラインが呼び出されます。 構成を追加することによって、既存の処理の内容を再利用し、変更があったステップとスキルだけを実行することもできます。 | [インデクサー](search-indexer-overview.md)と[インクリメンタル エンリッチメント (プレビュー)](cognitive-search-incremental-indexing-conceptual.md) に関するページを参照してください。 |
| Data Source  | Azure でサポートされる型の外部データ ソースに接続するために、インデクサーによって使用されるオブジェクト。 | [インデクサーの概要](search-indexer-overview.md)に関するページを参照してください。 |
| インデックス | フィールド構造と使用状況を定義するインデックス スキーマから構築された Azure Cognitive Search で永続化された検索インデックスです。 | [基本インデックスの作成](search-what-is-an-index.md)に関するページを参照してください。 | 
| ナレッジ ストア | 検索インデックスに加えて、エンリッチメントされたドキュメントを整形して投影することができるストレージ アカウント | [ナレッジ ストアの概要](knowledge-store-concept-intro.md)に関するページを参照してください。 | 
| キャッシュ | エンリッチメント パイプラインによって作成された出力のキャッシュを含むストレージ アカウント。 キャッシュを有効にすることで、既存の出力が保存されます。キャッシュに保存された出力は、スキルセットなど、エンリッチメント パイプラインのコンポーネントに対する変更の影響を受けません。 | 「[インクリメンタル エンリッチメント](cognitive-search-incremental-indexing-conceptual.md)」を参照してください。 | 

<a name="where-do-i-start"></a>

## <a name="where-do-i-start"></a>どこから始めるか

**ステップ 1:[Azure Cognitive Search リソースを作成する](search-create-service-portal.md)** 

**手順 2:クイック スタートや例を試して実際に体験する**

+ [クイック スタート (ポータル)](cognitive-search-quickstart-blob.md)
+ [チュートリアル (HTTP 要求)](cognitive-search-tutorial-blob.md)
+ [例:AI エンリッチメント用のカスタム スキルを作成する (C#)](cognitive-search-create-custom-skill-example.md)

学習目的には無料のサービスをお勧めしますが、無料のトランザクションの数は 1 日あたり 20 のドキュメントまでに制限されます。 1 日でクイックスタートとチュートリアルの両方を実行するには、両方の演習が制限内に収まるように、より小さなファイル セット (10 ドキュメント) を使用するか、クイックスタートまたはチュートリアルで使用したインデクサーを削除してカウンターをゼロにリセットしてください。

**ステップ 3:API を確認する**

REST `api-version=2019-05-06` は、要求または .NET SDK 上で使用できます。 ナレッジ ストアを試してみる場合は、プレビューの REST API (`api-version=2019-05-06-Preview`) を使用してください。

この手順では、REST API を使用して AI エンリッチメント ソリューションを構築します。 AI エンリッチメント用に追加または拡張された API は 2 つのみです。 その他の API では、一般的に使用可能なバージョンと同じ構文です。

| REST API | [説明] |
|-----|-------------|
| [データ ソースの作成](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | エンリッチメントされたドキュメントを作成するために使用されるソース データを提供する外部データ ソースを識別するリソースです。  |
| [スキルセットの作成 (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | この API は、AI エンリッチメントに固有のものです。 エンリッチメント パイプラインでインデックス作成時に使用される[組み込みのスキル](cognitive-search-predefined-skills.md)と[カスタム コグニティブ スキル](cognitive-search-custom-skill-interface.md)の使用を調整するリソースとなっています。 |
| [インデックスの作成](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Azure Cognitive Search インデックスを表すスキーマです。 ソース データ内のフィールドやエンリッチメント フェーズで作成されたフィールドにマッピングされるインデックス内のフィールド (たとえば、エンティティ認識によって作成された組織名のためのフィールド)。 |
| [インデクサーの作成 (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | データ ソース、スキルセット、ソースからのフィールドの関連付け、ターゲット インデックスまでの中間データ構造、およびインデックス自体などの、インデックス作成時に使用されるコンポーネントを定義するリソースです。 インデクサーを実行すると、データの取り込みやエンリッチメントがトリガーされます。 出力は、インデックス スキーマを基に作成され、ソース データが入力され、スキルセットでエンリッチメントされた検索インデックスです。 この既存の API は、スキルセット プロパティを含めることで、コグニティブ検索シナリオ用に拡張されています。 |

**チェックリスト:一般的なワークフロー**

1. 代表的なサンプルに Azure ソース データをサブセット化します。 インデックスの作成には時間がかかるため、代表的な少量のデータ セットから始め、ソリューションの成熟度に応じて段階的に構築します。

1. データ取得のための接続文字列を指定する[データ ソース オブジェクト](https://docs.microsoft.com/rest/api/searchservice/create-data-source)を Azure Cognitive Search で作成します。

1. エンリッチメント手順に従って[スキルセット](https://docs.microsoft.com/rest/api/searchservice/create-skillset)を作成します。

1. [インデックス スキーマ](https://docs.microsoft.com/rest/api/searchservice/create-index)を定義します。 *フィールド* コレクションには、ソース データからのフィールドが含まれます。 エンリッチメント中に作成された、コンテンツのために生成された値を保管するための追加フィールドも削除する必要があります。

1. データ ソース、スキルセット、およびインデックスを参照する[インデクサー](https://docs.microsoft.com/rest/api/searchservice/create-skillset)を定義します。

1. インデクサー内に *outputFieldMappings* を追加します。 このセクションでは、(手順 4 の) インデックス スキーマ内の入力フィールドに、(手順 3 の) スキルセットからの出力をマッピングします。

1. Azure Cognitive Search のインデクサーを表現するため、前の手順で作成した *Create Indexer* 要求を (要求本文にインデクサー定義を含む POST 要求) 送信します。 この手順では、パイプラインを呼び出しインデクサーを実行する方法について説明します。

1. クエリを実行して結果を評価し、スキルセット、スキーマ、またはインデクサー構成を更新するためにコードを編集します。

1. パイプラインを再構築する前に[インデクサーをリセット](search-howto-reindex.md)します。

特定の疑問点または問題に関する詳細については、[トラブルシューティングのヒント](cognitive-search-concept-troubleshooting.md)を参照してください。

## <a name="next-steps"></a>次のステップ

+ [AI エンリッチメント ドキュメント リンク](cognitive-search-resources-documentation.md)
+ [クイック スタート: ポータルにおける AI エンリッチメントのチュートリアル](cognitive-search-quickstart-blob.md)
+ [チュートリアル:AI エンリッチメント API について](cognitive-search-tutorial-blob.md)
+ [ナレッジ ストア (プレビュー)](knowledge-store-concept-intro.md)
+ [REST でナレッジ ストアを作成する](knowledge-store-create-rest.md)
