---
title: Bing Spell Check API クイック スタート | Microsoft Docs
description: Bing Spell Check API の使用方法の概要について説明します。
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: AF8EB1F0-386D-4555-9354-735611435F04
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: article
ms.date: 06/21/2016
ms.author: scottwhi
ms.openlocfilehash: cae8353e5be6e70eca90e5995b29b6774fc7d6a9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372896"
---
# <a name="your-first-spell-check-request"></a>最初のスペル チェック要求

最初の呼び出しを行う前に、Cognitive Services のサブスクリプション キーを入手する必要があります。 キーの入手については、「[Cognitive Services を試す](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)」をご覧ください。

テキスト文字列のスペルや文法の誤りを確認するには、次のエンドポイントに GET 要求を送信します。  
  
```
https://api.cognitive.microsoft.com/bing/v5.0/spellcheck
```

> [!NOTE]
> V7 プレビュー エンドポイント:
> 
> ```
> https://api.cognitive.microsoft.com/bing/v7.0/spellcheck
> ```  
  
要求では、HTTPS プロトコルを使う必要があります。

すべての要求をサーバーから送信することをお勧めします。 クライアント アプリケーションの一部としてキーを配布すると、悪意のあるサードパーティがアクセスする可能性が高くなります。 また、サーバーから呼び出しを行うと、API の将来のバージョンでアップグレードする場所が 1 つで済みます。

要求では、検査するテキスト文字列が含まれている [text](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#text) クエリ パラメーターを指定する必要があります。 必須ではありませんが、要求では [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#mkt) クエリ パラメーターも指定するべきです。このパラメーターは、結果取得元の市場を示します。 `mode` などの省略可能なクエリ パラメーターの一覧については、「[クエリ パラメーター](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#query-parameters)」をご覧ください。 すべてのクエリ パラメーターの値は、URL でエンコードする必要があります。  
  
要求では、[Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#subscriptionkey) ヘッダーを指定する必要があります。 省略可能ですが、次のヘッダーも指定することをお勧めします。  
  
-   [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#clientid)  
-   [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#clientip)  
-   [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#location)  

すべての要求ヘッダーと応答ヘッダーの一覧は、「[Headers](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#headers)」(ヘッダー) をご覧ください。

## <a name="the-request"></a>要求

すべての推奨されるクエリ パラメーターとヘッダーを含む要求を次に示します。 いずれかの Bing API を初めて呼び出す場合は、クライアント ID ヘッダーを含めないでください。 クライアント ID を含めるのは、過去に Bing API を呼び出したことがあり、かつユーザーとデバイスの組み合わせに対応するクライアント ID が Bing から返されたことがある場合だけです。 
  
```  
GET https://api.cognitive.microsoft.com/bing/v5.0/spellcheck?text=when+its+your+turn+turn,+john,+come+runing&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

> [!NOTE]
> V7 プレビュー要求:

> ```  
> GET https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?text=when+its+your+turn+turn,+john,+come+runing&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE  
> X-MSEdge-ClientIP: 999.999.999.999  
> X-Search-Location: lat:47.60357;long:-122.3295;re:100  
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
> Host: api.cognitive.microsoft.com  
> ```  

前述の要求への応答は次のようになります。 例では、Bing に固有の応答ヘッダーも示されています。

```
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{  
    "_type" : "SpellCheck",  
    "flaggedTokens" : [{  
        "offset" : 5,  
        "token" : "its",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "it's",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 25,  
        "token" : "john",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "John",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 19,  
        "token" : "turn",  
        "type" : "RepeatedToken",  
        "suggestions" : [{  
            "suggestion" : "",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 35,  
        "token" : "runing",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "running",  
            "score" : 1  
        }]  
    }]  
}  
```  


## <a name="next-steps"></a>次の手順

API を試してみます。 [Spell Check API テスト コンソール](https://dev.cognitive.microsoft.com/docs/services/56e73033cf5ff80c2008c679/operations/57855119bca1df1c647bc358)に関するページをご覧ください。 

応答オブジェクトの使用について詳しくは、[テキスト文字列のスペル チェック](./proof-text.md)に関する記事をご覧ください。

