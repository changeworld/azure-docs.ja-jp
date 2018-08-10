---
title: 'チュートリアル: キー フレーズを返す LUIS アプリを作成する - Azure | Microsoft Docs'
description: このチュートリアルでは、LUIS アプリに keyPhrase エンティティを追加して返し、主題の発話を分析する方法について説明します。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: d5b92681b9677db085b3c271c37490154d781d84
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494312"
---
# <a name="tutorial-8-add-keyphrase-entity"></a>チュートリアル: 8.  keyPhrase エンティティを追加する 
このチュートリアルでは、発話から主題を抽出する方法を示すアプリを使用します。

<!-- green checkmark -->
> [!div class="checklist"]
> * keyPhrase エンティティの概要 
> * 人事 (HR) ドメインで LUIS アプリを使用する 
> * 発話からコンテンツを抽出する keyPhrase エンティティを追加する
> * アプリをトレーニングして公開する
> * アプリのエンドポイントにクエリを実行して、キー フレーズを含む LUIS JSON の応答を確認する

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>開始する前に
[シンプル エンティティ](luis-quickstart-primary-and-secondary-data.md) チュートリアルからの人事アプリを保持していない場合は、JSON を [LUIS](luis-reference-regions.md#luis-website) Web サイトの新しいアプリに[インポート](luis-how-to-start-new-app.md#import-new-app)します。 インポートするアプリは、[LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-simple-HumanResources.json) GitHub リポジトリにあります。

元の人事アプリを保持したい場合は、[[設定]](luis-how-to-manage-versions.md#clone-a-version) ページ上でバージョンを複製して、`keyphrase` という名前を付けます。 複製は、元のバージョンに影響を及ぼさずに LUIS のさまざまな機能を使用するための優れた方法です。 

## <a name="keyphrase-entity-extraction"></a>keyPhrase エンティティの抽出
主題は、事前構築済みのエンティティ **keyPhrase** によって提供されます。 このエンティティは、発話に含まれる主題を返します。

以下の発話は、キー フレーズの例です。

|発話|keyPhrase エンティティ値|
|--|--|
|来年提供される控除額が低い新しい医療計画はありますか?|"控除額が低い"<br>"新しい医療計画"<br>"年"|
|視覚療法は高額の控除が可能な医療計画に含まれていますか?|"高額の控除が可能な医療計画"<br>"視覚療法"|

お使いのクライアント アプリケーションでは、これらの値を、他の抽出されたエンティティと共に使用して、会話の次のステップを判断できます。

## <a name="add-keyphrase-entity"></a>keyPhrase エンティティを追加する 
発話から主題を抽出する keyPhrase 事前構築済みエンティティを追加します。

1. 人事アプリは必ず、LUIS の**ビルド** セクションに配置してください。 右上のメニュー バーの **[Build]\(ビルド\)** を選択すると、このセクションに変更できます。 

2. 左側のメニューから **[Entities]\(エンティティ\)** を選択します。

    [ ![[ビルド] セクションの左側のナビゲーションで強調表示されている [Entities]\(エンティティ\) スクリーンショット](./media/luis-quickstart-intent-and-key-phrase/hr-select-entities-button.png)](./media/luis-quickstart-intent-and-key-phrase/hr-select-entities-button.png#lightbox)

3. **[Manage prebuilt entities]\(事前構築済みエンティティの管理\)** を選択します。

    [ ![[Entities]\(エンティティ\) リストのポップアップ ダイアログのスクリーンショット](./media/luis-quickstart-intent-and-key-phrase/hr-manage-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/hr-manage-prebuilt-entities.png#lightbox)

4. ポップアップ ダイアログで **[keyPhrase]** を選択し、**[完了]** を選択します。 

    [ ![[Entities]\(エンティティ\) リストのポップアップ ダイアログのスクリーンショット](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png#lightbox)

    <!-- TBD: asking Carol
    You won't see these entities labeled in utterances on the intents pages. 
    -->
5. 左側のメニューから **[Intents]\(意図\)** を選択し、**[Utilities.Confirm]** 意図を選択します。 keyPhrase エンティティは、複数の発話でラベル付けされています。 

    [ ![発話で keyPhrases がラベル付けされている Utilities.Confirm 意図のスクリーンショット](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png)](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png#lightbox)

## <a name="train-the-luis-app"></a>LUIS アプリをトレーニングする

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-app-to-endpoint"></a>エンドポイントにアプリを公開する

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]


## <a name="query-the-endpoint-with-an-utterance"></a>発話を使用してエンドポイントにクエリを実行する

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. アドレスの URL の末尾に移動し、「`does form hrf-123456 cover the new dental benefits and medical plan`」と入力します。 最後の querystring パラメーターは `q` です。これは発話の**クエリ**です。 

```
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

## <a name="what-has-this-luis-app-accomplished"></a>この LUIS アプリの処理内容
このアプリは、keyPhrase エンティティの検出を使用し、自然言語のクエリの意図を特定し、主題を含む抽出されたデータを返しました。 

チャットボットは、会話の次のステップを判断することができる十分な情報を取得しまた。 

## <a name="where-is-this-luis-data-used"></a>この LUIS データの使用場所 
LUIS はこの要求の処理を完了しています。 チャットボットなどの呼び出し側アプリは、発話から topScoringIntent の結果と keyPhrase データを取得し、次のステップに進むことができます。 LUIS は、ボットや呼び出し側アプリケーションのためにこのようなプログラム作業を実行しません。 LUIS はユーザーの意図を判断するだけです。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [センチメント分析をアプリに追加する](luis-quickstart-intent-and-sentiment-analysis.md)