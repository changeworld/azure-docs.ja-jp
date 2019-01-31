---
title: Conversation Learner モデルで否定可能エンティティを使用する方法 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner モデルで否定可能エンティティを使用する方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: fea950e2c13d9b5ce0c3619990961e611edd6626
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55207379"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-model"></a>Conversation Learner モデルで否定可能エンティティを使用する方法

このチュートリアルでは、エンティティの "否定可能" プロパティのデモンストレーションを行います。

## <a name="video"></a>ビデオ

[![否定可能エンティティのチュートリアルのプレビュー](https://aka.ms/cl_Tutorial_v3_NegatableEntities_Preview)](https://aka.ms/cl_Tutorial_v3_NegatableEntities)

## <a name="requirements"></a>必要条件
このチュートリアルでは、general tutorial ボットが実行されている必要があります。

    npm run tutorial-general

## <a name="details"></a>詳細
エンティティの "否定可能" プロパティを使用すると、エンティティの標準 (正) と負の両方のインスタンスにラベルを付け、正と負のモデルに基づいて学習させ、既存のエンティティの値をクリアできます。 Conversation Leaner では、"否定可能" プロパティ セットを持つエンティティは否定可能エンティティと呼ばれます。

## <a name="steps"></a>手順

### <a name="create-the-model"></a>モデルを作成する

1. Web UI で、[新しいモデル] をクリックします。
2. [名前] フィールドに、"NegatableEntity" と入力し、Enter キーを押します。
3. [作成] ボタンをクリックします。

### <a name="entity-creation"></a>エンティティの作成

1. 左側のパネルで、[エンティティ]、[新しいエンティティ] ボタンの順にクリックします。
2. [エンティティ型] に対して "カスタム" を選択します。
3. [エンティティ名] に「name」(名前) と入力します。
4. [Negatable]\(否定可能\) チェック ボックスをオンにします。
    - このプロパティをオンにすると、ユーザーはエンティティの値を指定したり、何かがエンティティ値で "*ない*" ことを指定したりできます。 後者の場合、結果として一致するエンティティ値が削除されます。
5. [作成] ボタンをクリックします。

![](../media/tutorial5_entities.PNG)

### <a name="create-the-first-action"></a>1 つ目のアクションを作成する

1. 左側のパネルで [アクション] をクリックし、[新しいアクション] をクリックします。
2. [Bot's response...]\(ボットの応答...\) フィールドに、「I don't know your name.」(あなたの名前がわかりません。) と入力します。
3. [Disqualifying Entitles]\(不適格エンティティ\) フィールドに「name」(名前) と入力します。
4. [作成] ボタンをクリックします。

### <a name="create-the-second-action"></a>2 つ目のアクションを作成する

1. 左側のパネルで [アクション] をクリックし、[新しいアクション] をクリックします。
2. [Bot's response...]\(ボットの応答...\) フィールドに、「I know your name. It is $name」(あなたの名前は $name です) と入力します。
3. [作成] ボタンをクリックします。

> [!NOTE]
> "name" エンティティは、応答内の参照によって "必須エンティティ" として自動的に追加されました。

これでアクションが 2 つできました。

![](../media/tutorial5_actions.PNG)

### <a name="train-the-model"></a>モデルをトレーニングする

1. 左側のパネルで、[Train Dialogs]\(トレーニング会話\) をクリックしてから、[New Train Dialog]\(新しいトレーニング会話\) ボタンをクリックします。
2. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルで、「hello」(こんにちは) と入力します。
3. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
4. 応答 "I don't know your name." (あなたの名前がわかりません。) を選択します。
    - 制約に基づく唯一の有効なアクションであるため、パーセンタイルは 100% です。
5. [Type your message...]\(メッセージを入力...\) と表示されているチャット パネルで、「My name is Frank」(私の名前は Frank です) と入力します
6. "Frank" を選択し、"+name" (+名前) ラベルを選択します
    - "name" (名前) エンティティには "+name" (+名前) と "-name" (-名前) の 2 つのインスタンスがあります。  (+) プラスは、値を追加するか、または上書きします。 (-) マイナスは、値を削除します。
7. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
    - "name" (名前) エンティティはモデルのメモリ内で "Frank" として定義されているので、"I know your name. It is $name" (あなたの名前は $name です) アクションが使用可能になります。
8. 応答 "I know your name. It is $name" (あなたの名前は $name です) を選択します。
9. [Type your message...]\(メッセージを入力...\) と表示されているチャット ウィンドウで、「My name is not Frank」(私の名前は Frank ではありません) と入力します。
10. "Frank" を選択し、"-name" (-名前) ラベルを選択します
    - エンティティの現在の値をクリアするために "-name" (-名前) を選択しています。
11. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
12. 応答 "I don't know your name." (あなたの名前がわかりません。) を選択します。
13. [Type your message...]\(メッセージを入力...\) と表示されているチャット ウィンドウで、「My name is Susan」(私の名前は Susan です) と入力します。
14. "Susan" を選択し、"+name" (+名前) ラベルを選択します

![](../media/tutorial5_dialogs.PNG)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [複数値エンティティ](./07-multi-value-entities.md)
