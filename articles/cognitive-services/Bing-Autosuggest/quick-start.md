---
title: Autosuggest API クイック スタート | Microsoft Docs
description: Bing Autosuggest API の使用方法の概要について説明します。
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 1482E781-7352-4A3F-B1D5-B896381348C4
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: a7b54a1fb0b7c76eb72097357a6b51aa02e6e2fd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377000"
---
# <a name="making-your-first-autosuggest-query"></a>初めての Autosuggest のクエリを実行する

最初の呼び出しを行う前に、Cognitive Services のサブスクリプション キーを入手する必要があります。 キーを取得するには、「[Cognitive Services を試す](https://azure.microsoft.com/try/cognitive-services/?api=autosuggest-api)」を参照してください。

Web 検索の結果を取得するには、次のエンドポイントに GET 要求を送信します。

```http
https://api.cognitive.microsoft.com/bing/v5.0/Suggestions
```

> [!NOTE]
> V7 プレビュー エンドポイント:
>
> ```http
> https://api.cognitive.microsoft.com/bing/v7.0/Suggestions
> ```

要求では、HTTPS プロトコルを使う必要があります。

すべての要求をサーバーから送信することをお勧めします。 クライアント アプリケーションの一部としてキーを配布すると、悪意のあるサードパーティがアクセスする可能性が高くなります。 また、サーバーから呼び出しを行うと、API の将来のバージョンでアップグレードする場所が 1 つで済みます。

要求では、ユーザーの部分検索語句が含まれている [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query) クエリ パラメーターを指定する必要があります。 必須ではありませんが、要求では [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#mkt) クエリ パラメーターも指定するべきです。このパラメーターは、結果取得元の市場を示します。 省略可能なクエリ パラメーターの一覧については、「[Query Parameters (クエリ パラメーター)](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query-parameters)」を参照してください。 すべてのクエリ パラメーターの値は、URL でエンコードする必要があります。

要求では、[Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#subscriptionkey) ヘッダーを指定する必要があります。 省略可能ですが、次のヘッダーも指定することをお勧めします。

- [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#useragent)
- [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientid)
- [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientip)
- [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#location)

クライアント IP と場所のヘッダーは、場所に対応したコンテンツを返すために重要です。

すべての要求ヘッダーと応答ヘッダーの一覧については、「[Headers (ヘッダー)](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#headers)」を参照してください。

## <a name="the-request"></a>要求

要求には、推奨されるすべてのクエリ パラメーターとヘッダーが含まれている必要があります。 ユーザーが検索ボックスに新しい文字を入力するたびにこの API を呼び出します。 クエリ文字列の完全性は、API から返されるクエリ語句の候補の関連性に影響します。 クエリ文字列がより完全になるほど、より高い関連性を持つクエリ語句の候補が返されます。 たとえば、API から *s* に対して返される可能性のある候補は、*sailing dinghies* に対して返されるクエリよりもおそらく関連性が低くなります。 

次の例に、*sail* に対するクエリ文字列の候補を返す要求を示します。

```http
GET https://api.cognitive.microsoft.com/bing/v5.0/suggestions?q=sail&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

> [!NOTE]
> V7 プレビュー要求:

> ```http
> GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE
> X-MSEdge-ClientIP: 999.999.999.999
> X-Search-Location: lat:47.60357;long:-122.3295;re:100
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
> Host: api.cognitive.microsoft.com
> ```

いずれかの Bing API を初めて呼び出す場合は、クライアント ID ヘッダーを含めないでください。 クライアント ID ヘッダーを含めるのは、過去に Bing API を呼び出したことがあり、かつユーザーとデバイスの組み合わせに対応するクライアント ID が Bing から返されたことがある場合だけです。

前述の要求への応答は次のようになります。 この応答には、検索クエリの候補の一覧を含む Web 候補グループが含まれます。 それぞれの候補には、`displayText`、`query`、および `url` フィールドが含まれています。

`displayText` フィールドには、検索ボックスのドロップダウン リストの設定に使用するクエリ候補が含まれています。 応答に含まれるすべての候補を、指定された順序で表示する必要があります。  

ユーザーがドロップダウン リストからクエリを選択した場合は、`query` フィールドのクエリ文字列を使用して [Bing Search API](../bing-web-search/search-the-web.md) を呼び出し、独自にその結果を表示することができます。 または、`url` フィールドの URL を使用して、ユーザーを Bing 検索結果ページに誘導することもできます。

```  
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "Suggestions",
    "queryContext" : {
        "originalQuery" : "sail"
    },
    "suggestionGroups" : [{
        "name" : "Web",
        "searchSuggestions" : [{
            "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
            "displayText" : "sailing lessons seattle",
            "query" : "sailing lessons seattle",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+moon+news&FORM=USBAPI",
            "displayText" : "sailor moon news",
            "query" : "sailor moon news",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+jack%27s+lincoln+city&FORM=USBAPI",
            "displayText" : "sailor jack's lincoln city",
            "query" : "sailor jack's lincoln city",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailing+anarchy&FORM=USBAPI",
            "displayText" : "sailing anarchy",
            "query" : "sailing anarchy",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale&FORM=USBAPI",
            "displayText" : "sailboats for sale",
            "query" : "sailboats for sale",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailstn.mylabsplus.com&FORM=USBAPI",
            "displayText" : "sailstn.mylabsplus.com",
            "query" : "sailstn.mylabsplus.com",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailusfood&FORM=USBAPI",
            "displayText" : "sailusfood",
            "query" : "sailusfood",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale+seattle&FORM=USBAPI",
            "displayText" : "sailboats for sale seattle",
            "query" : "sailboats for sale seattle",
            "searchKind" : "WebSearch"
        }]
    }]
}
```

## <a name="next-steps"></a>次の手順

API を試してみます。 [Autosuggest API テスト コンソール](https://dev.cognitive.microsoft.com/docs/services/56c7694ecf5ff801a090fbd1/operations/56c769a2cf5ff801a090fbd2)にアクセスしてください。

応答オブジェクトの使用に関する詳細については、[検索語句候補の取得](./get-suggested-search-terms.md)に関するページを参照してください。
