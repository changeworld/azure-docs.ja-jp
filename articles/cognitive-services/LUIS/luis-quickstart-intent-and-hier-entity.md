---
title: 階層構造エンティティ
titleSuffix: Azure Cognitive Services
description: コンテキストに基づいて関連するデータを検索します。 たとえば、あるビルやオフィスから別のビルやオフィスへの物理的な移動の出発地と到着地が関連します。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 91a9646e88adbfaf6d3c3fc0b06b341c647e773f
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/22/2018
ms.locfileid: "53753695"
---
# <a name="tutorial-extract-contextually-related-data-from-an-utterance"></a>チュートリアル:コンテキストが関連するデータを発話から抽出する

このチュートリアルでは、コンテキストに基づいて関連するデータを検索します。 たとえば、ある都市から別の都市へ移動する場合の出発地と目的地です。 両方のデータが必要である可能性があり、これらは互いに関連しています。  

**このチュートリアルで学習する内容は次のとおりです。**

> [!div class="checklist"]
> * 新しいアプリを作成する
> * 意図を追加する 
> * 出発地と到着地を子として持つ場所階層エンティティを追加する
> * トレーニング
> * 発行
> * エンドポイントから意図とエンティティを取得する

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="hierarchical-data"></a>階層構造データ

このアプリでは、従業員の移動に関して出発地の都市と目的地の都市の場所を判断します。 発話内での場所の判断には、階層エンティティが使用されます。 

階層エンティティは、2 つのデータ (子の場所) が次のようなものであるため、この種のデータに適しています。

* シンプル エンティティである。
* 発話のコンテキストで相互に関連する。
* 特定の単語の選択を使用して、それぞれのエンティティを表す  ("から/への"、"を出て/に向かう"、"から/に" などの単語)。
* どちらの子も同じ発話内に頻出する。 
* クライアント アプリによって情報の単位としてグループ化され、処理される必要がある。

## <a name="create-a-new-app"></a>新しいアプリの作成

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-an-intent-to-move-employees-between-cities"></a>従業員を都市間で移動させる意図を作成する

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. **[Create new intent]\(意図の新規作成\)** を選択します。 

1. ポップアップ ダイアログ ボックスに「`MoveEmployeeToCity`」と入力して、**[完了]** を選択します。 

    ![新しい意図の作成ダイアログのスクリーンショット](./media/luis-quickstart-intent-and-hier-entity/create-new-intent-move-employee-to-city.png)

