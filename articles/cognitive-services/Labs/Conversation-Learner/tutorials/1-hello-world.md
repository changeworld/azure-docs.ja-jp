---
title: "\"Hello World\" Conversation Learner モデルを作成する方法 - Microsoft Cognitive Services | Microsoft Docs"
titleSuffix: Azure
description: "\"Hello World\" Conversation Learner モデルを作成する方法について説明します。"
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 70b8f25bd699cbdb069892d65bf766ef3953f59d
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39170873"
---
# <a name="how-to-create-a-hello-world-model-with-conversation-learner"></a>Conversation Learner で "Hello World" モデルを作成する方法

このチュートリアルでは、Conversation Learner でのアクションの作成、対話形式での学習、エンド ユーザーによる記録された会話の修正など、その基本的な操作方法について説明します。

## <a name="video"></a>ビデオ

[![チュートリアル 1 のプレビュー](http://aka.ms/cl-tutorial-01-preview)](http://aka.ms/blis-tutorial-01)


## <a name="requirements"></a>必要条件
LUIS オーサリング キーを含む `.env` ファイルの作成など、セットアップ手順がまだ完了していない場合は、先にすべて済ませておいてください。  詳細については、[クイック スタート](https://github.com/Microsoft/ConversationLearner-Samples)を参照してください。

このチュートリアルでは、general tutorial ボットが実行されている必要があります。

    npm run tutorial-general

## <a name="steps"></a>手順

Web UI のホーム ページから始めます。

### <a name="create-the-model"></a>モデルの作成
1. [新しいモデル] をクリックする
2. [名前] フィールドに「Hello World」と入力します。
3. Click Create

### <a name="create-an-action"></a>アクションを作成する

1. Hello World モデルをクリックして起動する
2. [アクション] をクリックし、[新しいアクション] をクリックします。
    - アクションは、Conversation Learner からユーザーに返されるテキスト メッセージ、API 呼び出し、カードのいずれかになります。
3. [応答] に「Hello World!」と入力します。
    - これは、ボットから返されることになる応答です。
4. Click Create

ボットでできる初めてのこと、つまりテキスト応答を返す機能を作成しました。

### <a name="train-the-bot"></a>ボットのトレーニング

#### <a name="create-the-first-dialog"></a>初めての会話を作成する

1. [Train Dialogs]\(トレーニング会話\) をクリックし、[New Train Dialog]\(新しいトレーニング会話\) をクリックします。
2. 会話の最初にユーザーが発言する内容の例を入力します (「hello」など)。
3. [Score Actions]\(アクションのスコア付け\) をクリックします。
4. [Hello World!] を選択します。
    - これで、1 ターンの会話例が作成されます。 
2. 「goodbye」と入力します。
3. [Score Actions]\(アクションのスコア付け\) をクリックします。
4. [アクションの追加] をクリックし、「Goodbye!」と入力します。 [応答] の [作成] をクリックします。
5. [Done Teaching]\(学習の完了\) をクリックします。 このトレーニング会話が終了されます。

これで、学習させるための会話が 1 つシステムに作成されました。

#### <a name="continue-teaching-the-bot"></a>引き続きボットに学習させる
さらにもう 1 つトレーニングを実行し、ボットが応答するようすを見てみましょう。

1. [New Train Dialog]\(新しいトレーニング会話\) をクリックします。
2. 「hi there」と入力します。
    - これは 1 つ目の会話に似ているので、ボットから良好なスコアを得られることが予想されます。
2. [Score Action]\(アクションのスコア付け\) をクリックします。
    - ポジションとスコアはまだ正確さが不足している可能性があり、さらなる学習が必要です。
3. [Hello World!] の横の [選択] をクリックします。
4. 次に「bye」と入力します。
5. [Score Actions]\(アクションのスコア付け\) をクリックします。
6. [Goodbye!] を選択します。
7. [Done Teaching]\(学習の完了\) をクリックします。

![](../media/tutorial1_actions.PNG)

ボットの動作を確認するため、さらに別の学習セッションを行います。

"hi" と "byebye" を使用して上記の手順を繰り返し、[Score Action]\(アクションのスコア付け\) をクリックしたときのボットの応答について、そのポジションとスコアの変化に注目します。

今度は、"howdy" と "good bye" を使用して手順を繰り返してみてください。スコアに改善が見られ、ボットがこの対話を学習したことがわかります。

![](../media/tutorial1_dialogs.PNG)

### <a name="test-the-bot-as-an-end-user"></a>エンド ユーザーとしてボットをテストする

1. [Log Dialogs]\(会話記録\) をクリックし、[New Log Dialog]\(新しい会話記録\) をクリックします。
2. 「hello there」と入力します。
3. 次に、「bye」と入力します。

また、"bye" で会話を始めてみてもかまいません。ボットの応答に注目してください。

### <a name="view-conversations-in-the-log-dialogs"></a>[Log Dialogs]\(会話記録\) で会話を表示する

[Log Dialogs]\(会話記録\) では、会話の一覧を表示し、対話を更新して、トレーニング会話として保存することができます。 その手順を次に示します。

1. 会話のログをクリックします。
2. 会話に問題がなさそうなら、最後のアクションをクリックします (例: "Goodbye")。
3. 提示された応答をクリックして選択します。 
    - 別のアクションを選択したり追加したりすることもできます。
4. 次に、[完了] をクリックして、これをトレーニング会話として保存します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [待機アクションと非待機アクション](./2-wait-vs-nonwait-actions.md)