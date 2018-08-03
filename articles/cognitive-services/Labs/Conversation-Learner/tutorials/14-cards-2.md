---
title: Conversation Learner モデルでカードを使用する方法 (パート 2) - Microsoft Cognitive Services | Microsoft Docs
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
ms.openlocfilehash: 1c7c88742c69041594006add76f7e3c642c64dec
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39170574"
---
# <a name="how-to-use-cards-part-1-of-2"></a>カードを使用する方法 (パート 1/2)
このチュートリアルでは、ご利用のボットに、記入式のフォーム カードを追加する方法について説明します。 フォームのフィールドが各エンティティに対してどのように移動するかを紹介します。

Conversation Learner では、カード定義ファイルが、ボットの起動ディレクトリに存在する "cards" というディレクトリに格納されている必要があります。

## <a name="video"></a>ビデオ

[![チュートリアル 14 のプレビュー](http://aka.ms/cl-tutorial-14-preview)](http://aka.ms/blis-tutorial-14)

## <a name="requirements"></a>必要条件
このチュートリアルでは、general tutorial ボットが実行されている必要があります。

    npm run tutorial-general

## <a name="details"></a>詳細

カードは、ユーザーが会話の中でオプションを選択できる UI 要素です。 

### <a name="open-the-demo"></a>デモを開く

Web UI の [モデル] 一覧で、[Tutorial-14-Cards-2] をクリックします。 

### <a name="the-card"></a>カード

カードの定義は、C:\<installedpath\>\src\cards\shippingAddress.json にあります。

配送先住所の 3 つのフィールド (City、Street、State) をこのカードで収集します。

![](../media/tutorial14_card.PNG)

### <a name="actions"></a>アクション

アクションは 3 つ作成してあります。 次に示したように、1 つ目のアクションはカードです。

![](../media/tutorial14_actions.PNG)

カード アクション タイプがどのように作成されているかを見てみましょう。

- Address-Street (タイプは Input.text) とその ID に注目してください。
- 同様に、Address-City があり、さらに、Address-State という ID のドロップダウンがあります。

フィールドにデータが入力されて送信されるとき、それらの値をボットで受け取る際のエンティティ名になるため、これらの ID は重要です。

## <a name="entities"></a>エンティティ
前出のカードに対応するエンティティを 3 つ定義してあります。

![](../media/tutorial14_entities.PNG)

## <a name="actions"></a>アクション

アクションは 2 つ定義しました。

![](../media/tutorial14_actions.PNG)

- 1 つは、アクション タイプが CARD である配送先住所カードです。shippingAddress として、ドロップダウンからテンプレートが選択されています。
- もう 1 つは、配送先住所を復唱する単純なアクションです。

![](../media/tutorial14_sa_card.PNG)

### <a name="train-dialog"></a>[Train Dialog]\(トレーニング会話\)

それでは、学習させるための会話を見ていきましょう。

1. [Train Dialogs]\(トレーニング会話\) をクリックし、[New Train Dialog]\(新しいトレーニング会話\) をクリックします。
1. 「hi」と入力します。
2. [Score Action]\(アクションのスコア付け\) をクリックします。
3. [Shipping Address] をクリックして選択します。
4. カードに入力して提出します。
    - それらの値がエンティティのメモリに移動されていることに注目してください。 入力データはフォームによって既に分割されているため、解析は必要ありません。
5. [Score Action]\(アクションのスコア付け\) をクリックします。
3. [Shipping to $Address...] をクリックして選択します。
4. [Done Testing]\(テストの完了\) をクリックします。

![](../media/tutorial14_train_dialog.PNG)

記入式のフィールドとドロップダウンを含んだカードから値を取得する方法や、それらをボットのエンティティでキャプチャして収集する方法について説明しました。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ブランチと "元に戻す"](./15-branching-and-undo.md)