1. 発話の例を意図に追加します。

    |発話の例|
    |--|
    |シアトルを離任する John W. Smith をダラスへ移動させる|
    |Jill Jones をシアトルからカイロに転任させる|
    |John Jackson をタンパから出してアトランタに着任させる |
    |Debra Doughtery をタルサからダラスに移動させる|
    |カイロを離任する Jill Jones をタンパに移動|
    |Alice Anderson をオークランドからレドモンドへ移す|
    |サンフランシスコの Carl Chamerlin をレドモンドへ|
    |Steve Standish をサンディエゴからベルビューへ転任させる |
    |カンザスシティの Tanner Thompson を昇進させてシカゴへ移す|

    [ ![MoveEmployee 意図で新しい発話が指定された LUIS のスクリーンショット](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png)](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png#lightbox)

## <a name="create-a-location-entity"></a>場所エンティティを作成する
発話で出発地と目的地にラベルを付けて、LUIS に場所を認識させる必要があります。 トークン (未加工) のビューで発話を表示する必要がある場合は、発話の上にあるバーで、**[Entities view]\(エンティティ ビュー\)** というラベルが付いた切り替えコントロールを選択します。 スイッチを切り替えると、コントロールのラベルは **[Tokens View]\(トークン ビュー)** になります。

次のような発話について考えます。

```json
move John W. Smith leaving Seattle headed to Dallas
```

この発話では、`Seattle` と `Dallas` の 2 つの場所が指定されています。 クライアント アプリケーションで要求を完了するには、発話から両方のデータを抽出する必要があり、これらは互いに関連しているため、これらは両方とも階層構造エンティティ `Location` の子としてグループ化されます。 
 
階層エンティティに 1 つの子 (出発地または到着地) しか存在しない場合でも、やはり抽出されます。 1 つだけまたは一部を抽出するために、すべての子が見つかる必要はありません。 

1. 発話 `move John W. Smith leaving Seattle headed to Dallas` において、単語 `Seattle` を選びます。 上部にテキスト ボックスがあるドロップダウン メニューが表示されます。 テキスト ボックスにエンティティ名「`Location`」を入力し、を選択し、ドロップダウン メニューで **[Create new entity]\(新しいエンティティの作成\)** を選択します。 

    [![意図ページの新しいエンティティ作成のスクリーンショット](media/luis-quickstart-intent-and-hier-entity/create-location-hierarchical-entity-from-example-utterance.png "意図ページの新しいエンティティ作成のスクリーンショット")](media/luis-quickstart-intent-and-hier-entity/create-location-hierarchical-entity-from-example-utterance.png#lightbox)

1. ポップアップ ウィンドウで、**[Hierarchical]\(階層\)** エンティティ タイプを選択し、子エンティティとして `Origin` と `Destination` を選択します。 **[完了]** を選択します。

    ![新しい [場所] エンティティのエンティティ作成ポップアップ ダイアログのスクリーンショット](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-2.png "新しい [場所] エンティティのエンティティ作成ポップアップ ダイアログのスクリーンショット")

1. LUIS には用語が出発地か、到着地か、どちらでもないかわからないので、`Seattle` のラベルは `Location` とマークされています。 `Seattle` を選択し、**[Location]\(場所\)** を選択した後、右側のメニューに従って `Origin` を選択します。

    [![場所のエンティティの子を変更するためのエンティティ ラベル付けのポップアップ ダイアログのスクリーンショット](media/luis-quickstart-intent-and-hier-entity/choose-hierarchical-child-entity-from-example-utterance.png "場所のエンティティの子を変更するためのエンティティ ラベル付けのポップアップ ダイアログのスクリーンショット")](media/luis-quickstart-intent-and-hier-entity/choose-hierarchical-child-entity-from-example-utterance.png#lightbox)

1. 他のすべての発話内の他の場所にラベルを付けます。 すべての場所にマークが付くと、発話は次のように表示されます。 

    [![発話内でラベル付けされた [場所] エンティティのスクリーンショット](media/luis-quickstart-intent-and-hier-entity/all-intents-marked-with-origin-and-destination-location.png "発話内でラベル付けされた [場所] エンティティのスクリーンショット")](media/luis-quickstart-intent-and-hier-entity/all-intents-marked-with-origin-and-destination-location.png#lightbox)

    赤の下線は、LUIS によるエンティティの確実性が低いことを示しています。 これはトレーニングによって解決されます。 

## <a name="add-example-utterances-to-the-none-intent"></a>発話の例を None 意図に追加する 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>意図への変更をテストできるようにアプリをトレーニングする 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>トレーニング済みのモデルがエンドポイントからクエリ可能になるようにアプリを発行する

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>エンドポイントから意図およびエンティティ予測を取得する

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]


1. アドレス バーの URL の末尾に移動し、「`Please move Carl Chamerlin from Tampa to Portland`」と入力します。 最後の querystring パラメーターは `q` です。これは発話の**クエリ**です。 この発話はラベル付けされたどの発話とも異なるので、よいテストであり、`MoveEmployee` 意図と階層エンティティが抽出されて返される必要があります。

    ```json
    {
      "query": "Please move Carl Chamerlin from Tampa to Portland",
      "topScoringIntent": {
        "intent": "MoveEmployeeToCity",
        "score": 0.979823351
      },
      "intents": [
        {
          "intent": "MoveEmployeeToCity",
          "score": 0.979823351
        },
        {
          "intent": "None",
          "score": 0.0156363435
        }
      ],
      "entities": [
        {
          "entity": "portland",
          "type": "Location::Destination",
          "startIndex": 41,
          "endIndex": 48,
          "score": 0.6044041
        },
        {
          "entity": "tampa",
          "type": "Location::Origin",
          "startIndex": 32,
          "endIndex": 36,
          "score": 0.739491045
        }
      ]
    }
    ```
    
    正しい意図が予測され、エンティティ配列の対応する **entities** プロパティには出発地と到着地の両方の値が含まれています。
    
## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>関連情報

* [階層エンティティ](luis-concept-entity-types.md)の概念に関する情報
* [トレーニング方法](luis-how-to-train.md)
* [発行方法](luis-how-to-publish-app.md)
* [LUIS ポータルでのテスト方法](luis-interactive-test.md)
* [ロールと階層エンティティ](luis-concept-roles.md#roles-versus-hierarchical-entities)
* [パターンを使った予測精度の向上](luis-concept-patterns.md)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、新しい意図を作成し、出発地と到着地のコンテキストから学習されたデータに関する発話の例を追加しました。 アプリがトレーニングおよび発行されたら、クライアント アプリケーションはその情報を使用して、関連する情報を含む移動チケットを作成できます。

> [!div class="nextstepaction"] 
> [複合エンティティを追加する方法を確認する](luis-tutorial-composite-entity.md) 
