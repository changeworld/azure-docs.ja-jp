---
title: REST API インクルード ファイル
description: インクルード ファイル
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 11/30/2020
ms.topic: include
ms.openlocfilehash: ac5d4ce3080bad36b0f68289bee32910bdc58837
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/09/2020
ms.locfileid: "96941522"
---
このクイック スタートでは、3 つの REST 呼び出しを順番に実行します。

- まず、[REST のラベル バッチ追加](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c09)呼び出しを使用して、Pizza アプリ モデルのトレーニングに使用される発話の例のバッチをアップロードします。
- 次に、[REST のアプリケーション バージョン トレーニング](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c45)呼び出しを使用して、Pizza アプリのトレーニング セッションを開始します。
- 最後に、[REST のバージョン トレーニング状態取得](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c46)呼び出しを使用して、Pizza アプリのトレーニング セッションの状態を取得します。

[リファレンス ドキュメント](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c45)

## <a name="prerequisites"></a>前提条件

* 無料の [LUIS](https://www.luis.ai) アカウント。
* [Visual Studio Code](https://code.visualstudio.com/) などのテキスト エディター。
* コマンド ライン プログラム cURL。 cURL プログラムは、macOS、ほとんどの Linux ディストリビューション、Windows 10 ビルド 1803 以降に既にインストールされています。

    cURL をインストールする必要がある場合は、[cURL ダウンロード ページ](https://curl.haxx.se/download.html)から cURL をダウンロードできます。

## <a name="create-pizza-app"></a>Pizza アプリを作成する

[!INCLUDE [Create pizza app](get-started-get-model-create-pizza-app.md)]


## <a name="create-a-json-file-to-train-the-pizza-app"></a>Pizza アプリをトレーニングするための JSON ファイルを作成する

発話の 3 つの例を含む JSON ファイルを作成するには、次の JSON データを `ExampleUtterances.JSON` という名前のファイルに保存します。

```JSON
[
  {
    "text": "order a pizza",
    "intentName": "ModifyOrder",
    "entityLabels": [
      {
        "entityName": "Order",
        "startCharIndex": 6,
        "endCharIndex": 12
      }
    ]
  },
  {
    "text": "order a large pepperoni pizza",
    "intentName": "ModifyOrder",
    "entityLabels": [
      {
        "entityName": "Order",
        "startCharIndex": 6,
        "endCharIndex": 28
      },
      {
        "entityName": "FullPizzaWithModifiers",
        "startCharIndex": 6,
        "endCharIndex": 28
      },
      {
        "entityName": "PizzaType",
        "startCharIndex": 14,
        "endCharIndex": 28
      },
      {
        "entityName": "Size",
        "startCharIndex": 8,
        "endCharIndex": 12
      }
    ]
  },
  {
    "text": "I want two large pepperoni pizzas on thin crust",
    "intentName": "ModifyOrder",
    "entityLabels": [
      {
        "entityName": "Order",
        "startCharIndex": 7,
        "endCharIndex": 46
      },
      {
        "entityName": "FullPizzaWithModifiers",
        "startCharIndex": 7,
        "endCharIndex": 46
      },
      {
        "entityName": "PizzaType",
        "startCharIndex": 17,
        "endCharIndex": 32
      },
      {
        "entityName": "Size",
        "startCharIndex": 11,
        "endCharIndex": 15
      },
      {
        "entityName": "Quantity",
        "startCharIndex": 7,
        "endCharIndex": 9
      },
      {
        "entityName": "Crust",
        "startCharIndex": 37,
        "endCharIndex": 46
      }
    ]
  }
]`
```

発話の例の JSON は、特定の形式に従っています。

`text` フィールドには、サンプルの発話テキストが含まれています。 `intentName` フィールドは、LUIS アプリの既存の意図の名前と対応している必要があります。 `entityLabels` フィールドは必須です。 どのエンティティもラベル付けしない場合は、空の配列を指定します。

entityLabels 配列が空でない場合、`entityName` フィールドで示されるエンティティを `startCharIndex` および `endCharIndex` で指定する必要があります。 インデックスは 0 から始まります。 テキスト内のスペースでラベルを開始または終了すると、発話を追加する API 呼び出しは失敗します。

## <a name="add-example-utterances"></a>発話の例を追加する

1. 発話の例のバッチをアップロードするには、次のコマンドをテキスト エディターにコピーします。

    #### <a name="windows"></a>[Windows](#tab/windows)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT**_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/examples?verbose=true&show-all-intents=true" ^
          --request POST ^
          --header "Content-Type:application/json" ^
          --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_" ^
          --data "@ExampleUtterances.JSON"
    ```

    #### <a name="macos"></a>[macOS](#tab/unix)

    ```cURL
    curl "_*_YOUR-AUTHORING-ENDPOINT_*_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/examples?verbose=true&show-all-intents=true" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_" \
          --data "@ExampleUtterances.JSON"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "_*_YOUR-AUTHORING-ENDPOINT_*_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/examples?verbose=true&show-all-intents=true" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_" \
          --data "@ExampleUtterances.JSON"
    ```

    _*_

1. `_*_YOUR-` で始まる値を実際の値に置き換えます。

    |Information|目的|
    |--|--|
    |`_*_YOUR-AUTHORING-ENDPOINT_*_`| 作成 URL エンドポイント。 (例: "https://REPLACE-WITH-YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/")。 リソース名は、リソースの作成時に設定します。|
    |`_*_YOUR-APP-ID_*_`| LUIS アプリ ID。 |
    |`_*_YOUR-APP-VERSION_*_`| LUIS アプリのバージョン。 Pizza アプリの場合、バージョン番号は "0.1" です (引用符は除く)。|
    |`_*_YOUR-AUTHORING-KEY_*_`|32 文字の実際の作成キー。|

    割り当てられたキーとリソースは、LUIS ポータルの [Manage]\(管理\) セクションの *[Azure リソース]* * ページで確認できます。 アプリ ID は、同じ [Manage]\(管理\) セクションの **[アプリケーションの設定]** ページで入手できます。

1. コマンド プロンプト (Windows) またはターミナル (macOS と Linux) を起動し、`ExampleUtterances.JSON` ファイルを保存したディレクトリに移動します。

1. エディターから cURL コマンドをコピーし、コマンド プロンプト (Windows) またはターミナル (macOS と Linux) に貼り付けます。 Enter キーを押してコマンドを実行します。

    次のような応答が表示されます。

    ```json
    [{"value":{"ExampleId":1255129706,"UtteranceText":"order a pizza"},"hasError":false},{"value":{"ExampleId":1255129707,"UtteranceText":"order a large pepperoni pizza"},"hasError":false},{"value":{"ExampleId":1255129708,"UtteranceText":"i want two large pepperoni pizzas on thin crust"},"hasError":false}]
    ```

    読みやすいように書式設定された出力を次に示します。

    ```json
    [
      {
        "value": {
          "ExampleId": 1255129706,
          "UtteranceText": "order a pizza"
        },
        "hasError": false
      },
      {
        "value": {
          "ExampleId": 1255129707,
          "UtteranceText": "order a large pepperoni pizza"
        },
        "hasError": false
      },
      {
        "value": {
          "ExampleId": 1255129708,
          "UtteranceText": "i want two large pepperoni pizzas on thin crust"
        },
        "hasError": false
      }
    ]
    ```

## <a name="train-the-pizza-app-model"></a>Pizza アプリ モデルをトレーニングする

1. Pizza アプリのトレーニング セッションを開始するには、次のコマンドをテキスト エディターにコピーします。

    #### <a name="windows"></a>[Windows](#tab/windows)

    ```cURL
    curl "**_YOUR-AUTHORING-ENDPOINT_*_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/train?verbose=true&show-all-intents=true" ^
          --data "" ^
          --request POST ^
          --header "Content-Type:application/json" ^
          --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_"
    ```

    #### <a name="macos"></a>[macOS](#tab/unix)

    ```cURL
    curl "_*_YOUR-AUTHORING-ENDPOINT_*_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/train?verbose=true&show-all-intents=true" \
          --data "" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "_*_YOUR-AUTHORING-ENDPOINT_*_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/train?verbose=true&show-all-intents=true" \
          --data "" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_"
    ```

    _*_

1. 前の手順と同様に、`_*_YOUR-` で始まる値を実際の値に置き換えます。

1. エディターから cURL コマンドをコピーし、コマンド プロンプト (Windows) またはターミナル (macOS と Linux) に貼り付けます。 Enter キーを押してコマンドを実行します。

    次のような応答が表示されます。

    ```json
    {"statusId":2,"status":"UpToDate"}
    ```

    読みやすいように書式設定された出力を次に示します。

    ```json
    {
      "statusId": 2,
      "status": "UpToDate"
    }
    ```

## <a name="get-the-status-of-training"></a>トレーニングの状態を取得する

1. トレーニング セッションのトレーニング状態を取得するには、次のコマンドをテキスト エディターにコピーします。

    #### <a name="windows"></a>[Windows](#tab/windows)

    ```cURL
    curl "_*_YOUR-AUTHORING-ENDPOINT_*_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/train?verbose=true&show-all-intents=true" ^
            --request GET ^
            --header "Content-Type:application/json" ^
            --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_"
    ```

    #### <a name="macos"></a>[macOS](#tab/unix)

    ```cURL
    curl "_*_YOUR-AUTHORING-ENDPOINT_*_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/train?verbose=true&show-all-intents=true" \
            --request GET \
            --header "Content-Type:application/json" \
            --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "_*_YOUR-AUTHORING-ENDPOINT_*_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/train?verbose=true&show-all-intents=true" \
            --request GET \
            --header "Content-Type:application/json" \
            --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_"
    ```

    _*_

1. 前の手順と同様に、`_*_YOUR-` で始まる値を実際の値に置き換えます。

1. エディターから cURL コマンドをコピーし、コマンド プロンプト (Windows) またはターミナル (macOS と Linux) に貼り付けます。 Enter キーを押してコマンドを実行します。

    次のような応答が表示されます。

    ```json
    [{"modelId":"8eb7ad8f-5db5-4c28-819b-ca3905fffd80","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"6f53bc92-ae54-44ce-bc4e-010d1f8cfda0","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"6cb17888-ad6e-464c-82c0-d37fd1f2c4f8","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"a16fc4fd-1949-4e77-9de3-30369f16c3a5","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"6bacdb75-1889-4f93-8971-8c8995ff8960","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"be963f4c-4898-48d7-9eba-3c6af0867b9d","details":{"statusId":2,"status":"UpToDate","exampleCount":171}}]
    ```

    読みやすいように書式設定された出力を次に示します。

    ```json
    [
      {
        "modelId": "8eb7ad8f-5db5-4c28-819b-ca3905fffd80",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "6f53bc92-ae54-44ce-bc4e-010d1f8cfda0",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "6cb17888-ad6e-464c-82c0-d37fd1f2c4f8",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "a16fc4fd-1949-4e77-9de3-30369f16c3a5",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "6bacdb75-1889-4f93-8971-8c8995ff8960",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "be963f4c-4898-48d7-9eba-3c6af0867b9d",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      }
    ]
    ```


## <a name="get-intent-from-the-prediction-endpoint"></a>予測エンドポイントから意図を取得する

cURL を使用して、[予測エンドポイント](https://aka.ms/luis-apim-v3-prediction)のクエリを実行し、予測結果を取得します。

> [!NOTE]
> このコマンドは予測エンドポイントを使用します。

1. 次のコマンドをテキスト エディターにコピーします。

    #### <a name="windows"></a>[Windows](#tab/windows)
    ```cURL
    curl "https://_*_YOUR-PREDICTION-ENDPOINT_*_/luis/prediction/v3.0/apps/_*_YOUR-APP-ID_*_/slots/production/predict" ^
          --request GET ^
          --get ^
          --data "subscription-key=_*_YOUR-PREDICTION-KEY_*_" ^
          --data "verbose=true" ^
          --data "show-all-intents=true" ^
          --data-urlencode "query=I want two large pepperoni pizzas on thin crust please"
    ```

    #### <a name="macos"></a>[macOS](#tab/unix)

    ```cURL
    curl "https://_*_YOUR-PREDICTION-ENDPOINT_*_/luis/prediction/v3.0/apps/_*_YOUR-APP-ID_*_/slots/production/predict" \
          --request GET \
          --get \
          --data "subscription-key=_*_YOUR-PREDICTION-KEY_*_" \
          --data "verbose=true" \
          --data "show-all-intents=true" \
          --data-urlencode "query=I want two large pepperoni pizzas on thin crust please"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "https://_*_YOUR-PREDICTION-ENDPOINT_*_/luis/prediction/v3.0/apps/_*_YOUR-APP-ID_*_/slots/production/predict" \
          --request GET \
          --get \
          --data "subscription-key=_*_YOUR-PREDICTION-KEY_*_" \
          --data "verbose=true" \
          --data "show-all-intents=true" \
          --data-urlencode "query=I want two large pepperoni pizzas on thin crust please"
    ```

    _*_

1. `_*_YOUR-` で始まる値を実際の値に置き換えます。

    |Information|目的|
    |--|--|
    |`_*_YOUR-PREDICTION-ENDPOINT_*_`| 予測 URL エンドポイント。 LUIS ポータルのアプリの [Azure リソース] ページにあります。<br>たとえば、「 `https://westus.api.cognitive.microsoft.com/` 」のように入力します。|
    |`_*_YOUR-APP-ID_*_`|アプリ ID。 LUIS ポータルのアプリの [アプリケーションの設定] ページにあります。
    |`_*_YOUR-PREDICTION-KEY_**`|32 文字の予測キー。 LUIS ポータルのアプリの [Azure リソース] ページにあります。

1. テキストをコンソール ウィンドウにコピーし、Enter キーを押してコマンドを実行します。

1. JSON として返される予測応答を確認します。

    ```console
    {"query":"I want two large pepperoni pizzas on thin crust please","prediction":{"topIntent":"ModifyOrder","intents":{"ModifyOrder":{"score":1.0},"None":{"score":8.55E-09},"Greetings":{"score":1.82222226E-09},"CancelOrder":{"score":1.47272727E-09},"Confirmation":{"score":9.8125E-10}},"entities":{"Order":[{"FullPizzaWithModifiers":[{"PizzaType":["pepperoni pizzas"],"Size":[["Large"]],"Quantity":[2],"Crust":[["Thin"]],"$instance":{"PizzaType":[{"type":"PizzaType","text":"pepperoni pizzas","startIndex":17,"length":16,"score":0.9978157,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"Size":[{"type":"SizeList","text":"large","startIndex":11,"length":5,"score":0.9984481,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"Quantity":[{"type":"builtin.number","text":"two","startIndex":7,"length":3,"score":0.999770939,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"Crust":[{"type":"CrustList","text":"thin crust","startIndex":37,"length":10,"score":0.933985531,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}]}}],"$instance":{"FullPizzaWithModifiers":[{"type":"FullPizzaWithModifiers","text":"two large pepperoni pizzas on thin crust","startIndex":7,"length":40,"score":0.90681237,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}]}}],"ToppingList":[["Pepperoni"]],"$instance":{"Order":[{"type":"Order","text":"two large pepperoni pizzas on thin crust","startIndex":7,"length":40,"score":0.9047088,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"ToppingList":[{"type":"ToppingList","text":"pepperoni","startIndex":17,"length":9,"modelTypeId":5,"modelType":"List Entity Extractor","recognitionSources":["model"]}]}}}}
    ```

    読みやすくするために書式設定された JSON 応答:

    ```JSON
    {
      "query": "I want two large pepperoni pizzas on thin crust please",
      "prediction": {
        "topIntent": "ModifyOrder",
        "intents": {
          "ModifyOrder": {
            "score": 1.0
          },
          "None": {
            "score": 8.55e-9
          },
          "Greetings": {
            "score": 1.82222226e-9
          },
          "CancelOrder": {
            "score": 1.47272727e-9
          },
          "Confirmation": {
            "score": 9.8125e-10
          }
        },
        "entities": {
          "Order": [
            {
              "FullPizzaWithModifiers": [
                {
                  "PizzaType": [
                    "pepperoni pizzas"
                  ],
                  "Size": [
                    [
                      "Large"
                    ]
                  ],
                  "Quantity": [
                    2
                  ],
                  "Crust": [
                    [
                      "Thin"
                    ]
                  ],
                  "$instance": {
                    "PizzaType": [
                      {
                        "type": "PizzaType",
                        "text": "pepperoni pizzas",
                        "startIndex": 17,
                        "length": 16,
                        "score": 0.9978157,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                          "model"
                        ]
                      }
                    ],
                    "Size": [
                      {
                        "type": "SizeList",
                        "text": "large",
                        "startIndex": 11,
                        "length": 5,
                        "score": 0.9984481,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                          "model"
                        ]
                      }
                    ],
                    "Quantity": [
                      {
                        "type": "builtin.number",
                        "text": "two",
                        "startIndex": 7,
                        "length": 3,
                        "score": 0.999770939,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                          "model"
                        ]
                      }
                    ],
                    "Crust": [
                      {
                        "type": "CrustList",
                        "text": "thin crust",
                        "startIndex": 37,
                        "length": 10,
                        "score": 0.933985531,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                          "model"
                        ]
                      }
                    ]
                  }
                }
              ],
              "$instance": {
                "FullPizzaWithModifiers": [
                  {
                    "type": "FullPizzaWithModifiers",
                    "text": "two large pepperoni pizzas on thin crust",
                    "startIndex": 7,
                    "length": 40,
                    "score": 0.90681237,
                    "modelTypeId": 1,
                    "modelType": "Entity Extractor",
                    "recognitionSources": [
                      "model"
                    ]
                  }
                ]
              }
            }
          ],
          "ToppingList": [
            [
              "Pepperoni"
            ]
          ],
          "$instance": {
            "Order": [
              {
                "type": "Order",
                "text": "two large pepperoni pizzas on thin crust",
                "startIndex": 7,
                "length": 40,
                "score": 0.9047088,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                  "model"
                ]
              }
            ],
            "ToppingList": [
              {
                "type": "ToppingList",
                "text": "pepperoni",
                "startIndex": 17,
                "length": 9,
                "modelTypeId": 5,
                "modelType": "List Entity Extractor",
                "recognitionSources": [
                  "model"
                ]
              }
            ]
          }
        }
      }
    }
    ```