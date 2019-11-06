---
title: チュートリアル:あらかじめ構築された意図とエンティティ - LUIS
titleSuffix: Azure Cognitive Services
description: このチュートリアルでは、事前構築済みの意図とエンティティをアプリに追加して、意図の予測の取得とデータの抽出を迅速化します。 発話に事前構築済みエンティティのラベルを付ける必要はありません。 このエンティティは自動的に検出されます。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: diberry
ms.openlocfilehash: 0a59d9783eac122f96b1671f2dba5d0d708e1d83
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499393"
---
# <a name="tutorial-identify-common-intents-and-entities"></a>チュートリアル:一般的な意図とエンティティを識別する

このチュートリアルでは、事前構築済みの意図とエンティティを Human Resources チュートリアル アプリに追加して、意図の予測の取得とデータの抽出を迅速化します。 エンティティは自動的に検出されるため、事前構築済みエンティティがある発話にはマークを付ける必要がありません。

事前構築済みモデル (ドメイン、意図、およびエンティティ) を使用すると、モデルをすばやく構築できます。

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**このチュートリアルで学習する内容は次のとおりです。**

> [!div class="checklist"]
> * 新しいアプリを作成する
> * 事前構築済み意図を追加する 
> * 事前構築済みエンティティを追加する 
> * トレーニング 
> * 発行 
> * エンドポイントから意図とエンティティを取得する

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="create-a-new-app"></a>新しいアプリの作成

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]


## <a name="add-prebuilt-intents-to-help-with-common-user-intentions"></a>ユーザーの一般的な意図を使用する際に役立つ事前構築済み意図を追加する

LUIS には、ユーザーの一般的な意図を使用する際に役立つ事前構築済み意図が用意されています。  

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. **[Add prebuilt domain intent]\(ドメインの事前構築済み意図の追加\)** を選択します。 

1. `Utilities` を検索します。 

1. すべての意図を選択し、 **[完了]** を選択します。 これらの意図は、ユーザーがいる場所やユーザーがしてほしいことを会話の中で判定するのに役立ちます。 

## <a name="add-prebuilt-entities-to-help-with-common-data-type-extraction"></a>一般的な種類のデータを抽出するのに役立つ事前構築済みエンティティを追加する

LUIS には、一般的なデータ抽出のための事前構築済みエンティティが用意されています。 

1. 左側のナビゲーション メニューの **[Entities]\(エンティティ\)** を選択します。

1. **[Add prebuilt entity]\(作成済みエンティティの追加\)** ボタンを選択します。

1. 事前構築済みエンティティの一覧から 次のエンティティを選択し、 **[完了]** を選択します。

   * **[GeographyV2](luis-reference-prebuilt-geographyV2.md)**

     このエンティティは、対象のクライアント アプリケーションに場所の認識を追加するのに役立ちます。

## <a name="add-example-utterances-to-the-none-intent"></a>発話の例を None 意図に追加する 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>意図への変更をテストできるようにアプリをトレーニングする 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>トレーニング済みのモデルがエンドポイントからクエリ可能になるようにアプリを発行する

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>エンドポイントから意図およびエンティティ予測を取得する

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. ブラウザーのアドレス バーで URL の末尾に移動して「`I want to cancel my trip to Seattle`」と入力します。 最後のクエリ文字列パラメーターは `q` です。これは発話の**クエリ**です。 

    ```json
    {
      "query": "I want to cancel my trip to Seattle",
      "topScoringIntent": {
        "intent": "Utilities.Cancel",
        "score": 0.1055009
      },
      "intents": [
        {
          "intent": "Utilities.Cancel",
          "score": 0.1055009
        },
        {
          "intent": "Utilities.SelectItem",
          "score": 0.02659072
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.0253379084
        },
        {
          "intent": "Utilities.ReadAloud",
          "score": 0.02528683
        },
        {
          "intent": "Utilities.SelectNone",
          "score": 0.02434013
        },
        {
          "intent": "Utilities.Escalate",
          "score": 0.009161292
        },
        {
          "intent": "Utilities.Help",
          "score": 0.006861785
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.00633448
        },
        {
          "intent": "Utilities.ShowNext",
          "score": 0.0053827134
        },
        {
          "intent": "None",
          "score": 0.002602003
        },
        {
          "intent": "Utilities.ShowPrevious",
          "score": 0.001797354
        },
        {
          "intent": "Utilities.SelectAny",
          "score": 0.000831930141
        },
        {
          "intent": "Utilities.Repeat",
          "score": 0.0006924066
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.000606057351
        },
        {
          "intent": "Utilities.GoBack",
          "score": 0.000276725681
        },
        {
          "intent": "Utilities.FinishTask",
          "score": 0.000267822179
        },
        {
          "intent": "Utilities.Reject",
          "score": 3.21784828E-05
        }
      ],
      "entities": [
        {
          "entity": "seattle",
          "type": "builtin.geographyV2.city",
          "startIndex": 28,
          "endIndex": 34
        }
      ]
    }
    ```

    結果として、Utilities.Cancel 意図が 80% の信頼度で予測され、市区町村のデータが抽出されました。 


## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>関連情報

事前構築済みモデルの詳細については、次をご覧ください。

* 「[Prebuilt domains (事前構築済みドメイン)](luis-reference-prebuilt-domains.md)」: これらは、LUIS アプリの作成を全体的に削減する一般的なドメインです。
* 「Prebuilt intents (事前構築済み意図)」: これは、一般的なドメインの個々の意図です。 ドメイン全体を追加するのではなく、個々に意図を追加できます。
* 「[事前構築済みエンティティ (Prebuilt entities)](luis-prebuilt-entities.md)」: これらは、大部分の LUIS アプリで役立つ一般的な種類のデータです。

LUIS アプリ の使用法の詳細については、以下をご覧ください。

* 「[How to train (トレーニング方法)](luis-how-to-train.md)」
* [発行方法](luis-how-to-publish-app.md)
* [LUIS ポータルでのテスト方法](luis-interactive-test.md)

## <a name="next-steps"></a>次の手順

事前構築済みの意図とエンティティを追加することにより、クライアント アプリケーションで一般的なユーザーの意図を判断したり、一般的なデータ型を抽出したりできます。  

> [!div class="nextstepaction"]
> [正規表現エンティティを追加する](luis-quickstart-intents-regex-entity.md)

