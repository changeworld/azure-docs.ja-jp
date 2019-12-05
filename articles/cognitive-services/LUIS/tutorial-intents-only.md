---
title: チュートリアル:意図の予測 - LUIS
titleSuffix: Azure Cognitive Services
description: このチュートリアルでは、ユーザーの意図を予測するカスタム アプリを作成します。 このアプリは、メール アドレスや日付などの発話テキストからさまざまなデータ要素を抽出しないため、最も単純な種類の LUIS アプリです。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: 4d096ee829a425af3763c212daf5049acccf9f19
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325926"
---
# <a name="tutorial-build-a-luis-app-to-determine-user-intentions"></a>チュートリアル:ユーザーの意図を特定する LUIS アプリを構築する

このチュートリアルでは、発話 (テキスト) に基づいてユーザーの意図を予測するカスタム アプリを作成します。 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

**このチュートリアルで学習する内容は次のとおりです。**

> [!div class="checklist"]
> * 新しいアプリの作成 
> * 意図の作成
> * 発話の例を追加する
> * アプリのトレーニング
> * アプリの発行
> * エンドポイントから意図の予測を取得する


[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]

## <a name="user-intentions-as-intents"></a>意図としてのユーザーの意図

アプリの目的は、会話や自然言語テキストの意図を特定することです。 

`I'd like to order a veggie pizza with a salad on the side.`

これらの意図 (intention) は、**意図**(Intent) に分類されます。 

|Intent|目的|
|--|--|
|`ModifyOrder`|ユーザーのピザの注文を決定します。|
|`Greeting`|ボットの会話を開始します。|
|`ConfirmOrder`|ピザの注文を確定します。|
|`None`|アプリによって回答が想定されていないことをユーザーが質問しているかどうかを判定します。 この意図はアプリの作成の一部として指定され (指定した場合)、削除することはできません。 |

## <a name="create-a-new-app"></a>新しいアプリの作成

[!INCLUDE [Follow these steps to create a new LUIS app](includes/create-pizza-app.md)]

## <a name="create-a-new-intent"></a>新しい意図を作成する 

1. ポータルで、アプリの **[ビルド]** セクション内にある **[+ 作成]** を選択します。 新しい意図の名前として「`OrderPizza`」を入力し、 **[完了]** を選択します。

    `OrderPizza` 意図は、ユーザーがピザの注文を希望したときに予測されます。 

1. この意図に、ユーザーの質問として予想される発話例をいくつか追加します。

    |`OrderPizza` の発話例|
    |--|
    |`can i get a pepperoni pizza and a can of coke please`|
    |`can i get a small pizza with onions peppers and olives`|
    |`delivery for a small pepperoni pizza`|
    |`pickup a cheddar cheese pizza large with extra anchovies`|
    |`i need 2 large cheese pizzas 6 large pepperoni pizzas and 1 large supreme pizza`|

    ![発話の例を追加する](media/tutorial-intents-only/add-example-utterances-for-pizza-order.png)

    "_発話例_" を提供することで、この意図についてはどのような種類の発話を予測する必要があるか、LUIS のトレーニングを行っています。 

    [!INCLUDE [Do not use too few utterances](includes/do-not-use-too-few-utterances.md)]    

## <a name="create-remaining-intents"></a>残りの意図を作成する

1. `Greeting` 意図を作成して、以下の発話例を追加します。 これは、ユーザーが新しくピザの注文の会話を開始しているかどうかを判断する意図です。

    |`Greeting` 発話例|
    |--|
    |`Hi`|
    |`Hello`|
    |`Hey`|
    |`Start`|
    |`Begin`|

1. `Confirm` 意図を作成して、以下の発話例を追加します。 これは、ユーザーが注文を完了して、注文の詳細を受け入れるかを判定するための意図です。 

    |`Confirm` 発話例|
    |--|
    |`Go ahead`|
    |`ok`|
    |`Yes`|
    |`Sure`|


## <a name="none-intent-example-utterances"></a>None 意図の発話の例

[!INCLUDE [Follow these steps to add the None intent to the app](includes/add-example-utterances-none-intent.md)]

## <a name="train-the-app"></a>アプリをトレーニングする 

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app"></a>アプリの発行 

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)] 

## <a name="get-intent-prediction"></a>意図の予測を取得する

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. アドレス バー上の URL の末尾に移動して、以下を入力します。

    `get a medium vegetarian pizza for delivery` 

    これは、発話例とまったく同じではないため、この意図によって何を予測するべきかを LUIS が学習できるかどうかを判断するための有用なテストになります。

    最後のクエリ文字列パラメーターは `query` です。これは発話の**クエリ**です。 この発話は、どの発話例とも同じではありません。 これは適切なテストであり、最上位のスコアリング意図として `OrderPizza` 意図を返すはずです。 

    ```JSON
    {
        "query": "get a medium vegetarian pizza for delivery",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.6488959
                },
                "None": {
                    "score": 0.139966831
                },
                "Confirm": {
                    "score": 0.00736504374
                },
                "Greeting": {
                    "score": 0.003970454
                }
            },
            "entities": {}
        }
    }
    ```

    現在このアプリにはまったくエンティティ (抽出する発話内のデータ単位) がないため、エンティティの配列は空になっています。 

    JSON の結果では、最上位のスコアリング意図が **`prediction.topIntent`** プロパティとして識別されています。 すべてのスコアは 0 から 1 の範囲であり、1 に近いほどよいスコアです。 

1. **Greeting** 意図を対象にするように、URL の **query** パラメーターを変更します。

    `Howdy`

    これは、発話例とまったく同じではないため、この意図によって何を予測するべきかを LUIS が学習できるかどうかを判断するための有用なテストになります。 

    ```json
    {
        "query": "howdy",
        "prediction": {
            "topIntent": "Greeting",
            "intents": {
                "Greeting": {
                    "score": 0.446016937
                },
                "Confirm": {
                    "score": 0.2390079
                },
                "None": {
                    "score": 0.09119555
                },
                "OrderPizza": {
                    "score": 0.00109590159
                }
            },
            "entities": {}
        }
    }    
    ```
 
    この予測には、44% の信頼度スコアが付けられています。 信頼度スコアを上げるには、15 から 30 個の発話例を追加します。  

## <a name="client-application-next-steps"></a>クライアント アプリケーションの次の手順

LUIS は、JSON 応答を返した後は、この要求の処理を終えています。 LUIS は、ユーザーの発話に対する回答は提供しません。自然言語で、どのような種類の情報が質問されているかを識別するだけです。 会話のフォローアップは、Azure ボットなどのクライアント アプリケーションによって提供されます。 


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>関連情報

* [エンティティの種類](luis-concept-entity-types.md)
* [トレーニング方法](luis-how-to-train.md)
* [発行方法](luis-how-to-publish-app.md)
* [How to test in LUIS portal (LUIS ポータルでのテスト方法)](luis-interactive-test.md)
* [Azure Bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)


## <a name="next-steps"></a>次の手順

このチュートリアルでは、LUIS アプリを作成し、意図を作成して、各意図に発話例を追加しました。また、None 意図への発話例の追加、トレーニング、発行、およびエンドポイントでのテストを行いました。 これらが、LUIS モデルを構築する基本的な手順です。 

> [!div class="nextstepaction"]
> [このアプリに分解可能なエンティティを追加する](tutorial-machine-learned-entity.md)
