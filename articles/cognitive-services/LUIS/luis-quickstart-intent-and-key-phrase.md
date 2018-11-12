---
title: 'チュートリアル 8: LUIS でのキー フレーズ抽出'
titleSuffix: Azure Cognitive Services
description: keyPhrase 事前構築済みエンティティを使用して、発話から主題を抽出します。 発話に事前構築済みエンティティのラベルを付ける必要はありません。 このエンティティは自動的に検出されます。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: d6feb24b1e59aee70204d8438f1a4c51f71d1835
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2018
ms.locfileid: "51281814"
---
# <a name="tutorial-8-extract-key-phrases-of-utterance"></a>チュートリアル 8: 発話のキー フレーズを抽出する
このチュートリアルでは、事前構築済みの keyPhrase エンティティを使用して、発話から主題を抽出します。 発話に事前構築済みエンティティのラベルを付ける必要はありません。 このエンティティは自動的に検出されます。

以下の発話は、キー フレーズの例です。

|発話|keyPhrase エンティティ値|
|--|--|
|来年提供される控除額が低い新しい医療計画はありますか?|"控除額が低い"<br>"新しい医療計画"<br>"年"|
|視覚療法は高額の控除が可能な医療計画に含まれていますか?|"高額の控除が可能な医療計画"<br>"視覚療法"|

お使いのクライアント アプリケーションでは、これらの値を、他の抽出されたエンティティと共に使用して、会話の次のステップを判断できます。

**このチュートリアルで学習する内容は次のとおりです。**

<!-- green checkmark -->
> [!div class="checklist"]
> * 既存のチュートリアル アプリを使用する
> * keyPhrase エンティティを追加する 
> * トレーニング
> * [発行]
> * エンドポイントから意図とエンティティを取得する

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>既存のアプリを使用する

最後のチュートリアルで作成した、**HumanResources** という名前のアプリを引き続き使用します。 

以前のチュートリアルの HumanResources アプリがない場合は、次の手順を使用します。

1.  [アプリの JSON ファイル](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-simple-HumanResources.json)をダウンロードして保存します。

2. JSON を新しいアプリにインポートします。

3. **[管理]** セクションの **[バージョン]** タブで、バージョンを複製し、それに `keyphrase` という名前を付けます。 複製は、元のバージョンに影響を及ぼさずに LUIS のさまざまな機能を使用するための優れた方法です。 バージョン名は URL ルートの一部として使用されるため、URL 内で有効ではない文字を名前に含めることはできません。

## <a name="add-keyphrase-entity"></a>keyPhrase エンティティを追加する 
発話から主題を抽出する keyPhrase 事前構築済みエンティティを追加します。

1. [!INCLUDE[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. 左側のメニューから **[Entities]\(エンティティ\)** を選択します。

3. **[Add prebuilt entity]\(作成済みエンティティの追加\)** を選択します。

4. ポップアップ ダイアログで **[keyPhrase]** を選択し、**[完了]** を選択します。 

    [ ![[Entities]\(エンティティ\) リストのポップアップ ダイアログのスクリーンショット](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png#lightbox)

5. 左側のメニューから **[Intents]\(意図\)** を選択し、**[Utilities.Confirm]** 意図を選択します。 keyPhrase エンティティは、複数の発話でラベル付けされています。 

    [ ![発話で keyPhrases がラベル付けされている Utilities.Confirm 意図のスクリーンショット](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png)](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png#lightbox)

## <a name="train"></a>トレーニング

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>[発行]

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>エンドポイントから意図とエンティティを取得する

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. アドレスの URL の末尾に移動し、「`does form hrf-123456 cover the new dental benefits and medical plan`」と入力します。 最後の querystring パラメーターは `q` です。これは発話の**クエリ**です。 
    
    ```JSON
    {
      "query": "does form hrf-123456 cover the new dental benefits and medical plan",
      "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9300641
      },
      "intents": [
        {
          "intent": "FindForm",
          "score": 0.9300641
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0359598845
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0141798034
        },
        {
          "intent": "MoveEmployee",
          "score": 0.0112197418
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.00507669244
        },
        {
          "intent": "None",
          "score": 0.00238501839
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00202810857
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00102957746
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.0008688423
        },
        {
          "intent": "Utilities.Confirm",
          "score": 3.557994E-05
        }
      ],
      "entities": [
        {
          "entity": "hrf-123456",
          "type": "HRF-number",git 
          "startIndex": 10,
          "endIndex": 19
        },
        {
          "entity": "new dental benefits",
          "type": "builtin.keyPhrase",
          "startIndex": 31,
          "endIndex": 49
        },
        {
          "entity": "medical plan",
          "type": "builtin.keyPhrase",
          "startIndex": 55,
          "endIndex": 66
        },
        {
          "entity": "hrf",
          "type": "builtin.keyPhrase",
          "startIndex": 10,
          "endIndex": 12
        },
        {
          "entity": "-123456",
          "type": "builtin.number",
          "startIndex": 13,
          "endIndex": 19,
          "resolution": {
            "value": "-123456"
          }
        }
      ]
    }
    ```

    フォームの検索中、ユーザーが指定した情報には、フォームを見つけるうえで必要だったもの以外も含まれています。 追加情報は **builtin.keyPhrase** として返されます。 クライアント アプリケーションは、この追加情報を、"新しい歯科治療給付について人事担当者に問い合わせますか" といった補足質問に対して使用できます。また、"新しい歯科治療または医療プランに関する詳細情報" オプションを含むメニューを、アプリケーションに追加することもできます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、事前構築済みの keyPhrase エンティティを追加し、発話にラベルを付けることなく、発話内のキー フレーズをすばやく提供しました。 

> [!div class="nextstepaction"]
> [センチメント分析をアプリに追加する](luis-quickstart-intent-and-sentiment-analysis.md)