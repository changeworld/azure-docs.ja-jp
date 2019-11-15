---
title: REST API バージョン 2019-05-06-Preview
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search サービス REST API バージョン 2019-05-06-Preview には、ナレッジ ストアや顧客管理の暗号化キーなどの試験的機能が含まれています。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 24e16942410c72640628bd4120d05a85e68de993
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720031"
---
# <a name="azure-cognitive-search-service-rest-api-version-2019-05-06-preview"></a>Azure Cognitive Search サービス REST API バージョン 2019-05-06-Preview

この記事では、まだ一般公開されていない実験的機能を提供する Search サービス REST API の `api-version=2019-05-06-Preview` バージョンについて説明します。

> [!NOTE]
> プレビュー機能は、フィードバックの収集を目的とするテストと実験のために提供されており、変更される可能性があります。 運用アプリケーションではプレビュー API を使用しないことを強くお勧めします。


## <a name="new-in-2019-05-06-preview"></a>2019-05-06-Preview の新機能

+ [増分インデックスの作成](cognitive-search-incremental-indexing-conceptual.md)は、スキルセットに状態とキャッシュを追加するインデックス作成用の新しいモードであり、ソース データ、インデクサー、スキルセットの定義が変更されていない場合に、既存の出力を再利用できます。 この機能は、コグニティブ スキルセットを通じて定義されたエンリッチメントにのみ適用されます。

+ [Cosmos DB インデクサー](search-howto-index-cosmosdb.md)は、MongoDB API、Gremlin API、Cassandra API をサポートしています。

+ [Azure Data Lake Storage Gen2 インデクサー](search-howto-index-azure-data-lake-storage.md)では、Data Lake Storage Gen2 のコンテンツとメタデータのインデックスを作成できます。

+ [ドキュメント抽出 (プレビュー)](cognitive-search-skill-document-extraction.md) は、インデックスの作成中に使用されるコグニティブ スキルであり、スキルセット内からファイルのコンテンツを抽出できます。 ドキュメント解析は、これまでスキルセットの実行前にのみ行われていました。 このスキルを追加することで、スキルセットの実行内でこの操作を実行することもできます。

+ [テキスト翻訳 (プレビュー)](cognitive-search-skill-text-translation.md) は、インデックスの作成中に使用されるコグニティブ スキルであり、テキストを評価し、各レコードについて、指定したターゲット言語に翻訳されたテキストを返します。

+ [ナレッジ ストア](knowledge-store-concept-intro.md)は、AI ベースのエンリッチメント パイプラインの新しい宛先です。 その物理データ構造は Azure Blob Storage と Azure Table Storage に存在し、コグニティブ スキルセットがアタッチされているインデクサーを実行した時点で作成され、データが挿入されます。 ナレッジ ストア自体の定義は、スキルセットの定義の中で指定されます。 ナレッジ ストアの定義では、"*プロジェクション*" 要素によってデータの物理構造 (データの形状をどのようにするか、データの保存先をテーブル ストレージと BLOB ストレージのどちらにするか、ビューが複数存在するかどうか) を制御できます。

+ サービス側で保存時の暗号化に使用する[顧客が管理する暗号化キー](search-security-manage-encryption-keys.md)も新しいプレビュー機能です。 Microsoft が管理する組み込みの暗号化の保存だけではなく、自分だけが鍵を所有している暗号化のレイヤーを追加で適用できます。

## <a name="earlier-preview-features"></a>これまでのプレビュー機能

以前のプレビューで発表された機能は、パブリック プレビューにもあります。 以前のプレビュー api-version で API を呼び出している場合は、引き続きそのバージョンを使用することも、想定される動作を変更することなく、`2019-05-06-Preview` に切り替えることもできます。

+ [moreLikeThis クエリ パラメーター](search-more-like-this.md)は、特定のドキュメントに関連するドキュメントを検索します。 この機能は、以前のプレビューからありました。 

+ [CSV BLOB のインデックス作成](search-howto-index-csv-blobs.md)では、テキスト BLOB ごとに 1 つのドキュメントではなく、行ごとに 1 つのドキュメントを作成します。

## <a name="how-to-call-a-preview-api"></a>プレビュー API を呼び出す方法

古いプレビューはまだ動作しますが、時間がたてば古くなります。 コードで `api-version=2016-09-01-Preview` または `api-version=2017-11-11-Preview` を呼び出している場合、それらの呼び出しはまだ有効です。 ただし、機能強化の更新が行われるのは、最新のプレビュー バージョンのみです。 

次のサンプル構文は、API のプレビュー バージョンの呼び出しを示しています。

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Azure Cognitive Search サービスは複数のバージョンで使用できます。 詳しくは、[API バージョン](search-api-versions.md)に関するページをご覧ください。

## <a name="next-steps"></a>次の手順

Search REST API リファレンス ドキュメントを確認します。 問題が発生した場合は、[StackOverflow](https://stackoverflow.com/) または[サポートへの問い合わせ](https://azure.microsoft.com/support/community/?product=search)でサポートを依頼してください。

> [!div class="nextstepaction"]
> [Search Service REST API リファレンス](https://docs.microsoft.com/rest/api/searchservice/)