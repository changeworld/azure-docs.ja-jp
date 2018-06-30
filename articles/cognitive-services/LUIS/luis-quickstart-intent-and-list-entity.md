---
title: 完全なテキスト一致リスト データを取得するための LUIS アプリを作成するチュートリアル - Azure | Microsoft Docs
description: このチュートリアルでは、意図とリスト エンティティを使用して簡単な LUIS アプリを作成し、このクイック スタートのデータを抽出する方法を学習します。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 33394dff1091f27c79c74d8648a90724ba8d6698
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264829"
---
# <a name="tutorial-create-app-using-a-list-entity"></a>チュートリアル: リスト エンティティを使用してアプリを作成する
このチュートリアルでは、定義済みのリストに一致するデータを取得する方法を示すアプリを作成します。 

<!-- green checkmark -->
> [!div class="checklist"]
> * リスト エンティティを理解する 
> * OrderDrinks 意図を使用して飲み物ドメイン用の新しい LUIS アプリを作成する
> * _None_ 意図を追加し、発話の例を追加する
> * 発話から飲み物項目を抽出するリスト エンティティを追加する
> * アプリをトレーニングして公開する
> * アプリのエンドポイントをクエリして LUIS JSON の応答を表示する

この記事に従って LUIS アプリケーションを作成するには、無料の [LUIS][LUIS] アカウントが必要です。

## <a name="purpose-of-the-list-entity"></a>リスト エンティティの目的
このアプリは、`1 coke and 1 milk please` のような飲み物の注文を受け取って、飲み物の種類などのデータを返します。 飲み物の**リスト** エンティティは、完全なテキスト一致を探して返します。 

リスト エンティティは、データ値が既知のセットであるデータの種類に適しています。 飲み物の名前はスラングや略語を含めてさまざまですが、頻繁に変わることはありません。 

