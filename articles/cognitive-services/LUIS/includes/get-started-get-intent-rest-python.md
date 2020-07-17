---
title: Python で REST 呼び出しを使用して意図を取得する
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/20/2020
ms.author: diberry
ms.openlocfilehash: 2307ef6ea8940a3b3ddfb8c7539f4f809dc4c52c
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733280"
---
## <a name="prerequisites"></a>前提条件

* [Python 3.6](https://www.python.org/downloads/) 以降。
* [Visual Studio Code](https://code.visualstudio.com/)
* LUIS アプリ ID - パブリック IoT アプリ ID `df67dcdb-c37d-46af-88e1-8b97951ca1c2` を使用します。 クイックスタートのコードで使用されるユーザー クエリは、そのアプリに固有のものです。

## <a name="create-luis-runtime-key-for-predictions"></a>予測の LUIS ランタイム キーを作成する

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. [**Language Understanding** の作成](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)をクリックします
1. **ランタイム** キーに必要な設定をすべて入力します。

    |設定|値|
    |--|--|
    |名前|任意の名前 (2 から 64 文字)|
    |サブスクリプション|適切なサブスクリプションを選択します|
    |場所|近くにある任意の使用可能な場所を選択します|
    |価格レベル|`F0` -最小限の価格レベル|
    |リソース グループ|使用可能なリソース グループを選択します|

1. **[作成]** をクリックして、リソースが作成されるまで待ちます。 作成後、リソース ページに移動します。
1. 構成された `endpoint` と `key` を収集します。

## <a name="get-intent-from-the-prediction-endpoint"></a>予測エンドポイントから意図を取得する

Python を使用して、[予測エンドポイント](https://aka.ms/luis-apim-v3-prediction)のクエリを実行し、予測結果を取得します。

1. このコード スニペットを `predict.py` というファイルにコピーします。

    ```python
    ########### Python 3.6 #############
    import requests

    try:

        key = 'YOUR-KEY' # your Runtime key
        endpoint = 'YOUR-ENDPOINT' # such as 'your-resource-name.api.cognitive.microsoft.com'
        appId = 'df67dcdb-c37d-46af-88e1-8b97951ca1c2'
        utterance = 'turn on all lights'

        headers = {
        }

        params ={
            'query': utterance,
            'timezoneOffset': '0',
            'verbose': 'true',
            'show-all-intents': 'true',
            'spellCheck': 'false',
            'staging': 'false',
            'subscription-key': key
        }

        r = requests.get(f'https://{endpoint}/luis/prediction/v3.0/apps/{appId}/slots/production/predict',headers=headers, params=params)
        print(r.json())

    except Exception as e:
        print(f'{e}')
    ```

1. `YOUR-KEY` と `YOUR-ENDPOINT` の値を、実際の予測の**ランタイム** キーとエンドポイントに置き換えます。

    |Information|目的|
    |--|--|
    |`YOUR-KEY`|32 文字の予測の**ランタイム** キー。|
    |`YOUR-ENDPOINT`| 予測 URL エンドポイント。 たとえば、「 `replace-with-your-resource-name.api.cognitive.microsoft.com` 」のように入力します。|

1. `requests` 依存関係をインストールします。 これは、HTTP 要求を行うために使用されます。

    ```console
    pip install requests
    ```

1. 次のコンソール コマンドを使用してスクリプトを実行します。

    ```console
    python predict.py
    ```

1. JSON として返される予測応答を確認します。

    ```console
    {'query': 'turn on all lights', 'prediction': {'topIntent': 'HomeAutomation.TurnOn', 'intents': {'HomeAutomation.TurnOn': {'score': 0.5375382}, 'None': {'score': 0.08687421}, 'HomeAutomation.TurnOff': {'score': 0.0207554}}, 'entities': {'HomeAutomation.Operation': ['on'], '$instance': {'HomeAutomation.Operation': [{'type': 'HomeAutomation.Operation', 'text': 'on', 'startIndex': 5, 'length': 2, 'score': 0.724984169, 'modelTypeId': -1, 'modelType': 'Unknown', 'recognitionSources': ['model']}]}}}}
    ```

    読みやすくするために書式設定された JSON 応答を次に示します。

    ```JSON
    {
        "query": "turn on all lights",
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
                ],
                "$instance": {
                    "HomeAutomation.Operation": [
                        {
                            "type": "HomeAutomation.Operation",
                            "text": "on",
                            "startIndex": 5,
                            "length": 2,
                            "score": 0.724984169,
                            "modelTypeId": -1,
                            "modelType": "Unknown",
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

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイックスタートを使用して完了したときに、ファイル システムからファイルを削除します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [発話の追加とトレーニング](../get-started-get-model-rest-apis.md)