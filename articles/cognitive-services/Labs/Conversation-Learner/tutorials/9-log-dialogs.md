---
title: Conversation Learner モデルで会話を記録する方法 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner モデルで会話を記録する方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 6ceeb9683a979256a8a52347fc74ab758fd1d348
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171437"
---
# <a name="how-to-log-dialogs-in-a-conversation-learner-model"></a>Conversation Learner モデルで会話を記録する方法

このチュートリアルでは、Conversation Learner インターフェイス内でエンド ユーザー テストを行う方法について説明します。会話がどのように記録されるか、また、モデルを改善するために、記録された会話をどのように修正するかについて取り上げています。

## <a name="video"></a>ビデオ

[![チュートリアル 9 のプレビュー](http://aka.ms/cl-tutorial-09-preview)](http://aka.ms/blis-tutorial-09)

## <a name="requirements"></a>必要条件
このチュートリアルでは、general tutorial ボットが実行されている必要があります。

    npm run tutorial-general

## <a name="details"></a>詳細
エンド ユーザーとの間で行われた会話は、会話記録を使用して確認し、修正を加えることができます。  具体的には、エンティティ ラベルとアクションの選択を修正することで、トレーニング済みのモデルと全体的なシステムのパフォーマンスを改善することができます。 

## <a name="steps"></a>手順

### <a name="create-the-model"></a>モデルの作成

1. Web UI で、[新しいモデル] をクリックします。
2. [名前] に「LogDialogs」と入力します。 続けて [作成] をクリックします。

### <a name="create-an-entity"></a>エンティティの作成

1. [エンティティ] をクリックし、[新しいエンティティ] をクリックします。
2. [エンティティ名] に「city」と入力します。
3. [作成] をクリックします。

### <a name="create-two-actions"></a>2 つのアクションの作成

1. [アクション] をクリックし、[新しいアクション] をクリックします。
2. [応答] に「Which city?」と入力します。
3. [Disqualifying Entities]\(不適格エンティティ\) に「$city」と入力します。
3. Click Create

次に、アクションをもう 1 つ作成します。

1. [アクション] をクリックし、[新しいアクション] をクリックします。
3. [応答] に「The weather in $city is probably sunny」と入力します。
4. [Required Entities]\(必須エンティティ\) に「$city」と入力します。
4. [作成] をクリックします。

これでアクションが 2 つできました。

### <a name="train-the-bot"></a>ボットのトレーニング

1. [Train Dialogs]\(トレーニング会話\) をクリックし、[New Train Dialog]\(新しいトレーニング会話\) をクリックします。
2. 「what's the weather」と入力します。
3. [Score Actions]\(アクションのスコア付け\) をクリックし、[Which city?] を選択します。
2. 「Seattle」と入力します。
3. [Seattle] をダブルクリックし、[city] を選択します。
    - これで city エンティティとしてマークされます。
5. [Score Actions]\(アクションのスコア付け\) をクリックします。
6. [The weather in $city is probably sunny] を選択します。
7. [Done Teaching]\(学習の完了\) をクリックします。

別の会話例を追加します。

1. [新しいアクション] をクリックし、[New Train Dialog]\(新しいトレーニング会話\) をクリックします。
2. 「what's the weather in Seattle?」と入力します。 Seattle がエンティティとしてタグ付けされていることに注目してください。
5. [Score Actions]\(アクションのスコア付け\) をクリックします。 
6. [The weather in $city is probably sunny] を選択します。
7. [Done Teaching]\(学習の完了\) をクリックします。

### <a name="try-the-bot-as-the-user"></a>ユーザーとしてボットを試す
このボットがユーザーにデプロイ済みであるとしましょう。

1. [Log Dialogs]\(会話記録\) をクリックします。
2. [New Chat Session]\(新しいチャット セッション\) をクリックします。
    - UI の左側にある Web チャット コントロールに、実際のユーザーが体験するようにボットが表示されます。 右側の余白領域は無視してください。
3. 「hello」と入力します。
4. ボットの応答: "which city?"
4. 「Boston」と入力します。
5. ボットの応答: "which city?"
    - これは正しいとは思えません。 この会話は保存することにしましょう。
2. [Done Testing]\(テストの完了\) をクリックします。

新しいセッションを開始しましょう。

2. [New Chat Session]\(新しいチャット セッション\) をクリックします。
3. 「forecast for Boston」と入力します。
4. ボットの応答: "which city?"
2. [Done Teaching]\(学習の完了\) をクリックします。

今度は、2 つ目の会話に修正を加えましょう。

1. [Log Dialogs]\(会話記録\) の [forecast for Boston] をクリックします。
    - 会話が開きます。
    - 会話のユーザー側 (ここでは "forecast for Boston") をクリックすると、エンティティ ラベルを変更できます。
    - システム側 (ここでは "which city") をクリックすると、アクションの選択を変更できます。
5. [forecast for Boston] をクリックします。 
    - この問題の根本的原因は、Boston がエンティティとしてタグ付けされていないことです。 この点を変更する必要があります。
    - [Boston] をダブルクリックし、[city] を選択します。
    - [Submit Changes]\(変更内容の送信\) をクリックし、[保存] をクリックします。 先ほどの変更に基づくトレーニング会話が作成され、トレーニング会話内の変更を行った部分に誘導されます。
6. [The weather in $city is probably sunny] を選択します。
7. [Done Teaching]\(学習の完了\) をクリックします。 ここで [Train Dialogs]\(トレーニング会話\) に移動すると、新しい操作が追加されていることがわかります。

![](../media/tutorial9_logdiag1.PNG)

今度は、もう一方の会話に修正を加えましょう。

1. [Log Dialogs]\(会話記録\) の [hello] をクリックします。
    - 会話が開きます。
3. 'hello' への応答は 'city' です。 よりふさわしい応答になるように変更したいところです。 たとえば、この場合、"hello, I'm the weather bot" の方が適切でしょう。 しかし、そのようなアクションは存在しないので、新たに作成する必要があります。
4. [アクション] をクリックします。
    - [応答] に「I'm the weather bot. I can help with forecasts.」と入力します。
6. [Wait for Response]\(応答を待機する\) チェック ボックスはオフにして、非待機アクションにします。
7. [作成] をクリックします。
8. [選択] をクリックして、この新しいアクションを選択します。 その後、[保存] をクリックします。
    - これにより、トレーニング セッション内の対応するポイントに戻ります。
6. [which city?] をクリックします。
7. 「Boston」と入力します。 まだ Boston タグがエンティティになっていない場合は、Boston タグをエンティティとしてダブルクリックします。
8. [Score Action]\(アクションのスコア付け\) をクリックします。
9. [The weather in $city is probably sunny] をクリックして選択します。
10. [Done Teaching]\(学習の完了\) をクリックします。

![](../media/tutorial9_addnewaction.PNG)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [エンティティ検出コールバック](./10-entity-detection-callback.md)
