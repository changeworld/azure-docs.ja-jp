---
title: Azure Cognitive Search の概要
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search は、Microsoft が提供するフル マネージドのクラウド検索サービスです。 ユース ケース、開発ワークフロー、Microsoft の他の検索製品との比較、および開始方法について説明します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 10/22/2020
ms.custom: contperfq1
ms.openlocfilehash: d045014cd92df084e4dd409012fdb668d312de1a
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2020
ms.locfileid: "92442322"
---
# <a name="what-is-azure-cognitive-search"></a>Azure Cognitive Search とは

Azure Cognitive Search ([旧称 Azure Search](whats-new.md#new-service-name)) は、Web、モバイル、エンタープライズの各アプリケーション内のプライベートな異種コンテンツに対する豊富な検索エクスペリエンスを構築するための API とツールを開発者に提供するクラウド検索サービスです。

Cognitive Search サービスを作成すると、インデックス作成とクエリを実行する検索エンジン、お客様が作成および管理するインデックスの永続的なストレージ、単純なものから複雑なものまでさまざまなクエリを作成するためのクエリ言語が得られます。 検索サービスは必要に応じて他の Azure サービスと統合できます。これには、Azure データ ソースからのデータのインジェストと取得を自動化する " *インデクサー* " という形式があるほか、Cognitive Services で提供されている AI (画像やテキストの分析など) や、Azure Machine Learning 内で作成したり Azure Functions 内にラップしたりするカスタム AI を組み込んだ " *スキルセット* " という形式があります。

![Azure Cognitive Search のアーキテクチャ](media/search-what-is-azure-search/azure-search-diagram.svg "Azure Cognitive Search のアーキテクチャ")

アーキテクチャ上、検索サービスは、インデックスが設定されていないデータを含む外部データ ストアと、検索インデックスにクエリ要求を送信して応答を処理するクライアント アプリとの間に配置されます。  インデックス スキーマにより、検索可能なコンテンツの構造が決まります。 

検索サービスの 2 つの主なワークロードは、" *インデックスの作成* " と " *クエリの実行* " です。

+ インデックスを作成すると、テキストが検索サービスに取り込まれて検索可能になります。 内部では、受信テキストはトークンへと処理され、高速スキャンできるように逆インデックスに格納されます。 インデックス作成中には、" *コグニティブ スキル* " を追加することもできます (Microsoft が提供する事前定義済みのもの、またはお客様が作成したカスタム スキル)。 その後の分析と変換によって、以前は存在していなかった新しい情報と構造が生成されるため、さまざまな検索およびナレッジ マイニング シナリオに対応した高度な実用性が実現します。

+ インデックスに検索可能なデータが設定されると、クライアント アプリは検索サービスにクエリ要求を送信して、応答を処理できるようになります。 すべてのクエリは、お客様のサービス内で作成、所有、保存する検索インデックスに対して実行されます。 クライアント アプリでの検索エクスペリエンスは Azure Cognitive Search の API を使用して定義され、関連性のチューニング、オートコンプリート、シノニムの一致、あいまい一致、パターン マッチング、フィルター、および並べ替えを含めることができます。

機能は、情報の検索に固有の複雑さを感じさせないシンプルな [REST API](/rest/api/searchservice/) または [.NET SDK](search-howto-dotnet-sdk.md) を使って公開されます。 また、Azure portal を使用して、サービスの管理とコンテンツの管理を行うこともできます。インデックスとスキルセットのプロトタイプ作成やクエリを実行するためのツールが用意されています。 サービスはクラウドで実行されるため、インフラストラクチャと可用性は Microsoft によって管理されます。

## <a name="when-to-use-cognitive-search"></a>Cognitive Search の用途

Azure Cognitive Search は、次のアプリケーション シナリオに適しています。

+ ユーザー定義のプライベートな検索インデックスへの異種コンテンツ タイプの統合。 任意のソースの JSON ドキュメントのストリームから検索インデックスを作成できます。 Azure でサポートされているソースについては、" *インデクサー* " を使用してインデックス作成を自動化できます。 インデックス スキーマと更新スケジュールを制御できることが、Cognitive Search を使用する主要な理由となります。

+ 検索に関連する機能の簡単な実装。 Search API シリーズを使用すると、クエリの構築、ファセット ナビゲーション、フィルター (地理空間検索を含む)、シノニム マッピング、オートコンプリート、および関連性チューニングを簡素化できます。 組み込みの機能を使用して、商用 Web 検索エンジンと同様の検索体験に対するエンドユーザーの期待に応えることができます。

+ 生コンテンツは、Azure Blob Storage や Cosmos DB に格納されている、未区分の大きなテキストまたは画像ファイルあるいはアプリケーション ファイルです。 インデックスの作成中に[コグニティブ スキル](cognitive-search-concept-intro.md)を適用することで、テキストの識別と抽出、構造の作成、または新しい情報 (翻訳されたテキストやエンティティなど) の作成を行うことができます。

+ コンテンツには、言語分析またはカスタムのテキスト分析が必要です。 英語以外のコンテンツがある場合、Azure Cognitive Search では、Lucene アナライザーと Microsoft の自然言語プロセッサの両方がサポートされます。 また、生コンテンツの特殊な処理 (分音記号のフィルター処理や、文字列内のパターンの認識と保持など) を実行するようにアナライザーを構成することもできます。

特定の機能の詳細については、「[Azure Cognitive Search の機能](search-features-list.md)」を参照してください

## <a name="how-to-use-cognitive-search"></a>Cognitive Search の使用方法

### <a name="step-1-provision-service"></a>手順 1:サービスのプロビジョニング

他のサブスクライバーと共有する[無料サービスを作成する](search-create-service-portal.md)ことも、自分のサービスのみで使用するリソース専用の[有料レベル](https://azure.microsoft.com/pricing/details/search/)を選択することもできます。 すべてのクイック スタートとチュートリアルは、無料のサービスで完了できます。

有料レベルでは、実稼働要件に基づいて 2 つのディメンションでサービスをスケーリングして、リソースを調整できます。

+ 大量のクエリ負荷を処理できるように、レプリカを追加して容量を拡張する
+ より多くのドキュメントに対応できるように、パーティションを追加してストレージを拡大する

### <a name="step-2-create-an-index"></a>手順 2:インデックスを作成する

データベースのフィールドに似た、検索対象ドキュメントの構造が反映されるように、マップするインデックス スキーマを定義します。 検索インデックスは、クエリを高速で実行するために最適化された特殊なデータ構造です。

通常、[インデックス スキーマは Azure portal で作成する](search-what-is-an-index.md)か、[.NET SDK](search-howto-dotnet-sdk.md) または [REST API](/rest/api/searchservice/) を使用してプログラムによって作成します。

> [!TIP]
> [データのインポート ウィザードに関するクイックスタート](search-get-started-portal.md)を開始すると、数分でインデックスの作成、読み込み、クエリを実行できます。

### <a name="step-3-load-data"></a>手順 3:データの読み込み

インデックスを定義したら、コンテンツをアップロードする準備が完了します。 プッシュ モデルまたはプル モデルを使用できます。

プッシュ モデルでは、[SDK](search-howto-dotnet-sdk.md) または [REST](/rest/api/searchservice/addupdate-or-delete-documents) の API を使用して、JSON ドキュメントをインデックスに "プッシュ" します。 ドキュメントが JSON である限り、ほぼすべてのデータ ソースを外部データセットとして指定できます。

プル モデルでは、Azure 上のソースからデータを "プル" し、検索インデックスに送信します。 プル モデルは、データの接続、読み取り、シリアル化など、データ インジェストのさまざまな側面を効率化および自動化する " [*インデクサー*](/rest/api/searchservice/Indexer-operations)" を通じて実装されます。 サポートされているデータ ソースとしては、Azure Cosmos DB、Azure SQL、Azure Storage などがあります。

### <a name="step-4-send-queries-and-handle-responses"></a>手順 4:クエリの送信と応答の処理

インデックスを入力したら、[REST API](/rest/api/searchservice/Search-Documents) または [.NET SDK](/dotnet/api/microsoft.azure.search.idocumentsoperations) によって簡単な HTTP 要求を使用して、サービス エンドポイントに[検索クエリを発行](search-query-overview.md)できます。

[初めての検索アプリの作成](tutorial-csharp-create-first-app.md)を行うことで、ユーザー入力を収集して結果を処理する Web ページをビルドして拡張します。 [対話型 REST 向けの Postman](search-get-started-postman.md) 呼び出しや Azure portal の組み込みの[検索エクスプローラー](search-explorer.md)を使用して、既存のインデックスに対するクエリを実行することもできます。

## <a name="how-it-compares"></a>他のソリューションとの比較

お客様から、Azure Cognitive Search が他の検索に関連するソリューションと比較してどうなのかよくお問い合わせいただきます。 主な相違点を次の表に示します。

| 比較対象 | 主な相違点 |
|-------------|-----------------|
| Microsoft Search | [Microsoft Search](https://docs.microsoft.com/microsoftsearch/overview-microsoft-search) は、SharePoint 内のコンテンツに対してクエリを実行する必要がある、Microsoft 365 の認証済みユーザーを対象としています。 すぐに使用できる検索環境として提供され、管理者によって有効にされて構成が行われます。Microsoft や他のソースから提供されているコネクタを通じて外部コンテンツを受け入れることができます。 それが目的に合致していれば、Microsoft Search と Microsoft 365 は、試す価値のある選択肢となります。<br/><br/>これに対し、Azure Cognitive Search は、所有するデータやドキュメントを、多くの場合さまざまなソースから取り込んで定義したインデックスに対してクエリを実行します。 Azure Cognitive Search は[インデクサー](search-indexer-overview.md)により複数の Azure データ ソースに対するクローラー機能を備えていますが、インデックス スキーマに準拠している任意の JSON ドキュメントを 1 つの統合された検索可能なリソースにプッシュできます。 また、機械学習と字句解析器を含むようにインデックス作成パイプラインをカスタマイズすることもできます。 Cognitive Search は、より大きなソリューションのプラグイン コンポーネントとなるように作成されているため、プラットフォームにかかわらず、ほぼすべてのアプリに検索機能を統合することができます。|
|Bing | [Bing Web Search API](../cognitive-services/bing-web-search/index.yml) は Bing.com のインデックスで送信された語句と一致するものを検索します。 インデックスは、公開サイトの HTML、XML、および他の Web コンテンツから構築されます。 同じ基板上に構築されている [Bing Custom Search](/azure/cognitive-services/bing-custom-search/) は、個々の Web サイトに範囲指定して、各 Web コンテンツ タイプに対して同じクローラー テクノロジを提供します。<br/><br/>Cognitive Search では、インデックスを自分で定義してデータを投入することができます。 [インデクサー](search-indexer-overview.md)を使用して Azure データ ソース上のデータをクロールしたり、インデックスに適合する JSON ドキュメントを検索サービスにプッシュしたりすることができます。 |
|データベース検索 | 多くのデータベース プラットフォームには、組み込みの検索エクスペリエンスが含まれます。 SQL Server には[フル テキスト検索](/sql/relational-databases/search/full-text-search)があります。 Cosmos DB や同種のテクノロジには、クエリ可能なインデックスがあります。 検索とストレージを結合する製品を評価するとき、採用する方法を決めるのが困難な場合があります。 多くのソリューションでは両方が採用され、ストレージには DBMS が、専用の検索機能には Azure Cognitive Search が使用されています。<br/><br/>DBMS 検索と比較すると、Azure Cognitive Search は異種ソースからのコンテンツを格納し、[56 言語](/rest/api/searchservice/language-support)で言語対応のテキスト処理 (語幹検索、レンマ化、語形) などの専用のテキスト処理機能を提供します。 また、スペルミスの単語のオート コレクト、[シノニム](/rest/api/searchservice/synonym-map-operations)、[候補](/rest/api/searchservice/suggestions)、[スコアリング コントロール](/rest/api/searchservice/add-scoring-profiles-to-a-search-index)、[ファセット](./search-filters-facets.md)、および[カスタムトークン化](/rest/api/searchservice/custom-analyzers-in-azure-search)もサポートしています。 Azure Cognitive Search の[フル テキスト検索エンジン](search-lucene-query-architecture.md)は情報取得の業界標準である Apache Lucene に基づいて構築されています。 ただし、Azure Cognitive Search では逆インデックスの形式でデータを保持しますが、真のデータ ストレージの代わりにはならないため、その役割として使用することはお勧めしません。 詳しくは、こちらの[フォーラム投稿](https://stackoverflow.com/questions/40101159/can-azure-search-be-used-as-a-primary-database-for-some-data)をご覧ください。 <br/><br/>リソース使用率も、このカテゴリで別途考慮する必要があります。 インデックス付けと一部のクエリ操作は、多くの場合、計算を大量に使用します。 DBMS からクラウドの専用ソリューションに検索をオフロードすることにより、トランザクション処理用のシステム リソースを保持します。 さらに、検索を外部化することで、クエリのボリュームに合わせてスケールを簡単に調整できます。|
|専用の検索ソリューション | 全機能を持つ専用の検索を使用する場合、最後の比較カテゴリはオンプレミス ソリューションかクラウド サービスかです。 多くの検索テクノロジには、インデックスの作成とクエリ パイプラインの管理、豊富なクエリとフィルター構文へのアクセス、ランクと関連性の管理、自主的な高度な検索の機能が用意されています。 <br/><br/>クラウド サービスは、オーバーヘッドとメンテナンスが最小限で済み、スケールが調整可能なターンキー ソリューションを望むお客様に最適の選択肢です。 <br/><br/>クラウドの枠組みでは、複数のプロバイダーがフルテキスト検索、地理空間検索、検索入力の程度のあいまいさを処理する機能など、同等の基本機能を備えるソリューションを提供しています。 使用環境に最も合うものを判断する場合、通常は、[特化した機能](search-features-list.md)か、API、ツール、管理の容易さと全体的な単純さが決め手になります。 |

クラウド プロバイダーの中で Azure Cognitive Search は、Azure 上のコンテンツ ストアとデータベースに対するフルテキスト検索のワークロードに関して最も強力で、情報の取得とコンテンツのナビゲーションの両方を主に検索に依存するアプリに適しています。 

以下のような大きな強みがあります。

+ インデックス層での Azure データ統合 (クローラー)
+ 中央管理のための Azure Portal
+ Azure のスケール、信頼性、世界クラスの可用性
+ 画像からのテキストや非構造化コンテンツ内でのパターンの検索など、より検索しやすくするための生データの AI 処理。
+ 56 の言語での堅実なフルテキスト検索を実現するアナライザーによる言語分析とカスタム分析
+ [検索中心のアプリに共通のコア機能](search-features-list.md): スコアリング、ファセット、検索候補、シノニム、地理空間検索など。

Microsoft のお客様は、オンライン カタログ、基幹業務プログラム、およびドキュメント検索アプリケーションを含む、Azure Cognitive Search の機能を最大限利用できます。

## <a name="watch-this-video"></a>次の動画をご覧ください

この 15 分間のビデオでは、プログラム マネージャー Luis Cabrera が Azure Cognitive Search を紹介しています。

>[!VIDEO https://www.youtube.com/embed/kOJU0YZodVk?version=3]