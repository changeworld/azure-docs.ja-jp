---
title: 'チュートリアル: JavaScript を使用して LUIS アプリに発話を追加する方法 | Microsoft Docs'
description: このチュートリアルでは、JavaScript を使用して LUIS アプリを呼び出す方法について説明します。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/18/2017
ms.author: v-geberr
ms.openlocfilehash: b6d021dcfdddb5449aa989c6aa06d7faf326befb
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265461"
---
# <a name="tutorial-add-utterances-to-app-using-javascript"></a>チュートリアル: JavaScript を使用してアプリに発話を追加する
このチュートリアルでは、JavaScript で Authoring API を使用して、意図に発話を追加するプログラムを作成します。

<!-- green checkmark -->
> [!div class="checklist"]
> * Visual Studio コンソール プロジェクトを作成する 
> * LUIS API を呼び出すメソッドを追加して、発話を追加し、アプリをトレーニングする
> * BookFlight 意図の発話例を含む JSON ファイルを追加する
> * コンソールを実行し、発話のトレーニング状態を確認する

詳細については、[意図に発話例を追加する](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08) API、[トレーニング](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) API、[トレーニング状態](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) API に関する技術ドキュメントを参照してください。

この記事に従って LUIS アプリケーションを作成するには、無料の [LUIS][LUIS] アカウントが必要です。

## <a name="prerequisites"></a>前提条件
* LUIS の[**オーサリング キー**](luis-concept-keys.md#authoring-key)。 
* 既存の LUIS **アプリケーション ID** と**バージョン ID**。 
* VSCode の `add-utterances.html` という新しいプロジェクト ファイル。

> [!NOTE] 
> 完成した `add-utterances.html` ファイルは、[**LUIS-Samples** の Github リポジトリ](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/authoring-api-samples/javascript/add-utterance.html)から入手できます。


## <a name="write-the-code"></a>コードを作成する
`add-utterances.html` を作成し、次のコードを追加します。

   [!code-javascript[Java Dependencies](~/samples-luis/documentation-samples/authoring-api-samples/javascript/add-utterance.html "Java Dependencies")]

## <a name="view-in-browser"></a>ブラウザーで表示
1. ブラウザーでファイルを開きます。

2. LUIS オーサリング ID、LUIS アプリケーション ID を追加し、バージョンが `0.1` でなければ変更します。

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
チュートリアルを終了し、不要になったら、Visual Studio とコンソール アプリケーションを削除してください。 

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [LUIS をボットと統合する](luis-csharp-tutorial-build-bot-framework-sample.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website