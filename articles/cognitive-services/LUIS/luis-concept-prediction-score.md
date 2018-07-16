---
title: LUIS によって返される予測スコアについて - Azure | Microsoft Docs
description: LUIS における予測スコアの意味する内容を学習します。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 31c101a23892df8599b8cdc0f67647fefb969490
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265990"
---
# <a name="prediction-score"></a>予測スコア
予測スコアは、予測結果についての LUIS の信頼度を示します。 

予測スコアは通常、0 と 1 の間です。 十分に信頼できる LUIS スコアの例は 0.99 です。 信頼度の低いのスコアの例は 0.01 です。 

|スコアの値|信頼度|
|--|--|
|1|明確に一致|
|0.99|信頼度が高い|
|0.01|信頼度が低い|
|0|明確に不一致|

発話の信頼度が低かった場合、LUIS では、[LUIS][LUIS] Web サイトの **[Intent]\(意図\)** のページで、識別した**ラベル付き意図**を赤色で囲んでそのことを強調します。 

![スコアの不一致](./media/luis-concept-score/score-discrepancy.png)

## <a name="top-scoring-intent"></a>トップ スコアの意図
すべての発話予測で、トップ スコアの意図が返されます。 これは予測スコアの数値を比較したものです。 上位 2 つのスコアでは、違いが非常に小さいことがあります。 LUIS でこの近接性が示されるのは、スコアを返すときのみです。  

トップ スコアが近いことが懸念される場合は、すべての意図についてスコアが返されるようにする必要があります。 2 つの意図に、単語の選択と配置が異なる発話を追加することもできますし、LUIS 呼び出し元アプリケーション (チャットボットなど) に、上位 2 つの意図を処理する方法をプログラム的に選択させることもできます。 

## <a name="return-prediction-score-for-all-intents"></a>すべての意図について予測スコアを返す
テストまたはエンドポイントの結果には、すべての意図を含めることができます。 この構成は、`verbose=true` クエリ文字列の名前/値ペアを使用して[エンドポイント](https://aka.ms/v1-endpoint-api-docs)で設定します。 

## <a name="review-intents-with-similar-scores"></a>スコアが近い意図を確認する
正しい意図が識別されているだけでなく、どの発話でも 2 番目に識別される意図のスコアが常にかなり低いことを検証するために、すべての意図のスコアを確認することをお勧めします。 

発話のコンテキストに基づいて、複数の意図の予測スコアが近い場合、LUIS によって意図が切り替えられる場合があります。 これを解決するには、コンテキストが異なるさまざまな発話を各意図に引き続きに追加します。   

## <a name="e-exponent-notation"></a>E (指数) 表記

予測スコアでは、`9.910309E-07` など、0 から 1 の範囲を超えているように "*見える*" 指数表記が使用される場合があります。 このスコアは、非常に**小さな**数を表します。

|E 表記スコア |実際のスコア|
|--|--|
|9.910309E-07|.0000009910309|

## <a name="differences-with-predictions"></a>予測との違い
別のアプリで同じモデルをトレーニングしたときに、同じスコアにならないとしたら、その原因はトレーニングにランダムな要素があるためです。 また、複数の意図に対して 1 つの発話が重複して設定されていると、同じ発話であるのにトップの意図がトレーニング次第で変わる可能性があります。

意図の信頼性を示すためにチャットボットで特定の LUIS スコアを必要とする場合、上位 2 つの意図のスコアの差異を代わりに使用する必要があります。 これによって、トレーニングのバリエーションが柔軟になります。 

## <a name="next-steps"></a>次の手順

[エンティティの追加](luis-how-to-add-entities.md)に関するページで、LUIS アプリにエンティティを追加する方法の詳細を確認します。

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions