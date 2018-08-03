---
title: Conversation Learner モデルでエンティティを使用する方法 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner モデルでエンティティを使用する方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: f851d43d69999a848dea01c9457a379adb63353b
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39172383"
---
# <a name="introduction-to-entities"></a>エンティティの概要

このチュートリアルでは、エンティティの概要を紹介すると共に、アクションの [Disqualifying entities]\(不適格エンティティ\) フィールドと [Required entities]\(必須エンティティ\) フィールドの使用方法について説明します。

## <a name="video"></a>ビデオ

[![チュートリアル 3 のプレビュー](http://aka.ms/cl-tutorial-03-preview)](http://aka.ms/blis-tutorial-03)

## <a name="requirements"></a>必要条件

このチュートリアルでは、general tutorial ボットが実行されている必要があります。

    npm run tutorial-general

## <a name="details"></a>詳細

このチュートリアルでは、エンティティの一般的な用途を 2 つ例示しています。  1 つ目は、ユーザー メッセージから部分文字列を抽出する機能です。たとえばエンティティは、"what's the weather in Seattle?" から都市を識別することができます。  2 つ目は、どのようなときにアクションを選択できるかという点に関して制約を加える機能です。  具体的に言うと、アクションには、特定のエンティティを "必須" または "不適格" として登録することができます。
- アクションが選択可能となるために、ボットのメモリ内に存在していなければならないのが、そのアクションの必須エンティティです。
- アクションが選択可能となるために、ボットのメモリ内に "*存在してはならない*" のが不適格エンティティです。

その他、既製のエンティティ、複数値エンティティ、否定可能エンティティ、プログラマティック エンティティ、コード内の操作を目的としたエンティティなど、エンティティに関するさまざまな側面については、他のチュートリアルで取り上げています。

## <a name="steps"></a>手順

### <a name="create-the-model"></a>モデルの作成

1. Web UI で、[新しいモデル] をクリックします。
2. [名前] に「IntroToEntities」と入力します。 続けて [作成] をクリックします。

### <a name="create-entity"></a>エンティティの作成

1. [エンティティ] をクリックし、[新しいエンティティ] をクリックします。
2. [エンティティ名] に「city」と入力します。
3. Click Create

> [!NOTE]
> エンティティ型は 'custom' です。つまり、そのエンティティをトレーニングできます。  また、動作を調整できないことを意味する既製のエンティティもあります。既製のエンティティについては、別のチュートリアルで取り上げます。

### <a name="create-two-actions"></a>2 つのアクションを作成する

1. [アクション] をクリックし、[新しいアクション] をクリックします。
2. [応答] に「I don't know what city you want」と入力します。
3. [Disqualifying Entities]\(不適格エンティティ\) に「$city」と入力します。 [保存] をクリックします。
    - これは、このエンティティがボットのメモリに定義されていた場合、このアクションが "*選択不可*" になることを意味します。
2. [アクション] をクリックし、[新しいアクション] をクリックして 2 つ目のアクションを作成します。
3. [応答] に「The weather in the $city is probably sunny」と入力します。
4. [Required Entities] (必須エンティティ) には、参照された city エンティティが自動的に追加されています。
5. [保存] をクリックします。

これでアクションが 2 つできました。

![](../media/tutorial3_actions.PNG)

### <a name="train-the-bot"></a>ボットのトレーニング

1. [Train Dialogs]\(トレーニング会話\) をクリックし、[New Train Dialog]\(新しいトレーニング会話\) をクリックします。
2. 「hello」と入力します。
3. [Score Actions]\(アクションのスコア付け\) をクリックし、[I don't know what city you want?] を選択します。
    - city エンティティはボットのメモリ内で定義されていないため、city エンティティが必要な応答は選択できません。
2. [I don't know what city you want] を選択します。
4. 「seattle」と入力します。 "seattle" をハイライトし、[city] をクリックします。
5. [Score Actions]\(アクションのスコア付け\) をクリックします。
    - city 値は現在、ボットのメモリ内にあります。
    - 応答として [Weather in $city is probably sunny] を選択できるようになりました。 
6. [Weather in $city is probably sunny] を選択します。

たとえば、ユーザーが「repeat that」と入力したとしましょう。 
1. そのように入力して Enter キーを押します。 city エンティティとその値はメモリ内にあり、使用可能です。
2. [Weather in $city is probably sunny] を選択します。

![](../media/tutorial3_entities.PNG)

エンティティを作成し、ユーザー メッセージの中で、そのインスタンスにラベル付けを行いました。  また、アクションの不適格エンティティ フィールドと必須エンティティ フィールドを利用し、ボットのメモリにエンティティが存在するかどうかの情報を使って、アクションを選択できるタイミングを制御しました。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [予期されるエンティティ](./4-expected-entity.md)
