---
title: センチメント分析
titleSuffix: Azure Cognitive Services
description: このチュートリアルでは、ポジティブ、ネガティブ、およびニュートラルな感情を発話から抽出する方法を示すアプリを作成します。 感情は発話全体から決定されます。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: d93c7619bb670a81372ab83359836a78b8956b09
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53098937"
---
# <a name="tutorial-9--extract-sentiment-of-overall-utterance"></a>チュートリアル 9: 発話全体から感情を抽出する
このチュートリアルでは、ポジティブ、ネガティブ、およびニュートラルな感情を発話から抽出する方法を示すアプリを作成します。 感情は発話全体から決定されます。

感情分析は、ユーザーの発話がポジティブ、ネガティブ、またはニュートラルかを判断する機能です。 

以下の発話は、感情の例を示しています。

|センチメント|Score|発話|
|:--|:--|:--|
|ポジティブ|0.91 |John W. Smith はパリのプレゼンテーションで大活躍だった。|
|ポジティブ|0.84 |jill-jones@mycompany.com は Parker への売り込みですばらしい働きをした。|

感情分析は、どの発話にも適用される発行設定です。 発話の中にセンチメントを表す単語を見つけて、ラベル付けする必要はありません。発話全体にセンチメント分析が適用されるためです。 

それは発行設定であるため、意図またはエンティティのページでは確認できません。 [対話型テスト](luis-interactive-test.md#view-sentiment-results)のウィンドウで、またはエンドポイント URL のテスト時に確認できます。 

**このチュートリアルで学習する内容は次のとおりです。**

<!-- green checkmark -->
> [!div class="checklist"]
> * 既存のチュートリアル アプリを使用する 
> * 感情分析を発行設定として追加する
> * トレーニング
> * [発行]
> * 発話の感情をエンドポイントから取得する

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>既存のアプリを使用する

最後のチュートリアルで作成した、**HumanResources** という名前のアプリを引き続き使用します。 

以前のチュートリアルの HumanResources アプリがない場合は、次の手順を使用します。

1.  [アプリの JSON ファイル](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-keyphrase-HumanResources.json)をダウンロードして保存します。

2. JSON を新しいアプリにインポートします。

3. **[管理]** セクションの **[バージョン]** タブで、バージョンを複製し、それに `sentiment` という名前を付けます。 複製は、元のバージョンに影響を及ぼさずに LUIS のさまざまな機能を使用するための優れた方法です。 バージョン名は URL ルートの一部として使用されるため、URL 内で有効ではない文字を名前に含めることはできません。

## <a name="employeefeedback-intent"></a>EmployeeFeedback 意図 
新しい意図を追加して、会社のメンバーから従業員のフィードバックをキャプチャします。 

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. **[Create new intent]\(意図の新規作成\)** を選択します。

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

## <a name="train"></a>トレーニング

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>感情分析を含むアプリを構成する
1. 右上のナビゲーションで **[管理]** を選択し、左側のメニューから **[Publish settings]\(発行設定\)** を選択します。

2. **[Sentiment Analysis]\(感情分析\)** を切り替えてこの設定を有効にします。 

    ![感情分析を発行設定として有効にする](./media/luis-quickstart-intent-and-sentiment-analysis/turn-on-sentiment-analysis-as-publish-setting.png)

## <a name="publish"></a>[発行]

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-sentiment-of-utterance-from-endpoint"></a>発話の感情をエンドポイントから取得する

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. アドレスの URL の末尾に移動し、「`Jill Jones work with the media team on the public portal was amazing`」と入力します。 最後の querystring パラメーターは `q` です。これは発話の**クエリ**です。 この発話はラベル付けされたどの発話とも同じではないので、よいテストであり、感情分析が抽出された `EmployeeFeedback` 意図を返す必要があります。
    
    ```json
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

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>次の手順
このチュートリアルでは、感情分析を発行設定として追加し、発話全体から感情値を抽出します。

> [!div class="nextstepaction"] 
> [人事アプリでエンドポイントの発話をレビューする](luis-tutorial-review-endpoint-utterances.md) 

