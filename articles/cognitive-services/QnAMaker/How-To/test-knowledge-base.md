---
title: ナレッジ ベースをテストする方法 - QnA Maker
description: QnA Maker のナレッジ ベースをテストすることは、回答の正確性を高めるという反復的なプロセスの重要な要素です。 ナレッジ ベースのテストは、編集機能も備えた強化された会話インターフェイスを通じて行うことができます。
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: da4988ced0b077952ce64e6227d16e58d40ae329
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927271"
---
# <a name="test-your-knowledge-base-in-qna-maker"></a>QnA Maker のナレッジ ベースをテストする

QnA Maker のナレッジ ベースをテストすることは、回答の正確性を高めるという反復的なプロセスの重要な要素です。 ナレッジ ベースのテストは、編集機能も備えた強化された会話インターフェイスを通じて行うことができます。

## <a name="interactively-test-in-qna-maker-portal"></a>QnA Maker ポータルで対話形式でテストする

1. **[My knowledge bases]\(マイ ナレッジ ベース\)** ページで目的のナレッジ ベースの名前を選択して、ナレッジ ベースにアクセスします。
1. スライド式の [テスト] パネルにアクセスするために、アプリケーションの上部パネルにある **[テスト]** を選択します。
1. テキスト ボックスにクエリを入力して Enter キーを押します。
1. ナレッジ ベースから、最も適合した回答が応答として返されます。

### <a name="clear-test-panel"></a>テスト パネルのクリア

入力したすべてのテスト クエリとその結果を テスト コンソールからクリアするには、[テスト] パネルの左上隅の **[やり直す]** を選択します。

### <a name="close-test-panel"></a>テスト パネルを閉じる

[テスト] パネルを閉じるには、 **[テスト]** ボタンをもう一度選択します。 [テスト] パネルが開いている間は、ナレッジ ベースのコンテンツを編集できません。

### <a name="inspect-score"></a>スコアの検査

テスト結果の詳細は、[検査] パネルで調べることができます。

1.  回答の詳細を表示するには、スライド式の [テスト] パネルを開いた状態で **[検査]** を選択します。

    ![回答の検査](../media/qnamaker-how-to-test-kb/inspect.png)

2.  [検査] パネルが表示されます。 このパネルには、最もスコアの高い意図のほか、特定されたエンティティが含まれています。 パネルには、選択された発話の結果が表示されます。

### <a name="correct-the-top-scoring-answer"></a>上位スコアの回答を修正する

最もスコアの高い回答に誤りがある場合は、正しい回答を一覧から選び、 **[Save and Train]\(保存してトレーニング\)** を選択します。

![上位スコアの回答を修正する](../media/qnamaker-how-to-test-kb/choose-answer.png)

### <a name="add-alternate-questions"></a>代わりの質問を追加する

ある特定の回答に対して、代わりの質問形式を追加することができます。 テキスト ボックスに代わりの回答を入力し、Enter キーを押してそれらを追加します。 **[Save and Train]\(保存してトレーニング\)** を選択して更新内容を保存します。

![代わりの質問を追加する](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

### <a name="add-a-new-answer"></a>新しい回答の追加

一致した既存の回答に誤りがあった場合や、ナレッジ ベースに回答が存在しない (適切な一致が KB に見つからない) 場合は、新しい回答を追加することができます。

回答一覧の一番下で、テキスト ボックスに新しい回答を入力し、Enter を押すと追加できます。

その回答を保持するには、 **[Save and Train]\(保存してトレーニング\)** を選択してください。 これでナレッジ ベースには、質問とその回答のペアが新たに追加されたことになります。

> [!NOTE]
> ナレッジ ベースに対するすべての編集は、 **[Save and Train]\(保存してトレーニング\)** ボタンを押したときにのみ保存されます。

### <a name="test-the-published-knowledge-base"></a>公開ナレッジ ベースをテストする

ナレッジ ベースの公開バージョンをテスト ウィンドウでテストできます。 KB を公開したら、 **[Published KB]\(公開済み KB\)** ボックスを選択し、公開済み KB から結果を取得するためのクエリを送信します。

![公開済み KB のテスト](../media/qnamaker-how-to-test-kb/test-against-published-kb.png)

## <a name="batch-test-with-tool"></a>ツールを使用したバッチ テスト

次のことを行う場合は、バッチ テスト ツールを使用します。:

* 一連の質問に対する上位の回答とスコアを判定する
* 一連の質問に対する予測される回答を検証する

詳細な手順については、バッチ テストの[チュートリアル](../Quickstarts/batch-testing.md)を参照してください。

バッチ テストには、バッチ テスト ツールが用意されています。 このツールは、ダウンロード用の [zip ファイル化された実行可能ファイル](https://aka.ms/qnamakerbatchtestingtool)、または [C# ソース コード](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting)として入手できます。

[このツールに関するリファレンス ドキュメント](../reference-tsv-format-batch-testing.md)には、次のものがあります。

* ツールのコマンド ラインの例
* TSV 入力および出力ファイルの形式

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ナレッジ ベースの公開](./publish-knowledge-base.md)
