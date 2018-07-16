---
title: Language Understanding で事前構築済みの意図とエンティティを追加して一般的なデータを抽出する - Azure | Microsoft Docs
description: 事前構築済みの意図とエンティティを使用して、さまざまな種類のエンティティ データを抽出する方法を説明します。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 06/11/2018
ms.author: v-geberr
ms.openlocfilehash: 20950ced66497fb0dc96365975b37f244f677ce3
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266381"
---
# <a name="use-prebuilt-intents-and-entities-to-handle-common-intents-and-data"></a>事前構築済みの意図とエンティティを使用して一般的な意図とデータを処理する
Human Resources クイック スタート アプリに事前構築済みの意図とエンティティを追加して、意図の予測の取得とデータの抽出を迅速化します。 

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
* 事前構築済み意図を追加する 
* 事前構築済みエンティティの datetimeV2 と number を追加する
* トレーニングして公開する
* LUIS にクエリを実行し、予測応答を受け取る

## <a name="before-you-begin"></a>開始する前に
[カスタム ドメイン](luis-quickstart-intents-only.md) クイック スタートの Human Resources アプリがない場合は、[LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-intent-only-HumanResources.json) GitHub リポジトリから [LUIS][LUIS] Web サイトの新しいアプリに JSON を[インポート](create-new-app.md#import-new-app)します。

元の Human Resources アプリを保持する場合は、[[Settings]\(設定\)](luis-how-to-manage-versions.md#clone-a-version) ページでバージョンを複製し、`prebuilts` という名前を付けます。 複製は、元のバージョンに影響を及ぼさずに LUIS のさまざまな機能を使用するための優れた方法です。 

## <a name="add-prebuilt-intents"></a>事前構築済み意図を追加する
LUIS には、ユーザーの一般的な意図を使用する際に役立つ事前構築済み意図が用意されています。  

1. LUIS の **[Build]\(ビルド\)** セクションにアプリがあることを確認します。 右上のメニュー バーの **[Build]\(ビルド\)** を選択すると、このセクションに変更できます。 

    [ ![右上のナビゲーション バーの [Build]\(ビルド\) が強調表示された LUIS アプリのスクリーンショット](./media/luis-tutorial-prebuilt-intents-and-entities/first-image.png)](./media/luis-tutorial-prebuilt-intents-and-entities/first-image.png#lightbox)

2. **[Add prebuilt domain intent]\(ドメインの事前構築済み意図の追加\)** を選択します。 

    [ ![[Add prebuilt domain intent]\(ドメインの事前構築済み意図の追加\) ボタンが強調表示されている [Intents]\(意図\) ページのスクリーンショット](./media/luis-tutorial-prebuilt-intents-and-entities/add-prebuilt-domain-button.png) ](./media/luis-tutorial-prebuilt-intents-and-entities/add-prebuilt-domain-button.png#lightbox)

3. `Utilities` を検索します。 

    [ ![検索ボックスに「Utilities」と入力された事前構築済み意図ダイアログのスクリーンショット](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png)](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png#lightbox)

4. 次の意図を選択し、**[完了]** をクリックします。 

    * Utilities.Cancel
    * Utilities.Confirm
    * Utilities.Help
    * Utilities.Stop
    * Utilities.StartOver

## <a name="add-prebuilt-entities"></a>事前構築済みエンティティを追加する
LUIS には、一般的なデータ抽出のための事前構築済みエンティティが用意されています。 

1. 左側のナビゲーション メニューの **[Entities]\(エンティティ\)** を選択します。

    [ ![左側のナビゲーションで [Entities]\(エンティティ\) が強調表示されている意図の一覧のスクリーンショット](./media/luis-tutorial-prebuilt-intents-and-entities/entities-navigation.png)](./media/luis-tutorial-prebuilt-intents-and-entities/entities-navigation.png#lightbox)

2. **[Manage prebuilt entities]\(事前構築済みエンティティの管理\)** を選択します。

    [ ![[Manage prebuilt entities]\(事前構築済みエンティティの管理\) が強調表示されているエンティティの一覧のスクリーンショット](./media/luis-tutorial-prebuilt-intents-and-entities/manage-prebuilt-entities-button.png)](./media/luis-tutorial-prebuilt-intents-and-entities/manage-prebuilt-entities-button.png#lightbox)

3. 事前構築済みエンティティの一覧で **[number]** と **[datetimeV2]** を選択し、**[完了]** をクリックします。

    ![[number] が選択されている事前構築済みエンティティ ダイアログのスクリーンショット](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

## <a name="train-and-publish-the-app"></a>アプリをトレーニングして公開する
1. LUIS Web サイトの右上にある **[Train]\(トレーニング\)** ボタンを選択します。 

    ![[Train]\(トレーニング\) ボタン](./media/luis-quickstart-intents-only/train-button.png)

    成功したことを示す緑色のステータス バーが Web サイトの上部に表示されたら、トレーニングは完了しています。

    ![トレーニング済みを示すステータス バー](./media/luis-quickstart-intents-only/trained.png)

2. LUIS Web サイトの右上にある **[Publish]\(公開\)** を選択して、[Publish]\(公開\) ページを開きます。 運用スロットが既定で選択されています。 運用スロットを選択して、**[Publish]\(公開\)** を選択します。 成功したことを示す緑色のステータス バーが Web サイトの上部に表示されたら、公開は完了しています。

    公開前またはエンドポイント URL をテストする前に、Azure portal で LUIS キーを作成する必要はありません。 すべての LUIS アプリケーションには、作成用の無料のスターター キーがあります。 無制限の作成と少数の[エンドポイント ヒット](luis-boundaries.md#key-limits)が提供されます。 

## <a name="query-endpoint-with-an-utterance"></a>発話を使用してエンドポイントにクエリを実行する
**[Publish]\(公開\)** ページで、ページの下部にある**エンドポイント**のリンクを選択します。 別のブラウザー ウィンドウが開き、アドレス バーにエンドポイント URL が表示されます。 アドレスの URL の末尾に移動し、「`I want to cancel on March 3`」と入力します。 最後のクエリ文字列パラメーターは `q` です。これは発話の**クエリ**です。 

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

事前構築済みの意図とエンティティをすばやく簡単に追加することで、クライアント アプリケーションで会話管理を追加し、一般的なデータ型を抽出できます。 

## <a name="next-steps"></a>次の手順

[エンティティの詳細](luis-concept-entity-types.md)を確認します。 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
