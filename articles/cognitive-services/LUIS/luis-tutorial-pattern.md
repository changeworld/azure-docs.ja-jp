---
title: パターンを使用して LUIS の予測を向上させる方法のチュートリアル - Azure | Microsoft Docs
titleSuffix: Cognitive Services
description: このチュートリアルでは、意図のパターンを使用して、LUIS による意図とエンティティの予測を向上させます。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 07/20/2018
ms.author: diberry
ms.openlocfilehash: 9ad1d9e1543c3d9a74025fb23bd1767478b53b4b
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2018
ms.locfileid: "39238456"
---
# <a name="tutorial-improve-app-with-patterns"></a>チュートリアル: パターンを使用してアプリを改善する

このチュートリアルでは、パターンを使用して意図とエンティティの予測を向上させます。  

> [!div class="checklist"]
* パターンがアプリで役立つことを確認する方法
* パターンを作成する方法 
* パターン予測の改善を検証する方法

この記事に従って LUIS アプリケーションを作成するには、無料の [LUIS](luis-reference-regions.md) アカウントが必要です。

## <a name="before-you-begin"></a>開始する前に
[バッチ テスト](luis-tutorial-batch-testing.md) チュートリアルの人事アプリがない場合は、JSON を [LUIS](luis-reference-regions.md#luis-website) Web サイトの新しいアプリに[インポート](luis-how-to-start-new-app.md#import-new-app)します。 インポートするアプリは、[LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-batchtest-HumanResources.json) GitHub リポジトリにあります。

元の Human Resources アプリを保持する場合は、[[Settings]\(設定\)](luis-how-to-manage-versions.md#clone-a-version) ページでバージョンを複製し、`patterns` という名前を付けます。 複製は、元のバージョンに影響を及ぼさずに LUIS のさまざまな機能を使用するための優れた方法です。 

## <a name="patterns-teach-luis-common-utterances-with-fewer-examples"></a>パターンによって少ない例で一般的な発話を LUIS に学習させる
人事ドメインの性質上、組織内の従業員の関係について一般的な尋ね方がいくつかあります。 例: 

```
Who does Jill Jones report to?
Who reports to Jill Jones? 
```

発話の多数の例を提供せずに、それぞれの文脈の一意性を判断するには、これらの発話は類似しすぎています。 意図のパターンを追加することにより、発話の多数の例を提供しなくても、LUIS は意図の一般的な発話パターンを学習します。 

この意図のテンプレート発話の例を次に示します。

```
Who does {Employee} report to?
Who reports to {Employee}? 
```

このパターンは、エンティティと無視できるテキストを識別するための構文を含むテンプレート発話の例によって提供されます。 パターンは、正規表現の照合と機械学習の組み合わせです。  テンプレート発話の例は、意図の発話と共に、どのような発話が意図に適合するかを LUIS がより適切に解釈できるようにします。

パターンが発話に一致するには、まず、発話内のエンティティがテンプレート発話内のエンティティに一致している必要があります。 ただし、テンプレートは意図の予測にのみ役立ち、エンティティの予測には役立ちません。 

**パターンを使用すると提供される発話の例を少なくすることができますが、エンティティが検出されない場合、パターンは一致しません。**

従業員が[リスト エンティティのチュートリアル](luis-quickstart-intent-and-list-entity.md)で作成されたことに注意してください。

## <a name="create-new-intents-and-their-utterances"></a>新しい意図とその発話を作成する
2 つの新しい意図である `OrgChart-Manager` と `OrgChart-Reports` を追加します。 LUIS がクライアント アプリに予測を返したら、そのクライアント アプリで意図の名前を関数名として使用でき、従業員エンティティをその関数へのパラメーターとして使用できます。

```
OrgChart-Manager(employee){
    ///
}
```

1. 人事アプリは必ず、LUIS の**ビルド** セクションに配置してください。 右上のメニュー バーの **[Build]\(ビルド\)** を選択すると、このセクションに変更できます。 

2. **[Intents]\(意図\)** ページで、**[Create new intent]\(意図の新規作成\)** を選びます。 

3. ポップアップ ダイアログ ボックスに「`OrgChart-Manager`」と入力して、**[完了]** を選択します。

    ![新しいメッセージ ポップアップ ウィンドウを作成する](media/luis-tutorial-pattern/hr-create-new-intent-popup.png)

4. 発話の例を意図に追加します。

    |発話の例|
    |--|
    |John W. Smith は誰の部下ですか?|
    |John W. Smith は誰に報告しますか?|
    |John W. Smith のマネージャーは誰ですか?|
    |Jill Jones は誰に直接報告しますか?|
    |Jill Jones の監督者は誰ですか?|

    [![](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png "意図に新しい発話を追加している LUIS のスクリーンショット")](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png#lightbox)

    意図の発話で従業員エンティティの代わりに keyPhrase エンティティにラベルが付いているかどうかは気にしないでください。 どちらも [テスト] ウィンドウとエンドポイントで正しく予測されます。 

5. 左側のナビゲーションで、**[Intents]\(意図\)** を選択します。

6. **[Create new intent]\(意図の新規作成\)** を選択します。 

7. ポップアップ ダイアログ ボックスに「`OrgChart-Reports`」と入力して、**[完了]** を選択します。

8. 発話の例を意図に追加します。

    |発話の例|
    |--|
    |John W. Smith の部下は誰ですか?|
    |誰が John W. Smith に報告しますか?|
    |Smith は誰を管理していますか?|
    |Jill Jones の直属の部下は誰ですか?|
    |Jill Jones は誰を監督していますか?|

## <a name="caution-about-example-utterance-quantity"></a>発話の例の量に関する注意
これらの意図での発話の例の量は、LUIS の適切なトレーニングに十分ではありません。 実際のアプリでは、各意図に、さまざまな単語の選択や発話の長さを持つ少なくとも 15 の発話が必要です。 これらのいくつかの発話は、特にパターンを強調するために選択されます。 

## <a name="train-the-luis-app"></a>LUIS アプリをトレーニングする
新しい意図と発話には、トレーニングが必要です。 

1. LUIS Web サイトの右上にある **[Train]\(トレーニング\)** ボタンを選択します。

    ![[Train]\(トレーニング\) ボタンの画像](./media/luis-tutorial-pattern/hr-train-button.png)

2. 成功したことを示す緑色のステータス バーが Web サイトの上部に表示されたら、トレーニングは完了しています。

    ![成功の通知バーの画像](./media/luis-tutorial-pattern/hr-trained-inline.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>アプリを公開してエンドポイント URL を取得する
チャットボットや他のアプリケーションで LUIS の予測を取得するには、アプリを公開する必要があります。 

1. LUIS Web サイトの右上にある **[Publish]\(公開\)** ボタンを選択します。 

2. [Production]\(運用\) スロットを選択し、**[Publish]\(公開\)** ボタンを選択します。

    [ ![運用スロットへの [発行] ボタンが強調表示されている [発行] ページのスクリーンショット](./media/luis-tutorial-pattern/hr-publish-to-production.png)](./media/luis-tutorial-pattern/hr-publish-to-production.png#lightbox)

3. 成功したことを示す緑色のステータス バーが Web サイトの上部に表示されたら、公開は完了しています。

## <a name="query-the-endpoint-with-a-different-utterance"></a>異なる発話でエンドポイントにクエリを実行する
1. **[Publish]\(公開\)** ページで、ページの下部にある**エンドポイント**のリンクを選択します。 別のブラウザー ウィンドウが開き、アドレス バーにエンドポイント URL が表示されます。 

    [ ![エンドポイント URL が強調表示されている [発行] ページのスクリーンショット](./media/luis-tutorial-pattern/hr-publish-select-endpoint.png)](./media/luis-tutorial-pattern/hr-publish-select-endpoint.png#lightbox)


2. アドレスの URL の末尾に移動し、「`Who is the boss of Jill Jones?`」と入力します。 最後の querystring パラメーターは `q` です。これは発話の**クエリ**です。 

    ```JSON
    {
        "query": "who is the boss of jill jones?",
        "topScoringIntent": {
            "intent": "OrgChart-Manager",
            "score": 0.353984952
        },
        "intents": [
            {
                "intent": "OrgChart-Manager",
                "score": 0.353984952
            },
            {
                "intent": "OrgChart-Reports",
                "score": 0.214128986
            },
            {
                "intent": "EmployeeFeedback",
                "score": 0.08434003
            },
            {
                "intent": "MoveEmployee",
                "score": 0.019131
            },
            {
                "intent": "GetJobInformation",
                "score": 0.004819009
            },
            {
                "intent": "Utilities.Confirm",
                "score": 0.0043958663
            },
            {
                "intent": "Utilities.StartOver",
                "score": 0.00312064588
            },
            {
                "intent": "Utilities.Cancel",
                "score": 0.002265454
            },
            {
                "intent": "Utilities.Help",
                "score": 0.00133465114
            },
            {
                "intent": "None",
                "score": 0.0011388344
            },
            {
                "intent": "Utilities.Stop",
                "score": 0.00111166481
            },
            {
                "intent": "FindForm",
                "score": 0.0008900076
            },
            {
                "intent": "ApplyForJob",
                "score": 0.0007836131
            }
        ],
        "entities": [
            {
                "entity": "jill jones",
                "type": "Employee",
                "startIndex": 19,
                "endIndex": 28,
                "resolution": {
                    "values": [
                        "Employee-45612"
                    ]
                }
            },
            {
                "entity": "boss of jill jones",
                "type": "builtin.keyPhrase",
                "startIndex": 11,
                "endIndex": 28
            }
        ]
    }
    ```

このクエリは成功しましたか。 このトレーニング サイクルの場合、それは成功しました。 2 つの最上位の意図のスコアは接近しています。 LUIS トレーニングは毎回まったく同じではなく、少し変動があるため、次のトレーニング サイクルではこれらの 2 つのスコアが逆になる可能性があります。 その結果、間違った意図が返されることがあります。 

正しい意図のスコアを割合で大幅に高く、また次に高いスコアからさらに離れたものにするパターンを使用してください。 

## <a name="add-the-template-utterances"></a>テンプレート発話を追加する

1. 最上位メニューの **[構築]** を選択します。

2. 左側のナビゲーションの **[Improve app performance]\(アプリのパフォーマンスの改善\)** で、**[Patterns]\(パターン\)** を選択します。

3. **[OrgChart-Manager]** 意図を選択してから、次のテンプレート発話を 1 つずつ入力し、各テンプレート発話の後に Enter キーを押します。

    |テンプレート発話|
    |:--|
    |{Employee} は誰の部下ですか[?]|
    |{Employee} は誰に報告しますか[?]|
    |{Employee} のマネージャーは誰ですか[?]|
    |{Employee} は誰に直接報告しますか[?]|
    |{Employee} の監督者は誰ですか[?]|
    |{Employee} の上司は誰ですか[?]|

    `{Employee}` 構文では、テンプレート発話内でのエンティティの位置とそれがどのエンティティであるかがマークされます。 
    
    ロールを持つエンティティはロール名が含まれた構文を使用し、[ロールのための別のチュートリアル](luis-tutorial-pattern-roles.md)で説明されています。 

    オプションの構文 `[]` は、オプションである単語または句読点をマークします。 LUIS は、かっこ内のオプションのテキストを無視し、発話を照合します。

    テンプレート発話を入力する場合は、左の中かっこ `{` を入力すると LUIS がエンティティの入力を支援します。

    [ ![意図のためのテンプレート発話の入力を示すスクリーンショット](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png)](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png#lightbox)



4. **[OrgChart-Reports]** 意図を選択してから、次のテンプレート発話を 1 つずつ入力し、各テンプレート発話の後に Enter キーを押します。

    |テンプレート発話|
    |:--|
    |{Employee} の部下は誰ですか[?]|
    |誰が {Employee} に報告しますか[?]|
    |{Employee} は誰を管理していますか[?]|
    |{Employee} の直属の部下は誰ですか[?]|
    |{Employee} は誰を監督していますか[?]|
    |{Employee} の上司は誰ですか[?]|

## <a name="query-endpoint-when-patterns-are-used"></a>パターンが使用されるときにエンドポイントにクエリを実行する

1. アプリを再度トレーニングして発行します。

2. **[Publish]\(公開\)** ページで、ページの下部にある**エンドポイント**のリンクを選択します。 別のブラウザー ウィンドウが開き、アドレス バーにエンドポイント URL が表示されます。 

3. アドレスの URL の末尾に移動し、発話として「`Who is the boss of Jill Jones?`」と入力します。 最後の querystring パラメーターは `q` です。これは発話の**クエリ**です。 

    ```JSON
    {
        "query": "who is the boss of jill jones?",
        "topScoringIntent": {
            "intent": "OrgChart-Manager",
            "score": 0.9999989
        },
        "intents": [
            {
                "intent": "OrgChart-Manager",
                "score": 0.9999989
            },
            {
                "intent": "OrgChart-Reports",
                "score": 7.616303E-05
            },
            {
                "intent": "EmployeeFeedback",
                "score": 7.84204349E-06
            },
            {
                "intent": "GetJobInformation",
                "score": 1.20674213E-06
            },
            {
                "intent": "MoveEmployee",
                "score": 7.91245157E-07
            },
            {
                "intent": "None",
                "score": 3.875E-09
            },
            {
                "intent": "Utilities.StartOver",
                "score": 1.49E-09
            },
            {
                "intent": "Utilities.Confirm",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Help",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Stop",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Cancel",
                "score": 1.225E-09
            },
            {
                "intent": "FindForm",
                "score": 1.123077E-09
            },
            {
                "intent": "ApplyForJob",
                "score": 5.625E-10
            }
        ],
        "entities": [
            {
                "entity": "jill jones",
                "type": "Employee",
                "startIndex": 19,
                "endIndex": 28,
                "resolution": {
                    "values": [
                        "Employee-45612"
                    ]
                },
                "role": ""
            },
            {
                "entity": "boss of jill jones",
                "type": "builtin.keyPhrase",
                "startIndex": 11,
                "endIndex": 28
            }
        ]
    }
    ```

これで、意図の予測が大幅に高くなりました。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ
不要になったら、LUIS アプリを削除します。 それを行うには、アプリの一覧内のアプリ名の右にある省略記号 (***...***) を選択し、**[削除]** を選択します。 **[Delete app?]\(アプリを削除しますか?\)** ポップアップ ダイアログで、**[OK]** をクリックします。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [パターンと共にロールを使用する方法について](luis-tutorial-pattern-roles.md)