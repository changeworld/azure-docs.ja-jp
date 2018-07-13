---
title: 'Conversation Learner アプリケーションのデモ: ピザの注文 - Microsoft Cognitive Services | Microsoft Docs'
titleSuffix: Azure
description: Conversation Learner のデモ アプリケーションの作成方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 99cd89c4f4430f2d65ed0963e3092d51a83842d7
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376392"
---
# <a name="demo-pizza-order"></a>デモ - ピザの注文
このデモでは、ピザ注文ボットの例を紹介します。 次の機能を使ってピザの単品を注文することができます。

- ユーザーの発話からピザのトッピングを認識する
- ピザのトッピングがストックされているかどうかを確認し、それに応じた応答を返す
- 過去の注文からピザのトッピングを察知して提供する (同じトッピングで新しい注文を開始する)

## <a name="requirements"></a>必要条件
このチュートリアルでは、ピザ注文ボットが実行されている必要があります。

    npm run demo-pizza

### <a name="open-the-demo"></a>デモを開く

Web UI のアプリ一覧で、[TutorialDemo Pizza Order]\(TutorialDemo ピザの注文\) をクリックします。 

## <a name="entities"></a>エンティティ

エンティティは 3 つ作成してあります。

- Toppings: ユーザーから依頼されたトッピングを蓄積します。 ストックされている有効なトッピングが対象となります。 トッピングがストックされているかどうかがチェックされます。
- OutofStock: 選択されたトッピングがストック切れであることをユーザーに伝える目的で使用されます。
- LastToppings: 注文が出された後、このエンティティを使用して、注文されたトッピングの一覧をユーザーに提供します。

![](../media/tutorial_pizza_entities.PNG)

### <a name="actions"></a>アクション

ピザのトッピングをユーザーに尋ねるアクションや、既に追加されたトッピングをユーザーに伝えアクションなど、一連のアクションを作成しました。

また、次の 2 つの API 呼び出しが存在します。

- FinalizeOrder: ピザを注文します。
- UseLastToppings: 前の注文のトッピングを引き継ぎます。 

![](../media/tutorial_pizza_actions.PNG)

### <a name="training-dialogs"></a>トレーニング会話
少数のトレーニング会話を定義しました。 

![](../media/tutorial_pizza_dialogs.PNG)

例として、学習セッションを試してみましょう。

1. [Train Dialogs]\(トレーニング会話\) をクリックし、[New Train Dialog]\(新しいトレーニング会話\) をクリックします。
1. 「order a pizza」と入力します。
2. [Score Action]\(アクションのスコア付け\) をクリックします。
3. [what would you like on your pizza?] をクリックして選択します。
4. 「mushrooms and cheese」と入力します。
    - LUIS により、どちらも Toppings としてラベル付けされていることに注目してください。 これが正しくなかった場合は、クリックして強調表示することで修正できます。
    - エンティティの横にある "+" 記号は、それが一連のトッピングに追加されることを意味します。
5. [Score Action]\(アクションのスコア付け\) をクリックします。
    - Toppings のメモリには、mushrooms と cheese が存在しないことに注目してください。
3. [you have $Toppings on your pizza] をクリックして選択します。
    - これは非待機アクションであるため、ボットから次のアクションを求められることに注目してください。
6. [Would you like anything else?] を選択します。
7. 「remove mushrooms and add peppers」と入力します。
    - **mushroom** は、削除対象であるため、その横に "-" 記号が付いていることに注目してください。 一方、peppers は、トッピングへの追加対象として "+" が付いています。
2. [Score Action]\(アクションのスコア付け\) をクリックします。
    - **peppers** は新たに追加されたものであるため、太字になっていることに注目してください。 また、**mushrooms** は取り消されています。
8. [you have $Toppings on your pizza] をクリックして選択します。
6. [Would you like anything else?] を選択します。
7. 「add peas」と入力します。
    - peas はストック切れのトッピングの例です。 依然としてトッピングとしてラベル付けされていることに注意してください。
2. [Score Action]\(アクションのスコア付け\) をクリックします。
    - peas が OutOfStock として表示されます。
    - その具体的な処理を確認するために、C:\<\installedpath>\src\demos\demoPizzaOrder.ts にあるコードを開いてみましょう。 EntityDetectionCallback メソッドに注目してください。 各トッピングの後にこのメソッドが呼び出されて、ストックされているかどうかが確認されます。 ストック切れのトッピングは、一連のトッピングからクリアされ、OutOfStock エンティティに追加されます。 ストックされているトッピングのリストは、このメソッドの上に定義されている inStock 変数に格納されます。
6. [We don't have $OutOfStock] を選択します。
7. [Would you like anything else?] を選択します。
8. 「no」と入力します。
9. [Score Action]\(アクションのスコア付け\) をクリックします。
10. [FinalizeOrder] という API 呼び出しを選択します。 
    - ここから、コードに定義されている "FinalizeOrder" 関数が呼び出されます。 これにより、toppings がクリアされ、"your order is on its way" が返されます。 
2. 「order another」と入力します。 新しい注文を開始します。
9. [Score Action]\(アクションのスコア付け\) をクリックします。
    - 前回の注文から、cheese と peppers は toppings としてメモリに格納されていることに注意してください。
1. [Would you like $LastToppings] を選択します。
2. 「yes」と入力します。
3. [Score Action]\(アクションのスコア付け\) をクリックします。
    - ボットは UseLastToppings アクションの実行を要求します。 これは 2 つあるコールバック メソッドの 2 つ目です。 前回の注文のトッピングを toppings にコピーして、前回のトッピングをクリアします。 このようにして前回の注文が記憶され、ユーザーがもう 1 枚ピザが欲しいと言った場合には、それらのトッピングがオプションとして提供されます。
2. [you have $Toppings on your pizza] をクリックして選択します。
3. [Would you like anything else?] を選択します。
8. 「no」と入力します。
4. [Done Teaching]\(学習の完了\) をクリックします。

![](../media/tutorial_pizza_callbackcode.PNG)

![](../media/tutorial_pizza_apicalls.PNG)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [デモ - VR アプリ起動ツール](./demo-vr-app-launcher.md)
