---
title: C# で REST 呼び出しを使用してモデルを取得する
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: 056b2d2b1951b6630b61bbd6fd8a8c38b272900a
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2020
ms.locfileid: "76966949"
---
## <a name="prerequisites"></a>前提条件

* Azure Language Understanding (作成リソースの 32 文字のキーおよび作成エンドポイントの URL)。 [Azure portal](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) または [Azure CLI](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli) で作成します。
* cognitive-services-language-understanding GitHub リポジトリから [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) アプリをインポートします。
* インポートした TravelAgent アプリ用の LUIS アプリケーション ID。 アプリケーション ID は、アプリケーション ダッシュボードに表示されます。
* 発話を受け取るアプリケーション内のバージョン ID。 既定の ID は "0.1" です。
* [Node.js](https://nodejs.org/) プログラミング言語
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>発話の例の JSON ファイル

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]


## <a name="change-model-programmatically"></a>プログラムを使用してモデルを変更する

Go を使用して、機械学習されたエンティティ [API](https://aka.ms/luis-apim-v3-authoring) をアプリケーションに追加します。

1. `model.js` という名前で新しいファイルを作成します。 次のコードを追加します。

    ```javascript
    var request = require('request');
    var requestpromise = require('request-promise');

    // 32 character key value
    const LUIS_authoringKey = "YOUR-KEY";

    // endpoint example: your-resource-name.api.cognitive.microsoft.com
    const LUIS_endpoint = "YOUR-ENDPOINT";
    const LUIS_appId = "YOUR-APP-ID";
    const LUIS_versionId = "0.1";
    const addUtterancesURI = `https://${LUIS_endpoint}/luis/authoring/v3.0-preview/apps/${LUIS_appId}/versions/${LUIS_versionId}/examples`;
    const addTrainURI = `https://${LUIS_endpoint}/luis/authoring/v3.0-preview/apps/${LUIS_appId}/versions/${LUIS_versionId}/train`;

    const utterances = [
            {
              'text': 'go to Seattle today',
              'intentName': 'BookFlight',
              'entityLabels': [
                {
                  'entityName': 'Location::LocationTo',
                  'startCharIndex': 6,
                  'endCharIndex': 12
                }
              ]
            },
            {
                'text': 'a barking dog is annoying',
                'intentName': 'None',
                'entityLabels': []
            }
          ];

    const main = async() =>{


        await addUtterance();
        await train("POST");
        await trainStatus("GET");

    }
    const addUtterance = async () => {

        const options = {
            uri: addUtterancesURI,
            method: 'POST',
            headers: {
                'Ocp-Apim-Subscription-Key': LUIS_authoringKey
            },
            json: true,
            body: utterances
        };

        const response = await requestpromise(options)
        console.log(response.body);
    }
    const train = async (verb) => {

        const options = {
            uri: addTrainURI,
            method: verb,
            headers: {
                'Ocp-Apim-Subscription-Key': LUIS_authoringKey
            },
            json: true,
            body: null // The body can be empty for a training request
        };

        const response = await requestpromise(options)
        console.log(response.body);
    }

    // MAIN
    main().then(() => console.log("done")).catch((err)=> console.log(err returned));
    ```

1. `YOUR-` で始まる値を実際の値に置き換えます。

    |Information|目的|
    |--|--|
    |`YOUR-KEY`|32 文字の実際の作成キー。|
    |`YOUR-ENDPOINT`| 作成 URL エンドポイント。 たとえば、「 `replace-with-your-resource-name.api.cognitive.microsoft.com` 」のように入力します。 リソース名は、リソースの作成時に設定します。|
    |`YOUR-APP-ID`| LUIS アプリ ID。 |

    割り当てられたキーとリソースは、LUIS ポータルの [Manage]\(管理\) セクションの **[Azure リソース]** ページで確認できます。 アプリ ID は、同じ [Manage]\(管理\) セクションの **[アプリケーションの設定]** ページで入手できます。

1. このファイルを作成したときと同じディレクトリからコマンド プロンプトで次のコマンドを入力し、ファイルを実行します。

    ```console
    node model.js
    ```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイックスタートを使用して完了したときに、ファイル システムからファイルを削除します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [アプリのベスト プラクティス](../luis-concept-best-practices.md)