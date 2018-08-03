---
title: Conversation Learner モデルでカードを使用する方法 (パート 1) - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner モデルでカードを使用する方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 988a2433f098f41bca4796299825293efd4de44b
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171137"
---
# <a name="how-to-use-cards-part-1-of-2"></a>カードを使用する方法 (パート 1/2)

このチュートリアルでは、ご利用のボットに単純なカードを追加して使用する方法について説明します。

> [!NOTE]
> Conversation Learner は現在、カード定義ファイルが、ボットが起動されるディレクトリ内に存在する "cards" という名前のディレクトリにあることを想定しています。 将来、これを構成可能にする予定です。

## <a name="video"></a>ビデオ

[![チュートリアル 13 のプレビュー](http://aka.ms/cl-tutorial-13-preview)](http://aka.ms/blis-tutorial-13)

## <a name="requirements"></a>必要条件
このチュートリアルでは、general tutorial ボットが実行されている必要があります。

    npm run tutorial-general

## <a name="details"></a>詳細

カードは、ユーザーが会話の中でオプションを選択できる UI 要素です。 

### <a name="open-the-demo"></a>デモを開く

Web UI の [モデル] 一覧で、[Tutorial-13-Cards-1] をクリックします。 

### <a name="the-card"></a>カード

カードの定義は、C:\<installedpath\>\src\cards\prompt.json にあります。

この cards ディレクトリにカードの定義があることをシステムは想定しています。

![](../media/tutorial13_prompt.PNG)

> [!NOTE]
> テキスト フィールド内の本文の型 `TextBlock` と `{{question}}` プレースホルダーに注意してください。
> 2 つの提出ボタンがあり、さらに、それぞれについて提出されるテキストが存在します。

### <a name="actions"></a>アクション

アクションは 3 つ作成してあります。 次に示したように、1 つ目のアクションはカードです。

![](../media/tutorial13_actions.PNG)

カード アクション タイプがどのように作成されているかを見てみましょう。

![](../media/tutorial13_cardaction.PNG)

> [!NOTE]
> 質問の入力、およびボタン 1 と 2。 これらはカード内のテンプレート参照であり、ここに質問とそれぞれの答えを入力することになります。 エンティティを参照して使用できるほか、テキストとエンティティの組み合わせを参照することもできます。

カードの体裁は、目のアイコンで表示することができます。

### <a name="train-dialog"></a>[Train Dialog]\(トレーニング会話\)

それでは、学習させるための会話を見ていきましょう。

1. [Train Dialogs]\(トレーニング会話\) をクリックし、[New Train Dialog]\(新しいトレーニング会話\) をクリックします。
1. 「hi」と入力します。
2. [Score Action]\(アクションのスコア付け\) をクリックします。
3. [Prompt go left or right] をクリックして選択します。
    - [left] または [right] をクリックすることは、それぞれ、ユーザーが「left」または「right」と入力することに相当します。 
4. [Score Action]\(アクションのスコア付け\) をクリックします。
4. [left] をクリックして選択します。 これは非待機アクションです。
6. [Prompt go left or right] をクリックして選択します。
4. [right] をクリックします。
5. [Score Action]\(アクションのスコア付け\) をクリックします。
3. [Right] をクリックして選択します。
6. [Prompt go left or right] をクリックして選択します。
4. [Done Testing]\(テストの完了\) をクリックします。

以上、カードの動作を見てきました。 これらは、cards ディレクトリに json テンプレートとして定義されています。 テンプレートは UI に反映され、その UI から、文字列やエンティティ、またはその両方の組み合わせを使用してデータを事前設定することができます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [カード (パート 2)](./14-cards-2.md)
