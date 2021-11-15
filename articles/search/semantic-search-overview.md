---
title: セマンティック検索
titleSuffix: Azure Cognitive Search
description: 検索結果をより直観的にするために Cognitive Search でどのように Bing のディープ ラーニングのセマンティック検索モデルが使用されているかについて学習します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/01/2021
ms.custom: references_regions
ms.openlocfilehash: 18ed79768f1a803c4565426de67f6cde35a0a1fe
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131577994"
---
# <a name="semantic-search-in-azure-cognitive-search"></a>Azure Cognitive Search でのセマンティック検索

> [!IMPORTANT]
> セマンティック検索はパブリック プレビュー段階にあり、[追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)の下で提供されます。 Azure portal、プレビュー REST API、ベータ版 SDK から利用できます。 これらの機能は課金対象です。 詳細については、「[可用性と料金](semantic-search-overview.md#availability-and-pricing)」を参照してください。

セマンティック検索は、検索結果にセマンティックの関連性と言語理解を取り込むクエリ関連機能のコレクションです。 この記事はセマンティック検索全体の概要紹介になっています。各特徴とそれらが集まってどのように機能するかについて説明します。 埋め込まれている動画では、このテクノロジについて説明しています。終わりのセクションでは販売状況と価格について説明しています。

セマンティック検索はプレミアム機能です。 最初にこの記事を読んで基礎知識を得ることをお勧めしますが、それはかまわないのですぐに使いたいという方は、これらの手順を実行してください。

> [!div class="checklist"]
> * [リージョンおよびサービス レベルに関する要件を確認します](#availability-and-pricing)。
> * [プレビュー プログラムに登録します](https://aka.ms/SemanticSearchPreviewSignup)。 要求の処理には、最大で 2 営業日かかります。
> * 受け入れが済み次第、[セマンティック キャプションとハイライトを返す](semantic-how-to-query-request.md)ようにクエリを作成または修正します。
> * クエリにプロパティをいくつか追加して、[セマンティック回答](semantic-answers.md)も返すようにします。
> * 必要に応じて、[スペル チェック](speller-how-to-add.md) を呼び出し、精度とリコールを最大化します。

## <a name="what-is-semantic-search"></a>セマンティック検索とは

セマンティック検索は、検索結果の品質を向上させる機能のコレクションです。 検索サービスで有効にすると、クエリの実行パイプラインに 2 つの機能が追加されます。 1 つ目として、最初の結果セットに対する二次ランク付けが追加され、セマンティック的に最も関連性の高い結果が一覧の先頭にレベル上げされます。 2 つ目として、キャプションと回答が抽出されて応答で返されます。これを検索ページにレンダリングして、ユーザーの検索エクスペリエンスを向上させることができます。

| 機能 | 説明 |
|---------|-------------|
| [セマンティックの再ランク付け](semantic-ranking.md) | コンテキストまたはセマンティックの意味を利用して、既存の結果に対して新しい関連スコアを計算します。 |
| [セマンティック キャプションとハイライト](semantic-how-to-query-request.md) | コンテンツを最もよく要約している文やフレーズをドキュメントから抽出し、スキャンを簡単にするために重要な部分を強調表示します。 結果を要約するキャプションは、個々のコンテンツ フィールドが結果ページに対して高密度である場合に便利です。 強調表示されたテキストにより、最も関連性の高い用語とフレーズが目立つため、ユーザーはその一致が関連していると見なされた理由を迅速に判断できます。 |
| [セマンティック回答](semantic-answers.md) | セマンティック クエリから返される省略可能な追加のサブ構造体。 これにより、質問のようなクエリに直接回答することができます。 これには、回答の特性を持つテキストがドキュメントに含まれている必要があります。 |
| [スペル チェック](speller-how-to-add.md) | クエリ用語が検索エンジンに到達する前に入力ミスを修正します。 |

## <a name="how-semantic-ranking-works"></a>セマンティック ランク付けのしくみ

"*セマンティックの順位付け*" では、用語のコンテキストと関連性が検索され、クエリにとってより意味のある一致が昇格されます。 言語理解では、コンテンツを解析して概要、または "*キャプション*" と "*回答*" を用意し、それを応答に含めます。これを検索結果ページに反映させれば検索の生産性を高めることができます。

概要作成と順位付けには、最先端の事前トレーニング済みモデルが使用されます。 ユーザーが検索に期待する高速なパフォーマンスを維持するために、セマンティック ベースの概要作成とランク付けは、 [既定の類似性スコアリング アルゴリズム](index-similarity-and-scoring.md#similarity-ranking-algorithms)のスコアに従って上位 50 件の結果にのみ適用されます。 セマンティック ランク付けでは、これらの結果をドキュメントのコーパスとして使用して、一致のセマンティック強度に基づいて結果を再スコアリングします。

テクノロジは Bing と Microsoft Research が基になっており、Cognitive Search インフラストラクチャにアドオン機能として統合されます。 研究と AI 投資でバックアップされるセマンティック検索に関する詳細については、「[Bing の AI が Azure Cognitive Search にパワーを与えるしくみ (Microsoft Research ブログ)](https://www.microsoft.com/research/blog/the-science-behind-semantic-search-how-ai-from-bing-is-powering-azure-cognitive-search/)」を参照してください。

次の動画では、機能の概要について説明しています。

> [!VIDEO https://www.youtube.com/embed/yOf0WfVd_V0]

### <a name="order-of-operations"></a>演算の順序

セマンティック検索のコンポーネントでは、既存のクエリ実行パイプラインが双方向に拡張されます。 スペル修正を有効にすると、用語が検索エンジンに到達する前に、[スペル チェック](speller-how-to-add.md)によってクエリ開始時の入力ミスが修正されます。

:::image type="content" source="media/semantic-search-overview/semantic-workflow.png" alt-text="クエリ実行のセマンティック コンポーネント" border="true":::

クエリ実行は通常どおりに進行し、逆インデックスに関する用語の解析、分析、スキャンが行われます。 エンジンでは、トークン照合を使用してドキュメントを取得し、 [既定の類似性スコアリング アルゴリズム](index-similarity-and-scoring.md#similarity-ranking-algorithms)を使用して結果をスコア付けします。 スコアは、クエリ用語とインデックス内の一致する用語との言語的類似性の度合いに基づいて計算されます。 定義されている場合、この段階で、スコアリング プロファイルも適用されます。 その後、結果はセマンティック検索サブシステムに渡されます。

準備ステップでは、最初の結果セットから返されたドキュメント コーパスが文と段落のレベルで分析され、各ドキュメントを要約する節が検索されます。 キーワード検索とは対照的に、このステップでは機械読解を使用して内容を評価します。 コンテンツ処理のこの段階全体で、セマンティック クエリによって[キャプション](semantic-how-to-query-request.md)と[回答](semantic-answers.md)が返されます。 それらを作成するために、セマンティック検索では言語表現を利用し、結果を最もよくまとめる重要な節が抽出され、強調表示されます。 検索クエリが質問であり、回答が要求されると、検索クエリで表された質問に最適な回答を与えるテキスト節も応答に含まれます。 

キャプションと回答の両方について、作成には既存のテキストが使用されます。 セマンティック モデルでは、使用可能な内容から新しい文やフレーズを作成したり、新しい結論に達するロジックを適用したりすることはありません。 つまり、存在していない内容が返されることはありません。

次に、クエリ用語の[概念的類似性](semantic-ranking.md) に基づいて結果が再スコアリングされます。

クエリでセマンティック機能を使用するには、[検索要求](semantic-how-to-query-request.md)を少し変更する必要がありますが、追加の構成やインデックスの再作成は不要です。

## <a name="semantic-capabilities-and-limitations"></a>セマンティック機能と制限

セマンティック検索は新しいテクノロジなので、実行できることとできないことについての期待値を設定することが重要です。 できるのは、検索の品質を向上させることです。そのために次のことが行われます。

* セマンティック的に元のクエリの意図に近い一致がレベル上げされます。

* それぞれの結果の中から、キャプションや場合によっては回答として使用でき、検索結果ページに表示できる文字列が検出されます。

できないのは、コーパス全体に対してクエリを再実行して、セマンティックな関連がある結果を検出することです。 セマンティック検索では、既定のランク付けアルゴリズムによってスコア付けされた上位 50 の結果で構成される "*既存の*" 結果セットが再ランク付けされます。 さらに、セマンティック検索で新しい情報や文字列を作成することはできません。 キャプションと回答は、コンテンツから逐語的に抽出されるので、結果に回答のようなテキストが含まれていない場合、その言語モデルではキャプションや回答は生成されません。

セマンティック検索はすべてのシナリオで有益なわけではありませんが、特定のコンテンツではその機能から多くのメリットが得られます。 セマンティック検索の言語モデルは、情報が豊富で、散文として構造化された検索可能なコンテンツに最適です。 ナレッジ ベース、オンライン ドキュメント、説明的なコンテンツを含むドキュメントでは、セマンティック検索機能から最も多くのメリットが得られます。

## <a name="availability-and-pricing"></a>可用性と料金

セマンティック検索は[サインアップ登録](https://aka.ms/SemanticSearchPreviewSignup)をすることで使用できます。 一度サインアップすれば、セマンティック検索とスペル チェックの両方を使用できます。

| 機能 | レベル | リージョン | サインアップ | 価格 |
|---------|------|--------|---------------------|-------------------|
| セマンティック検索 (ランク、キャプション、ハイライト、回答) | スタンダード レベル (S1、S2、S3) | 米国中北部、米国西部、米国西部 2、米国東部 2、北ヨーロッパ、西ヨーロッパ | 必須 | [Cognitive Search の価格ページ](https://azure.microsoft.com/pricing/details/search/)  |
| スペル チェック | Basic<sup>1</sup> 以上  | All | なし | なし (無料) |

<sup>1</sup> 共有 (無料) 検索サービスのプロビジョニングの仕組みや有効期間により、少数のサービスで無料のスペルチェックが発生します。 ただし、無料のサービスでスペル チェックを使用できるかどうかは保証および予想されるわけではありません。

セマンティック検索の料金は、"queryType=semantic" を含む、検索文字列が空でないクエリ要求 (たとえば "search=pet friendly hotels in new york") を使用した場合に発生します。 検索文字が空の場合 ("search=*")、queryType が "semantic" に設定されていても課金されません。

## <a name="disable-semantic-search"></a>セマンティック検索を無効にする

偶発的な使用と課金から完全に守るため、検索サービスでは Create または Update Service API を使用して[セマンティック検索を無効](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#searchsemanticsearch)にできます。 この機能を無効にすると、セマンティック クエリ タイプが含まれるあらゆる要求が拒否されます。

* Management REST API バージョン 2021-04-01-Preview でこのオプションが提供されています

* 機能を無効にするには、所有者か共同作成者のアクセス許可が必要です

```http
PUT https://management.azure.com/subscriptions/{{subscriptionId}}/resourcegroups/{{resource-group}}/providers/Microsoft.Search/searchServices/{{search-service-name}}?api-version=2021-04-01-Preview
    {
      "location": "{{region}}",
      "sku": {
        "name": "standard"
      },
      "properties": {
        "semanticSearch": "disabled"
      }
    }
```

セマンティック検索を再び有効にするには、上記の要求を再実行し、"semanticSearch" を "free" (既定値) か "standard" に設定します。

> [!TIP]
> Management REST API 呼び出しは、Azure Active Directory を介して認証されます。 セキュリティの原則と要求の設定に関するガイダンスについては、ブログ記事「[Postman を使用した Azure REST API (2021)](https://blog.jongallant.com/2021/02/azure-rest-apis-postman-2021/)」を参照してください。 前の例は、このブログ記事に記載されている手順と Postman コレクションを使用してテストされています。

## <a name="next-steps"></a>次のステップ

[新規登録](https://aka.ms/SemanticSearchPreviewSignup)し、前のセクションで注記したサービス レベルとリージョンの要件を満たす検索サービスのプレビュー版を申し込みます。

要求の処理には、最大で 2 営業日かかります。 サービスの準備ができたら、[セマンティック クエリを作成](semantic-how-to-query-request.md)して、実際のコンテンツでパフォーマンスを評価します。
