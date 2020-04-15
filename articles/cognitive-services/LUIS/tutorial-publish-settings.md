---
title: チュートリアル:発行設定 - LUIS
description: このチュートリアルでは、予測の改善を目的として発行設定に変更を加えます。
ms.topic: tutorial
ms.date: 04/01/2020
ms.openlocfilehash: 19913d16ecb1457ad4edb93ea34e4b96a590aca0
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545770"
---
# <a name="tutorial--add-sentiment-analysis-as-a-publishing-setting"></a>チュートリアル:感情分析を発行設定として追加する

このチュートリアルでは、感情分析を抽出し、LUIS エンドポイントに対するクエリを通じて、返されたユーザー発話のセンチメントを確認するための変更を発行設定に加えます。

**このチュートリアルで学習する内容は次のとおりです。**

<!-- green checkmark -->
> [!div class="checklist"]
> * 感情分析を発行設定として追加する
> * 発行されたエンドポイントから発話の感情を取得する

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="sentiment-analysis-is-a-publish-setting"></a>感情分析は発行設定

以下の発話は、感情の例を示しています。

|センチメント|Score|発話|
|:--|:--|:--|
|ネガティブ|0.01 |The pizza was awful.|
|ポジティブ|0.97 |The cheese pizza was wonderful.|

感情分析は、どの発話にも適用される発行設定です。 設定後は、データのラベル付けを行わなくても発話のセンチメントがアプリから返されます。

それは発行設定であるため、ラベル付けされていることを意図またはエンティティのページで確認することはできません。 [対話型テスト](luis-interactive-test.md#view-sentiment-results)のウィンドウで、またはエンドポイント URL のテスト時に確認できます。

## <a name="import-example-json-to-begin-app"></a>サンプル .json をインポートしてアプリを開始する

1.  [アプリの JSON ファイル](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json)をダウンロードして保存します。

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="train-the-app"></a>アプリをトレーニングする

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>感情分析を含むアプリを構成する

1. 上部のメニューから **[Publish]\(公開\)** を選択します。 感情分析は発行設定です。

1. **[運用スロット]** を選択し、 **[設定の変更]** を選択します。
1. 感情分析設定を **[オン]** にします。

    ![感情分析を発行設定として有効にする](./media/luis-quickstart-intent-and-sentiment-analysis/select-sentiment-publishing-setting.png)

## <a name="get-the-sentiment-of-an-utterance-from-the-endpoint"></a>エンドポイントから発話の感情を取得する

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. アドレス バーの URL の末尾に移動して、_YOUR_QUERY_HERE_ を次のように置き換えます。

    `Deliver 2 of the best cheese pizzas ever!!!`

    この発話はラベル付けされたどの発話とも同じではないので、よいテストであり、感情分析が抽出された `OrderPizza` 意図を返す必要があります。

    ```json
    {
        "query": "Deliver 2 of the best cheese pizzas ever!!!",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.4659952
                },
                "None": {
                    "score": 0.16901511
                },
                "Confirm": {
                    "score": 0.00421415
                },
                "Greeting": {
                    "score": 0.00109914376
                }
            },
            "entities": {
                "number": [
                    2
                ],
                "$instance": {
                    "number": [
                        {
                            "type": "builtin.number",
                            "text": "2",
                            "startIndex": 8,
                            "length": 1,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            },
            "sentiment": {
                "label": "positive",
                "score": 0.861665964
            }
        }
    }
    ```

    感情分析はポジティブで、スコアは 86% です。

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>関連情報

* 感情分析は、Cognitive Service の [Text Analytics](../Text-Analytics/index.yml) によって提供されます。 この機能は、Text Analytics の[サポートされる言語](luis-language-support.md#languages-supported)に制限されています。
* [トレーニング方法](luis-how-to-train.md)
* [発行方法](luis-how-to-publish-app.md)
* [LUIS ポータルでのテスト方法](luis-interactive-test.md)


## <a name="next-steps"></a>次のステップ
このチュートリアルでは、感情分析を発行設定として追加し、発話全体から感情値を抽出します。

> [!div class="nextstepaction"]
> [人事アプリでエンドポイントの発話をレビューする](luis-tutorial-review-endpoint-utterances.md)

