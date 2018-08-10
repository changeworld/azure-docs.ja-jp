---
title: Language Understanding (LUIS) でエンドポイントの発話をレビューするチュートリアル - Azure | Microsoft Docs
description: このチュートリアルでは、LUIS の人事 (HR) ドメインにおけるエンドポイントの発話をレビューする方法について説明します。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 08/03/2018
ms.author: diberry
ms.openlocfilehash: 5ce08861934305cccca9933a822fccf642746a59
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39527706"
---
# <a name="tutorial-review-endpoint-utterances"></a>チュートリアル: エンドポイントの発話のレビュー
このチュートリアルでは、LUIS HTTP エンドポイント経由で受け取った発話を確認または修正することによって、アプリの予測精度を高めます。 

<!-- green checkmark -->
> [!div class="checklist"]
> * エンドポイントの発話のレビューについて理解する 
> * 人事 (HR) ドメイン向けの LUIS アプリを使用する 
> * エンドポイントの発言の確認
> * アプリをトレーニングして公開する
> * アプリのエンドポイントをクエリして LUIS JSON の応答を表示する

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>開始する前に
[センチメント](luis-quickstart-intent-and-sentiment-analysis.md) チュートリアルの人事アプリがない場合は、[LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-sentiment-HumanResources.json) Github リポジトリからアプリをインポートしてください。 新たにインポートしたアプリとしてこのチュートリアルを使用する場合は、発話をトレーニングして公開したうえで、エンドポイントに追加する必要があります。エンドポイントへの追加は、[スクリプト](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/demo-upload-endpoint-utterances/endpoint.js)を使って行うか、またはエンドポイントからブラウザーで行います。 追加する発話は次のとおりです。

   [!code-nodejs[Node.js code showing endpoint utterances to add](~/samples-luis/examples/demo-upload-endpoint-utterances/endpoint.js?range=15-26)]

