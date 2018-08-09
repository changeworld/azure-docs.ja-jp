---
title: 'チュートリアル: 感情分析を返す LUIS アプリを作成する - Azure | Microsoft Docs'
description: このチュートリアルでは、感情分析を LUIS アプリに追加して、ポジティブ、ネガティブ、およびニュートラルな感情について発話を分析する方法を説明します。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: baa449bb9e78a5c6437b0a9528e5d1f10dfa519f
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39520454"
---
# <a name="tutorial-9--add-sentiment-analysis"></a>チュートリアル: 9.   センチメント分析を追加する
このチュートリアルでは、ポジティブ、ネガティブ、およびニュートラルな感情を発話から抽出する方法を示すアプリを作成します。

<!-- green checkmark -->
> [!div class="checklist"]
> * センチメント分析を理解する
> * 人事 (HR) ドメインで LUIS アプリを使用する 
> * センチメント分析を追加する
> * アプリをトレーニングして公開する
> * アプリのエンドポイントをクエリして LUIS JSON の応答を表示する 

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>開始する前に
[事前構築済みの keyPhrase エンティティ](luis-quickstart-intent-and-key-phrase.md) チュートリアルからの人事アプリを保持していない場合は、JSON を [LUIS](luis-reference-regions.md#luis-website) Web サイトの新しいアプリに[インポート](luis-how-to-start-new-app.md#import-new-app)します。 インポートするアプリは、[LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-keyphrase-HumanResources.json) GitHub リポジトリにあります。

元の人事アプリを保持したい場合は、[[設定]](luis-how-to-manage-versions.md#clone-a-version) ページ上でバージョンを複製して、`sentiment` という名前を付けます。 複製は、元のバージョンに影響を及ぼさずに LUIS のさまざまな機能を使用するための優れた方法です。 

## <a name="sentiment-analysis"></a>センチメント分析
感情分析は、ユーザーの発話がポジティブ、ネガティブ、またはニュートラルかを判断する機能です。 

以下の発話は、感情の例を示しています。

|センチメント|Score|発話|
|:--|:--|:--|
|ポジティブ|0.91 |John W. Smith はパリのプレゼンテーションで大活躍だった。|
|ポジティブ|0.84 |jill-jones@mycompany.com は Parker への売り込みですばらしい働きをした。|

センチメント分析は、どの発話にも適用されるアプリ設定です。 発話の中にセンチメントを表す単語を見つけて、ラベル付けする必要はありません。発話全体にセンチメント分析が適用されるためです。 

## <a name="add-employeefeedback-intent"></a>EmployeeFeedback 意図を追加する 
新しい意図を追加して、会社のメンバーから従業員のフィードバックをキャプチャします。 

1. 人事アプリは必ず、LUIS の**ビルド** セクションに配置してください。 右上のメニュー バーにある **[ビルド]** を選択すると、このセクションに変更できます。 

    [ ![右上のナビゲーション バーにある [ビルド] が強調表示された LUIS アプリのスクリーンショット](./media/luis-quickstart-intent-and-sentiment-analysis/hr-first-image.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-first-image.png#lightbox)

2. **[Create new intent]\(意図の新規作成\)** を選択します。

    [ ![右上のナビゲーション バーの [Build]\(ビルド\) が強調表示された LUIS アプリのスクリーンショット](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent.png#lightbox)

3. 新しい意図の名前として「`EmployeeFeedback`」と入力します。

    ![名前として EmployeeFeedback が指定された [Create new intent]\(意図の新規作成\) ダイアログ ボックス](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent-ddl.png)

4. 良い仕事をした従業員や改善が必要な領域を示す発話をいくつか追加します。

    この人事アプリでは、従業員がリスト エンティティ `Employee` で、名前、メール、電話の内線番号、携帯電話番号、および米国連邦政府の社会保障番号によって定義されていることに注意してください。 

    |発話|
    |--|
    |425-555-1212 は産休から復帰した同僚を迎えるときに良い仕事をした|
    |234-56-7891 は、悲しみにくれている同僚を上手く慰めた。|
    |jill-jones@mycompany.com は、事務処理に必要な請求書の一部を用意できていなかった。|
    |john.w.smith@mycompany.com は、必要なフォームの提出が 1 か月遅れたうえ、そのフォームには署名がなかった|
    |x23456 は、重要なマーケティング オフサイト ミーティングに参加しなかった。|
    |x12345 は、6 月レビュー会議に出席しなかった。|
    |Jill Jones はハーバードでの売り込みを上手くこなした|
    |John W. Smith はスタンフォードのプレゼンテーションで大活躍だった|

    [ ![EmployeeFeedback 意図の発話の例が示された LUIS アプリのスクリーンショット](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png#lightbox)

## <a name="train-the-luis-app"></a>LUIS アプリをトレーニングする

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>感情分析を含むアプリを構成する
**[Publish]\(公開\)** ページで、センチメント分析を構成します。 

1. 右上のナビゲーションで **[Publish]\(公開\)** を選択します。

    ![[Publish]\(公開\) ボタンが展開された [Intents]\(意図\) ページのスクリーンショット ](./media/luis-quickstart-intent-and-sentiment-analysis/hr-publish-button-in-top-nav-highlighted.png)

2. **[Enable Sentiment Analysis]\(感情分析を有効にする\)** を選択します。 

## <a name="publish-app-to-endpoint"></a>エンドポイントにアプリを公開する

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-an-utterance"></a>発話を使用してエンドポイントにクエリを実行する

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. アドレスの URL の末尾に移動し、「`Jill Jones work with the media team on the public portal was amazing`」と入力します。 最後の querystring パラメーターは `q` です。これは発話の**クエリ**です。 この発話はラベル付けされたどの発話とも同じではないので、よいテストであり、感情分析が抽出された `EmployeeFeedback` 意図を返す必要があります。

```
{
  "query": "Jill Jones work with the media team on the public portal was amazing",
  "topScoringIntent": {
    "intent": "EmployeeFeedback",
    "score": 0.4983256
  },
  "intents": [
    {
      "intent": "EmployeeFeedback",
      "score": 0.4983256
    },
    {
      "intent": "MoveEmployee",
      "score": 0.06617523
    },
    {
      "intent": "GetJobInformation",
      "score": 0.04631853
    },
    {
      "intent": "ApplyForJob",
      "score": 0.0103248553
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.007531875
    },
    {
      "intent": "FindForm",
      "score": 0.00344597152
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00337914471
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.0026357458
    },
    {
      "intent": "None",
      "score": 0.00214573368
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.00157622492
    },
    {
      "intent": "Utilities.Confirm",
      "score": 7.379545E-05
    }
  ],
  "entities": [
    {
      "entity": "jill jones",
      "type": "Employee",
      "startIndex": 0,
      "endIndex": 9,
      "resolution": {
        "values": [
          "Employee-45612"
        ]
      }
    },
    {
      "entity": "media team",
      "type": "builtin.keyPhrase",
      "startIndex": 25,
      "endIndex": 34
    },
    {
      "entity": "public portal",
      "type": "builtin.keyPhrase",
      "startIndex": 43,
      "endIndex": 55
    },
    {
      "entity": "jill jones",
      "type": "builtin.keyPhrase",
      "startIndex": 0,
      "endIndex": 9
    }
  ],
  "sentimentAnalysis": {
    "label": "positive",
    "score": 0.8694164
  }
}
```

sentimentAnalysis はポジティブで、スコアは 0.86 です。 

## <a name="what-has-this-luis-app-accomplished"></a>この LUIS アプリの処理内容
感情分析が有効になったこのアプリでは、、自然言語クエリの意図を識別し、感情全体をスコアとして含むデータを抽出しました。 

チャットボットは、会話の次のステップを判断することができる十分な情報を取得しまた。 

## <a name="where-is-this-luis-data-used"></a>この LUIS データの使用場所 
LUIS はこの要求の処理を完了しています。 チャットボットなどの呼び出し側アプリは、発話から topScoringIntent の結果と感情データを取得し、次のステップに進むことができます。 LUIS は、ボットや呼び出し側アプリケーションのためにこのようなプログラム作業を実行しません。 LUIS はユーザーの意図を判断するだけです。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"] 
> [人事アプリでエンドポイントの発話をレビューする](luis-tutorial-review-endpoint-utterances.md) 

