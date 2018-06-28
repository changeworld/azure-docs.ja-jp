---
title: データを抽出するための LUIS アプリ作成のチュートリアル - Azure | Microsoft Docs
description: このチュートリアルでは、意図とシンプルなエンティティを使用して単純な LUIS アプリを作成し、機械学習されたデータを抽出する方法を学習します。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 03/29/2018
ms.author: v-geberr
ms.openlocfilehash: 1e8647e34da3d34946a4f6ac298017f6d4c99de6
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265362"
---
# <a name="tutorial-create-app-that-uses-simple-entity"></a>チュートリアル: シンプルなエンティティを使用するアプリを作成する
このチュートリアルでは、**Simple** エンティティを使用して発話から機械学習されたデータを抽出する方法を示すアプリを作成します。

<!-- green checkmark -->
> [!div class="checklist"]
> * シンプルなエンティティとは 
> * SendMessage 意図での通信ドメイン用の新しい LUIS アプリを作成する
> * _None_ 意図を追加し、発話の例を追加する
> * シンプル エンティティを追加して発話からメッセージの内容を抽出する
> * アプリをトレーニングして公開する
> * アプリのエンドポイントをクエリして LUIS JSON の応答を表示する

この記事に従って LUIS アプリケーションを作成するには、無料の [LUIS][LUIS] アカウントが必要です。

## <a name="purpose-of-the-app"></a>アプリの目的
このアプリでは、発話からデータを取得する方法を示します。 チャットボットからの次の発話について考えます。

```JSON
Send a message telling them to stop
```

意図はメッセージを送信することです。 発話の重要なデータは、メッセージ自体 `telling them to stop` です。  

## <a name="purpose-of-the-simple-entity"></a>シンプル エンティティの目的
シンプル エンティティの目的は、メッセージがどのようなもので、発話のどこで見つけられるかを、LUIS に教えることです。 発話のメッセージ部分は、単語の選択や発話の長さに基づいて、発話ごとに異なります。 LUIS には、すべての意図のすべての発話におけるメッセージの例が必要です。  

この簡単なアプリの場合、メッセージは発話の末尾にあります。 

## <a name="create-a-new-app"></a>新しいアプリの作成
1. [LUIS][LUIS] Web サイトにログインします。 LUIS エンドポイントを公開する必要があるリージョンにログインします。

2. [LUIS][LUIS] Web サイトで **[新しいアプリの作成]** を選びます。  

    ![LUIS アプリの一覧](./media/luis-quickstart-primary-and-secondary-data/app-list.png)

3. ポップアップ ダイアログで、名前に「`MyCommunicator`」と入力します。 

    ![LUIS アプリの一覧](./media/luis-quickstart-primary-and-secondary-data/create-new-app-dialog.png)

