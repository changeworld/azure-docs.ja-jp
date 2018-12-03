---
title: 'クイック スタート: Bing News Search SDK (Node)'
titleSuffix: Azure Cognitive Services
description: Bing News Search SDK コンソール アプリケーションの設定
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: 075ce771b6fa92663f172734aabf57b089b294a9
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/26/2018
ms.locfileid: "52316941"
---
# <a name="quickstart-bing-news-search-sdk-with-node"></a>クイック スタート: Bing News Search SDK (Node)

Bing News Search SDK には、ニュースのクエリと結果解析のための REST API 機能が含まれています。 

[Node Bing News Search SDK サンプルのソース コード](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js)は、Git Hub で入手できます。

## <a name="application-dependencies"></a>アプリケーションの依存関係
**[検索]** で [Cognitive Services のアクセス キー](https://azure.microsoft.com/try/cognitive-services/)を取得します。  「[Cognitive Services の価格 - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)」も参照してください。 

Bing News Search SDK を使用してコンソール アプリケーションを設定するには:
* 開発環境で `npm install ms-rest-azure` を実行します。
* 開発環境で `npm install azure-cognitiveservices-newssearch` を実行します。

## <a name="news-search-client"></a>News Search クライアント
*[検索]* で [Cognitive Services のアクセス キー](https://azure.microsoft.com/try/cognitive-services/)を取得します。 `CognitiveServicesCredentials` のインスタンスを作成します。
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
次に、クライアントをインスタンス化します。
```
const NewsSearchAPIClient = require('azure-cognitiveservices-newssearch');
let client = new NewsSearchAPIClient(credentials);
```
クライアントを使用してクエリ テキスト (この場合は 'Winter Olympics') を検索します。
```
client.newsOperations.search('Winter Olympics').then((result) => {
    console.log(result.value);
}).catch((err) => {
    throw err;
});

```
このコードでは、テキストを解析せず、コンソールに `result.value` アイテムを出力します。 カテゴリ別の結果がある場合、その結果には次が含まれます。
- _type: 'NewsArticle'
- _type: 'WebPage'
- _type: 'VideoObject'
- _type: 'ImageObject'

<!-- Remove until we can replace with santized version
![News results](media/node-sdk-quickstart-results.png)
-->

## <a name="next-steps"></a>次の手順

[Cognitive Services Node.js SDK サンプル](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
