---
title: Azure Cognitive Services、 Bing Web Search API の Go のクイック スタート | Microsoft Docs
description: Go 言語の使用をすぐに開始し、Azure 上の Cognitive Services で Bing Web Search API をクエリします。
services: cognitive-services
author: Nhoya
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 03/09/2018
ms.author: rosh
ms.reviewer: nhoyadx@gmail.com, v-gedod
ms.openlocfilehash: 86cb67d46bca40c83c2f175ab7fdf6fbf52cb02f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374493"
---
# <a name="call-and-response-your-first-bing-web-search-query-in-go"></a>呼び出しと応答: Go での最初の Bing Web Search クエリ

Bing Web Search API は、Bing.com に似ており、ユーザーのクエリに関連した検索結果を返します。 結果には、Web ページ、イメージ、ビデオ、ニュース、およびエンティティと共に、関連する検索クエリ、誤字の修正、タイム ゾーン、単位変換、翻訳、計算が含まれます。 結果は、それらの関連性と、ユーザーがサブスクライブしている Bing Search API のレベルに基づいています。

API の詳細については、[API リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)をご覧ください。

## <a name="prerequisites"></a>前提条件
[Cognitive Services API アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)と **Bing Search API** を取得している必要があります。 このクイック スタートには[無料試用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)で十分です。 無料試用版を起動するとき、アクセス キーを入力する必要があります。あるいは、Azure ダッシュボードの有料サブスクリプション キーを使用できます。

[Go バイナリ](https://golang.org/dl/)をインストールします。
 
このチュートリアルでは、**コア** ライブラリだけを使用するので、外部との依存関係はありません。

## <a name="core-libraries"></a>コア ライブラリ

エンドポイントに要求を送信するには `http` を、応答を読み取るには `ioutil` を、JSON を処理するには `time` および `json` を、出力を印刷するには `fmt` を使用します

```
package main

import (
    "fmt"
    "net/http"
    "io/iutil"
    "time"
    "encoding/json"
)
```

## <a name="define-variables"></a>変数の定義
API エンドポイントと検索用語を設定します。

```
//This is the valid endpoint at the time of the writing
const endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/search"
//API token
token := "YOUR-ACCESS-KEY"
searchTerm := "Microsoft Cognitive Services"
```

## <a name="building-and-sending-the-request"></a>要求のビルドと送信

```
//Declare a new GET request
req, err := http.NewRequest("GET", endpoint, nil)
if err != nil {
    panic(err)
}
//Add the payload to the request
param := req.URL.Query()
param.Add("q", searchTerm)
//Encoding the payload
req.URL.RawQuery = param.Encode()

//Insert the API token in the request header
req.Header.Add("Ocp-Apim-Subscription-Key", token)

//create new client
client := new(http.Client)
//Send the request
resp, err := client.Do(req)
if err != nil {
    panic(err)
}
//Close the response body at the function exit
defer resp.Body.Close() 
```

## <a name="printing-the-answer"></a>応答の印刷
検索結果は、`resp` 変数の内部にあります。 変数から応答の本文を印刷します。

```
body, err := ioutil.ReadAll(resp.Body)
if err != nil {
    panic(err)
}
//Convert body from byte to string.
fmt.Println(string(body))
```

## <a name="put-everything-together"></a>すべてをまとめる

```
package main
import (
    "fmt"
    "net/http"
    "io/ioutil"
    "time"
    "encoding/json"
)

//The struct that will contain the answer
type BingAnswer struct {
        Type         string `json:"_type"`
        QueryContext struct {
                OriginalQuery string `json:"originalQuery"`
        } `json:"queryContext"`
        WebPages struct {
                WebSearchURL          string `json:"webSearchUrl"`
                TotalEstimatedMatches int    `json:"totalEstimatedMatches"`
                Value                 []struct {
                        ID               string    `json:"id"`
                        Name             string    `json:"name"`
                        URL              string    `json:"url"`
                        IsFamilyFriendly bool      `json:"isFamilyFriendly"`
                        DisplayURL       string    `json:"displayUrl"`
                        Snippet          string    `json:"snippet"`
                        DateLastCrawled  time.Time `json:"dateLastCrawled"`
                        SearchTags       []struct {
                                Name    string `json:"name"`
                                Content string `json:"content"`
                        } `json:"searchTags,omitempty"`
                        About []struct {
                                Name string `json:"name"`
                        } `json:"about,omitempty"`
                } `json:"value"`
        } `json:"webPages"`
        RelatedSearches struct {
                ID    string `json:"id"`
                Value []struct {
                        Text         string `json:"text"`
                        DisplayText  string `json:"displayText"`
                        WebSearchURL string `json:"webSearchUrl"`
                } `json:"value"`
        } `json:"relatedSearches"`
        RankingResponse struct {
                Mainline struct {
                        Items []struct {
                                AnswerType  string `json:"answerType"`
                                ResultIndex int    `json:"resultIndex"`
                                Value       struct {
                                        ID string `json:"id"`
                                } `json:"value"`
                        } `json:"items"`
                } `json:"mainline"`
                Sidebar struct {
                        Items []struct {
                                AnswerType string `json:"answerType"`
                                Value      struct {
                                        ID string `json:"id"`
                                } `json:"value"`
                        } `json:"items"`
                } `json:"sidebar"`
        } `json:"rankingResponse"`
}

func main() {
    const endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/search"
    token := "YOUR-ACCESS-KEY"
    searchTerm := "Microsoft Cognitive Services"
    
    req, err := http.NewRequest("GET", endpoint, nil)
    if err != nil {
        panic(err)
    }
    
    param := req.URL.Query()
    param.Add("q", searchTerm)
    req.URL.RawQuery = param.Encode()

    req.Header.Add("Ocp-Apim-Subscription-Key", token)
    
    client := new(http.Client)
    resp, err := client.Do(req)
    if err != nil {
        panic(err)
    }
    defer resp.Body.Close() 
    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        panic(err)
    }
    //creating a new answer struct
    ans := new(BingAnswer)
    err = json.Unmarshal(body, &ans)
    if err != nil {
         fmt.Println(err)
    }
    //Iterate over search results
    for _, result := range ans.WebPages.Value {
         //Printing result name and URL
         fmt.Println(result.Name, "||", result.URL)
    }

}
```

## <a name="next-steps"></a>次の手順

[Bing Web Search の概要](../overview.md)  
[試してみる](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/)  
[無料試用版のアクセス キーを取得する](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)
[Bing Web Search API リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)

