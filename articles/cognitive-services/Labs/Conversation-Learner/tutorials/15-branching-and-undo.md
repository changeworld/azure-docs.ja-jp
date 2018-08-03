---
title: Conversation Learner モデルで分岐操作と元に戻す操作を使用する方法 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner モデルで分岐操作と元に戻す操作を使用する方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 05140693026e21a73b756ed0ea7bc9936bef067e
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173300"
---
# <a name="how-to-use-branching-and-undo-operations"></a>分岐操作と元に戻す操作の使用方法
このチュートリアルでは、元に戻す操作と分岐処理について取り上げます。


## <a name="details"></a>詳細
- 元に戻す: 開発者は、ユーザーの入力またはアクションの選択を "元に戻す" ことができます。 "元に戻す" 操作を実行すると、実際にはバックグラウンドで新しい会話が作成され、直前のステップまで再生されます。  つまり、会話の API 呼び出しとエンティティ検出コールバックが再び呼び出されます。

- 分岐: 既存のトレーニング会話と同じ方法で開始される新しいトレーニング会話を作成します。これにより、会話のターンを手動で再入力する手間を省くことができます。 "分岐" 操作を実行すると、バックグラウンドで新しい会話が作成され、選択されたステップまで既存のトレーニング会話が再生されます。  つまり、会話の API 呼び出しとエンティティ検出コールバックが再び呼び出されます。


## <a name="requirements"></a>必要条件
このチュートリアルでは、ピザ注文ボットが実行されている必要があります。

    npm run demo-pizza

### <a name="open-the-demo"></a>デモを開く

Web UI の [モデル] 一覧で、[TutorialDemo Pizza Order] (TutorialDemo ピザの注文) をクリックします。 

ピザの注文デモの詳細については、ピザの注文に関するチュートリアルを参照してください。

## <a name="undo"></a>元に戻す

会話の一部を元に戻し、そのステップから会話を再現します。

### <a name="training-dialogs"></a>トレーニング会話
それでは、トレーニング セッションを開始しましょう。 

1. [Train Dialogs]\(トレーニング会話\) をクリックし、[New Train Dialog]\(新しいトレーニング会話\) をクリックします。
1. 「order a pizza」と入力します。
2. [Score Action]\(アクションのスコア付け\) をクリックします。
3. [what would you like on your pizza?] をクリックして選択します。
4. 「mushrooms and cheese」と入力します。
5. [Score Action]\(アクションのスコア付け\) をクリックします。
3. [you have $Toppings on your pizza] をクリックして選択します。
6. [Would you like anything else?] を選択します。
7. 「remove mushrooms and add peppers」と入力します。
    - [mushrooms] を選択し、Toppings エンティティのチェック ボックスをオフにします。 ここで作成しているのは、元に戻すアクションです。
2. [Undo Step]\(ステップを元に戻す\) をクリックします。
    - 最後のエントリが削除され、'Would you like anything else?' に戻っています。  (以下のスクリーンショットを参照)。
2. 「remove mushrooms and add peppers」と入力します。
8. [you have $Toppings on your pizza] をクリックして選択します。
    - 両方のエンティティが正しく選択されていることを確認します。
2. [Score Action]\(アクションのスコア付け\) をクリックします。 訂正した会話を続行することができます。
4. [Done Teaching]\(学習の完了\) をクリックします。

元に戻す操作を使用して、ユーザーの入力とアクションを取り消す方法を見てきました。

![](../media/tutorial15_undo.PNG)

## <a name="branch"></a>[Branch]\(ブランチ\)

既存のトレーニング会話を開いて、分岐により、もう 1 つトレーニング会話を作成する例を紹介します。

1. [Train Dialogs]\(トレーニング会話\) をクリックし、[new order] をクリックして既存の会話を開きます。 
2. 会話の最後の [no] をクリックします (以下のスクリーンショットを参照)。
3. [分岐] をクリックします。
    - 'no' が削除され、その時点までの会話全体が新しい会話にコピーされます。 
    - そのため、先行するターンを再入力する手間なく、その時点から新しい "分岐" を検討することができます。
1. 「yes」と入力します。
2. [Score Action]\(アクションのスコア付け\) をクリックします。
3. [You have $Toppings on your pizza] を選択します。
6. [Would you like anything else?] を選択します。
7. 「no」と入力します。
4. [Done Teaching]\(学習の完了\) をクリックします。

![](../media/tutorial15_branch.PNG)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [バージョン管理とタグ付け](./16-versioning-and-tagging.md)
