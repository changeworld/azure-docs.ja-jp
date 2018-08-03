---
title: LUIS アプリのバッチ テスト - Azure | Microsoft Docs
description: Language Understanding (LUIS) バッチ テストを使用して、不適切な意図とエンティティを含む発話を見つけます。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/14/2018
ms.author: diberry
ms.openlocfilehash: 2c648cdd82f89a9646fa0b311a7f1f68dd4bc4a9
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223583"
---
# <a name="batch-testing"></a>バッチ テスト
 バッチ テストは、ご自身の現在のトレーニング済みモデルで実行する包括的なテストで、LUIS におけるそのモデルのパフォーマンスを測定します。 

<a name="batch-testing"></a>
## <a name="import-a-dataset-file-for-batch-testing"></a>バッチ テスト用のデータセット ファイルのインポート

1. 上部のバーで **[テスト]** を選択し、**[Batch testing panel]\(バッチ テスト パネル\)** を選択します。

    ![バッチ テストのリンク](./media/luis-how-to-batch-test/batch-testing-link.png)

2. **[Import dataset]\(データセットのインポート\)** を選択します。 **[Import new dataset]\(新しいデータセットのインポート\)** ダイアログ ボックスが表示されます。 **[ファイルの選択]** を選択し、テスト対象の *1,000 個以下の*発話を含む、正しい [JSON 形式](luis-concept-batch-test.md#batch-file-format)を持つ JSON ファイルを見つけます。

    ![データセット ファイルのインポート](./media/luis-how-to-batch-test/batchtest-importset.png)

    インポート エラーは、ブラウザーの上部にある赤い通知バーで報告されます。 インポートでエラーが発生した場合、データセットは作成されません。 詳細については、[一般的なエラー](luis-concept-batch-test.md#common-errors-importing-a-batch)に関するトピックをご覧ください。

3. **[データセット名]** フィールドに、データセット ファイルの名前を入力します。 データセット ファイルには、"*ラベル付きの意図*" および "*エンティティ*" を含む**発話の配列**が含まれています。 [バッチ ファイルの例](luis-concept-batch-test.md#batch-file-format)で構文を確認します。 

4. **[完了]** を選択します。 データセット ファイルが追加されます。

## <a name="run-rename-export-or-delete-dataset"></a>データセットの実行、名前変更、エクスポート、または削除
データセットの実行、名前の変更、エクスポート、または削除を行うには、データセット行の末尾にある省略記号 (***...***) ボタンを使用します。

![データセットのアクション](./media/luis-how-to-batch-test/batch-testing-options.png)

## <a name="run-a-batch-test-on-your-trained-app"></a>トレーニング済みアプリでのバッチ テストの実行

テストを実行するには、データセット名を選択します。 テストが完了すると、この行にデータセットのテスト結果が表示されます。

![バッチ テストの結果](./media/luis-how-to-batch-test/run-test.png)

ダウンロード可能なデータセットは、バッチ テスト用にアップロードされたファイルと同じです。

|State|意味|
|--|--|
|![テストの成功を示す緑色の丸いアイコン](./media/luis-how-to-batch-test/batch-test-result-green.png)|すべての発話が成功しました。|
|![テストの失敗を示す赤い x アイコン](./media/luis-how-to-batch-test/batch-test-result-red.png)|1 つ以上の発話の意図が予測と一致しませんでした。|
|![テスト準備が完了していることを示すアイコン](./media/luis-how-to-batch-test/batch-test-result-blue.png)|いつでもテストを実行できます。|

<a name="access-batch-test-result-details-in-a-visualized-view"></a>
## <a name="view-batch-test-results"></a>バッチ テストの結果の表示 
バッチ テストの結果を確認するには、**[See results]\(結果の表示\)** を選択します。

![バッチ テストの結果](./media/luis-how-to-batch-test/run-test-results.png)

<!--
 Select the **See results** link that appears after you run the test. A scatter graph known as an error matrix displays. The data points represent the utterances in the dataset. 

Green points indicate correct prediction, and red ones indicate incorrect prediction.

The filtering panel on the right side of the screen displays a list of all intents and entities in the app, with a green point for intents/entities that were predicted correctly in all dataset utterances, and a red point for those items with errors. Also, for each intent/entity, you can see the number of correct predictions out of the total utterances.

-->


<a name="filter-chart-results-by-intent-or-entity"></a> ## グラフの結果のフィルター処理

特定の意図またはエンティティでグラフをフィルター処理するには、右側のフィルター処理パネルで意図またはエンティティを選択します。 グラフのデータ ポイントとその分布が、選択した内容に応じて更新されます。 
 
![視覚化されたバッチ テストの結果](./media/luis-how-to-batch-test/filter-by-entity.png) 

## <a name="view-single-point-utterance-data"></a>単一ポイントの発話データの表示
グラフで、データ ポイントの上にポインターを置くと、その予測の確実性を示すスコアが表示されます。 データ ポイントを選択すると、ページ下部にある発話一覧で、そのデータ ポイントに対応する発話を確認できます。 

![選択した発話](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>
## <a name="view-section-data"></a>セクション データの表示
4 つのセクションで構成されるグラフで、セクション名を選択します。たとえば、グラフの右上のセクションの **[False Positive]\(誤検知\)** を選択します。 そのセクションのすべての発話が、グラフの下に一覧表示されます。 

![セクションで選択した発話](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

前の画像では、発話 `switch on` に TurnAllOn 意図のラベルが付いていますが、None 意図の予測を受け取りました。 つまり、想定される予測を得るには、TurnAllOn 意図に発話の例がさらに必要であるということです。 

グラフ上で赤で示されている 2 つのセクションは、想定される予測と一致しなかった発話を示します。 これらのセクションが示す発話については、LUIS でのトレーニングがさらに必要であることを示します。 

グラフ上で緑色で示されている 2 つのセクションは、想定される予測と一致しました。

## <a name="next-steps"></a>次の手順

ご自身の LUIS アプリで正しい意図とエンティティが認識されないことがテストによって示されている場合、LUIS アプリのパフォーマンスを向上させるには、発話にさらに多くのラベルを付けるか、機能を追加します。 

* [LUIS で推奨される発話にラベルを付ける](luis-how-to-review-endoint-utt.md) 
* [LUIS アプリのパフォーマンスを向上させる機能を使用する](luis-how-to-add-features.md) 
* [このチュートリアルでのバッチ テストについて](luis-tutorial-batch-testing.md)
* [バッチ テストの概念について](luis-concept-batch-test.md)