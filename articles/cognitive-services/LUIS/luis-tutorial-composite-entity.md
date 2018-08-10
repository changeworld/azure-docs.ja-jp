---
title: 'チュートリアル: 複合エンティティを作成して複雑なデータを抽出する - Azure | Microsoft Docs'
description: LUIS アプリで複合エンティティを作成して、さまざまな種類のエンティティ データを抽出する方法を説明します。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: 5f11409ff49830be97d9a13a0ab7f033d9cc1041
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494467"
---
# <a name="tutorial-6-add-composite-entity"></a>チュートリアル: 6.  複合エンティティを追加する 
このチュートリアルでは、抽出されたデータを、包含するエンティティにバンドルするための複合エンティティを追加します。

このチュートリアルで学習する内容は次のとおりです。

<!-- green checkmark -->
> [!div class="checklist"]
> * 複合エンティティについて 
> * データを抽出するための複合エンティティを追加する
> * アプリをトレーニングして公開する
> * アプリのエンドポイントをクエリして LUIS JSON の応答を表示する

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>開始する前に
[階層エンティティ](luis-quickstart-intent-and-hier-entity.md) チュートリアルからの人事アプリを保持していない場合は、JSON を [LUIS](luis-reference-regions.md#luis-website) Web サイトの新しいアプリに[インポート](luis-how-to-start-new-app.md#import-new-app)します。 インポートするアプリは、[LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-hier-HumanResources.json) GitHub リポジトリにあります。

元の人事アプリを保持したい場合は、[[設定]](luis-how-to-manage-versions.md#clone-a-version) ページ上でバージョンを複製して、`composite` という名前を付けます。 複製は、元のバージョンに影響を及ぼさずに LUIS のさまざまな機能を使用するための優れた方法です。  

## <a name="composite-entity-is-a-logical-grouping"></a>複合エンティティは論理グループ 
複合エンティティの目的は、関連するエンティティを親カテゴリのエンティティにグループ化することです。 複合エンティティが作成される前は、情報は個別のエンティティとして存在しています。 これは階層構造エンティティに似ていますが、より多くの種類のエンティティを含むことができます。 

 個別のエンティティを論理的にグループ化できる場合は、複合エンティティを作成します。この論理的なグループ化は、クライアント アプリケーションに役立ちます。 

このアプリでは、従業員名は **[Employee] (従業員)** リスト エンティティで定義されており、名前のシノニム、メール アドレス、会社の内線番号、携帯電話番号、および米国連邦税 ID を含みます。 

**MoveEmployee** 意図には、従業員をあるビルとオフィスから別のビルとオフィスに移動することを要求する発話の例があります。 ビルの名前が "A"、"B" などの英字であるのに対して、オフィスは "1234"、"13245" などの数値です。 

**MoveEmployee** 意図にある発話の例には、次が含まれます。

|発話の例|
|--|
|John W.  Smith を a-2345 に移動する|
|明日 x12345 を h-1234 にシフトする|
 
この移動要求には、少なくともその従業員 (いずれかのシノニムを使用) と、最終的なビルとオフィスの場所を含める必要があります。 この要求にはまた、元のオフィスや、その移動が発生する日付も含めることができます。 

エンドポイントから抽出されたデータはこれらの情報を含み、それを `RequestEmployeeMove` 複合エンティティで返す必要があります。 

## <a name="create-composite-entity"></a>複合エンティティを作成する
1. 人事アプリは必ず、LUIS の**ビルド** セクションに配置してください。 右上のメニュー バーの **[Build]\(ビルド\)** を選択すると、このセクションに変更できます。 

2. **[意図]** ページで、**[MoveEmployee]** 意図を選択します。 

3. ツール バーの虫眼鏡アイコンを選択して発話一覧をフィルター処理します。 

    [![](media/luis-tutorial-composite-entity/hr-moveemployee-magglass.png "虫眼鏡ボタンが強調表示されている 'MoveEmployee' 意図での LUIS のスクリーンショット")](media/luis-tutorial-composite-entity/hr-moveemployee-magglass.png#lightbox)

4. フィルター テキスト ボックスに `tomorrow` を入力して、発話 `shift x12345 to h-1234 tomorrow` を検索します。

    [![](media/luis-tutorial-composite-entity/hr-filter-by-tomorrow.png "'tomorrow' のフィルターが強調表示されている 'MoveEmployee' 意図での LUIS のスクリーンショット")](media/luis-tutorial-composite-entity/hr-filter-by-tomorrow.png#lightbox)

    datetimeV2 でエンティティをフィルター処理する別の方法として、**[Entity filters] (エンティティ フィルター)** を選択し、一覧から **[datetimeV2]** を選択する方法があります。 

5. 最初のエンティティ `Employee` を選択してから、ポップアップ メニューの一覧にある **[Wrap in composite entity] (複合エンティティにラップする)** を選択します。 

    [![](media/luis-tutorial-composite-entity/hr-create-entity-1.png "複合内の最初のエンティティの選択が強調表示されている 'MoveEmployee' 意図での LUIS のスクリーンショット")](media/luis-tutorial-composite-entity/hr-create-entity-1.png#lightbox)


6. 次に、発話内の最後のエンティティ `datetimeV2` を直ちに選択します。 選択された単語の下に、複合エンティティを示す緑色のバーが描画されます。 ポップアップ メニューで、複合名 `RequestEmployeeMove` を入力してから、ポップアップ メニューの **[Create new composite] (新しい複合を作成する)** を選択します。 

    [![](media/luis-tutorial-composite-entity/hr-create-entity-2.png "複合内の最後のエンティティの選択とエンティティの作成が強調表示されている 'MoveEmployee' 意図での LUIS のスクリーンショット")](media/luis-tutorial-composite-entity/hr-create-entity-2.png#lightbox)

7. **[What type of entity do you want to create?] (どのような種類のエンティティを作成しますか?)** では、必要なほぼすべてのフィールドが一覧にあります。 元の場所だけがありません。 **[子エンティティを追加する]** を選択し、既存のエンティティの一覧から **[Locations::Origin]** を選択してから、**[完了]** を選択します。 

    ![ポップアップ ウィンドウで別のエンティティを追加している 'MoveEmployee' 意図での LUIS のスクリーンショット](media/luis-tutorial-composite-entity/hr-create-entity-ddl.png)

8. ツールバーの虫眼鏡を選択してフィルターを削除します。 

## <a name="label-example-utterances-with-composite-entity"></a>発話の例に複合エンティティのラベルを付ける
1. 各発話の例で、複合に含まれている左端のエンティティを選択します。 次に、**[Wrap in composite entity] (複合エンティティにラップする)** を選択します。

    [![](media/luis-tutorial-composite-entity/hr-label-entity-1.png "複合内の最初のエンティティの選択が強調表示されている 'MoveEmployee' 意図での LUIS のスクリーンショット")](media/luis-tutorial-composite-entity/hr-label-entity-1.png#lightbox)

2. 複合エンティティ内の最後の単語を選択してから、ポップアップ メニューから **[RequestEmployeeMove]** を選択します。 

    [![](media/luis-tutorial-composite-entity/hr-label-entity-2.png "複合内の最後のエンティティの選択が強調表示されている 'MoveEmployee' 意図での LUIS のスクリーンショット")](media/luis-tutorial-composite-entity/hr-label-entity-2.png#lightbox)

3. 意図のすべての発話に複合エンティティのラベルが付けられていることを確認します。 

    [![](media/luis-tutorial-composite-entity/hr-all-utterances-labeled.png "すべての発話にラベルが付いている 'MoveEmployee' での LUIS のスクリーンショット")](media/luis-tutorial-composite-entity/hr-all-utterances-labeled.png#lightbox)

## <a name="train-the-luis-app"></a>LUIS アプリをトレーニングする

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>アプリを公開してエンドポイント URL を取得する

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint"></a>エンドポイントにクエリを実行する 

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. アドレスの URL の末尾に移動し、「`Move Jill Jones from a-1234 to z-2345 on March 3 2 p.m.`」と入力します。 最後のクエリ文字列パラメーターは `q` です。これは発話のクエリです。 

    このテストは複合が正しく抽出されたことを確認するためであるため、テストには、既存のサンプル発話または新しい発話のどちらを含めることもできます。 適切なテストでは、複合エンティティ内のすべての子エンティティを含めます。

    ```JSON
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
          "type": "requestemployeemove",
          "startIndex": 5,
          "endIndex": 54,
          "score": 0.4027723
        }
      ],
      "compositeEntities": [
        {
          "parentType": "requestemployeemove",
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
      ],
      "sentimentAnalysis": {
        "label": "neutral",
        "score": 0.5
      }
    }
    ```

  この発話は、複合エンティティの配列を返します。 各エンティティには、型と値が与えられます。 子エンティティごとの精度を高めるには、複合の配列項目の型と値の組み合わせを使用して、エンティティの配列内の対応する項目を見つけます。  

## <a name="what-has-this-luis-app-accomplished"></a>この LUIS アプリの処理内容
このアプリは自然言語クエリの意図を識別し、抽出されたデータを名前付きグループとして返しました。 

チャットボットには現在、主要なアクションと、発話内の関連する詳細を決定するための十分な情報が含まれています。 

## <a name="where-is-this-luis-data-used"></a>この LUIS データの使用場所 
LUIS はこの要求の処理を完了しています。 チャットボットなどの呼び出し元アプリケーションは、エンティティから topScoringIntent の結果とデータを取得して、次のステップに進むことができます。 LUIS は、ボットや呼び出し元アプリケーションのためにこのようなプログラムによる処理を実行するわけではありません。 LUIS はユーザーの意図を判断するだけです。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"] 
> [フレーズ リストと共にシンプル エンティティを追加する方法について](luis-quickstart-primary-and-secondary-data.md)  