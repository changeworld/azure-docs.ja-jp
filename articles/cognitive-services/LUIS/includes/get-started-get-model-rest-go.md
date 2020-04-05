---
title: Go で REST 呼び出しを使用してモデルを取得する
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: 8d180eeffdbc41db6fa0e636daf7702faad47fcc
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368482"
---
## <a name="prerequisites"></a>前提条件

* Azure Language Understanding (作成リソースの 32 文字のキーおよび作成エンドポイントの URL)。 [Azure portal](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) または [Azure CLI](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli) で作成します。
* cognitive-services-language-understanding GitHub リポジトリから [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) アプリをインポートします。
* インポートした TravelAgent アプリ用の LUIS アプリケーション ID。 アプリケーション ID は、アプリケーション ダッシュボードに表示されます。
* 発話を受け取るアプリケーション内のバージョン ID。 既定の ID は "0.1" です。
* [Go](https://golang.org/) プログラミング言語
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>発話の例の JSON ファイル

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="change-model-programmatically"></a>プログラムを使用してモデルを変更する

1. `predict.go` という名前で新しいファイルを作成します。 次のコードを追加します。

    ```go
    // dependencies
    package main
    import (
        "fmt"
        "net/http"
        "io/ioutil"
        "log"
        "strings"
    )

    // main function
    func main() {

        // NOTE: change to your app ID
        var appID = "YOUR-APP-ID"

        // NOTE: change to your authoring key
        var authoringKey = "YOUR-KEY"

        // NOTE: change to your authoring key's endpoint, for example, your-resource-name.api.cognitive.microsoft.com
        var endpoint = "YOUR-ENDPOINT"

        var version = "0.1"

        var exampleUtterances = `
        [
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
          ]
        `

        fmt.Println("add example utterances requested")
        addUtterance(authoringKey, appID, version, exampleUtterances, endpoint)

        fmt.Println("training selected")
        requestTraining(authoringKey, appID, version, endpoint)

        fmt.Println("training status selected")
        getTrainingStatus(authoringKey, appID, version, endpoint)
    }

    // get utterances from file and add to model
    func addUtterance(authoringKey string, appID string,  version string, labeledExampleUtterances string, endpoint string){

        var authoringUrl = fmt.Sprintf("https://%s/luis/authoring/v3.0-preview/apps/%s/versions/%s/examples", endpoint, appID, version)

        httpRequest("POST", authoringUrl, authoringKey, labeledExampleUtterances)
    }
    func requestTraining(authoringKey string, appID string,  version string, endpoint string){

        trainApp("POST", authoringKey, appID, version, endpoint)
    }
    func trainApp(httpVerb string, authoringKey string, appID string,  version string, endpoint string){

        var authoringUrl = fmt.Sprintf("https://%s/luis/authoring/v3.0-preview/apps/%s/versions/%s/train", endpoint, appID, version)

        httpRequest(httpVerb,authoringUrl, authoringKey, "")
    }
    func getTrainingStatus(authoringKey string, appID string, version string, endpoint string){

        trainApp("GET", authoringKey, appID, version, endpoint)
    }
    // generic HTTP request
    // includes setting header with authoring key
    func httpRequest(httpVerb string, url string, authoringKey string, body string){

        client := &http.Client{}

        request, err := http.NewRequest(httpVerb, url, strings.NewReader(body))
        request.Header.Add("Ocp-Apim-Subscription-Key", authoringKey)

        fmt.Println("body")
        fmt.Println(body)

        response, err := client.Do(request)
        if err != nil {
            log.Fatal(err)
        } else {
            defer response.Body.Close()
            contents, err := ioutil.ReadAll(response.Body)
            if err != nil {
                log.Fatal(err)
            }
            fmt.Println("   ", response.StatusCode)
            fmt.Println(string(contents))
        }
    }
    ```

1. `YOUR-` で始まる値を実際の値に置き換えます。

    |Information|目的|
    |--|--|
    |`YOUR-KEY`|32 文字の実際の作成キー。|
    |`YOUR-ENDPOINT`| 作成 URL エンドポイント。 たとえば、「 `replace-with-your-resource-name.api.cognitive.microsoft.com` 」のように入力します。 リソース名は、リソースの作成時に設定します。|
    |`YOUR-APP-ID`| LUIS アプリ ID。 |

    割り当てられたキーとリソースは、LUIS ポータルの [Manage]\(管理\) セクションの **[Azure リソース]** ページで確認できます。 アプリ ID は、同じ [Manage]\(管理\) セクションの **[アプリケーションの設定]** ページで入手できます。

1. このファイルを作成したのと同じディレクトリからコマンド プロンプトで次のコマンドを入力し、Go ファイルをコンパイルします。

    ```console
    go build model.go
    ```

1. コマンド プロンプトに次のテキストを入力して、コマンド ラインから Go アプリケーションを実行します。

    ```console
    go run model.go
    ```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイックスタートを使用して完了したときに、ファイル システムからファイルを削除します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [アプリのベスト プラクティス](../luis-concept-best-practices.md)