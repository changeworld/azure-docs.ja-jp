---
title: Azure Cognitive Search の新機能
description: Azure Cognitive Search への Azure Search のサービス名の変更を含む、新機能や拡張機能についてのお知らせです。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 02/09/2021
ms.custom: references_regions
ms.openlocfilehash: 13cb22c178be29af71b57d0f50fdbd0e95718069
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/10/2021
ms.locfileid: "100104605"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Azure Cognitive Search の新機能

サービス内の新機能について説明します。 このページをブックマークして、常にサービスの最新情報を確認してください。 パブリック プレビュー段階の機能については、「[プレビュー機能一覧](search-api-preview.md)」をご覧ください。

## <a name="february-2021"></a>2021 年 2 月

|機能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  説明 | 可用性  |
|------------------------------|---------------|---------------|
| [ドキュメントのリセット (プレビュー)](search-howto-run-reset-indexers.md) |  インデクサー ワークロードで個別に選択された検索ドキュメントを再処理します。 | [Search REST API 2020-06-30-Preview](/rest/api/searchservice/index-preview) |
| [可用性ゾーン](search-performance-optimization.md#availability-zones)| [こちらの記事](search-performance-optimization.md#availability-zones)に記載されているように、特定のリージョンに 2 つ以上のレプリカがある Search サービスは、2 つ以上の異なる物理的な場所にレプリカを配置することで回復性が得られます。  | 検索サービスを作成したリージョンと作成日によって可用性が決まります。 詳細については、パフォーマンス チューニングに関するドキュメントをご覧ください。 |

## <a name="january-2021"></a>2021 年 1 月

|機能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  説明 | 可用性  |
|------------------------------|-------------|---------------|
| [Azure Cognitive Search および QnA Maker のソリューション アクセラレータ](https://github.com/Azure-Samples/search-qna-maker-accelerator) | ドキュメントから質問と回答を引き出し、最も関連性の高い回答を提示します。 [https://aka.ms/qnaWithAzureSearchDemo](https://aka.ms/qnaWithAzureSearchDemo) にライブ デモ アプリがあります。  | オープンソース プロジェクト (SLA なし) |

## <a name="2020-archive"></a>2020 年アーカイブ

| 月 | 機能 | 説明 |
|-------|---------|-------------|
| November | [カスタマー マネージド キー暗号化 (拡張)](search-security-manage-encryption-keys.md) | 検索サービスによって作成および管理されるすべての資産にわたって、カスタマー マネージド暗号化が拡張されます。 一般提供されています。|
| September | [Azure Cognitive Search 用 Visual Studio Code 拡張機能](search-get-started-vs-code.md) | ワークスペース、ナビゲーション、IntelliSense、およびインデックス、インデクサー、データ ソース、スキルセットを作成するためのテンプレートが追加されます。 | パブリック プレビュー |
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
|January | [インバウンド ファイアウォールをサポートするための IP 規則 (プレビュー)](service-configure-firewall.md) | [CreateOrUpdate API](/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service) の新しい **IpRule** および **NetworkRuleSet** プロパティ。  |
|January | [プライベート エンドポイントを作成する (プレビュー)](service-create-private-endpoint.md) | 検索サービスへの接続をセキュリティで保護するために Private Link を設定します。 このプレビュー機能には、ソリューションの一部として、[Azure Private Link](../private-link/private-link-overview.md) と [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) の依存関係があります。 |

## <a name="2019-archive"></a>2019 年アーカイブ

| 月 | 機能 | 説明 |
|-------|---------|-------------|
|12 月 | [デモ アプリの作成 (プレビュー)](search-create-app-portal.md) | インデックスへのクエリ (読み取り専用) アクセスを使用してダウンロード可能な HTML ファイルを生成するウィザード。完全なクライアント アプリへの近道ではなく、検証およびテスト ツールとして使用することを目的としています。|
|November | [インクリメンタル エンリッチメント (プレビュー)](cognitive-search-incremental-indexing-conceptual.md) | 今後、再利用するために、スキルセット処理をキャッシュします。  |
|November | [ドキュメント抽出スキル (プレビュー)](cognitive-search-skill-document-extraction.md) | スキルセット内からファイルの内容を抽出するコグニティブ スキル。|
|November | [テキスト翻訳スキル](cognitive-search-skill-text-translation.md) | テキストを評価し、翻訳するコグニティブ スキル。インデックス作成中に使用されます。 一般提供されています。|
|November | [Power BI テンプレート](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) | ナレッジ ストアのコンテンツを視覚化するためのテンプレート。 |
|November | [Azure Data Lake Storage Gen2 (プレビュー)](search-howto-index-azure-data-lake-storage.md)、[Cosmos DB Gremlin API (プレビュー)](search-howto-index-cosmosdb.md)、[Cosmos DB Cassandra API (プレビュー)](search-howto-index-cosmosdb.md) | パブリック プレビュー段階にある新しいインデクサー データ ソース。 |
|7 月 | [Azure Government クラウドのサポート](../azure-government/compare-azure-government-global-azure.md#azure-cognitive-search) | 一般提供されています。|

<a name="new-service-name"></a>

## <a name="new-service-name"></a>新しいサービス名

Azure Search は、コア操作でのコグニティブ スキルと AI 処理の利用拡大 (任意) を反映するために、2019 年 10 月に **Azure Cognitive Search** に名前が変更されました。 API のバージョン、NuGet のパッケージ、名前空間、およびエンドポイントは変更されません。 新規および既存の検索ソリューションは、サービス名の変更の影響を受けません。

## <a name="service-updates"></a>サービスの更新情報

Azure Cognitive Search の[サービス更新のお知らせ](https://azure.microsoft.com/updates/?product=search&status=all)に関する情報については、Azure の Web サイトで参照できます。