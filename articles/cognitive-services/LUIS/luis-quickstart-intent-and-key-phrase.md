---
title: 'チュートリアル: キー フレーズを返す LUIS アプリを作成する - Azure | Microsoft Docs'
description: このチュートリアルでは、LUIS アプリに keyPhrase エンティティを追加して返し、主題の発話を分析する方法について説明します。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 12c306b5199da5862302c28d1690b81c6e1edb0e
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264617"
---
# <a name="tutorial-create-app-that-returns-keyphrases-entity-data-found-in-utterances"></a>チュートリアル: 発話で見つかった keyPhrases エンティティ データを返すアプリを作成する
このチュートリアルでは、発話から主題を抽出する方法を示すアプリを作成します。

<!-- green checkmark -->
> [!div class="checklist"]
> * keyPhrase エンティティの概要 
> * 人事ドメイン用の新しい LUIS アプリを作成する
> * _None_ 意図を追加し、発話の例を追加する
> * 発話からコンテンツを抽出する keyPhrase エンティティを追加する
> * アプリをトレーニングして公開する
> * アプリのエンドポイントをクエリして LUIS JSON の応答を表示する

この記事に従って LUIS アプリケーションを作成するには、無料の [LUIS][LUIS] アカウントを利用できます。

## <a name="keyphrase-entity-extraction"></a>keyPhrase エンティティの抽出
主題は、事前構築済みエンティティ **keyPhrase** によって提供されます。 このエンティティは、発話に含まれる主題を返します

以下の発話は、キー フレーズの例です。

|発話|keyPhrase エンティティ値|
|--|--|
|来年提供される控除額が低い新しい医療計画はありますか?|"控除額が低い"<br>"新しい医療計画"<br>"年"|
|視覚療法は高額の控除が可能な医療計画に含まれていますか?|"高額の控除が可能な医療計画"<br>"視覚療法"|

チャットボットでは、会話の次のステップを決定するときに、抽出された他のすべてのエンティティに加えて、これらの値を考慮することができます。

## <a name="download-sample-app"></a>サンプル アプリをダウンロードする
[人事](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/HumanResources.json)アプリをダウンロードして、拡張子が *.json のファイルに保存します。 このサンプル アプリは、従業員の給付、組織図、および物理的資産に関連する発話を認識します。

## <a name="create-a-new-app"></a>新しいアプリの作成
1. [LUIS][LUIS] Web サイトにログインします。 LUIS エンドポイントを公開する必要がある[リージョン][LUIS-regions]にログインします。

