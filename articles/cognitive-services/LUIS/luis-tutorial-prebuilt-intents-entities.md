---
title: 事前構築済みの意図とエンティティを追加する
titleSuffix: Azure Cognitive Services
description: Human Resources チュートリアル アプリに事前構築済みの意図とエンティティを追加して、意図の予測の取得とデータの抽出を迅速化します。 発話に事前構築済みエンティティのラベルを付ける必要はありません。 このエンティティは自動的に検出されます。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: b6fb603b84cdcf3cb0f75d0020fa2047a0a838d1
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53074078"
---
# <a name="tutorial-2-identify-common-intents-and-entities"></a>チュートリアル 2:一般的な意図とエンティティを識別する
このチュートリアルでは、Human Resources アプリに変更を加えます。 Human Resources チュートリアル アプリに事前構築済みの意図とエンティティを追加して、意図の予測の取得とデータの抽出を迅速化します。 エンティティは自動的に検出されるため、事前構築済みのエンティティがある発話にはラベルを付ける必要がありません。

一般的な主題領域やデータ型の事前構築済みモデルは、モデルをすばやく構築するのに役立ち、モデルがどのように機能するかの例を提供します。 

**このチュートリアルで学習する内容は次のとおりです。**

> [!div class="checklist"]
> * 既存のチュートリアル アプリを使用する
> * 事前構築済み意図を追加する 
> * 事前構築済みエンティティを追加する 
> * トレーニング 
> * 発行 
> * エンドポイントから意図とエンティティを取得する

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>既存のアプリを使用する
最後のチュートリアルで作成した、**HumanResources** という名前のアプリを引き続き使用します。 

以前のチュートリアルの HumanResources アプリがない場合は、次の手順を使用します。

1.  [アプリの JSON ファイル](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-intent-only-HumanResources.json)をダウンロードして保存します。

2. JSON を新しいアプリにインポートします。

3. **[管理]** セクションの **[バージョン]** タブで、バージョンを複製し、それに `prebuilts` という名前を付けます。 複製は、元のバージョンに影響を及ぼさずに LUIS のさまざまな機能を使用するための優れた方法です。 バージョン名は URL ルートの一部として使用されるため、URL 内で有効ではない文字を名前に含めることはできません。 

## <a name="add-prebuilt-intents"></a>事前構築済み意図を追加する
LUIS には、ユーザーの一般的な意図を使用する際に役立つ事前構築済み意図が用意されています。  

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. **[Add prebuilt intent]\(事前構築済み意図の追加\)** を選択します。 

3. `Utilities` を検索します。 

    [ ![検索ボックスに「Utilities」と入力された事前構築済み意図ダイアログのスクリーンショット](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png)](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png#lightbox)

4. 次の意図を選択し、**[完了]** をクリックします。 

    * Utilities.Cancel
    * Utilities.Confirm
    * Utilities.Help
    * Utilities.StartOver
    * Utilities.Stop


## <a name="add-prebuilt-entities"></a>事前構築済みエンティティを追加する
LUIS には、一般的なデータ抽出のための事前構築済みエンティティが用意されています。 

1. 左側のナビゲーション メニューの **[Entities]\(エンティティ\)** を選択します。

2. **[Manage prebuilt entity]\(事前構築済みエンティティの管理\)** ボタンを選択します。

3. 事前構築済みエンティティの一覧で **[number]** と **[datetimeV2]** を選択し、**[完了]** をクリックします。

    ![[number] が選択されている事前構築済みエンティティ ダイアログのスクリーンショット](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

## <a name="train"></a>トレーニング

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>発行

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>エンドポイントから意図とエンティティを取得する

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. ブラウザーのアドレス バーで URL の末尾に移動して「`I want to cancel on March 3`」と入力します。 最後のクエリ文字列パラメーターは `q` です。これは発話の**クエリ**です。 

    ```json
    {
      "query": "I want to cancel on March 3",
      "topScoringIntent": {
        "intent": "Utilities.Cancel",
        "score": 0.7818295
      },
      "intents": [
        {
          "intent": "Utilities.Cancel",
          "score": 0.7818295
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0237864349
        },
        {
          "intent": "GetJobInformation",
          "score": 0.017576348
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.0130122062
        },
        {
          "intent": "Utilities.Help",
          "score": 0.006731322
        },
        {
          "intent": "None",
          "score": 0.00524190161
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.004912514
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00092950504
        }
      ],
      "entities": [
        {
          "entity": "march 3",
          "type": "builtin.datetimeV2.date",
          "startIndex": 20,
          "endIndex": 26,
          "resolution": {
            "values": [
              {
                "timex": "XXXX-03-03",
                "type": "date",
                "value": "2018-03-03"
              },
              {
                "timex": "XXXX-03-03",
                "type": "date",
                "value": "2019-03-03"
              }
            ]
          }
        },
        {
          "entity": "3",
          "type": "builtin.number",
          "startIndex": 26,
          "endIndex": 26,
          "resolution": {
            "value": "3"
          }
        }
      ]
    }
    ```

    結果では、意図 Utilities.Cancel が予測され、3 月 3 日の日付と数値 3 が抽出されています。 

    3 月 3 日の値が 2 つあるのは、3 月 3 日が過去か未来かが、発話の中で言及されなかったからです。 必要に応じて仮説を立てるか明確化を求めるかは、クライアント アプリケーションの判断に委ねられます。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>次の手順

事前構築済みの意図とエンティティを追加することにより、クライアント アプリケーションで一般的なユーザーの意図を判断したり、一般的なデータ型を抽出したりできます。 

> [!div class="nextstepaction"]
> [正規表現エンティティを追加する](luis-quickstart-intents-regex-entity.md)

