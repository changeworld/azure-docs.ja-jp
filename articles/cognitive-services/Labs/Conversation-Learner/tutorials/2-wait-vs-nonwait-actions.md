---
title: Conversation Learner モデルで待機アクションと非待機アクションを使用する方法 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner モデルで待機アクションと非待機アクションを使用する方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: a8f7ccf79e750c9f3c21c25c50c3e275db7e4195
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173086"
---
# <a name="wait-and-non-wait-actions"></a>待機アクションと非待機アクション

このチュートリアルでは、Conversation Learner における待機アクションと非待機アクションの違いについて説明します。

## <a name="video"></a>ビデオ

[![チュートリアル 2 のプレビュー](http://aka.ms/cl-tutorial-02-preview)](http://aka.ms/blis-tutorial-02)

## <a name="requirements"></a>必要条件
このチュートリアルでは、general tutorial ボットが実行されている必要があります。

    npm run tutorial-general

## <a name="details"></a>詳細

- 待機アクション: "待機" アクションを実行したシステムは、その後アクションの実行を停止し、ユーザー入力を待機します。
- 非待機アクション: "非待機" アクションを実行したシステムは、その後すぐに (ユーザー入力を待たずに) 別のアクションを選択します。

## <a name="steps"></a>手順

### <a name="create-a-new-model"></a>新しいモデルを作成する

1. Web UI で、[新しいモデル] をクリックします。
2. [名前] に「WaitNonWait」と入力します。 続けて [作成] をクリックします。

### <a name="create-the-first-wait-action"></a>初めての待機アクションの作成

1. [アクション] をクリックし、[新しいアクション] をクリックします。
2. [応答] に「Which animal do you want?」と入力します。
    - これは待機アクションであるため、[Wait for Response]\(応答を待機する\) チェック ボックスはオンのままにしておきます。
3. [作成] をクリックします。

### <a name="create-a-non-wait-action"></a>非待機アクションの作成

1. [新しいアクション] をクリックします。
2. [応答] に「Cows say moo」と入力します。
3. [Wait for Response]\(応答を待機する\) チェック ボックスをオフにします。
4. Click Create

### <a name="create-a-second-non-wait-action"></a>2 つ目の非待機アクションの作成

1. [新しいアクション] をクリックします。
2. [応答] に「Ducks say quack」と入力します。
3. [Wait for Response]\(応答を待機する\) チェック ボックスをオフにします。
4. Click Create

![](../media/tutorial2_actions.PNG)

### <a name="train-the-bot"></a>ボットのトレーニング

1. [Train Dialogs]\(トレーニング会話\) をクリックし、[New Train Dialog]\(新しいトレーニング会話\) をクリックします。
2. 「hello」と入力します。
3. [Score Actions]\(アクションのスコア付け\) をクリックし、[Which animal do you want?] を選択します。
4. 「cow」と入力します。
5. [Score Actions]\(アクションのスコア付け\) をクリックし、[Cows say moo] を選択します。
    - ボットは入力を待たずに次のアクションを実行します。
2. [Which animal do you want?] を選択します。
3. 「duck」と入力します。
5. [Score Actions]\(アクションのスコア付け\) をクリックし、[Ducks say quack] を選択します。

![](../media/tutorial2_dialogs.PNG)

> [!NOTE]
> 待機アクションと非待機アクションに関するボット応答のシーケンス。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [エンティティの概要](./3-introduction-to-entities.md)