2. [LUIS][LUIS] Web サイトで **[Import new app]\(新しいアプリのインポート\)** を選択して、前のセクションでダウンロードした人事アプリをインポートします。 

    [![](media/luis-quickstart-intent-and-key-phrase/app-list.png "アプリ リスト ページのスクリーンショット")](media/luis-quickstart-intent-and-key-phrase/app-list.png#lightbox)

3. **[Import new app]\(新しいアプリのインポート\)** ダイアログ ボックスで、アプリに `Human Resources with Key Phrase entity` と名前を付けます。 

    ![[Import new app]\(新しいアプリのインポート\) ダイアログ ボックスの画像](./media/luis-quickstart-intent-and-key-phrase/import-new-app-inline.png)

    アプリの作成プロセスが完了すると、LUIS に意図リストが表示されます。

    [![](media/luis-quickstart-intent-and-key-phrase/intents-list.png "[Intents lists]\(意図リスト\) ページのスクリーンショット")](media/luis-quickstart-intent-and-key-phrase/intents-list.png#lightbox)

## <a name="add-keyphrase-entity"></a>keyPhrase エンティティを追加する 
発話から主題を抽出する keyPhrase 事前構築済みエンティティを追加します。

1. 左側のメニューから **[Entities]\(エンティティ\)** を選択します。

    [ ![[ビルド] セクションの左側のナビゲーションで強調表示されている [Entities]\(エンティティ\) スクリーンショット](./media/luis-quickstart-intent-and-key-phrase/select-entities.png)](./media/luis-quickstart-intent-and-key-phrase/select-entities.png#lightbox)

2. **[Manage prebuilt entities]\(事前構築済みエンティティの管理\)** を選択します。

    [ ![[Entities]\(エンティティ\) リストのポップアップ ダイアログのスクリーンショット](./media/luis-quickstart-intent-and-key-phrase/manage-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/manage-prebuilt-entities.png#lightbox)

3. ポップアップ ダイアログで **[keyPhrase]** を選択し、**[完了]** を選択します。 

    [ ![[Entities]\(エンティティ\) リストのポップアップ ダイアログのスクリーンショット](./media/luis-quickstart-intent-and-key-phrase/add-or-remove-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/add-or-remove-prebuilt-entities.png#lightbox)

    <!-- TBD: asking Carol
    You won't see these entities labeled in utterances on the intents pages. 
    -->

## <a name="train-the-luis-app"></a>LUIS アプリをトレーニングする
LUIS は、トレーニングされるまで、モデルに加えられたこの変更を認識していません。 

1. LUIS Web サイトの右上にある **[Train]\(トレーニング\)** ボタンを選択します。

    ![強調表示されている [Train]\(トレーニング\) のスクリーンショット](./media/luis-quickstart-intent-and-key-phrase/train-button-expanded.png)

2. 成功したことを示す緑色のステータス バーが Web サイトの上部に表示されたら、トレーニングは完了しています。

    ![トレーニングの成功を示す通知バーのスクリーンショット ](./media/luis-quickstart-intent-and-key-phrase/trained-inline.png)

## <a name="publish-app-to-endpoint"></a>エンドポイントにアプリを公開する

1. 右上のナビゲーションで **[Publish]\(公開\)** を選択します。

    ![[Publish]\(公開\) ボタンが展開された [Entities]\(エンティティ\) ページのスクリーンショット ](./media/luis-quickstart-intent-and-key-phrase/publish-expanded.png)

2. [Production]\(運用\) スロットを選択し、**[Publish]\(公開\)** ボタンを選択します。

    [![](media/luis-quickstart-intent-and-key-phrase/publish-to-production-inline.png "運用スロットへの [Publish]\(公開\) ボタンが強調表示された [公開] ページのスクリーンショット")](media/luis-quickstart-intent-and-key-phrase/publish-to-production-expanded.png#lightbox)

3. 成功したことを示す緑色のステータス バーが Web サイトの上部に表示されたら、公開は完了しています。

## <a name="query-the-endpoint-with-an-utterance"></a>発話を使用してエンドポイントにクエリを実行する

1. **[Publish]\(公開\)** ページで、ページの下部にある**エンドポイント**のリンクを選択します。 別のブラウザー ウィンドウが開き、アドレス バーにエンドポイント URL が表示されます。 

    ![エンドポイントの URL が強調表示された [Publish]\(公開\) ページのスクリーンショット](media/luis-quickstart-intent-and-key-phrase/endpoint-url-inline.png )

2. アドレスの URL の末尾に移動し、「`Is there a new medical plan with a lower deductible offered next year?`」と入力します。 最後の querystring パラメーターは `q` です。これは発話の**クエリ**です。 

```
{
  "query": "Is there a new medical plan with a lower deductible offered next year?",
  "topScoringIntent": {
    "intent": "FindForm",
    "score": 0.216838628
  },
  "entities": [
    {
      "entity": "lower deductible",
      "type": "builtin.keyPhrase",
      "startIndex": 35,
      "endIndex": 50
    },
    {
      "entity": "new medical plan",
      "type": "builtin.keyPhrase",
      "startIndex": 11,
      "endIndex": 26
    },
    {
      "entity": "year",
      "type": "builtin.keyPhrase",
      "startIndex": 65,
      "endIndex": 68
    }
  ]
}
```

## <a name="what-has-this-luis-app-accomplished"></a>この LUIS アプリの処理内容
このアプリは、keyPhrase エンティティの検出を使用し、自然言語のクエリの意図を特定し、主題を含む抽出されたデータを返しました。 

チャットボットは、会話の次のステップを判断することができる十分な情報を取得しまた。 

## <a name="where-is-this-luis-data-used"></a>この LUIS データの使用場所 
LUIS はこの要求の処理を完了しています。 チャットボットなどの呼び出し側アプリは、発話から topScoringIntent の結果と keyPhrase データを取得し、次のステップに進むことができます。 LUIS は、ボットや呼び出し側アプリケーションのためにこのようなプログラム作業を実行しません。 LUIS は、ユーザーの意図が何かのみを判断します。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ
不要になったら、LUIS アプリを削除します。 削除するには、アプリ リストのアプリ名の右にある 3 つのドット メニュー (...) を選択し、**[Delete]\(削除\)** を選択します。 **[Delete app?]\(アプリを削除しますか?\)** のポップアップ ダイアログで、**[OK]** を選択します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [意図の予測と共にセンチメントを返すアプリを作成する](luis-quickstart-intent-and-sentiment-analysis.md)

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
