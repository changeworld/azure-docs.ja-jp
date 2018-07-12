---
title: Azure の LUIS アプリでの発話 | Microsoft Docs
description: Language Understanding Intelligent Service (LUIS) アプリで発話を追加します。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/13/2018
ms.author: v-geberr
ms.openlocfilehash: 66a23876eebe177c767b20f60f86891c35da3385
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/21/2018
ms.locfileid: "36301864"
---
# <a name="utterances-in-luis"></a>LUIS での発話

**発話**は、アプリが解釈する必要のあるユーザーからの入力です。 発話から意図とエンティティを抽出するように LUIS をトレーニングするには、各意図に対してさまざまな異なる入力をキャプチャすることが重要です。 新しい発話についてのアクティブな学習、または継続的なトレーニング プロセスは、LUIS が提供する機械学習されたインテリジェンスに不可欠です。

ユーザーが入力すると思われる語句を収集します。 同じことを意味しますが、異なる単語の長さと単語の配置で構成されている発話を含めるようにします。 

## <a name="how-to-choose-varied-utterances"></a>さまざまな発話を選択する方法
LUIS モデルへの[発話例の追加][add-example-utterances]を初めて始めるときに留意すべきいくつかの原則があります。

### <a name="utterances-arent-always-well-formed"></a>発話は常に正しい形式になっているわけではない
"パリ行きのチケットを予約して" のような文の場合も、"予約" や "パリ便" のような文の一部の場合もあります。  ユーザーはスペルミスをすることがよくあります。 アプリを計画するときは、LUIS に渡す前にユーザー入力のスペルチェックを行うかどうかを検討します。 [Bing Spell Check API][BingSpellCheck] は LUIS と統合します。 発行するときに、Bing Spell Check API の外部キーと LUIS アプリを関連付けることができます。 ユーザーの発話のスペルチェックを行わない場合は、タイプミスやミススペルを含む発話で LUIS をトレーニングする必要があります。

### <a name="use-the-representative-language-of-the-user"></a>ユーザーの代表的な言語を使用する
発話を選択するときは、開発者が一般的な用語または語句と考えるものがクライアント アプリケーションの一般的なユーザーにとってはそうではない可能性があることに注意してください。 ユーザーはドメインのエクスペリエンスを持たない可能性があります。 そのため、専門家だけが話すような用語や語句を使用するときは注意が必要です。

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>さまざまな用語と表現を選択する
さまざまなセンテンス パターンを作成しようとしても、いくつかのボキャブラリを繰り返していることに気付きます。

次の発話の例を使用します。
```
how do I get a computer?
Where do I get a computer?
I want to get a computer, how do I go about it?
When can I have a computer? 
```
ここでのコア用語 "コンピューター" は全部同じです。 デスクトップ コンピューター、ラップトップ、ワークステーション、またはマシンと言うこともできます。 LUIS はコンテキストからシノニムをインテリジェントに推論しますが、それでもトレーニング用の発話を作成するときは、変更することをお勧めします。

## <a name="example-utterances-in-each-intent"></a>各意図での発話の例
意図ごとに少なくとも 10 から 15 個の発話の例が必要です。 発話の例がない意図がある場合、LUIS をトレーニングできません。 意図の発話の例が 1 つまたは非常に少ない場合、LUIS は意図を正確に予測できません。 

## <a name="add-small-groups-of-10-15-utterances-for-each-authoring-iteration"></a>作成イテレーションごとに 10 から 15 個の発話からなる小さいグループを追加します。
モデルの各イテレーションでは、大量の発話を追加しないようにします。 数十個の発話を追加します。 もう一度[トレーニング](luis-how-to-train.md)し、[発行](publishapp.md)し、[テスト](interactive-test.md)します。  

LUIS は、慎重に選択された発話で有効なモデルを構築します。 追加する発話が多すぎると、混乱が発生するので役に立ちません。  

少数の発話で開始し、[エンドポイント発話を確認](label-suggested-utterances.md)して意図の予測とエンティティの抽出が正しいことを確認することをお勧めします。

## <a name="ignoring-words-and-punctuation"></a>単語と句読点を無視する
発話の例にある特定の単語や句読点を無視する場合、[パターン](luis-concept-patterns.md#pattern-syntax)と _ignore_ 構文を使用します。 

## <a name="training-utterances"></a>発話のトレーニング
トレーニングは非決定論的です。発話の予測は、バージョンまたはアプリによって若干変わる可能性があります。

## <a name="testing-utterances"></a>発話のテスト 

開発者は、発話をエンドポイントに送信することにより、実際のトラフィックで LUIS アプリケーションのテストを開始する必要があります。 これらの発話は、[発話の確認](label-suggested-utterances.md)での意図とエンティティのパフォーマンスを向上させるために使われます。 LUIS Web サイトのテスト ウィンドウで送信されるテストは、エンドポイント経由では送信されず、したがってアクティブな学習には影響しません。 

## <a name="review-utterances"></a>発話の確認
モデルがトレーニングされて発行され、[エンドポイント](luis-glossary.md#endpoint) クエリを受け取った後、LUIS によって提案された[発話を確認](label-suggested-utterances.md)します。 LUIS は、意図またはエンティティに対してスコアの低いエンドポイント発話を選択します。 

## <a name="best-practices"></a>ベスト プラクティス
[ベスト プラクティス](luis-concept-best-practices.md)を確認してさらに詳しく学習してください。

## <a name="next-steps"></a>次の手順
ユーザーの発話を理解するように LUIS アプリをトレーニングする方法については、[発話の例の追加][add-example-utterances]に関するページをご覧ください。

[add-example-utterances]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-add-example-utterances
[BingSpellCheck]: https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/proof-text