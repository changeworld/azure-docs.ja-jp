---
title: Conversation Learner モデルで複数値エンティティを使用する方法 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner モデルで複数値エンティティを使用する方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 8544d63f38f88a0e623dff343bf8b5133931b70b
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228306"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-model"></a>Conversation Learner モデルで複数値エンティティを使用する方法
このチュートリアルでは、エンティティの複数値プロパティを示します。

## <a name="video"></a>ビデオ

[![複数値エンティティのチュートリアルのプレビュー](https://aka.ms/cl_Tutorial_v3_MultiValued_Preview)](https://aka.ms/cl_Tutorial_v3_MultiValued)

## <a name="requirements"></a>必要条件
このチュートリアルでは、general tutorial ボットが実行されている必要があります。

    npm run tutorial-general

## <a name="details"></a>詳細
複数値エンティティでは、1 つの値が格納されるのではなく、一覧に値が累積されます。  これらのエンティティは、ユーザーが複数の値を指定できる場合に便利です。 たとえば、ピザのトッピングなどです。

複数値としてマークされたエンティティでは、エンティティの認識された各インスタンスが、ボットのメモリ内のリストに追加されます。 それ以降に認識された値は、上書きではなく、エンティティの値に追加されます。

## <a name="steps"></a>手順

### <a name="create-the-model"></a>モデルを作成する

1. Web UI で、[New Model]\(新しいモデル\) をクリックします。
2. [Name]\(名前\) フィールドに「MultiValueEntities」と入力して、Enter キーを押します。
3. [Create]\(作成\) ボタンをクリックします。

### <a name="entity-creation"></a>エンティティの作成

1. 左側のパネルで、[Entities]\(エンティティ\)、[New Entity]\(新しいエンティティ\) ボタンの順にクリックします。
2. [Entity Type]\(エンティティの種類\) で [Custom Trained]\(カスタム トレーニング済み\) を選択します。
3. [Entity Name]\(エンティティ名\) に「toppings」と入力します。
4. [Multi-valued]\(複数値\) チェック ボックスをオンにします。
    - 複数値エンティティでは、1 つまたは複数の値がエンティティに累積されます。
5. [Negatable]\(否定可能\) チェック ボックスをオンにします。
    - "否定可能" プロパティについては別のチュートリアルで説明されています。
6. [Create]\(作成\) ボタンをクリックします。

![](../media/tutorial6_entities.PNG)

### <a name="create-the-first-action"></a>1 つ目のアクションを作成する

1. 左側のパネルで [Actions]\(アクション\) をクリックし、[New Action]\(新しいアクション\) ボタンをクリックします。
2. [Bot's response...]\(ボットの応答...\) フィールドに、「Here are your toppings: $toppings」(選択したトッピング: $toppings) と入力します
    - 先頭のドル記号は、エンティティ参照を示します
3. [Create]\(作成\) ボタンをクリックします。

### <a name="create-the-second-action"></a>2 つ目のアクションを作成する

1. 左側のパネルで [Actions]\(アクション\) をクリックし、[New Action]\(新しいアクション\) ボタンをクリックします。
2. [Bot's response...]\(ボットの応答...\) フィールドに、「What toppings would you like?」(何をトッピングしますか?) と入力します
3. [Disqualifying Entitles]\(不適格エンティティ\) フィールドに「toppings」と入力します。
4. [Create]\(作成\) ボタンをクリックします。

これでアクションが 2 つできました。

![](../media/tutorial6_actions.PNG)

### <a name="train-the-model"></a>モデルをトレーニングする

1. 左側のパネルで、[Train Dialogs]\(トレーニング会話\) をクリックしてから、[New Train Dialog]\(新しいトレーニング会話\) ボタンをクリックします。
2. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルに、「hi」(こんにちは) と入力します。
3. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
4. "What toppings would you like?" (何をトッピングしますか?) という応答を選択します。
    - 制約に基づく唯一の有効なアクションであるため、パーセンタイルは 100% です。
5. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルに、「cheese and mushrooms」(チーズとマッシュルーム) と入力します
6. "cheese" をクリックして、ラベル [+toppings] を選択します
7. "mushrooms" をクリックして、ラベル [+toppings] を選択します
8. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
9. "Here are your toppings: $toppings" (選択したトッピング: $toppings) という応答を選択します
10. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルに、「add pepper」(ペッパーを追加) と入力します
11. "pepper" をクリックして、ラベル [+toppings] を選択します
12. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
13. "Here are your toppings: $toppings" (選択したトッピング: $toppings) という応答を選択します
14. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルに、「remove cheese」(チーズを削除) と入力します
15. "cheese" をクリックして、ラベル [-toppings] を選択します
16. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
17. "Here are your toppings: $toppings" (選択したトッピング: $toppings) という応答を選択します

![](../media/tutorial5_dialogs.PNG)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [事前トレーニング済みエンティティ](./08-pre-trained-entities.md)
