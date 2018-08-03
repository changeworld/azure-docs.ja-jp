---
title: Conversation Learner で代替入力を使用する方法 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner で代替入力を使用する方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 8d3b3f419ceacbb9a6fe2b19cf68ea6873de536f
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171019"
---
# <a name="how-to-use-alternative-inputs"></a>代替入力を使用する方法

このチュートリアルでは、学習インターフェイスでユーザー入力の "代替入力" フィールドを使用する方法について説明します。

## <a name="video"></a>ビデオ

[![チュートリアル 8 のプレビュー](http://aka.ms/cl-tutorial-08-preview)](http://aka.ms/blis-tutorial-08)

## <a name="requirements"></a>必要条件
このチュートリアルでは、general tutorial ボットが実行されている必要があります。

    npm run tutorial-general

## <a name="details"></a>詳細
"代替入力" とは、トレーニング会話の特定の時点でユーザーが別の表現を使ったとしたらどう言うか、つまり代わりに使用できるユーザー発話のことです。 ユーザーが発する可能性のある言葉のバリエーションは、それぞれを別個のトレーニング会話で列挙しなくても、代替入力を使って、より簡潔に指定することができます。

## <a name="steps"></a>手順

### <a name="create-the-model"></a>モデルの作成

1. Web UI で、[新しいモデル] をクリックします。
2. [名前] に「AlternativeInputs」と入力します。 続けて [作成] をクリックします。

### <a name="create-an-entity"></a>エンティティの作成

1. [エンティティ] をクリックし、[新しいエンティティ] をクリックします。
2. [エンティティ名] に「city」と入力します。
3. [作成] をクリックします。

### <a name="create-three-actions"></a>3 つのアクションを作成します。

1. [アクション] をクリックし、[新しいアクション] をクリックします。
2. [応答] に「Which city do you want?」と入力します。
3. [Disqualifying Entities]\(不適格エンティティ\) に「$city」と入力します。
3. Click Create

次に、アクションをもう 1 つ作成します。

1. [アクション] をクリックし、[新しいアクション] をクリックします。
3. [応答] に「The weather in $city is probably sunny」と入力します。
4. [Required Entities]\(必須エンティティ\) に「$city」と入力します。
4. [作成] をクリックします。

3 つ目のアクションを作成します。

1. [アクション] をクリックし、[新しいアクション] をクリックします。
3. [応答] に「Try asking for the weather」と入力します。
    - これは、"what can the system do?" といったユーザーの質問に対する応答として使用されます。
4. [Disqualifying Entities]\(不適格エンティティ\) に「$city」と入力します。
4. Click Create

これでアクションが 3 つできました。

### <a name="train-the-bot"></a>ボットのトレーニング

1. [Train Dialogs]\(トレーニング会話\) をクリックし、[New Train Dialog]\(新しいトレーニング会話\) をクリックします。
2. 「what's the weather」と入力します。
3. [Score Actions]\(アクションのスコア付け\) をクリックし、[Which city do you want?] を選択します。
2. 「denver」と入力します。
3. [denver] をダブルクリックし、[city] を選択します。
    - これで city エンティティとしてマークされます。
5. [Score Actions]\(アクションのスコア付け\) をクリックします。
    - 'denver' は現在、city エンティティに存在します。 
6. [The weather in $city is probably sunny] を選択します。
7. [Done Teaching]\(学習の完了\) をクリックします。

別の会話例を追加します。

1. [新しいアクション] をクリックし、[New Train Dialog]\(新しいトレーニング会話\) をクリックします。
2. 「what can you do?」と入力します。
3. [Score Actions]\(アクションのスコア付け\) をクリックし、[Try asking for the weather] を選択します。
2. 「What's the weather in seattle」と入力します。
3. [seattle] をダブルクリックし、[city] を選択します。
    - これで city エンティティとしてマークされます。
5. [Score Actions]\(アクションのスコア付け\) をクリックします。
    - 'seattle' は現在、city エンティティに存在します。 
6. [The weather in $city is probably sunny] を選択します。
7. [Done Teaching]\(学習の完了\) をクリックします。

ユーザーが、上記と意味的に近い内容を言った場合にどうなるかを見てみましょう。

1. [新しいアクション] をクリックし、[New Train Dialog]\(新しいトレーニング会話\) をクリックします。
2. 「help」と入力します。
3. [Score Action]\(アクションのスコア付け\) をクリックします。
    - 2 つの潜在的な応答のスコアは、非常に接近しています。 このことから、2 つのアクションの境界について、モデルが混乱していることがわかります。
6. [Abandon Teaching]\(学習の破棄\) をクリックして [Confirm]\(確定\) をクリックします。

![](../media/tutorial8_closescores.png)

この場合、会話に代替入力を追加することが回避策となる可能性があります。 これらは、学習させるときと同じように追加することができます。 後から戻って追加することもできます。

2. [Train Dialogs]\(トレーニング会話\) で [What can you do?] をクリックします 。
2. 会話の [what can you do?] をクリックして それを選択します。
    1. 右側のウィンドウの [Entity Detection]\(エンティティ検出\) で、[Add alternative input]\(代替入力の追加\) に、いくつかの代替候補を入力します。
    1. [what are my choices?] と入力します。
    2. [Tell me my choices] と入力します。
    3. [help] と入力します。
    1. [Submit Changes]\(変更内容の送信\) をクリックします。


![](../media/tutorial8_helpalternates.png)

2. [what's the weather in seattle] をクリックします。
    1. [Add alternative input]\(代替入力の追加\) に「forecast for seattle」と入力します。
    2. [seattle] をダブルクリックし、[city] を選択します。 代替入力のエンティティが存在し、同じ一連のエンティティが含まれている必要があります。 エンティティの内容が異なっていても問題ありません。
    3. [Add alternative input]\(代替入力の追加\) に「will it rain today in denver」と入力します。
    4. [denver] をクリックし、[city] を選択します。
    5. [Submit Changes]\(変更内容の送信\) をクリックし、[完了] をクリックします。


1 つ目の会話に代替入力を追加してみましょう。

1. [Train Dialogs]\(トレーニング会話\) をクリックします。
2. "what's the weather" で始まる会話をクリックします。
2. 左側のウィンドウで [what's the weather] をクリックして選択します。
    1. [Add alternative input]\(代替入力の追加\) に「weather forecast」と入力します。
    2. 「will it rain?」と入力します。
    3. [Submit Changes]\(変更内容の送信\) をクリックします。
4. 左側のウィンドウで [denver] をクリックして選択します。
    1. [Add alternative input]\(代替入力の追加\) に「for denver」と入力します。
    2. 「forecast for austin」と入力します。
        - フレーズ全体が強調表示されています。 そのフレーズをクリックし、赤色の [x] をクリックします。 次に [austin] を選択して、[city] をクリックします。
        - [Submit Changes]\(変更内容の送信\) をクリックします。
    1. [完了] をクリックすると、モデルの再トレーニングが実行されます。

![](../media/tutorial8_altcities.png)

いくつかのバリエーションを試してみましょう。

1. [New Train Dialog]\(新しいトレーニング会話\) をクリックします。
2. 「what are you capabilities」と入力します。
3. [Score Action]\(アクションのスコア付け\) をクリックします。
    - 次のアクションに関してスコアがより決定的になりました。これは、モデルの確実性を示しています。
2. [Try asking for weather] を選択します。
6. [Done Teaching]\(学習の完了\) をクリックします。

以上、発話する表現の言い換えを、代替入力によって指定する方法を見てきました。 多くの部分が共通する会話を 1 つにまとめ、ユーザーが利用できる言い換えパターンを列挙することができるので、そのような会話をいくつも作成する手間が省けます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [会話記録](./9-log-dialogs.md)
