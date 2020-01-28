---
title: クイック スタート:ブラウザーで意図を取得する - LUIS
titleSuffix: Azure Cognitive Services
description: このクイックスタートでは、利用可能なパブリック LUIS アプリを使用して、ブラウザーで会話形式のテキストからユーザーの意図を判断します。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 10/17/2019
ms.author: diberry
ms.openlocfilehash: 2ca0f9faf1623df9212072abbc960cba41a6414b
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/17/2020
ms.locfileid: "76165740"
---
# <a name="quickstart-get-intent-with-a-browser"></a>クイック スタート:ブラウザーで意図を取得する

LUIS の予測エンドポイントから返される内容を理解するために、予測結果を Web ブラウザーで表示します。 

## <a name="prerequisites"></a>前提条件

パブリック アプリのクエリには、以下が必要です。

* ご自身の Language Understanding (LUIS) 作成キーまたは予測キー。これは、[LUIS ポータル (プレビュー)](https://preview.luis.ai/) から取得できます。 まだキーを作成するサブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)に登録できます。 
* パブリック アプリの ID: `df67dcdb-c37d-46af-88e1-8b97951ca1c2`。 

## <a name="use-the-browser-to-see-predictions"></a>ブラウザーを使用して予測を表示する

1. Web ブラウザーを開きます。 
1. 以下の完全な URL を使用して、`YOUR-KEY` を独自の LUIS 作成キーまたは予測キーで置き換えます。 要求は GET 要求であり、クエリ文字列パラメーターとして LUIS 作成キーまたは予測キーキーを使用した承認が含まれます。

    #### <a name="v3-prediction-requesttabv3-1-1"></a>[V3 予測要求](#tab/V3-1-1)
    
    
    **GET** エンドポイント (スロットによる) 要求に使う V3 URL の形式は、次のとおりです。
    
    `
    https://westus.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-KEY
    `

    #### <a name="v2-prediction-requesttabv2-1-2"></a>[V2 予測要求](#tab/V2-1-2)
    
    **GET** エンドポイント要求に使う V2 URL の形式は、次のとおりです。
    
    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=YOUR-KEY&q=turn on all lights
    `

1. この URL をブラウザー ウィンドウに貼り付け、Enter キーを押します。 `HomeAutomation.TurnOn` の意図が最上位の意図であることと、値が `on` の `HomeAutomation.Operation` エンティティが LUIS で検出されたことを示す JSON 結果がブラウザーに表示されます。

    #### <a name="v3-prediction-responsetabv3-2-1"></a>[V3 予測応答](#tab/V3-2-1)

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    #### <a name="v2-prediction-responsetabv2-2-2"></a>[V2 予測応答](#tab/V2-2-2)

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```

    * * *

1. すべての意図を表示するには、適切なクエリ文字列パラメーターを追加します。 

    #### <a name="v3-prediction-endpointtabv3-3-1"></a>[V3 予測エンドポイント](#tab/V3-3-1)

    **すべての意図を表示する**には、クエリ文字列の末尾に `show-all-intents=true` を追加します。

    `
    https://westus.api.cognitive.microsoft.com/luis/predict/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-KEY&show-all-intents=true
    `

    ```JSON
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "None": {
                    "score": 0.08687421
                },
                "HomeAutomation.TurnOff": {
                    "score": 0.0207554
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    #### <a name="v2-prediction-endpointtabv2"></a>[V2 予測エンドポイント](#tab/V2)

    **すべての意図を表示する**には、クエリ文字列の末尾に `verbose=true` を追加します。

    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?q=turn on all lights&subscription-key={your-key}&verbose=true
    `

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "intents": [
            {
                "intent": "HomeAutomation.TurnOn",
                "score": 0.5375382
            },
            {
                "intent": "None",
                "score": 0.08687421
            },
            {
                "intent": "HomeAutomation.TurnOff",
                "score": 0.0207554
            }
        ],
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```


<!-- FIX - is the public app getting updated for the new prebuilt domain with entities? -->   

## <a name="next-steps"></a>次のステップ

[V3 予測エンドポイント](luis-migration-api-v3.md)の詳細について学習します。

> [!div class="nextstepaction"]
> [LUIS ポータル内でアプリを作成する](get-started-portal-build-app.md)
