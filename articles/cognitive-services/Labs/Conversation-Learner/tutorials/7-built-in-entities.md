---
title: Conversation Learner モデルに作成済みのエンティティを追加する方法 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner モデルに作成済みのエンティティを追加する方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 84d73add5586aaaf130253a8122a4152e39bcbe9
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171369"
---
# <a name="how-to-add-pre-built-entities"></a>作成済みのエンティティを追加する方法
このチュートリアルでは、Conversation Learner モデルに "作成済みの" エンティティを追加する方法を示します。

## <a name="video"></a>ビデオ

[![チュートリアル 7 のプレビュー](http://aka.ms/cl-tutorial-07-preview)](http://aka.ms/blis-tutorial-07)

## <a name="requirements"></a>必要条件
このチュートリアルでは、general tutorial ボットが実行されている必要があります。

    npm run tutorial-general

## <a name="details"></a>詳細

作成済みのエンティティは、数値、日付、金額などの一般的なエンティティの種類を認識します。  カスタム エンティティとは異なり、"そのままの状態" で動作し、トレーニングの必要はありません。  カスタム エンティティとは異なり、動作を変更することはできません。  既定では、作成済みエンティティは複数値です。つまり、ボットのメモリには、エンティティの識別されたすべてのインスタンスが蓄積されます。

## <a name="steps"></a>手順

### <a name="create-the-model"></a>モデルの作成

1. Web UI で、[新しいモデル] をクリックします。
2. [名前] に「BuiltInEntities」と入力します。 続けて [作成] をクリックします。

### <a name="create-an-entity"></a>エンティティの作成

1. [エンティティ] をクリックし、[新しいエンティティ] をクリックします。
2. [EntityType]\(エンティティの種類\) ドロップダウンをクリックし、datetimev2 を選択します。
    - [Programmable]\(プログラム可能\) および [Negatable]\(否定可能\) オプションは、作成済みエンティティには適用されないので、無効になっています。
3. [作成] をクリックします。

![](../media/tutorial7_entities.PNG)

### <a name="create-two-actions"></a>2 つのアクションの作成

1. [アクション] をクリックし、[新しいアクション] をクリックします。
2. [Response]\(応答\) に、「The date is $luis-datetimev2」と入力します。
3. [作成] をクリックします。

![](../media/tutorial7_actions.PNG)

次に、アクションをもう 1 つ作成します。

1. [アクション] をクリックし、[新しいアクション] をクリックして 2 つ目のアクションを作成します。
3. [Response]\(応答\) に、「What's the date?」と入力します。
4. [Disqualifying Entities]\(不適格エンティティ\) に「luis-datetimev2」と入力します。
4. Click Create

![](../media/tutorial7_actions2.PNG)

これでアクションが 2 つできました。

### <a name="train-the-bot"></a>ボットのトレーニング

1. [Train Dialogs]\(トレーニング会話\) をクリックし、[New Train Dialog]\(新しいトレーニング会話\) をクリックします。
2. 「hello」と入力します。
3. [Score Actions]\(アクションのスコア付け\) をクリックし、"What's the date?" を選択します。
2. 「today」と入力します。 
    - "today" にタグが付き、作成済みエンティティであり編集できないため 2 行目に表示されることに注意してください。
5. [Score Actions]\(アクションのスコア付け\) をクリックします。
    - [Entity Memory]\(エンティティのメモリ\) セクションに日付が表示されることに注意してください。 
    - 日付をマウスでポイントすると、LUIS によって提供された追加データが表示されます。これは使用可能であり、コードでさらに操作できます。 
6. "The date is $luis-datetimev2" を選択します。
7. [Done Teaching]\(学習の完了\) をクリックします。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [代替入力](./8-alternative-inputs.md)
