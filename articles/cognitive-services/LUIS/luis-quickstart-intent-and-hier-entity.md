---
title: 場所データを取得する LUIS アプリの作成に関するチュートリアル - Azure | Microsoft Docs
description: このチュートリアルでは、意図と階層エンティティを使用して単純な LUIS アプリを作成し、データを抽出する方法を学習します。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: v-geberr
ms.openlocfilehash: 2547407126943161ba604fa2f5e80b9186cae57e
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266500"
---
# <a name="tutorial-create-app-that-uses-hierarchical-entity"></a>チュートリアル: 階層エンティティを使用するアプリを作成する
このチュートリアルでは、コンテキストに基づいて関連するデータを検索する方法を示すアプリを作成します。 

<!-- green checkmark -->
> [!div class="checklist"]
> * 階層エンティティおよびコンテキストから学習された子とは 
> * Bookflight 意図の旅行ドメイン用の新しい LUIS アプリを作成する
> * _None_ 意図を追加し、発話の例を追加する
> * 出発地と到着地を子として持つ場所階層エンティティを追加する
> * アプリをトレーニングして公開する
> * アプリのエンドポイントのクエリを行って、階層の子を含む LUIS JSON の応答を確認する 

この記事に従って LUIS アプリケーションを作成するには、無料の [LUIS][LUIS] アカウントが必要です。

## <a name="purpose-of-the-app-with-this-entity"></a>このエンティティでのアプリの目的
このアプリは、ユーザーがフライトを予約したいかどうかを判断します。 階層エンティティを使用して、ユーザーのテキスト内の場所、出発都市、到着都市を決定します。 

階層エンティティは、2 つのデータが次のようなものであるため、この種のデータに適しています。

* 両方とも場所であり、通常は都市または空港コードで表されます。
* 通常は、出発地と到着地を特定できる一意の単語を選択できます。 このような単語としては、to、headed toward、from、leaving (まで、行き、から、発) などがあります。
* どちらの場所も同じ発話内に頻出します。 

**階層**エンティティの目的は、コンテキストに基づいて発話内から関連データを検索することです。 次のような発話について考えます。

