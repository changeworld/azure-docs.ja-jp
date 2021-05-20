---
title: セマンティック検索
titleSuffix: Azure Cognitive Search
description: 検索結果をより直観的にするために Cognitive Search でどのように Bing のディープ ラーニングのセマンティック検索モデルが使用されているかについて学習します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/06/2021
ms.custom: references_regions
ms.openlocfilehash: 4e6b30e6b6df02bac366e2907f33fd2bda13716f
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2021
ms.locfileid: "109789805"
---
# <a name="semantic-search-in-azure-cognitive-search"></a>Azure Cognitive Search でのセマンティック検索

> [!IMPORTANT]
> セマンティック検索はパブリック プレビュー段階にあり、プレビュー REST API とポータルを介してのみ利用できます。 プレビュー機能は、[補足利用規約](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に基づいて、現状のまま提供されます。一般提供時に同じ実装があるとは限りません。 これらの機能は課金対象です。 詳細については、[可用性と価格](semantic-search-overview.md#availability-and-pricing)に関するページを参照してください。

セマンティック検索は、検索結果にセマンティックの関連性と Language Understanding を追加するクエリ関連の機能のコレクションです。 この記事はセマンティック検索全体の概要紹介になっています。各特徴とそれらが集まってどのように機能するかについて説明します。 埋め込まれている動画では、このテクノロジについて説明しています。終わりのセクションでは販売状況と価格について説明しています。

セマンティック検索はプレミアム機能です。 最初にこの記事を読んで基礎知識を得ることをお勧めしますが、それはかまわないのですぐに使いたいという方は、これらの手順を実行してください。

1. [リージョンおよびサービス レベルに関する要件を確認します](#availability-and-pricing)。
1. [プレビュー プログラムに登録します](https://aka.ms/SemanticSearchPreviewSignup)。
1. 受け入れが済み次第、[セマンティック キャプションおよびハイライト](semantic-how-to-query-request.md)を返すようにクエリを作成または修正します。
1. クエリにプロパティをいくつか追加して、[セマンティック回答](semantic-answers.md)も返すようにします。
1. 必要に応じて [スペル チェック](speller-how-to-add.md) プロパティを持たせ、精度とリコールを最大化します。

## <a name="what-is-semantic-search"></a>セマンティック検索とは

セマンティック検索はクエリ関連 AI の、オプションのレイヤー (機能) です。 検索サービスでこれを有効にすると、クエリの従来の実行パイプラインに 2 つの機能が追加されます。 まず、オプションであるセマンティック ランク付けモデルを追加します。次に、キャプションと回答を含む応答を返すようになります。

"*セマンティックの順位付け*" では、用語のコンテキストと関連性が検索され、クエリにとってより意味のある一致が昇格されます。 言語理解では、コンテンツを解析して概要、または "*キャプション*" と "*回答*" を用意し、それを応答に含めます。これを検索結果ページに反映させれば検索の生産性を高めることができます。

概要作成と順位付けには、最先端の事前トレーニング済みモデルが使用されます。 ユーザーが検索に期待する高速なパフォーマンスを維持するために、セマンティック ベースの概要作成とランク付けは、 [既定の類似性スコアリング アルゴリズム](index-similarity-and-scoring.md#similarity-ranking-algorithms)のスコアに従って上位 50 件の結果にのみ適用されます。 セマンティック ランク付けでは、これらの結果をドキュメントのコーパスとして使用して、一致のセマンティック強度に基づいて結果を再スコアリングします。

テクノロジは Bing と Microsoft Research が基になっており、Cognitive Search インフラストラクチャにアドオン機能として統合されます。 研究と AI 投資でバックアップされるセマンティック検索に関する詳細については、「[Bing の AI が Azure Cognitive Search にパワーを与えるしくみ (Microsoft Research ブログ)](https://www.microsoft.com/research/blog/the-science-behind-semantic-search-how-ai-from-bing-is-powering-azure-cognitive-search/)」を参照してください。

次の動画では、機能の概要について説明しています。

> [!VIDEO https://www.youtube.com/embed/yOf0WfVd_V0]

## <a name="feature-description-and-workflow"></a>機能の説明とワークフロー

セマンティック検索では、次の機能を追加することで精度と再現率が向上します。

| 機能 | 説明 |
|---------|-------------|
| [スペル チェック](speller-how-to-add.md) | クエリ用語が検索エンジンに到達する前に入力ミスを修正します。 |
| [セマンティック ランク付け](semantic-ranking.md) | コンテキストまたはセマンティックの意味を利用して新しい関連スコアを計算します。 |
| [セマンティック キャプションとハイライト](semantic-how-to-query-request.md) | 内容を最もよくまとめる文とフレーズをドキュメントから抽出し、スキャンを簡単にするために重要な節を強調表示します。 結果を要約するキャプションは、個々のコンテンツ フィールドが結果ページに対して高密度である場合に便利です。 強調表示されたテキストにより、最も関連性の高い用語とフレーズが目立つため、ユーザーはその一致が関連していると見なされた理由を迅速に判断できます。 |
| [セマンティック回答](semantic-answers.md) | セマンティック クエリから返される省略可能な追加のサブ構造体。 これにより、質問のようなクエリに直接回答することができます。 |

### <a name="order-of-operations"></a>演算の順序

セマンティック検索のコンポーネントでは、既存のクエリ実行パイプラインが双方向に拡張されます。 スペル修正を有効にすると、用語が検索エンジンに到達する前に、[スペル チェック](speller-how-to-add.md)によってクエリ開始時の入力ミスが修正されます。

:::image type="content" source="media/semantic-search-overview/semantic-workflow.png" alt-text="クエリ実行のセマンティック コンポーネント" border="true":::

クエリ実行は通常どおりに進行し、逆インデックスに関する用語の解析、分析、スキャンが行われます。 エンジンでは、トークン照合を使用してドキュメントを取得し、 [既定の類似性スコアリング アルゴリズム](index-similarity-and-scoring.md#similarity-ranking-algorithms)を使用して結果をスコア付けします。 スコアは、クエリ用語とインデックス内の一致する用語との言語的類似性の度合いに基づいて計算されます。 定義されている場合、この段階で、スコアリング プロファイルも適用されます。 その後、結果はセマンティック検索サブシステムに渡されます。

準備ステップでは、最初の結果セットから返されたドキュメント コーパスが文と段落のレベルで分析され、各ドキュメントを要約する節が検索されます。 キーワード検索とは対照的に、このステップでは機械読解を使用して内容を評価します。 コンテンツ処理のこの段階全体で、セマンティック クエリによって[キャプション](semantic-how-to-query-request.md)と[回答](semantic-answers.md)が返されます。 それらを作成するために、セマンティック検索では言語表現を利用し、結果を最もよくまとめる重要な節が抽出され、強調表示されます。 検索クエリが質問であり、回答が要求されると、検索クエリで表された質問に最適な回答を与えるテキスト節も応答に含まれます。 

キャプションと回答の両方について、作成には既存のテキストが使用されます。 セマンティック モデルでは、使用可能な内容から新しい文やフレーズを作成したり、新しい結論に達するロジックを適用したりすることはありません。 つまり、存在していない内容が返されることはありません。

次に、クエリ用語の[概念的類似性](semantic-ranking.md) に基づいて結果が再スコアリングされます。

クエリでセマンティック機能を使用するには、[検索要求](semantic-how-to-query-request.md)を少し変更する必要がありますが、追加の構成やインデックスの再作成は不要です。

## <a name="availability-and-pricing"></a>可用性と料金

セマンティック検索は[サインアップ登録](https://aka.ms/SemanticSearchPreviewSignup)をすることで使用できます。 3 月 2 日のプレビュー公開から 6 月前半までは、セマンティック検索を無料で利用できます。

| 機能 | レベル | リージョン | サインアップ | 計画価格 |
|---------|------|--------|---------------------|-------------------|
| セマンティック検索 (キャプション、ハイライト、回答) | スタンダード レベル (S1、S2、S3) | 米国中北部、米国西部、米国西部 2、米国東部 2、北ヨーロッパ、西ヨーロッパ | 必須 | 6 月 1 日から、想定価格は 250,000 クエリまで $500/月。これを超えると 1,000 クエリごとに $2。  |
| スペル チェック | Any | 米国中北部、米国西部、米国西部 2、米国東部 2、北ヨーロッパ、西ヨーロッパ | 必須 | なし (無料) |

一度[サインアップ登録](https://aka.ms/SemanticSearchPreviewSignup)すれば、セマンティック関連機能とスペル チェックの両方を使用できます。 

セマンティック検索を利用せずにスペル チェックだけを使用する場合は無料です。 セマンティック検索の料金は、`queryType=semantic` を含む、検索文字列が空でないクエリ要求 (たとえば `search=pet friendly hotels in new york`) を使用した場合に発生します。 空の検索 (`search=*` であるクエリ) は課金されません。

最終的な価格の情報は、[Cognitive Search の料金](https://azure.microsoft.com/pricing/details/search/)に関するページと、[コストの見積もりと管理](search-sku-manage-costs.md)に関する記事に記載する予定です。

## <a name="next-steps"></a>次のステップ

[新規登録](https://aka.ms/SemanticSearchPreviewSignup)し、前のセクションで注記したサービス レベルとリージョンの要件を満たす検索サービスのプレビュー版を申し込みます。

サービスの準備が完了したら、[セマンティック クエリを作成](semantic-how-to-query-request.md)し、実際のセマンティック ランク付けの様子を確認します。