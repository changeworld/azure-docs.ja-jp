---
title: 'クイック スタート: テキスト スクリプトを変換する - Translator Text、Go'
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、Go で Translator Text API を使って 1 つの言語の中でテキストの表記を変換します。
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/29/2018
ms.author: nolachar
ms.openlocfilehash: c4930e03f5d366eb3f21b44c5be90c20230065c3
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2018
ms.locfileid: "46126753"
---
# <a name="quickstart-transliterate-text-with-go"></a>クイック スタート: Go を使ったテキストの表記変換

このクイック スタートでは、Translator Text API を使って、1 つの言語の中でテキストの表記を変換します。

## <a name="prerequisites"></a>前提条件

このコードを実行するためには、[Go ディストリビューション](https://golang.org/doc/install)をインストールする必要があります。 サンプル コードでは、**コア** ライブラリだけを使用するので、外部との依存関係はありません。

Translator Text API を使用するには、サブスクリプション キーも必要となります。「[Translator Text API にサインアップする方法](translator-text-how-to-signup.md)」を参照してください。

## <a name="transliterate-request"></a>表記変換要求

以下のコードは、[Transliterate](./reference/v3-0-transliterate.md) メソッドを使って、1 つの言語の中でテキストの表記を変換します。

1. 任意のコード エディターで新しい Go プロジェクトを作成します。
2. 次に示すコードを追加します。
3. `subscriptionKey` の値を、お使いのサブスクリプションで有効なアクセス キーに置き換えます。
4. "go" という拡張子でファイルを保存します。
5. Go がインストールされているコンピューターのコマンド プロンプトを開きます。
6. ファイルをビルドします (例: "go build quickstart-transliterate.go")。
7. ファイルを実行します (例: "quickstart-transliterate")。

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
    const uriPath = "/transliterate?api-version=3.0"

    // Transliterate text in Japanese from Japanese script (i.e. Hiragana/Katakana/Kanji) to Latin script
    const params = "&language=ja&fromScript=jpan&toScript=latn"

    const uri = uriBase + uriPath + params

    // Transliterate "good afternoon".
    const text = "こんにちは"

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

## <a name="transliterate-response"></a>表記変換応答

成功した応答は、次の例に示すように JSON で返されます。

```json
[
  {
    "script": "latn",
    "text": "konnnichiha"
  }
]
```

## <a name="next-steps"></a>次の手順

GitHub の [Azure SDK for Go](https://github.com/Azure/azure-sdk-for-go) から Cognitive Services API の Go パッケージを詳しく見てみましょう。

> [!div class="nextstepaction"]
> [GitHub で Go パッケージを詳しく見てみる](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices)
