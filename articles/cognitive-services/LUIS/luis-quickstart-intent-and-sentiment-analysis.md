---
title: 'チュートリアル: 感情分析を返す LUIS アプリを作成する - Azure | Microsoft Docs'
description: このチュートリアルでは、感情分析を LUIS アプリに追加して、ポジティブ、ネガティブ、およびニュートラルな感情について発話を分析する方法を説明します。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: d000637312619fc493e2f7bad8e8edf0d8d0d94b
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265336"
---
# <a name="tutorial-create-app-that-returns-sentiment-along-with-intent-prediction"></a>チュートリアル: 意図の予測と共に感情を返すアプリを作成する
このチュートリアルでは、ポジティブ、ネガティブ、およびニュートラルな感情を発話から抽出する方法を示すアプリを作成します。

<!-- green checkmark -->
> [!div class="checklist"]
> * 階層エンティティおよびコンテキストから学習された子とは 
> * Bookflight 意図の旅行ドメイン用の新しい LUIS アプリを作成する
> * _None_ 意図を追加し、発話の例を追加する
> * 出発地と目的地の子を持つ場所階層エンティティを追加する
> * アプリをトレーニングして公開する
> * アプリのエンドポイントのクエリを行って、階層の子を含む LUIS JSON の応答を確認する 

この記事に従って LUIS アプリケーションを作成するには、無料の [LUIS][LUIS] アカウントが必要です。

## <a name="sentiment-analysis"></a>センチメント分析
感情分析は、ユーザーの発話がポジティブ、ネガティブ、またはニュートラルかを判断する機能です。 

以下の発話は、感情の例を示しています。

|感情とスコア|発話|
|:--|--|
|ポジティブ - 0.89 |スープとサラダ コンボが美味しかった。|
|ネガティブ - 0.07 |ディナー サービスの前菜が好きじゃなかった。|

感情分析は、どの発話にも適用されるアプリ設定として用意されています。 発話の中に感情を示す単語を見つけて、ラベル付けする必要はありません。 LUIS がユーザーに代わってこれを行います。

## <a name="create-a-new-app"></a>新しいアプリの作成
1. [LUIS][LUIS] Web サイトにログインします。 LUIS エンドポイントを公開する必要がある[リージョン][LUIS-regions]にログインします。

