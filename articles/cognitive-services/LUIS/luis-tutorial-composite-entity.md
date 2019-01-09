---
title: 複合エンティティ"
titleSuffix: Azure Cognitive Services
description: さまざまな種類の抽出されたデータを、1 つの包含するエンティティにバンドルするための複合エンティティを追加します。 データをバンドルすることにより、クライアント アプリケーションはさまざまなデータ型で関連データを簡単に抽出できます。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: b5923d5cd4a704dda76e33ee6a2b76cfd903219d
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53079213"
---
# <a name="tutorial-6-group-and-extract-related-data"></a>チュートリアル 6: 関連するデータのグループ化と抽出
このチュートリアルでは、さまざまな種類の抽出されたデータを、1 つの包含するエンティティにバンドルするための複合エンティティを追加します。 データをバンドルすることにより、クライアント アプリケーションはさまざまなデータ型で関連データを簡単に抽出できます。

複合エンティティの目的は、関連するエンティティを親カテゴリのエンティティにグループ化することです。 複合エンティティが作成される前は、情報は個別のエンティティとして存在しています。 これは階層構造エンティティに似ていますが、異なる種類のエンティティを含むことができます。 

複合エンティティは、データが次のようなものであるため、この種のデータに適しています。

* 相互に関連している。 
* さまざまなエンティティ型を使用する。
* クライアント アプリによって情報の単位としてグループ化され、処理される必要がある。

**このチュートリアルで学習する内容は次のとおりです。**

<!-- green checkmark -->
> [!div class="checklist"]
> * 既存のチュートリアル アプリを使用する
> * 複合エンティティを追加する 
> * トレーニング
> * [発行]
> * エンドポイントから意図とエンティティを取得する

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>既存のアプリを使用する
最後のチュートリアルで作成した、**HumanResources** という名前のアプリを引き続き使用します。 

以前のチュートリアルの HumanResources アプリがない場合は、次の手順を使用します。

1.  [アプリの JSON ファイル](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-hier-HumanResources.json)をダウンロードして保存します。

2. JSON を新しいアプリにインポートします。

3. **[管理]** セクションの **[バージョン]** タブで、バージョンを複製し、それに `composite` という名前を付けます。 複製は、元のバージョンに影響を及ぼさずに LUIS のさまざまな機能を使用するための優れた方法です。 バージョン名は URL ルートの一部として使用されるため、URL 内で有効ではない文字を名前に含めることはできません。


## <a name="composite-entity"></a>複合エンティティ
個別のエンティティを論理的にグループ化できる場合は、複合エンティティを作成します。この論理的なグループ化は、クライアント アプリケーションに役立ちます。 

このアプリでは、従業員名は **「Employee」(従業員)** リスト エンティティで定義されており、名前のシノニム、メール アドレス、会社の内線番号、携帯電話番号、および米国連邦税 ID を含みます。 

**MoveEmployee** 意図には、従業員をあるビルとオフィスから別のビルとオフィスに移動することを要求する発話の例があります。 ビルの名前が "A"、"B" などの英字であるのに対して、オフィスは "1234"、"13245" などの数値です。 

**MoveEmployee** 意図にある発話の例には、次が含まれます。

|発話の例|
|--|
|John W.  Smith を a-2345 に移動する|
|明日 x12345 を h-1234 にシフトする|
 
この移動要求には、その従業員 (いずれかのシノニムを使用) と、最終的なビルとオフィスの場所を含める必要があります。 この要求にはまた、元のオフィスや、その移動が発生する日付も含めることができます。 

エンドポイントから抽出されたデータはこれらの情報を含み、それを `RequestEmployeeMove` 複合エンティティで返す必要があります。

```json
"compositeEntities": [
  {
    "parentType": "RequestEmployeeMove",
    "value": "jill jones from a - 1234 to z - 2345 on march 3 2 p . m",
    "children": [
      {
        "type": "builtin.datetimeV2.datetime",
        "value": "march 3 2 p.m"
      },
      {
        "type": "Locations::Destination",
        "value": "z - 2345"
      },
      {
        "type": "Employee",
        "value": "jill jones"
      },
      {
        "type": "Locations::Origin",
        "value": "a - 1234"
      }
    ]
  }
]
```

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. **[意図]** ページで、**[MoveEmployee]** 意図を選択します。 

