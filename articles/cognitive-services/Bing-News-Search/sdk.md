---
title: Bing Search SDK | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Web を検索するアプリケーション用の Bing Search SDK。
services: cognitive-services
author: mikedodaro
manager: rosh
ms.assetid: ''
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 1/24/2018
ms.author: v-gedod
ms.openlocfilehash: 4a40ea665e153536d2322706b455598902ce41eb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377920"
---
# <a name="bing-search-sdk"></a>Bing Search SDK
Bing News Search API のサンプルには、次のようなシナリオが含まれています。
1. `market` および `count` パラメーターを使用して、検索語句についてニュースのクエリを実行し、結果の数を確認し、最初のニュース結果の `totalEstimatedMatches`、名前、URL、説明、公開時刻、および提供元の名前を出力します。
2. `freshness` および `sortBy` パラメーターを使用して、検索語句について最近のニュースのクエリを実行し、結果の数を確認し、最初のニュース結果の `totalEstimatedMatches`、URL、説明、公開時刻、および提供元の名前を出力します。
3. セーフ サーチを使用して、`movie` および `TV entertainment` に関するカテゴリのニュースのクエリを実行し、結果の数を確認し、最初のニュース結果のカテゴリ、名前、URL、説明、公開時刻、および提供元の名前を出力します。
4. Bing の新しいトレンドのトピックのクエリを実行し、結果の数を確認し、最初のニュース結果の名前、クエリのテキスト、`webSearchUrl`、`newsSearchUrl`、および画像の URL を出力します。

Bing Search SDK を使用すると、次のプログラミング言語で簡単に Web 検索機能にアクセスできるようになります。
* [.NET サンプル](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)を使ってみる
    * [NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.NewsSearch/1.2.0)
    * 定義と依存関係については、[.NET ライブラリ](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Search/BingNewsSearch)も参照してください。
* [Node.js サンプル](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)を使ってみる 
    * 定義と依存関係については、[Node.js ライブラリ](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/newsSearch)も参照してください。
* [Java サンプル](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)を使ってみる 
    * 定義と依存関係については、[Java ライブラリ](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingNewsSearch)も参照してください。
* [Python サンプル](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)を使ってみる 
    * 定義と依存関係については、[Python ライブラリ](https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-search-newssearch)も参照してください。

各言語の SDK サンプルには、前提条件と、サンプルのインストール/実行の詳細が記載された ReadMe ファイルが含まれています。