---
title: パターン
titleSuffix: Azure Cognitive Services
description: パターンを使用して意図とエンティティの予測を改善しますが、提供する発話の例は減らします。 このパターンは、エンティティと無視できるテキストを識別するための構文を含むテンプレート発話の例によって提供されます。
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 05af52ab492fcfe509b547efdd182a366642b9ed
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/22/2018
ms.locfileid: "53754409"
---
# <a name="tutorial-add-common-pattern-template-utterance-formats"></a>チュートリアル:一般的なパターン テンプレート発話フォーマットを追加する

このチュートリアルでは、パターンを使用して意図とエンティティの予測を改善しますが、提供する発話の例は減らします。 このパターンは、エンティティと無視できるテキストを識別するための構文を含むテンプレート発話の例によって提供されます。 パターンは、式の照合と機械学習の組み合わせです。  テンプレート発話の例は、意図の発話と共に、どのような発話が意図に適合するかを LUIS がより適切に解釈できるようにします。 

**このチュートリアルで学習する内容は次のとおりです。**

> [!div class="checklist"]
> * サンプル アプリをインポートする 
> * 意図を作成する
> * トレーニング
> * 発行
> * エンドポイントから意図とエンティティを取得する
> * パターンを作成する
> * パターン予測の改善を検証する
> * テキストを無視できるものとマークし、パターン内で入れ子にする
> * テスト パネルを使用してパターンの成功を検証する

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>サンプル アプリをインポートする

最後のチュートリアルで作成した、**HumanResources** という名前のアプリを引き続き使用します。 

次の手順に従います。

1.  [アプリの JSON ファイル](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-batchtest-HumanResources.json)をダウンロードして保存します。

2. JSON を新しいアプリにインポートします。

3. **[管理]** セクションの **[バージョン]** タブで、バージョンを複製し、それに `patterns` という名前を付けます。 複製は、元のバージョンに影響を及ぼさずに LUIS のさまざまな機能を使用するための優れた方法です。 バージョン名は URL ルートの一部として使用されるため、URL 内で有効ではない文字を名前に含めることはできません。

