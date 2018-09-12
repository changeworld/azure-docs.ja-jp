---
title: 'クイック スタート: PHP を使った LUIS アプリのモデル変更とトレーニング - Azure Cognitive Services | Microsoft Docs'
description: この PHP クイック スタートでは、Home Automation アプリに発話の例を追加してアプリをトレーニングします。 発話の例は、意図にマッピングされる会話形式のユーザー テキストです。 意図に対する発話の例を与えるには、ユーザーによって入力されるどのようなテキストが、どの意図に属しているかを LUIS に学習させます。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/24/2018
ms.author: diberry
ms.openlocfilehash: ae2d3624cb3f8314a613af356730fb1d8b5d4b29
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "43771827"
---
# <a name="quickstart-change-model-using-php"></a>クイック スタート: PHP を使ってモデルを変更する 

[!include[Quickstart introduction for change model](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>前提条件

[!include[Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* 最新の [**PHP**](http://php.net/)。
* PHP の依存関係として openssl を利用できることを確認します。  

[!include[Code is available in LUIS-Samples Github repo](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>発話の例の JSON ファイル

[!include[Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>クイック スタート コードの作成 

`add-utterances.php` という名前のファイルに依存関係を追加します。

   [!code-php[PHP and LUIS Dependencies](~/samples-luis/documentation-samples/quickstarts/change-model/php/add-utterances.php?range=1-22 "PHP and LUIS Dependencies")]

トレーニング状態に使用される GET 要求を追加します。

   [!code-php[SendGet](~/samples-luis/documentation-samples/quickstarts/change-model/php/add-utterances.php?range=24-43 "SendGet")]

発話の作成やトレーニングの開始に使用される POST 要求を追加します。 

   [!code-php[SendPost](~/samples-luis/documentation-samples/quickstarts/change-model/php/add-utterances.php?range=45-65 "SendPost")]

`AddUtterances` 関数を追加します。

   [!code-php[AddUtterances method](~/samples-luis/documentation-samples/quickstarts/change-model/php/add-utterances.php?range=67-72 "AddUtterances method")]


`Train` 関数を追加します。 

   [!code-php[Train](~/samples-luis/documentation-samples/quickstarts/change-model/php/add-utterances.php?range=74-81 "Train")]

`Status` 関数を追加します。

   [!code-php[Status](~/samples-luis/documentation-samples/quickstarts/change-model/php/add-utterances.php?range=83-87 "Status")]

コマンド ライン引数を管理するには、メイン コード ブロックを追加します。

   [!code-php[Main code](~/samples-luis/documentation-samples/quickstarts/change-model/php/add-utterances.php?range=89-93 "Main code")]

## <a name="run-code"></a>コードの実行

PHP を使用してコマンド ラインからアプリケーションを実行します。

### <a name="add-an-utterance-from-the-command-line"></a>コマンド ラインから発話を追加する

PHP を使用してコマンド ラインからアプリケーションを実行します。

`add-utterances.php` を呼び出すと、発話が追加され、トレーニングが実行されて、その状態が返されます。

```CMD
> php add-utterances.php 
```

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


```JSON
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

このクイック スタートで作成したファイルは、完了後、すべて削除してください。 

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"] 
> [プログラムで LUIS アプリを構築する](luis-tutorial-node-import-utterances-csv.md)