元の Human Resources アプリを保持する場合は、[[Settings]\(設定\)](luis-how-to-manage-versions.md#clone-a-version) ページでバージョンを複製し、`review` という名前を付けます。 複製は、元のバージョンに影響を及ぼさずに LUIS のさまざまな機能を使用するための優れた方法です。 

このチュートリアル シリーズで使用したアプリのバージョンをすべてお持ちの方は、**[Review endpoint utterances]\(エンドポイントの発話の確認\)** リストがどのバージョンも変わらないことに気付いて意外に感じるかもしれません。 実際に編集している発話のバージョンや、エンドポイントに公開されたアプリのバージョンに関係なく、レビューする発話のプールは 1 つだけです。 

## <a name="purpose-of-reviewing-endpoint-utterances"></a>エンドポイントの発話をレビューする目的
このレビュー プロセスも、LUIS がユーザーのアプリ ドメインを学習する方法の 1 つとなります。 LUIS は、レビュー リストから発話を選択しました。 このリストには、次の特徴があります。

* アプリに固有である。
* アプリの予測精度を高めることを意図している。 
* 定期的にレビューする必要がある。 

エンドポイントの発話をレビューすることによって、その発話から予測される意図を確認または修正することになります。 また、予測されなかったカスタム エンティティへのラベル付けも行います。 

## <a name="review-endpoint-utterances"></a>エンドポイントの発言の確認

1. 人事アプリは必ず、LUIS の**ビルド** セクションに配置してください。 右上のメニュー バーの **[Build]\(ビルド\)** を選択すると、このセクションに変更できます。 

2. 左側のナビゲーションから **[Review endpoint utterances]\(エンドポイントの発話の確認\)** を選択します。 このリストは、**ApplyForJob** という意図がフィルターで抽出されています。 

    [ ![左側のナビゲーションにある [Review endpoint utterances]\(エンドポイントの発話の確認\) ボタンのスクリーンショット](./media/luis-tutorial-review-endpoint-utterances/entities-view-endpoint-utterances.png)](./media/luis-tutorial-review-endpoint-utterances/entities-view-endpoint-utterances.png#lightbox)

3. **[エンティティの表示]** を切り替えて、ラベル付けされたエンティティを表示します。 
    
    [ ![[エンティティの表示] トグルを強調表示した [Review endpoint utterances]\(エンドポイントの発話の確認\) のスクリーンショット](./media/luis-tutorial-review-endpoint-utterances/select-entities-view.png)](./media/luis-tutorial-review-endpoint-utterances/select-entities-view.png#lightbox)

    |発話|正しい意図|エンティティが不明|
    |:--|:--|:--|
    |I'm looking for a job with Natural Language Processing|GetJobInfo|Job - "Natural Language Process"|

    この発話は正しい意図には存在せず、スコアは 50% 未満となっています。 **ApplyForJob** 意図には 21 の発話があります。一方、**GetJobInformation** の発話は 7 個です。 エンドポイントの発話を正しくアラインメントすると共に、**GetJobInformation** 意図には、もっと発話を追加する必要があります。 この作業については各自が演習として行ってください。 それぞれの意図に割り当てられる発話の例は、**None** を除き、ほぼ同数となるようにする必要があります。 **None** 意図は、アプリ内の発話全体の 10% にすることをお勧めします。 

    **[Tokens View]\(トークン ビュー\)** では、発話に表示されているいずれかの青色のテキストにマウス ポインターを合わせることで、予測されたエンティティ名を確認できます。 

4. 意図 `I'm looking for a job with Natual Language Processing` について、**[Aligned intent]\(アラインメントされた意図\)** 列から、正しい意図である **[GetJobInformation]** を選択します。 

    [ ![[Review endpoint utterances]\(エンドポイントの発話の確認\) で発話を意図にアラインメントする操作のスクリーンショット](./media/luis-tutorial-review-endpoint-utterances/align-intent-1.png)](./media/luis-tutorial-review-endpoint-utterances/align-intent-1.png#lightbox)

5. 同じ発話で、`Natural Language Processing` のエンティティが keyPhrase になっています。 これは **Job** エンティティに変更する必要があります。 [`Natural Language Processing`] を選択し、ボックスの一覧から **[Job]** エンティティを選択してください。

    [ ![[Review endpoint utterances]\(エンドポイントの発話の確認\) で発話のエンティティをラベル付けする操作のスクリーンショット](./media/luis-tutorial-review-endpoint-utterances/label-entity.png)](./media/luis-tutorial-review-endpoint-utterances/label-entity.png#lightbox)

6. 同じ行の **[Add to aligned intent]\(アラインメント済みの意図に追加\)** 列にある円形のチェックマークをオンにします。 

    [ ![意図に対する発話のアラインメントを確定する操作のスクリーンショット](./media/luis-tutorial-review-endpoint-utterances/align-utterance.png)](./media/luis-tutorial-review-endpoint-utterances/align-utterance.png#lightbox)

    この操作により、発話が **[Review endpoint utterances]\(エンドポイントの発話の確認\)** から **[GetJobInformation]** の意図に移されます。 これでエンドポイントの発話が、適切な意図の発話例になりました。 

7. この意図に関して、残りの発話をレビューします。発話のラベル付けを行い、**[Aligned intent]\(アラインメントされた意図\)** が誤っていれば修正してください。

8. すべての発話が正しければ、各行のチェック ボックスをオンにし、**[Add selected]\(選択項目の追加\)** を選択すると、発話が正しくアラインメントされます。 

    [ ![アラインメントされた意図に残りの発話を確定する操作のスクリーンショット](./media/luis-tutorial-review-endpoint-utterances/finalize-utterance-alignment.png)](./media/luis-tutorial-review-endpoint-utterances/finalize-utterance-alignment.png#lightbox)

9. 前出の発話はリストから消えます。 さらに発話が表示された場合は、引き続きリストの項目を処理します。リストが空になるまで意図を修正し、不明なエンティティのラベル付けを行ってください。 フィルター リストから次の意図を選択し、続けて発話の修正とエンティティのラベル付けを行います。 意図ごとの最後の手順で必ず、発話行の **[Add to aligned intent]\(アラインメント済みの意図に追加\)** を選択するか、または、各意図のチェック ボックスをオンにして、表の上にある **[Add selected]\(選択項目の追加\)** を選択してください。 

    これはきわめて小さなアプリです。 レビュー プロセスの所要時間は数分程度です。

## <a name="add-new-job-name-to-phrase-list"></a>新しいジョブ名をフレーズ リストに追加する
フレーズ リストは、新たに検出されたジョブ名で最新の状態に維持してください。 

1. 左側のナビゲーションから **[Phrase lists]\(フレーズ リスト\)** を選択します。

2. **[Jobs]** フレーズ リストを選択します。

3. `Natural Language Processing` を値として追加し、**[保存]** を選択します。 

## <a name="train-the-luis-app"></a>LUIS アプリをトレーニングする

LUIS は、トレーニングされてはじめて変更を認識します。 

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>アプリを公開してエンドポイント URL を取得する

このアプリをインポートした場合は、**[感情分析]** を選択する必要があります。

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-an-utterance"></a>発話を使用してエンドポイントにクエリを実行する

修正後の発話に近い発話を試してみましょう。 

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. アドレスの URL の末尾に移動し、「`Are there any natural language processing jobs in my department right now?`」と入力します。 最後の querystring パラメーターは `q` です。これは発話の**クエリ**です。 

  ```JSON
  {
    "query": "are there any natural language processing jobs in my department right now?",
    "topScoringIntent": {
      "intent": "GetJobInformation",
      "score": 0.9247605
    },
    "intents": [
      {
        "intent": "GetJobInformation",
        "score": 0.9247605
      },
      {
        "intent": "ApplyForJob",
        "score": 0.129989788
      },
      {
        "intent": "FindForm",
        "score": 0.006438211
      },
      {
        "intent": "EmployeeFeedback",
        "score": 0.00408575451
      },
      {
        "intent": "Utilities.StartOver",
        "score": 0.00194211153
      },
      {
        "intent": "None",
        "score": 0.00166400627
      },
      {
        "intent": "Utilities.Help",
        "score": 0.00118593348
      },
      {
        "intent": "MoveEmployee",
        "score": 0.0007885918
      },
      {
        "intent": "Utilities.Cancel",
        "score": 0.0006373631
      },
      {
        "intent": "Utilities.Stop",
        "score": 0.0005980781
      },
      {
        "intent": "Utilities.Confirm",
        "score": 3.719905E-05
      }
    ],
    "entities": [
      {
        "entity": "right now",
        "type": "builtin.datetimeV2.datetime",
        "startIndex": 64,
        "endIndex": 72,
        "resolution": {
          "values": [
            {
              "timex": "PRESENT_REF",
              "type": "datetime",
              "value": "2018-07-05 15:23:18"
            }
          ]
        }
      },
      {
        "entity": "natural language processing",
        "type": "Job",
        "startIndex": 14,
        "endIndex": 40,
        "score": 0.9869922
      },
      {
        "entity": "natural language processing jobs",
        "type": "builtin.keyPhrase",
        "startIndex": 14,
        "endIndex": 45
      },
      {
        "entity": "department",
        "type": "builtin.keyPhrase",
        "startIndex": 53,
        "endIndex": 62
      }
    ],
    "sentimentAnalysis": {
      "label": "positive",
      "score": 0.8251864
    }
  }
  }
  ```

  正しい意図が高いスコアで予測され、**Job** エンティティが `natural language processing` として検出されます。 

## <a name="can-reviewing-be-replaced-by-adding-more-utterances"></a>レビューの代わりに発話をもっと追加したらいいのではないでしょうか? 
発話例をもっと追加してみたらどうだろう、と思われるかもしれません。 エンドポイントの発話をレビューする目的は何でしょうか。 実世界の LUIS アプリでは、エンドポイントの発話がユーザーから与えられるため、単語の選び方や並べ方が未知のものになります。 ユーザーと同じ単語の選び方や並べ方を使用すれば、元の予測のパーセンテージはもっと高い値になるでしょう。 

## <a name="why-is-the-top-intent-on-the-utterance-list"></a>最上位の意図が発話リストにあるのはなぜでしょうか? 
エンドポイントの発話の中には、レビュー リストの中で高いパーセンテージが割り当てられるものがあります。 それらの発話も、レビューして確認する必要があります。 これらがリストに表示されるのは、次に高い意図のスコアが、最上位の意図のスコアと近すぎるためです。 

## <a name="what-has-this-tutorial-accomplished"></a>このチュートリアルによってどのような成果が得られたのでしょうか?
エンドポイントの発話をレビューすることで、このアプリの予測精度が向上しました。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [パターンの使用方法を確認する](luis-tutorial-pattern.md)
