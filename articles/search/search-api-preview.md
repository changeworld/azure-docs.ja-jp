---
title: REST API バージョン 2019-05-06-Preview
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search サービス REST API バージョン 2019-05-06-Preview には、ナレッジ ストアやインクリメンタル エンリッチメント用のインデクサー キャッシュなどの試験的機能が含まれています。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/04/2020
ms.openlocfilehash: eb73d614ca94bc1fa007a14f3705e50c74ab9e4f
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/13/2020
ms.locfileid: "75922468"
---
# <a name="azure-cognitive-search-service-rest-api-version-2019-05-06-preview"></a>Azure Cognitive Search サービス REST API バージョン 2019-05-06-Preview

この記事では、まだ一般公開されていない実験的機能を提供する Search サービス REST API の `api-version=2019-05-06-Preview` バージョンについて説明します。

> [!NOTE]
> プレビュー機能は、フィードバックの収集を目的とするテストと実験のために提供されており、変更される可能性があります。 運用アプリケーションではプレビュー API を使用しないことを強くお勧めします。


## <a name="new-in-2019-05-06-preview"></a>2019-05-06-Preview の新機能

+ [インクリメンタル エンリッチメント (プレビュー)](cognitive-search-incremental-indexing-conceptual.md) では、エンリッチメント パイプラインにキャッシュが追加されます。これにより、スキルセットや別のオブジェクトに対する更新など、対象を絞った変更によってコンテンツが変更されない場合に、既存の出力を再利用できるようになります。 キャッシュは、スキルセットによって生成されるエンリッチされたドキュメントにのみ適用されます。

+ [Cosmos DB インデクサー](search-howto-index-cosmosdb.md)では、MongoDB API (プレビュー)、Gremlin API (プレビュー)、Cassandra API (プレビュー) がサポートされています。

+ [Azure Data Lake Storage Gen2 インデクサー (プレビュー)](search-howto-index-azure-data-lake-storage.md) では、Data Lake Storage Gen2 のコンテンツとメタデータにインデックスを付けることができます。

+ [ドキュメント抽出 (プレビュー)](cognitive-search-skill-document-extraction.md) は、インデックスの作成中に使用されるコグニティブ スキルであり、スキルセット内からファイルのコンテンツを抽出できます。 ドキュメント解析は、これまでスキルセットの実行前にのみ行われていました。 このスキルを追加することで、スキルセットの実行内でこの操作を実行することもできます。

+ [テキスト翻訳 (プレビュー)](cognitive-search-skill-text-translation.md) は、インデックスの作成中に使用されるコグニティブ スキルであり、テキストを評価し、各レコードについて、指定したターゲット言語に翻訳されたテキストを返します。

+ [ナレッジ ストア](knowledge-store-concept-intro.md)は、AI ベースのエンリッチメント パイプラインの新しい宛先です。 その物理データ構造は Azure Blob Storage と Azure Table Storage に存在し、コグニティブ スキルセットがアタッチされているインデクサーを実行した時点で作成され、データが挿入されます。 ナレッジ ストア自体の定義は、スキルセットの定義の中で指定されます。 ナレッジ ストアの定義では、"*プロジェクション*" 要素によってデータの物理構造 (データの形状をどのようにするか、データの保存先をテーブル ストレージと BLOB ストレージのどちらにするか、ビューが複数存在するかどうか) を制御できます。

## <a name="earlier-preview-features"></a>これまでのプレビュー機能

以前のプレビューで発表された機能は、パブリック プレビューにもあります。 以前のプレビュー api-version で API を呼び出している場合は、引き続きそのバージョンを使用することも、想定される動作を変更することなく、`2019-05-06-Preview` に切り替えることもできます。

+ [moreLikeThis クエリ パラメーター](search-more-like-this.md)は、特定のドキュメントに関連するドキュメントを検索します。 この機能は、以前のプレビューからありました。 

+ [CSV BLOB のインデックス作成](search-howto-index-csv-blobs.md)では、テキスト BLOB ごとに 1 つのドキュメントではなく、行ごとに 1 つのドキュメントを作成します。

## <a name="how-to-call-a-preview-api"></a>プレビュー API を呼び出す方法

古いプレビューはまだ動作しますが、時間がたてば古くなります。 コードで `api-version=2016-09-01-Preview` または `api-version=2017-11-11-Preview` を呼び出している場合、それらの呼び出しはまだ有効です。 ただし、機能強化の更新が行われるのは、最新のプレビュー バージョンのみです。 

次のサンプル構文は、API のプレビュー バージョンの呼び出しを示しています。

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Azure Cognitive Search サービスは複数のバージョンで使用できます。 詳しくは、[API バージョン](search-api-versions.md)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ

Search REST API リファレンス ドキュメントを確認します。 問題が発生した場合は、[StackOverflow](https://stackoverflow.com/) または[サポートへの問い合わせ](https://azure.microsoft.com/support/community/?product=search)でサポートを依頼してください。

> [!div class="nextstepaction"]
> [Search Service REST API リファレンス](https://docs.microsoft.com/rest/api/searchservice/)