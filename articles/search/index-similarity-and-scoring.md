---
title: 類似性とスコアリングの概要
titleSuffix: Azure Cognitive Search
description: 類似性とスコアリングの概念について、さらにスコアリングの結果をカスタマイズするために開発者ができることについて説明します。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 9f9cc4c29b117c83595a36c4e28b1edb428c3cde
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82254064"
---
# <a name="similarity-and-scoring-in-azure-cognitive-search"></a>Azure Cognitive Search での類似性とスコアリング

スコアリングとは、フル テキスト検索クエリの検索結果に返されるすべての項目の検索スコアを計算することです。 スコアは、現在の検索操作のコンテキストにおける項目の関連性を示すインジケーターです。 スコアが高いほど、項目の関連性が高くなります。 検索結果内の項目は、項目ごとに計算された検索スコアに基づいて、順位の高い順に並べられます。 

既定では、上位 50 個が応答で返されます。しかし、 **$top** パラメーターを使用すれば、返される項目数を減らしたり増やしたりすることができ (1 回の応答で 1000 個まで)、 **$skip** を使用すれば、次の結果セットを取得することができます。

検索スコアは、データとクエリの統計プロパティに基づいて計算されます。 Azure Cognitive Search では、検索語句 ([searchMode](https://docs.microsoft.com/rest/api/searchservice/search-documents#searchmodeany--all-optional) に応じて一部またはすべて) に一致するドキュメントが検索され、検索語句のインスタンスを多く含むドキュメントが優先されます。 データ インデックス全体での語句の出現頻度は低いがドキュメント内ではよく使用されている場合、検索スコアはより高くなります。 関連性を計算するこのアプローチの基礎となる手法は、*TF-IDF* (単語の出現頻度 - 逆文書頻度) と呼ばれています。

検索スコアの値は、結果セット全体で繰り返すことができます。 同じ検索スコアを持つ項目が複数ヒットした場合、同じスコアを持つ項目の順序付けは定義されていないので安定しません。 クエリを再度実行すると、特に、複数のレプリカで無料のサービスまたは課金対象サービスを使用している場合は、項目の位置が変わる場合があります。 同一スコアの項目が 2 つ存在する場合、最初に表示される項目を特定することはできません。

繰り返しスコアの間の関係を解除したい場合は、 **$orderby** 句を追加することで、まずスコアで並べ替えを行い、次に別の並べ替え可能なフィールド (`$orderby=search.score() desc,Rating desc` など) で並べ替えを行うことができます。 詳細については、[$orderby](https://docs.microsoft.com/azure/search/search-query-odata-orderby) に関するページを参照してください。

> [!NOTE]
> `@search.score = 1.00` は、スコア付けまたは順位付けが行われていない結果セットを示します。 スコアは、すべての結果にわたって均一です。 スコア付けされていない結果が生じるのは、クエリ フォームがあいまい検索、ワイルドカードまたは正規表現のクエリ、または **$filter** 式である場合です。 

## <a name="scoring-profiles"></a>スコアリング プロファイル

さまざまなフィールドの順位付け方法をカスタマイズするには、カスタム "*スコアリング プロファイル*" を定義します。 スコアリング プロファイルでは、検索結果の項目の順位付けをより細かく制御できます。 たとえば、収益の可能性に基づいて項目をブーストしたり、より新しい項目を昇格させたり、場合によっては在庫期間が長すぎる項目をブーストしたりできます。 

スコアリング プロファイルは、インデックス定義の一部であり、重み付けされたフィールド、関数、およびパラメーターで構成されます。 定義方法の詳細については、「[スコアリング プロファイル](index-add-scoring-profiles.md)」を参照してください。

## <a name="scoring-statistics"></a>スコア付けの統計

スケーラビリティのために、Azure Cognitive Search ではシャーディング プロセスを介して各インデックスが水平方向に分散されます。つまり、インデックスの各部が物理的に分離されます。

既定では、ドキュメントのスコアは、"*シャード内*" のデータの統計プロパティに基づいて計算されます。 このアプローチは、一般に、データの大規模なコーパスでは問題にならず、すべてのシャードの情報に基づいてスコアを計算する必要がある場合よりもパフォーマンスが向上します。 ただし、このパフォーマンスの最適化を使用すると、2 つの非常に類似したドキュメント (またはまったく同一のドキュメント) は、それぞれが異なるシャードになる場合、関連性スコアが異なる可能性があります。

すべてのシャードの統計プロパティに基づいてスコアを計算する場合、これを行うには、*scoringStatistics=global* を[クエリ パラメーター](https://docs.microsoft.com/rest/api/searchservice/search-documents)として追加します (または[クエリ要求](https://docs.microsoft.com/rest/api/searchservice/search-documents)の本文パラメーターとして *"scoringStatistics": "global"* を追加します)。

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs?scoringStatistics=global
  Content-Type: application/json
  api-key: [admin key]  
```

> [!NOTE]
> `scoringStatistics` パラメーターには、管理者 API キーが必須です。

## <a name="similarity-ranking-algorithms"></a>類似性ランク付けアルゴリズム

Azure Cognitive Search では、次の 2 種類の類似性ランク付けアルゴリズムがサポートされています: *ClassicSimilarity* アルゴリズムと、*Okapi BM25* アルゴリズム (現在プレビュー段階) の公式実装。 従来の類似性アルゴリズムは既定のアルゴリズムですが、7 月 15 日からは、その日以降に作成された新しいサービスでは新しい BM25 アルゴリズムが使用されます。 これは、新しいサービスで利用できる唯一のアルゴリズムです。

今のところ、使用したい類似性ランク付けアルゴリズムを指定できます。 詳細については、[ランク付けアルゴリズム](index-ranking-similarity.md)に関するページを参照してください。

## <a name="watch-this-video"></a>次の動画をご覧ください

この 16 分間のビデオでは、ソフトウェア エンジニアの Raouf Merouche が、インデックスの作成のプロセス、クエリの実行、スコアリング プロファイルの作成方法について説明しています。 ドキュメントへのインデックス付け、およびドキュメントの取得の際に、内部で何が行われているのかをわかりやすく説明します。

>[!VIDEO https://channel9.msdn.com/Shows/AI-Show/Similarity-and-Scoring-in-Azure-Cognitive-Search/player]

+ 2 - 3 分ではインデックス付けについて説明: テキスト処理と字句解析。
+ 3 - 4 分ではインデックス付けについて説明: 逆インデックス。
+ 4 - 6 分ではクエリの実行について説明: 取得と順位付け。
+ 7 - 16 分ではスコアリング プロファイルについて説明。

## <a name="see-also"></a>関連項目

 [スコアリング プロファイル](index-add-scoring-profiles.md) [REST API リファレンス](https://docs.microsoft.com/rest/api/searchservice/)   
 [ドキュメント API の検索](https://docs.microsoft.com/rest/api/searchservice/search-documents)   
 [Azure Cognitive Search .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