3. ツール バーの虫眼鏡アイコンを選択して発話一覧をフィルター処理します。 

    [![虫眼鏡ボタンが強調表示されている "MoveEmployee" 意図での LUIS のスクリーンショット](media/luis-tutorial-composite-entity/hr-moveemployee-magglass.png "虫眼鏡ボタンが強調表示されている \"MoveEmployee\" 意図での LUIS のスクリーンショット")](media/luis-tutorial-composite-entity/hr-moveemployee-magglass.png#lightbox)

4. フィルター テキスト ボックスに `tomorrow` を入力して、発話 `shift x12345 to h-1234 tomorrow` を検索します。

    [!["tomorrow" のフィルターが強調表示されている "MoveEmployee" 意図での LUIS のスクリーンショット](media/luis-tutorial-composite-entity/hr-filter-by-tomorrow.png "\"tomorrow\" のフィルターが強調表示されている \"MoveEmployee\" 意図での LUIS のスクリーンショット")](media/luis-tutorial-composite-entity/hr-filter-by-tomorrow.png#lightbox)

    datetimeV2 でエンティティをフィルター処理する別の方法として、**[Entity filters] (エンティティ フィルター)** を選択し、一覧から **[datetimeV2]** を選択する方法があります。 

5. 最初のエンティティ `Employee` を選択してから、ポップアップ メニューの一覧にある **「Wrap in composite entity」(複合エンティティにラップする)** を選択します。 

    [![複合内の最初のエンティティの選択が強調表示されている "MoveEmployee" 意図での LUIS のスクリーンショット](media/luis-tutorial-composite-entity/hr-create-entity-1.png "複合内の最初のエンティティの選択が強調表示されている \"MoveEmployee\" 意図での LUIS のスクリーンショット")](media/luis-tutorial-composite-entity/hr-create-entity-1.png#lightbox)


6. 次に、発話内の最後のエンティティ `datetimeV2` を直ちに選択します。 選択された単語の下に、複合エンティティを示す緑色のバーが描画されます。 ポップアップ メニューで、複合名 `RequestEmployeeMove` を入力して Enter キーを押します。 

    [![複合内の最後のエンティティの選択とエンティティの作成が強調表示されている "MoveEmployee" 意図での LUIS のスクリーンショット](media/luis-tutorial-composite-entity/hr-create-entity-2.png "複合内の最後のエンティティの選択とエンティティの作成が強調表示されている \"MoveEmployee\" 意図での LUIS のスクリーンショット")](media/luis-tutorial-composite-entity/hr-create-entity-2.png#lightbox)

7. **「What type of entity do you want to create?」(どのような種類のエンティティを作成しますか?)** では、必要なほぼすべてのフィールドが一覧にあります。 元の場所だけがありません。 **[子エンティティを追加する]** を選択し、既存のエンティティの一覧から **[Locations::Origin]** を選択してから、**[完了]** を選択します。 

    事前構築済みのエンティティ number が複合エンティティに追加されました。 複合エンティティの開始トークンと終了トークンの間に事前構築済みエンティティを出現させることができた場合、複合エンティティにはそれらの事前構築済みエンティティが含まれている必要があります。 事前構築済みエンティティが含まれていない場合、複合エンティティは正しく予測されませんが、個別の要素は正しく予測されます。

    ![ポップアップ ウィンドウで別のエンティティを追加している 'MoveEmployee' 意図での LUIS のスクリーンショット](media/luis-tutorial-composite-entity/hr-create-entity-ddl.png)

8. ツールバーの虫眼鏡を選択してフィルターを削除します。 

9. すべての発話例をもう一度確認できるよう、単語 `tomorrow` をフィルターから削除します。 

## <a name="label-example-utterances-with-composite-entity"></a>発話の例に複合エンティティのラベルを付ける


1. 各発話の例で、複合に含まれている左端のエンティティを選択します。 次に、**「Wrap in composite entity」(複合エンティティにラップする)** を選択します。

    [![複合内の最初のエンティティの選択が強調表示されている "MoveEmployee" 意図での LUIS のスクリーンショット](media/luis-tutorial-composite-entity/hr-label-entity-1.png "複合内の最初のエンティティの選択が強調表示されている \"MoveEmployee\" 意図での LUIS のスクリーンショット")](media/luis-tutorial-composite-entity/hr-label-entity-1.png#lightbox)

2. 複合エンティティ内の最後の単語を選択してから、ポップアップ メニューから **[RequestEmployeeMove]** を選択します。 

    [![複合内の最後のエンティティの選択が強調表示されている "MoveEmployee" 意図での LUIS のスクリーンショット](media/luis-tutorial-composite-entity/hr-label-entity-2.png "複合内の最後のエンティティの選択が強調表示されている \"MoveEmployee\" 意図での LUIS のスクリーンショット")](media/luis-tutorial-composite-entity/hr-label-entity-2.png#lightbox)

3. 意図のすべての発話に複合エンティティのラベルが付けられていることを確認します。 

    [![すべての発話にラベルが付いている "MoveEmployee" での LUIS のスクリーンショット](media/luis-tutorial-composite-entity/hr-all-utterances-labeled.png "すべての発話にラベルが付いている \"MoveEmployee\" での LUIS のスクリーンショット")](media/luis-tutorial-composite-entity/hr-all-utterances-labeled.png#lightbox)

## <a name="train"></a>トレーニング

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>[発行]

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>エンドポイントから意図とエンティティを取得する 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. アドレスの URL の末尾に移動し、「`Move Jill Jones from a-1234 to z-2345 on March 3 2 p.m.`」と入力します。 最後のクエリ文字列パラメーターは `q` です。これは発話のクエリです。 

    このテストは複合が正しく抽出されたことを確認するためであるため、テストには、既存のサンプル発話または新しい発話のどちらを含めることもできます。 適切なテストでは、複合エンティティ内のすべての子エンティティを含めます。

    ```json
    {
      "query": "Move Jill Jones from a-1234 to z-2345 on March 3  2 p.m",
      "topScoringIntent": {
        "intent": "MoveEmployee",
        "score": 0.9959525
      },
      "intents": [
        {
          "intent": "MoveEmployee",
          "score": 0.9959525
        },
        {
          "intent": "GetJobInformation",
          "score": 0.009858314
        },
        {
          "intent": "ApplyForJob",
          "score": 0.00728598563
        },
        {
          "intent": "FindForm",
          "score": 0.0058053555
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.005371796
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00266987388
        },
        {
          "intent": "None",
          "score": 0.00123299169
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.00116407464
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00102653319
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.0006628214
        }
      ],
      "entities": [
        {
          "entity": "march 3 2 p.m",
          "type": "builtin.datetimeV2.datetime",
          "startIndex": 41,
          "endIndex": 54,
          "resolution": {
            "values": [
              {
                "timex": "XXXX-03-03T14",
                "type": "datetime",
                "value": "2018-03-03 14:00:00"
              },
              {
                "timex": "XXXX-03-03T14",
                "type": "datetime",
                "value": "2019-03-03 14:00:00"
              }
            ]
          }
        },
        {
          "entity": "jill jones",
          "type": "Employee",
          "startIndex": 5,
          "endIndex": 14,
          "resolution": {
            "values": [
              "Employee-45612"
            ]
          }
        },
        {
          "entity": "z - 2345",
          "type": "Locations::Destination",
          "startIndex": 31,
          "endIndex": 36,
          "score": 0.9690751
        },
        {
          "entity": "a - 1234",
          "type": "Locations::Origin",
          "startIndex": 21,
          "endIndex": 26,
          "score": 0.9713137
        },
        {
          "entity": "-1234",
          "type": "builtin.number",
          "startIndex": 22,
          "endIndex": 26,
          "resolution": {
            "value": "-1234"
          }
        },
        {
          "entity": "-2345",
          "type": "builtin.number",
          "startIndex": 32,
          "endIndex": 36,
          "resolution": {
            "value": "-2345"
          }
        },
        {
          "entity": "3",
          "type": "builtin.number",
          "startIndex": 47,
          "endIndex": 47,
          "resolution": {
            "value": "3"
          }
        },
        {
          "entity": "2",
          "type": "builtin.number",
          "startIndex": 50,
          "endIndex": 50,
          "resolution": {
            "value": "2"
          }
        },
        {
          "entity": "jill jones from a - 1234 to z - 2345 on march 3 2 p . m",
          "type": "RequestEmployeeMove",
          "startIndex": 5,
          "endIndex": 54,
          "score": 0.4027723
        }
      ],
      "compositeEntities": [
        {
          "parentType": "RequestEmployeeMove",
          "value": "jill jones from a - 1234 to z - 2345 on march 3 2 p . m",
          "children": [
            {
              "type": "builtin.datetimeV2.datetime",
              "value": "march 3 2 p.m"
            },
            {
              "type": "Locations::Destination",
              "value": "z - 2345"
            },
            {
              "type": "Employee",
              "value": "jill jones"
            },
            {
              "type": "Locations::Origin",
              "value": "a - 1234"
            }
          ]
        }
      ]
    }
    ```

  この発話は、複合エンティティの配列を返します。 各エンティティには、型と値が与えられます。 子エンティティごとの精度を高めるには、複合の配列項目の型と値の組み合わせを使用して、エンティティの配列内の対応する項目を見つけます。  

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、既存のエンティティをカプセル化するための複合エンティティを作成しました。 これによりクライアント アプリケーションは、会話を続けるために、さまざまなデータ型の関連データのグループを見つけることができます。 この Human Resources アプリのクライアント アプリケーションは、移動が開始および終了する必要がある日時を問い合わせることができました。 電話機などのその他の物品の移動について問い合わせることもできました。 

> [!div class="nextstepaction"] 
> [フレーズ リストと共にシンプル エンティティを追加する方法について](luis-quickstart-primary-and-secondary-data.md)  