```JSON
1 ticket from Seattle to Cairo`
```

この発話では 2 つの場所が指定されています。 1 つは出発地の都市 Seattle で、もう 1 つは到着地の都市 Cairo です。 どちらの都市もフライトの予約に重要です。 シンプル エンティティを使って検出することもできますが、これらは相互に関連しており、同じ発話内で頻繁に見つかります。 したがって、両方を階層エンティティ **"場所"** の子としてグループ化することには必然性があります。 

機械学習されるエンティティなので、アプリには出発都市と到着都市のラベルが付いた発話の例が必要です。 これにより、発話内でエンティティがある場所、エンティティの長さ、周囲の単語を、LUIS に学習させます。 

## <a name="app-intents"></a>アプリの意図
意図は、ユーザーが望むカテゴリです。 このアプリには、BookFlight と None の 2 つの意図があります。 [None](luis-concept-intent.md#none-intent-is-fallback-for-app) 意図には、アプリの外部のすべてのものを示す目的があります。  

## <a name="hierarchical-entity-is-contextually-learned"></a>階層エンティティはコンテキストによって学習されます。 
エンティティの目的は、発話内のテキストの一部分を検索して分類することです。 [階層](luis-concept-entity-types.md)エンティティは、使用コンテキストに基づく親子エンティティです。 人は、`to` (まで) と `from` (から) の使われ方に基づいて、発話に含まれる出発都市と到着都市を判断できますです。 これらは、コンテキストによって決まる使用方法の例です。  

この Travel アプリでは、LUIS は、標準的な予約を作成して入力できる方法で、出発地と到着地を抽出します。 LUIS では、発話のバリエーション、省略形、俗語に含まれていてもかまいません。 

ユーザーからの簡単な発話の例を次に示します。

```
Book a flight to London for next Monday
2 tickets from Dallas to Dublin this weekend
Researve a seat from New York to Paris on the first of April
```

発話の省略形または俗語バージョンを次に示します。

```
LHR tomorrow
SEA to NYC next Monday
LA to MCO spring break
```
 
階層エンティティは、出発地と到着地に一致します。 階層エンティティに 1 つの子 (出発地または到着地) しか存在しない場合でも、やはり抽出されます。 1 つだけまたは一部を抽出するために、すべての子が見つかる必要はありません。 

## <a name="what-luis-does"></a>LUIS の機能
発話の意図とエンティティが識別され、[抽出](luis-concept-data-extraction.md#list-entity-data)されて、[エンドポイント](https://aka.ms/luis-endpoint-apis)から JSON で返されると、LUIS は終了します。 呼び出し元のアプリケーションやチャットボットは、その JSON 応答を受け取って、それぞれの設計された方法で要求を満たします。 

## <a name="create-a-new-app"></a>新しいアプリの作成
1. [LUIS][LUIS] Web サイトにログインします。 LUIS エンドポイントを公開する必要がある[リージョン][LUIS-regions]にログインします。

2. [LUIS][LUIS] Web サイトで **[新しいアプリの作成]** を選びます。  

    [![](media/luis-quickstart-intent-and-hier-entity/app-list.png "アプリ リスト ページのスクリーンショット")](media/luis-quickstart-intent-and-hier-entity/app-list.png#lightbox)

3. ポップアップ ダイアログで、名前に「`MyTravelApp`」と入力します。 

    [![](media/luis-quickstart-intent-and-hier-entity/create-new-app.png "[新しいアプリの作成] ポップアップ ダイアログのスクリーンショット")](media/luis-quickstart-intent-and-hier-entity/create-new-app.png#lightbox)

4. 終了が処理すると、アプリの **[Intents]\(意図\)** ページに **None** 意図が表示されます。 

    [![](media/luis-quickstart-intent-and-hier-entity/intents-page-none-only.png "None 意図だけの意図一覧のスクリーンショット")](media/luis-quickstart-intent-and-hier-entity/intents-page-none-only.png#lightbox)

## <a name="create-a-new-intent"></a>新しい意図を作成する

1. **[Intents]\(意図\)** ページで、**[Create new intent]\(意図の新規作成\)** を選びます。 

    [![](media/luis-quickstart-intent-and-hier-entity/create-new-intent-button.png "[Create new intent]\(意図の新規作成\) ボタンが強調表示されてい [Intents]\(意図\) リストのスクリーンショット")](media/luis-quickstart-intent-and-hier-entity/create-new-intent-button.png#lightbox)

2. 新しい意図の名前として「`BookFlight`」と入力します。 ユーザーがフライトを予約したいときは常に、この意図が選択される必要があります。

    意図を作成すると、識別する情報の主なカテゴリが作成されます。 カテゴリに名前を付けると、LUIS クエリ結果を使用する他のアプリケーションが、そのカテゴリ名を使用して、適切な回答を見つけたり、適切なアクションを実行したりできます。 LUIS はこれらの質問に回答しません。どのような種類の情報が自然言語で質問されたかを明らかにするだけです。 

    [![](media/luis-quickstart-intent-and-hier-entity/create-new-intent.png "[Create new intent]\(意図の新規作成\) ポップアップ ダイアログのスクリーンショット")](media/luis-quickstart-intent-and-hier-entity/create-new-intent.png#lightbox)

3. ユーザーの質問として予想される複数の発話を `BookFlight` 意図に追加します。次はその例です。

    | 発話の例|
    |--|
    |次の月曜日にシアトルからカイロまで 2 フライト予約する|
    |明日ロンドンまでのチケットを予約する|
    |4 月 1 日にパリからロンドンまで 4 席をスケジュールする|

    [![](media/luis-quickstart-intent-and-hier-entity/enter-utterances-on-intent.png "BookFlight 意図ページでの発話入力のスクリーンショット")](media/luis-quickstart-intent-and-hier-entity/enter-utterances-on-intent.png#lightbox)

## <a name="add-utterances-to-none-intent"></a>None 意図に発話を追加する

現在、この LUIS アプリの **None** 意図には発話はありません。 アプリに応答させたくない発話が必要なので、**None** 意図に発話を追加します。 空白のままにしないでください。 

1. 左側のパネルから **[Intents]\(意図\)** を選びます。 

    [![](media/luis-quickstart-intent-and-hier-entity/select-intents-from-bookflight-intent.png "[Intents]\(意図\) ボタンが強調表示された BookFlight 意図ページのスクリーンショット")](media/luis-quickstart-intent-and-hier-entity/select-intents-from-bookflight-intent.png#lightbox)

2. **None** 意図を選択します。 ユーザーが入力する可能性があるがアプリには関係のない 3 つの発話を追加します。

    | 発話の例|
    |--|
    |キャンセル|
    |さようなら|
    |どうなっているの|

## <a name="when-the-utterance-is-predicted-for-the-none-intent"></a>None 意図の発話が予想されるとき
LUIS 呼び出し元アプリケーション (チャットボットなど) では、LUIS が発話に対して **None** 意図を返したら、ユーザーが会話の終了を望んでいるかどうかを確認できます。 また、ユーザーが終了を望んでいない場合は、会話を続けるよう指示することもできます。 

エンティティは **None** 意図で動作します。 最もスコアの高い意図が **None** であり、しかしチャットボットにとって意味のあるエンティティが抽出された場合、チャットボットはさらに質問して客の意図を絞り込むことができます。 

## <a name="create-a-location-entity-from-the-intent-page"></a>意図ページから場所エンティティを作成する
2 つの意図に発話を追加したので、LUIS は場所が何かを理解する必要があります。 `BookFlight` 意図に戻り、以下の手順に従って発話内の都市名にラベル (マーク) を付けます。

1. 左側のパネルで **[Intents]\(意図\)** を選択して `BookFlight` 意図に戻ります。

2. 意図の一覧から `BookFlight` を選びます。

3. 発話 `Book 2 flights from Seattle to Cairo next Monday` において、単語 `Seattle` を選びます。 ドロップダウン メニューが表示され、上部にあるテキスト ボックスで新しいエンティティを作成できます。 テキスト ボックスにエンティティ名「`Location`」を入力し、を選択し、ドロップダウン メニューで **[Create new entity]\(新しいエンティティの作成\)** を選択します。 

    [![](media/luis-quickstart-intent-and-hier-entity/label-seattle-in-utterance.png "選択したテキストから新しいエンティティを作成する BookFlight 意図ページのスクリーンショット")](media/luis-quickstart-intent-and-hier-entity/label-seattle-in-utterance.png#lightbox)

4. ポップアップ ウィンドウで、**[Hierarchical]\(階層\)** エンティティ タイプを選択し、子エンティティとして `Origin` と `Destination` を選択します。 **[完了]** を選択します。

    [![](media/luis-quickstart-intent-and-hier-entity/hier-entity-ddl.png "新しい場所エンティティのエンティティ作成ポップアップ ダイアログのスクリーンショット")](media/luis-quickstart-intent-and-hier-entity/hier-entity-ddl.png#lightbox)

    LUIS には用語が出発地か、到着地か、どちらでもないかわからないので、`Seattle` のラベルは `Location` とマークされています。 `Seattle` を選択し、[Location]\(場所\) を選択した後、右側のメニューに従って `Origin` を選択します。

5. エンティティを作成し、1 つの発話にラベルを付けたので、他の都市にラベルを付けます。都市名を選択し、[Location]\(場所\) を選択した後、右側のメニューに従って `Origin` または `Destination` を選択します。

    [![](media/luis-quickstart-intent-and-hier-entity/label-destination-in-utterance.png "エンティティ選択のために発話テキストが選択された BookFlight エンティティのスクリーンショット")](media/luis-quickstart-intent-and-hier-entity/label-destination-in-utterance.png#lightbox)

## <a name="train-the-luis-app"></a>LUIS アプリをトレーニングする
LUIS は、意図やエンティティ (モデル) に対する変更を、トレーニングされるまで認識しません。 

1. LUIS Web サイトの右上にある **[Train]\(トレーニング\)** ボタンを選択します。

    ![アプリをトレーニングする](./media/luis-quickstart-intent-and-hier-entity/train-button.png)

2. 成功したことを示す緑色のステータス バーが Web サイトの上部に表示されたら、トレーニングは完了しています。

    ![トレーニング成功](./media/luis-quickstart-intent-and-hier-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>アプリを公開してエンドポイント URL を取得する
チャットボットや他のアプリケーションで LUIS の予測を取得するには、アプリを公開する必要があります。 

1. LUIS Web サイトの右上にある **[Publish]\(公開\)** ボタンを選択します。 

    [![](media/luis-quickstart-intent-and-hier-entity/publish.png "[Publish]\(公開\) ボタンが強調表示された BookFlight 意図のスクリーンショット")](media/luis-quickstart-intent-and-hier-entity/publish.png#lightbox)

2. [Production]\(運用\) スロットを選択し、**[Publish]\(公開\)** ボタンを選択します。

    [![](media/luis-quickstart-intent-and-hier-entity/publish-to-production.png "運用スロットへの [Publish]\(公開\) ボタンが強調表示された [公開] ページのスクリーンショット")](media/luis-quickstart-intent-and-hier-entity/publish-to-production.png#lightbox)

3. 成功したことを示す緑色のステータス バーが Web サイトの上部に表示されたら、公開は完了しています。

## <a name="query-the-endpoint-with-a-different-utterance"></a>異なる発話でエンドポイントにクエリを行う
1. **[Publish]\(公開\)** ページで、ページの下部にある**エンドポイント**のリンクを選択します。 別のブラウザー ウィンドウが開き、アドレス バーにエンドポイント URL が表示されます。 

    [![](media/luis-quickstart-intent-and-hier-entity/publish-select-endpoint.png "エンドポイントの URL が強調表示された [Publish]\(公開\) ページのスクリーンショット")](media/luis-quickstart-intent-and-hier-entity/publish-select-endpoint.png#lightbox)

2. アドレスの URL の末尾に移動し、「`1 ticket to Portland on Friday`」と入力します。 最後の querystring パラメーターは `q` です。これは発話の**クエリ**です。 この発話はラベル付けされたどの発話とも同じではないので、よいテストであり、`BookFlight` 意図と階層エンティティが抽出されて返される必要があります。

```
{
  "query": "1 ticket to Portland on Friday",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.9998226
  },
  "intents": [
    {
      "intent": "BookFlight",
      "score": 0.9998226
    },
    {
      "intent": "None",
      "score": 0.221926212
    }
  ],
  "entities": [
    {
      "entity": "portland",
      "type": "Location::Destination",
      "startIndex": 12,
      "endIndex": 19,
      "score": 0.564448953
    }
  ]
}
```

## <a name="what-has-this-luis-app-accomplished"></a>この LUIS アプリの処理内容
2 つだけの意図と階層エンティティで構成されるこのアプリは、自然言語クエリの意図を識別し、抽出されたデータを返しました。 

チャットボットは、主要なアクション `BookFlight` を決定するのに十分な情報と、発話で見つかった場所情報を取得します。 

## <a name="where-is-this-luis-data-used"></a>この LUIS データの使用場所 
LUIS はこの要求の処理を完了しています。 チャットボットなどの呼び出し側アプリは、エンティティから topScoringIntent の結果とデータを取得し、次のステップに進むことができます。 LUIS は、ボットや呼び出し側アプリケーションのためにこのようなプログラム作業を実行しません。 LUIS は、ユーザーの意図が何かのみを判断します。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ
不要になったら、LUIS アプリを削除します。 削除するには、アプリ リストのアプリ名の右にある 3 つのドット メニュー [...] を選択し、**[Delete]\(削除\)** を選択します。 **[Delete app?]\(アプリを削除しますか?\)** のポップアップ ダイアログで、**[OK]** を選択します。

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"] 
> [リスト エンティティの追加方法を確認する](luis-quickstart-intent-and-list-entity.md) 

数値を抽出するための **number** [事前構築済みエンティティ](luis-how-to-add-entities.md#add-prebuilt-entity)を追加する。 

日付情報を抽出するための **datetimeV2** [事前構築済みエンティティ](luis-how-to-add-entities.md#add-prebuilt-entity)を追加する。


<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
