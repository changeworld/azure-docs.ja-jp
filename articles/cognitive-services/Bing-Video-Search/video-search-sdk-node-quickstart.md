---
title: 'クイック スタート: Bing Video Search SDK、Node'
titleSuffix: Azure Cognitive Services
description: Bing Video Search SDK コンソール アプリケーションの設定。
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: quickstart
ms.date: 02/12/2018
ms.author: rosh
ms.openlocfilehash: 4dcc9220d4d38bfe34514edd6a3ad47c7a7d4ba8
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2018
ms.locfileid: "47225618"
---
# <a name="quickstart-bing-video-search-sdk-with-node"></a>クイック スタート: Node での Bing Video Search SDK

Bing Video Search SDK には、ビデオ クエリと結果解析のための REST API 機能が含まれています。 

Git Hub に [Node Bing Video Search SDK のサンプル ソース コード](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js)があります。

## <a name="application-dependencies"></a>アプリケーションの依存関係

Bing Video Search SDK を利用してコンソール アプリケーションを設定するには、ご利用の開発環境で `npm install azure-cognitiveservices-videosearch` を実行します。

## <a name="video-search-client"></a>Video Search クライアント
*[検索]* で [Cognitive Services のアクセス キー](https://azure.microsoft.com/try/cognitive-services/)を取得します。 `CognitiveServicesCredentials` のインスタンスを作成します。
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
次に、クライアントをインスタンス化します。
```
const VideoSearchAPIClient = require('azure-cognitiveservices-videosearch');
let client = new VideoSearchAPIClient(credentials);
```
結果を探します。
```
client.videosOperations.search('Interstellar Trailer').then((result) => {
    console.log(result.value);
}).catch((err) => {
    throw err;
});

```

<!-- Remove until the response can be replace with a sanitized version.
The code prints `result.value` items to the console without parsing any text. The results will be:
- _type: 'VideoObjectElementType'

![Video results](media/video-search-sdk-node-results.png)
-->

## <a name="next-steps"></a>次の手順

[Cognitive Services Node.js SDK サンプル](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
