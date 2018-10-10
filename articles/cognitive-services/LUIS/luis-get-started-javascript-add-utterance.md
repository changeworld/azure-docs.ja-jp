---
title: JavaScript クイック スタート - モデルの変更と LUIS アプリのトレーニング
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、JavaScript を使用して LUIS アプリを呼び出す方法について説明します。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 38e25b0634b53f4fcc0507091e78ab49b29c8d38
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033398"
---
# <a name="quickstart-change-model-using-javascript"></a>クイック スタート: JavaScript を使ってモデルを変更する

[!INCLUDE [Quickstart introduction for change model](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>前提条件

[!INCLUDE [Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* [Visual Studio Code](https://code.visualstudio.com/)。

[!INCLUDE [Code is available in LUIS-Samples Github repo](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>発話の例の JSON ファイル

[!INCLUDE [Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]


## <a name="create-quickstart-code"></a>クイック スタート コードの作成

`add-utterances.html` を作成し、次のコードを追加します。

   [!code-html[Html code](~/samples-luis/documentation-samples/quickstarts/change-model/javascript/add-utterance.html "Javascript code")]

## <a name="run-code"></a>コードの実行

1. ブラウザーでファイルを開きます。

2. LUIS オーサリング ID、LUIS アプリケーション ID を追加します。

3. アプリケーションに追加する**発話の配列**を変更します。 この配列は utteranceJSON 変数に格納されます。 これらの値は、実際のドメインと発話のニーズに合わせて変更します。 

    ```json
    // example batch utterances
    var utteranceJSON = [
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
        }
    ,
        {
            "text": "book a flight",
            "intentName": "BookFlight",
            "entityLabels": []
        }
    ];
    ```

4. `Upload utterance` ボタンを選択します。 LUIS の結果がボタンの下に表示されます。

5. [`Train model`] ボタンを選択して、これらの新しい発話でアプリケーションをトレーニングします。

6. [`Train Status`] ボタンを選択して、トレーニングの状態を表示します。 

    ![Add-utterances.html](./media/luis-quickstart-javascript-add-utterance/add-utterance.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ
このクイック スタートで作成したファイルは、完了後、すべて削除してください。 

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [LUIS をボットと統合する](luis-csharp-tutorial-build-bot-framework-sample.md)
