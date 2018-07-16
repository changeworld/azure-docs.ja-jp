---
title: バッチ テストを使用して LUIS の予測を改善する | Microsoft Docs
titleSuffix: Azure
description: バッチ テストを読み込み、結果を確認し、変更を加えて LUIS の予測を改善します。
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/19/2018
ms.author: v-geberr
ms.openlocfilehash: 5788f17f2724a0354a1db506971c2343c1800f01
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266398"
---
# <a name="use-batch-testing-to-find-prediction-accuracy-issues"></a>バッチ テストを使用した予測精度の問題の検出

このチュートリアルでは、バッチ テストを使用して発話予測の問題を検出する方法を説明します。  

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
* バッチ テスト ファイルを作成する 
* バッチ テストを実行する
* テスト結果を確認する
* 意図のエラーを修正する
* バッチを再テストする

## <a name="prerequisites"></a>前提条件

> [!div class="checklist"]
> * この記事に従って [LUIS] アプリケーションを作成するには、[LUIS] アカウントが必要です。

> [!Tip]
> サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)に登録できます。

## <a name="create-new-app"></a>新しいアプリを作成する
この記事では、事前構築済みドメイン HomeAutomation を使用します。 この事前構築済みドメインには、照明などの HomeAutomation デバイスを制御するための意図、エンティティ、発話があります。 アプリを作成してドメインを追加し、トレーニングして公開します。

1. [LUIS] Web サイトの **[My Apps]\(マイ アプリ\)** ページで、**[新しいアプリの作成]** を選択して新しいアプリを作成します。 

    ![新しいアプリを作成する](./media/luis-tutorial-batch-testing/create-app-1.png)

2. ダイアログで、名前として「`Batchtest-HomeAutomation`」と入力します。

    ![アプリ名を入力する](./media/luis-tutorial-batch-testing/create-app-2.png)

3. 左下隅の **[Prebuilt Domains]\(事前構築済みドメイン\)** を選択します。 

    ![[Prebuilt Domains]\(事前構築済みドメイン\) を選択する](./media/luis-tutorial-batch-testing/prebuilt-domain-1.png)

4. HomeAutomation の **[ドメインの追加]** を選択します。

    ![HomeAutomation ドメインを追加する](./media/luis-tutorial-batch-testing/prebuilt-domain-2.png)

5. 右上のナビゲーション バーの **[Train]\(トレーニング\)** を選択します。

    ![[Train]\(トレーニング\) ボタンを選択する](./media/luis-tutorial-batch-testing/train-button.png)

