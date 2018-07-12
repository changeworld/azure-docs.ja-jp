---
title: Image Search SDK Node のクイック スタート | Microsoft Docs
description: Image Search SDK コンソール アプリケーションの設定。
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: e4c8303e39accbb7caec15c0ef47d701971ce632
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377901"
---
# <a name="image-search-sdk-node-quickstart"></a>Image Search SDK Node のクイック スタート

Bing Image Search SDK には、画像クエリと結果解析のための REST API 機能が含まれています。 

Git Hub に [Node Bing Image Search SDK のサンプル ソース コード](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/imageSearch.js)があります。

## <a name="application-dependencies"></a>アプリケーションの依存関係

Bing Image Search SDK を利用してコンソール アプリケーションを設定するには、ご利用の開発環境で `npm install azure-cognitiveservices-imagesearch` を実行します。

## <a name="image-search-client"></a>Image Search クライアント
*[検索]* で [Cognitive Services のアクセス キー](https://azure.microsoft.com/try/cognitive-services/)を取得します。 `CognitiveServicesCredentials` のインスタンスを作成します。
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
次に、クライアントをインスタンス化します。
```
const ImageSearchAPIClient = require('azure-cognitiveservices-imagesearch');
let client = new ImageSearchAPIClient(credentials);
```
クライアントを使用してクエリ テキスト (この場合は 'El Capitan') を検索します。
```
client.imagesOperations.search('El Capitan', function (err, result, request, response) {
    if (err) throw err;
    console.log(result.value);
});

```
<!-- Need to sanitize result
The code prints `result.value` items to the console without parsing any text. The results will be:
- _type: 'ImageObjectElementType'

![Imageresults](media/node-sdk-quickstart-image-results.png)
-->

## <a name="next-steps"></a>次の手順

[Cognitive Services Node.js SDK サンプル](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)