---
title: Conversation Learner モデルに事前トレーニング済みエンティティを追加する方法 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner モデルに事前トレーニング済みエンティティを追加する方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: c198dfc19a350188f500af86c531be9a9ac424ce
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796545"
---
# <a name="how-to-add-pre-trained-entities"></a>事前トレーニング済みエンティティを追加する方法
このチュートリアルでは、Conversation Learner モデルに事前トレーニング済みエンティティを追加する方法を説明します。

## <a name="video"></a>ビデオ

[![事前トレーニング済みエンティティのチュートリアルのプレビュー](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities_Preview)](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities)

## <a name="requirements"></a>必要条件
このチュートリアルでは、general tutorial ボットが実行されている必要があります。

    npm run tutorial-general

## <a name="details"></a>詳細

事前トレーニング済みエンティティでは、数値、日付、金額などの一般的なエンティティの種類が認識されます。  "何もしなくても" 動作し、カスタム エンティティとは異なり、トレーニングする必要はなく、動作を変更することはできません。  既定では、事前トレーニング済みエンティティは複数値であり、エンティティの識別されたすべてのインスタンスが蓄積されます。

## <a name="steps"></a>手順

### <a name="create-the-model"></a>モデルを作成する

1. Web UI で、[New Model]\(新しいモデル\) をクリックします。
2. [Name]\(名前\) フィールドに「PretrainedEntities」と入力して、Enter キーを押します。
3. [Create]\(作成\) ボタンをクリックします。

### <a name="entity-creation"></a>エンティティの作成

1. 左側のパネルで、[Entities]\(エンティティ\)、[New Entity]\(新しいエンティティ\) ボタンの順にクリックします。
2. [Entity Type]\(エンティティの種類\) で [Pre-Trained/datetimeV2]\(事前トレーニング済み/datetimeV2\) を選択します。
3. [Multi-valued]\(複数値\) チェック ボックスをオンにします。
    - 複数値エンティティでは、1 つまたは複数の値がエンティティに累積されます。
    - 事前トレーニング済みエンティティでは、否定可能プロパティは無効になっています。
4. [Create]\(作成\) ボタンをクリックします。

![](../media/tutorial7_entities_a.PNG)

### <a name="create-the-first-action"></a>1 つ目のアクションを作成する

1. 左側のパネルで [Actions]\(アクション\) をクリックし、[New Action]\(新しいアクション\) ボタンをクリックします。
2. [Bot's response...]\(ボットの応答...\) フィールドに、「The date is $builtin-datetimev2」(日付は $builtin-datetimev2 です) と入力します
3. [Create]\(作成\) ボタンをクリックします。

![](../media/tutorial7_actions_a.PNG)

### <a name="create-the-second-action"></a>2 つ目のアクションを作成する

1. 左側のパネルで [Actions]\(アクション\) をクリックし、[New Action]\(新しいアクション\) ボタンをクリックします。
2. [Bot's response...]\(ボットの応答...\) フィールドに、「What's the date?」(何日ですか?) と入力します
    - 事前トレーニング済みエンティティは、すべてのユーザー発話に対して既定で認識されるため、必須エンティティにすることはできません。
3. [Disqualifying Entitles]\(不適格エンティティ\) フィールドに「builtin-datetimev2」と入力します。
4. [Create]\(作成\) ボタンをクリックします。

![](../media/tutorial7_actions2_a.PNG)

### <a name="train-the-model"></a>モデルをトレーニングする

1. 左側のパネルで、[Train Dialogs]\(トレーニング会話\) をクリックしてから、[New Train Dialog]\(新しいトレーニング会話\) ボタンをクリックします。
2. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルで、「hello」(こんにちは) と入力します。
3. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
4. 応答 "What's the date?" (何日ですか?) を選択します
5. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルに、「today」(今日) と入力します
    - 今日の発話が、LUIS の事前トレーニング済みモデルによって自動的に認識されます。
    - 事前トレーニング済みエンティティの値をマウスでポイントすると、LUIS によって提供される追加のデータが表示されます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [エンティティ リゾルバー](./09-entity-resolvers.md)
