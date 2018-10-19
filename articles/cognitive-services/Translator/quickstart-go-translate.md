---
title: 'クイック スタート: テキストを翻訳する - Translator Text、Go'
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、Go で Translator Text API を使ってテキストを別の言語に翻訳します。
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/29/2018
ms.author: nolachar
ms.openlocfilehash: df8b938c9541a4f57a337065af0ab02fe6815c6d
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2018
ms.locfileid: "46126702"
---
# <a name="quickstart-translate-text-with-go"></a>クイック スタート: Go を使ったテキストの翻訳

このクイック スタートでは、Translator Text API を使って、テキストを別の言語に翻訳します。

## <a name="prerequisites"></a>前提条件

このコードを実行するためには、[Go ディストリビューション](https://golang.org/doc/install)をインストールする必要があります。 サンプル コードでは、**コア** ライブラリだけを使用するので、外部との依存関係はありません。

Translator Text API を使用するには、サブスクリプション キーも必要となります。「[Translator Text API にサインアップする方法](translator-text-how-to-signup.md)」を参照してください。

## <a name="translate-request"></a>翻訳要求

以下のコードは、[Translate](./reference/v3-0-translate.md) メソッドを使ってソース テキストを別の言語に翻訳します。

1. 任意のコード エディターで新しい Go プロジェクトを作成します。
2. 次に示すコードを追加します。
3. `subscriptionKey` の値を、お使いのサブスクリプションで有効なアクセス キーに置き換えます。
4. "go" という拡張子でファイルを保存します。
5. Go がインストールされているコンピューターのコマンド プロンプトを開きます。
6. ファイルをビルドします (例: "go build quickstart-translate.go")。
7. ファイルを実行します (例: "quickstart-translate")。

```golang
package translate

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
    const uriPath = "/translate?api-version=3.0"

    // Translate to German and Italian
    const params = "&to=de&to=it"

    const uri = uriBase + uriPath + params

    const text = "Hello, world!"

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

## <a name="translate-response"></a>翻訳応答

成功した応答は、次の例に示すように JSON で返されます。

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "translations": [
      {
        "text": "Hallo Welt!",
        "to": "de"
      },
      {
        "text": "Salve, mondo!",
        "to": "it"
      }
    ]
  }
]
```

## <a name="next-steps"></a>次の手順

GitHub の [Azure SDK for Go](https://github.com/Azure/azure-sdk-for-go) から Cognitive Services API の Go パッケージを詳しく見てみましょう。

> [!div class="nextstepaction"]
> [GitHub で Go パッケージを詳しく見てみる](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices)
