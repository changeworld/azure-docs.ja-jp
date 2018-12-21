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
ms.date: 12/05/2018
ms.author: diberry
ms.openlocfilehash: a79c0091220e2980101471abaaa0aaf4c0a898ca
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53104409"
---
# <a name="tutorial-5-extract-contextually-related-data"></a>チュートリアル 5: 文脈的に関連するデータを抽出する
このチュートリアルでは、コンテキストに基づいて関連するデータを検索します。 たとえば、あるビルやオフィスから別のビルやオフィスへの物理的な移動の出発地と到着地が関連します。 作業指示を生成するには、両方のデータが必要である可能性があり、これらは互いに関連しています。  

このアプリは、従業員の移動の出発地 (建物とオフィス) と目的地 (建物とオフィス) を判断します。 発話内での場所の判断には、階層エンティティが使用されます。 **階層**エンティティの目的は、コンテキストに基づいて発話内から関連データを検索することです。 

階層エンティティは、2 つのデータが次のようなものであるため、この種のデータに適しています。

* シンプル エンティティである。
* 発話のコンテキストで相互に関連する。
* 特定の単語を使用して、それぞれの場所を表す  ("から/への"、"を出て/に向かう"、"から/に" などの単語)。
* どちらの場所も同じ発話内に頻出します。 
* クライアント アプリによって情報の単位としてグループ化され、処理される必要がある。

**このチュートリアルで学習する内容は次のとおりです。**

> [!div class="checklist"]
> * 既存のチュートリアル アプリを使用する
> * 意図を追加する 
> * 出発地と到着地を子として持つ場所階層エンティティを追加する
> * トレーニング
> * [発行]
> * エンドポイントから意図とエンティティを取得する

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>既存のアプリを使用する
最後のチュートリアルで作成した、**HumanResources** という名前のアプリを引き続き使用します。 

以前のチュートリアルの HumanResources アプリがない場合は、次の手順を使用します。

1.  [アプリの JSON ファイル](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-list-HumanResources.json)をダウンロードして保存します。

2. JSON を新しいアプリにインポートします。

3. **[管理]** セクションの **[バージョン]** タブで、バージョンを複製し、それに `hier` という名前を付けます。 複製は、元のバージョンに影響を及ぼさずに LUIS のさまざまな機能を使用するための優れた方法です。 バージョン名は URL ルートの一部として使用されるため、URL 内で有効ではない文字を名前に含めることはできません。 

