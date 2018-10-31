---
title: 'クイック スタート: 文の長さを取得する (Go) - Translator Text API'
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、Go で Translator Text API を使ってテキストに含まれる文の長さを調べます。
services: cognitive-services
author: noellelacharite
manager: erhopf
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/29/2018
ms.author: erhopf
ms.openlocfilehash: 92ea2291760edf16863dc58a00bac4389f818e72
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2018
ms.locfileid: "49644926"
---
# <a name="quickstart-get-sentence-lengths-with-the-translator-text-rest-api-go"></a>クイック スタート: Translator Text REST API を使用して文の長さを取得する (Go)

このクイック スタートでは、Translator Text API を使って、テキストに含まれる文の長さを調べます。

## <a name="prerequisites"></a>前提条件

このコードを実行するためには、[Go ディストリビューション](https://golang.org/doc/install)をインストールする必要があります。 サンプル コードでは、**コア** ライブラリだけを使用するので、外部との依存関係はありません。

Translator Text API を使用するには、サブスクリプション キーも必要となります。「[Translator Text API にサインアップする方法](translator-text-how-to-signup.md)」を参照してください。

## <a name="breaksentence-request"></a>BreakSentence 要求

以下のコードは、[BreakSentence](./reference/v3-0-break-sentence.md) メソッドを使ってソース テキストを文に分割します。

1. 任意のコード エディターで新しい Go プロジェクトを作成します。
2. 次に示すコードを追加します。
3. `subscriptionKey` の値を、お使いのサブスクリプションで有効なアクセス キーに置き換えます。
4. "go" という拡張子でファイルを保存します。
5. Go がインストールされているコンピューターのコマンド プロンプトを開きます。
6. ファイルをビルドします (例: "go build quickstart-sentences.go")。
7. ファイルを実行します (例: "quickstart-sentences")。

```golang
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "strconv"
    "strings"
    "time"
)

func main() {
    // Replace the subscriptionKey string value with your valid subscription key
    const subscriptionKey = "<Subscription Key>"

    const uriBase = "https://api.cognitive.microsofttranslator.com"
    const uriPath = "/breaksentence?api-version=3.0"

    const uri = uriBase + uriPath

    const text = "How are you? I am fine. What did you do today?"

    r := strings.NewReader("[{\"Text\" : \"" + text + "\"}]")

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Content-Length", strconv.FormatInt(req.ContentLength, 10))
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    resp, err := client.Do(req)
    if err != nil {
        fmt.Printf("Error on request: %v\n", err)
        return
    }
    defer resp.Body.Close()

    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        fmt.Printf("Error reading response body: %v\n", err)
        return
    }

    var f interface{}
    json.Unmarshal(body, &f)

    jsonFormatted, err := json.MarshalIndent(f, "", "  ")
    if err != nil {
        fmt.Printf("Error producing JSON: %v\n", err)
        return
    }
    fmt.Println(string(jsonFormatted))
}
```

## <a name="breaksentence-response"></a>BreakSentence 応答

成功した応答は、次の例に示すように JSON で返されます。

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "sentLen": [
      13,
      11,
      22
    ]
  }
]
```

## <a name="next-steps"></a>次の手順

GitHub の [Azure SDK for Go](https://github.com/Azure/azure-sdk-for-go) から Cognitive Services API の Go パッケージを詳しく見てみましょう。

> [!div class="nextstepaction"]
> [GitHub で Go パッケージを詳しく見てみる](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices)
