---
title: Conversation Learner モデルでセッション コールバックを使用する方法 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner モデルでセッション コールバックを使用する方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 0f51b232470e4e4da3f25d40d025dd3b09dd1204
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171917"
---
# <a name="how-to-use-session-callbacks-with-a-conversation-learner-model"></a>Conversation Learner モデルでセッション コールバックを使用する方法

このチュートリアルでは、onSessionStart コールバックと onSessionEnd コールバックを例示しています。

## <a name="video"></a>ビデオ

[![チュートリアル 11 のプレビュー](http://aka.ms/cl-tutorial-11-preview)](http://aka.ms/blis-tutorial-11)

## <a name="requirements"></a>必要条件
このチュートリアルでは、`tutorialSessionCallbacks` ボットが実行されている必要があります。

    npm run tutorial-session-callbacks

## <a name="details"></a>詳細
このチュートリアルでは、セッションの概念とセッションの既定の処理、さらに、その動作を上書きする方法について取り上げています。

セッションは、ボットとの間でなされる 1 回の会話です。 複数のターンを含むことはありますが、その会話に長時間 (たとえば 30 分) の中断は存在しません。  セッションの既定のタイムアウト期間については、ヘルプの "制限" に関するページを参照してください。

長い中断が存在する場合、ボットは次のセッションに進みます。  新しいセッションを開始すると、再帰型ニューラル ネットワークがその初期状態に設定されます。  既定では、エンティティの値もすべてクリアされますが、この動作は変更することができます (後述)。

### <a name="open-the-demo"></a>デモを開く

[モデル] 一覧で、[Tutorial-11-SessionCallbacks] をクリックします。 

### <a name="entities"></a>エンティティ

このモデルでは、4 つのエンティティが定義されています。

![](../media/tutorial11_entities.PNG)

1 つ注意すべきなのは、BotName がプログラマティック エンティティであるという点です。  このエンティティは、セッションの開始時にボットによって設定されます。

### <a name="actions"></a>アクション

このモデルでは、4 つのアクションが定義されています。

![](../media/tutorial11_actions.PNG)

まず、このチュートリアルでは、セッションの開始時にエンティティの値を制御する方法を説明します (ユーザーが何か言う前に、BotName エンティティを設定するなど)。

次に、セッション間で値を保持する方法について説明します。  このチュートリアルでは、セッション間でユーザーの名前と電話番号は同じであるものの、位置情報は変化する可能性があることを想定しています。  そこで、名前と電話番号についてはセッション間で保持しつつ、ユーザーの位置情報はクリアすることにします。

### <a name="train-dialog"></a>[Train Dialog]\(トレーニング会話\)

以下に会話の例を示します。 これは 1 回のセッションです。つまり、この会話に長い中断はありません。

![](../media/tutorial11_traindialog.PNG)

### <a name="code-for-the-callbacks"></a>コールバックのコード

コールバック メソッドのコードは、c:\<installedpath>\src\demos\tutorialSessionCallbacks.ts というファイルにあります。

![](../media/tutorial11_code.PNG)

どちらのメソッドも省略可能です。

- OnSessionStartCallback: このメソッドは BotName エンティティを設定します。
- OnSessionEndCallback: 保持する内容を指定できます。 この場合、ユーザーの名前と電話番号を除く、すべてのエンティティがクリアされます。

### <a name="try-the-bot"></a>ボットを試す

Web UI に切り替えて、[Log Dialogs]\(会話記録\) をクリックします。

1. 「hello」と入力します。
2. システム: "Hi, I'm Botty.  What's your name?" この中の Botty という名前は、OnSessionStartCallback で設定されたものです。
3. 「jason」と入力します。
4. システム: "Hi jason.  What's your phone number?"
5. 「555-555-5555」と入力します。
6. システム: "Can you tell Botty your location, jason?"
7. 「Redmond」と入力します。

これで 1 セッションです。 新しいセッションを開始するには、このセッションを終了する必要があります。 

1. [Session Timeout]\(セッションのタイムアウト\) をクリックします。 これで次のセッションに切り替わります。
    - [Session Timeout]\(セッションのタイムアウト\)" ボタンは、デバッグ目的で用意されています。  実際のセッションでは、30 分程度の長い一時停止が必要になることも考えられます。  セッションの既定のタイムアウト期間については、ヘルプの "制限" に関するページを参照してください。
1. 「hi」と入力します。
2. システム: "Can you tell Botty your location, jason?"
    - 名前と電話番号は、システムが記憶しています。
2. 新しい位置情報として「Seattle」を入力します。
3. システム: "So, jason you are in Seattle".
4. [Done Testing]\(テストの完了\) をクリックします。

もう一度、[Log Dialogs]\(会話記録\) に切り替えてみましょう。 最後の会話は 2 つに分割されていることに注目してください。これは、それぞれの会話記録が 1 回のセッションに対応するためです。  

![](../media/tutorial11_splitdialogs.PNG)

- 1 回目の対話では、Botty は設定されますが、名前と電話番号は設定されません。
- 2 回目の対話では、名前と電話番号が表示されます。

以上、セッションの既定の処理とその既定の動作を上書きする方法を紹介しました。 

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [API 呼び出し](./12-api-calls.md)
