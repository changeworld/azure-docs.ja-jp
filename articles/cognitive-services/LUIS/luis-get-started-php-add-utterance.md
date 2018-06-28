---
title: 'チュートリアル: PHP を使用して LUIS アプリに発話を追加する方法 | Microsoft Docs'
description: このチュートリアルでは、PHP を使用して LUIS アプリを呼び出す方法について説明します。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: 59150b7ed6782c28f243041be2ed6aa17e69cc01
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2018
ms.locfileid: "36263782"
---
# <a name="tutorial-add-utterances-to-app-using-php"></a>チュートリアル: PHP を使用してアプリに発話を追加する 
このチュートリアルでは、PHP で Authoring API を使用して、意図に発話を追加するプログラムを作成します。

<!-- green checkmark -->
> [!div class="checklist"]
> * Visual Studio コンソール プロジェクトを作成する 
> * LUIS API を呼び出すメソッドを追加して、発話を追加し、アプリをトレーニングする
> * BookFlight 意図の発話例を含む JSON ファイルを追加する
> * コンソールを実行し、発話のトレーニング状態を確認する

詳細については、[意図に発話例を追加する](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08) API、[トレーニング](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) API、[トレーニング状態](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) API に関する技術ドキュメントを参照してください。

この記事に従って LUIS アプリケーションを作成するには、無料の [LUIS][LUIS] アカウントが必要です。

## <a name="prerequisites"></a>前提条件

* 最新の [**PHP**](http://php.net/)。
* PHP の依存関係として openssl を利用できることを確認します。  
* LUIS の**[オーサリング キー](luis-concept-keys.md#authoring-key)**。 このキーは、[LUIS](luis-reference-regions.md) Web サイトの [アカウント設定] で確認できます。
* 既存の LUIS [**アプリケーション ID**](./luis-get-started-create-app.md)。 アプリケーション ID は、アプリケーション ダッシュボードに表示されます。 `add-utterances.php` でコードを実行する前に、`utterances.json` ファイルで使用されている意図とエンティティを含む LUIS アプリケーションが存在している必要があります。 この記事のコードでは、意図とエンティティが作成されません。 既存の意図とエンティティの発話のみが追加されます。 
* 発話を受け取るアプリケーション内の**バージョン ID**。 既定の ID は "0.1" です。
* VSCode で `add-utterances.php` という新しいプロジェクト ファイルを作成します。

> [!NOTE] 
> 完成した `add-utterances.cs` ファイルと例の `utterances.json` ファイルは、[**LUIS-Samples** の Github リポジトリ](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/authoring-api-samples/php/)から入手できます。


## <a name="write-the-php-code"></a>PHP コードを作成する

ファイルに依存関係を追加します。

   [!code-php[PHP and LUIS Dependencies](~/samples-luis/documentation-samples/authoring-api-samples/php/add-utterances.php?range=10-29 "PHP and LUIS Dependencies")]

トレーニング状態に使用される GET 要求を追加します。

   [!code-php[SendGet](~/samples-luis/documentation-samples/authoring-api-samples/php/add-utterances.php?range=31-50 "SendGet")]

発話の作成やトレーニングの開始に使用される POST 要求を追加します。 

   [!code-php[SendPost](~/samples-luis/documentation-samples/authoring-api-samples/php/add-utterances.php?range=52-72 "SendPost")]

`AddUtterances` 関数を追加します。

   [!code-php[AddUtterances method](~/samples-luis/documentation-samples/authoring-api-samples/php/add-utterances.php?range=74-79 "AddUtterances method")]


`Train` 関数を追加します。 

   [!code-php[Train](~/samples-luis/documentation-samples/authoring-api-samples/php/add-utterances.php?range=81-88 "Train")]

`Status` 関数を追加します。

   [!code-php[Status](~/samples-luis/documentation-samples/authoring-api-samples/php/add-utterances.php?range=90-94 "Status")]

コマンド ライン引数を管理するには、メイン コード ブロックを追加します。

   [!code-php[Main code](~/samples-luis/documentation-samples/authoring-api-samples/php/add-utterances.php?range=96-120 "Main code")]

## <a name="specify-utterances-to-add"></a>追加する発話を指定する
ファイル `utterances.json` を作成して編集し、LUIS アプリに追加する**発話の配列**を指定します。 意図とエンティティは、LUIS アプリに既に存在する**必要があります**。

> [!NOTE]
> `add-utterances.php` でコードを実行する前に、`utterances.json` ファイルで使用されている意図とエンティティを含む LUIS アプリケーションが存在している必要があります。 この記事のコードでは、意図とエンティティが作成されません。 既存の意図とエンティティの発話のみが追加されます。

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

## <a name="add-an-utterance-from-the-command-line"></a>コマンド ラインから発話を追加する

PHP を使用してコマンド ラインからアプリケーションを実行します。

引数として utterance.json のみを指定して `add-utterances.php` を呼び出すと、LUIS は追加されますが、新しい発話に対して LUIS はトレーニングされません。
````
> php add-utterances.php ./utterances.json
````

発話の追加 API 呼び出しから、次の JSON が返されます。 `response` フィールドは、追加された発話のためにこの形式です。 `hasError` は false です。発話が追加されたことを示します。  

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

## <a name="add-an-utterance-and-train-from-the-command-line"></a>コマンド ラインから発話を追加し、トレーニングする
`-train` 引数を指定して `add-utterance.php` を呼び出し、トレーニングする要求を送信します。 

````
> php add-utterances.php ./utterances.json -train
````

> [!NOTE]
> 重複する発話は再び追加されませんが、エラーは発生しません。 `response` には元の発話の ID が含まれています。

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
`-status` 引数を指定してアプリを呼び出し、トレーニングの状態を確認して、状態の詳細を表示します。

````
> php add-utterances.php -status
````

```
Requested training status.
[
   {
      "modelId": "eb2f117c-e10a-463e-90ea-1a0176660acc",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "c1bdfbfc-e110-402e-b0cc-2af4112289fb",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "863023ec-2c96-4d68-9c44-34c1cbde8bc9",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "82702162-73ba-4ae9-a6f6-517b5244c555",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "37121f4c-4853-467f-a9f3-6dfc8cad2763",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "de421482-753e-42f5-a765-ad0a60f50d69",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "80f58a45-86f2-4e18-be3d-b60a2c88312e",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "c9eb9772-3b18-4d5f-a1e6-e0c31f91b390",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "2afec2ff-7c01-4423-bb0e-e5f6935afae8",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "95a81c87-0d7b-4251-8e07-f28d180886a1",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   }
]
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ
チュートリアルを終了し、不要になったら、Visual Studio とコンソール アプリケーションを削除してください。 

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"] 
> [プログラムで LUIS アプリを構築する](luis-tutorial-node-import-utterances-csv.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website