4. 終了が処理すると、アプリの **[Intents]\(意図\)** ページに **None** 意図が表示されます。 

    [![](media/luis-quickstart-primary-and-secondary-data/intents-list.png "None 意図の LUIS [Intents]\(意図\) ページのスクリーンショット")](media/luis-quickstart-primary-and-secondary-data/intents-list.png#lightbox)

## <a name="create-a-new-intent"></a>新しい意図を作成する

1. **[Intents]\(意図\)** ページで、**[Create new intent]\(意図の新規作成\)** を選びます。 

    [![](media/luis-quickstart-primary-and-secondary-data/create-new-intent-button.png "[Create new intent]\(意図の新規作成\) ボタンが強調表示されている LUIS のスクリーンショット")](media/luis-quickstart-primary-and-secondary-data/create-new-intent-button.png#lightbox)

2. 新しい意図の名前として「`SendMessage`」と入力します。 ユーザーがメッセージを送信したいときは常に、この意図が選択される必要があります。

    意図を作成すると、識別する情報の主なカテゴリが作成されます。 カテゴリに名前を付けると、LUIS クエリ結果を使用する他のアプリケーションが、そのカテゴリ名を使用して、適切な回答を見つけたり、適切なアクションを実行したりできます。 LUIS はこれらの質問に回答しません。どのような種類の情報が自然言語で質問されたかを明らかにするだけです。 

    ![意図名 SendMessage を入力する](./media/luis-quickstart-primary-and-secondary-data/create-new-intent-popup-dialog.png)

3. ユーザーの要求として予想される 7 つの発話を `SendMessage` 意図に追加します。次はその例です。

    | 発話の例|
    |--|
    |Reply with I got your message, I will have the answer tomorrow (受け取ったメッセージへの返答、明日お答えします)|
    |Send message of When will you be home? (メッセージの送信、いつ家にいますか?)|
    |Text that I am busy (テキスト、私は忙しい)|
    |Tell them that it needs to be done today (彼らに連絡、今日中に終える必要があります)|
    |IM that I am driving and will respond later (IM、今運転中なので後で連絡します)|
    |Compose message to David that says When was that? (David へのメッセージの作成、それはいつでしたか?)|
    |say greg hello (greg への伝言、こんにちは)|

    [![](media/luis-quickstart-primary-and-secondary-data/enter-utterances-on-intent-page.png "発話が入力された LUIS のスクリーンショット")](media/luis-quickstart-primary-and-secondary-data/enter-utterances-on-intent-page.png#lightbox)

## <a name="add-utterances-to-none-intent"></a>None 意図に発話を追加する

現在、この LUIS アプリの **None** 意図には発話はありません。 アプリに応答させたくない発話が必要なので、**None** 意図に発話を追加します。 空白のままにしないでください。 
    
1. 左側のパネルから **[Intents]\(意図\)** を選びます。 

    [![](media/luis-quickstart-primary-and-secondary-data/select-intent-link.png "[Intents]\(意図\) ボタンが強調表示されている LUIS のスクリーンショット")](media/luis-quickstart-primary-and-secondary-data/select-intent-link.png#lightbox)

2. **None** 意図を選択します。 

    [![](media/luis-quickstart-primary-and-secondary-data/select-none-intent.png "None 意図選択のスクリーンショット")](media/luis-quickstart-primary-and-secondary-data/select-none-intent.png#lightbox)

3. ユーザーが入力する可能性があるがアプリには関係のない 3 つの発話を追加します。 適切な **None** 発話の例を次に示します。

    | 発話の例|
    |--|
    |キャンセル|
    |さようなら|
    |どうなっているの|
    
    LUIS 呼び出し元アプリケーション (チャットボットなど) では、LUIS が発話に対して **None** 意図を返したら、ユーザーが会話の終了を望んでいるかどうかを確認できます。 また、ユーザーが終了を望んでいない場合は、会話を続けるよう指示することもできます。 

    [![](media/luis-quickstart-primary-and-secondary-data/utterances-for-none-intent.png "None 意図の発話が表示された LUIS のスクリーンショット")](media/luis-quickstart-primary-and-secondary-data/utterances-for-none-intent.png#lightbox)

## <a name="create-a-simple-entity-to-extract-message"></a>メッセージを抽出するためのシンプル エンティティを作成する 
1. 左側のメニューから **[Intents]\(意図\)** を選びます。

    ![[Intents]\(意図\) リンクを選択する](./media/luis-quickstart-primary-and-secondary-data/select-intents-from-none-intent.png)

2. 意図の一覧から `SendMessage` を選びます。

    ![SendMessage 意図を選択する](./media/luis-quickstart-primary-and-secondary-data/select-sendmessage-intent.png)

3. 発話 `Reply with I got your message, I will have the answer tomorrow` で、メッセージ本文の最初の単語 `I` と、メッセージ本文の最後の単語 `tomorrow` を選択します。 これらの単語すべてがメッセージとして選択され、上部にテキスト ボックスのあるドロップダウン メニューが表示されます。

    [![](media/luis-quickstart-primary-and-secondary-data/select-words-in-utterance.png "発話内のメッセージの単語を選択するスクリーンショット")](media/luis-quickstart-primary-and-secondary-data/select-words-in-utterance.png#lightbox)

4. エンティティ名としてテキスト ボックスに「`Message`」と入力します。

    [![](media/luis-quickstart-primary-and-secondary-data/enter-entity-name-in-box.png "エンティティ名をボックスに入力したスクリーンショット")](media/luis-quickstart-primary-and-secondary-data/enter-entity-name-in-box.png#lightbox)

5. ドロップダウン メニューで **[Create new entity]\(新しいエンティティの作成\)** を選択します。 このエンティティの目的は、メッセージの本文のテキストを抽出することです。 この LUIS アプリでは、テキスト メッセージは発話の末尾にありますが、発話とメッセージはどのような長さでもかまいません。 

    [![](media/luis-quickstart-primary-and-secondary-data/create-message-entity.png "発話から新しいエンティティを作成するスクリーンショット")](media/luis-quickstart-primary-and-secondary-data/create-message-entity.png#lightbox)

6. ポップアップ ウィンドウで、既定のエンティティの種類は **[Simple]\(シンプル\)**、エンティティ名は `Message` になっています。 これらの設定のままにして、**[Done]\(完了\)** を選択します。

    ![エンティティの種類を確認する](./media/luis-quickstart-primary-and-secondary-data/entity-type.png)

7. エンティティを作成し、1 つ発話にラベルを付けたので、そのエンティティの残りの発話にラベルを付けます。 発話を選択し、メッセージの最初の単語と最後の単語を選択します。 ドロップダウン メニューで、エンティティ `Message` を選択します。 エンティティでメッセージにラベルが付けられました。 続けて、残りの発話のすべてのメッセージ フレーズにラベルを付けます。

    [![](media/luis-quickstart-primary-and-secondary-data/all-labeled-utterances.png "すべてのメッセージ発話にラベルが付いたスクリーンショット")](media/luis-quickstart-primary-and-secondary-data/all-labeled-utterances.png#lightbox)

    発話の既定のビューは**エンティティ ビュー**です。 発話の上の **[Entities view]\(エンティティ ビュー\)** コントロールを選択します。 **トークン ビュー**に発話のテキストが表示されます。 

    [![](media/luis-quickstart-primary-and-secondary-data/tokens-view-of-utterances.png "トークン ビューの発話のスクリーンショット")](media/luis-quickstart-primary-and-secondary-data/tokens-view-of-utterances.png#lightbox)

## <a name="train-the-luis-app"></a>LUIS アプリをトレーニングする
LUIS は、意図やエンティティ (モデル) に対する変更を、トレーニングされるまで認識しません。 

1. LUIS Web サイトの右上にある **[Train]\(トレーニング\)** ボタンを選択します。

    ![[Train]\(トレーニング\) ボタンを選択する](./media/luis-quickstart-primary-and-secondary-data/train-button.png)

2. 成功したことを示す緑色のステータス バーが Web サイトの上部に表示されたら、トレーニングは完了しています。

    ![トレーニング成功通知](./media/luis-quickstart-primary-and-secondary-data/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>アプリを公開してエンドポイント URL を取得する
チャットボットや他のアプリケーションで LUIS の予測を取得するには、アプリを公開する必要があります。 

1. LUIS Web サイトの右上にある **[Publish]\(公開\)** ボタンを選択します。 

2. [Production]\(運用\) スロットを選択し、**[Publish]\(公開\)** ボタンを選択します。

    [![](media/luis-quickstart-primary-and-secondary-data/publish-to-production.png "運用スロットへの [Publish]\(公開\) ボタンが強調表示された [公開] ページのスクリーンショット")](media/luis-quickstart-primary-and-secondary-data/publish-to-production.png#lightbox)

3. 成功したことを示す緑色のステータス バーが Web サイトの上部に表示されたら、公開は完了しています。

## <a name="query-the-endpoint-with-a-different-utterance"></a>異なる発話でエンドポイントにクエリを行う
**[Publish]\(公開\)** ページで、ページの下部にある**エンドポイント**のリンクを選択します。 

[![](media/luis-quickstart-primary-and-secondary-data/publish-select-endpoint.png "エンドポイントが強調表示された [Publish]\(公開\) ページのスクリーンショット")](media/luis-quickstart-primary-and-secondary-data/publish-select-endpoint.png#lightbox)

別のブラウザー ウィンドウが開き、アドレス バーにエンドポイント URL が表示されます。 アドレスの URL の末尾に移動し、「`text I'm driving and will be 30 minutes late to the meeting`」と入力します。 最後の querystring パラメーターは `q` です。これは発話の**クエリ**です。 この発話はラベル付けされたどの発話とも同じではないので、よいテストであり、`SendMessage` 発話が返される必要があります。

```
{
  "query": "text I'm driving and will be 30 minutes late to the meeting",
  "topScoringIntent": {
    "intent": "SendMessage",
    "score": 0.987501
  },
  "intents": [
    {
      "intent": "SendMessage",
      "score": 0.987501
    },
    {
      "intent": "None",
      "score": 0.111048922
    }
  ],
  "entities": [
    {
      "entity": "i ' m driving and will be 30 minutes late to the meeting",
      "type": "Message",
      "startIndex": 5,
      "endIndex": 58,
      "score": 0.162995353
    }
  ]
}
```

## <a name="what-has-this-luis-app-accomplished"></a>この LUIS アプリの処理内容
2 つだけの意図と 1 つのエンティティで構成されるこのアプリは、自然言語クエリの意図を識別し、メッセージ データを返しました。 

JSON の結果では、意図 `SendMessage` が最高のスコア 0.987501 になったことが示されています。 すべてのスコアは 0 から 1 の範囲であり、1 に近いほどよいスコアです。 `None` 意図のスコアは 0.111048922 であり、0 に非常に近い値です。 

メッセージ データの種類は `Message`、値は `i ' m driving and will be 30 minutes late to the meeting` です。 

チャットボットは、主要なアクション `SendMessage` を決定するのに十分な情報と、そのアクションのパラメーターであるメッセージ テキストを取得します。 

## <a name="where-is-this-luis-data-used"></a>この LUIS データの使用場所 
LUIS はこの要求の処理を完了しています。 チャットボットなどの呼び出し元アプリは、エンティティから topScoringIntent の結果とデータを取得し、サード パーティの API を使ってメッセージを送信できます。 ボットまたは呼び出し元アプリケーションに他のプログラム オプションがある場合でも、LUIS はその処理を行いません。 LUIS は、ユーザーの意図が何かのみを判断します。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ
不要になったら、LUIS アプリを削除します。 削除するには、アプリ リストのアプリ名の右にある 3 つのドット メニュー [...] を選択し、**[Delete]\(削除\)** を選択します。 **[Delete app?]\(アプリを削除しますか?\)** のポップアップ ダイアログで、**[OK]** を選択します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [階層エンティティを追加する方法を確認する](luis-quickstart-intent-and-hier-entity.md)


<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
