---
title: Conversation Learner モデルで否定可能エンティティを使用する方法 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner モデルで否定可能エンティティを使用する方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 2fd00d53755e44e3a3d86782c40aa6a53ff4d378
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171403"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-model"></a>Conversation Learner モデルで否定可能エンティティを使用する方法

このチュートリアルでは、エンティティの "否定可能" プロパティのデモンストレーションを行います。

## <a name="video"></a>ビデオ

[![チュートリアル 5 のプレビュー](http://aka.ms/cl-tutorial-05-preview)](http://aka.ms/blis-tutorial-05)

## <a name="requirements"></a>必要条件
このチュートリアルでは、general tutorial ボットが実行されている必要があります。

    npm run tutorial-general

## <a name="details"></a>詳細
アクションは、"No, I do not want $entity" や "no, not $entity." のように、ユーザーがエンティティの値を "クリア" できる場合、"否定可能" としてマークします  (例: "no, I do not want to leave from Boston.")。

具体的に言うと、エンティティの "否定可能" プロパティが設定されている場合、次の事実が当てはまります。

- エンティティの言及内容にラベル付けする際、エンティティの正常 (肯定的) なインスタンスと "否定的" なインスタンスの両方にラベル付けすることができます。
- LUIS は、2 つのエンティティ モデルを学習します。1 つは肯定的なインスタンスのモデル、もう 1 つは否定的なインスタンスのモデルです。
- 否定的なインスタンスには、その値 (存在する場合) をエンティティ変数からクリアする効果があります。

## <a name="steps"></a>手順

### <a name="create-the-model"></a>モデルの作成

1. Web UI で、[新しいモデル] をクリックします。
2. [名前] に「NegatableEntity」と入力します。 続けて [作成] をクリックします。

### <a name="create-an-entity"></a>エンティティの作成

1. [エンティティ] をクリックし、[新しいエンティティ] をクリックします。
2. [エンティティ名] に「name」と入力します。
3. [Negatable]\(否定可能\) チェック ボックスをオンにします。
    - このプロパティは、ユーザーがエンティティの値を指定するか、または何かがエンティティの値では*ない*と言えるようになることを示します。 後者のケースでは、対応するエンティティの値が削除されることになります。
3. [作成] をクリックします。

![](../media/tutorial5_entities.PNG)

### <a name="create-two-actions"></a>2 つのアクションの作成

1. [アクション] をクリックし、[新しいアクション] をクリックします。
2. [応答] に「I don't know your name」と入力します。
3. [Disqualifying Entities]\(不適格エンティティ\) に「name」と入力します。
3. Click Create

次に、アクションをもう 1 つ作成します。

1. [アクション] をクリックし、[新しいアクション] をクリックして 2 つ目のアクションを作成します。
3. [応答] に「I know your name.  It is $name」と入力します。
4. Click Create

これでアクションが 2 つできました。

![](../media/tutorial5_actions.PNG)

### <a name="train-the-bot"></a>ボットのトレーニング

1. [Train Dialogs]\(トレーニング会話\) をクリックし、[New Train Dialog]\(新しいトレーニング会話\) をクリックします。
2. 「hello」と入力します。
3. [Score Actions]\(アクションのスコア付け\) をクリックし、[I don't know your name] を選択します。
    - これが唯一の有効なアクションであるため、スコアは 100% です。
2. 「my name is david」と入力します。
3. [david] を選択し、[+name] ラベルを選択します。
    - 'name' には '+name' と '-name' の 2 つのインスタンスがあります。  (+) プラスは、値を追加するか、または上書きします。 (-) マイナスは、値を削除します。
5. [Score Actions]\(アクションのスコア付け\) をクリックします。
    - これで、name 値がボットのメモリに入りました。
    - "I know your name.  It is $name" だけが唯一選択可能な応答となります。 
6. I know your name. It is $name を選択します。

否定可能エンティティをクリアしてみましょう。

7. 「my name is not david」と入力します。
    - 前のパターンに基づいて、"not" が name として選択されていることに注目してください。 このラベルは正しくありません。
2. [not] をクリックし、次に赤色の [x] をクリックします。 
3. [david] をクリックします。
    - これは否定エンティティになり、name エンティティの値ではないことが伝えられます。
2. [-name] を選択します。
3. [Score Action]\(アクションのスコア付け\) をクリックします。
    - その値がメモリからクリアされていることに注目してください。
2. [I don't know your name] を選択します。これが唯一のアクションになります。

次に、名前に新しい値を入力する方法について説明します。

3. 名前として「john」を入力します。 次に [john] を選択して、[name] をクリックします。
4. [Score Action]\(アクションのスコア付け\) をクリックします。
5. I know your name. It is $name を選択します。

今度は、入力した名前を置き換えてみましょう。

6. 「my name is susan」と入力します。
7. I know your name. It is $name を選択します。
7. [Score Action]\(アクションのスコア付け\) をクリックします。
8. エンティティ値の **john** が **susan** で上書きされていることに注目してください。
9. 「my name is not susan」と入力します。
    - これは、システムによって否定インスタンスとしてラベル付けされていることに注目してください。
2. [Score Action]\(アクションのスコア付け\) をクリックします。
3. [I don't know your name] を選択します。これが唯一のアクションになります。
7. [Done Teaching]\(学習の完了\) をクリックします。

![](../media/tutorial5_dialogs.PNG)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [複数値エンティティ](./6-multi-value-entities.md)
