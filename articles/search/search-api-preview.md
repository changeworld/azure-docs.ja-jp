---
title: REST API のプレビュー機能
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search サービス REST API バージョン 2019-05-06-Preview には、ナレッジ ストアやインクリメンタル エンリッチメント用のインデクサー キャッシュなど、試験的な機能が含まれています。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: fd21a4b821e1911e94d542a0922e5269786c365d
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2020
ms.locfileid: "76991067"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Azure Cognitive Search のプレビュー機能

この記事では、現在プレビューの段階にある機能を示します。 プレビュー機能が一般公開に切り替えられると、この一覧から削除されます。 一般公開に関するお知らせについては、[サービスの更新情報](https://azure.microsoft.com/updates/?product=search)や[新機能](whats-new.md)を確認できます。

一部のプレビュー機能にはポータルと .NET SDK で使用できるものがありますが、REST API には常にプレビュー機能があります。 

+ 検索操作の場合、現在のプレビュー API バージョンは [`2019-05-06-Preview`](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview) です
+ 管理操作の場合、現在のプレビュー バージョンは [`2019-10-01-Preview`](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) です

> [!IMPORTANT]
> プレビュー段階の機能はサービス レベル アグリーメントなしで提供しています。運用環境のワークロードに使用することはお勧めできません。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="ai-enrichment-features"></a>AI エンリッチメント機能

[プレビュー版の検索 API](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview) を使用して、AI エンリッチメントの最新の機能強化について説明します。

+ [カスタム エンティティ参照スキル (プレビュー)](cognitive-search-skill-custom-entity-lookup.md ) は、単語と語句のカスタムのユーザー定義リストからテキストを検索するコグニティブ スキルです。 このリストを使用して、エンティティが一致するすべての文書がラベル付けされます。 このスキルでは、ある程度のあいまい一致もサポートされており、類似しているが完全一致ではない一致を見つけるために適用できます。 

+ [PII 検出スキル (プレビュー)](cognitive-search-skill-pii-detection.md) はインデックス作成中に使用されるコグニティブ スキルで、入力テキストから個人を特定できる情報を抽出し、それをさまざまな方法でそのテキストからマスクするオプションがユーザーに提供されます。

+ [インクリメンタル エンリッチメント (プレビュー)](cognitive-search-incremental-indexing-conceptual.md) では、エンリッチメント パイプラインにキャッシュが追加され、スキルセットや別のオブジェクトに対する更新など、対象となる変更によりコンテンツが変更されない場合に既存の出力を再利用できます。 キャッシュは、スキルセットによって生成されるエンリッチされたドキュメントにのみ適用されます。

+ [ナレッジ ストア (プレビュー)](knowledge-store-concept-intro.md) は、AI ベースのエンリッチメント パイプラインの新しいコピー先です。 その物理データ構造は Azure Blob Storage と Azure Table Storage に存在し、コグニティブ スキルセットがアタッチされているインデクサーを実行した時点で作成され、データが挿入されます。 ナレッジ ストア自体の定義は、スキルセットの定義の中で指定されます。 ナレッジ ストアの定義では、"*プロジェクション*" 要素によってデータの物理構造 (データの形状をどのようにするか、データの保存先をテーブル ストレージと BLOB ストレージのどちらにするか、ビューが複数存在するかどうか) を制御できます。

## <a name="indexing-and-query-features"></a>インデックス作成機能とクエリ機能

インデクサー プレビュー機能は、プレビュー版の検索 API で使用できます。 

+ [Cosmos DB インデクサー](search-howto-index-cosmosdb.md)では、MongoDB API (プレビュー)、Gremlin API (プレビュー)、Cassandra API (プレビュー) がサポートされています。

+ [Azure Data Lake Storage Gen2 インデクサー (プレビュー)](search-howto-index-azure-data-lake-storage.md) では、Data Lake Storage Gen2 のコンテンツとメタデータにインデックスを付けることができます。

+ [moreLikeThis クエリ パラメーター (プレビュー)](search-more-like-this.md) により、特定のドキュメントに関連するドキュメントが検索されます。 この機能は、以前のプレビューからありました。 

## <a name="management-features"></a>管理機能

+ 管理 REST API の [`api-version=2019-10-01-Preview`](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) を使用した[プライベート エンドポイントのサポート](service-create-private-endpoint.md)。 エンドポイントへのアクセス方法に制限があるサービスを作成できます。

## <a name="earlier-preview-features"></a>これまでのプレビュー機能

以前にプレビューで発表され、一般公開に切り替えられていない機能は、引き続きパブリック プレビューの段階です。 以前のプレビュー api-version で API を呼び出している場合は、引き続きそのバージョンを使用することも、想定される動作を変更することなく、`2019-05-06-Preview` に切り替えることもできます。

## <a name="how-to-call-a-preview-api"></a>プレビュー API を呼び出す方法

古いプレビューはまだ動作しますが、時間がたてば古くなります。 コードで `api-version=2016-09-01-Preview` または `api-version=2017-11-11-Preview` を呼び出している場合、それらの呼び出しはまだ有効です。 ただし、機能強化の更新が行われるのは、最新のプレビュー バージョンのみです。 

次のサンプル構文は、API のプレビュー バージョンの呼び出しを示しています。

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Azure Cognitive Search サービスは複数のバージョンで使用できます。 詳しくは、[API バージョン](search-api-versions.md)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ

Search REST API リファレンス ドキュメントを確認します。 問題が発生した場合は、[StackOverflow](https://stackoverflow.com/) または[サポートへの問い合わせ](https://azure.microsoft.com/support/community/?product=search)でサポートを依頼してください。

> [!div class="nextstepaction"]
> [Search Service REST API リファレンス](https://docs.microsoft.com/rest/api/searchservice/)