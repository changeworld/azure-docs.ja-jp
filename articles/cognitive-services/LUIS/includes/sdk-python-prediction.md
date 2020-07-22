---
title: インクルード ファイル
description: インクルード ファイル
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 05/28/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: 8aeb6b964ab38a68a6d8681a4e5c93e1650c6a69
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2020
ms.locfileid: "84171281"
---
Python 用 Language Understanding (LUIS) 予測クライアント ライブラリの用途は次のとおりです。

* スロット別の予測を取得する
* バージョン別の予測を取得する

[リファレンス ドキュメント](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/index?view=azure-python) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis) | [予測ランタイム パッケージ (PyPi)](https://pypi.org/project/azure-cognitiveservices-language-luis/) | [サンプル](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/LUIS)

## <a name="prerequisites"></a>前提条件

* Language Understanding (LUIS) ポータル アカウント - [無料で作成できます](https://www.luis.ai)。
* [Python 3.x](https://www.python.org/)
* LUIS アプリ ID - パブリック IoT アプリ ID `df67dcdb-c37d-46af-88e1-8b97951ca1c2` を使用します。 クイックスタートのコードで使用されるユーザー クエリは、そのアプリに固有のものです。

## <a name="setting-up"></a>設定

### <a name="get-your-language-understanding-luis-runtime-key"></a>Language Understanding (LUIS) ランタイム キーを取得する

LUIS ランタイム リソースを作成して、[ランタイム キー](../luis-how-to-azure-subscription.md)を取得します。 次の手順のために、キーとキーのエンドポイントを保持しておきます。

[!INCLUDE [Set up environment variables for prediction quickstart](sdk-prediction-environment-variables.md)]

### <a name="create-a-new-python-file"></a>新しい Python ファイルを作成する

好みのエディターまたは IDE で、`prediction_quickstart.py` という名前の新しい Python ファイルを作成します。

### <a name="install-the-sdk"></a>SDK のインストール

次のコマンドを使用して、アプリケーション ディレクトリ内に Python 用 Language Understanding (LUIS) 予測ランタイム クライアント ライブラリをインストールします。

```python
python -m pip install azure-cognitiveservices-language-luis
```

## <a name="object-model"></a>オブジェクト モデル

Language Understanding (LUIS) 予測ランタイム クライアントは、認証を経てリソース キーの保管場所である Azure にアクセスする [LUISRuntimeClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-python) オブジェクトです。

クライアントを作成したら、このクライアントを使用して次のような機能にアクセスします。

* [ステージングまたは運用スロット](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)別の予測
* [バージョン](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-version-prediction-app-id--version-id--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)別の予測

## <a name="code-examples"></a>コード例

以下のコード スニペットは、Python 用 Language Understanding (LUIS) 予測ランタイム クライアント ライブラリを使用して次のことを実行する方法を示したものです。

* [スロット別の予測](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>依存関係を追加する

プロジェクト ディレクトリから、好みのエディターまたは IDE で `prediction_quickstart.py` ファイルを開きます。 次の依存関係を追加します。

[!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/prediction_quickstart.py?name=Dependencies)]

## <a name="authenticate-the-client"></a>クライアントを認証する

1. 独自の必須 LUIS 情報の変数を作成します。

    `LUIS_RUNTIME_KEY` という名前の環境変数から取得した予測キーを管理するための変数を追加します。 アプリケーションの起動後に環境変数を作成した場合、その変数にアクセスするには、アプリケーションを実行しているエディター、IDE、またはシェルを閉じて、再読み込みしなければならない場合があります。 メソッドは後で作成します。

    リソース名 `LUIS_RUNTIME_ENDPOINT` を保持する変数を作成します。

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/prediction_quickstart.py?name=AuthorizationVariables)]

1. `LUIS_APP_ID` という名前の環境変数として、アプリ ID の変数を作成します。 この環境変数をパブリック IoT アプリ **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`** に設定します。 `production` 公開スロットを設定する変数を作成します。

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/prediction_quickstart.py?name=OtherVariables)]


1. 自分のキーを使用して資格情報オブジェクトを作成し、それを自分のエンドポイントと共に使用して [LUISRuntimeClientConfiguration]https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclientconfiguration?view=azure-python() オブジェクトを作成します。

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/prediction_quickstart.py?name=Client)]

## <a name="get-prediction-from-runtime"></a>ランタイムから予測を取得する

次のメソッドを追加して、予測ランタイムへの要求を作成します。

ユーザーの発話は、[prediction_request](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-python) オブジェクトの一部です。

**[get_slot_prediction](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)** メソッドには、アプリ ID、スロット名、要求を満たす予測要求オブジェクトなど、いくつかのパラメーターが必要です。 詳細、すべての意図の表示、ログなど、その他のオプションは省略可能です。 この要求により、[PredictionResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionresponse?view=azure-python) オブジェクトが返されます。

[!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/prediction_quickstart.py?name=predict)]

## <a name="main-code-for-the-prediction"></a>予測の Main コード

次の Main メソッドを使用して変数とメソッドを結び付け、予測を取得します。

```python
predict(luisAppID, luisSlotName)
```
## <a name="run-the-application"></a>アプリケーションの実行

自分のアプリケーション ディレクトリで `python prediction_quickstart.py` コマンドを使用してアプリケーションを実行します。

```console
python prediction_quickstart.py
```

クイックスタート コンソールに出力が表示されます。

```console
Top intent: HomeAutomation.TurnOn
Sentiment: None
Intents:
        "HomeAutomation.TurnOn"
Entities: {'HomeAutomation.Operation': ['on']}
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

予測が終了したら、ファイルとそのサブディレクトリを削除して、このクイックスタートの作業をクリーンアップします。
