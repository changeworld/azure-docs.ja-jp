---
title: Azure Cognitive Search の新機能
description: Azure Cognitive Search への Azure Search のサービス名の変更を含む、新機能や拡張機能についてのお知らせです。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 07/20/2021
ms.custom: references_regions
ms.openlocfilehash: a3554cee4d5538fc8291ef2a2db5c852c33b137a
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131503586"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Azure Cognitive Search の新機能

サービス内の新機能について説明します。 このページをブックマークして、常にサービスの最新情報を確認してください。 まだ一般提供されていない機能の包括的な一覧を確認するには、[プレビュー段階の機能の一覧](search-api-preview.md)に関するページを参照してください。

## <a name="november-2021"></a>2021 年 11 月

|機能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  説明 | 可用性  |
|------------------------------------|--------------|---------------|
| [Azure Files インデクサー (プレビュー)](./search-file-storage-integration.md) | [Azure Files](https://azure.microsoft.com/services/storage/files/) のインデクサーを作成するために REST API サポートを追加します | パブリック プレビュー |

## <a name="july-2021"></a>2021 年 7 月

|機能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  説明 | 可用性  |
|------------------------------------|--------------|---------------|
| [Search REST API 2021-04-30-Preview](/rest/api/searchservice/index-preview) | [マネージド ID](search-howto-managed-identities-data-sources.md) と Azure Active Directory (Azure AD) 認証を使用して行われるインデクサー接続の REST API サポートを追加します。 | パブリック プレビュー |
| [ロールベースの承認 (プレビュー)](search-security-rbac.md) | データ プレーンからインデックスとインデックス作成へのアクセスのために Azure Active Directory と新しい組み込みのロールを使用して認証することで、API キーへの依存を解消または軽減します。 | パブリック プレビュー ([要求による](./search-security-rbac.md?tabs=config-svc-portal%2croles-portal%2ctest-portal#step-1-preview-sign-up))。 サブスクリプションのオンボード化後、Azure portal または管理 REST API バージョン 2021-04-01- プレビューを使用して、データ プレーン認証用に検索サービスを構成します。|
| [管理 REST API 2021-04-01-Preview](/rest/api/searchmanagement/) | 新しい [DataPlaneAuthOptions](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#dataplaneauthoptions) をサポートするために [[サービスの作成または更新]](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update) を変更します。 | パブリック プレビュー |

## <a name="may-2021"></a>2021 年 5 月

|機能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  説明 | 可用性  |
|------------------------------------|--------------|---------------|
| [Power Query コネクタのサポート (プレビュー)](search-how-to-index-power-query-data-sources.md) | インデクサーで、他のクラウド プラットフォームからインデックスを作成できるようになりました。 インデクサーを使用してインデックス作成に外部データ ソースをクロールする場合、Power Query コネクタを使用して Amazon Redshift、Elasticsearch、PostgreSQL、Salesforce オブジェクト、Salesforce レポート、Smartsheet、Snowflake に接続できるようになりました。 </br></br>[お知らせ (テクニカルコミュニティのブログ)](https://techcommunity.microsoft.com/t5/azure-ai/azure-cognitive-search-indexers-allow-you-to-ingest-data-from/ba-p/2381988)  | パブリック プレビュー ([リクエストによる)](https://aka.ms/azure-cognitive-search/indexer-preview)、REST api-version=2020-06-30-Preview と Azure portal を使用。 |
|[Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md) | インデクサーで使用される ADLS Gen2 データ ソースの一般提供が開始されました。 | 一般提供、REST api-version=2020-06-30 と Azure portal を使用。 |
|[MySQL のサポート (プレビュー)](search-howto-index-mysql.md) | インデクサーベースのインデックス作成のために、Azure MySQL のプレビュー データ ソースのサポートを発表します。 | パブリック プレビュー、REST api-version=2020-06-30-Preview、[.NET SDK 11.2.1](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype.mysql)、および Azure portal。 |
| [スペル チェックとセマンティック結果のためのその他の queryLanguage](/rest/api/searchservice/preview-api/search-documents#queryLanguage) | スペル チェックまたは queryType=semantic を呼び出すクエリ要求の場合、queryLanguage を英語以外の [38 の言語](/rest/api/searchservice/preview-api/search-documents#queryLanguage)に設定できるようになりました。 </br></br>[お知らせ (テクニカルコミュニティのブログ)](https://techcommunity.microsoft.com/t5/azure-ai/introducing-multilingual-support-for-semantic-search-on-azure/ba-p/2385110) | パブリック プレビュー ([要求による](https://aka.ms/SemanticSearchPreviewSignup))。 </br></br>[ドキュメントの検索 (REST)](/rest/api/searchservice/preview-api/search-documents) api-version=2020-06-30-Preview、[Azure.Search.Documents 11.3.0-beta.2](https://www.nuget.org/packages/Azure.Search.Documents/11.3.0-beta.2)、または Azure portal の [Search エクスプローラー](search-explorer.md)を使用します。 </br></br>[リージョンとレベル](semantic-search-overview.md#availability-and-pricing)の制限が適用されます。 |
| [二重暗号化の可用性](search-security-manage-encryption-keys.md#double-encryption) | カスタマー マネージド キーを使用して暗号化された検索インデックスとオブジェクトについては、サポートされているすべてのリージョンに二重暗号化 (静的ディスクと一時ディスクの両方の暗号化) が実装されました。 | すべてのリージョンで、[サービスの作成日](search-security-manage-encryption-keys.md#double-encryption)が適用されます。 |

## <a name="april-2021"></a>2021 年 4 月

|機能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  説明 | 可用性  |
|------------------------------|---------------|---------------|
| [Gremlin API サポート (プレビュー)](search-howto-index-cosmosdb-gremlin.md) | インデクサーベースのインデックス作成で、Gremlin API を介してアクセスされる Cosmos DB からコンテンツを取得するデータ ソースを作成できるようになりました。 | api-version=2020-06-30-Preview を使用したパブリック プレビュー ([リクエストによる](https://aka.ms/azure-cognitive-search/indexer-preview))。 |

## <a name="march-2021"></a>2021 年 3 月

|機能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  説明 | 可用性  |
|------------------------------|---------------|---------------|
| [セマンティック検索 (プレビュー)](semantic-search-overview.md) | クエリ要求の最小限の調整で検索結果の関連性を大幅に向上させる、クエリ関連の機能のコレクション。 </br></br>[セマンティックの順位付け](semantic-ranking.md)は、単語と内容の裏にあるセマンティックな意味を使用して関連性スコアを計算します。 </br></br>[セマンティック キャプション](semantic-how-to-query-request.md)は、当該ドキュメントを最も的確に要約している、関連性の高い一節を、最も重要な用語や語句を強調してドキュメントから返します。 </br></br>[セマンティック回答](semantic-answers.md)は、検索ドキュメントから抽出された重要な一節を返します。これは、クエリ (質問のような形式) に対する直接回答の形式で作成されます。 | パブリック プレビュー ([要求による](https://aka.ms/SemanticSearchPreviewSignup))。 </br></br>Azure portal で、[Search Documents (REST)](/rest/api/searchservice/preview-api/search-documents) api-version = 2020-06-30-Preview または[検索エクスプローラー](search-explorer.md)を使用してください。 </br></br>リージョンとレベルの制限が適用されます。 |
| [クエリ用語のスペル チェック (プレビュー)](speller-how-to-add.md) | クエリの用語が検索エンジンに到達する前に、スペル ミスをチェックすることができます。 `speller` オプションは、任意の種類のクエリ (単純、フル、またはセマンティック) で動作します。 |  パブリック プレビュー、REST のみ、api-version=2020-06-30-Preview|
| [SharePoint Online インデクサー (プレビュー)](search-howto-index-sharepoint-online.md) | ドキュメント ライブラリのコンテンツにインデックスを付けることができるように、このインデクサーによって SharePoint Online サイトに接続されます。 | パブリック プレビュー、REST のみ、api-version=2020-06-30-Preview |
| [ノーマライザー (プレビュー)](search-normalizers.md) | ノーマライザーでは、完全なテキスト分析チェーンを呼び出さずに、一貫した大文字と小文字の区別、アクセントの削除、ASCII フォールディングなど、単純なテキストの事前処理が提供されます。| パブリック プレビュー、REST のみ、api-version=2020-06-30-Preview |
| [カスタム エンティティの参照スキル](cognitive-search-skill-custom-entity-lookup.md ) |  ユーザーが定義したカスタムの単語と語句のリストからテキストを検索するコグニティブ スキル。 この一覧を使用して、エンティティが一致するすべての文書がラベル付けされます。 このスキルは、ある程度のあいまい一致もサポートしており、類似しているが完全一致ではない一致を見つけるために適用できます。 | 一般提供されています。 |

## <a name="february-2021"></a>2021 年 2 月

|機能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  説明 | 可用性  |
|------------------------------|---------------|---------------|
| [ドキュメントのリセット (プレビュー)](search-howto-run-reset-indexers.md) |  インデクサー ワークロードで個別に選択された検索ドキュメントを再処理します。 | [Search REST API 2020-06-30-Preview](/rest/api/searchservice/index-preview) |
| [可用性ゾーン](search-performance-optimization.md#availability-zones)| 「[パフォーマンスのためのスケール](search-performance-optimization.md#availability-zones)」に記載されているように、特定のリージョンに 2 つ以上のレプリカがある Search サービスは、2 つ以上の異なる物理的な場所にレプリカを配置することで回復性が得られます。  | 検索サービスを作成したリージョンと作成日によって可用性が決まります。 詳細については、パフォーマンスのスケーリングに関する記事を参照してください。 |
| [Azure CLI](/cli/azure/search) </br>[Azure PowerShell](/powershell/module/az.search/) | 新しいリビジョンでは、IP ファイアウォール規則とプライベート エンドポイントのサポートを含め、Management REST API 2020-08-01 のあらゆる種類の操作が利用できるようになりました。 | 一般提供されています。 |

## <a name="january-2021"></a>2021 年 1 月

|機能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  説明 | 可用性  |
|------------------------------|-------------|---------------|
| [Azure Cognitive Search および QnA Maker のソリューション アクセラレータ](https://github.com/Azure-Samples/search-qna-maker-accelerator) | ドキュメントから質問と回答を引き出し、最も関連性の高い回答を提示します。 [https://aka.ms/qnaWithAzureSearchDemo](https://aka.ms/qnaWithAzureSearchDemo) にライブ デモ アプリがあります。  | オープンソース プロジェクト (SLA なし) |

## <a name="2020-archive"></a>2020 年アーカイブ

| 月 | 機能 | 説明 |
|-------|---------|-------------|
| November | [カスタマー マネージド キー暗号化 (拡張)](search-security-manage-encryption-keys.md) | 検索サービスによって作成および管理されるすべての資産にわたって、カスタマー マネージド暗号化が拡張されます。 一般提供されています。|
| September | [Azure Cognitive Search 用 Visual Studio Code 拡張機能](search-get-started-vs-code.md) | ワークスペース、ナビゲーション、IntelliSense、およびインデックス、インデクサー、データ ソース、スキルセットを作成するためのテンプレートが追加されます。 現在、この機能はパブリック プレビュー段階にあります。| 
| September | [マネージド サービス ID (インデクサー)](search-howto-managed-identities-data-sources.md) | 一般提供されています。  |
| September | [プライベート リンクを使用したアウトバウンド要求](search-indexer-howto-access-private.md) | 一般提供されています。  |
| September | [Management REST API (2020-08-01)](/rest/api/searchmanagement/management-api-versions) | 一般提供されています。 |
| September | [Management REST API (2020-08-01-Preview)](/rest/api/searchmanagement/management-api-versions) | Azure Functions と Azure SQL for MySQL Databases 用の共有プライベート リンク リソースを追加します。 |
| September | [Management .NET SDK 4.0](/dotnet/api/overview/azure/search/management) |  Azure SDK の更新です。REST API バージョン 2020-08-01 を対象とする管理 SDK に適用されます。 一般提供されています。|
| 8 月 | [二重暗号化](search-security-overview.md#encryption) | 次のリージョンで 2020 年 8 月 1 日以降に作成されたすべての検索サービスで一般提供されます: 米国西部 2、米国東部、米国中南部、US Gov バージニア、US Gov アリゾナ。 |
| 7 月 | [Azure.Search.Documents クライアント ライブラリ](/dotnet/api/overview/azure/search.documents-readme) | Azure SDK for .NET。一般提供されています。 |
| 7 月 | [azure.search.documents クライアント ライブラリ](/python/api/overview/azure/search-documents-readme)  | Azure SDK for Python。一般提供されています。 |
| 7 月 | [@azure/search-documents クライアント ライブラリ](/javascript/api/overview/azure/search-documents-readme)  | Azure SDK for JavaScript。一般提供されています。 |
| 6 月 | [ナレッジ ストア](knowledge-store-concept-intro.md) | 一般提供されています。 |
| 6 月 | [Search REST API 2020-06-30](/rest/api/searchservice/) | 一般提供されています。 |
| 6 月 | [Search REST API 2020-06-30-Preview](/rest/api/searchservice/) | スキルを選択的に再処理する "スキルセットのリセット" と、インクリメンタル エンリッチメントが追加されました。 |
| 6 月 | [Okapi BM25 関連性アルゴリズム](index-ranking-similarity.md) | 一般提供されています。 |
| 6 月 |  **executionEnvironment** (Azure Private Link を使用する検索サービスに適用) | 一般提供されています。 |
| 6 月 | [AML スキル (プレビュー)](cognitive-search-aml-skill.md) | カスタム Azure Machine Learning (AML) モデルを使用して AI エンリッチメントを拡張するコグニティブ スキル。 |
| May | [デバッグ セッション (プレビュー)](cognitive-search-debug-session.md) | ポータルのスキルセット デバッガー。  |
| May | [インバウンド ファイアウォールをサポートするための IP 規則](service-configure-firewall.md) | 一般提供されています。  |
| May | [プライベート検索エンドポイント用の Azure Private Link](service-create-private-endpoint.md) | 一般提供されています。  |
| May | [マネージド サービス ID (インデクサー) - (プレビュー)](search-howto-managed-identities-data-sources.md) | マネージド ID を使用して Azure データ ソースに接続します。  |
| May | [sessionId クエリ パラメーター](index-similarity-and-scoring.md)、[scoringStatistics=global パラメーター](index-similarity-and-scoring.md#scoring-statistics)  | グローバル検索統計。[検索の関連性のための機械学習 (LearnToRank) モデル](https://github.com/Azure-Samples/search-ranking-tutorial)に役立ちます。  |
| May | [featuresMode 関連性スコアの拡張 (プレビュー)](index-similarity-and-scoring.md#featuresMode-param)  |   |
|3 月  | [ネイティブ BLOB の論理的な削除 (プレビュー)](search-howto-index-changed-deleted-blobs.md) | Blob Storage でソース BLOB が論理的に削除された場合に検索ドキュメントを削除します。 |
|3 月  | [Management REST API (2020-03-13)](/rest/api/searchmanagement/management-api-versions) | 一般提供されています。 |
|Ferbruary | [PII 検出スキル (プレビュー)](cognitive-search-skill-pii-detection.md)  | 個人情報を抽出してマスクするコグニティブ スキル。 |
|Ferbruary | [カスタム エンティティの参照スキル (プレビュー)](cognitive-search-skill-custom-entity-lookup.md) | リストから単語や語句を検索し、一致するエンティティを含むすべてのドキュメントにラベルを付けるコグニティブ スキル。  |
|January | [カスタマー マネージド キー暗号化](search-security-manage-encryption-keys.md) | 一般公開  |
|January | [インバウンド ファイアウォールをサポートするための IP 規則 (プレビュー)](service-configure-firewall.md) | [CreateOrUpdate API](/rest/api/searchmanagement/2020-08-01/services/create-or-update) の新しい **IpRule** および **NetworkRuleSet** プロパティ。  |
|January | [プライベート エンドポイントを作成する (プレビュー)](service-create-private-endpoint.md) | 検索サービスへの接続をセキュリティで保護するために Private Link を設定します。 このプレビュー機能には、ソリューションの一部として、[Azure Private Link](../private-link/private-link-overview.md) と [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) の依存関係があります。 |

## <a name="2019-archive"></a>2019 年アーカイブ

| 月 | 機能 | 説明 |
|-------|---------|-------------|
|12 月 | [デモ アプリを作成する](search-create-app-portal.md) | インデックスへのクエリ (読み取り専用) アクセスを使用してダウンロード可能な HTML ファイルを生成するウィザード。完全なクライアント アプリへの近道ではなく、検証およびテスト ツールとして使用することを目的としています。|
|November | [インクリメンタル エンリッチメント (プレビュー)](cognitive-search-incremental-indexing-conceptual.md) | 今後、再利用するために、スキルセット処理をキャッシュします。  |
|November | [ドキュメント抽出スキル (プレビュー)](cognitive-search-skill-document-extraction.md) | スキルセット内からファイルの内容を抽出するコグニティブ スキル。|
|November | [テキスト翻訳スキル](cognitive-search-skill-text-translation.md) | テキストを評価し、翻訳するコグニティブ スキル。インデックス作成中に使用されます。 一般提供されています。|
|November | [Power BI テンプレート](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) | ナレッジ ストアのコンテンツを視覚化するためのテンプレート。 |
|November | [Azure Data Lake Storage Gen2 (プレビュー)](search-howto-index-azure-data-lake-storage.md) および [Cosmos DB Gremlin API (プレビュー)](search-howto-index-cosmosdb.md) | パブリック プレビュー段階にある新しいインデクサー データ ソース。 |
|7 月 | [Azure Government クラウドのサポート](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-texas,usgov-virginia&products=search) | 一般提供されています。|

<a name="new-service-name"></a>

## <a name="new-service-name"></a>新しいサービス名

Azure Search は、コア操作でのコグニティブ スキルと AI 処理の利用拡大 (任意) を反映するために、2019 年 10 月に **Azure Cognitive Search** に名前が変更されました。 API のバージョン、NuGet のパッケージ、名前空間、およびエンドポイントは変更されません。 新規および既存の検索ソリューションは、サービス名の変更の影響を受けません。

## <a name="service-updates"></a>サービスの更新情報

Azure Cognitive Search の[サービス更新のお知らせ](https://azure.microsoft.com/updates/?product=search&status=all)に関する情報については、Azure の Web サイトで参照できます。