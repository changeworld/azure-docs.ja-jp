---
title: LUIS ポータルでアプリをテストする
description: Language Understanding (LUIS) を使用して、アプリケーションの改善とその言語解釈の向上に継続的に取り組みます。
ms.topic: how-to
ms.date: 05/20/2020
ms.openlocfilehash: 86ee90e2d3bb322a4f55439d105941cf43462d3e
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/03/2020
ms.locfileid: "84344154"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>LUIS ポータルで LUIS アプリをテストする

アプリの[テスト](luis-concept-test.md)は反復処理です。 ご自身の LUIS アプリをトレーニングした後、サンプルの発話を使用して、意図とエンティティが正しく認識されるかどうかをテストします。 正しく認識されない場合は、もう一度 LUIS アプリを更新し、トレーニングおよびテストを行います。

<!-- anchors for H2 name changes -->
<a name="train-your-app"></a>
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="train-before-testing"></a>テストの前にトレーニングする

1. [LUIS ポータル](https://www.luis.ai)にサインインし、自分の**サブスクリプション**と**作成リソース**を選択して、その作成リソースに割り当てられているアプリを表示します。
1. **[マイ アプリ]** ページで自分のアプリの名前を選択して、そのアプリを開きます。
1. アクティブなアプリの最新バージョンに対してテストを行うには、テストの前に、上部のメニューから **[トレーニング]** を選択します。

## <a name="test-an-utterance"></a>発話のテスト

テストの発話は、アプリでの発話の例とまったく同じにすることはできません。 テストの発話には、ユーザーが使用すると予想される単語の選択、語句の長さ、エンティティの使用法を含める必要があります。

1. [LUIS ポータル](https://www.luis.ai)にサインインし、自分の**サブスクリプション**と**作成リソース**を選択して、その作成リソースに割り当てられているアプリを表示します。
1. **[マイ アプリ]** ページで自分のアプリの名前を選択して、そのアプリを開きます。

1. スライド式の **[Test]\(テスト\)** パネルにアクセスするには、アプリケーションの上部パネルにある **[Test]\(テスト\)** を選択します。

    > [!div class="mx-imgBorder"]
    > ![アプリのトレーニングとテストのページ](./media/luis-how-to-interactive-test/test.png)

1. テキスト ボックスに発話を入力し、Enter キーを押します。 **[Test]\(テスト\)** にはテスト用の発話を必要な数だけ入力できますが、同時に入力できる発話は 1 つだけです。

1. 発話とその最上位の意図、およびスコアが、テキスト ボックスの下の発話の一覧に追加されています。

    > [!div class="mx-imgBorder"]
    > ![対話型テストによる間違った意図の特定](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-the-prediction"></a>予測の検査

テスト結果の詳細は、 **[検査]** パネルで調べることができます。

1. スライド式の **[Test]\(テスト\)** パネルを開いた状態で、比較する発話の **[検査]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![テスト結果の詳細を確認するには、[検査] ボタンを選択します](./media/luis-how-to-interactive-test/inspect.png)

1. **[検査]** パネルが表示されます。 このパネルには、最もスコアの高い意図のほか、特定されたエンティティが含まれています。 パネルには、選択された発話の予測が表示されます。

    > [!div class="mx-imgBorder"]
    > ![[テスト] パネルと [検査] パネルの一部のスクリーンショット](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="add-to-example-utterances"></a>発話の例に追加する

[検査] パネルで、 **[Add to example utterances]\(発話の例に追加する\)** を選択して、テスト発話を意図に追加できます。

## <a name="disable-required-features"></a>必須の機能を無効にする

この切り替えをオンにすると、エンティティの機能が必須でなかった場合の予測の内容が表示されます。

この切り替えは、トレーニング済みアプリが必須の機能に基づいてエンティティを正しく予測しているかどうかを判断するのに役立ちます。 トレーニング済みアプリは、発話例のラベル付けが正しくないこと、または必須の機能がテキストと一致しないことのいずれかに基づいて、機械学習エンティティの予測に失敗することがあります。

## <a name="view-sentiment-results"></a>センチメント結果の表示

**[[Publish]\(公開\)](luis-how-to-publish-app.md#enable-sentiment-analysis)** ページで**感情分析**が構成されている場合、テスト結果には、発話で見つかったセンチメントが含まれます。

## <a name="correct-matched-patterns-intent"></a>一致したパターンの意図の修正

[パターン](luis-concept-patterns.md)の使用中、発話がパターンに一致したが、予測された意図が間違っている場合は、パターンによる **[編集]** リンクを選択し、正しい意図を選択します。

## <a name="compare-with-published-version"></a>公開されたバージョンとの比較

公開された[エンドポイント](luis-glossary.md#endpoint) バージョンでアプリのアクティブなバージョンをテストできます。 **[検査]** パネルで、 **[Compare with published]\(公開済みのものと比較\)** を選択します。 公開されたモデルに対するテストは、お使いの Azure サブスクリプションのクォータ残量から差し引かれます。

> [!div class="mx-imgBorder"]
> ![公開済みとの比較](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>テスト パネルでのエンドポイント JSON の表示
比較のために返されたエンドポイント JSON を表示するには、 **[Show JSON view]\(JSON ビューの表示\)** を選択します。

> [!div class="mx-imgBorder"]
> ![公開された JSON 応答](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

## <a name="additional-settings-in-test-panel"></a>テスト パネルでの追加設定

### <a name="luis-endpoint"></a>LUIS エンドポイント

LUIS エンドポイントが複数ある場合は、テストの [公開済み] ウィンドウで **[追加設定]** リンクを使用して、テスト用に使用されているエンドポイントを変更します。 使用するエンドポイントがわからない場合は、既定の **Starter_Key** を選択します。

> [!div class="mx-imgBorder"]
> ![[追加設定] リンクが強調して示されているテスト パネル](media/luis-how-to-interactive-test/additional-settings-v3-settings.png)


## <a name="batch-testing"></a>バッチ テスト
バッチ テストの[概念](luis-concept-batch-test.md)と、発話のバッチをテストする[方法](luis-how-to-batch-test.md)を参照してください。

## <a name="next-steps"></a>次のステップ

ご自身の LUIS アプリで正しい意図とエンティティが認識されないことがテストによって示されている場合、LUIS アプリの精度を向上させるには、発話にさらに多くのラベルを付けるか、機能を追加します。

* [LUIS で推奨される発話にラベルを付ける](luis-how-to-review-endpoint-utterances.md)
* [LUIS アプリのパフォーマンスを向上させる機能を使用する](luis-how-to-add-features.md)
