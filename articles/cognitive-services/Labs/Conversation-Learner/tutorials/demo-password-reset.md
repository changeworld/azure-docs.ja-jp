---
title: 'Conversation Learner のデモ モデル: パスワードのリセット - Microsoft Cognitive Services | Microsoft Docs'
titleSuffix: Azure
description: Conversation Learner のデモ モデルを作成する方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: f633dd375d690a1c3e66a2a6e02ae69665dbe960
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39170635"
---
# <a name="demo-password-reset"></a>デモ - パスワードのリセット
このデモでは、パスワードのリセットを支援する機能を備えた単純なテクニカル サポート ボットの例を紹介します。 

Conversation Learner は、ドメイン外クラスを含め、簡単ではない会話の流れを伴うマルチ ターン シーケンスを学習することができます。 このデモンストレーションでは、コードやエンティティを一切使用しません。

## <a name="video"></a>ビデオ

[![パスワードのデモのプレビュー](http://aka.ms/cl-demo-password-preview)](http://aka.ms/blis-demo-password)

## <a name="requirements"></a>必要条件
このチュートリアルでは、パスワードのリセット ボットが実行されている必要があります。

    npm run demo-password

### <a name="open-the-demo"></a>デモを開く

Web UI の [モデル] 一覧で、[Tutorial Demo Password Reset] (チュートリアル デモ: パスワードのリセット) をクリックします。 

### <a name="actions"></a>アクション

ユーザーがパスワードに関して支援 (解決策を含む) を求める一連のアクションを作成してあります。

![](../media/tutorial_pw_reset_actions.PNG)

### <a name="training-dialogs"></a>トレーニング会話

トレーニング会話は数多く存在します。 また、ドメイン外クラスのデモンストレーションも存在します。たとえば、"driving directions" といったユーザーの要求はドメイン外となります。ボットには、いくつかのドメイン外要求の例が与えられており、それらに対しては "I can't help with that." で応答します。

![](../media/tutorial_pw_reset_entities.PNG)

例として、学習セッションを試してみましょう。

1. [Train Dialogs]\(トレーニング会話\) をクリックし、[New Train Dialog]\(新しいトレーニング会話\) をクリックします。
1. 「I lost my password」と入力します。
2. [Score Action]\(アクションのスコア付け\) をクリックします。
3. [Is that for your local account or Microsoft account?] をクリックして選択します。
4. 「Local account」と入力します。
5. [Score Action]\(アクションのスコア付け\) をクリックします。
3. [Which version of Windows do you have?] をクリックして選択します。
4. 「Windows 8」と入力します。
5. [Score Action]\(アクションのスコア付け\) をクリックします。
6. [SOLUTION: how to reset password on Windows 8.] を選択します。
4. [Done Teaching]\(学習の完了\) をクリックします。

ドメイン外クラスをボットがどのように学習できるのかを試してみましょう。

1. [Train Dialogs]\(トレーニング会話\) をクリックし、[New Train Dialog]\(新しいトレーニング会話\) をクリックします。
1. 「web search」と入力します。
    - これはドメイン外クラスの例です。 
2. [Score Action]\(アクションのスコア付け\) をクリックします。
3. [Sorry, I can't help with that.] をクリックして選択します。
    - このオプションのスコアが現在は低いことに注目してください。 しかし、少し学習を重ねるとスコアが上昇します。
4. [Done Teaching]\(学習の完了\) をクリックします。

基本的なテクニカル サポートのデモを作成する方法や、解決策を提示するように学習させる方法、例外的な問い合わせに対処する方法を見てきました。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [デモ - ピザの注文](./demo-pizza-order.md)
