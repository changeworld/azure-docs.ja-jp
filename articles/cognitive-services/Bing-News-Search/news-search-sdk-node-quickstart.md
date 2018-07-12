---
title: News Search SDK Node のクイック スタート | Microsoft Docs
description: News Search SDK コンソール アプリケーションの設定
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: 4ae99aa100b697a0dd75863c6f0c3c556dfa3d21
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377941"
---
# <a name="news-search-sdk-node-quickstart"></a>News Search SDK Node のクイック スタート

Bing News Search SDK には、ニュースのクエリと結果解析のための REST API 機能が含まれています。 

[Node Bing News Search SDK サンプルのソース コード](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js)は、Git Hub で入手できます。

## <a name="application-dependencies"></a>アプリケーションの依存関係

Bing News Search SDK を利用してコンソール アプリケーションを設定するには、ご利用の開発環境で `npm install azure-cognitiveservices-newssearch` を実行します。

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