## <a name="remove-prebuilt-number-entity-from-app"></a>事前構築済みの番号エンティティをアプリから削除する
発話全体を表示して、階層の子をマークするには、[事前構築済みの番号エンティティを一時的に削除します](luis-prebuilt-entities.md#marking-entities-containing-a-prebuilt-entity-token)。 

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. 左側のメニューから **[Entities]\(エンティティ\)** を選択します。

3. 一覧で番号エンティティの左側にあるチェック ボックスをオンにします。 **[削除]** を選択します。 

## <a name="add-utterances-to-moveemployee-intent"></a>MoveEmployee 意図に発話を追加する

1. 左側のメニューから **[Intents]\(意図\)** を選びます。

2. 意図の一覧から **[MoveEmployee]** を選択します。

3. 次の発話の例を追加します。

    |発話の例|
    |--|
    |W. John Smith を a-2345 **に**移動する|
    |Jill Jones を b-3499 **に**向かわせる|
    |hh-2345 **から** e-0234 **への** x23456 の移動を整理する|
    |a-3459 **を出て** f-34567 **に向かう** x12345 を設定する事務処理を開始する|
    |g-2323 **から** hh-2345 **に** 425-555-0000 を移動させる|

    [ ![MoveEmployee 意図で新しい発話が指定された LUIS のスクリーンショット](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png)](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png#lightbox)

    [リスト エンティティ](luis-quickstart-intent-and-list-entity.md) チュートリアルでは、従業員は名前、メール アドレス、内線番号、携帯電話番号、または米国連邦政府の社会保障番号で指定されます。 これらの従業員番号は発話で使用されます。 前の発話の例では、さまざまな言葉で出発地と目的地が表されており、その言葉は太字で示されています。 目的地のみの発話も 2 つあります。 これは、出発地が指定されていないときに、これらの場所が発話の中でどのように配置されているかを LUIS に認識させるうえで役立ちます。     

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="create-a-location-entity"></a>場所エンティティを作成する
発話で出発地と目的地にラベルを付けて、LUIS に場所を認識させる必要があります。 トークン (未加工) のビューで発話を表示する必要がある場合は、発話の上にあるバーで、**[Entities view]\(エンティティ ビュー\)** というラベルが付いた切り替えコントロールを選択します。 スイッチを切り替えると、コントロールのラベルは **[Tokens View]\(トークン ビュー)** になります。

次のような発話について考えます。

```json
mv Jill Jones from a-2349 to b-1298
```

この発話では、`a-2349` と `b-1298` の 2 つの場所が指定されています。 アルファベットの文字は建物の名前を、数字は建物内のオフィスを表すとします。 クライアント アプリケーションで要求を完了するには、発話から両方のデータを抽出する必要があり、これらは互いに関連しているため、これらが両方とも階層構造エンティティ `Locations` の子としてグループ化されることは理にかなっています。 
 
階層エンティティに 1 つの子 (出発地または到着地) しか存在しない場合でも、やはり抽出されます。 1 つだけまたは一部を抽出するために、すべての子が見つかる必要はありません。 

1. 発話 `Displace 425-555-0000 away from g-2323 toward hh-2345` において、単語 `g-2323` を選びます。 上部にテキスト ボックスがあるドロップダウン メニューが表示されます。 テキスト ボックスにエンティティ名「`Locations`」を入力し、を選択し、ドロップダウン メニューで **[Create new entity]\(新しいエンティティの作成\)** を選択します。 

    [![意図ページの新しいエンティティ作成のスクリーンショット](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-1.png "意図ページの新しいエンティティ作成のスクリーンショット")](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-1.png#lightbox)

2. ポップアップ ウィンドウで、**[Hierarchical]\(階層\)** エンティティ タイプを選択し、子エンティティとして `Origin` と `Destination` を選択します。 **[完了]** を選択します。

    ![新しい [場所] エンティティのエンティティ作成ポップアップ ダイアログのスクリーンショット](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-2.png "新しい [場所] エンティティのエンティティ作成ポップアップ ダイアログのスクリーンショット")

3. LUIS には用語が出発地か、到着地か、どちらでもないかわからないので、`g-2323` のラベルは `Locations` とマークされています。 `g-2323`、**[場所]** の順に選択し、右側のメニューに従って `Origin` を選択します。

    [![場所のエンティティの子を変更するためのエンティティ ラベル付けのポップアップ ダイアログのスクリーンショット](media/luis-quickstart-intent-and-hier-entity/hr-label-entity.png "場所のエンティティの子を変更するためのエンティティ ラベル付けのポップアップ ダイアログのスクリーンショット")](media/luis-quickstart-intent-and-hier-entity/hr-label-entity.png#lightbox)

5. 他のすべての発話で他の場所にラベルを付けます。それには、発話で建物とオフィスを選択し、[場所] を選択します。次に、右側のメニューに従って `Origin` または `Destination` を選択します。 すべての場所にラベルが付いたら、**[Tokens View]\(トークン ビュー\)** の発話は次のように表示されます。 

    [![発話内でラベル付けされた [場所] エンティティのスクリーンショット](media/luis-quickstart-intent-and-hier-entity/hr-entities-labeled.png "発話内でラベル付けされた [場所] エンティティのスクリーンショット")](media/luis-quickstart-intent-and-hier-entity/hr-entities-labeled.png#lightbox)

## <a name="add-prebuilt-number-entity-to-app"></a>事前構築済みの番号エンティティをアプリに追加する
事前構築済みの番号エンティティをアプリケーションに再度追加します。

1. 左側のナビゲーション メニューの **[Entities]\(エンティティ\)** を選択します。

2. **[Add prebuilt entity]\(作成済みエンティティの追加\)** ボタンを選択します。

3. 事前構築済みエンティティの一覧から **[number]\(番号\)** を選択し、**[完了]** を選択します。

    ![[number] が選択されている事前構築済みエンティティ ダイアログのスクリーンショット](./media/luis-quickstart-intent-and-hier-entity/hr-add-number-back-ddl.png)

## <a name="train-the-luis-app"></a>LUIS アプリをトレーニングする

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>アプリを公開してエンドポイント URL を取得する

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>異なる発話でエンドポイントにクエリを実行する

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]


2. アドレス バーの URL の末尾に移動し、「`Please relocation jill-jones@mycompany.com from x-2345 to g-23456`」と入力します。 最後の querystring パラメーターは `q` です。これは発話の**クエリ**です。 この発話はラベル付けされたどの発話とも異なるので、よいテストであり、`MoveEmployee` 意図と階層エンティティが抽出されて返される必要があります。

    ```json
    {
      "query": "Please relocation jill-jones@mycompany.com from x-2345 to g-23456",
      "topScoringIntent": {
        "intent": "MoveEmployee",
        "score": 0.9966052
      },
      "intents": [
        {
          "intent": "MoveEmployee",
          "score": 0.9966052
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.0325253047
        },
        {
          "intent": "FindForm",
          "score": 0.006137873
        },
        {
          "intent": "GetJobInformation",
          "score": 0.00462633232
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.00415637763
        },
        {
          "intent": "ApplyForJob",
          "score": 0.00382325822
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00249120337
        },
        {
          "intent": "None",
          "score": 0.00130756292
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.00119622645
        },
        {
          "intent": "Utilities.Confirm",
          "score": 1.26910036E-05
        }
      ],
      "entities": [
        {
          "entity": "jill - jones @ mycompany . com",
          "type": "Employee",
          "startIndex": 18,
          "endIndex": 41,
          "resolution": {
            "values": [
              "Employee-45612"
            ]
          }
        },
        {
          "entity": "x - 2345",
          "type": "Locations::Origin",
          "startIndex": 48,
          "endIndex": 53,
          "score": 0.8520272
        },
        {
          "entity": "g - 23456",
          "type": "Locations::Destination",
          "startIndex": 58,
          "endIndex": 64,
          "score": 0.974032
        },
        {
          "entity": "-2345",
          "type": "builtin.number",
          "startIndex": 49,
          "endIndex": 53,
          "resolution": {
            "value": "-2345"
          }
        },
        {
          "entity": "-23456",
          "type": "builtin.number",
          "startIndex": 59,
          "endIndex": 64,
          "resolution": {
            "value": "-23456"
          }
        }
      ]
    }
    ```
    
    正しい意図が予測され、エンティティ配列の対応する **entity** プロパティには出発地と到着地の両方の値が含まれています。
    

## <a name="could-you-have-used-a-regular-expression-for-each-location"></a>それぞれの場所で正規表現を使用できますか。
はい。出発地と到着地のロールを含む正規表現エンティティを作成し、それをパターン内で使用します。

この例の場所 (`a-1234` など) は、特定の形式に従っています。つまり、1 文字または 2 文字のアルファベット、ハイフン、4 桁または 5 桁の数字が順番に使用されています。 このデータは、場所ごとのロールを含む正規表現エンティティとして記述できます。 ロールは、パターンでのみ使用できます。 これらの発話に基づいてパターンを作成した後、場所の形式に対して正規表現を作成し、それをパターンに追加できます。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="hierarchical-entities-versus-roles"></a>階層エンティティとロール

詳細については、「[ロールと階層エンティティ](luis-concept-roles.md#roles-versus-hierarchical-entities)」を参照してください。

## <a name="next-steps"></a>次の手順
このチュートリアルでは、新しい意図を作成し、出発地と到着地のコンテキストから学習されたデータに関する発話の例を追加しました。 アプリがトレーニングおよび発行されたら、クライアント アプリケーションはその情報を使用して、関連する情報を含む移動チケットを作成できます。

> [!div class="nextstepaction"] 
> [複合エンティティを追加する方法を確認する](luis-tutorial-composite-entity.md) 