## <a name="batch-test-criteria"></a>バッチ テストの条件
バッチ テストでは、一度に最大 1000 個の発話をテストできます。 バッチに重複がないようにする必要があります。 現在の発話のリストを確認するために、アプリを[エクスポート](create-new-app.md#export-app)します。  

LUIS のテスト戦略では、モデル発話、バッチ テスト発話、エンドポイント発話の 3 つのデータ セットを使用します。 このチュートリアルでは、モデル発話 (意図に追加) とエンドポイント発話のどちらの発話も使用しません。 

アプリの既存の発話は、バッチ テストに使用しないでください。

```
'breezeway on please',
'change temperature to seventy two degrees',
'coffee bar on please',
'decrease temperature for me please',
'dim kitchen lights to 25 .',
'fish pond off please',
'fish pond on please',
'illuminate please',
'living room lamp on please',
'living room lamps off please',
'lock the doors for me please',
'lower your volume',
'make camera 1 off please',
'make some coffee',
'play dvd',
'set lights bright',
'set lights concentrate',
'set lights out bedroom',
'shut down my work computer',
'silence the phone',
'snap switch fan fifty percent',
'start master bedroom light .',
'theater on please',
'turn dimmer off',
'turn off ac please',
'turn off foyer lights',
'turn off living room light',
'turn off staircase',
'turn off venice lamp',
'turn on bathroom heater',
'turn on external speaker',
'turn on my bedroom lights .',
'turn on the furnace room lights',
'turn on the internet in my bedroom please',
'turn on thermostat please',
'turn the fan to high',
'turn thermostat on 70 .' 
```

## <a name="create-a-batch-to-test-intent-prediction-accuracy"></a>意図の予測精度をテストするバッチを作成する
1. [VSCode](https://code.visualstudio.com/) などのテキスト エディターで `homeauto-batch-1.json` を作成します。 

2. テストで予測する**意図**で発話を追加します。 このチュートリアルでは、簡単にするために、`HomeAutomation.TurnOn` と `HomeAutomation.TurnOff` の発話を取得し、発話内のテキスト `on` と `off` を切り替えます。 意図 `None` では、[ドメイン](luis-glossary.md#domain) (対象) 領域に含まれない発話をいくつか追加します。 

    バッチ テストの結果とバッチ JSON の関連性を理解するために、6 つの意図のみを追加します。

    ```JSON
    [
        {
          "text": "lobby on please",
          "intent": "HomeAutomation.TurnOn",
          "entities": []
        },
        {
          "text": "change temperature to seventy one degrees",
          "intent": "HomeAutomation.TurnOn",
          "entities": []
        },
        {
          "text": "where is my pizza",
          "intent": "None",
          "entities": []
        },
        {
          "text": "help",
          "intent": "None",
          "entities": []
        },
        {
          "text": "breezeway off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": []
        },
        {
          "text": "coffee bar off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": []
        }
    ]
    ```

## <a name="run-the-batch"></a>バッチを実行する
1. 上部のナビゲーション バーの **[Test]\(テスト\)** を選択します。 

    ![ナビゲーション バーの [Test]\(テスト\) を選択する](./media/luis-tutorial-batch-testing/test-1.png)

2. 右側のパネルで、**[Batch testing panel]\(バッチ テスト パネル\)** を選択します。 

    ![[Batch testing panel]\(バッチ テスト パネル\) を選択する](./media/luis-tutorial-batch-testing/test-2.png)

3. **[Import dataset]\(データセットのインポート\)** を選択します。

    ![[Import dataset]\(データセットのインポート\) を選択する](./media/luis-tutorial-batch-testing/test-3.png)

4. `homeauto-batch-1.json` ファイルのファイル システムの場所を選択します。

5. データセットに `set 1` という名前を付けます。

    ![ファイルの選択](./media/luis-tutorial-batch-testing/test-4.png)

6. **[実行]** ボタンを選択します。 テストが完了するまで待ちます。

    ![[実行] を選択する](./media/luis-tutorial-batch-testing/test-5.png)

7. **[See results]\(結果の表示\)** を選択します。

    ![結果を表示する](./media/luis-tutorial-batch-testing/test-6.png)

8. グラフと凡例で結果を確認します。

    ![バッチ結果](./media/luis-tutorial-batch-testing/batch-result-1.png)

## <a name="review-batch-results"></a>バッチ結果を確認する
バッチ結果は 2 つのセクションに分かれています。 上部のセクションには、グラフと凡例が含まれています。 グラフの領域名を選択すると、下部のセクションに発話が表示されます。

エラーは赤色で示されます。 グラフは 4 つのセクションに分かれており、2 つのセクションが赤色で表示されます。 **これらは注目すべきセクションです**。 

右上のセクションは、意図またはエンティティが存在することがテストで正しく予測されなかったことを示します。 左下のセクションは、意図またはエンティティが存在しないことがテストで正しく予測されなかったことを示します。

### <a name="homeautomationturnoff-test-results"></a>HomeAutomation.TurnOff のテスト結果
凡例で、意図 `HomeAutomation.TurnOff` を選択します。 凡例の名前の左側に緑色の成功アイコンが表示されています。 この意図にはエラーはありません。 

![バッチ結果](./media/luis-tutorial-batch-testing/batch-result-1.png)

### <a name="homeautomationturnon-and-none-intents-have-errors"></a>意図 HomeAutomation.TurnOn と None のエラー
他の 2 つの意図にはエラーがあります。これは、テストの予測がバッチ ファイルの期待値と一致しなかったことを意味します。 凡例で意図 `None` を選択して最初のエラーを確認します。 

![None 意図](./media/luis-tutorial-batch-testing/none-intent-failures.png)

グラフの赤色のセクション (**[False Positive]\(誤検知\)** と **[False Negative]\(検知漏れ\)**) にエラーが表示されます。 グラフの **[False Negative]\(検知漏れ\)** というセクション名を選択すると、グラフの下にエラーになった発話が表示されます。 

![検知漏れのエラー](./media/luis-tutorial-batch-testing/none-intent-false-negative.png)

エラーになった発話 `help` の意図の期待値は `None` でしたが、テストでは意図 `HomeAutomation.TurnOn` が予測されました。  

2 つのエラーがあります。1 つは HomeAutomation.TurnOn のエラー、もう 1 つは None のエラーです。 どちらも発話 `help` に起因するものです。この発話は期待値の None を満たしておらず、期待値とは異なる意図 HomeAutomation.TurnOn が予測されています。 

`None` の発話がエラーになった理由を特定するために、`None` の現在の発話を確認します。 

## <a name="review-none-intents-utterances"></a>None 意図の発話を確認する

1. 上部のナビゲーション バーの **[Test]\(テスト\)** を選択して、**[Test]\(テスト\)** パネルを閉じます。 

2. 上部のナビゲーション パネルの **[Build]\(ビルド\)** を選択します。 

3. 意図の一覧で **[None]** を選択します。

4. Ctrl + E キーを押して、発話のトークン ビューを表示します。 
    
    |None 意図の発話|予測スコア|
    |--|--|
    |"decrease temperature for me please"|0.44|
    |"dim kitchen lights to 25."|0.43|
    |"lower your volume"|0.46|
    |"turn on the internet in my bedroom please"|0.28|

## <a name="fix-none-intents-utterances"></a>None 意図の発話を修正する
    
`None` の発話は、いずれもアプリ ドメインの範囲外でなければなりません。 これらの発話は HomeAutomation に関連しているので、間違った意図に配置されています。 

また、LUIS はこれらの発話に対して 50% (<.50) 未満の予測スコアを返しています。 他の 2 つの意図の発話を見ると、予測スコアがはるかに高いことがわかります。 LUIS から返される発話の例のスコアが低いときは、現在の意図と他の意図との間で発話が LUIS を混乱させていることを示しています。 

アプリを修正するには、意図 `None` の現在の発話を正しい意図に移動する必要があります。また、意図 `None` には、新しい適切な意図が必要です。 

意図 `None` の 3 つの発話は、オートメーション デバイスの設定を下げることを意図しています。 これらの発話では、`dim`、`lower`、`decrease` などの単語が使用されています。 4 番目の発話は、インターネットをオンにするよう求めています。 4 つの発話はすべて、デバイスの設定の有効化や変更に関するものであるため、意図 `HomeAutomation.TurnOn` に移動する必要があります。 

これは解決策の 1 つにすぎません。 `ChangeSetting` という新しい意図を作成し、dim、lower、decrease が使用されている発話をこの新しい意図に移動することもできます。 

## <a name="fix-the-app-based-on-batch-results"></a>バッチ結果に基づいてアプリを修正する
4 つの発話を意図 `HomeAutomation.TurnOn` に移動します。 

1. すべての発話が選択されるように、発話リストの上のチェック ボックスをオンにします。 

2. **[Reassign intent]\(意図の再割り当て\)** ドロップダウンで、`HomeAutomation.TurnOn` を選択します。 

    ![発話を移動する](./media/luis-tutorial-batch-testing/move-utterances.png)

    4 つの発話が再割り当てされると、意図 `None` の発話リストが空になります。

3. None 意図の 4 つの新しい意図を追加します。

    ```
    "fish"
    "dogs"
    "beer"
    "pizza"
    ```

    これらの発話は、HomeAutomation のドメインの範囲外であることは明らかです。 各発話を入力して、そのスコアを確認します。 スコアは低いまたは非常に低い (赤色の枠で囲まれます) 可能性があります。 手順 8. でアプリをトレーニングすると、スコアが大幅に上がります。 

7. 発話の青色のラベルを選択してラベルを削除し、**[ラベルの削除]** を選択します。

8. 右上のナビゲーション バーの **[Train]\(トレーニング\)** を選択します。 各発話のスコアが大幅に上がります。 意図 `None` のすべてのスコアが、.80 を上回っているはずです。 

## <a name="verify-the-fix-worked"></a>修正が機能していることを確認する
バッチ テストの発話が、意図 **None** について正しく予測されていることを確認するために、バッチ テストをもう一度実行します。

1. 上部のナビゲーション バーの **[Test]\(テスト\)** を選択します。 

2. 右側のパネルで、**[Batch testing panel]\(バッチ テスト パネル\)** を選択します。 

3. バッチ名の右側にある 3 つのドット (...) を選択し、**[Run Dataset]\(データセットの実行\)** を選択します。 バッチ テストが完了するまで待ちます。

    ![データセットを実行する](./media/luis-tutorial-batch-testing/run-dataset.png)

4. **[See results]\(結果の表示\)** を選択します。 どの意図も、意図名の左側に緑色のアイコンが表示されています。 意図 `HomeAutomation.Turnoff` に適切なフィルターを設定して、右上のパネル内の、グラフの中央に最も近い緑色の点を選択します。 グラフの下の表に、その発話の名前が表示されます。 `breezeway off please` のスコアが非常に低くなっています。 必要に応じて、このスコアを上げるために、さらに多くの発話を意図に追加します。 

    ![データセットを実行する](./media/luis-tutorial-batch-testing/turnoff-low-score.png)

<!--
    The Entities section of the legend may have errors. That is the next thing to fix.

## Create a batch to test entity detection
1. Create `homeauto-batch-2.json` in a text editor such as [VSCode](https://code.visualstudio.com/). 

2. Utterances have entities identified with `startPos` and `endPost`. These two elements identify the entity before [tokenization](luis-glossary.md#token), which happens in some [cultures](luis-supported-languages.md#tokenization) in LUIS. If you plan to batch test in a tokenized culture, learn how to [extract](luis-concept-data-extraction.md#tokenized-entity-returned) the non-tokenized entities.

    Copy the following JSON into the file:

    ```JSON
    [
        {
          "text": "lobby on please",
          "intent": "HomeAutomation.TurnOn",
          "entities": [
            {
              "entity": "HomeAutomation.Room",
              "startPos": 0,
              "endPos": 4
            }
          ]
        },
        {
          "text": "change temperature to seventy one degrees",
          "intent": "HomeAutomation.TurnOn",
          "entities": [
            {
              "entity": "HomeAutomation.Operation",
              "startPos": 7,
              "endPos": 17
            }
          ]
        },
        {
          "text": "where is my pizza",
          "intent": "None",
          "entities": []
        },
        {
          "text": "help",
          "intent": "None",
          "entities": []
        },
        {
          "text": "breezeway off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": [
            {
              "entity": "HomeAutomation.Room",
              "startPos": 0,
              "endPos": 9
            }
          ]
        },
        {
          "text": "coffee bar off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": [
            {
              "entity": "HomeAutomation.Device",
              "startPos": 0,
              "endPos": 10
            }
          ]
        }
      ]
    ```

3. Import the batch file, following the [same instructions](#run-the-batch) as the first import, and name the dataset `set 2`. Run the test.

## Possible entity errors
Since the intents in the right-side filter of the test panel still pass the test, this section focuses on correct entity identification. 

Entity testing is diferrent than intents. An utterance will have only one top scoring intent, but it may have several entities. An utterance's entity may be correctly identified, may be incorrectly identified as an entity other than the one in the batch test, may overlap with other entities, or not identified at all. 

## Review entity errors
1. Select `HomeAutomation.Device` in the filter panel. The chart changes to show a single false positive and several true negatives. 

2. Select the False positive section name. The utterance for this chart point is displayed below the chart. The labeled intent and the predicted intent are the same, which is consistent with the test -- the intent prediction is correct. 

    The issue is that the HomeAutomation.Device was detected but the batch expected HomeAutomation.Room for the utterance "coffee bar off please". `Coffee bar` could be a room or a device, depending on the environment and context. As the model designer, you can either enforce the selection as `HomeAutomation.Room` or change the batch file to use `HomeAutomation.Device`. 

    If you want to reinforce that coffee bar is a room, you nee to add an utterances to LUIS that help LUIS decide a coffee bar is a room. 

    The most direct route is to add the utterance to the intent but that to add the utterance for every entity detection error is not the machine-learned solution. Another fix would be to add an utterance with `coffee bar`.

## Add utterance to help extract entity
1. Select the **Test** button on the top navigation to close the batch test panel.

2. On the `HomeAutomation.TurnOn` intent, add the utterance, `turn coffee bar on please`. The uttterance should have all three entities detected after you select enter. 

3. Select **Train** on the top navigation panel. Wait until training completes successfully.

3. Select **Test** on the top navigation panel to open the Batch testing pane again. 

4. If the list of datasets is not visible, select **Back to list**. Select the three dots (...) at the end of `Set 2` and select `Run Dataset`. Wait for the test to complete.

5. Select **See results** to review the test results.

6. 
-->
## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [発話の例の詳細](luis-how-to-add-example-utterances.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions