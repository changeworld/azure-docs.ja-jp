---
title: 複合エンティティを作成して複雑なデータを抽出する - Azure | Microsoft Docs
description: LUIS アプリで複合エンティティを作成して、さまざまな種類のエンティティ データを抽出する方法を説明します。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 03/28/2018
ms.author: v-geberr
ms.openlocfilehash: cb581ee60dea2b0810332933455a03a8b68e16ea
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264387"
---
# <a name="use-composite-entity-to-extract-complex-data"></a>複合エンティティを使用した複雑なデータの抽出
この簡単なアプリには、2 つの[意図](luis-concept-intent.md)と複数のエンティティがあります。 その目的は、"金曜日のシアトルからカイロへのチケット 1 枚" のようにフライトを予約し、予約の詳細をすべて 1 つのデータとして返すことです。 

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
* 事前構築済みエンティティの datetimeV2 と number を追加する
* 複合エンティティを作成する
* LUIS にクエリを実行し、複合エンティティ データを取得する

## <a name="before-you-begin"></a>開始する前に
* **[階層クイック スタート](luis-tutorial-composite-entity.md)** の LUIS アプリ。 

> [!Tip]
> サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)に登録できます。

## <a name="composite-entity-is-a-logical-grouping"></a>複合エンティティは論理グループ 
エンティティの目的は、発話内のテキストの一部分を検出して分類することです。 [複合](luis-concept-entity-types.md)エンティティは、コンテキストから学習した他の複数のエンティティ型で構成されます。 フライトを予約するこの旅行アプリの場合、日付、場所、座席数などの複数の情報があります。 

複合エンティティが作成される前は、情報は個別のエンティティとして存在しています。 個々のエンティティを論理的にグループ化できる場合は、複合エンティティを作成します。この論理グループは、チャットボットや LUIS を使用する他のアプリケーションで役立ちます。 

ユーザーの簡単な発話の例を次に示します。

```
Book a flight to London for next Monday
2 tickets from Dallas to Dublin this weekend
Reserve a seat from New York to Paris on the first of April
```
 
複合エンティティは、座席数、出発地、目的地、日付と一致します。 

## <a name="what-luis-does"></a>LUIS の機能
発話の意図とエンティティが識別され、[抽出](luis-concept-data-extraction.md#list-entity-data)されて、[エンドポイント](https://aka.ms/luis-endpoint-apis)から JSON で返されると、LUIS は終了します。 呼び出し元のアプリケーションやチャットボットは、その JSON 応答を受け取って、それぞれの設計された方法で要求を満たします。 

## <a name="add-prebuilt-entities-number-and-datetimev2"></a>事前構築済みエンティティの number と datetimeV2 を追加する
1. [LUIS][LUIS] Web サイトのアプリの一覧で、`MyTravelApp` アプリを選択します。

2. アプリが開いたら、左側の **[Entities]\(エンティティ\)** ナビゲーション リンクを選択します。

    ![エンティティ ボタンを選択する](./media/luis-tutorial-composite-entity/intents-page-select-entities.png)    

3. **[Manage prebuilt entities]\(事前構築済みエンティティの管理\)** を選択します。

    ![エンティティ ボタンを選択する](./media/luis-tutorial-composite-entity/manage-prebuilt-entities-button.png)

4. ポップアップ ボックスで、**[number]** と **[datetimeV2]** を選択します。

    ![エンティティ ボタンを選択する](./media/luis-tutorial-composite-entity/prebuilt-entity-ddl.png)

5. 新しいエンティティを抽出するために、上部のナビゲーション バーで **[Train]\(トレーニング\)** を選択します。

    ![[Train]\(トレーニング\) ボタンを選択する](./media/luis-tutorial-composite-entity/train.png)

## <a name="use-existing-intent-to-create-composite-entity"></a>既存の意図を使用して複合エンティティを作成する
1. 左側のナビゲーションで、**[Intents]\(意図\)** を選択します。 

    ![[Intents]\(意図\) ページを選択する](./media/luis-tutorial-composite-entity/intents-from-entities-page.png)

2. **[Intents]\(意図\)** の一覧から `BookFlight` を選択します。  

    ![意図の一覧から BookFlight を選択する](./media/luis-tutorial-composite-entity/intent-page-with-prebuilt-entities-labeled.png)

    事前構築済みエンティティの number と datetimeV2 は、各発話でラベル付けされています。

3. `book 2 flights from seattle to cairo next monday` という発話で、青色の `number` エンティティを選択し、一覧で **[Wrap in composite entity]\(複合エンティティにラップする\)** を選択します。 単語の下の緑色の線は、右に移動するカーソルの動きに従って複合エンティティを示します。 次に、右に移動して最後の事前構築済みエンティティ `datetimeV2` を選択し、ポップアップ ウィンドウのテキスト ボックスに「`FlightReservation`」と入力して、**[Create new composite]\(新しい複合エンティティの作成\)** を選択します。 

    ![意図ページで複合エンティティを作成する](./media/luis-tutorial-composite-entity/create-new-composite.png)

4. ポップアップ ダイアログが表示され、複合エンティティの子を確認できます。 **[完了]** を選択します。

    ![意図ページで複合エンティティを作成する](./media/luis-tutorial-composite-entity/validate-composite-entity.png)

## <a name="wrap-the-entities-in-the-composite-entity"></a>エンティティを複合エンティティにラップする
複合エンティティが作成されたら、複合エンティティで残りの発話にラベルを付けます。 フレーズを複合エンティティとしてラップするには、左端の単語を選択し、表示された一覧で **[Wrap in composite entity]\(複合エンティティにラップする\)** を選択します。次に、右端の単語を選択し、名前付き複合エンティティ `FlightReservation` を選択します。 これはすばやくスムーズな選択の手順です。この手順の詳細を次に示します。

1. 発話 `schedule 4 seats from paris to london for april 1` で、事前構築済みエンティティ number として 4 を選択します。

    ![左端の単語を選択する](./media/luis-tutorial-composite-entity/wrap-composite-step-1.png)

2. 表示された一覧で、**[Wrap in composite entity]\(複合エンティティにラップする\)** を選択します。

    ![一覧でラップを選択する](./media/luis-tutorial-composite-entity/wrap-composite-step-2.png)

3. 右端の単語を選択します。 フレーズの下に、複合エンティティを示す緑色の線が表示されます。

    ![右端の単語を選択する](./media/luis-tutorial-composite-entity/wrap-composite-step-3.png)

4. 表示された一覧で、複合名 `FlightReservation` を選択します。

    ![名前付き複合エンティティを選択する](./media/luis-tutorial-composite-entity/wrap-composite-step-4.png)

    この最後の発話では、同じ手順を使用して、`London` と `tomorrow` を複合エンティティにラップします。 

## <a name="train-the-luis-app"></a>LUIS アプリをトレーニングする
LUIS は、意図やエンティティ (モデル) に対する変更を、トレーニングされるまで認識しません。 

1. LUIS Web サイトの右上にある **[Train]\(トレーニング\)** ボタンを選択します。

    ![アプリをトレーニングする](./media/luis-tutorial-composite-entity/train-button.png)

2. 成功したことを示す緑色のステータス バーが Web サイトの上部に表示されたら、トレーニングは完了しています。

    ![トレーニング成功](./media/luis-tutorial-composite-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>アプリを公開してエンドポイント URL を取得する
チャットボットや他のアプリケーションで LUIS の予測を取得するには、アプリを公開する必要があります。 

1. LUIS Web サイトの右上にある **[Publish]\(公開\)** ボタンを選択します。 

2. [Production]\(運用\) スロットを選択し、**[Publish]\(公開\)** ボタンを選択します。

    ![アプリを公開する](./media/luis-tutorial-composite-entity/publish-to-production.png)

3. 成功したことを示す緑色のステータス バーが Web サイトの上部に表示されたら、公開は完了しています。

## <a name="query-the-endpoint-with-a-different-utterance"></a>異なる発話でエンドポイントにクエリを実行する
1. **[Publish]\(公開\)** ページで、ページの下部にある**エンドポイント**のリンクを選択します。 別のブラウザー ウィンドウが開き、アドレス バーにエンドポイント URL が表示されます。 

    ![エンドポイント URL を選択する](./media/luis-tutorial-composite-entity/publish-select-endpoint.png)

2. アドレスの URL の末尾に移動し、「`reserve 3 seats from London to Cairo on Sunday`」と入力します。 最後のクエリ文字列パラメーターは `q` です。これは発話のクエリです。 この発話はラベル付けされたどの発話とも異なるので、よいテストであり、`BookFlight` 意図と階層エンティティが抽出されて返される必要があります。

```
{
  "query": "reserve 3 seats from London to Cairo on Sunday",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.999999046
  },
  "intents": [
    {
      "intent": "BookFlight",
      "score": 0.999999046
    },
    {
      "intent": "None",
      "score": 0.227036044
    }
  ],
  "entities": [
    {
      "entity": "sunday",
      "type": "builtin.datetimeV2.date",
      "startIndex": 40,
      "endIndex": 45,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-WXX-7",
            "type": "date",
            "value": "2018-03-25"
          },
          {
            "timex": "XXXX-WXX-7",
            "type": "date",
            "value": "2018-04-01"
          }
        ]
      }
    },
    {
      "entity": "3 seats from london to cairo on sunday",
      "type": "flightreservation",
      "startIndex": 8,
      "endIndex": 45,
      "score": 0.6892485
    },
    {
      "entity": "cairo",
      "type": "Location::Destination",
      "startIndex": 31,
      "endIndex": 35,
      "score": 0.557570755
    },
    {
      "entity": "london",
      "type": "Location::Origin",
      "startIndex": 21,
      "endIndex": 26,
      "score": 0.8933808
    },
    {
      "entity": "3",
      "type": "builtin.number",
      "startIndex": 8,
      "endIndex": 8,
      "resolution": {
        "value": "3"
      }
    }
  ],
  "compositeEntities": [
    {
      "parentType": "flightreservation",
      "value": "3 seats from london to cairo on sunday",
      "children": [
        {
          "type": "builtin.datetimeV2.date",
          "value": "sunday"
        },
        {
          "type": "Location::Destination",
          "value": "cairo"
        },
        {
          "type": "builtin.number",
          "value": "3"
        },
        {
          "type": "Location::Origin",
          "value": "london"
        }
      ]
    }
  ]
}
```

この発話は、データが抽出された **flightreservation** オブジェクトを含む複合エンティティ配列を返します。  

## <a name="what-has-this-luis-app-accomplished"></a>この LUIS アプリの処理内容
2 つの意図と複合エンティティを使用するこのアプリは、自然言語クエリの意図を識別し、抽出されたデータを返しました。 

チャットボットは、主要アクションの `BookFlight` を決定できるだけの十分な情報と、発話で検出された予約情報を取得しました。 

## <a name="where-is-this-luis-data-used"></a>この LUIS データの使用場所 
LUIS はこの要求の処理を完了しています。 チャットボットなどの呼び出し元アプリケーションは、エンティティから topScoringIntent の結果とデータを取得して、次のステップに進むことができます。 LUIS は、ボットや呼び出し元アプリケーションのためにこのようなプログラムによる処理を実行するわけではありません。 LUIS はユーザーの意図を判断するだけです。 

## <a name="next-steps"></a>次の手順

[エンティティの詳細](luis-concept-entity-types.md)を確認します。 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