## <a name="create-new-intents-and-their-utterances"></a>新しい意図とその発話を作成する

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

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

    [![LUIS での意図への新規発話追加のスクリーンショット](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png "LUIS での意図への新規発話追加のスクリーンショット")](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png#lightbox)

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

[!INCLUDE [Too few examples](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]

## <a name="train"></a>トレーニング

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>発行

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>エンドポイントから意図とエンティティを取得する

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. アドレスの URL の末尾に移動し、「`Who is the boss of Jill Jones?`」と入力します。 最後の querystring パラメーターは `q` です。これは発話の**クエリ**です。 

    ```json
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

この 2 つ目のブラウザー ウィンドウは開いたままにします。 後でまた使います。 

## <a name="template-utterances"></a>テンプレート発話
人事ドメインの性質上、組織内の従業員の関係について一般的な尋ね方がいくつかあります。 例: 

|発話|
|--|
|Jill Jones は誰に報告しますか?|
|誰が Jill Jones に報告しますか?|

発話の多数の例を提供せずに、それぞれの文脈の一意性を判断するには、これらの発話は類似しすぎています。 意図のパターンを追加することにより、発話の多数の例を提供しなくても、LUIS は意図の一般的な発話パターンを学習します。 

この意図のテンプレート発話例には、次のものが含まれます:

|テンプレート発話例|構文の意味|
|--|--|
|{Employee} は誰に報告しますか[?]|交換可能な {Employee}、[?] を無視}|
|誰が {Employee} に報告しますか[?]|交換可能な {Employee}、[?] を無視}|

`{Employee}` 構文では、テンプレート発話内でのエンティティの位置とそれがどのエンティティであるかがマークされます。 オプションの構文 `[?]` は、オプションである単語または句読点をマークします。 LUIS は、かっこ内のオプションのテキストを無視し、発話を照合します。

構文は正規表現のように見えますが、正規表現ではありません。 中かっこ `{}` と角かっこ `[]` の構文のみがサポートされています。 これらは 2 階層まで入れ子にすることができます。

パターンが発話に一致するには、まず、発話内のエンティティがテンプレート発話内のエンティティに一致している必要があります。 ただし、テンプレートは意図の予測にのみ役立ち、エンティティの予測には役立ちません。 

**パターンを使用すると提供される発話の例を少なくすることができますが、エンティティが検出されない場合、パターンは一致しません。**

このチュートリアルでは、2 つの新しい意図 `OrgChart-Manager` および `OrgChart-Reports` を追加します。 

|意図|発話|
|--|--|
|OrgChart-Manager|Jill Jones は誰に報告しますか?|
|OrgChart-Reports|誰が Jill Jones に報告しますか?|

LUIS がクライアント アプリに予測を返したら、そのクライアント アプリで意図の名前を関数名として使用でき、従業員エンティティをその関数へのパラメーターとして使用できます。

```nodejs
OrgChartManager(employee){
    ///
}
```

従業員が[リスト エンティティのチュートリアル](luis-quickstart-intent-and-list-entity.md)で作成されたことに注意してください。

1. 上部メニューの **[ビルド]** を選択します。

2. 左側のナビゲーションの **[Improve app performance]\(アプリのパフォーマンスの改善\)** で、**[Patterns]\(パターン\)** を選択します。

3. **OrgChart-Manager** 意図を選択し、次のテンプレート発話を入力します。

    |テンプレート発話|
    |:--|
    |{Employee} は誰の部下ですか[?]|
    |{Employee} は誰に報告しますか[?]|
    |{Employee} のマネージャーは誰ですか[?]|
    |{Employee} は誰に直接報告しますか[?]|
    |{Employee} の監督者は誰ですか[?]|
    |{Employee} の上司は誰ですか[?]|

    ロールを持つエンティティはロール名が含まれた構文を使用し、[ロールのための別のチュートリアル](luis-tutorial-pattern-roles.md)で説明されています。 

    テンプレート発話を入力する場合は、左の中かっこ `{` を入力すると LUIS がエンティティの入力を支援します。

    [![意図のためのテンプレート発話の入力を示すスクリーンショット](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png)](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png#lightbox)

4. **OrgChart-Reports** 意図を選択し、次のテンプレート発話を入力します。

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

2. ブラウザーのタブをエンドポイント URL のタブに再び切り替えます。

3. アドレスの URL の末尾に移動し、発話として「`Who is the boss of Jill Jones?`」と入力します。 最後の querystring パラメーターは `q` です。これは発話の**クエリ**です。 

    ```json
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

## <a name="working-with-optional-text-and-prebuilt-entities"></a>省略可能なテキストと事前構築済みエンティティの使用

このチュートリアルの前掲のパターン テンプレートの発話には、文字 s の所有格での使用 `'s` や疑問符の使用 `?` など、省略可能なテキストの例がいくつか含まれました。 マネージャーおよび人事担当者が履歴データを探しているだけでなく、将来の日付で発生する従業員の社内異動を計画していることをエンドポイントの発話が示しているものとします。

発話の例は次のようになります。

|意図|省略可能なテキストと事前構築済みエンティティを含む発話の例|
|:--|:--|
|OrgChart-Manager|`Who was Jill Jones manager on March 3?`|
|OrgChart-Manager|`Who is Jill Jones manager now?`|
|OrgChart-Manager|`Who will be Jill Jones manager in a month?`|
|OrgChart-Manager|`Who will be Jill Jones manager on March 3?`|

これらの各例では、LUIS が正しく予測するために必要な動詞の時制 `was`、`is`、`will be`、および日付 `March 3`、`now`、`in a month` が使用されています。 最後の 2 つの例では、`in` と `on` 除きほとんど同じテキストが使用されていることに注意してください。

テンプレート発話の例:
|意図|省略可能なテキストと事前構築済みエンティティを含む発話の例|
|:--|:--|
|OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?`|
|OrgChart-Manager|`who is {Employee}['s] manager [[on]{datetimeV2}?]`|
|OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
|OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

省略可能な構文の角かっこ `[]` を使用すると、この省略可能なテキストをテンプレート発話に簡単に追加でき、最大 2 レベルまで入れ子にでき `[[]]`、エンティティやテキストを含めることができます。

**質問: 最後の 2 つの発話例を 1 つのテンプレート発話にまとめることができないのはなぜですか。** パターン テンプレートは、OR 構文をサポートしていません。 `in` バージョンと `on` バージョンの両方をキャッチするには、それぞれを別のテンプレート発話にする必要があります。

**質問: 各テンプレート発話の最初の文字 `w` がすべて小文字になっているのはなぜですか。必要に応じて大文字または小文字にする必要はありませんか。** クライアント アプリケーションによってクエリ エンドポイントに送信される発話は、小文字に変換されます。 テンプレート発話は大文字でも小文字でもよく、エンドポイント発話もどちらでもかまいません。 比較は常に小文字への変換後に行われます。

**質問: 3 月 3 日が数値 `3` および日付 `March 3` の両方として予測される場合、事前構築済みの数がテンプレート発話の一部になっていないのはなぜですか。** コンテキスト上、テンプレート発話では日付を `March 3` のようにリテラルで、または `in a month` のように抽象化して使用しています。 日付は数値を含むことができますが、数値は必ずしも日付として見えなくてもかまいません。 常に、予測 JSON の結果で返したい種類をもっともよく表すエンティティを使用します。  

**質問: `Who will {Employee}['s] manager be on March 3?` のような不適切な語句の発話はどうなりますか。** `will` と `be` が分けられるこのような文法的に異なる動詞の時制は、新しいテンプレート発話にする必要があります。 既存のテンプレート発話は一致しません。 発話の意図は変わっていませんが、発話での単語の配置が変わっています。 この変更は、LUIS での予測に影響します。

**注意: エンティティが最初に見つけられた後、パターンが照合されます。**

## <a name="edit-the-existing-pattern-template-utterance"></a>既存のパターン テンプレート発話を編集する

1. LUIS の Web サイトで、上部メニューの **[Build]\(ビルド\)** を選択し、左側のメニューで **[Patterns]\(パターン\)** を選択します。 

2. 既存のテンプレート発話 `Who is {Employee}['s] manager[?]` を見つけて、右側の省略記号 [***...***] を選択します。 

3. ポップアップ メニューから **[Edit]\(編集\)** を選択します。 

4. テンプレートの発話を `who is {Employee}['s] manager [[on]{datetimeV2}?]]` に変更します。

## <a name="add-new-pattern-template-utterances"></a>新しいパターン テンプレート発話を追加する

1. **[Build]\(ビルド\)** の **[Patterns]\(パターン\)** セクションで、複数の新しいパターン テンプレート発話を追加します。 [Intent]\(意図\) ドロップダウン メニューから **OrgChart-Manager** を選択し、次のテンプレート発話を入力します。

    |意図|省略可能なテキストと事前構築済みエンティティを含む発話の例|
    |--|--|
    |OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?]`|
    |OrgChart-Manager|`who is {Employee}['s] manager [[on]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

2. アプリをトレーニングします。

3. パネル上部の **[Test]\(テスト\)** を選択して、テスト パネルを開きます。 

4. テスト発話をいくつか入力し、パターンが一致して意図スコアが十分に高いことを確認します。 

    最初の発話を入力した後、結果の下の **[Inspect]\(検査\)** を選択してすべての予測結果を見ることができます。

    |発話|
    |--|
    |Who will be Jill Jones manager|
    |who will be jill jones's manager|
    |Who will be Jill Jones's manager?|
    |who will be Jill jones manager on March 3|
    |Who will be Jill Jones manager next Month|
    |Who will be Jill Jones manager in a month?|

これらの発話はすべてエンティティ内で検出され、したがって同じパターンに一致し、高い予測スコアになります。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、多くの発話例を追加しなければ高い精度での予測が難しかった発話のために 2 つの意図を追加します。 これらのパターンの追加によって LUIS での意図の予測が改善され、スコアが大幅に向上しました。 エンティティと無視可能テキストのマーク付けによって、LUIS でより多様な発話にパターンを適用できるようになりました。

> [!div class="nextstepaction"]
> [パターンと共にロールを使用する方法について](luis-tutorial-pattern-roles.md)