## <a name="app-intents"></a>アプリの意図
意図は、ユーザーが望むカテゴリです。 このアプリには、OrderDrink と None の 2 つの意図があります。 [None](luis-concept-intent.md#none-intent-is-fallback-for-app) 意図には、アプリの外部のすべてのものを示す目的があります。  

## <a name="list-entity-is-an-exact-text-match"></a>リスト エンティティは完全なテキスト一致である
エンティティの目的は、発話内のテキストの一部分を検索して分類することです。 [リスト](luis-concept-entity-types.md) エンティティは、語句の完全一致に対応します。  

この飲み物アプリでは、LUIS は標準的な注文を作成して実現できるような方法で飲み物の注文を抽出します。 LUIS では、発話のバリエーション、省略形、俗語に含まれていてもかまいません。 

ユーザーからの簡単な発話の例を次に示します。

```
2 glasses of milk
3 bottles of water
2 cokes
```

発話の省略形または俗語バージョンを次に示します。

```
5 milk
3 h2o
1 pop
```
 
リスト エンティティは、水を意味する `h2o` やソフト ドリンクを示す `pop` と一致します。  

## <a name="what-luis-does"></a>LUIS の機能
発話の意図とエンティティが識別され、[抽出](luis-concept-data-extraction.md#list-entity-data)されて、[エンドポイント](https://aka.ms/luis-endpoint-apis)から JSON で返されると、LUIS は終了します。 呼び出し元のアプリケーションやチャットボットは、その JSON 応答を受け取って、それぞれの設計された方法で要求を満たします。 

## <a name="create-a-new-app"></a>新しいアプリの作成
1. [LUIS][LUIS] Web サイトにログインします。 LUIS エンドポイントを公開する必要がある[リージョン][LUIS-regions]にログインします。

2. [LUIS][LUIS] Web サイトで **[新しいアプリの作成]** を選びます。  

    ![新しいアプリの作成](./media/luis-quickstart-intent-and-list-entity/app-list.png)

3. ポップアップ ダイアログで、名前に「`MyDrinklist`」と入力します。 

    ![アプリの名前を MyDrinkList にする](./media/luis-quickstart-intent-and-list-entity/create-app-dialog.png)

4. 終了が処理すると、アプリの **[Intents]\(意図\)** ページに **None** 意図が表示されます。 

    [![](media/luis-quickstart-intent-and-list-entity/intents-page-none-only.png "[Intents]\(意図\) ページのスクリーンショット")](media/luis-quickstart-intent-and-list-entity/intents-page-none-only.png#lightbox)

## <a name="create-a-new-intent"></a>新しい意図を作成する

1. **[Intents]\(意図\)** ページで、**[Create new intent]\(意図の新規作成\)** を選びます。 

    [![](media/luis-quickstart-intent-and-list-entity/create-new-intent.png "[Create new intent]\(意図の新規作成\) ボタンが強調表示されてい [Intents]\(意図\) ページのスクリーンショット")](media/luis-quickstart-intent-and-list-entity/create-new-intent.png#lightbox)

2. 新しい意図の名前として「`OrderDrinks`」と入力します。 ユーザーが飲み物を注文したいときは常に、この意図が選択される必要があります。

    意図を作成すると、識別する情報の主なカテゴリが作成されます。 カテゴリに名前を付けると、LUIS クエリ結果を使用する他のアプリケーションが、そのカテゴリ名を使用して、適切な回答を見つけたり、適切なアクションを実行したりできます。 LUIS はこれらの質問に回答しません。どのような種類の情報が自然言語で質問されたかを明らかにするだけです。 

    [![](media/luis-quickstart-intent-and-list-entity/intent-create-dialog-order-drinks.png "新しい OrderDrinks 意図の作成のスクリーンショット")](media/luis-quickstart-intent-and-list-entity/intent-create-dialog-order-drinks.png#lightbox)

3. ユーザーの質問として予想される複数の発話を `OrderDrinks` 意図に追加します。次はその例です。

    | 発話の例|
    |--|
    |コークを 2 杯と水を 1 本、私の部屋に運んでください|
    |ライムのツイスト付きのペリエを 2 杯|
    |H2O|

    [![](media/luis-quickstart-intent-and-list-entity/intent-order-drinks-utterance.png "OrderDrinks 意図ページでの発話入力のスクリーンショット")](media/luis-quickstart-intent-and-list-entity/intent-order-drinks-utterance.png#lightbox)

## <a name="add-utterances-to-none-intent"></a>None 意図に発話を追加する

現在、この LUIS アプリの **None** 意図には発話はありません。 アプリに応答させたくない発話が必要なので、**None** 意図に発話を追加します。 空白のままにしないでください。 

1. 左側のパネルから **[Intents]\(意図\)** を選びます。 

    [![](media/luis-quickstart-intent-and-list-entity/left-panel-intents.png "左側のパネルから [Intents]\(意図\) リンクを選択したスクリーンショット")](media/luis-quickstart-intent-and-list-entity/left-panel-intents.png#lightbox)

2. **None** 意図を選択します。 ユーザーが入力する可能性があるがアプリには関係のない 3 つの発話を追加します。

    | 発話の例|
    |--|
    |キャンセル|
    |さようなら|
    |どうなっているの|

## <a name="when-the-utterance-is-predicted-for-the-none-intent"></a>None 意図の発話が予想されるとき
LUIS 呼び出し元アプリケーション (チャットボットなど) では、LUIS が発話に対して **None** 意図を返したら、ユーザーが会話の終了を望んでいるかどうかを確認できます。 また、ユーザーが終了を望んでいない場合は、会話を続けるよう指示することもできます。 

エンティティは **None** 意図で動作します。 最もスコアの高い意図が **None** であり、しかしチャットボットにとって意味のあるエンティティが抽出された場合、チャットボットはさらに質問して客の意図を絞り込むことができます。 

## <a name="create-a-menu-entity-from-the-intent-page"></a>意図ページからメニュー エンティティを作成する
2 つの意図に発話を追加したので、LUIS は飲み物が何かを理解する必要があります。 `OrderDrinks` 意図に戻り、以下の手順に従って発話内の飲み物にラベル (マーク) を付けます。

1. 左側のパネルで **[Intents]\(意図\)** を選択して `OrderDrinks` 意図に戻ります。

2. 意図の一覧から `OrderDrinks` を選びます。

3. 発話 `Please send 2 cokes and a bottle of water to my room` において、単語 `water` を選びます。 ドロップダウン メニューが表示され、上部にあるテキスト ボックスで新しいエンティティを作成できます。 テキスト ボックスにエンティティ名「`Drink`」を入力し、を選択し、ドロップダウン メニューで **[Create new entity]\(新しいエンティティの作成\)** を選択します。 

    [![](media/luis-quickstart-intent-and-list-entity/intent-label-h2o-in-utterance.png "発話で単語を選択して新しいエンティティを作成するスクリーンショット")](media/luis-quickstart-intent-and-list-entity/intent-label-h2o-in-utterance.png#lightbox)

4. ポップアップ ウィンドウで、**[List]\(リスト\)** エンティティ タイプを選択します。 シノニム `h20` を追加します。 各シノニムの後で Enter キーを押します。 シノニムのリストに `perrier` を追加しないでください。 次のステップで例として追加します。 **[完了]** を選択します。

    [![](media/luis-quickstart-intent-and-list-entity/create-list-ddl.png "新しいエンティティの構成のスクリーンショット")](media/luis-quickstart-intent-and-list-entity/create-list-ddl.png#lightbox)

5. エンティティが作成されたので、水のシノニムを選択し、ドロップダウン リストで `Drink` を選択することにより、水の他のシノニムにラベルを付けます。 右側のメニューに従い、[`Set as synonym`] を選択してから、`water` を選択します。

    [![](media/luis-quickstart-intent-and-list-entity/intent-label-perriers.png "既存のエンティティで発話にラベルを付けるスクリーンショット")](media/luis-quickstart-intent-and-list-entity/intent-label-perriers.png#lightbox)

## <a name="modify-the-list-entity-from-the-entity-page"></a>[Entity]\(エンティティ\) ページからリスト エンティティを変更する
飲み物のリスト エンティティは作成されていますが、項目とシノニムはあまりありません。 わかっている用語、省略形、俗語がある場合は、**[Entity]\(エンティティ\)** ページでリストに追加するのが簡単です。 

1. 左側のパネルから **[Entities]\(エンティティ\)** を選びます。

    [![](media/luis-quickstart-intent-and-list-entity/intent-select-entities.png "左側のパネルで [Entities]\(エンティティ\) を選択したスクリーンショット")](media/luis-quickstart-intent-and-list-entity/intent-select-entities.png#lightbox)

2. エンティティの一覧から `Drink` を選びます。

    [![](media/luis-quickstart-intent-and-list-entity/entities-select-drink-entity.png "エンティティ一覧から Drink エンティティを選択したスクリーンショット")](media/luis-quickstart-intent-and-list-entity/entities-select-drink-entity.png#lightbox)

3. テキスト ボックスに「`Soda pop`」と入力して Enter キーを押します。 これは、炭酸飲料に広く適用される用語です。 すべての文化には、この種の飲み物に対するニックネームや俗語があります。

    [![](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-canonical-name.png "正規の名前を入力するスクリーンショット")](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-canonical-name.png#lightbox)

4. `Soda pop` と同じ行に、次のようなシノニムを入力します。 

    ```
    coke
    cokes
    coca-cola
    coca-colas
    ```

    シノニムには、語句、句読点、所有格、複数形を含めることができます。 リスト エンティティは完全なテキスト一致なので (大文字と小文字の区別を除きます)、シノニムにはすべてのバリエーションを設定する必要があります。 クエリ ログやエンドポイント ヒットからさらに多くのバリエーションを学習して、一覧を増やすことができます。 

    この記事では、例を短くしておきたいので少数のシノニムに留めます。 運用レベルの LUIS アプリでは、多くのシノニムを登録し、定期的に確認および拡充します。 

    [![](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-synonyms.png "シノニム追加のスクリーンショット")](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-synonyms.png#lightbox)

## <a name="train-the-luis-app"></a>LUIS アプリをトレーニングする
LUIS は、意図やエンティティ (モデル) に対する変更を、トレーニングされるまで認識しません。 

1. LUIS Web サイトの右上にある **[Train]\(トレーニング\)** ボタンを選択します。

    ![アプリをトレーニングする](./media/luis-quickstart-intent-and-list-entity/train-button.png)

2. 成功したことを示す緑色のステータス バーが Web サイトの上部に表示されたら、トレーニングは完了しています。

    ![トレーニング成功](./media/luis-quickstart-intent-and-list-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>アプリを公開してエンドポイント URL を取得する
チャットボットや他のアプリケーションで LUIS の予測を取得するには、アプリを公開する必要があります。 

1. LUIS Web サイトの右上にある **[Publish]\(公開\)** ボタンを選択します。 

    [![](media/luis-quickstart-intent-and-list-entity/publish.png "公開ボタン選択のスクリーンショット")](media/luis-quickstart-intent-and-list-entity/publish.png#lightbox)

2. [Production]\(運用\) スロットを選択し、**[Publish]\(公開\)** ボタンを選択します。 

    [![](media/luis-quickstart-intent-and-list-entity/publish-to-production.png "運用への公開スロット ボタンのスクリーンショット")](media/luis-quickstart-intent-and-list-entity/publish-to-production.png#lightbox)

3. 成功したことを示す緑色のステータス バーが Web サイトの上部に表示されたら、公開は完了しています。

## <a name="query-the-endpoint-with-a-different-utterance"></a>異なる発話でエンドポイントにクエリを行う
1. **[Publish]\(公開\)** ページで、ページの下部にある**エンドポイント**のリンクを選択します。 別のブラウザー ウィンドウが開き、アドレス バーにエンドポイント URL が表示されます。 

    [![](media/luis-quickstart-intent-and-list-entity/publish-select-endpoint.png "[Publish]\(公開\) ページのエンドポイント URL のスクリーンショット")](media/luis-quickstart-intent-and-list-entity/publish-select-endpoint.png#lightbox)

2. アドレスで URL の末尾に移動し、「`2 cokes and 3 waters`」と入力します。 最後の querystring パラメーターは `q` であり、utterance **q**uery (発話クエリ) を示します。 この発話はラベル付けされたどの発話とも同じではないので、よいテストであり、`OrderDrinks` 意図と 2 つの飲み物の種類 `cokes` と `waters` が返される必要があります。

```
{
  "query": "2 cokes and 3 waters",
  "topScoringIntent": {
    "intent": "OrderDrinks",
    "score": 0.999998569
  },
  "intents": [
    {
      "intent": "OrderDrinks",
      "score": 0.999998569
    },
    {
      "intent": "None",
      "score": 0.23884207
    }
  ],
  "entities": [
    {
      "entity": "cokes",
      "type": "Drink",
      "startIndex": 2,
      "endIndex": 6,
      "resolution": {
        "values": [
          "Soda pop"
        ]
      }
    },
    {
      "entity": "waters",
      "type": "Drink",
      "startIndex": 14,
      "endIndex": 19,
      "resolution": {
        "values": [
          "h20"
        ]
      }
    }
  ]
}
```

## <a name="where-is-the-natural-language-processing-in-the-list-entity"></a>リスト エンティティでの自然言語処理の場所 
リスト エンティティは完全テキスト一致なので、自然言語処理 (または機械学習) には依存しません。 LUIS は、自然言語処理 (または機械学習) を使って、正しいトップ スコア意図を選択します。 また、発話には、複数のエンティティや、さらには複数の種類のエンティティが含まれていてもかまいません。 各発話は、**Simple** エンティティなどの自然言語処理 (または機械学習) エンティティも含めて、アプリ内のすべてのエンティティについて処理されます。

## <a name="what-has-this-luis-app-accomplished"></a>この LUIS アプリが実現したこと
2 つだけの意図とリスト エンティティで構成されるこのアプリは、自然言語クエリの意図を識別し、抽出されたデータを返しました。 

チャットボットは、主要なアクション `OrderDrinks` と、Drink リスト エンティティから注文された飲み物の種類を決定するのに十分な情報を持つようになります。 

## <a name="where-is-this-luis-data-used"></a>この LUIS データの使用場所 
LUIS はこの要求の処理を完了しました。 チャットボットなどの呼び出し側アプリは、エンティティから topScoringIntent の結果とデータを取得し、次のステップに進むことができます。 LUIS は、ボットや呼び出し側アプリケーションのためにこのようなプログラム作業を実行しません。 LUIS は、ユーザーの意図が何かのみを判断します。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ
不要になったら、LUIS アプリを削除します。 削除するには、アプリ リストのアプリ名の右にある 3 つのドット メニュー [...] を選択し、**[Delete]\(削除\)** を選択します。 **[Delete app?]\(アプリを削除しますか?\)** のポップアップ ダイアログで、**[OK]** を選択します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [正規表現エンティティを追加する方法を確認する](luis-quickstart-intents-regex-entity.md)

数値を抽出するための **number** [事前構築済みエンティティ](luis-how-to-add-entities.md#add-prebuilt-entity)を追加する。 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
