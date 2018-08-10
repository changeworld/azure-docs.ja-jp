---
title: Language Understanding で事前構築済みの意図とエンティティを追加して一般的なデータを抽出する - Azure | Microsoft Docs
description: 事前構築済みの意図とエンティティを使用して、さまざまな種類のエンティティ データを抽出する方法を説明します。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 08/03/2018
ms.author: diberry
ms.openlocfilehash: 5e4201f3d0684066bb6ba75850191e28405d8e8a
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39522273"
---
# <a name="tutorial-2-add-prebuilt-intents-and-entities"></a>チュートリアル: 2. 事前構築済みの意図とエンティティの追加
Human Resources チュートリアル アプリに事前構築済みの意図とエンティティを追加して、意図の予測の取得とデータの抽出を迅速化します。 

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
* 事前構築済み意図を追加する 
* 事前構築済みエンティティの datetimeV2 と number を追加する
* トレーニングして公開する
* LUIS にクエリを実行し、予測応答を受け取る

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>開始する前に
前チュートリアルの[人事](luis-quickstart-intents-only.md)アプリがない場合は、[LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-intent-only-HumanResources.json) GitHub リポジトリから JSON を [LUIS](luis-reference-regions.md#luis-website) Web サイトの新しいアプリに[インポート](luis-how-to-start-new-app.md#import-new-app)します。

元の Human Resources アプリを保持する場合は、[[Settings]\(設定\)](luis-how-to-manage-versions.md#clone-a-version) ページでバージョンを複製し、`prebuilts` という名前を付けます。 複製は、元のバージョンに影響を及ぼさずに LUIS のさまざまな機能を使用するための優れた方法です。 

## <a name="add-prebuilt-intents"></a>事前構築済み意図を追加する
LUIS には、ユーザーの一般的な意図を使用する際に役立つ事前構築済み意図が用意されています。  

1. LUIS の **[Build]\(ビルド\)** セクションにアプリがあることを確認します。 右上のメニュー バーの **[Build]\(ビルド\)** を選択すると、このセクションに変更できます。 

2. **[Add prebuilt domain intent]\(ドメインの事前構築済み意図の追加\)** を選択します。 

    [ ![[Add prebuilt domain intent]\(ドメインの事前構築済み意図の追加\) ボタンが強調表示されている [Intents]\(意図\) ページのスクリーンショット](./media/luis-tutorial-prebuilt-intents-and-entities/add-prebuilt-domain-button.png) ](./media/luis-tutorial-prebuilt-intents-and-entities/add-prebuilt-domain-button.png#lightbox)

3. `Utilities` を検索します。 

    [ ![検索ボックスに「Utilities」と入力された事前構築済み意図ダイアログのスクリーンショット](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png)](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png#lightbox)

4. 次の意図を選択し、**[完了]** をクリックします。 

    * Utilities.Cancel
    * Utilities.Confirm
    * Utilities.Help
    * Utilities.StartOver
    * Utilities.Stop


## <a name="add-prebuilt-entities"></a>事前構築済みエンティティを追加する
LUIS には、一般的なデータ抽出のための事前構築済みエンティティが用意されています。 

1. 左側のナビゲーション メニューの **[Entities]\(エンティティ\)** を選択します。

    [ ![左側のナビゲーションで [Entities]\(エンティティ\) が強調表示されている意図の一覧のスクリーンショット](./media/luis-tutorial-prebuilt-intents-and-entities/entities-navigation.png)](./media/luis-tutorial-prebuilt-intents-and-entities/entities-navigation.png#lightbox)

2. **[Manage prebuilt entities]\(事前構築済みエンティティの管理\)** を選択します。

    [ ![[Manage prebuilt entities]\(事前構築済みエンティティの管理\) が強調表示されているエンティティの一覧のスクリーンショット](./media/luis-tutorial-prebuilt-intents-and-entities/manage-prebuilt-entities-button.png)](./media/luis-tutorial-prebuilt-intents-and-entities/manage-prebuilt-entities-button.png#lightbox)

3. 事前構築済みエンティティの一覧で **[number]** と **[datetimeV2]** を選択し、**[完了]** をクリックします。

    ![[number] が選択されている事前構築済みエンティティ ダイアログのスクリーンショット](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

## <a name="train-and-publish-the-app"></a>アプリをトレーニングして公開する

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-app-to-endpoint"></a>エンドポイントにアプリを公開する

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-endpoint-with-an-utterance"></a>発話を使用してエンドポイントにクエリを実行する

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. アドレスの URL の末尾に移動し、「`I want to cancel on March 3`」と入力します。 最後のクエリ文字列パラメーターは `q` です。これは発話の**クエリ**です。 

    結果では、意図 Utilities.Cancel が予測され、3 月 3 日の日付と数値 3 が抽出されています。 

    ```
    {
      "query": "I want to cancel on March 3",
      "topScoringIntent": {
        "intent": "Utilities.Cancel",
        "score": 0.7818295
      },
      "intents": [
        {
          "intent": "Utilities.Cancel",
          "score": 0.7818295
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0237864349
        },
        {
          "intent": "GetJobInformation",
          "score": 0.017576348
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.0130122062
        },
        {
          "intent": "Utilities.Help",
          "score": 0.006731322
        },
        {
          "intent": "None",
          "score": 0.00524190161
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.004912514
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00092950504
        }
      ],
      "entities": [
        {
          "entity": "march 3",
          "type": "builtin.datetimeV2.date",
          "startIndex": 20,
          "endIndex": 26,
          "resolution": {
            "values": [
              {
                "timex": "XXXX-03-03",
                "type": "date",
                "value": "2018-03-03"
              },
              {
                "timex": "XXXX-03-03",
                "type": "date",
                "value": "2019-03-03"
              }
            ]
          }
        },
        {
          "entity": "3",
          "type": "builtin.number",
          "startIndex": 26,
          "endIndex": 26,
          "resolution": {
            "value": "3"
          }
        }
      ]
    }
    ```

    3 月 3 日の値が 2 つあるのは、3 月 3 日が過去か未来かが、発話の中で言及されなかったからです。 必要に応じて仮説を立てるか明確化を求めるかは、LUIS 呼び出し元アプリケーションの判断に委ねられます。 

    事前構築済みの意図とエンティティをすばやく簡単に追加することで、クライアント アプリケーションで会話管理を追加し、一般的なデータ型を抽出できます。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [正規表現エンティティを追加する](luis-quickstart-intents-regex-entity.md)

