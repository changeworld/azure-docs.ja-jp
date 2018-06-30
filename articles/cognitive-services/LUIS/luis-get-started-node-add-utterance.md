---
title: 'チュートリアル: Node.js を使用して LUIS アプリに発話を追加する方法 | Microsoft Docs'
description: このチュートリアルでは、Node.js を使用して LUIS アプリを呼び出す方法について説明します。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: a1e028395ce4b1679a367ce110c2ba120128c501
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264228"
---
# <a name="tutorial-add-utterances-app-using-nodejs"></a>チュートリアル: Node.js を使用してアプリに発話を追加する 
このチュートリアルでは、Node.js で Authoring API を使用して、意図に発話を追加するプログラムを作成します。

<!-- green checkmark -->
> [!div class="checklist"]
> * Visual Studio コンソール プロジェクトを作成する 
> * LUIS API を呼び出すメソッドを追加して、発話を追加し、アプリをトレーニングする
> * BookFlight 意図の発話例を含む JSON ファイルを追加する
> * コンソールを実行し、発話のトレーニング状態を確認する

詳細については、[意図に発話例を追加する](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08) API、[トレーニング](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) API、[トレーニング状態](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) API に関する技術ドキュメントを参照してください。

この記事に従って LUIS アプリケーションを作成するには、無料の [LUIS][LUIS] アカウントが必要です。

## <a name="prerequisites"></a>前提条件

