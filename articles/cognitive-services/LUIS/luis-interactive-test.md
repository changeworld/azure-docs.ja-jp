---
title: LUIS アプリのトレーニングとテスト - Azure | Microsoft Docs
description: Language Understanding (LUIS) を使用して、アプリケーションの改善とその言語解釈の向上に継続的に取り組みます。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: diberry
ms.openlocfilehash: 925b96b61de876d40105c89556fd6dcb600d1b1a
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223652"
---
# <a name="test-your-luis-app"></a>LUIS アプリのテスト
<a name="train-your-app"></a>
アプリの[トレーニング](luis-how-to-train.md)と[テスト](luis-concept-test.md)は反復処理です。 ご自身の LUIS アプリをトレーニングした後、サンプルの発話を使用して、意図とエンティティが正しく認識されるかどうかをテストします。 正しく認識されない場合は、もう一度 LUIS アプリを更新し、トレーニングおよびテストを行います。 

<!-- anchors for H2 name changes -->
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>
## <a name="test-an-utterance"></a>発話のテスト

1. **[My Apps]\(マイ アプリ\)** ページでご自身のアプリの名前を選択して、アプリにアクセスします。 

2. スライド式の **[Test]\(テスト\)** パネルにアクセスするには、アプリケーションの上部パネルにある **[Test]\(テスト\)** を選択します。

    ![アプリのトレーニングとテストのページ](./media/luis-how-to-interactive-test/test.png)

3. テキスト ボックスに発話を入力し、Enter キーを押します。 **[Test]\(テスト\)** にはテスト用の発話を必要な数だけ入力できますが、同時に入力できる発話は 1 つだけです。

4. 発話とその最上位の意図、およびスコアが、テキスト ボックスの下の発話の一覧に追加されています。

    ![対話型テストによる間違った意図の特定](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="clear-test-panel"></a>テスト パネルのクリア
入力したすべてのテスト用の発話とその結果をテスト コンソールからクリアするには、**[Test]\(テスト\)** パネルの左上隅の **[やり直す]** を選択します。 

## <a name="close-test-panel"></a>テスト パネルを閉じる
**[Test]\(テスト\)** パネルを閉じるには、**[Test]\(テスト\)** をもう一度選択します。

## <a name="inspect-score"></a>スコアの検査
テスト結果の詳細は、**[検査]** パネルで調べることができます。 
 
1. スライド式の **[Test]\(テスト\)** パネルを開いた状態で、比較する発話の **[検査]** を選択します。 

    ![[検査] ボタン](./media/luis-how-to-interactive-test/inspect.png)

2. **[検査]** パネルが表示されます。 このパネルには、最もスコアの高い意図のほか、特定されたエンティティが含まれています。 パネルには、選択された発話の結果が表示されます。

    ![[検査] ボタン](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="correct-top-scoring-intent"></a>上位スコアの意図の修正

1. 上部スコアの意図が間違っている場合は、**[編集]** をクリックします。

2.  ドロップダウン リストで、発話の正しい意図を選択します。

    ![正しい意図の選択](./media/luis-how-to-interactive-test/intent-select.png)

## <a name="view-sentiment-results"></a>センチメント結果の表示

**[[Publish]\(公開\)](luis-how-to-publish-app.md#enable-sentiment-analysis)** ページで**感情分析**が構成されている場合、テスト結果には、発話で見つかったセンチメントが含まれます。 

![感情分析を含む [Test]\(テスト\) ウィンドウの画像](./media/luis-how-to-interactive-test/sentiment.png)

## <a name="correct-matched-patterns-intent"></a>一致したパターンの意図の修正
[パターン](luis-concept-patterns.md)の使用中、発話がパターンに一致したが、予測された意図が間違っている場合は、パターンによる **[編集]** リンクを選択し、正しい意図を選択します。

## <a name="compare-with-published-version"></a>公開されたバージョンとの比較
公開された[エンドポイント](luis-glossary.md#endpoint) バージョンでアプリのアクティブなバージョンをテストできます。 **[検査]** パネルで、**[Compare with published]\(公開済みのものと比較\)** を選択します。 公開されたモデルに対するテストは、お使いの Azure サブスクリプションのクォータ残量から差し引かれます。 

![公開済みとの比較](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>テスト パネルでのエンドポイント JSON の表示
比較のために返されたエンドポイント JSON を表示するには、**[Show JSON view]\(JSON ビューの表示\)** を選択します。

![公開された JSON 応答](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

<!--Service name is 'Bing Spell Check v7 API' in the portal-->
## <a name="additional-settings-in-test-panel"></a>テスト パネルでの追加設定

### <a name="luis-endpoint"></a>LUIS エンドポイント
LUIS エンドポイントが複数ある場合は、テストの [公開済み] ウィンドウで **[追加設定]** リンクを使用して、テスト用に使用されているエンドポイントを変更します。 使用するエンドポイントがわからない場合は、既定の **Starter_Key** を選択します。 

![[追加設定] リンクが強調表示されているテスト パネル](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key.png)


### <a name="view-bing-spell-check-corrections-in-test-panel"></a>テスト パネルでの Bing Spell Check 修正の表示
スペルの修正を表示するための要件: 

* 公開済みアプリ
* Bing Spell Check [サービス キー](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)。 サービス キーは保存されないため、ブラウザー セッションごとにリセットする必要があります。 

次の手順を使用して、[Bing Spell Check v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) サービスを [Test]\(テスト\) ウィンドウの結果に追加します。 

1. **[Test]\(テスト\)** ウィンドウで、発話を入力します。 発話が予測されるときに、入力した発話の下で **[[検査]](#inspect-score)** を選択します。 

2. **[検査]** パネルが開いたら、**[[Compare with published]\(公開済みのものと比較\)](#compare-with-published-version)** を選択します。 

3. **[公開済み]** パネルが開いたら、**[[追加設定]](#additional-settings-in-test-panel)** を選択します。

4. ポップアップ ダイアログで、ご自身の **Bing Spell Check** サービス キーを入力します。 
    ![Bing Spell Check サービス キーの入力](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key.png)

5. `book flite to seattle` など、間違っているスペルを含むクエリを入力し、Enter キーを入力します。 LUIS に送信されたクエリ内で、スペルが間違っている単語 `flite` が置き換えられ、結果の JSON の `query` には元のクエリが、`alteredQuery` にはクエリ内の修正されたスペルが表示されます。

    ![修正されたスペルの JSON](./media/luis-how-to-interactive-test/interactive-with-spell-check-results.png)

<a name="json-file-with-no-duplicates"></a>
<a name="import-a-dataset-file-for-batch-testing"></a>
<a name="export-rename-delete-or-download-dataset"></a>
<a name="run-a-batch-test-on-your-trained-app"></a>
<a name="access-batch-test-result-details-in-a-visualized-view"></a>
<a name="filter-chart-results-by-intent-or-entity"></a>
<a name="investigate-false-sections"></a>
<a name="view single-point utterance data"></a>
<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>
## <a name="batch-testing"></a>バッチ テスト
バッチ テストの[概念](luis-concept-batch-test.md)と、発話のバッチをテストする[方法](luis-how-to-batch-test.md)を参照してください。

## <a name="next-steps"></a>次の手順

ご自身の LUIS アプリで正しい意図とエンティティが認識されないことがテストによって示されている場合、LUIS アプリの精度を向上させるには、発話にさらに多くのラベルを付けるか、機能を追加します。 

* [LUIS で推奨される発話にラベルを付ける](luis-how-to-review-endoint-utt.md) 
* [LUIS アプリのパフォーマンスを向上させる機能を使用する](luis-how-to-add-features.md) 
