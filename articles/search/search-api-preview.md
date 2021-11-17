---
title: プレビュー機能一覧
titleSuffix: Azure Cognitive Search
description: プレビュー機能は、お客様が設計とユーティリティに関してフィードバックできるようにリリースされています。 この記事では、現在プレビュー段階にあるすべての機能を一覧表示しています。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/21/2021
ms.openlocfilehash: 968e4320efc8e22a6a561b7c75c89e966045d6ac
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132305828"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Azure Cognitive Search のプレビュー機能

この記事では、パブリック プレビュー段階にあるすべての機能を一覧表示しています。 プレビュー段階の機能はサービス レベル アグリーメントなしで提供しています。運用環境のワークロードに使用することはお勧めできません。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

プレビュー機能が一般公開に切り替えられると、この一覧から削除されます。 以下に記載されていない機能は、一般公開されているとお考えください。 一般公開については、[サービスの更新情報](https://azure.microsoft.com/updates/?product=search)、または[新機能](whats-new.md)に関するページでご確認いただけます。

|機能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | カテゴリ | 説明 | 可用性  |
|---------|------------------|-------------|---------------|
|  [**Azure Files Indexer**](search-file-storage-integration.md) | インデクサー データ ソース | [Azure Files](https://azure.microsoft.com/services/storage/files/) のインデクサーを作成するために REST API サポートを追加します。 | パブリック レビュー、[REST API 2021-04-30-Preview の検索](/rest/api/searchservice/index-preview) |
| [**REST API 2021-04-30-Preview の検索**](/rest/api/searchservice/index-preview) | セキュリティ | 外部データ ソースに接続するインデクサーについて、Azure Active Directory でマネージド ID をサポートするように、[[データ ソースの作成または更新]](/rest/api/searchservice/preview-api/create-or-update-data-source) を変更します。 | パブリック レビュー、[REST API 2021-04-30-Preview の検索](/rest/api/searchservice/index-preview) |
| [**Azure RBAC のサポート**](search-security-rbac.md) | セキュリティ | 新しい組み込みのロールを使用して、インデックスとインデックス作成へのアクセスを制御し、API キーの依存を解消または軽減します。 | パブリック プレビュー ([要求による](./search-security-rbac.md?tabs=config-svc-portal%2croles-portal%2ctest-portal#step-1-preview-sign-up))。 サブスクリプションのオンボード化後、Azure portal または管理 REST API バージョン 2021-04-01-Preview を使用して、データ プレーン認証用に検索サービスを構成します。 |
| [**管理 REST API 2021-04-01-Preview**](/rest/api/searchmanagement/) | セキュリティ | 新しい [DataPlaneAuthOptions](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#dataplaneauthoptions) をサポートするために [[サービスの作成または更新]](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update) を変更します。 | パブリック プレビュー、[管理 REST API](/rest/api/searchmanagement/)、API バージョン 2021-04-01-Preview。|
| [**ドキュメントのリセット**](search-howto-run-reset-indexers.md) | Indexer | インデクサー ワークロードで個別に選択された検索ドキュメントを再処理します。 | [ドキュメントのリセット REST API](/rest/api/searchservice/preview-api/reset-documents)、API バージョン 2021-04-30-Preview または 2020-06-30-Preview を使用します。 |
|  [**Power Query コネクタ**](search-how-to-index-power-query-data-sources.md) | インデクサー データ ソース | インデクサーで、他のクラウド プラットフォームからインデックスを作成できるようになりました。 インデクサーを使用してインデックス作成に外部データ ソースをクロールする場合、Power Query コネクタを使用して Amazon Redshift、Elasticsearch、PostgreSQL、Salesforce オブジェクト、Salesforce レポート、Smartsheet、Snowflake に接続できるようになりました。 | バックエンドでご自分のサブスクリプションがサポートされるように、[サインアップ](https://aka.ms/azure-cognitive-search/indexer-preview)する必要があります。 このデータ ソースを構成するには、[[データ ソースの作成または更新]](/rest/api/searchservice/preview-api/create-or-update-data-source)、API バージョン 2021-04-30-Preview または 2020-06-30-Preview、または Azure portal を使用します。|
| [**SharePoint Online インデクサー**](search-howto-index-sharepoint-online.md) | インデクサー データ ソース | SharePoint コンテンツのインデクサーベースのインデックス作成用の新しいデータソースです。 | バックエンドでご自分のサブスクリプションがサポートされるように、[サインアップ](https://aka.ms/azure-cognitive-search/indexer-preview)する必要があります。 このデータ ソースを構成するには、[[データ ソースの作成または更新]](/rest/api/searchservice/preview-api/create-or-update-data-source)、API バージョン 2021-04-30-Preview または 2020-06-30-Preview、または Azure portal を使用します。 |
|  [**MySQL インデクサー データ ソース**](search-howto-index-mysql.md) | インデクサー データ ソース | Azure MySQL データ ソースからコンテンツとメタデータのインデックスを作成します。| バックエンドでご自分のサブスクリプションがサポートされるように、[サインアップ](https://aka.ms/azure-cognitive-search/indexer-preview)する必要があります。 このデータ ソースを構成するには、[[データ ソースの作成または更新]](/rest/api/searchservice/preview-api/create-or-update-data-source)、API バージョン 2021-04-30-Preview または 2020-06-30-Preview、[.NET SDK 11.2.1](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype.mysql)、または Azure portal を使用します。 |
| [**Cosmos DB インデクサｰ: MongoDB API、Gremlin API**](search-howto-index-cosmosdb.md) | インデクサー データ ソース | Cosmos DB には、SQL の API が一般公開されていますが、MongoDB と Gremlin の API はプレビュー段階です。 | MongoDB と Gremlin では、バックエンドでご自分のサブスクリプションがサポートされるように、[最初にサインアップ](https://aka.ms/azure-cognitive-search/indexer-preview)します。 MongoDB データ ソースは、ポータルで構成できます。 このデータ ソースを構成するには、[[データ ソースの作成または更新]](/rest/api/searchservice/preview-api/create-or-update-data-source)、API バージョン 2021-04-30-Preview または 2020-06-30-Preview を使用します。 |
| [**ネイティブ BLOB の論理的な削除**](search-howto-index-changed-deleted-blobs.md) | インデクサー データ ソース | Azure Cognitive Search の Azure Blob Storage インデクサーは、論理的に削除された状態にある BLOB を認識し、インデックス作成時に対応する検索ドキュメントを削除します。 | このデータ ソースを構成するには、[[データ ソースの作成または更新]](/rest/api/searchservice/preview-api/create-or-update-data-source)、API バージョン 2021-04-30-Preview または 2020-06-30-Preview を使用します。 |
| [**セマンティック検索**](semantic-search-overview.md) | 関連性 (スコアリング) | 結果、キャプション、および回答のセマンティック ランク付け。 | セマンティック検索を構成するには、[[ドキュメントの検索]](/rest/api/searchservice/preview-api/search-documents)、API バージョン 2021-04-30-Preview または 2020-06-30-Preview、Search エクスプローラー (ポータル) を使用します。 |
| [**スペル チェック**](cognitive-search-aml-skill.md) | クエリ | 単純、完全、およびセマンティック クエリのクエリ用語入力に対するスペル修正 (オプション)。 | [検索のプレビュー REST API](/rest/api/searchservice/preview-api/search-documents)、API バージョン 2021-04-30-Preview または 2020-06-30-Preview、Search エクスプローラー (ポータル)。 |
| [**ノーマライザー**](search-normalizers.md) | クエリ |  ノーマライザーでは、完全なテキスト分析チェーンを呼び出さずに、一貫した大文字と小文字の区別、アクセントの削除、ASCII フォールディングなど、単純なテキストの事前処理が提供されます。| [[ドキュメントの検索]](/rest/api/searchservice/preview-api/search-documents)、API バージョン 2021-04-30-Preview または 2020-06-30-Preview を使用します。|
| [**featuresMode パラメーター**](/rest/api/searchservice/preview-api/search-documents#query-parameters) | 関連性 (スコアリング) | 関連性のスコアリングで、フィールドごとの類似性スコア、フィールドごとの用語の頻度、およびフィールドごとの一致した一意のトークン数の詳細が含まれ強化されました。 これらのデータ ポイントは、[カスタム スコアリング ソリューション](https://github.com/Azure-Samples/search-ranking-tutorial)で使用できます。 | このクエリ パラメーターを追加するには、[[ドキュメントの検索]](/rest/api/searchservice/preview-api/search-documents)、API バージョン 2021-04-30-Preview、2020-06-30-Preview、または 2019-05-06-Preview を使用します。 |
| [**Azure Machine Learning (AML) スキル**](cognitive-search-aml-skill.md) | AI エンリッチメント (スキル) | Azure Machine Learning からの推論エンドポイントを統合するための新しいスキルの種類です。 [このチュートリアル](cognitive-search-tutorial-aml-custom-skill.md)で作業を開始します。 | [検索のプレビュー REST API](/rest/api/searchservice/)、API バージョン 2021-04-30-Preview、2020-06-30-Preview、または 2019-05-06-Preview を使用します。 また、Cognitive Search と Azure ML サービスが同じサブスクリプションにデプロイされていることを前提として、ポータル (スキルセットの設計) でも利用できます。 |
| [**インクリメンタル エンリッチメント**](cognitive-search-incremental-indexing-conceptual.md) | AI エンリッチメント (スキル) | エンリッチメント パイプラインにキャッシュが追加され、スキルセットや別のオブジェクトに対する更新など、目的の変更によってコンテンツが変更されない場合に既存の出力を再利用できます。 キャッシュは、スキルセットによって生成されるエンリッチされたドキュメントにのみ適用されます。| この構成設定を追加するには、[インデクサｰ プレビューの作成または更新 REST API](/rest/api/searchservice/create-indexer)、API バージョン 2021-04-30-Preview、2020-06-30-Preview、または 2019-05-06-Preview を使用します。 |
| [**デバッグ セッション**](cognitive-search-debug-session.md) | ポータル、AI エンリッチメント (スキル) | スキルセットの問題を調査して解決するために使用する、セッション内スキルセット エディター。 デバッグ セッション中に適用された修正は、サービス内のスキルセットに保存できます。 | ポータルのみで、デバッグ セッションを開くための [概要] ページの中間ページのリンクを使用します。 |
| [**moreLikeThis**](search-more-like-this.md) | クエリ | 特定のドキュメントに関連する別のドキュメントを検索します。 この機能は、以前のプレビューからありました。 | このクエリ パラメーターを [検索ドキュメント プレビュー REST API](/rest/api/searchservice/search-documents) 呼び出しに追加するには、API バージョン 2021-04-30-Preview、2020-06-30-Preview、2019-05-06-Preview、2016-09-01-Preview、または 2017-11-11-Preview を使用します。 |

## <a name="how-to-call-a-preview-rest-api"></a>プレビューの REST API を呼び出す方法

Azure Cognitive Search では常に、まず REST API によって、次に .NET SDK のプレリリース版で実験機能を事前公開しています。

プレビュー機能は、機能の設計と実装に関するフィードバックの収集を目的とするテストと実験のために提供されています。 このため、プレビュー機能は、下位互換性を損なう可能性のある形で、時間の経過と共に変更されることがあります。 これは、下位互換性のある小規模な修正プログラムと機能強化以外は変更される可能性が低い安定した GA バージョンの機能とは対照的です。 また、プレビュー機能は必ずしも GA リリースに反映されません。

一部のプレビュー機能にはポータルと .NET SDK で使用できるものがありますが、REST API には常にプレビュー機能があります。

+ 検索操作の場合、現在のプレビュー バージョンは [ **`2021-04-30-Preview`**](/rest/api/searchservice/index-preview) です。

+ 管理操作の場合、現在のプレビュー バージョンは [ **`2021-04-01-Preview`**](/rest/api/searchmanagement/management-api-versions) です。

古いプレビューはまだ動作しますが、時間がたてば古くなります。 コードで `api-version=2019-05-06-Preview`、`api-version=2016-09-01-Preview` または `api-version=2017-11-11-Preview` を呼び出す場合、これらの呼び出しは引き続き有効ですが、これらのバージョンには新機能は含められず、バグ修正は保証されません。

次のサンプル構文は、API のプレビュー バージョンの呼び出しを示しています。

```HTTP
POST https://[service name].search.windows.net/indexes/hotels-idx/docs/search?api-version=2021-04-30-Preview  
  Content-Type: application/json  
  api-key: [admin key]
```

Azure Cognitive Search サービスは複数のバージョンとクライアント ライブラリで使用できます。 詳しくは、[API バージョン](search-api-versions.md)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ

Search REST プレビュー API リファレンス ドキュメントを確認します。 問題が発生した場合は、[Stack Overflow](https://stackoverflow.com/) または[サポートへの問い合わせ](https://azure.microsoft.com/support/community/?product=search)でサポートを依頼してください。

> [!div class="nextstepaction"]
> [Search Service REST API リファレンス (プレビュー)](/rest/api/searchservice/index-preview)
