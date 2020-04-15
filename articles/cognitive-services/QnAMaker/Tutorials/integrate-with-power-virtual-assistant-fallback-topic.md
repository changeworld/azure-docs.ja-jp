---
title: チュートリアル:Power Virtual Agent との統合 - QnA Maker
description: このチュートリアルでは、アクティブ ラーニングを使用してナレッジ ベースの質を向上させます。 既存の質問の削除や変更は行わずに、レビュー、承認、却下を行います。
ms.topic: tutorial
ms.date: 03/11/2020
ms.openlocfilehash: 283667c587e395a1d712f82f3385582b4c5c3227
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398633"
---
# <a name="tutorial-add-knowledge-base-to-power-virtual-agent"></a>チュートリアル:Power Virtual Agent にナレッジ ベースを追加する
ナレッジ ベースからの回答を提供するように [Power Virtual Agent](https://powervirtualagents.microsoft.com/) ボットを作成、拡張します。

**このチュートリアルで学習する内容は次のとおりです。**

<!-- green checkmark -->
> [!div class="checklist"]
> * Power Virtual Agent を作成する
> * システム フォールバック トピックを作成する
> * QnA Maker アクションを Power Automate フローとしてトピックに追加する
> * Power Automate ソリューションを作成する
> * Power Automate フローをソリューションに追加する
> * Power Virtual Agent を公開する
> * Power Virtual Agent をテストし、QnA Maker ナレッジ ベースから回答を受け取る

## <a name="integrate-a-power-virtual-agent-with-a-knowledge-base"></a>Power Virtual Agent とナレッジ ベースを統合する

[Power Virtual Agents](https://powervirtualagents.microsoft.com/) を利用すると、データ サイエンティストや開発者がいなくても、ガイド付きでコード不要のグラフィカル インターフェイスをチームで使用して、強力なボットを簡単に作成できます。

Power Virtual Agent は、アクションを実行してユーザーの質問に回答することを目的に、一連のトピック (サブジェクト領域) を使用して作成されます。 回答が見つからなかった場合は、システム フォールバックから回答を返すことができます。

トピックのアクションか、**システム フォールバック** トピックのパスからナレッジ ベースに質問を送信するようにエージェントを構成します。 どちらも同じアクション メカニズムを使用してナレッジ ベースに接続し、回答を返します。

## <a name="power-automate-connects-to-generateanswer-action"></a>Power Automate で GenerateAnswer アクションに接続する

エージェントをナレッジ ベースに接続するには、Power Automate を使用してアクションを作成します。 Power Automate にはプロセス **フロー**が用意されており、そのフローを通じて QnA Maker の GenerateAnswer API に接続します。

**フロー**は、設計して保存した後、Power Automate **ソリューション**から利用できるようになります。  GenerateAnswer フローをソリューションに追加した後、そのソリューションをエージェントでアクションとして使用します。

## <a name="process-steps-to-connect-an-agent-to-your-knowledge-base"></a>エージェントをナレッジ ベースに接続するためのプロセス ステップ

以下の手順は、Power Virtual Agent を QnA Maker ナレッジ ベースに接続するという目標との関連性がわかりやすいよう、概要として示されています。

Power Virtual Agent と QnA Maker を組み合わせて使用するための手順は次のとおりです。
* [QnA Maker](https://www.qnamaker.ai/) ポータルでの手順
    * ナレッジ ベースを作成して公開します。
    * ナレッジ ベースの詳細 (ナレッジ ベース ID、ランタイム エンドポイント キー、ランタイム エンドポイント ホストなど) をコピーします。
* [Power Virtual Agent](https://powerva.microsoft.com/) ポータルでの作業
    * エージェントのトピックを作成します。
    * (Power Automate フローに対して) アクションを呼び出します。
* [Power Automate](https://us.flow.microsoft.com/) ポータルでの作業
    * [QnA Maker の GenerateAnswer](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/) へのコネクタを使用してフローを作成します。
        * QnA Maker によって公開されたナレッジ ベース情報
            * ナレッジ ベース ID
            * QnA Maker リソース エンドポイント ホスト
            * QnA Maker リソース エンドポイント キー
        * 入力 - ユーザー クエリ
        * 出力 - ナレッジ ベースの回答
    * ソリューションを作成してフローを追加します。
* Power Virtual Agent に返します。
    * ソリューションの出力をトピックのメッセージとして選択します。

## <a name="create-and-publish-a-knowledge-base"></a>ナレッジ ベースの作成と公開

1. [クイックスタート](../Quickstarts/create-publish-knowledge-base.md)に従ってナレッジ ベースを作成します。 ボットを作成する最後のセクションは行わないでください。 このチュートリアルは、同クイックスタートの最後のセクションの代わりとなるものです。クイックスタートでは Bot Framework でボットを作成していますが、このチュートリアルでは、Power Virtual Agent を使用してボットを作成します。

    > [!div class="mx-imgBorder"]
    > ![[QnA Maker](https://www.qnamaker.ai/) ポータルの **[設定]** ページにある公開済みのナレッジ ベース設定を入力する。](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

    [Power Automate の手順](#create-power-automate-flow-to-connect-to-your-knowledge-base)で QnA Maker GenerateAnswer 接続を構成する際に、この情報が必要となります。

1. QnA Maker ポータルの **[設定]** ページでエンドポイント キー、エンドポイント ホスト、ナレッジ ベース ID を見つけます。

## <a name="create-power-virtual-agent"></a>Power Virtual Agent を作成する

1. 学校または職場の電子メール アカウントで Power Virtual Agent に[サインイン](https://go.microsoft.com/fwlink/?LinkId=2108000&clcid=0x409)します。
1. これが初めてのボットである場合、エージェントの **[ホーム]** ページが表示されます。 これが初めての Power Virtual Agent ではない場合、右上のナビゲーションからボットを選択し、 **[+ New Bot]\(+ 新しいボット\)** を選択します。

    > [!div class="mx-imgBorder"]
    > ![[QnA Maker](https://www.qnamaker.ai/) ポータルの **[設定]** ページにある公開済みのナレッジ ベース設定を入力する。](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-home.png)

## <a name="several-topics-are-provided-in-the-bot"></a>ボットにはいくつかのトピックが用意されている

エージェントは、トピック コレクションを使用して、サブジェクト領域の質問に回答します。 このチュートリアルでは、エージェントにさまざまなトピックが最初から用意されていて、それらが**ユーザー トピック**と**システム トピック**とに分かれています。

> [!div class="mx-imgBorder"]
> ![エージェントは、トピック コレクションを使用して、サブジェクト領域の質問に回答します。 このチュートリアルでは、エージェントにさまざまなトピックが最初から用意されていて、それらが**ユーザー トピック**と**システム トピック**とに分かれています。](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topics-provided.png)


## <a name="create-power-virtual-agents-system-fallback-topic"></a>Power Virtual Agent のシステム フォールバック トピックを作成する

このエージェントは、あらゆるトピックのナレッジ ベースに接続できますが、このチュートリアルではシステム **フォールバック** トピックを使用します。 フォールバック トピックは、エージェントが回答を見つけられなかった場合に使用されます。 エージェントは、QnA Maker の GenerateAnswer API にユーザーからのテキストを渡し、ナレッジ ベースから回答を受け取って、それをメッセージとしてユーザーに返して表示します。

1. [Power Virtual Agents](https://powerva.microsoft.com/#/) ポータルで、ナビゲーションの右上隅にある **[設定]** ページを選択します。 このページでは、歯車のアイコンです。 **[System Fallback]\(システム フォールバック\)** を選択します。

    > [!div class="mx-imgBorder"]
    > ![Power Virtual Agent のシステム フォールバックのメニュー項目](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-system-fallback.png)

1. ポップアップの **[設定]** ウィンドウで、 **[+ 追加]** を選択してシステム フォールバック トピックを追加します。

    > [!div class="mx-imgBorder"]
    > ![[設定] ウィンドウでフォールバック トピックを追加する。](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-add-fallback-topic.png)

1. トピックの追加後、 **[Go to Fallback topic]\(フォールバック トピックに移動\)** を選択し、作成キャンバスでフォールバック トピックを作成します。

    > [!TIP]
    > フォールバック トピックに戻る必要がある場合は、 **[トピック]** セクションの **[システム]** トピックからアクセスできます。

## <a name="use-authoring-canvas-to-add-an-action"></a>作成キャンバスを使用してアクションを追加する

Power Virtual Agents の作成キャンバスを使用して、フォールバック トピックをナレッジ ベースに接続します。 トピックは、**認識されていないユーザー テキスト**から始まります。 そのテキストを QnA Maker に渡して、回答をメッセージとして表示するアクションを追加します。 回答を表示する最後のステップは、このチュートリアルで後ほど[別個のステップ](#add-solutions-flow-to-power-virtual-agent)として取り扱います。

このセクションでは、フォールバック トピックの会話フローを作成します。

1. 新しいフォールバック アクションには、あらかじめ会話フローの要素が含まれていることがあります。 [Options]\(オプション\) メニューを選択して、 **[Escalate]\(エスカレーション\)** 項目を削除してください。

    > [!div class="mx-imgBorder"]
    > ![トリガー語句を使用してフォールバック アクションを開始する](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-fallback-topic-delete-escalate.png)

1. **[Message]\(メッセージ\)** ボックスから出ている **+** コネクタを選択し、 **[Call an action]\(アクションの呼び出し\)** を選択します。

    > [!div class="mx-imgBorder"]
    > ![アクションの呼び出し](../media/how-to-integrate-power-virtual-agent/create-new-item-call-an-action.png)

1. **[Create a flow]\(フローの作成\)** を選択します。 このプロセスから先は、ブラウザーベースの別のポータルである **Power Automate** を使います。

    > [!div class="mx-imgBorder"]
    > ![アクションの呼び出し](../media/how-to-integrate-power-virtual-agent/create-a-flow.png)

## <a name="create-power-automate-flow-to-connect-to-your-knowledge-base"></a>ナレッジ ベースに接続するための Power Automate フローを作成する

以下の手順では、次のことを行う **Power Automate** フローを作成します。
* 届いたユーザー テキストを受け取る
* それを QnA Maker に送信する
* QnA Maker からの上位の回答を変数に代入する
* その変数 (上位の回答) を応答としてエージェントに返す

1. **Power Automate** で、**フロー テンプレート**が自動的に開始されます。 **[Power Virtual Agents]** フロー項目の **[Edit]\(編集\)** を選択して、エージェントからナレッジ ベースへの入力変数を構成します。 このテキストベースの入力変数は、エージェントを介してユーザーから送信されるテキスト形式の質問です。

    > [!div class="mx-imgBorder"]
    > ![入力変数をテキスト文字列として構成する](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable.png)

1. テキスト入力を追加し、変数の名前と説明をそれぞれ `InputText` および `IncomingUserQuestion` に設定します。 この名前付けにより、後で作成する出力テキストを入力テキストと見分けやすくなります。

    > [!div class="mx-imgBorder"]
    > ![テキスト入力を追加し、変数の名前と説明をそれぞれ `InputText` および `UserQuestion` に設定する](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable-name-and-description.png)

1. **[Power Virtual Agents]** ボックスから出ている **+** コネクタを選択してフロー ( **[Return value(s) to Power Virtual Agent]\(Power Virtual Agent に値を返す\)** の前) に新しいステップを挿入し、 **[Add an action]\(アクションの追加\)** を選択します。

1. `Qna` を検索して **QnA Maker** アクションを見つけ、 **[Generate answer]\(回答の生成\)** を選択します。

    > [!div class="mx-imgBorder"]
    > ![`Qna` を検索して **QnA Maker** アクションを見つけ、**[Generate answer]\(回答の生成\)** を選択する](../media/how-to-integrate-power-virtual-agent/generate-answer-action-selected.png)

    QnA Maker に関して必要な 3 つの接続設定と Power Virtual Agent からの質問設定がアクションに表示されます。

    > [!div class="mx-imgBorder"]
    > ![QnA Maker の接続設定がアクションに表示される。](../media/how-to-integrate-power-virtual-agent/generate-answer-knowledge-base-settings.png)

1. ナレッジ ベース ID、エンドポイント ホスト、エンドポイント キーを使用してアクションを構成します。 これらの値は、QnA Maker ポータルにあるナレッジ ベースの **[Settings]\(設定\)** ページで確認できます。

    > [!div class="mx-imgBorder"]
    > ![[QnA Maker](https://www.qnamaker.ai/) ポータルの **[設定]** ページにある公開済みのナレッジ ベース設定を入力する。](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

1. **[Question]\(質問\)** を構成するために、テキスト ボックスを選択して一覧から `InputText` を選択します。

1. フローに新しいステップを挿入するために、 **[Generate answer]\(回答の生成\)** アクション ボックスから出ている **+** コネクタを選択し、 **[Add an action]\(アクションの追加\)** を選択します。

1. GenerateAnswer から返された回答テキストを取り込むための変数を追加するために、`Initialize variable` アクションを探して選択します。

    変数の名前を `OutgoingQnAAnswer` に設定し、型に **[String]\(文字列\)** を選択します。 **[Value]\(値\)** は設定しないでください。

    > [!div class="mx-imgBorder"]
    > ![変数の名前を `QnAAnswer` に設定し、型に **[String]\(文字列\)** を選択する](../media/how-to-integrate-power-virtual-agent/initialize-output-variable-for-qna-answer.png)

1. フローに新しいステップを挿入するために、 **[Initialize variable]\(変数の初期化\)** アクション ボックスから出ている **+** コネクタを選択し、 **[Add an action]\(アクションの追加\)** を選択します。

1. ナレッジ ベースの JSON 応答全体を変数に設定するために、`Apply to each` アクションを探して選択します。 GenerateAnswer `answers` を選択します。

1. 上位の回答のみを返すために、同じ **[Apply to each]\(個々に適用\)** ボックスで **[Add an action]\(アクションの追加\)** を選択します。 **[Set variable]\(変数の設定\)** を探して選択します。

    **[Set variable]\(変数の設定\)** ボックスで **[Name]\(名前\)** のテキスト ボックスを選択し、一覧から **[OutgoingQnAAnswer]** を選択します。

    **[Value]\(値\)** のテキスト ボックスを選択し、一覧から **[Answers Answer]\(回答\)** を選択します。

    > [!div class="mx-imgBorder"]
    > ![変数の名前と値を設定する](../media/how-to-integrate-power-virtual-agent/power-automate-flow-apply-to-each-set-variable.png)

1. 変数 (とその値) を返すために、 **[Return value(s) to Power Virtual Agent]\(Power Virtual Agent に値を返す\)** フロー項目を選択し、 **[Edit]\(編集\)** を選択して、 **[Add an output]\(出力の追加\)** を選択します。 出力のタイプとして **[Text]\(テキスト\)** を選択し、 **[Title]\(タイトル\)** として「`FinalAnswer`」を入力します。 **[Value]\(値\)** のテキスト ボックスを選択し、`OutgoingQnAAnswer` 変数を選択します。

    > [!div class="mx-imgBorder"]
    > ![戻り値を設定する](../media/how-to-integrate-power-virtual-agent/power-automate-flow-return-value.png)

1. **[保存]** を選択してフローを保存します。

## <a name="create-solution-and-add-flow"></a>ソリューションを作成してフローを追加する

Power Virtual Agent からフローを探して接続するためには、そのフローが Power Automate ソリューションに含まれている必要があります。

1. 引き続き Power Automate ポータルで、左側のナビゲーションから **[Solutions]\(ソリューション\)** を選択します。

1. **[+ New solution]\(+ 新しいソリューション\)** を選択します。

1. 表示名を入力します。 ソリューションの一覧には、組織または学校のすべてのソリューションが含まれています。 ソリューション名の先頭にメールを追加するなど、自分のソリューションのみをフィルターで抽出しやすいような名前付け規則を選んでください (例: `jondoe-power-virtual-agent-qnamaker-fallback`)。

1. 選択肢の一覧から公開元を選択します。

1. 名前とバージョンには、既定値をそのまま使用します。

1. **[Create]\(作成\)** を選択して、このプロセスを完了します。

## <a name="add-flow-to-solution"></a>ソリューションにフローを追加する

1. ソリューションの一覧から、先ほど作成したソリューションを選択します。 リストの一番上に表示されていると思います。 見つからない場合は、ソリューション名の一部になっている電子メール名で検索してください。

1. ソリューションで **[+ Add existing]\(+ 既存を追加\)** を選択し、一覧から **[Flow]\(フロー\)** を選択します。

1. フローを探し、 **[Add]\(追加\)** を選択して、このプロセスを完了します。 多数のフローがある場合は、 **[Modified]\(変更\)** 列を見て最新のフローを探してください。

## <a name="add-solutions-flow-to-power-virtual-agent"></a>ソリューションのフローを Power Virtual Agent に追加する

1. Power Virtual Agent が表示されているブラウザー タブに戻ります。 作成キャンバスがまだ開いていると思います。

1. **[Message]\(メッセージ\)** アクション ボックスの下にある **+** コネクタを選択して、フローに新しいステップを挿入し、 **[Call an action]\(アクションの呼び出し\)** を選択します。

1. 新しいアクションで、**UnrecognizedTriggerPhrase** という入力値を選択します。 これによって、エージェントからのテキストがフローに渡されます。

    > [!div class="mx-imgBorder"]
    > ![新しいアクションで、**UnrecognizedTriggerPhrase** という入力値を選択する。](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-select-unrecognized-trigger-phrase.png)

1. **[Action]\(アクション\)** ボックスの下にある **+** コネクタを選択して、新しいステップをフローに挿入し、 **[Show a message]\(メッセージの表示\)** を選択します。

1. メッセージ テキスト「`Your answer is:`」を入力し、インプレース ツール バーの関数を使用して、コンテキスト変数に `FinalAnswer` を選択します。

    > [!div class="mx-imgBorder"]
    > ![メッセージ テキストと Power Automate フローからの `FinalAnswer` を入力する。](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-show-message-final-answer.png)

1. コンテキスト ツール バーの **[Save]\(保存\)** を選択して、このトピックに関する作成キャンバスの詳細を保存します。

最終的なキャンバスは次のとおりです。

> [!div class="mx-imgBorder"]
> ![最終的なエージェント キャンバス](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-full-flow.png)

## <a name="test-power-virtual-agent"></a>Power Virtual Agent をテストする

1. テスト ペインで、 **[Track between topics]\(トピック間を追跡する\)** を切り替えます。 この機能により、個々のトピックだけでなくトピック間の推移を観察できます。

1. 以下の記載順にユーザー テキストを入力してエージェントをテストします。 成功したステップは、作成キャンバスに緑色のチェック マークでレポートされます。

|質問の順序|テスト質問|目的|
|--|--|--|
|1|こんにちは|会話を開始します。|
|2|Store hours|サンプル トピック (特に何もしなくても自動的に構成されます)。|
|3|はい|`Did that answer your question?` への応答。|
|4|[非常に良い]|`Please rate your experience.` への応答。|
|5|はい|`Can I help with anything else?` への応答。|
|6|ナレッジ ベースとは|この質問によって、フォールバック アクションがトリガーされます。回答するテキストをフォールバック アクションがナレッジ ベースに送信した後、その回答が表示されます。 |

> [!div class="mx-imgBorder"]
> ![最終的なエージェント キャンバス](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-test-tracked.png)

## <a name="publish-your-bot"></a>ボットを公開する

学校または組織のすべてのメンバーがエージェントを利用できるようにするためには、そのエージェントを公開する必要があります。

1. 左側のナビゲーションから **[Publish]\(公開\)** を選択し、ページの **[Publish]\(公開\)** を選択します。

1. **[Publish]\(公開\)** ボタンの下にリンクとして提供されているデモ Web サイトでボットを試します。

    新しい Web ページがボットと共に表示されます。 `What is a knowledge base?` という同じテスト質問をボットにたずねます。

    > [!div class="mx-imgBorder"]
    > ![最終的なエージェント キャンバス](../media/how-to-integrate-power-virtual-agent/demo-chat-bot.png)

## <a name="share-your-bot"></a>ボットを共有する

デモ Web サイトを共有するためには、そのサイトをチャネルとして構成します。

1. 左側のナビゲーションから **[Manage]\(管理\)** 、 **[Channels]\(チャネル\)** の順に選択します。

1. チャネル一覧から **[Demo website]\(デモ Web サイト\)** を選択します。

1. リンクをコピーし、 **[Save]\(保存\)** を選択します。 学校または組織のメンバー宛てのメールにデモ Web サイトへのリンクを貼り付けます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

ナレッジ ベースが完成したら、QnA Maker リソースを Azure portal から削除してください。

## <a name="next-step"></a>次のステップ

[ナレッジ ベースに関する分析結果の取得](../How-To/get-analytics-knowledge-base.md)

各項目の詳細情報
* [Power Virtual Agents](https://docs.microsoft.com/power-virtual-agents/)
* [Power Automate](https://docs.microsoft.com/power-automate/)
* [QnA Maker コネクタ](https://us.flow.microsoft.com/connectors/shared_cognitiveservicesqnamaker/qna-maker/)と[コネクタの設定](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/)