2. [LUIS][LUIS] Web サイトで **[新しいアプリの作成]** を選びます。 

    [![](media/luis-quickstart-intent-and-sentiment-analysis/app-list.png "アプリ リスト ページのスクリーンショット")](media/luis-quickstart-intent-and-sentiment-analysis/app-list.png#lightbox)

3. **[新しいアプリの作成]** ダイアログ ボックスで、アプリに `Restaurant Reservations With Sentiment` という名前を付けて、**[完了]** をクリックします。 

    ![[新しいアプリの作成] ダイアログ ボックスの画像](./media/luis-quickstart-intent-and-sentiment-analysis/create-app-ddl.png)

    アプリの作成プロセスが完了すると、LUIS に None 意図を含む意図リストが表示されます。

    [![](media/luis-quickstart-intent-and-sentiment-analysis/intents-list.png "[Intents lists]\(意図リスト\) ページのスクリーンショット")](media/luis-quickstart-intent-and-sentiment-analysis/intents-list.png#lightbox)

## <a name="add-a-prebuilt-domain"></a>事前構築済みのドメインの追加
意図、エンティティ、およびラベル付けされた発話を迅速に追加できるように、事前構築済みのドメインを追加します。

1. 左側のメニューから **[Prebuilt Domains]** \(事前構築済みドメイン\) を選択します。

    [ ![[Prebuilt Domains]\(事前構築済みドメイン\) ボタンのスクリーンショット](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-button-inline.png)](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-button-expanded.png#lightbox)

2. **RestaurantReservation** 事前構築ドメインの **[ドメインの追加]** を選択します。 ドメインが追加されるまで待機します。

    [ ![[Prebuilt Domains]\(事前構築済みドメイン\) リストのスクリーンショット](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-inline.png)](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-expanded.png#lightbox)

3. 左側のナビゲーションで、**[Intents]\(意図\)** を選択します。 この事前構築ドメインには 1 つの意図があります。

    [ ![左側のナビゲーションの [Intents]\(意図\) が強調表示された事前構築済みドメインのスクリーンショット](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-domain-added-expanded.png)](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-domain-added-expanded.png#lightbox)

4.  **RestaurantReservation.Reserve** 意図を選択します。 

    [ ![RestaurantReservation.Reserve が強調表示された [Intents lists]\(意図リスト\) のスクリーンショット](./media/luis-quickstart-intent-and-sentiment-analysis/select-intent.png)](./media/luis-quickstart-intent-and-sentiment-analysis/select-intent.png#lightbox)

5. **[エンティティの表示]** を切り替えると、ラベル付けされたドメイン固有のエンティティを示した多数の発話が表示されます。

    [ ![[トークン] ビューに切り替えられた [エンティティの表示] が強調表示された RestaurantReservation.Reserve 意図のスクリーンショット](./media/luis-quickstart-intent-and-sentiment-analysis/utterance-list-inline.png)](./media/luis-quickstart-intent-and-sentiment-analysis/utterance-list-expanded.png#lightbox)

## <a name="train-the-luis-app"></a>LUIS アプリをトレーニングする
LUIS は、意図やエンティティ (モデル) に対する変更を、トレーニングされるまで認識しません。 

1. LUIS Web サイトの右上にある **[Train]\(トレーニング\)** ボタンを選択します。

    ![強調表示されている [Train]\(トレーニング\) のスクリーンショット](./media/luis-quickstart-intent-and-sentiment-analysis/train-button-expanded.png)

2. 成功したことを示す緑色のステータス バーが Web サイトの上部に表示されたら、トレーニングは完了しています。

    ![トレーニングの成功を示す通知バーのスクリーンショット ](./media/luis-quickstart-intent-and-sentiment-analysis/trained-expanded.png)

## <a name="configure-app-to-include-sentiment-analysis"></a>感情分析を含むアプリを構成する
**[Publish]\(公開\)** ページで、感情分析が有効になっています。 

1. 右上のナビゲーションで **[Publish]\(公開\)** を選択します。

    ![[Publish]\(公開\) ボタンが展開された [Intents]\(意図\) ページのスクリーンショット ](./media/luis-quickstart-intent-and-sentiment-analysis/publish-expanded.png)

2. **[Enable Sentiment Analysis]\(感情分析を有効にする\)** を選択します。

    ![[Enable Sentiment Analysis]\(感情分析を有効にする\) が強調表示された [Publish]\(公開\) ページのスクリーンショット ](./media/luis-quickstart-intent-and-sentiment-analysis/enable-sentiment-expanded.png)

3. [Production]\(運用\) スロットを選択し、**[Publish]\(公開\)** ボタンを選択します。

    [![](media/luis-quickstart-intent-and-sentiment-analysis/publish-to-production-inline.png "運用スロットへの [Publish]\(公開\) ボタンが強調表示された [Publish]\(公開\) ページのスクリーンショット")](media/luis-quickstart-intent-and-sentiment-analysis/publish-to-production-expanded.png#lightbox)

4. 成功したことを示す緑色のステータス バーが Web サイトの上部に表示されたら、公開は完了しています。

## <a name="query-the-endpoint-with-an-utterance"></a>発話を使用してエンドポイントにクエリを実行する

1. **[Publish]\(公開\)** ページで、ページの下部にある**エンドポイント**のリンクを選択します。 別のブラウザー ウィンドウが開き、アドレス バーにエンドポイント URL が表示されます。 

    ![エンドポイントの URL が強調表示された [Publish]\(公開\) ページのスクリーンショット](media/luis-quickstart-intent-and-sentiment-analysis/endpoint-url-inline.png)

2. アドレスの URL の末尾に移動し、「`Reserve table for  10 on upper level away from kitchen`」と入力します。 最後の querystring パラメーターは `q` です。これは発話の**クエリ**です。 この発話はラベル付けされたどの発話とも同じではないので、よいテストであり、感情分析が抽出された `RestaurantReservation.Reserve` 意図を返す必要があります。

```
{
  "query": "Reserve table for 10 on upper level away from kitchen",
  "topScoringIntent": {
    "intent": "RestaurantReservation.Reserve",
    "score": 0.9926384
  },
  "intents": [
    {
      "intent": "RestaurantReservation.Reserve",
      "score": 0.9926384
    },
    {
      "intent": "None",
      "score": 0.00961109251
    }
  ],
  "entities": [],
  "sentimentAnalysis": {
    "label": "neutral",
    "score": 0.5
  }
}
```

## <a name="what-has-this-luis-app-accomplished"></a>この LUIS アプリの処理内容
感情分析が有効になったこのアプリでは、、自然言語クエリの意図を識別し、感情全体をスコアとして含むデータを抽出しました。 

チャットボットは、会話の次のステップを判断することができる十分な情報を取得しまた。 

## <a name="where-is-this-luis-data-used"></a>この LUIS データの使用場所 
LUIS はこの要求の処理を完了しています。 チャットボットなどの呼び出し側アプリは、発話から topScoringIntent の結果と感情データを取得し、次のステップに進むことができます。 LUIS は、ボットや呼び出し側アプリケーションのためにこのようなプログラム作業を実行しません。 LUIS は、ユーザーの意図が何かのみを判断します。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ
不要になったら、LUIS アプリを削除します。 削除するには、アプリ リストのアプリ名の右にある 3 つのドット メニュー (...) を選択し、**[Delete]\(削除\)** を選択します。 **[Delete app?]\(アプリを削除しますか?\)** のポップアップ ダイアログで、**[OK]** を選択します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"] 
> [C# を使って LUIS エンドポイント API を呼び出す](luis-get-started-cs-get-intent.md) 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
