---
title: 単語のスペルミスの修正 - LUIS
titleSuffix: Azure Cognitive Services
description: Bing Spell Check API V7 を LUIS エンドポイント クエリに追加して、発話の単語のスペルミスを修正します。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 01/12/2021
ms.openlocfilehash: f416fe8ef4f6e89d07e6065d4c9435642d9bacb9
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/13/2021
ms.locfileid: "98179641"
---
# <a name="correct-misspelled-words-with-bing-search-resource"></a>Bing Search リソースを使用してスペルミスのある単語を修正する

LUIS アプリと [Bing Search](https://ms.portal.azure.com/#create/Microsoft.BingSearch) を統合すると、LUIS によって発話のスコアとエンティティが予測される前に、発話の単語のスペル ミスを修正できます。

## <a name="create-endpoint-key"></a>エンドポイント キーの作成

Azure portal で Bing Search リソースを作成するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) にログインします。

2. 左上隅の **[リソースの作成]** を選択します。

3. 検索ボックスに「`Bing Search V7`」と入力し、サービスを選択します。

4. 右側に情報パネルが表示され、法的通知などの情報が示されます。 **[作成]** を選択して、サブスクリプション作成プロセスを開始します。

    :::image type="content" source="./media/luis-tutorial-bing-spellcheck/bing-search-resource-portal.png" alt-text="Bing Spell Check API V7 リソース":::

5. 次のパネルで、サービス設定を入力します。 サービス作成プロセスが完了するまで待ちます。

6. リソースが作成されたら、左側の **[Keys and Endpoint]\(キーとエンドポイント\)** ブレードに移動します。 

7. 予測要求のヘッダーに追加するキーの 1 つをコピーします。 必要なのは 2 つのキーのうち 1 つだけです。

8. 予測要求ヘッダーの `mkt-bing-spell-check-key` にキーを追加します。

<!--
## Using the key in LUIS test panel
There are two places in LUIS to use the key. The first is in the [test panel](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key isn't saved into LUIS but instead is a session variable. You need to set the key every time you want the test panel to apply the Bing Spell Check API v7 service to the utterance. See [instructions](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in the test panel for setting the key.
-->
## <a name="adding-the-key-to-the-endpoint-url"></a>エンドポイント URL へのキーの追加
スペル修正を適用するクエリごとに、エンドポイント クエリでクエリ ヘッダー パラメーターに渡される Bing Spellcheck リソース キーが必要です。 LUIS を呼び出すチャットボットを使用することも、LUIS エンドポイント API を直接呼び出すこともできます。 エンドポイントがどのように呼び出されたかに関係なく、すべての呼び出しに、スペルの修正が適切に動作するうえでヘッダーの要求に必要な情報が含まれていなければなりません。 **mkt-bing-spell-check-key** の値をキー値に設定する必要があります。


## <a name="send-misspelled-utterance-to-luis"></a>LUIS への発話のスペルミスの送信
1. 送信する予測クエリに、"その山ままでどのくらいの距離ですか?" などのスペルミスのある発話を追加します。 英語では、`n` が 1 つの `mountain` が正しいスペルです。

2. LUIS が、JSON の結果 `How far is the mountain?` で応答します。 Bing Spell Check API v7 によってスペル ミスが検出された場合、LUIS アプリの JSON 応答の `query` フィールドには元のクエリが、`alteredQuery` フィールドには、LUIS に送信された修正済みクエリが含まれています。

```json
{
  "query": "How far is the mountainn?",
  "alteredQuery": "How far is the mountain?",
  "topScoringIntent": {
    "intent": "Concierge",
    "score": 0.183866
  },
  "entities": []
}
```

## <a name="ignore-spelling-mistakes"></a>スペル ミスの無視

Bing Search API v7 サービスを使用したくない場合は、正しいスペルと正しくないスペルを追加する必要があります。

次の 2 つの解決策があります。

* LUIS が正しいスペルと入力ミスを学習できるように、さまざまなスペルを含んだ発話の例にラベルを付ける。 このラベル付けオプションは、スペル チェックを使用するよりも手間がかかります。
* さまざまなバリエーションの単語を含んだ語句一覧を作成する。 この解決策では、発話の例に含まれる単語のバリエーションにラベルを付ける必要はありません。


> [!div class="nextstepaction"]
> [発話の例の詳細](./luis-how-to-add-entities.md)
