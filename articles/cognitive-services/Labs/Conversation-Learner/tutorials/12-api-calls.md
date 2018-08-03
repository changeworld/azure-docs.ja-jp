---
title: Conversation Learner モデルで API 呼び出しを使用する方法 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner モデルで API 呼び出しを使用する方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 1d4013d736d8cfcb75874bc0c86d20b86ab4dd62
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2018
ms.locfileid: "39215841"
---
# <a name="how-to-add-api-calls-to-a-conversation-learner-model"></a>Conversation Learner モデルに API 呼び出しを追加する方法

このこのチュートリアルでは、モデルに API 呼び出しを追加する方法を示します。 API 呼び出しは、定義してボット内に書き込む関数であり、Conversation Learner はそれらを呼び出すことができます。

## <a name="video"></a>ビデオ

[![チュートリアル 12 のプレビュー](http://aka.ms/cl-tutorial-12-preview)](http://aka.ms/blis-tutorial-12)

## <a name="requirements"></a>必要条件
このチュートリアルでは、"tutorialAPICalls.ts" ボットが実行されている必要があります。

    npm run tutorial-api-calls

## <a name="details"></a>詳細

- API 呼び出しは、エンティティを読み取って操作できます。
- API 呼び出しは、メモリ マネージャー オブジェクトにアクセスできます。
- API 呼び出しでは、引数を使用することもできます。これにより、同じ API 呼び出しを異なる目的で再利用できます。

### <a name="open-the-demo"></a>デモを開く

Web UI の [モデル] 一覧で、[Tutorial-12-APICalls] をクリックします。 

### <a name="entities"></a>エンティティ

モデル内に、number という名前の 1 つのエンティティを定義しました。

![](../media/tutorial12_entities.PNG)

### <a name="api-calls"></a>API 呼び出し
API 呼び出しのコードは、次のファイルに定義されています。C:\<installedpath\>\src\demos\tutorialAPICalls.ts

![](../media/tutorial12_apicalls.PNG)

- 最初の API コールバックは、RandomGreeting です。 greeting 変数に定義されたあいさつをランダムに返します。
- Multiply API コールバック: ユーザーによって指定された 2 つの数値を乗算します。 2 つの数値の乗算の結果を返します。 これは、API コールバックが入力を受け取ることができることを示しています。 最初の引数はメモリ マネージャーであることに注意してください。 
- ClearEntities API コールバック: ユーザーが次の番号を入力できるように、number エンティティをクリアします。 これは、API 呼び出しがエンティティをどのように操作できるかを示しています。

### <a name="actions"></a>アクション

アクションは 4 つ作成してあります。 

![](../media/tutorial12_actions.PNG)

- [What number do you want to multiply by 12?] の他に  (これはコミュニケーション アクションです)、API 呼び出しの一般的なパターンを示す 3 つの異なる API 呼び出しがあります。

- RandomGreeting: 非待機操作です。 これを設定するには、[アクションの作成] ダイアログで、アクションの種類として [API_LOCAL] を選択し、[RandomGreeting] を選択します。 

![](../media/tutorial12_setupapicall.PNG)

API の横にある更新ボタンは、ボットが停止された場合や API を変更する場合に使用します。 更新をクリックすると、最新の変更が取得されます。

ここでは、Multiply アクションをどのように作成したかを示します。API_Local と API を選択した後、最初の入力値 (num1string) にエンティティ ($number) を、2 つ目の入力値 (num2string) に値 (12) を入力しました。 これにより、ボットと API 呼び出しの間にある程度の間接性が提供され、同じコールバックをシステム内のアクションにマップできるようになり、異なる方法でアクションを割り当てることができます。

![](../media/tutorial12_actionmultiply.PNG)

### <a name="train-dialog"></a>会話をトレーニングする

それでは、学習させるための会話を見ていきましょう。

1. [Train Dialogs]\(トレーニング会話\) をクリックし、[New Train Dialog]\(新しいトレーニング会話\) をクリックします。
1. 「hi」と入力します。
2. [Score Action]\(アクションのスコア付け\) をクリックします。
3. RandomGreeting をクリックして選択します。 これにより、Random Greeting API 呼び出しが実行されます。
3. [What number to do you want to multiply by 12?] をクリックして選択します。
4. 「8」と入力します。 [Score Action]\(アクションのスコア付け\) をクリックします。
4. [Multiply $number 12] を選択します。 乗算の結果に注目します。
5. [Clear Entities] を選択します。
    - `number` エンティティの値がクリアされました。
3. [What number to do you want to multiply by 12?] をクリックして選択します。
4. [Done Testing]\(テストの完了\) をクリックします。

![](../media/tutorial12_dialog.PNG)

これで、API コールバックの登録方法、それらの共通パターン、引数と関連する値とエンティティを定義する方法の説明は終わりです。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [カード (パート 1)](./13-cards-1.md)
