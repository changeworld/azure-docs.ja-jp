---
title: Java で REST 呼び出しを使用して意図を取得する
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: 4e2fb81b19694136896b1dee07c3bd74c63fc01b
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/22/2019
ms.locfileid: "74414579"
---
## <a name="prerequisites"></a>前提条件

* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit Standard Edition)
* [Visual Studio Code](https://code.visualstudio.com/) または任意の IDE。
* パブリック アプリ ID: `df67dcdb-c37d-46af-88e1-8b97951ca1c2`

## <a name="get-luis-key"></a>LUIS キーを取得する

[!INCLUDE [Use authoring key for endpoint](../includes/get-key-quickstart.md)]

## <a name="get-intent-programmatically"></a>プログラムで意図を取得する

Java を使用して、[予測エンドポイント](https://aka.ms/luis-apim-v3-prediction)のクエリを実行し、予測結果を取得します。

1. `lib` という名前のサブディレクトリを作成し、次の Java ライブラリにコピーします。

    * [commons-logging-1.2.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/commons-logging-1.2.jar)
    * [httpclient-4.5.3.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/httpclient-4.5.3.jar)
    * [httpcore-4.4.6.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/httpcore-4.4.6.jar)

1. `Predict.java` という名前のファイルに次のコードをコピーしてクラスを作成します。

    ```java
    import java.io.*;
    import java.net.URI;
    import org.apache.http.HttpEntity;
    import org.apache.http.HttpResponse;
    import org.apache.http.client.HttpClient;
    import org.apache.http.client.methods.HttpGet;
    import org.apache.http.client.utils.URIBuilder;
    import org.apache.http.impl.client.HttpClients;
    import org.apache.http.util.EntityUtils;
    
    public class Predict {
    
        public static void main(String[] args) 
        {
            HttpClient httpclient = HttpClients.createDefault();
    
            try
            {
    
                // The ID of a public sample LUIS app that recognizes intents for turning on and off lights
                String AppId = "df67dcdb-c37d-46af-88e1-8b97951ca1c2";
                
                // Add your endpoint key 
                String Key = "YOUR-KEY";
    
                // Add your endpoint, example is westus.api.cognitive.microsoft.com
                String Endpoint = "YOUR-ENDPOINT";
    
                String Utterance = "turn on all lights";
    
                // Begin endpoint URL string building
                URIBuilder endpointURLbuilder = new URIBuilder("https://" + Endpoint + "/luis/prediction/v3.0/apps/" + AppId + "/slots/production/predict?");
    
                // query string params
                endpointURLbuilder.setParameter("query", Utterance);
                endpointURLbuilder.setParameter("subscription-key", Key);
                endpointURLbuilder.setParameter("show-all-intents", "true");
                endpointURLbuilder.setParameter("verbose", "true");
    
                // create URL from string
                URI endpointURL = endpointURLbuilder.build();
    
                // create HTTP object from URL
                HttpGet request = new HttpGet(endpointURL);
    
                // access LUIS endpoint - analyze text
                HttpResponse response = httpclient.execute(request);
    
                // get response
                HttpEntity entity = response.getEntity();
    
    
                if (entity != null) 
                {
                    System.out.println(EntityUtils.toString(entity));
                }
            }
    
            catch (Exception e)
            {
                System.out.println(e.getMessage());
            }
        }   
    }    
    ```

1. 次の値を置き換えます。

    * `YOUR-KEY` (スターター キーを使用)
    * `YOUR-ENDPOINT` を、ご利用のエンドポイントに。 たとえば、「 `westus2.api.cognitive.microsoft.com` 」のように入力します。


1. コマンド ラインから Java プログラムをコンパイルします。 

    ```console
    javac -cp ":lib/*" Predict.java
    ```

1. コマンド ラインから Java プログラムを実行します。

    ```console
    java -cp ":lib/*" Predict
    ```

1. JSON として返される予測応答を確認します。

    ```console
    {'query': 'turn on all lights', 'prediction': {'topIntent': 'HomeAutomation.TurnOn', 'intents': {'HomeAutomation.TurnOn': {'score': 0.5375382}, 'None': {'score': 0.08687421}, 'HomeAutomation.TurnOff': {'score': 0.0207554}}, 'entities': {'HomeAutomation.Operation': ['on'], '$instance': {'HomeAutomation.Operation': [{'type': 'HomeAutomation.Operation', 'text': 'on', 'startIndex': 5, 'length': 2, 'score': 0.724984169, 'modelTypeId': -1, 'modelType': 'Unknown', 'recognitionSources': ['model']}]}}}}
    ```

    読みやすくするために書式設定された JSON 応答: 

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

## <a name="luis-keys"></a>LUIS キー

[!INCLUDE [Use authoring key for endpoint](../includes/starter-key-explanation.md)]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このクイックスタートを使用して完了したときに、ファイル システムからファイルを削除します。 

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Java を使った発話の追加とトレーニング](../get-started-get-model-rest-apis.md)