---
title: セマンティック検索
titleSuffix: Azure Cognitive Search
description: 検索結果をより直観的にするために Cognitive Search でどのように Bing のディープ ラーニングのセマンティック検索モデルが使用されているかについて学習します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/01/2021
ms.custom: references_regions
ms.openlocfilehash: 04300b8d148bb22bf585aa81481c475b347ad462
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222046"
---
# <a name="semantic-search-in-azure-cognitive-search"></a>Azure Cognitive Search でのセマンティック検索

> [!IMPORTANT]
> セマンティック検索はパブリック プレビュー段階にあり、プレビュー REST API とポータルを介してのみ利用できます。 プレビュー機能は、[補足利用規約](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に基づいて、現状のまま提供されます。一般提供時に同じ実装があるとは限りません。 これらの機能は課金対象です。 詳細については、[可用性と価格](semantic-search-overview.md#availability-and-pricing)に関するページを参照してください。

セマンティック検索は、検索結果にセマンティックの関連性と Language Understanding を追加するクエリ関連の機能のコレクションです。 "*セマンティックの順位付け*" では、用語のコンテキストと関連性が検索され、クエリにとってより意味のある一致が昇格されます。 Language Understanding は、コンテンツ内の "*キャプション*" と "*回答*" が検索され、一致するドキュメントを要約したり質問に回答したりすることができます。これは、より生産性の高い検索エクスペリエンスになるように、検索結果ページに表示できます。

概要作成と順位付けには、最先端の事前トレーニング済みモデルが使用されます。 ユーザーが検索に期待する高速なパフォーマンスを維持するために、セマンティック ベースの概要作成とランク付けは、 [既定の類似性スコアリング アルゴリズム](index-similarity-and-scoring.md#similarity-ranking-algorithms)のスコアに従って上位 50 件の結果にのみ適用されます。 セマンティック ランク付けでは、これらの結果をドキュメントのコーパスとして使用して、一致のセマンティック強度に基づいて結果を再スコアリングします。

テクノロジは Bing と Microsoft Research が基になっており、Cognitive Search インフラストラクチャにアドオン機能として統合されます。 研究と AI 投資でバックアップされるセマンティック検索に関する詳細については、「[Bing の AI が Azure Cognitive Search にパワーを与えるしくみ (Microsoft Research ブログ)](https://www.microsoft.com/research/blog/the-science-behind-semantic-search-how-ai-from-bing-is-powering-azure-cognitive-search/)」を参照してください。

次の動画では、機能の概要について説明しています。

> [!VIDEO https://www.youtube.com/embed/yOf0WfVd_V0]

## <a name="components-and-workflow"></a>コンポーネントとワークフロー

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

セマンティック機能は、米国中北部、米国西部、米国西部 2、米国東部 2、北ヨーロッパ、西ヨーロッパのいずれかのリージョンにある、Standard レベル (S1、S2、S3) で作成された検索サービスの[サインアップ登録](https://aka.ms/SemanticSearchPreviewSignup)を通じて利用できます。 

スペル修正は同じリージョンで利用できますが、レベルの制限はありません。 レベルとリージョンの条件を満たす既存のサービスがある場合、必要なのはサインアップのみです。

3 月 2 日のプレビューの開始から 4 月下旬までは、スペル修正とセマンティック ランク付けが無料で提供されます。 4 月下旬には、この機能を実行するための計算コストが課金対象のイベントになります。 予想されるコストは、250,000 件のクエリで月額約 500 米国ドルです。 詳細なコスト情報については、[「Cognitive Search の価格」ページ](https://azure.microsoft.com/pricing/details/search/)および[コストの見積もりと管理](search-sku-manage-costs.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

新しいクエリ型により、セマンティック検索の関連性ランク付けと応答構造が可能になります。

[セマンティック クエリを作成](semantic-how-to-query-request.md)して開始します。 または、関連する情報について次の記事を参照してください。

+ [クエリ用語にスペル チェックを追加する](speller-how-to-add.md)
+ [セマンティック回答を返す](semantic-answers.md)
+ [セマンティック ランク付け](semantic-ranking.md)
+ [セマンティック検索の概要 (ブログの投稿)](https://techcommunity.microsoft.com/t5/azure-ai/introducing-semantic-search-bringing-more-meaningful-results-to/ba-p/2175636)
+ [セマンティック機能を使用して有意義な洞察を見つける (AI ショー ビデオ)](https://channel9.msdn.com/Shows/AI-Show/Find-meaningful-insights-using-semantic-capabilities-in-Azure-Cognitive-Search)