* 最新の [**Node.js**](https://nodejs.org/en/download/) と NPM。
* この記事の NPM の依存関係: [**request**](https://www.npmjs.com/package/request)、[**request-promise**](https://www.npmjs.com/package/request-promise)、[**fs-extra**](https://www.npmjs.com/package/fs-extra)。  
* **[推奨]** IntelliSense およびデバッグ用の [Visual Studio Code](https://code.visualstudio.com/)。
* LUIS の**[オーサリング キー](luis-concept-keys.md#authoring-key)**。 このキーは、[LUIS](luis-reference-regions.md) Web サイトの [アカウント設定] で確認できます。
* 既存の LUIS [**アプリケーション ID**](./luis-get-started-create-app.md)。 アプリケーション ID は、アプリケーション ダッシュボードに表示されます。 `add-utterances.js` でコードを実行する前に、`utterances.json` ファイルで使用されている意図とエンティティを含む LUIS アプリケーションが存在している必要があります。 この記事のコードでは、意図とエンティティが作成されません。 既存の意図とエンティティの発話のみが追加されます。 
* 発話を受け取るアプリケーション内の**バージョン ID**。 既定の ID は "0.1" です。
* VSCode で `add-utterances.js` という新しいプロジェクト ファイルを作成します。

> [!NOTE] 
> 完成した `add-utterances.js` ファイルは、[**LUIS-Samples** の Github リポジトリ](https://github.com/Microsoft/LUIS-Samples/tree/master/examples/add-utterances/nodejs)から入手できます。


## <a name="write-the-nodejs-code"></a>Node.js コードを作成する

ファイルに NPM 依存関係を追加します。

   [!code-javascript[NPM Dependencies](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=16-19 "NPM Dependencies")]

LUIS 定数をファイルに追加します。 次のコードをコピーし、実際のオーサリング キー、アプリケーション ID、およびバージョン ID に変更します。

   [!code-javascript[LUIS key and IDs](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=22-29 "LUIS key and IDs")]

発話を含むアップロード ファイルの名前と場所を追加します。 

   [!code-javascript[Add upload file](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=31-33 "Add upload file")]

コマンドラインの値を保持する変数を追加します。

   [!code-javascript[Add upload file](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=35-49 "Add upload file")]


HTTP 呼び出しを送受信する関数 `sendUtteranceToApi` を追加します。 

   [!code-javascript[Send the HTTP request](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=135-151 "Send the HTTP request")]

`addUtterance` 関数から使用される構成の JSON オブジェクトを追加します。

   [!code-javascript[Add configuration information for adding utterance](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=52-59 "Add configuration information for adding utterance")]

`SendUtteranceToApp` から使用される API 要求と応答を管理する関数 `addUtterance` を追加します。

   [!code-javascript[Add configuration information for adding utterance](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=62-92 "Add configuration information for adding utterance")]

`train` 関数から使用される構成の JSON オブジェクトを追加します。

   [!code-javascript[Add configuration information for training LUIS](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=94-101 "Add configuration information for training LUIS")]

トレーニング プロセスを開始する関数 `train` を追加します。 

   [!code-javascript[Train the application](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=103-133 "Train the application")]

コマンドライン変数に基づいて、実行するアクション (発話を追加する、またはトレーニングする) を選択するコードを追加します。

   [!code-javascript[Train the application](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=153-184 "Train the application")]

## <a name="specify-utterances-to-add"></a>追加する発話を指定する
ファイル `utterances.json` を作成して編集し、LUIS アプリに追加する**発話の配列**を指定します。 意図とエンティティは、LUIS アプリに既に存在する**必要があります**。

> [!NOTE]
> `add-utterances.js` でコードを実行する前に、`utterances.json` ファイルで使用されている意図とエンティティを含む LUIS アプリケーションが存在している必要があります。 この記事のコードでは、意図とエンティティが作成されません。 既存の意図とエンティティの発話のみが追加されます。

`text` フィールドには、発話のテキストが含まれています。 `intentName` フィールドは、LUIS アプリの意図の名前と対応している必要があります。 `entityLabels` フィールドは必須です。 どのエンティティにもラベルを付けない場合は、次の例のように空のリストを指定します。

entityLabels リストが空でない場合、`startCharIndex` および `endCharIndex` は `entityName` フィールドで参照されるエンティティをマークする必要があります。 どちらのインデックスもゼロベースのカウントです。つまり、一番上の例の 6 は、大文字の "S" の前のスペースではなく、"Seattle" の "S" を指します。

```json
[
    {
        "text": "go to Seattle",
        "intentName": "BookFlight",
        "entityLabels": [
            {
                "entityName": "Location::LocationTo",
                "startCharIndex": 6,
                "endCharIndex": 12
            }
        ]
    },
    {
        "text": "book a flight",
        "intentName": "BookFlight",
        "entityLabels": []
    }
]
```

## <a name="add-an-utterance-from-the-command-line"></a>コマンドラインから発話を追加する

Node.js を使用してコマンド ラインからアプリケーションを実行します。

引数を指定せずに add-utterance を呼び出すと、アプリに発話は追加されますが、トレーニングは実行されません。
````
> node add-utterances.js
````

このサンプルで、発話の追加 API を呼び出した結果を含む `results.json` とファイルが作成されます。 `response` フィールドは、追加された発話のためにこの形式です。 `hasError` は false です。発話が追加されたことを示します。  

```json
    "response": [
        {
            "value": {
                "UtteranceText": "go to seattle",
                "ExampleId": -5123383
            },
            "hasError": false
        },
        {
            "value": {
                "UtteranceText": "book a flight",
                "ExampleId": -169157
            },
            "hasError": false
        }
    ]
```

## <a name="add-an-utterance-and-train-from-the-command-line"></a>コマンドラインから発話を追加し、トレーニングする
`-train` 引数を指定して add-utterance を呼び出し、トレーニングする要求を送信してから、トレーニング状態を要求します。 状態は、トレーニングの開始直後にキューに格納されます。 状態の詳細はファイルに書き込まれます。

````
> node add-utterances.js -train
````

> [!NOTE]
> 重複する発話は再び追加されませんが、エラーは発生しません。 `response` には元の発話の ID が含まれています。

`-train` 引数を指定してサンプルを呼び出すと、LUIS アプリをトレーニングする要求が正常にキューに格納されたことを示す `training-results.json` ファイルが作成されます。 

次に、トレーニングに成功した要求の結果を示します。
```json
{
    "request": null,
    "response": {
        "statusId": 9,
        "status": "Queued"
    }
}
```

トレーニングする要求がキューに格納された後に、トレーニングを完了するまでに時間がかかることがあります。

## <a name="get-training-status-from-the-command-line"></a>コマンド ラインからトレーニングの状態を取得する
`-status` 引数を指定してサンプルを呼び出し、トレーニングの状態を確認し、状態の詳細をファイルに書き込みます。

````
> node add-utterances.js -status
````

## <a name="clean-up-resources"></a>リソースのクリーンアップ
チュートリアルを終了し、不要になったら、Visual Studio とコンソール アプリケーションを削除してください。 

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"] 
> [プログラムで LUIS アプリを構築する](luis-tutorial-node-import-utterances-csv.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
