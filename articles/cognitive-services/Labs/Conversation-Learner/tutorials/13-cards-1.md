---
title: Conversation Learner アプリケーションでカードを使用する方法 (パート 1) - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner アプリケーションでカードを使用する方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: e90ccd42b21eea6139c402937be7e20513d73c84
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376205"
---
# <a name="how-to-use-cards-part-1-of-2"></a>カードを使用する方法 (パート 1/2)

このチュートリアルでは、ご利用のボットに単純なカードを追加して使用する方法について説明します。

Conversation Learner では、カード定義ファイルが、ボットの起動ディレクトリに存在する "cards" というディレクトリに格納されている必要があることに注意してください。

## <a name="requirements"></a>必要条件
このチュートリアルでは、general tutorial ボットが実行されている必要があります。

    npm run tutorial-general

## <a name="details"></a>詳細

カードは、ユーザーが会話の中でオプションを選択できる UI 要素です。 

### <a name="open-the-demo"></a>デモを開く

Web UI のアプリ一覧で、[Tutorial-13-Cards-1] をクリックします。 

### <a name="the-card"></a>カード

カードの定義は、C:\<installedpath\>\src\cards\prompt.json にあります。

この cards ディレクトリにカードの定義があることをシステムは想定しています。

![](../media/tutorial13_prompt.PNG)

- TextBlock と question テンプレートに注意してください。
- 2 つの提出ボタンがあり、さらに、それぞれについて提出されるテキストが存在します。

### <a name="actions"></a>アクション

アクションは 3 つ作成してあります。 次に示したように、1 つ目のアクションはカードです。

![](../media/tutorial13_actions.PNG)

カード アクション タイプがどのように作成されているかを見てみましょう。

![](../media/tutorial13_cardaction.PNG)

question の入力と button1 および button2 に注意してください。 これらはカード内のテンプレート参照であり、ここに質問とそれぞれの答えを入力することになります。 エンティティを参照して使用できるほか、テキストとエンティティの組み合わせを参照することもできます。

カードの体裁は、目のアイコンで表示することができます。

### <a name="train-dialog"></a>[Train Dialog]\(トレーニング会話\)

それでは実際に、学習させるための会話を見ていきましょう。

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
