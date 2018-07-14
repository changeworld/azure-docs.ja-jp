---
title: Conversation Learner アプリケーションで複数値エンティティを使用する方法 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner アプリケーションで複数値エンティティを使用する方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 865b50747b2c9574b5f88d4902bea9e4c8e0e032
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376240"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-application"></a>Conversation Learner アプリケーションで複数値エンティティを使用する方法
このチュートリアルでは、エンティティの "複数値" プロパティを示します。

##<a name="requirements"></a>必要条件
このチュートリアルでは、汎用チュートリアル ボットが実行されている必要があります。

    npm run tutorial-general

## <a name="details"></a>詳細
"複数値" であるエンティティは、1 つの値を格納するのではなく、値を一覧に累積します。  これは、ピザのトッピングなど、ユーザーが複数の値を指定できるエンティティで役立ちます。

具体的には、エンティティが "複数値" としてマークされている場合、エンティティの認識された各インスタンスは、(単一のエンティティ値を上書きするのではなく) ボットのメモリ内の一覧に追加されます。

## <a name="steps"></a>手順

### <a name="create-the-application"></a>アプリケーションを作成する

1. Web UI で、[新しいアプリ] をクリックします。
2. [名前] に、「MultiValueEntities」と入力します。 [作成] をクリックします。

### <a name="create-an-entity"></a>エンティティを作成する

1. [エンティティ] をクリックし、[新しいエンティティ] をクリックします。
2. [エンティティ名] に、「Toppings」と入力します。
3. [複数値] チェック ボックスをオンにします。
    - 複数値エンティティは、 1 つまたは複数の値をエンティティ内に累積します。
2. [Negatable]\(否定可能\) チェック ボックスをオンにします。  
    - これにより、ユーザーは、累積されたピザのトッピングの一覧からトッピングを削除できます。
3. [作成] をクリックします。

![](../media/tutorial6_entities.PNG)

### <a name="create-two-actions"></a>2 つのアクションを作成する

1. [アクション] をクリックし、[新しいアクション] をクリックします。
2. [応答] に、「What toppings do you want?」と入力します。
3. [Disqualifying Entities]\(不適格エンティティ\) に「Toppings」と入力します。
3. [作成] をクリックします。

次に、アクションをもう 1 つ作成します。

1. [アクション] をクリックし、[新しいアクション] をクリックして 2 つ目のアクションを作成します。
3. [応答] に、「Here are your toppings: $Toppings」と入力します。
4. [作成] をクリックします。

これでアクションが 2 つできました。

![](../media/tutorial6_actions.PNG)

### <a name="train-the-bot"></a>ボットをトレーニングする

1. [Train Dialogs]\(トレーニング会話\) をクリックし、[New Train Dialog]\(新しいトレーニング会話\) をクリックします。
2. 「hello」と入力します。
3. [Score Actions]\(アクションのスコア付け\) をクリックし、[What toppings do you want?] を選択します。
2. 「mushrooms and cheese」と入力します。 
    - 1 つ以上のエンティティにラベルを付けることができます。まったく付けないこともできます。
3. ［mushrooms］ をクリックし、[Toppings] を選択します。
4. ［cheese］ をクリックし、[Toppings] を選択します。
5. [Score Actions]\(アクションのスコア付け\) をクリックします。
    - Toppings エンティティに 2 つの値が表示されます。 
6. [Here are your toppings: $Toppings] を選択します。

これにさらに追加できます。

7. 「add peppers」と入力します。
    - [Entity Detection]\(エンティティの検出\) の下の [peppers] をクリックし、[Toppings] を選択します。
3. [Score Action]\(アクションのスコア付け\) をクリックします。
    - トッピングの追加の値としてピーマンが表示されます。
6. [Here are your toppings: $Toppings] を選択します。

トッピングを1 つ 削除し、1 つ追加してみましょう。

2. 「remove peppers and add sausage」と入力します。
1. [peppers] をクリックし、赤い [x] をクリックして削除します。
2. [peppers] をクリックし、[Toppings] を選択します。
3. [Score Action]\(アクションのスコア付け\) をクリックします。
    - [peppers] が削除され、[sausage] が追加されています。
6. [Here are your toppings: $Toppings] を選択します。

次に、すべての削除を試してみましょう。

6. 「remove mushrooms, remove cheese, and remove sausage」と入力します。
7. 3 つをそれぞれクリックし、[Toppings] を選択します。
7. [Score Action]\(アクションのスコア付け\) をクリックします。
    - すべてのトッピングがクリアされます。
2. [What toppings do you want?] を選択します。
3. [Done Teaching]\(学習の完了\) をクリックします。

![](../media/tutorial6_dialogs.PNG)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [組み込みのエンティティ](./7-built-in-entities.md)
