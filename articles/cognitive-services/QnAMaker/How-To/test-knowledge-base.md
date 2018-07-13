---
title: ナレッジ ベースをテストする方法 - QnA Maker - Azure Cognitive Services | Microsoft Docs
description: ナレッジ ベースを発行する前にテストします。
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: cffb63666edab25e1b3b0739d0e0f2f828600f3a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376357"
---
# <a name="test-your-knowledge-base"></a>ナレッジ ベースのテスト

QnA Maker のナレッジ ベースをテストすることは、回答の正確性を高めるという反復的なプロセスの重要な要素です。 ナレッジ ベースのテストは、編集機能も備えた強化された会話インターフェイスを通じて行うことができます。

## <a name="test-answer-matching"></a>回答の適合性をテストする

1.  **[My knowledge bases]\(マイ ナレッジ ベース\)** ページで目的のナレッジ ベースの名前を選択して、ナレッジ ベースにアクセスします。
2.  スライド式の [テスト] パネルにアクセスするために、アプリケーションの上部パネルにある **[テスト]** を選択します。

    ![テキストへのアクセス](../media/qnamaker-how-to-test-kb/access-test.png)

3.  テキスト ボックスにクエリを入力して Enter キーを押します。

4.  ナレッジ ベースから、最も適合した回答が応答として返されます。

## <a name="clear-test-panel"></a>テスト パネルのクリア

入力したすべてのテスト クエリとその結果を テスト コンソールからクリアするには、[テスト] パネルの左上隅の **[やり直す]** を選択します。

## <a name="close-test-panel"></a>テスト パネルを閉じる

[テスト] パネルを閉じるには、**[テスト]** ボタンをもう一度選択します。 [テスト] パネルが開いている間は、ナレッジ ベースのコンテンツを編集できません。

## <a name="inspect-score"></a>スコアの検査

テスト結果の詳細は、[検査] パネルで調べることができます。

1.  回答の詳細を表示するには、スライド式の [テスト] パネルを開いた状態で **[検査]** を選択します。

    ![回答の検査](../media/qnamaker-how-to-test-kb/inspect.png)

2.  [検査] パネルが表示されます。 このパネルには、最もスコアの高い意図のほか、識別されたエンティティが含まれています。 このパネルには、選択された発話の結果が表示されます。

## <a name="correct-the-top-scoring-answer"></a>上位スコアの回答を修正する

最もスコアの高い回答に誤りがある場合は、正しい回答を一覧から選び、**[Save and Train]\(保存してトレーニング\)** を選択します。

![テキストへのアクセス](../media/qnamaker-how-to-test-kb/choose-answer.png)

## <a name="add-alternate-questions"></a>代わりの質問を追加する

ある特定の回答に対して、代わりの質問形式を追加することができます。 テキスト ボックスに代わりの回答を入力し、Enter キーを押してそれらを追加します。 **[Save and Train]\(保存してトレーニング\)** を選択して更新内容を保存します。

![テキストへのアクセス](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

## <a name="add-a-new-answer"></a>新しい回答の追加

一致した既存の回答に誤りがあった場合や、ナレッジ ベースに回答が存在しない (適切な一致が KB に見つからない) 場合は、新しい回答を追加することができます。 現在の質問のテキスト ボックスに新しい回答を入力し、Enter キーを押して追加します。 

その回答を保持するには、**[Save and Train]\(保存してトレーニング\)** を選択してください。 これでナレッジ ベースには、質問とその回答のペアが新たに追加されたことになります。

![テキストへのアクセス](../media/qnamaker-how-to-test-kb/add-answer.png)

> [!NOTE]
> ナレッジ ベースに対するすべての編集は、**[Save and Train]\(保存してトレーニング\)** ボタンを押したときにのみ保存されます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ナレッジ ベースの公開](./publish-knowledge-base.md)
