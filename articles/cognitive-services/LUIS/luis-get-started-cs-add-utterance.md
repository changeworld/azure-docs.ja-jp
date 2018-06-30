---
title: 'チュートリアル: C# を使用して LUIS アプリに発話を追加する方法 | Microsoft Docs'
description: このチュートリアルでは、C# を使用して LUIS アプリを呼び出す方法について説明します。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: v-geberr
ms.openlocfilehash: d9b3ca46cc635d961edadf3e3555f9656b6b5a1d
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264245"
---
# <a name="tutorial-add-utterances-to-a-luis-app-using-c"></a>チュートリアル: C# を使用して LUIS アプリに発話を追加する 
このチュートリアルでは、C# で Authoring API を使用して、意図に発話を追加するプログラムを作成します。

<!-- green checkmark -->
> [!div class="checklist"]
> * Visual Studio コンソール プロジェクトを作成する 
> * LUIS API を呼び出すメソッドを追加して、発話を追加し、アプリをトレーニングする
> * BookFlight 意図の発話例を含む JSON ファイルを追加する
> * コンソールを実行し、発話のトレーニング状態を確認する

詳細については、[意図に発話例を追加する](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08) API、[トレーニング](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) API、[トレーニング状態](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) API に関する技術ドキュメントを参照してください。

この記事に従って LUIS アプリケーションを作成するには、無料の [LUIS][LUIS] アカウントが必要です。

## <a name="prerequisites"></a>前提条件

* 最新の [**Visual Studio Community エディション**](https://www.visualstudio.com/downloads/)。 
* LUIS の**[オーサリング キー](luis-concept-keys.md#authoring-key)**。 このキーは、[LUIS](luis-reference-regions.md) Web サイトの [アカウント設定] で確認できます。
* 既存の LUIS [**アプリケーション ID**](./luis-get-started-create-app.md)。 アプリケーション ID は、アプリケーション ダッシュボードに表示されます。 このコードを実行する前に、`utterances.json` ファイルで使用されている意図とエンティティを含む LUIS アプリケーションが存在している必要があります。 この記事のコードでは、意図とエンティティが作成されません。 既存の意図とエンティティの発話が追加されます。 
* 発話を受け取るアプリケーション内の**バージョン ID**。 既定の ID は "0.1" です。
* VSCode で `add-utterances.cs` という新しいプロジェクト ファイルを作成します。

> [!NOTE] 
> 例の `utterances.json` ファイルを含む完成した C# ソリューションは、[**LUIS-Samples** の Github リポジトリ](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/authoring-api-samples/csharp/)から入手できます。

## <a name="create-the-project-in-visual-studio"></a>Visual Studio でプロジェクトを作成する

Visual Studio で .Net Framework を使用して新しい **Windows クラシック デスクトップ コンソール** アプリを作成します。 

![Visual Studio プロジェクトの種類](./media/luis-quickstart-cs-add-utterance/vs-project-type.png)

## <a name="add-the-systemweb-dependency"></a>System.Web の依存関係を追加する

Visual Studio プロジェクトでは、プロジェクトに **System.Web** が必要です。 ソリューション エクスプローラーで **[参照]** を右クリックし、**[参照の追加]** を選択します。

![System.web の参照を追加する](./media/luis-quickstart-cs-add-utterance/system.web.png)

## <a name="write-the-c-code"></a>C# コードを作成する
次のような **Program.cs** ファイルを作成します。

```CSharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ConsoleApp3
{
    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}

```

System.IO と System.Net.Http の依存関係を追加します。

   [!code-csharp[Add the dependencies](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=1-6 "Add the dependencies")]


LUIS ID と文字列を **Program** クラスに追加します。

   [!code-csharp[Add the LUIS IDs and strings](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=12-30&dedent=8 "Add the LUIS IDs and strings")]

JsonPrettyPrint メソッドを追加します。

   [!code-csharp[Add the JsonPrettyPrint method](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=32-92 "Add the JsonPrettyPrint method")]

発話の作成やトレーニングの開始に使用される GET 要求を追加します。 

   [!code-csharp[SendGet](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=93-103 "SendGet")]


発話の作成やトレーニングの開始に使用される POST 要求を追加します。 

   [!code-csharp[SendPost](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=104-115 "SendPost")]

`AddUtterances` 関数を追加します。

   [!code-csharp[AddUtterances method](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=116-125 "AddUtterances method")]


`Train` 関数を追加します。 

   [!code-csharp[Train](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=126-136 "Train")]

`Status` 関数を追加します。

   [!code-csharp[Status](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=137-143 "Status")]

引数を管理するには、メイン コードを追加します。

   [!code-csharp[Main code](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=144-172 "Main code")]

## <a name="specify-utterances-to-add"></a>追加する発話を指定する
ファイル `utterances.json` を作成して編集し、LUIS アプリに追加する**発話の配列**を指定します。 意図とエンティティは、LUIS アプリに既に存在する**必要があります**。

> [!NOTE]
> `add-utterances.cs` でコードを実行する前に、`utterances.json` ファイルで使用されている意図とエンティティを含む LUIS アプリケーションが存在している必要があります。 この記事のコードでは、意図とエンティティが作成されません。 既存の意図とエンティティの発話のみが追加されます。

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

## <a name="mark-the-json-file-as-content"></a>JSON ファイルをコンテンツとしてマークする
ソリューション エクスプローラーで `utterances.json` を右クリックし、**[プロパティ]** を選択します。 プロパティ ウィンドウで、`Content` の **[ビルド アクション]** と、`Copy Always` の **[出力ディレクトリにコピー]** をマークします。  

![JSON ファイルをコンテンツとしてマークする](./media/luis-quickstart-cs-add-utterance/content-properties.png)

## <a name="add-an-utterance-from-the-command-line"></a>コマンド ラインから発話を追加する

/bin/Debug ディレクトリから C# を使用してコマンド ラインからアプリケーションをビルドし、実行します。 utterances.json ファイルもこのディレクトリにあることを確認します。

引数として utterance.json のみを指定して add-utterances.cs を呼び出すと、LUIS は追加されますが、新しい発話に対して LUIS はトレーニングされません。
````
ConsoleApp\bin\Debug> ConsoleApp1.exe utterances.json
````

このコマンドラインで、発話の追加 API を呼び出した結果が表示されます。 `response` フィールドは、追加された発話のためにこの形式です。 `hasError` は false です。発話が追加されたことを示します。  

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
`-train` 引数を指定して add-utterance を呼び出し、トレーニングする要求を送信します。 

````
ConsoleApp\bin\Debug> ConsoleApp1.exe -train utterances.json
````

> [!NOTE]
> 重複する発話は再び追加されませんが、エラーは発生しません。 `response` には元の発話の ID が含まれています。

次の JSON は、トレーニングに成功した要求の結果です。

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
ConsoleApp\bin\Debug> ConsoleApp1.exe -status
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
