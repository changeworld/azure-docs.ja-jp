---
title: インクルード ファイル
description: インクルード ファイル
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: 05e668ff5b0ec19c5e380cf6bfee4b6e46900b2f
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "77371795"
---
Node.js 用 Language Understanding (LUIS) ランタイム クライアント ライブラリの用途は次のとおりです。

* スロット別の予測
* バージョン別の予測

[リファレンス ドキュメント](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-runtime) | [ランタイム パッケージ (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime) | [サンプル](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/luis_prediction.js)

## <a name="prerequisites"></a>前提条件

* Language Understanding ランタイム リソース:[Azure portal で作成する](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
* [Node.js](https://nodejs.org)

## <a name="setting-up"></a>設定

### <a name="get-your-language-understanding-luis-runtime-key"></a>Language Understanding (LUIS) ランタイム キーを取得する

LUIS ランタイム リソースを作成して、[ランタイム キー](../luis-how-to-azure-subscription.md)を取得します。 次の手順のために、キーとキーのエンドポイントを保持しておきます。

[!INCLUDE [Set up environment variables for prediction quickstart](sdk-prediction-environment-variables.md)]

### <a name="create-a-new-javascript-nodejs-file"></a>新しい JavaScript (Node.js) ファイルを作成する

好みのエディターまたは IDE で、`luis_prediction.js` という名前の新しい JavaScript ファイルを作成します。

### <a name="install-the-npm-library-for-the-luis-runtime"></a>LUIS ランタイム用 NPM ライブラリをインストールする

次のコマンドを使用して、アプリケーション ディレクトリ内に依存関係をインストールします。

```console
npm install @azure/cognitiveservices-luis-runtime @azure/ms-rest-js
```

## <a name="object-model"></a>オブジェクト モデル

Language Understanding (LUIS) 作成クライアントは、認証を経て作成キーの保管場所である Azure にアクセスする [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest) オブジェクトです。

クライアントを作成したら、このクライアントを使用して次のような機能にアクセスします。

* `staging` または `production` スロット別の[予測](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-)
* [バージョン別の予測](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getversionprediction-string--string--predictionrequest--models-predictiongetversionpredictionoptionalparams-)

## <a name="code-examples"></a>コード例

以下のコード スニペットは、Language Understanding (LUIS) 予測ランタイム クライアント ライブラリを使用して次のことを実行する方法を示したものです。

* [スロット別の予測](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>依存関係を追加する

プロジェクト ディレクトリから、好みのエディターまたは IDE で `luis_prediction.js` ファイルを開きます。 次の依存関係を追加します。

[!code-javascript [Dependencies](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Dependencies)]

## <a name="authenticate-the-client"></a>クライアントを認証する

1. 独自の必須 LUIS 情報の変数を作成します。

    `LUIS_RUNTIME_KEY` という名前の環境変数から取得した予測キーを管理するための変数を追加します。 アプリケーションの起動後に環境変数を作成した場合、その変数にアクセスするには、アプリケーションを実行しているエディター、IDE、またはシェルを閉じて、再読み込みしなければならない場合があります。 メソッドは後で作成します。

    リソース名 `LUIS_RUNTIME_ENDPOINT` を保持する変数を作成します。

    [!code-javascript [Azure resource variables](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Variables)]

1. `LUIS_APP_ID` という名前の環境変数として、アプリ ID の変数を作成します。 この環境変数をパブリック IoT アプリ **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`** に設定します。 `production` 公開スロットを設定する変数を作成します。

    [!code-javascript [LUIS app variables](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=OtherVariables)]


1. 自分のキーを指定して msRest.ApiKeyCredentials オブジェクトを作成し、それを自分のエンドポイントと共に使用して [LUIS.LUISRuntimeClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest) オブジェクトを作成します。

    [!code-javascript [LUIS Runtime client is required to access predictions for LUIS apps](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=AuthoringCreateClient)]

## <a name="get-prediction-from-runtime"></a>ランタイムから予測を取得する

次のメソッドを追加して、予測ランタイムへの要求を作成します。

ユーザーの発話は、[predictionRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionrequest?view=azure-node-latest) オブジェクトの一部です。

**[luisRuntimeClient.prediction.getSlotPrediction](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-)** メソッドには、アプリ ID、スロット名、要求を満たす予測要求オブジェクトなど、いくつかのパラメーターが必要です。 詳細、すべての意図の表示、ログなど、その他のオプションは省略可能です。

[!code-javascript [LUIS prediction request and response in Node.js NPM SDK](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=predict)]

## <a name="main-code-for-the-prediction"></a>予測の Main コード

次の Main メソッドを使用して変数とメソッドを結び付け、予測を取得します。

[!code-javascript [Main method and main call](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Main)]

## <a name="run-the-application"></a>アプリケーションの実行

自分のアプリケーション ディレクトリで `node luis_prediction.js` コマンドを使用してアプリケーションを実行します。

```console
node luis_prediction.js
```

予測結果として JSON オブジェクトが返されます。

```console
{
   "query":"turn on all lights",
   "prediction":{
      "topIntent":"HomeAutomation.TurnOn",
      "intents":{
         "HomeAutomation.TurnOn":{
            "score":0.5375382
         },
         "None":{
            "score":0.08687421
         },
         "HomeAutomation.TurnOff":{
            "score":0.0207554
         }
      },
      "entities":{
         "HomeAutomation.Operation":[
            "on"
         ],
         "$instance":{
            "HomeAutomation.Operation":[
               {
                  "type":"HomeAutomation.Operation",
                  "text":"on",
                  "startIndex":5,
                  "length":2,
                  "score":0.724984169,
                  "modelTypeId":-1,
                  "modelType":"Unknown",
                  "recognitionSources":[
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

予測が終了したら、ファイルとそのサブディレクトリを削除して、このクイックスタートの作業をクリーンアップします。
